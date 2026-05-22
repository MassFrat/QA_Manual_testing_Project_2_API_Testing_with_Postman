# Restful Booker API — Manual Testing Project 2

## Project Overview
Manual API testing project using Postman to test the Restful Booker API.
The project covers all 7 CRUD endpoints with a total of 15 test cases
including happy path, negative, and edge case testing.

## Test Summary
| Total Test Cases | Pass | Fail | Bugs Found |
|---|---|---|---|
| 15 | 10 | 5 | 5 |

## Tools Used
- Postman (API testing and request execution)
- Google Sheets (test case documentation)
- GitHub (version control and portfolio)

## API Under Test
- **Name:** Restful Booker API
- **Base URL:** https://restful-booker.herokuapp.com
- **Documentation:** https://restful-booker.herokuapp.com/apidoc

## Endpoints Tested
| Method | Endpoint | Description |
|---|---|---|
| POST | /auth | Generate auth token |
| GET | /booking | Get all bookings |
| POST | /booking | Create a booking |
| GET | /booking/{id} | Get booking by ID |
| PUT | /booking/{id} | Full update |
| PATCH | /booking/{id} | Partial update |
| DELETE | /booking/{id} | Delete booking |

## Test Types Covered
- Happy Path Testing (TC001-TC009)
- Negative Testing (TC010-TC012)
- Edge Case Testing (TC013-TC015)

## Bugs Found
### BUG-001 — High Severity
- Endpoint: POST /booking
- Issue: Returns 500 Internal Server Error instead of
  400 Bad Request when a required field is missing
- Screenshot: screenshots/TC010-500-error-missing-field.png

### BUG-002 — Medium Severity
- Endpoint: DELETE /booking/{id}
- Issue: Returns 403 Forbidden instead of
  404 Not Found for a non-existent booking ID

### BUG-003 — Medium Severity
- Endpoint: POST /booking
- Issue: Accepts bookings with a total price of zero —
  returns 200 OK with a new booking id

### BUG-004 — Medium Severity
- Endpoint: POST /booking
- Issue: Accepts bookings with identical check-in and check-out dates —
  returns 200 OK with a new booking id

### BUG-005 — Medium Severity
- Endpoint: POST /booking
- Issue: Accepts firstname values over 50+ characters —
  returns 200 OK with a new boooking  id

## How to Use the Postman Collection
1. Download `restful-booker-postman-collection.json`
2. Open Postman
3. Click Import and select the file
4. Download `restful-booker-environment.json` and import it too
5. Select the environment from the top right dropdown
6. Run TC001 first to generate a token before running other requests


