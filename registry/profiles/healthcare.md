# Profile: Healthcare

Additional standards and rules applied when `.bootstrap-config.yaml` specifies `profile: healthcare`.

## Additional Forbidden Patterns

Add these to `standards/forbidden.md`:

- Logging PHI (Protected Health Information) without redaction — HIPAA violation
- Storing patient data in browser localStorage/sessionStorage — Unencrypted client-side PHI storage
- Transmitting PHI over unencrypted channels — HIPAA requires encryption in transit
- Using patient names or SSNs as database keys — Use opaque identifiers
- Caching PHI in CDN or shared caches — PHI must not be stored in multi-tenant caches
- Disabling audit logging on PHI-accessing endpoints — HIPAA requires access audit trails
- Storing PHI and authentication credentials in the same database table — Separation of concerns for breach containment
- Returning PHI in error messages or stack traces — Data exposure through error handling
- Bulk PHI export without access controls — Must enforce minimum necessary principle
- Screenshots or screen recordings containing PHI in test fixtures — Test data must be synthetic

## Additional Security Standards

Add to `standards/security.md`:

### HIPAA Technical Safeguards
- **Access Control**: Role-based access to PHI; minimum necessary principle enforced in code
- **Audit Controls**: Every PHI access logged with user identity, timestamp, data accessed, and purpose
- **Integrity Controls**: PHI modifications tracked with before/after values
- **Transmission Security**: TLS 1.2+ for all PHI in transit; no PHI in URL parameters
- **Encryption at Rest**: AES-256 or equivalent for stored PHI

### De-identification Requirements
- Test environments use synthetic data only — never copies of production PHI
- De-identification follows HIPAA Safe Harbor or Expert Determination method
- Re-identification risk assessed for any data export or analytics pipeline

### Breach Response
- PHI exposure detection: monitor for PHI patterns in logs, error reports, analytics
- Breach notification timeline: 60-day HIPAA requirement referenced in incident checklist
- Containment procedures: immediate access revocation, affected data identification

## Additional Checklist Items

Add to `checklists/pre-commit.md`:
- [ ] No PHI in log statements (patient names, DOB, SSN, MRN, diagnoses)
- [ ] Audit logging added for any new PHI access patterns
- [ ] Test data uses synthetic patients, not production data
- [ ] PHI encrypted at rest in any new data stores

Add to `checklists/pr-review.md`:
- [ ] RBAC enforced on new endpoints accessing PHI
- [ ] Minimum necessary principle: only required PHI fields accessed
- [ ] No PHI in URL parameters, query strings, or client-side state
- [ ] Audit trail entries created for new data access paths
- [ ] De-identified data verified to meet Safe Harbor criteria

Add to `checklists/incident.md`:
- [ ] Determine if PHI was exposed (triggers HIPAA breach notification)
- [ ] Document affected patients and data elements
- [ ] Notify Privacy Officer within 24 hours if PHI breach confirmed
- [ ] Preserve all access logs for investigation

## Additional Context

Add to `context/domain.md`:
- Patient data classification (what constitutes PHI in this system)
- Consent management flows and how consent state affects data access
- Integration with EHR/EMR systems and their data exchange standards (HL7, FHIR)
- De-identification strategy and which method is used
