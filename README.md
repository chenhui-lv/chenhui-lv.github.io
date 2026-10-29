# Chenhui Lv 的学术主页

本项目用于长期维护个人简介、研究方向、论文、学术活动及公开联系方式。

## 技术与目录
网站使用 Jekyll 4.3.3、Liquid、Sass、Bootstrap 5.3.3 和 Jekyll Scholar。

| 路径 | 用途 |
| --- | --- |
| _pages/ | 页面内容 |
| _data/ | 结构化内容，部分仍为模板示例 |
| _posts/ | 文章 |
| assets/ref.bib | 完整论文数据 |
| _layouts/、_includes/ | 页面模板与组件 |
| _sass/、assets/ | 样式与交互 |
| images/、papers/ | 图片和 PDF，新增前须确认公开范围 |
| _plugins/ | Jekyll 插件 |
| .github/workflows/ | 发布流程 |
| _config.yml | 身份、导航、文献和发布排除设置 |
| _site/、.jekyll-cache/ | 生成文件和缓存，不直接编辑 |

## 本地使用
在项目根目录操作。需要 Ruby、Bundler 及 Gemfile 中的依赖；是否完整及兼容，须以实际验证为准。依赖安装或升级应单独审查。

Gemfile.lock 纳入版本管理，锁定 Windows x64-mingw-ucrt 和 Ubuntu x86_64-linux 两个平台，并记录依赖校验值。使用锁文件指定的 Bundler 4.0.15；首次准备环境时执行 `gem install bundler -v 4.0.15`，再执行 `bundle _4.0.15_ install`。日常内容更新不运行 `bundle update`，不删除锁文件重新解析依赖。

2026-10-29 已验证 Windows Ruby 4.0.5 的冻结安装和生产构建。提交 a74213e 的 [Actions 40791952977](https://github.com/chenhui-lv/chenhui-lv.github.io/actions/runs/40791952977) 已在 Ubuntu 24.04.5 / Ruby 3.2.11 上使用 Bundler 4.0.15，按已提交的 Gemfile.lock 完成依赖安装和生产构建；日志中的完整锁文件与提交一致，产物上传与部署均跳过。详细基线、验证范围及更新流程见 [SITE_MAINTENANCE.md](SITE_MAINTENANCE.md)。

预览：
```sh
bundle exec jekyll serve
```
访问 http://localhost:4000 。启动网站.bat 使用同一命令。

完整构建：
```sh
bundle exec jekyll build
```

仅修改 JavaScript 时，需要 Node 工具和 package.json 中的依赖。esbuild 固定为 0.25.0，package-lock.json 纳入版本管理；首次生成后及后续有意更新依赖时审查锁文件差异，日常安装按锁文件执行：
```sh
npm ci --include=dev --include=optional
npm run build
```
构建从 assets/js/site.js 生成页面实际使用的 assets/js/site.min.js，两者同步维护。普通页面和论文更新无需运行 npm。

本地 JavaScript 验证基线为 Windows x64、Node 24.18.0、npm 11.16.0。Windows 和 Ubuntu 共用一份 package-lock.json，各自在目标平台安装，不能复制 node_modules 跨平台使用。Bootstrap 继续固定为 5.3.3；网页使用仓库内的 Bootstrap JS 和 Sass 文件，npm run build 不会自动更新这些文件。

Actions 配置包含 Node 24.18.0 环境准备和 JavaScript 验证：按锁文件安装、重建及检查语法，确认 package.json 和 package-lock.json 无差异，并将生成的 site.min.js 与当前提交逐字节比较；全部通过后继续 Jekyll 构建。任何一步失败均阻止后续构建和部署，不自动提交生成文件。

该工作流已随提交 42544cd 推送到 main，并于 2026-10-29 通过 [Actions 40798127957](https://github.com/chenhui-lv/chenhui-lv.github.io/actions/runs/40798127957)：Ubuntu 24.04.5 / Node 24.18.0 / npm 11.16.0 / esbuild 0.25.0，锁文件安装、JavaScript 重建、语法检查及压缩产物逐字节比对均成功，package.json 和 package-lock.json 无差异，随后 Jekyll 构建成功。Pages 产物上传和部署均跳过，未发布网站。详细记录见 [SITE_MAINTENANCE.md](SITE_MAINTENANCE.md)。

## 维护与发布
先阅读 [AGENTS.md](AGENTS.md) 和 [SITE_MAINTENANCE.md](SITE_MAINTENANCE.md)。

本地配置的远程仓库为 https://github.com/chenhui-lv/chenhui-lv.github.io ，网址配置为 https://chenhui-lv.github.io 。这些配置不代表已核实线上状态。

源码及发布分支统一为 main。工作流在推送 main 时只构建验证；仅手动触发（workflow_dispatch）且选择 main 时才上传 Pages 产物并部署。手动选择其他分支只构建，不部署。发布仍须获得用户授权。

上述工作流已随提交 8d008be 推送并完成手动部署，记录见维护文档。2026-10-29 已只读核实 Pages 后台：Source 为 GitHub Actions，自定义域名为空，默认网址强制 HTTPS；github-pages 环境仅允许 main 分支、不允许标签部署。

默认网址为 https://chenhui-lv.github.io/ 。本地示例 CNAME 已按用户授权删除，当前无需 CNAME；未来启用自定义域名须另行确认，不能恢复模板示例值。本次未修改后台设置。

Rakefile 的 publish 任务已停用：调用时直接报错退出，不执行构建或推送；generate 本地构建任务保留。不要重新运行 setup.sh 初始化已有个人配置。

当前目录混有模板示例，不得视为个人经历或成果。已知问题和待办见维护文档。

## 来源与许可
本项目沿用 [academic-website-template](https://github.com/sbryngelson/academic-website-template)。原模板致谢 Allen Lab，并说明部分内容采用于其网站。保留现有 [MIT 许可证](LICENSE) 及必要署名；第三方库和资料遵守各自许可。

UPGRADING.md 为上游升级参考，不代表本项目已执行相应迁移。
