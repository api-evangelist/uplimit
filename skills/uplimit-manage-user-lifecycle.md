---
name: Manage user lifecycle and SSO
description: Audit active/inactive users, activate or deactivate accounts, remove
  session enrollments, and bind enterprise SSO identities.
api: openapi/uplimit-organization-openapi-original.yml
operations:
  - GET /v1/ListActiveUsers
  - GET /v1/ListInactiveUsers
  - POST /v1/ToggleUserActivation
  - POST /v1/UnenrollUserFromSession
  - POST /v1/AddUserAuthenticationMethod
generated: '2026-07-21'
method: generated
note: The published spec declares no operationIds; operations are referenced as
  METHOD path (see overlays/uplimit-organization-overlay.yaml for suggested ids).
---

# Manage user lifecycle and SSO

All requests use `https://uplimit.com/api/organization/` with
`Authorization: Bearer <org token>`.

1. **Audit membership** — `GET /v1/ListActiveUsers` and
   `GET /v1/ListInactiveUsers` (paginate with `skip`/`take`; responses carry
   `totalCount`). Each user record shows `userAccountIsActive`,
   `userHasValidSubscriptionEnrollment`, and their subscription-commitment ids.
2. **Deactivate / reactivate** — `POST /v1/ToggleUserActivation` with
   `emailAddress` and `setIsActive` (optionally `subscriptionCommitmentId` to
   target one group; deactivation without it applies across all commitments;
   `doNotSendWelcomeEmail: true` suppresses the reactivation email).
3. **Remove a session enrollment** — `POST /v1/UnenrollUserFromSession` with
   `emailAddress` and `uplimitSessionId`; the user must currently be enrolled.
4. **Bind SSO identity** — `POST /v1/AddUserAuthenticationMethod` with
   `emailAddress`, `authenticationMethod` (SAML or OAUTH2),
   `customAuthenticationMethodProviderId` (issued by Uplimit), and
   `authenticationSecret` (SAML entity ID or OAuth2 `sub`).

Errors arrive as `{ "error": "<message>" }` (400/401/404/405). Destructive
calls (deactivation, unenrollment) have no idempotency-key mechanism — check
state with GET /v1/GetUserInformation/{emailAddressOrUserId} before repeating.
