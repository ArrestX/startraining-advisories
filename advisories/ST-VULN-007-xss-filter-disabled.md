# StarTraining — 全局 XSS 过滤器关闭 (ST-VULN-007)

| 项 | 内容 |
|----|------|
| Vendor | zhistaredu |
| Product | StarTraining（职星学院） |
| Version | 3.8.1 |
| Type | Cross Site Scripting |
| CWE | CWE-79 |
| 认证 | N/A (defense-in-depth) |
| 严重度 | Medium |

## 说明

application.yml 里 xss.enabled=false，全局 XSS 过滤关着。

## 根因

配置开关关了。

## 利用链接 / 复现

配置项，不是单独接口：

- 文件：`edu-admin/src/main/resources/application.yml`
- 键：`xss.enabled: false`

配合 ST-VULN-006 看存储 XSS 更明显。相关后台接口示例：

- `http://127.0.0.1:8900/system/notice/list`（需 token）


## 请求包（Yakit）

```http
# Code/config verification — POST notice/content endpoints accept raw HTML when filter off
GET /system/notice/list HTTP/1.1
Host: 127.0.0.1:8900
Authorization: USER_TOKEN
Connection: close
```

期望：配置文件可见 xss.enabled: false。

## 截图

![xss-config](./screenshots/ST-VULN-007-xss-config.png)

（配置截图；也可只贴 yml 片段）

## 影响

公告等内容位更容易被 XSS 打穿，和 006 叠在一起更狠。

## 修复

生产改成 true。

## 关联

- 本地报告：`../POC_VERIFICATION_REPORT.md`
- 脚本：`poc_verify/startraining_poc_verify.py` / `startraining_jwt_forge.py`
