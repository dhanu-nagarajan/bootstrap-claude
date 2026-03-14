# Profile: Fintech

Additional standards and rules applied when `.bootstrap-config.yaml` specifies `profile: fintech`.

## Additional Forbidden Patterns

Add these to `standards/forbidden.md`:

- `eval()` or `new Function()` in any context — Code injection vector in financial systems
- `Math.round()` for currency — Use integer cents or a decimal library (e.g., `Decimal.js`, `BigDecimal`)
- Floating-point arithmetic on monetary values — IEEE 754 rounding causes real financial discrepancies
- `console.log` of PII or financial data — Compliance violation; use structured logging with redaction
- Hardcoded currency symbols or locale formats — Use `Intl.NumberFormat` or equivalent i18n library
- HTTP without TLS — All financial data must be encrypted in transit
- Storing raw credit card numbers — Use tokenization; PCI-DSS requirement
- Disabling CSRF protection — Financial transactions require anti-forgery tokens
- `SELECT *` on tables containing PII — Explicit column selection prevents accidental data exposure
- Retry logic without idempotency keys — Duplicate financial transactions cause real monetary loss

## Additional Security Standards

Add to `standards/security.md`:

### PCI-DSS Requirements (if handling payment data)
- Card data never touches application logs
- Encryption at rest for all stored financial records
- Access control: principle of least privilege on all financial endpoints
- Audit trail: every data access logged with who/when/what
- Network segmentation: payment processing isolated from general application

### SOC 2 Considerations
- Data retention policies enforced in code (auto-delete after retention period)
- Access logging on all sensitive endpoints
- Change management: all deployments through CI/CD with approval gates
- Incident response procedures documented and referenced in `.claude/checklists/incident.md`

### Anti-Fraud Patterns
- Rate limiting on all financial transaction endpoints
- Velocity checks on high-value operations
- Idempotency keys on all state-changing financial operations
- Amount validation with configurable min/max bounds

## Additional Checklist Items

Add to `checklists/pre-commit.md`:
- [ ] No PII or financial data in log statements
- [ ] All monetary calculations use integer cents or decimal library
- [ ] Idempotency keys present on financial mutation endpoints
- [ ] Input validation on all currency amounts (bounds, type, precision)

Add to `checklists/pr-review.md`:
- [ ] Audit trail entries added for new data access patterns
- [ ] No new `SELECT *` on PII-containing tables
- [ ] Rate limiting considered for new financial endpoints
- [ ] Error messages do not leak financial data or internal identifiers

## Additional Context

Add to `context/domain.md`:
- Regulatory environment and applicable compliance frameworks
- Payment processor integrations and their constraints
- Settlement/reconciliation flows and timing requirements
- Currency handling strategy (which decimal library, how precision is managed)
