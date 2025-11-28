# Security Guidelines

## Critical Security Requirements

### 1. Authentication Security

**MANDATORY REQUIREMENTS:**
- Never store passwords in plain text
- Use bcrypt, scrypt, or Argon2 for password hashing
- Implement account lockout after 5 failed login attempts
- Enforce strong password policies (minimum 8 characters, mixed case, numbers, symbols)
- Use multi-factor authentication (MFA) for sensitive operations

### 2. Authorization Checks

**CRITICAL: Every protected operation must verify:**
1. User is authenticated
2. User has permission for the specific resource
3. User owns the resource or has delegation rights

**Example Security Check:**
```java
public void deleteDocument(String documentId, String userId) {
    // 1. Verify authentication
    if (!isAuthenticated(userId)) {
        throw new UnauthorizedException("User not authenticated");
    }

    // 2. Check ownership
    Document doc = documentRepository.findById(documentId);
    if (!doc.getOwnerId().equals(userId)) {
        throw new ForbiddenException("User does not own this document");
    }

    // 3. Proceed with operation
    documentRepository.delete(documentId);
    logger.info("Document deleted: " + documentId + " by user: " + userId);
}
```

### 3. Injection Prevention

**SQL Injection:**
- ALWAYS use parameterized queries
- NEVER concatenate user input into SQL strings

**Command Injection:**
- Validate and sanitize all input used in system commands
- Use allowlists for permitted values
- Avoid shell execution where possible

**LDAP/XML Injection:**
- Escape special characters
- Use safe parsing libraries

### 4. Sensitive Data Protection

**Data Classification:**
- **Highly Sensitive**: Passwords, SSN, credit cards, health records
- **Sensitive**: Email addresses, phone numbers, addresses
- **Internal**: Business data, logs
- **Public**: Marketing materials

**Protection Requirements:**
- Encrypt highly sensitive data at rest (AES-256)
- Use TLS 1.2+ for data in transit
- Never log sensitive data
- Implement data retention policies
- Support data deletion requests (GDPR compliance)

### 5. Session Management

**Requirements:**
- Generate cryptographically random session IDs
- Regenerate session ID after login
- Implement session timeout (15 minutes for sensitive, 30 minutes for normal)
- Invalidate sessions on logout
- Use secure, httpOnly, sameSite cookies

### 6. Error Handling

**Security Best Practices:**
- Never expose stack traces to end users
- Don't reveal system information in error messages
- Log detailed errors server-side only
- Return generic error messages to clients

**Bad:**
```
Error: Database connection failed at mysql://internal-db.company.com:3306
User 'admin' denied access to table 'users'
```

**Good:**
```
An error occurred while processing your request. Please try again later.
(Error ID: 12345 for support reference)
```

### 7. Dependency Security

**MANDATORY:**
- Keep all dependencies up to date
- Scan dependencies for known vulnerabilities
- Review security advisories regularly
- Use dependency management tools (npm audit, OWASP Dependency-Check)

### 8. API Security

**REST API Requirements:**
- Require authentication for all non-public endpoints
- Use API keys or OAuth 2.0 tokens
- Implement rate limiting to prevent abuse
- Validate all input parameters
- Use HTTPS only

### 9. File Upload Security

**CRITICAL CHECKS:**
- Validate file type (don't trust MIME type, check magic bytes)
- Limit file size
- Scan for malware
- Store uploads outside web root
- Generate random filenames
- Implement access controls

**Dangerous File Extensions to Block:**
- Executables: .exe, .dll, .bat, .sh, .cmd
- Scripts: .js, .vbs, .php, .jsp, .asp
- Archives: .zip (if not scanned)

### 10. Cross-Site Scripting (XSS) Prevention

**Defense:**
- Escape all user input before rendering
- Use Content Security Policy (CSP) headers
- Set X-XSS-Protection header
- Validate input on both client and server
- Use framework auto-escaping features

### 11. Cross-Site Request Forgery (CSRF)

**Protection:**
- Use CSRF tokens for state-changing operations
- Verify Origin/Referer headers
- Require re-authentication for sensitive actions
- Use SameSite cookie attribute

### 12. Security Headers

**Required HTTP Headers:**
```
Strict-Transport-Security: max-age=31536000; includeSubDomains
X-Frame-Options: DENY
X-Content-Type-Options: nosniff
Content-Security-Policy: default-src 'self'
X-XSS-Protection: 1; mode=block
Referrer-Policy: strict-origin-when-cross-origin
```

### 13. Secrets Management

**PROHIBITED:**
- Hardcoding API keys, passwords, or tokens in code
- Committing secrets to version control
- Storing secrets in configuration files in the repo

**REQUIRED:**
- Use environment variables
- Use secret management systems (HashiCorp Vault, AWS Secrets Manager)
- Rotate secrets regularly
- Implement the principle of least privilege

### 14. Audit Logging

**Required Audit Events:**
- Authentication attempts (success and failure)
- Authorization failures
- Password changes
- Privilege escalations
- Data access (for sensitive data)
- Configuration changes

**Audit Log Requirements:**
- Include timestamp, user ID, action, resource, result
- Store securely with tamper detection
- Retain for compliance period
- Monitor for suspicious patterns

### 15. Security Testing

**Mandatory Tests:**
- [ ] SQL injection testing
- [ ] XSS vulnerability testing
- [ ] Authentication bypass attempts
- [ ] Authorization checks
- [ ] Session management testing
- [ ] Cryptography validation
- [ ] Dependency vulnerability scanning

**Tools:**
- OWASP ZAP for dynamic testing
- SonarQube for static analysis
- npm audit / Snyk for dependency scanning

### 16. Incident Response

**When a security incident is detected:**
1. Isolate affected systems
2. Preserve evidence and logs
3. Notify security team immediately
4. Document timeline and impact
5. Implement fixes
6. Conduct post-mortem review

### 17. Compliance Requirements

**Applicable Standards:**
- OWASP Top 10 - Address all vulnerabilities
- PCI DSS - If handling payment cards
- HIPAA - If handling health information
- GDPR - If handling EU citizen data
- SOC 2 - For service organizations

### 18. Code Review Security Checklist

Before approving code, verify:

- [ ] No hardcoded secrets or credentials
- [ ] All inputs are validated and sanitized
- [ ] Authentication and authorization checks present
- [ ] Parameterized queries used for database access
- [ ] Error messages don't leak sensitive information
- [ ] Sensitive data is encrypted
- [ ] Proper session management implemented
- [ ] Security headers are set
- [ ] File uploads are properly validated
- [ ] Dependencies are up to date and scanned
- [ ] Logging doesn't include sensitive data
- [ ] HTTPS is enforced

## Red Flags - Report Immediately

If you see any of these in code review, **STOP** and report to security team:

1. Plain text passwords
2. SQL concatenation with user input
3. Disabled SSL/TLS verification
4. Eval() or exec() with user input
5. Disabled security features "for convenience"
6. Custom cryptography implementations
7. Secrets in code or commits
8. XXE (XML External Entity) vulnerabilities

## Security Contact

For security concerns or to report vulnerabilities:
- Email: security@company.com
- Slack: #security-team
- Emergency Hotline: [REDACTED]

---

**Remember: Security is everyone's responsibility. When in doubt, ask the security team.**

*Last Updated: 2025-01-28*
*Version: 1.0*
