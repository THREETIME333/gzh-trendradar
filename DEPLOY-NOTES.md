# gzh-trendradar 部署说明（「智能体实测局」）

- 来源：sansan0/TrendRadar v6.11.0（GPL-3.0）。建库必须用 **"Use this template"**（不要点 Fork：Issue #606，Fork 的仓库触发不了工作流；"Include all branches" 勾上）。
- 仓库：THREETIME333/gzh-trendradar（公开，公开才能让所有人直接读 Pages 报告）。
- 运行：GitHub Actions，每天 2 次（北京时间 08:33 / 20:33，cron `33 0,12 * * *`），不用服务器、不用 Key、不花钱。
- 模式：关键词匹配（filter.method=keyword），报告模式 incremental（只推新增、零重复）。
- 关键词：`config/frequency_words.txt`，11 组（AI 免费额度、搞钱副业、学生、DeepSeek、Claude、Gemini、国产开源模型、OpenAI、AI 工具、公众号赚钱、云服务器）。
- 报告：每次运行把根目录 `index.html` + 当日 DB 提交回仓库。GitHub Pages 开启方法：仓库 Settings → Pages → Build and deployment → Source 选 "Deploy from a branch"，Branch 选默认分支、目录选 /(root)，保存。开启后报告地址是 `https://THREETIME333.github.io/gzh-trendradar/`，雷达直接读这个地址。
- 7 天试用周期：GitHub Actions 路线每 7 天自动停，必须每 7 天内跑一次 "Check In" 工作流（仓库 Actions → Check In → Run workflow）续期，否则 "Get Hot News" 会被自动禁用。长期用要么记得签到，要么以后迁 Docker。
- 邮件推送：未配。配的时候在仓库 Settings → Secrets and variables → Actions 里加 5 个 Secrets：EMAIL_FROM（发件 QQ 邮箱）、EMAIL_PASSWORD（QQ 邮箱授权码）、EMAIL_TO（收件邮箱）、EMAIL_SMTP_SERVER（smtp.qq.com）、EMAIL_SMTP_PORT（465）。授权码绝不进聊天、不进仓库文件。
- 和上游不同的地方（本仓库自改）：
  1. `.github/workflows/crawler.yml` 加了"提交报告回仓库"一步——上游 README 已说明 GitHub Actions 自动存储功能已下线，不加这一步，生成的 index.html 会随 runner 一起消失，Pages 读不到。每天 2 次、每次 1 个小提交，负载可忽略；同时把 `permissions.contents` 从 read 改成 write。
  2. `config/config.yaml`：report.mode 从 current 改成 incremental；ai_analysis.enabled 和 ai_translation.enabled 从 true 改成 false（没配 AI Key，开着会报错；以后要 AI 分析再配 Key、再打开）。
  3. cron 从每小时（`33 * * * *`）改成每天 2 次（`33 0,12 * * *`），别抢 newsnow 免费 API 的量。
  4. `config/frequency_words.txt` 换成本号专用的 11 组关键词（上游默认是作者个人的品牌/国家/科技词）。
- 历史数据：`output/news/*.db`、`output/rss/*.db` 按天存，增量去重靠这些 DB，删了就等于重置；30 天前的 DB 由工作流自动清理。`output/html/` 的历史快照不提交回仓库（控制体积），雷达只读根目录最新 `index.html`。
- 默认监控 11 个平台：今日头条、百度热搜、华尔街见闻、澎湃新闻、bilibili 热搜、财联社热门、凤凰网、贴吧、微博、抖音、知乎；RSS 默认 3 个源：Hacker News、雅虎财经（阮一峰的网络日志默认禁用）。
