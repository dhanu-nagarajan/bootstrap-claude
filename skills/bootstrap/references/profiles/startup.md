# Profile: Startup

Additional standards and rules applied when `.bootstrap-config.yaml` specifies `profile: startup`.

## Philosophy Adjustments

This profile optimizes for speed and iteration velocity while maintaining a safety floor. It relaxes ceremony-heavy standards where the cost exceeds the value at early stage, but never compromises on data safety or security fundamentals.

## Standards Modifications

### Architecture (relax)
- Permit pragmatic shortcuts in layer boundaries when shipping speed matters — document them as tech debt with `// TODO(tech-debt): [description]` comments
- Monolith-first is acceptable; don't over-engineer microservice boundaries before product-market fit
- Prefer simple, obvious solutions over clever abstractions — "boring technology" principle

### Testing (focused)
- Prioritize integration tests over unit tests at early stage — test the user-facing behavior, not internal implementation
- Required: tests for payment flows, auth flows, and data mutation endpoints
- Optional: tests for UI layout, admin-only features, internal tooling
- Never skip: tests for anything that touches money, user data, or access control

### Performance (defer)
- Performance optimization is premature until there's measurable user impact
- Exception: database queries must have appropriate indexes from day one
- Exception: no unbounded queries (always paginate, always limit)
- Measure before optimizing — add monitoring first, optimize second

### Error Handling (pragmatic)
- Crash loudly in development, recover gracefully in production
- Error tracking service (Sentry, Datadog, etc.) is required infrastructure, not optional
- User-facing errors: helpful message + support contact; never expose stack traces
- Internal errors: log with full context; alert on new error types

## Additional Forbidden Patterns

Add to `standards/forbidden.md`:

- Premature abstraction — Don't build a framework for one use case; wait for the third instance
- Feature flags without expiration — Every flag gets a removal date in the comment
- "Enterprise" patterns at seed stage — No CQRS, no event sourcing, no saga patterns unless you have the traffic to justify them
- Gold-plating — Ship the 80% solution; iterate based on user feedback

## Checklist Modifications

### Pre-Commit (streamlined)
- [ ] Tests pass for affected code paths
- [ ] No secrets in diff
- [ ] No unrelated changes bundled
- [ ] Commit message explains WHY

### PR Review (velocity-focused)
- [ ] Does it work? (correctness over elegance)
- [ ] Is it safe? (security, data integrity, error handling)
- [ ] Is it maintainable? (can someone else understand this in 3 months?)
- [ ] Is it shippable? (feature flags if not fully complete)
- Skip: style nits, naming bikeshedding, test coverage percentages

## Additional Context

Add to `context/domain.md`:
- Current product stage and what "done" means at this stage
- Key metrics being optimized (activation, retention, revenue — pick one)
- Technical debt register with severity and estimated cleanup cost
- Decision velocity: bias toward reversible decisions made quickly
