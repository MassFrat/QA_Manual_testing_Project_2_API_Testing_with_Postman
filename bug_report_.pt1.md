# Bug Report — Restful Booker API
### Project 2: API Testing with Postman

<br>

| Field | Details |
|---|---|
| **Tester** | Mass Frat |
| **Date** | May 2026 |
| **API Under Test** | Restful Booker API |
| **Base URL** | https://restful-booker.herokuapp.com |
| **Testing Tool** | Postman |
| **Total Test Cases Executed** | 15 |
| **Total Bugs Found** | 5 |

<br>

---

<br> 

### Test Execution Summary

| Total Test Cases | Passed | Failed | Bugs Found | Pass Rate |
|:---:|:---:|:---:|:---:|:---:|
| 15 | 10 | 5 | 5 | 67% |

<br>

### Results by Test Type

| Test Type | Test Cases | Passed | Failed |
|---|:---:|:---:|:---:|
| Happy Path | 9 (TC001–TC009) | 9 | 0 |
| Negative | 3 (TC010–TC012) | 1 | 2 |
| Edge Case | 3 (TC013–TC015) | 0 | 3 |
<br> 

---
<br>

### Failed Test Cases Reference

| TC ID | Description | Expected Status | Actual Status | Result |
|:---:|---|:---:|:---:|:---:|
| TC010 | Create booking with missing `lastname` field | 400 Bad Request | 500 Internal Server Error | ❌ FAIL |
| TC011 | Update booking with invalid auth token | 403 Forbidden | 403 Forbidden | ✅ PASS |
| TC012 | Delete a non-existent booking ID | 404 Not Found | 405 Method Not Allowed | ❌ FAIL |
| TC013 | Create booking with total price of zero | 400 Bad Request | 200 OK | ❌ FAIL |
| TC014 | Create booking with same check-in and check-out date | 400 Bad Request | 200 OK | ❌ FAIL |
| TC015 | Create booking with 50+ character firstname | 400 Bad Request | 200 OK | ❌ FAIL |

Note: TC011 is included for reference — it passed, confirming the API correctly blocks unauthorised requests.

<br>

---
<br>

### Severity Guide

| Severity | Level | Definition |
|:---:|:---:|---|
| 🔴 | Critical | System is unusable. Data loss or security breach possible. |
| 🟠 | High | Major feature is broken. Needs urgent fix before release. |
| 🟡 | Medium | Feature is impacted but a workaround exists. |
| 🟢 | Low | Minor issue. No impact on core functionality. |

<br>

---

### Detailed Bug Reports

---

<br>

### BUG-001 — POST /booking Returns 500 Internal Server Error When Required Field is Missing

<br>

| Field | Details |
|---|---|
| **Bug ID** | BUG-001 |
| **Severity** | 🟠 High |
| **Priority** | High |
| **Status** | Open |
| **Endpoint** | `POST /booking` |
| **Method** | POST |
| **Test Case Reference** | TC010 |
| **Environment** | Chrome 124 / Windows 11 / Postman / May 2026 |

#### Steps to Reproduce

1. Open Postman and create a new `POST` request to `https://restful-booker.herokuapp.com/booking`
2. Go to the **Headers** tab and add: `Content-Type: application/json`
3. Go to the **Body** tab, select **raw** and set format to **JSON**
4. Enter the following JSON body — deliberately omitting the `lastname` field:
```json
{
    "firstname": "John",
    "totalprice": 150,
    "depositpaid": true,
    "bookingdates": {
        "checkin": "2025-01-01",
        "checkout": "2025-01-07"
    }
}
```
5. Click **Send** and observe the response status code and body

#### Expected Result
> The API should detect the missing required field `lastname` and return **400 Bad Request** with a descriptive error message identifying which field is missing. The booking should **not** be created.

#### Actual Result
> ❌ The API returned **500 Internal Server Error**. The server crashed when attempting to process the request with a missing field instead of gracefully rejecting it. No meaningful error message was returned.

<br>

---

<br>

### BUG-002 — DELETE /booking/{id} Returns 405 Instead of 404 for Non-Existent Booking ID

<br>

| Field | Details |
|---|---|
| **Bug ID** | BUG-002 |
| **Severity** | 🟡 Medium |
| **Priority** | Medium |
| **Status** | Open |
| **Endpoint** | `DELETE /booking/{id}` |
| **Method** | DELETE |
| **Test Case Reference** | TC012 |
| **Environment** | Chrome 124 / Windows 11 / Postman / May 2026 |

#### Steps to Reproduce

1. First run `POST /auth` to obtain a valid token and save it to the `{{token}}` environment variable
2. Open Postman and create a new `DELETE` request to `https://restful-booker.herokuapp.com/booking/999999`
   *(Note: 999999 is a deliberately non-existent booking ID)*
3. Go to the **Headers** tab and add: `Cookie: token={{token}}`
4. Leave the request body empty
5. Click **Send** and observe the response status code

#### Expected Result
> The API should return **404 Not Found** to indicate that no booking exists with the specified ID. This is the standard HTTP convention for a resource that cannot be located.

#### Actual Result
> ❌ The API returned **405 Method Not Allowed**. This incorrectly implies the DELETE method itself is not permitted on this endpoint — which is misleading since DELETE is a valid, documented method here.

<br>

---

<br>

### BUG-003 — POST /booking Accepts a Total Price of Zero With No Business Logic Validation

<br>

| Field | Details |
|---|---|
| **Bug ID** | BUG-003 |
| **Severity** | 🟡 Medium |
| **Priority** | Medium |
| **Status** | Open |
| **Endpoint** | `POST /booking` |
| **Method** | POST |
| **Test Case Reference** | TC013 |
| **Environment** | Chrome 124 / Windows 11 / Postman / May 2026 |

#### Steps to Reproduce

1. Open Postman and create a new `POST` request to `https://restful-booker.herokuapp.com/booking`
2. Go to the **Headers** tab and add: `Content-Type: application/json`
3. Go to the **Body** tab, select **raw** and set format to **JSON**
4. Enter the following JSON body with `totalprice` set to `0`:
```json
{
    "firstname": "Zero",
    "lastname": "Price",
    "totalprice": 0,
    "depositpaid": false,
    "bookingdates": {
        "checkin": "2025-03-01",
        "checkout": "2025-03-05"
    },
    "additionalneeds": "None"
}
```
5. Click **Send** and observe the response

#### Expected Result
> The API should return **400 Bad Request** because a hotel booking with a total price of zero is not a valid business transaction. No real hotel booking should cost nothing.

#### Actual Result
> ❌ The API returned **200 OK** and successfully created a booking with a total price of zero, assigning it a new `bookingid`. No validation error was returned and the booking was stored as if it were valid.

<br>

---

<br>

### BUG-004 — POST /booking Allows Booking With Identical Check-in and Check-out Dates

<br>

| Field | Details |
|---|---|
| **Bug ID** | BUG-004 |
| **Severity** | 🟡 Medium |
| **Priority** | Medium |
| **Status** | Open |
| **Endpoint** | `POST /booking` |
| **Method** | POST |
| **Test Case Reference** | TC014 |
| **Environment** | Chrome 124 / Windows 11 / Postman / May 2026 |

#### Steps to Reproduce

1. Open Postman and create a new `POST` request to `https://restful-booker.herokuapp.com/booking`
2. Go to the **Headers** tab and add: `Content-Type: application/json`
3. Go to the **Body** tab, select **raw** and set format to **JSON**
4. Enter the following JSON body with `checkin` and `checkout` set to the same date:
```json
{
    "firstname": "Same",
    "lastname": "Day",
    "totalprice": 100,
    "depositpaid": true,
    "bookingdates": {
        "checkin": "2025-05-01",
        "checkout": "2025-05-01"
    },
    "additionalneeds": "None"
}
```
5. Click **Send** and observe the response

#### Expected Result
> The API should return **400 Bad Request** because a hotel booking where the check-out date is identical to the check-in date represents a stay of zero nights and is not a valid booking.

#### Actual Result
> ❌ The API returned **200 OK** and successfully created the booking with identical check-in and check-out dates, assigning it a new `bookingid`. No validation error was returned.

<br>

---

<br>

### BUG-005 — POST /booking Accepts Firstname Values Exceeding 50+ Characters With No Character Limit

<br>

| Field | Details |
|---|---|
| **Bug ID** | BUG-005 |
| **Severity** | 🟡 Medium |
| **Priority** | Medium |
| **Status** | Open |
| **Endpoint** | `POST /booking` |
| **Method** | POST |
| **Test Case Reference** | TC015 |
| **Environment** | Chrome 148 / Windows 11 / Postman / May 2026 |

#### Steps to Reproduce

1. Open Postman and create a new `POST` request to `https://restful-booker.herokuapp.com/booking`
2. Go to the **Headers** tab and add: `Content-Type: application/json`
3. Go to the **Body** tab, select **raw** and set format to **JSON**
4. Enter the following JSON body using a `firstname` value of over 50+ characters:
```json
{
    "firstname": "Johnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnnn",
    "lastname": "Smith",
    "totalprice": 100,
    "depositpaid": true,
    "bookingdates": {
        "checkin": "2025-04-01",
        "checkout": "2025-04-05"
    },
    "additionalneeds": "None"
}
```
5. Click **Send** and observe the response

#### Expected Result
> The API should return **400 Bad Request** because the `firstname` field value exceeds a reasonable character limit. Most systems impose character limits on name fields (typically 50–100 characters maximum).

#### Actual Result
> ❌ The API returned **200 OK** and created the booking successfully, storing the 50+ character firstname with no validation error or truncation.

---
<br>

### Retest Tracker

Once the development team has applied fixes, all bugs must be retested to confirm the fixes work correctly and have not introduced any new issues.

| Bug ID | Title | Status | Fix Version | Retest Date | Retested By | Retest Result |
|:---:|---|:---:|:---:|:---:|:---:|:---:|
| BUG-001 | POST /booking 500 on missing required field | 🔴 Open | — | — | — | — | 
| BUG-002 | DELETE returns 405 on non-existent booking ID | 🔴 Open | — | — | — | — |
| BUG-003 | POST /booking accepts zero total price | 🔴 Open | — | — | — | — |
| BUG-004 | POST /booking allows identical check-in and check-out dates | 🔴 Open | — | — | — | — |
| BUG-005 | POST /booking accepts 50+ character firstname | 🔴 Open | — | — | — | — |

<br>

### Retest Process

<br>

1. Receive notification from the development team that a bug fix has been deployed
2. Re-run the original failing test case in Postman using the exact same steps and request body
3. Verify the actual result now matches the expected result and the correct status code is returned
4. Run surrounding test cases to check for regression — confirm no previously passing tests have broken
5. Update the retest status above to **Closed** if confirmed fixed, or **Reopened** if the bug still exists



