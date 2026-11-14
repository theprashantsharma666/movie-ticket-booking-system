# API Contract

Base path: `/api`. All bodies are JSON. Dates use ISO 8601.

## Auth rules

| # | Decision | Rule |
|---|---|---|
| 1 | Roles | `USER`, `ADMIN` |
| 2 | Admin creation | Seeded in `data.sql`, never through public register |
| 3 | Login identifier | Email |
| 4 | Email handling | Stored in lowercase, compared case-insensitively |
| 5 | Password rule | At least 8 characters |
| 6 | Password storage | BCrypt hash, never returned in any response |
| 7 | Token type | JWT, signed with HS256 |
| 8 | Token lifetime | 24 hours (`expiresIn`: 86400 seconds) |
| 9 | Token claims | `sub` (user id), `email`, `role` |
| 10 | Header format | `Authorization: Bearer <token>` |
| 11 | JWT secret | In `application.properties` (not committed); placeholder in `application-example.properties` |
| 12 | Wrong login message | One generic "Invalid email or password" |


## 1. Auth module (owner: Prashant)

### POST /api/auth/register  (public)
Request:
```json
{ "name": "Asha Verma", "email": "asha@example.com", "password": "Secret123" }
```
Rules: name 2-50 chars, valid email, password at least 8 chars.

Response 201:
```json
{ "id": 1, "name": "Asha Verma", "email": "asha@example.com", "role": "USER" }
```
Errors: 400 validation failed, 409 email already registered.

### POST /api/auth/login  (public)
Request:
```json
{ "email": "asha@example.com", "password": "Secret123" }
```
Response 200:
```json
{
  "token": "<jwt>",
  "tokenType": "Bearer",
  "expiresIn": 86400,
  "user": { "id": 1, "name": "Asha Verma", "email": "asha@example.com", "role": "USER" }
}
```
Errors: 400 missing fields, 401 invalid email or password.

### GET /api/users/me  (USER, ADMIN)
Response 200:
```json
{ "id": 1, "name": "Asha Verma", "email": "asha@example.com", "role": "USER", "createdAt": "2026-11-10T10:30:00Z" }
```
Errors: 401 missing or invalid token.

### PUT /api/users/me  (USER, ADMIN)
Request (all fields optional):
```json
{ "name": "Asha V.", "email": "asha.v@example.com" }
```
Response 200: same shape as GET /api/users/me.

Errors: 400 validation failed, 401 unauthenticated, 409 email already taken.

### GET /api/users/me/bookings  (USER, ADMIN)
Returns the logged-in user's own bookings only.
Route owner: Prashant. Response shape: defined in the booking section (Aaditya).

Errors: 401 unauthenticated.

## Access summary
| Endpoint | Public | USER | ADMIN |
|---|---|---|---|
| POST /api/auth/register | yes | yes | yes |
| POST /api/auth/login | yes | yes | yes |
| GET, PUT /api/users/me | no | yes | yes |
| GET /api/users/me/bookings | no | yes | yes |