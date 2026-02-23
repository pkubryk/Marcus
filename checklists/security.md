# Security Review Checklist

Review all code produced in this feature against the following criteria.

## Authentication and Authorization
- Credentials, tokens, and secrets are never hardcoded or logged
- Access controls enforced at API boundaries
- JWT/session tokens validated with strong secrets
- Password hashing uses bcrypt with salt rounds >= 12
- Account lockout after repeated failed attempts

## Input Validation
- All user input validated and sanitized at entry points
- SQL injection prevention (parameterized queries / ORM)
- XSS prevention with proper encoding/escaping
- CSRF protection where applicable
- Request size limits enforced

## Data Protection
- Sensitive data encrypted at rest and in transit
- PII minimized — collect only what is necessary
- Data retention policies defined and enforced
- Secure deletion when data is removed
- No sensitive data in error messages or logs

## API Security
- Rate limiting on all public endpoints
- CORS configured with specific allowed origins
- Error responses sanitized — no stack traces in production
- API versioning strategy in place
- Request/response logging excludes sensitive fields

## Dependencies
- All dependencies from trusted sources
- No known vulnerabilities (run audit)
- Dependency versions pinned
- Minimal dependency surface — no unnecessary packages
