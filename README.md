# Movie Ticket Booking System

A full-stack movie ticket booking platform. Users browse movies, pick a show, select seats on a live seat map, pay (simulated), and manage their bookings. Admins manage the catalog and see all bookings.

The main engineering focus is **double-booking protection under concurrent requests**: two users can never end up with the same seat for the same show.

> **Status:** Week 1, planning and repository setup. Sections marked *(planned)* are not built yet.

## Features

### User side
- Register and log in (JWT), with role-based redirect (user vs admin)
- Browse and search movies, filter by city, language and genre
- Movie details with theatre-wise showtimes for the next 7 days
- Seat selection with live availability, a 6-seat limit, and a countdown timer while seats are held
- Simulated payment (success and failure paths) with a hold-expiry check
- Booking confirmation with a reference number and printable ticket
- My bookings: past and upcoming, with status (Confirmed, Cancelled, Expired) and cancellation
- Profile view and update

### Admin side
- Dashboard: total bookings, active shows, simulated revenue, top movies
- Manage movies (add, edit, delete, Now showing / Upcoming)
- Manage theatres and screens, with automatic seat-grid generation
- Manage shows (movie, screen, date, time, price), blocking overlapping shows on a screen
- View, search and cancel all bookings

## Business rules
- Maximum 6 seats per booking
- Seats are held for 5 minutes, then released automatically
- Cancellation is allowed up to 2 hours before the show
- Past shows cannot be booked
- Two shows cannot overlap on the same screen
- Users see only their own bookings; only admins see all bookings

*(Hold duration, cancellation window and locking approach are confirmed in `docs/DECISIONS.md`.)*

## Tech stack
| Layer | Technology |
|---|---|
| Backend | Java, Spring Boot, Spring Data JPA, Spring Security |
| Auth | JWT, two roles (USER, ADMIN) |
| Database | MySQL |
| API docs | Swagger / OpenAPI |
| Frontend | To be confirmed in kickoff (see `docs/DECISIONS.md`) |
| Testing | JUnit, service-layer tests, concurrency test |

## Project scale (target)
About 14 pages (10 user-facing, 4 admin), about 30 REST APIs, 10 database tables, 2 roles.

## Repository structure
```
movie-ticket-booking-system/
├── backend/        Spring Boot application (packaged by module)
│   └── src/main/java/.../ticketbooking/
│       ├── auth/       Authentication and user management
│       ├── catalog/    Movies, theatres, screens, shows
│       ├── booking/    Seats, bookings, payments
│       ├── common/     Shared exceptions and response format
│       └── config/     Security, CORS, Swagger
├── frontend/       Web client
├── docs/
│   ├── api-contract.md   Request/response contract for every endpoint
│   ├── er-diagram.md     Database schema
│   ├── DECISIONS.md      Design decisions and reasons
│   └── screenshots/
└── README.md
```

## Getting started *(planned)*
Setup instructions will be added once the backend skeleton exists.

```bash
git clone https://github.com/<your-username>/movie-ticket-booking-system.git
cd movie-ticket-booking-system
```

Configuration: copy `backend/src/main/resources/application-example.properties` to `application.properties` and fill in your own database credentials and JWT secret. The real `application.properties` is git-ignored and must never be committed.

## API documentation *(planned)*
Swagger UI will be available at `/swagger-ui.html` when the backend is running. The written contract lives in [`docs/api-contract.md`](docs/api-contract.md).

## Concurrency test results *(planned)*
This section will document the test of simultaneous booking attempts for one seat, with real results, for example the number of successes and rejections out of N parallel requests. Results will be added only after the test has been run.

## Team
| Member | Module |
|---|---|
| Prashant Sharma | Authentication and user management, repository setup, configuration |
| Om Gupta | Movies, theatres, screens, shows, search, admin catalog, seed data |
| Aaditya Vasishtha | Seat selection, booking, payment, cancellation, concurrency tests |

## Team workflow
- Branches: `main` (stable) ← `develop` (integration) ← `feature/<module>-<task>`
- Direct pushes to `main` and `develop` are blocked; every change goes through a pull request with 1 approval
- Commit prefixes: `feat:`, `fix:`, `test:`, `docs:`, `refactor:`
- Tasks are tracked with GitHub Issues and a Project board

## Milestones
| Milestone | Week | Goal |
|---|---|---|
| M1 | 2 | Skeleton runs and connects to the database |
| M2 | 5 | Auth and catalog working end to end |
| M3 | 7 | Full booking flow working in the backend |
| M4 | 9 | Frontend integrated, tests passing |
| M5 | 10 | Final release, tagged v1.0 |

## License
MIT
