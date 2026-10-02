# Doreen-US-B2Visa-Scheduler
To monitor the latest released time slots for US Visa(B2)
# Doreen US B1/B2 Visa Appointment Monitor

A Chromium browser extension for monitoring earlier U.S. visa appointment
availability in Canada.

This project is adapted from SardarJi-US-Visa-Scheduler and customized for
an existing-appointment / rescheduling workflow.

The current implementation focuses on safely monitoring selected Canadian
consular sections for an earlier appointment and notifying the user when
a genuinely bookable date and time becomes available.

---

## Table of Contents

1. Project Goal
2. Current Use Case
3. Features
4. Monitoring Workflow
5. Supported Facilities
6. Installation
7. Configuration
8. How Slot Detection Works
9. Session & Login Handling
10. Notifications
11. Reschedule Safety
12. Project Architecture
13. Project Structure
14. Privacy & Security
15. Known Limitations
16. Roadmap
17. Credits
18. Disclaimer

---

## Project Goal

The goal of this project is to monitor the U.S. visa appointment system
for earlier B1/B2 interview availability without repeatedly checking the
appointment calendar manually.

The extension:

- maintains an authenticated browser session;
- periodically checks selected Canadian consular sections;
- validates both appointment dates and available times;
- filters results against a desired date range;
- notifies the user when a qualifying slot is detected;
- handles session expiry and rate limiting;
- keeps booking actions separate from monitoring.

The default design is **monitor first, user decides**.

---

## Current Use Case

The extension is currently optimized for a rescheduling scenario:

- An appointment already exists.
- The user wants an earlier appointment.
- Halifax is the primary monitoring target.
- Other Canadian facilities can optionally be monitored.
- Only dates earlier than the currently booked appointment qualify.
- Desktop and sound notifications alert the user when a slot is found.
- Automatic booking/rescheduling is disabled by default.

Personal appointment dates, account identifiers, DS-160 numbers,
credentials, and other private information are not hard-coded into
the repository.

---

## Features

### Appointment Monitoring

| Feature | Description |
|---|---|
| Multi-facility monitoring | Monitor one or more Canadian consular sections |
| Date-range filtering | Ignore appointments outside the desired window |
| Earlier-date filtering | In reschedule mode, ignore dates that do not improve the current appointment |
| Bookable-slot validation | Confirm that an appointment time exists before reporting a slot |
| Randomized interval | Check within a configurable min/max interval |
| Active-hour control | Optionally monitor only during selected time windows |
| Rate-limit handling | Back off automatically after HTTP 429 responses |

### Session Management

| Feature | Description |
|---|---|
| Automatic login | Restore an authenticated AIS session when possible |
| Page detection | Identify login, appointment and intermediate pages |
| Session recovery | Re-login when the existing session expires |
| Keep-alive | Maintain the session between longer monitoring intervals |
| CAPTCHA handling | Pause for manual intervention when verification is required |

### Notifications

- Desktop notification
- Audible alert
- Activity log
- Slot history
- Optional Telegram integration

---

## Monitoring Workflow

```text
START
  │
  ▼
Load configuration
  │
  ▼
Open / restore AIS session
  │
  ▼
Identify appointment page
  │
  ▼
Start monitoring
  │
  ▼
Check selected facility
  │
  ├── No dates ────────────────┐
  │                            │
  ├── Date outside range ──────┤
  │                            │
  ├── No available time ───────┤
  │                            │
  ├── Session expired          │
  │        └── Re-login ───────┤
  │                            │
  ├── HTTP 429                 │
  │        └── Backoff ────────┤
  │                            │
  └── Bookable slot found      │
           │                   │
           ▼                   │
     Notify user               │
                               ▼
                     Schedule next check
