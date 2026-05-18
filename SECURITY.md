# Security Policy

## Supported Versions

| Version | Supported |
|---|---|
| 0.x (latest) | ✅ Active development |
| < 0.1 | ❌ Not supported |

## Reporting a Vulnerability

We take the security of our projects seriously. If you believe you've found a security vulnerability, **please do not open a public issue**.

### Private Disclosure Process

1. **Report** via [GitHub Security Advisory](https://github.com/ayomidearegbeshola29-dev/.github/security/advisories/new)
2. **Alternative** — Email the maintainers directly
3. **Do not** disclose the vulnerability publicly until it's resolved

### What to Include

- **Type** — XSS, injection, replay attack, etc.
- **Location** — Repository, file, line number
- **Impact** — What an attacker could do
- **Reproduction** — Step-by-step instructions
- **Fix suggestion** — (Optional) Proposed solution
- **Severity** — Critical, High, Medium, Low

### Response Timeline

| Timeframe | Action |
|---|---|
| **24 hours** | Initial acknowledgment |
| **7 days** | Assessment and confirmation |
| **30 days** | Fix released (depends on severity) |
| **60 days** | Public disclosure (if coordinated) |

## Security Best Practices

### For Contributors

- Never commit secrets, keys, or credentials
- Use environment variables for configuration
- Validate and sanitize all user inputs
- Use parameterized queries (Prisma prevents SQL injection)
- Follow the principle of least privilege

### For Smart Contracts (Soroban)

- Check authorization on all sensitive functions
- Reentrancy guards where applicable
- Input validation on all public functions
- Comprehensive test coverage
- Use `testutils` for thorough contract testing

### For API Servers

- CORS configured for specific origins
- Input validation with Zod schemas
- Rate limiting on sensitive endpoints
- No debug information in production errors
- HTTPS in production

## Dependency Management

- Regular `npm audit` and `cargo audit` runs
- Automated Dependabot alerts enabled
- Review and update dependencies quarterly
- Pin major versions in production

## Acknowledgments

We appreciate the security community's help in keeping our projects safe. Contributors who responsibly disclose vulnerabilities will be acknowledged (with permission).

---

Thank you for helping keep the ecosystem secure!
