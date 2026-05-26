# Acceptance Criteria

Each feature must pass its acceptance criteria before being considered "done." These are written to be verified by either a human tester or an automated test.

## [Module Name]

### [Feature Name]

- [ ] **AC-001**: [Specific, verifiable condition. e.g., "User can create an account with email + password and receives a verification email within 60 seconds"]
- [ ] **AC-002**: [e.g., "Submitting the form with an already-registered email shows error message 'This email is already in use'"]
- [ ] **AC-003**: [e.g., "Empty required fields show inline validation errors on blur"]

### [Next Feature]

- [ ] **AC-004**: ...

## Cross-cutting Acceptance

These apply across multiple modules:

- [ ] **CC-001**: All error messages are in [language] and use no technical jargon
- [ ] **CC-002**: All pages load within [X] seconds on a [connection type] connection
- [ ] **CC-003**: The app handles [N] concurrent users without degradation
- [ ] **CC-004**: All authenticated endpoints return 401 when accessed without valid credentials
