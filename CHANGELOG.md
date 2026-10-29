## Unreleased

### ✨ Features

- 新增 AI 校对功能：修正错别字/同音误写并删除乱码。先查百科建立专有名词白名单再改，宁可不改不可改坏
- AI 校对支持四种入口：下载流程内置（合并成品前校对章节缓存）、TUI 菜单 r、CLI `--proofread <路径>`、WebUI 一键异步校对（SSE 实时进度）
- 生成「书名 (作者) 校对记录.log」，逐条记录每处改动与被引擎拒绝的建议
- config.ini 新增 `[ai]` 段；密钥支持环境变量 `SONOVEL_AI_KEY` 与 `-Dai.api-key`，避免明文入库
- WebUI 设置页新增 AI 校对分组，API 密钥只回显掩码且不回写

### 🐛 Bug Fixes

- 修复保存配置后 hutool Setting 缓存未失效，导致 WebUI 改的配置不生效

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