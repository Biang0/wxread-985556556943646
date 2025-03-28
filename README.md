# 📖 wxread

**本项目基于 [findmover/wxread](https://github.com/findmover/wxread) 进行优化和扩展。**  
使用 **GitHub Actions** 自动执行 **微信阅读任务**，支持 **定时触发** 和 **手动触发**，可自由配置 **阅读时间**、**执行延迟** 和 **调试定时任务**。  

**⚠️ 注意：GitHub Actions 每月只有 2000 分钟的免费配额（公开仓库不受影响，fork默认公开）。**

---

## 🚀 功能介绍  

✅ **自动模式**：根据不同的定时任务规则，生成相应范围内的随机阅读时间，并默认有 `0 - 20` 分钟随机延迟（若手动触发自动模式且未设置自定义延迟）。  

✅ **手动模式**：  
   - 若未输入自定义阅读时间，会生成 `60 - 120`（等效 `30 - 60` 分钟）之间的随机阅读时间。  
   - 若输入了自定义阅读时间，则使用该时间乘以 2 作为 `READ_NUM` 用于后续任务。  

✅ **自定义延迟**：手动触发时，可使用 `custom_delay` 手动设置任务执行的延迟时间（单位：分钟）。  

✅ **调试定时任务**：可选择指定的定时任务进行调试，开启后按指定任务时间运行。  

✅ **周随机任务检查**：周随机任务有一定概率跳过执行。  

✅ **智能计算 READ_NUM**：
   - **自动模式**：基于定时规则生成合适的阅读时间。  
   - **手动模式**：支持 `custom_time` 设定阅读时长，或使用系统默认值。  
   - **自定义 `custom_delay`** 控制任务启动时间。  

✅ **主逻辑处理**：`main.py` 文件负责整个阅读任务的主逻辑，包括字段拼接、模拟请求、处理 Cookie 过期、记录运行数据和发送推送通知等。
✅ **自定义配置**：`config.py` 文件用于自定义配置，如阅读次数、推送 token 等，同时还能解析 Curl 命令获取请求头和 Cookie 信息。

---

## ⏰ 触发方式  

本项目支持 **GitHub Actions 触发**，包括 **定时触发** 和 **手动触发**，可根据需求进行配置。  

### 1️⃣ **自动触发（定时任务）**  

- **定时任务触发时，`READ_NUM` 自动生成**，无需手动设置。  
- 若手动触发自动模式但未设置 `custom_delay`，则会 **随机延迟 `0 - 20` 分钟**，防止固定时间触发导致异常。  
- 可使用 `custom_delay` 自定义具体延迟时间，确保任务在指定时间运行。  

**默认定时任务时间（北京时间 UTC+8）**：  

| 任务类型 | 触发时间（北京时间） | UTC 时间 | 生成 READ_NUM 范围 |
|----------|------------------|---------|---------------------|
| 晚间任务 | 每天 22:00       | 14:00   | 190 - 220           |
| 早间任务 | 周一至周五 05:00 | 前一天 21:00 | 130 - 160           |
| 午间任务 | 周一至周五 11:40 | 03:40   | 160 - 190           |
| 周末任务 | 周六 & 周日 17:00 | 09:00   | 190 - 220           |
| 周随机任务 | 周六 & 周日 08:00 | 前一天 00:00 | 120 - 180           |

### 2️⃣ **手动触发（GitHub Actions 触发）**  

可通过 **GitHub Actions** 手动触发阅读任务，并自由配置 **阅读时间**、**延迟** 和 **调试定时任务**。  

| 输入项 | 类型 | 描述 | 是否必填 | 默认值 | 选项 |
| --- | --- | --- | --- | --- | --- |
| mode | 选择框 | 选择运行模式：<br> - **自动**：自动模式，根据不同的定时任务规则，生成相应范围内的随机阅读时间。如果是手动触发且未设置自定义延迟，会有 `0 - 20` 分钟的随机延迟。<br> - **手动**：手动模式，若未输入自定义阅读时间，会生成 `60 - 120`（等效 `30 - 60` 分钟）之间的随机阅读时间；若输入了自定义阅读时间，则使用该时间计算阅读次数。 | 是 | 手动 | 自动、手动 |
| custom_time | 字符串 | 手动输入自定义阅读时间（分钟，仅手动触发有效）。输入后，会将该时间乘以 2 作为 `READ_NUM` 用于后续任务。 | 否 | 无 | 无 |
| custom_delay | 字符串 | 手动输入自定义延迟时间（分钟，仅手动触发有效）。输入后，任务会延迟相应的分钟数执行。 | 否 | 无 | 无 |
| debug_schedule | 选择框 | 选择调试定时任务，开启后按指定任务时间运行 | 否 | 无 | 无、晚间任务、早间任务、午间任务、周末任务、周随机任务 |

---

## ⚠️ 处罚介绍

请注意，使用自动化工具进行阅读可能违反平台的相关规则。如果被检测到，可能会受到以下处罚：  

1. **阅读时长不计入总时长**：系统可能会判定异常行为，使阅读时间不被计入有效阅读时长。  
2. **账号限制**：可能会出现账号阅读权限被限制的情况，影响后续正常使用。  
3. **封号风险**：如被多次检测到违规操作，严重情况下可能导致账号封禁。  
4. **设备异常警告**：如果系统检测到短时间内高频率的阅读行为，可能会触发设备异常提醒或验证码验证。  
5. **任务执行失败**：如服务器检测到异常活动，可能会导致任务无法正常执行。  

**降低风险的建议**：  
- **避免高频触发任务**：合理安排执行时间，避免过于频繁地运行任务。  
- **阅读时间保持合理**：设定 `READ_NUM` 在 **正常阅读时长范围内**，不要设定过高的值。  
- **定期检查账号状态**：留意是否收到异常提醒，防止账户受限。  
- **使用多种触发模式**：结合自动模式与手动模式，避免固定时间触发导致异常。  

---

## 🚀 **执行流程**  

### **GitHub Actions 自动执行**  
1. **定时任务** 会在设定时间自动触发，无需手动干预。  
2. **手动模式** 可在 GitHub Actions 界面手动触发，并自由设定 `custom_time`、`custom_delay` 和 `debug_schedule`。  
3. **随机延迟**：在自动模式或计划模式或手动触发调试模式无自定义延迟时，会生成 `0 - 20` 分钟的随机延迟。  
4. **自定义延迟**：手动触发且设置了 `custom_delay` 时，任务会延迟相应的分钟数执行。  
5. **周随机检查**：周随机任务有一定概率跳过执行。  
6. **生成 `READ_NUM`**：根据触发方式和输入参数，生成相应的 `READ_NUM`。  
7. **执行 `main.py`**：执行主逻辑文件 `main.py` 完成阅读任务，具体步骤如下：
    - 从 `config.py` 中获取选定书籍，并更新请求数据中的 `b` 值。
    - 循环进行阅读请求，每次请求前更新动态参数，如 `ct`、`ts`、`rn`、`sg` 和 `s`。
    - 发送阅读请求，若请求成功则累计阅读时间，每次阅读间隔 30 秒；若请求失败且提示 Cookie 过期，则尝试刷新 `wr_skey` 密钥。
    - 若刷新密钥成功，则重新尝试本次阅读；若刷新失败，则终止运行并发送错误推送。
    - 阅读任务完成后，发送推送通知（若配置了推送方式），并记录运行数据到 `run_data.log` 文件。

### **本地运行**  
如需在本地运行，请手动执行：python main.py
---

## 🔧 **环境变量（Secrets）**  

**⚠️ 请在 GitHub Secrets 中配置 `WXREAD_CURL_BASH`，否则无法正常运行。**  

| 变量名 | 说明 | 必填 |
|--------|------|------|
| `READ_NUM` | 阅读次数，若未设置，默认值为 120。可在 `config.py` 中配置，也可通过环境变量设置。 | ❌ |
| `PUSH_METHOD` | 推送方式（pushplus / wxpusher / telegram），用于阅读任务完成后的消息推送。 | ❌ |
| `PUSHPLUS_TOKEN` | pushplus 推送 Token，若使用 pushplus 推送方式，则需要配置该 Token。 | ❌ |
| `WXPUSHER_SPT` | wxpusher 推送 Token，若使用 wxpusher 推送方式，则需要配置该 Token。 | ❌ |
| `TELEGRAM_BOT_TOKEN` | Telegram Bot Token，若使用 Telegram 推送方式，则需要配置该 Bot Token。 | ❌ |
| `TELEGRAM_CHAT_ID` | Telegram Chat ID，若使用 Telegram 推送方式，则需要配置该 Chat ID。 | ❌ |
| `WXREAD_CURL_BASH` | 微信阅读 API 的 curl_bash 数据，用于解析请求头和 Cookie 信息。若未设置，将使用默认的请求头和 Cookie。 | ✅ |
| `READ_COMPLETE_HEADER` | 阅读完成通知的标题，若未设置，默认值为 "🎉 微信阅读已完成！"。 | ❌ |
    
