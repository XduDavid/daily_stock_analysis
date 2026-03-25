# GitHub Actions 部署 Step by Step 指南

本文档提供完整的 GitHub Actions 零成本部署步骤。

## 📋 前置准备

你需要准备：
1. 一个 GitHub 账号（免费即可）
2. AI 模型 API Key（至少一个）
3. 通知渠道配置（至少一个）

---

## 🚀 第一步：Fork 项目仓库

1. 访问项目地址：https://github.com/ZhuLinsen/daily_stock_analysis
2. 点击右上角的 **Fork** 按钮
3. 选择你的个人账号作为目标
4. 等待 Fork 完成（通常几秒钟）

---

## 🔑 第二步：准备必要的 API Keys

### AI 模型配置（至少选一个）

| 方案 | 获取地址 | 说明 |
|------|---------|------|
| **AIHubMix** | https://aihubmix.com/?aff=CfMq | 国内用户首选，一个 Key 用所有模型，无需科学上网 |
| **Gemini** | https://aistudio.google.com/ | 免费额度可用 |
| **DeepSeek** | https://platform.deepseek.com | 性价比高 |
| **OpenAI 兼容** | 各第三方平台 | 支持任何 OpenAI 兼容 API |

### 新闻搜索（强烈推荐配置）

- **Tavily**：https://tavily.com/ - 注册即可获取免费额度

---

## 📢 第三步：准备通知渠道（至少选一个）

### 方式一：企业微信（最简单）
1. 打开企业微信，创建一个群聊
2. 群设置 → 添加群机器人
3. 复制 Webhook URL

### 方式二：邮件通知
1. 准备发件人邮箱（如 QQ 邮箱）
2. 获取邮箱授权码（非登录密码）：
   - QQ 邮箱：设置 → 账户 → POP3/IMAP → 生成授权码

### 方式三：Telegram
1. 联系 [@BotFather](https://t.me/BotFather) 创建机器人，获取 Token
2. 给机器人发任意消息
3. 访问 `https://api.telegram.org/bot<你的Token>/getUpdates` 获取 Chat ID

### 更多方式
飞书、Discord、Slack、PushPlus、Server酱 等都支持，详见项目文档。

---

## 📊 第四步：准备股票列表

选择你要分析的股票，格式说明：

| 市场 | 格式 | 示例 |
|------|------|------|
| A股 | 6位数字 | `600519` (贵州茅台) |
| 港股 | `hk` + 5位 | `hk00700` (腾讯控股) |
| 美股 | 英文代码 | `AAPL` (苹果) |

多个股票用逗号分隔：
```
600519,300750,hk00700,AAPL
```

---

## ⚙️ 第五步：配置 GitHub Secrets 和 Variables

进入你 Fork 的仓库：

1. 点击顶部菜单 **Settings**
2. 左侧菜单找到 **Secrets and variables** → 点击 **Actions**
3. 你会看到两个标签页：**Secrets** 和 **Variables**

### 5.1 配置 Secrets（敏感信息）

点击 **New repository secret** 逐个添加：

| Secret 名称 | 说明 | 必填 |
|------------|------|:----:|
| `AIHUBMIX_KEY` | AIHubmix API Key | 选填* |
| `GEMINI_API_KEY` | Gemini API Key | 选填* |
| `OPENAI_API_KEY` | OpenAI/DeepSeek API Key | 选填* |
| `OPENAI_BASE_URL` | OpenAI 兼容 API 地址 | 按需 |
| `TAVILY_API_KEYS` | Tavily 搜索 API Key | 推荐 |
| `WECHAT_WEBHOOK_URL` | 企业微信 Webhook | 选填** |
| `EMAIL_SENDER` | 发件人邮箱 | 选填** |
| `EMAIL_PASSWORD` | 邮箱授权码 | 选填** |
| `TELEGRAM_BOT_TOKEN` | Telegram Bot Token | 选填** |
| `TELEGRAM_CHAT_ID` | Telegram Chat ID | 选填** |

> \* AI 相关**至少配置一个**
>
> \*\* 通知渠道**至少配置一个**

### 5.2 配置 Variables（非敏感配置）

切换到 **Variables** 标签页，点击 **New repository variable**：

| Variable 名称 | 说明 | 示例值 | 必填 |
|--------------|------|--------|:----:|
| `STOCK_LIST` | 自选股列表 | `600519,300750,AAPL` | ✅ |
| `REPORT_TYPE` | 报告类型 (`simple`/`full`/`brief`) | `simple` | 可选 |
| `MARKET_REVIEW_REGION` | 大盘复盘区域 (`cn`/`us`/`both`) | `cn` | 可选 |
| `MAX_WORKERS` | 并发线程数 | `3` | 可选 |
| `TRADING_DAY_CHECK_ENABLED` | 交易日检查 | `true` | 可选 |

> 💡 提示：`STOCK_LIST` 配置在 Variables 中比 Secrets 更方便后续修改。

---

## ▶️ 第六步：启用 GitHub Actions

1. 点击顶部菜单的 **Actions**
2. 如果看到提示 "Workflows aren't being run"，点击：
   **`I understand my workflows, go ahead and enable them`**
3. 左侧会看到工作流列表，确认 "每日股票分析" 已存在

---

## 🧪 第七步：手动测试运行

1. 在 Actions 页面，左侧点击 **每日股票分析**
2. 点击右侧的 **Run workflow** 按钮（下拉菜单）
3. 填写运行参数：
   - **运行模式**：选择 `full`（完整分析：个股 + 大盘复盘）
   - **强制运行**：勾选（今天可能是非交易日，强制跳过检查）
4. 点击绿色的 **Run workflow** 按钮确认
5. 刷新页面，你会看到一个新的 workflow 正在运行
6. 点击进入查看实时日志
7. **等待 5-10 分钟**，分析完成后你应该收到通知

---

## ✅ 第八步：完成！

恭喜！现在你的 GitHub Actions 已经配置完成。

- 默认：每个工作日（周一到周五）**北京时间 18:00** 自动执行分析并推送
- 随时可以手动触发运行
- 分析报告和日志会保留 30 天，可以在 Actions 中下载

---

## 📝 验证清单

部署完成后，请确认：

- [ ] 已 Fork 仓库到自己账号
- [ ] 已配置至少一个 AI API Key
- [ ] 已配置至少一个通知渠道
- [ ] 已配置 `STOCK_LIST`
- [ ] 已启用 GitHub Actions
- [ ] 手动测试运行成功
- [ ] 收到了分析报告通知

---

## 🔧 修改运行时间

如果需要修改自动运行时间：

1. 在你的仓库中编辑 `.github/workflows/daily_analysis.yml`
2. 修改这一行：
   ```yaml
   schedule:
     - cron: '0 10 * * 1-5'
   ```
   - 第一个数字是**分钟**（UTC）
   - 第二个数字是**小时**（UTC）
   - UTC 10:00 = 北京时间 18:00
   - 其他时间请自行转换

---

## ❓ 常见问题排查

### Q: 运行失败，没收到通知
**A:** 查看 Actions 日志：点击失败的 workflow run → "执行股票分析" 步骤查看详细错误信息。

### Q: 提示"今日所有相关市场均为非交易日，跳过执行"
**A:** 手动触发时勾选 "强制运行"，或在 Variables 中设置 `TRADING_DAY_CHECK_ENABLED=false`。

### Q: 提示"未检测到 API Key"
**A:**
- 确认是在 **Secrets** 标签页添加的，不是 Variables
- 确认名称拼写完全正确（区分大小写）
- GitHub Secrets 修改后下次运行自动生效

### Q: 怎么添加更多股票？
**A:** 进入 Settings → Secrets and variables → Actions → Variables → 点击 `STOCK_LIST` → Edit 修改 → 点击 Save，下次运行自动生效。

### Q: 免费的 GitHub Actions 够用吗？
**A:** 完全够用。每天运行一次，每个月总分钟数远远低于免费配额（2000分钟/月）。

---

## 📄 更多文档

- [完整配置指南](./full-guide.md)
- [LLM 配置指南](./LLM_CONFIG_GUIDE.md)
- [README](../README.md)
