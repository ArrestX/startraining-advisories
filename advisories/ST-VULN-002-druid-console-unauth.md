# StarTraining — Druid 监控控制台未授权访问 (ST-VULN-002)

| 项 | 内容 |
|----|------|
| Vendor | zhistaredu |
| Product | StarTraining（职星学院） |
| Version | 3.8.1 |
| Type | Security Misconfiguration |
| CWE | CWE-306 / CWE-200 |
| 认证 | None |
| 严重度 | High |

## 说明

Spring Security 把 /druid/** 放行了，Druid 控制台账号密码还是空的。浏览器直接打开就能看数据源和 SQL。

## 根因

SecurityConfig 对 /druid/** 走 anonymous；statViewServlet 没设登录凭据。

## 利用链接 / 复现

浏览器直接开（匿名）：

- [http://127.0.0.1:8900/druid/index.html](http://127.0.0.1:8900/druid/index.html)
- [http://127.0.0.1:8900/druid/login.html](http://127.0.0.1:8900/druid/login.html)（账号密码空也能进）
- [http://127.0.0.1:8900/druid/sql.html](http://127.0.0.1:8900/druid/sql.html)


## 请求包（Yakit）

```http
GET /druid/index.html HTTP/1.1
Host: 127.0.0.1:8900
Connection: close
```

期望：匿名 GET /druid/index.html 返回 200 监控页。

## 截图

![druid](./screenshots/ST-VULN-002-druid.png)

<!-- 截图：浏览器打开上方链接，按 F1；或跑 python capture_startraining_evidence.py -->

## 影响

库连接信息、SQL、URI 统计裸奔，方便继续挖注入。

## 修复

生产关掉或加强鉴权，最好再加 IP 限制。

## 关联

- 本地报告：`../POC_VERIFICATION_REPORT.md`
- 脚本：`poc_verify/startraining_poc_verify.py` / `startraining_jwt_forge.py`
