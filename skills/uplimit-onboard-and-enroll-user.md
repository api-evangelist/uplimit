---
name: Onboard a user and enroll them into a course or session
description: Create (or reuse) a learner account in your Uplimit organization and
  enroll them into a specific session, or into a course by policy.
api: openapi/uplimit-organization-openapi-original.yml
operations:
  - POST /v1/CreateUser
  - POST /v1/EnrollUserIntoSession
  - POST /v1/EnrollUserIntoCourse
  - GET /v1/GetUserInformation/{emailAddressOrUserId}
generated: '2026-07-21'
method: generated
note: The published spec declares no operationIds; operations are referenced as
  METHOD path (see overlays/uplimit-organization-overlay.yaml for suggested ids).
---

# Onboard a user and enroll them into a course or session

All requests use the base URL `https://uplimit.com/api/organization/` with an
`Authorization: Bearer <org token>` header. Verify the token first with
`GET /v1/Healthcheck` (200 = valid; 403 = incorrect/missing key).

1. **Create the user** — `POST /v1/CreateUser` with required `emailAddress`,
   `firstName`, `lastName` (optional `subscriptionCommitmentId` to place them in
   a group, `doNotSendWelcomeEmail: true` to suppress the welcome email). If a
   user with that email already exists, Uplimit enrolls the existing user into
   your organization instead — the call is safe to repeat. Keep the returned
   `uplimitUserId` and `uplimitSubscriptionEnrollmentId`.
2. **Find the target** — `GET /v1/ListCourses` for `uplimitCourseId`, then
   `GET /v1/ListSessionsInCourse?uplimitCourseId=...` for `uplimitSessionId`
   (check `enrollmentEnabled` and `startsAt`/`endsAt`). Lists paginate with
   `skip`/`take` and return `totalCount`.
3. **Enroll** — prefer `POST /v1/EnrollUserIntoSession` with `emailAddress` and
   `uplimitSessionId` (exact session). Use `POST /v1/EnrollUserIntoCourse`
   (with `uplimitCourseId` and the session-selection policy enum) only when you
   want Uplimit to pick the session by policy.
4. **Verify** — `GET /v1/GetUserInformation/{emailAddressOrUserId}` should show
   `userHasValidSubscriptionEnrollment: true`.

Errors arrive as `{ "error": "<message>" }` with 400 (invalid request), 401
(bad token), 404 (user/course/session not found), 405 (wrong method). There is
no idempotency-key mechanism; only CreateUser is documented as safe to repeat.
