## Unreleased

### ✨ Features

- 新增 AI 校对功能：修正错别字/同音误写并删除乱码。先查百科建立专有名词白名单再改，宁可不改不可改坏
- AI 校对支持四种入口：下载流程内置（合并成品前校对章节缓存）、TUI 菜单 r、CLI `--proofread <路径>`、WebUI 一键异步校对（SSE 实时进度）
- 生成「书名 (作者) 校对记录.log」，逐条记录每处改动与被引擎拒绝的建议
- config.ini 新增 `[ai]` 段；密钥支持环境变量 `SONOVEL_AI_KEY` 与 `-Dai.api-key`，避免明文入库
- WebUI 设置页新增 AI 校对分组，API 密钥只回显掩码且不回写
- `[ai] provider` 内置预设：接入本机 EAC router 的千问国内版（`eac-qwen-cn`）与国际版（`eac-qwen-intl`），
  地址/模型/密钥全自动，零配置即可用；国内版插件跑起来就能用，国际版需 Qoder 账号登录
- AI 请求失败（连不上网关 / 未登录 / 限流 / 区域受限）时，命令行与审计日志输出中文原因 + 下一步建议，
  并保留网关原文，不再甩一段看不懂的 JSON

### 🐛 Bug Fixes

- 修复保存配置后 hutool Setting 缓存未失效，导致 WebUI 改的配置不生效
- 修复校对路径缺 `BookContext` 导致整本书校对直接失败（`LogUtils` 取书名为 null）
- 修复送审块「请求失败」被计入「拒绝可疑建议」：两者含义相反（前者等于没校对、该重试，
  后者是已拦下危险改动、可放心），现分开计数并在摘要中单独告警
- 修复 HTML 片段坐标在解码空间计算导致回填错位：正文含实体（`&ldquo;`）时会覆盖标签或别处正文
- 修复 `Epub4jTest.readEpub` 读相对路径、`createEpub` 写 `target/`，路径不一致必然找不到文件
- 设置页 API 密钥 placeholder 改准确：原「留空表示不修改」与后端语义相反，照做会丢失已配置密钥

### ♻️ Refactor

- AI 服务不可用时跳过背景调研（专有名词只用于拒绝 AI 建议，不调 AI 时纯属浪费网络与全文分词）
- 建议定位改为「只在当前送审块内查找」：模型只看到本块，全书搜索既会把改动落到别处同名片段，
  又让整本书单文件场景的复杂度不可接受
- 节流下沉到引擎按「每次 AI 请求」执行，章级节流对「整本书一个文件」无效
- 测试可脱离开发者本地文件运行：补 `junit-jupiter-engine`（缺它时 surefire 静默跑 0 个用例）、
  新增 `src/test/resources/test-config.ini` 并由 surefire 注入 `-Dconfig.file`

## v1.11.0 (2026-07-01)

### ✨ Features

- WebUI 使用 alpinejs、tailwindcss 重构
- WebUI 增加搜索历史功能
- WebUI 搜索历史支持删除单项，增加删除确认框
- WebUI 增加搜索建议功能
- WebUI 增加书源列表功能 #317
- WebUI 增加已下书籍删除功能
- WebUI 增加下载进度条功能
- 新增书籍详情页解析属性 latestChapterUrl
- 添加 Windows 一键安装脚本及 README 说明

### 🐛 Bug Fixes

- 修复 WebUI 下载下拉菜单默认格式与显式格式去重
- 修复 BookDownloadServlet 和 BookDeleteServlet 路径穿越漏洞
- 修复 PdfMergeHandler 中 FileOutputStream 未安全关闭导致的资源泄漏
- 修复 CoverUpdater.fetchCover 中 InputStream 可能的资源泄漏
- 修复 FileUtils.sortFilesByName 潜在的空指针和数字解析异常
- 修复 Javet 5.0.8 下 ThreadLocal 频繁创建 V8Runtime 导致 V8 sandbox 虚拟地址空间耗尽 #336

### ♻️ Refactor

- 前端代码复用
- 抽取 applyCrawlSettings 方法简化重复 null 检查
- 将 search-filter 判断逻辑内聚到 SearchResultsHandler 中
- 预计算相似度消除重复 StrUtil.similar 调用，提取 weight 方法简化打分逻辑
- StrUtil.isEmpty() 改为更严格的 isBlank()
- 将全本小说网搜索特殊处理内联到规则文件，移除 SearchParserQuanben5.java
- 重构 LangType.java
- LangUtil 重命名为 LangUtils
- 删除冗余规则属性 pagination
- 降低搜索过滤阈值

### ⚡ Performance

- 优化 VirtualThreadLimiter.close() 忙等待模式（Phaser 替代自旋）
- 优化 AppConfigLoader.loadConfig() 重复读取配置文件

### 💄 UI

- 操作按钮统一替换为 svg 图标
- 历史记录和刷新按钮改为图标
- 设置按钮改为开关
- 将源站链接合并到源站列，删除前往按钮

### 🚸 Other Improvements

- 在 Windows cmd 中启用 ANSI 支持，防止彩色文字乱码 #334
- 清理 main.json 失效书源
- 更新单元测试