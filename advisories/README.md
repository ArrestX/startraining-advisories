# StarTraining（职星学院）Security Advisories

本地复现目标：`http://127.0.0.1:8900`

| ID | 文件 | 严重度 | 认证 |
|----|------|--------|------|
| ST-VULN-001 | [ST-VULN-001-jwt-hardcoded-secret-forge.md](./ST-VULN-001-jwt-hardcoded-secret-forge.md) | Critical | None (offline forge with default secret) |
| ST-VULN-002 | [ST-VULN-002-druid-console-unauth.md](./ST-VULN-002-druid-console-unauth.md) | High | None |
| ST-VULN-003 | [ST-VULN-003-swagger-api-docs-unauth.md](./ST-VULN-003-swagger-api-docs-unauth.md) | High | None |
| ST-VULN-004 | [ST-VULN-004-idor-auth-role-privesc.md](./ST-VULN-004-idor-auth-role-privesc.md) | Critical | Any authenticated user (low-priv token sufficient) |
| ST-VULN-005 | [ST-VULN-005-role-crud-missing-preauthorize.md](./ST-VULN-005-role-crud-missing-preauthorize.md) | High | Any authenticated user |
| ST-VULN-006 | [ST-VULN-006-stored-html-upload-xss.md](./ST-VULN-006-stored-html-upload-xss.md) | High | Authenticated user with upload permission |
| ST-VULN-007 | [ST-VULN-007-xss-filter-disabled.md](./ST-VULN-007-xss-filter-disabled.md) | Medium | N/A (defense-in-depth) |

## 一键核实

```bash
cd ../../poc_verify
python3 startraining_poc_verify.py http://127.0.0.1:8900
python3 capture_startraining_evidence.py
python3 generate_startraining_advisories.py
```

账号：`admin` / `wukong`，密码 `admin123`。

