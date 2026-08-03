# StarTraining Security Advisories

Lab target: `http://127.0.0.1:8900`

| ID | File | Severity | Auth |
|----|------|----------|------|
| ST-VULN-001 | [Hardcoded JWT secret allows forged identity tokens](./ST-VULN-001-jwt-hardcoded-secret-forge.md) | Critical | None (offline forge with default secret) |
| ST-VULN-002 | [Unauthenticated Druid monitor console access](./ST-VULN-002-druid-console-unauth.md) | High | None |
| ST-VULN-003 | [Unauthenticated Swagger / api-docs exposure](./ST-VULN-003-swagger-api-docs-unauth.md) | High | None |
| ST-VULN-004 | [Broken data-scope check enables arbitrary role reassignment (IDOR)](./ST-VULN-004-idor-auth-role-privesc.md) | Critical | Any authenticated user (low-priv token sufficient) |
| ST-VULN-005 | [Role management APIs missing method-level authorization](./ST-VULN-005-role-crud-missing-preauthorize.md) | High | Any authenticated user |
| ST-VULN-006 | [HTML upload allowed and /profile/** served anonymously (stored XSS)](./ST-VULN-006-stored-html-upload-xss.md) | High | Authenticated user with upload permission |
| ST-VULN-007 | [Global XSS filter disabled](./ST-VULN-007-xss-filter-disabled.md) | Medium | N/A (defense-in-depth) |

## Verify

```bash
cd ../../poc_verify
python3 startraining_poc_verify.py http://127.0.0.1:8900
python3 capture_startraining_evidence.py
python3 generate_startraining_advisories.py
```

Accounts: `admin` / `wukong`, password `admin123`.

