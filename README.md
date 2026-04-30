# KataBump Auto Renew 🔄

KataBump 免费服务器自动续期脚本，基于 Playwright + CDP 绕过 Cloudflare Turnstile。

## ✨ 特性

- 🛡️ **智能过盾**: CDP 协议模拟真实鼠标轨迹 + 屏幕坐标伪造，高成功率绕过 Cloudflare Turnstile
- 🔄 **自动重试**: 内置 20 次重试机制，验证失败自动刷新页面重来
- 👥 **多账号支持**: JSON 格式配置多账号批量续期
- 🌐 **代理支持**: 可选 Hysteria2 / HTTP 代理
- 📸 **截图留存**: 每次运行自动截图，上传 Artifacts
- 📱 **Telegram 通知**: 成功/失败/跳过均推送 TG 通知（含截图）
- ⏱️ **防仓库休眠**: 自动提交 time.txt，避免 GitHub 60天禁用 Actions

## 🚀 快速开始

### 1. Fork 本仓库

### 2. 配置 Secrets

进入 **Settings → Secrets and variables → Actions → New repository secret**

#### 必需

| Secret | 说明 | 格式 |
|--------|------|------|
| `USERS_JSON` | 账号信息 | 见下方 |

```json
[{"username": "your_email@example.com", "password": "your_password"}]
```

多账号：
```json
[{"username": "a@example.com", "password": "pass1"}, {"username": "b@example.com", "password": "pass2"}]
```

#### 可选

| Secret | 说明 | 格式 |
|--------|------|------|
| `HTTP_PROXY` | HTTP 代理 | `http://user:pass@host:port` |
| `HY2_PROXY_URL` | Hysteria2 代理 | `hysteria2://auth@host:port?sni=xxx` |
| `SOCKS_PORT` | SOCKS5 端口 (默认 51080) | `51080` |
| `TG_BOT_TOKEN` | Telegram Bot Token | `123456:ABC-DEF...` |
| `TG_CHAT_ID` | Telegram Chat ID | `123456789` |

### 3. 设置仓库权限

**Settings → Actions → General → Workflow permissions**

✅ 选择 **Read and write permissions**

### 4. 启用 Workflow

进入 **Actions** 页面，启用 Workflow。

- ⏰ 每天北京时间 10:30 自动运行
- 🔘 也可手动点击 **Run workflow** 立即测试

## 🔧 续期逻辑

1. 打开 `dashboard.katabump.com/auth/login`
2. 填入邮箱密码
3. **CDP 绕过 Turnstile**: 
   - 注入脚本 Hook `attachShadow`，获取 Turnstile checkbox 坐标比例
   - 通过 CDP `Input.dispatchMouseEvent` 发送真实鼠标事件
4. 点击登录
5. 进入服务器页面，点击 **Renew** 按钮
6. 模态框出现 → 再次 CDP 绕过 Turnstile → 点击确认
7. 检测续期结果：成功 / 未到时间 / 验证码失败（刷新重试）

## ⚠️ 注意事项

- KataBump 免费服务器**每 4 天**需续期一次
- 过期后 **1 天**即永久删除，请确保脚本正常运行
- 建议每天运行一次，不要等最后一天
- 如果 Turnstile 多次失败，脚本会自动刷新页面重试（最多 20 次）

## 📁 项目结构

```
├── .github/workflows/
│   └── kata-renew.yml     # GitHub Actions 工作流
├── action_renew.js         # 续期主脚本 (CI 环境)
├── package.json            # Node.js 依赖
├── .gitignore
├── time.txt                # 自动更新 (防仓库休眠)
└── README.md
```

## 📄 License

MIT
