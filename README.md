# Daily Sentinel

基于 GitHub Actions 的自动提醒工具，使用 Bark 推送通知到 iOS 设备。

## 功能

| 功能 | 时间 | 说明 |
|------|------|------|
| 💤 睡觉提醒 | 23:30 | 每晚提醒睡觉，支持随机话语 |
| ☀️ 天气预报 | 07:30 | 推送当日天气（wttr.in 免费 API） |
| 🎂 纪念日提醒 | 08:00 | 支持农历/新历，提前 N 天提醒 |
| 📝 每日一言 | 09:00 | 推送名言/诗词/影视台词（一言 API） |
| 🐱 GitHub 提醒 | 21:30 | 今天没有提交代码则提醒 |

## 配置

### 1. Fork 或 Clone 此仓库

```bash
git clone https://github.com/Dhgaj/daily-sentinel.git
```

### 2. 配置 GitHub Secrets

在仓库的 **Settings** → **Secrets and variables** → **Actions** 中添加：

| Secret 名称 | 说明 | 必填 |
|------------|------|------|
| `BARK_SERVER` | Bark 服务器地址 | 是 |
| `BARK_KEY` | Bark 设备 Key（从 Bark App 获取） | 是 |
| `SLEEP_REMINDER_TITLE` | 睡觉提醒标题 | 否（默认：该睡觉啦） |
| `WEATHER_LOCATION` | 天气预报地点，支持中文（如：广州黄埔区） | 否（默认：深圳） |
| `ANNIVERSARIES` | 纪念日配置（JSON 格式，见下方说明） | 否 |

### 3. 配置纪念日

在 `ANNIVERSARIES` Secret 中添加 JSON 格式配置：

```json
[
  {"name": "测试0", "date": "1965-05-15", "type": "lunar", "advance": 3},
  {"name": "测试1", "date": "1968-08-20", "type": "solar", "advance": 3},
  {"name": "测试2", "date": "2020-10-01", "type": "solar", "advance": 7}
]
```

- `name`: 纪念日名称
- `date`: 日期（格式：YYYY-MM-DD，年份仅用于记录，实际按月日匹配）
- `type`: `lunar`（农历）或 `solar`（新历）
- `advance`: 提前几天开始提醒（默认 3 天）

### 4. 自定义提醒话语

编辑 `messages/sleep_reminder.txt` 文件，每行一句话，`#` 开头的行为注释。

### 5. 自定义提醒时间

修改 `.github/workflows/sleep_reminder.yml` 中的 cron 表达式：

```yaml
schedule:
  - cron: '30 15 * * *'  # UTC 时间，需换算为本地时间
```

## 本地测试

### 测试 Bark 推送

```bash
# 替换 YOUR_SERVER 和 YOUR_KEY 为你的配置
curl "https://YOUR_SERVER/YOUR_KEY/测试标题/测试内容"

# 示例
curl "https://api.day.app/xxxxx/Hello/World"
```

### 测试天气 API

```bash
# 查看某地天气（支持中文地名）
curl "https://wttr.in/广州黄埔区?format=j1&lang=zh" | jq '.current_condition[0]'

# 查看简洁天气
curl "https://wttr.in/广州?lang=zh&format=3"

# 查看详细天气（终端展示）
curl "https://wttr.in/广州?lang=zh"
```

## GitHub Actions 测试

在 GitHub 仓库的 **Actions** 页面，选择对应的 workflow，点击 **Run workflow** 即可手动触发测试。

## 项目结构

```
daily-sentinel/
├── .github/workflows/            # GitHub Actions 工作流
│   ├── sleep_reminder.yml        # 睡觉提醒
│   ├── weather_report.yml        # 天气预报
│   ├── anniversary_reminder.yml  # 纪念日提醒
│   ├── daily_quote.yml           # 每日一言
│   ├── github_reminder.yml       # GitHub 提交提醒
│   └── changelog.yml             # 自动生成 CHANGELOG
├── messages/                     # 提醒话语文件
│   └── sleep_reminder.txt        # 睡觉提醒话语
├── .gitignore
├── LICENSE
└── README.md
```

## 常见问题

### 为什么收不到推送？

1. 检查 Secrets 是否配置正确
2. 查看 Actions 运行日志是否成功
3. 确认 Bark App 是否正常运行

### 时间不对？

GitHub Actions 使用 UTC 时间，北京时间需要 **-8 小时**。

例如：本地 08:00 = UTC 00:00，cron 应设为 `0 0 * * *`

### 如何修改每日一言的内容类型？

编辑 `daily_quote.yml` 中的 API 参数 `c=`：

- `a` 动画
- `b` 漫画
- `c` 游戏
- `d` 文学
- `h` 影视
- `i` 诗词
- `k` 哲学
- `l` 抗机灵

## License

MIT
