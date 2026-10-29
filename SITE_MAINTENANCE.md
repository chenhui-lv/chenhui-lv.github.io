# 网站维护流程

## 更新前
1. 明确本次内容、信息来源及公开范围。
2. 检查 Git 状态，记录当前提交，保护已有未提交修改。
3. 确认目标文件及验收要求。

## 内容更新
- 新增论文默认加入 Publications；只有用户指定为精选论文时才加入首页 Selected papers。
- 新合作者同步加入 Coauthors；合作次数超过 2 次时按现有格式更新 Frequent coauthors。按不同论文去重统计，核实姓名拼写变体，保留已有 2 次条目。
- 简介：检查 _pages/home.md、_config.yml 和 _data/pi.yml。
- 论文：更新 assets/ref.bib，检查唯一引用键、作者顺序、标题、年份、状态和 DOI/arXiv。
- 精选论文：同步核对首页手工列表及相关合作者统计。
- 报告、教学及活动：先核实内容，再决定是否启用现有页面。
- PDF、照片和简历：确认可公开、文件名及所有引用位置。
- 修改源码，不直接修补 _site。

## 本地验证
1. 在依赖已准备好的环境执行 bundle exec jekyll build，检查错误。
2. 修改 JavaScript 时先执行 npm ci --include=dev --include=optional，再执行 npm run build，同步生成压缩文件；审查生成文件与源码差异，普通内容更新无需运行 npm。
3. 使用 bundle exec jekyll serve 预览，检查导航、搜索、论文按钮、公式及深色模式。
4. 检查桌面及手机宽度下的排版，避免横向溢出和按钮不可操作。
5. 核对内部链接、本地资源和本次涉及的外部链接；区分网络限制和实际失效。
6. 检查 _site 中是否混入私人资料、备份或维护文件。
7. 审查 Git 差异，记录实际执行的检查；未执行的项目明确注明。

构建和预览会写入生成目录，只读审查阶段不执行。依赖不齐时先报告，不自动进行无关升级。

## 发布
先交付可审查的修改及验证结果。获得发布授权后：
1. 核对目标仓库、正式分支、工作流、CNAME 和 Pages 设置。
2. 检查发布内容及待提交文件，不盲目暂存所有文件。
3. 记录上一成功发布提交和本次发布提交，按已确认流程发布。
4. 检查 Actions 结果及线上首页、论文页、内部链接和下载链接。

本地工作流以 main 为源码及发布分支：push main 只构建验证，不上传 Pages 产物、不部署。仅手动触发 workflow_dispatch 且选择 main 时，构建成功后才上传产物并部署；手动选择其他分支只构建。部署写权限仅授予 deploy 任务。

本地工作流新增 JavaScript 验证，执行顺序为 Checkout → Setup Node → Verify JavaScript → Setup Ruby → Build site。JavaScript 验证按 npm 锁文件安装并重建，核对清单、锁文件和已提交压缩产物；失败即停止，不允许继续上传或部署。该新增检查仅提交到本地 Git，尚未推送，Ubuntu 实际执行仍待验证。

获得发布授权且本地配置已推送后，在 Actions 中选择 Build and Deploy → Run workflow → main。该手动操作会发布，未获授权不得执行。Rakefile 的 publish 任务已停用，调用时直接报错退出，没有 generate 前置任务。

工作流已随提交 8d008be 推送到 main，并于 2026-10-29 完成 push 构建验证及手动部署，详情见下方成功发布记录。2026-10-29 已只读核实 Pages 后台：Source 为 GitHub Actions，自定义域名为空，默认网址 https://chenhui-lv.github.io/ 强制 HTTPS；github-pages 环境仅允许 main 分支，允许标签数量为 0。

本地示例 CNAME 已按用户授权删除，当前默认域名无需该文件。重新构建后检查整个生成目录不含 CNAME；不要直接修补 _site。今后改用自定义域名须另行授权并核实后台和 DNS。

## 回滚
记录上一成功发布提交。优先通过新的撤销提交恢复，再经同样流程验证和发布。回滚也需要发布授权；不默认使用强制推送、硬重置或删除原始文件。域名、DNS 或服务器设置变更单独确认。

## 文件与依赖策略
- 已排除的模板截图、团队插图和研究插图使用精确文件路径保留在源码中；未来新增原创图片不受这些规则影响。启用团队或研究模板页前，应替换示例图片并核对引用。
- _site、.jekyll-cache、.sass-cache、node_modules 保持为生成或本地依赖目录。
- site.min.js 由 site.js 生成，修改时同步维护。
- 私人资料存放在发布根目录之外；.gitignore 不能阻止 Jekyll 复制文件。
- 维护文档和脚本在 _config.yml 的 exclude 中排除。
- Gemfile.lock 纳入版本管理，包含 x64-mingw-ucrt 和 x86_64-linux 两个平台及依赖 SHA256 校验值；Bundler 版本由 BUNDLED WITH 固定为 4.0.15。不得为解决安装失败直接删除锁文件或运行全量 bundle update。
- 当前锁文件已通过 Windows 冻结安装和生产构建，以及 Ubuntu 24.04.5 / Ruby 3.2.11 / Bundler 4.0.15 的实际安装和生产构建；Ubuntu 验证记录见 [Actions 40791952977](https://github.com/chenhui-lv/chenhui-lv.github.io/actions/runs/40791952977) 及文末核对结果。
- 首次安装执行 gem install bundler -v 4.0.15，再执行 bundle _4.0.15_ install；验证时临时设置 BUNDLE_FROZEN=true，运行 bundle check 和 JEKYLL_ENV=production 的 bundle exec jekyll build，结束后退出该终端或清除临时环境变量。
- 依赖变更须单独审查 Gemfile.lock 差异；确认两个平台保留、Bundler 与 Ruby/RubyGems 兼容，再分别验证 Windows 和 Ubuntu。只有用户授权推送后才能检查新的 Actions 构建，部署另行授权。
- 现有 ruby/setup-ruby@v1 会读取锁文件的 BUNDLED WITH，并在 bundler-cache: true 且存在锁文件时采用 deployment 模式；无需在工作流重复指定 Bundler 版本。本次未修改 Ruby 配置。
- package-lock.json 纳入版本管理，与 package.json 一起审查；esbuild 固定为 0.25.0，Bootstrap 保持 5.3.3。日常使用 npm ci --include=dev --include=optional，只有有意调整依赖时才重新生成锁文件，不运行无关的 npm update。
- JavaScript 本地验证基线为 Windows x64 / Node 24.18.0 / npm 11.16.0；两个平台共用 npm 锁文件，各自安装 esbuild 平台原生包，不复制 node_modules。npm 锁文件和 node_modules 继续由 _config.yml 排除发布。
- 本地 Actions 配置新增 Node 24.18.0（自带 npm 11.16.0），关闭额外 npm 缓存；执行 npm ci --include=dev --include=optional、npm run build 及生成脚本语法检查，再通过 git diff 核对 package.json/package-lock.json、通过 git show 与 cmp 逐字节核对 site.min.js。远端当前仍运行旧工作流，新增检查须经后续授权推送后才生效。
- 不在 Actions 中删除或重生成锁文件，不自动提交产物，不忽略安装或比对错误；Ubuntu npm 实际安装、重建及一致性验证通过前，保留相应未完成待办。
- 页面使用仓库内的 Bootstrap JS/Sass，npm run build 只生成 site.min.js；升级 npm 中的 Bootstrap 不会自动同步网页使用的文件，相关升级须单独审查。
- _sass/bootstrap_bak.scss 疑似旧备份，未确认前保留。

## 初始化待办（2026-10-28 本地审查）
- [ ] 确认准确身份称谓和教育经历：配置为 Ph.D.，首页为在读博士，教育数据为 Ph.D. Candidate（按用户要求暂缓，未修改）。
- [x] 核实正式发布分支为 main，并统一本地工作流；远端默认分支及最近成功手动发布均为 main。
- [x] 直接核实 Pages 后台 Source、自定义域名、HTTPS 和环境分支限制（2026-10-29）。
- [x] 确认当前不使用自定义域名，按用户授权删除本地示例 CNAME（2026-10-29）。
- [x] 停用 Rakefile 的旧 publish 入口，保留 generate 本地构建。
- [x] 为 assets/ref.bib 中两条 LK2026 分配唯一键，检查引用和按钮。
- [x] 将 allnews 示例新闻页排除发布，并停止向 RSS 收录其新闻（原文件保留）。
- [x] 排除指定的 3 个示例页面、3 份 PDF 和 17 张模板图片，保留 23 份原文件；线上地址全部返回 404。
- [ ] 后续启用其他模板功能时，再确认奖项、资助等原始示例数据的保留或替换范围。
- [x] 核对两篇新增论文的标题与作者，按用户确认更新 Combinatorica 待刊状态；14 条引用键唯一、合作者统计一致，线上内容已验收。
- [ ] 按后续更新范围持续核验既有论文的完整元数据；DOI 10.1080/03081087.2019.1628911 的访问检查返回 403，尚不能认定失效。
- [x] 验证 Windows 本地构建及 Ubuntu/Ruby 3.2 云端构建。
- [x] 将 Windows/Ubuntu Gemfile.lock 纳入版本管理，按最近成功 Ubuntu 构建锁定依赖，并通过 Windows 冻结安装和生产构建（2026-10-29）。
- [x] 在 Ubuntu/Ruby 3.2 上实际验证已提交的锁文件及 Bundler 4.0.15；提交 a74213e 的 [Actions 40791952977](https://github.com/chenhui-lv/chenhui-lv.github.io/actions/runs/40791952977) 安装与生产构建成功，日志锁文件与提交一致，Upload artifact 和 deploy 均 skipped（2026-10-29）。
- [x] 确定 package-lock.json 纳入版本管理，固定 esbuild 0.25.0，保持 Bootstrap 5.3.3，并完成本地构建验证（2026-10-29）。
- [ ] 在 Ubuntu 上实际验证 npm 锁文件安装、JavaScript 重建和压缩产物一致性；本地 Actions 已配置相关检查，尚未推送或在 Ubuntu 执行，不能据既有 Jekyll 构建成功认定该项完成。
- [x] 修复页脚闭合标签，并完成电脑宽度 1440、手机宽度 390 的浏览器检查。
- [ ] 确认核心页面 sitemap: false 设置是否符合搜索引擎收录意图；手机实机测试尚未执行。
- [x] 推送 main 后确认仅构建、不上传产物、不部署；获单独授权后手动部署并完成线上验收。

早期条目记录当时的本地审查状态；当前已完成的线上验证以“成功发布与线上验收”记录为准。未完成事项保留在上述待办中。

## 首页精选范围调整（2026-10-28）
- 根据用户要求，从首页 Selected papers 移除 2610.37010 和 2609.12318；两篇论文继续保留在 Publications。
- 首页其余精选论文、Combinatorica 待刊状态及合作者名单保留。

## 2026-10-28 内容更新与本地验证
- 按 arXiv 原始页面核对并新增 2610.37010（Hong、Hu、Lv）及 2609.12318（Lv 独著），同步首页和完整论文列表。
- 合作者页新增 Zhen-Mu Hong、Fu-Tao Hu；不添加未经核实的个人主页，不改变其余合作次数。
- 根据用户提供的信息，将 2506.04964 标为 Combinatorica 待刊，并归入 Publications；arXiv 页面本次未提供该接收状态，不补写卷期、页码或 DOI。
- 重复 LK2026 改为 LK2026Geometric 和 LK2026Bounding；共 14 条文献，键均唯一。
- 本地 Jekyll 构建成功（退出码 0）；生成论文页的相关 BIB 目标均唯一，页面无重复 HTML id。
- 首页新论文和状态、合作者名单、搜索 JSON、三页涉及的 11 个不同本地页面或资源目标检查通过。
- 维护文件的发布排除规则生效；git diff --check 通过。
- 未执行浏览器视觉检查、手机实机测试、全站外链检查或 Linux 构建；未安装依赖、提交、推送或部署。

## 2026-10-28 本地浏览器预览
- 使用仅绑定 127.0.0.1:4000 的预览服务，检查首页、论文页和合作者页。
- 在浏览器中检查电脑宽度 1440 和手机宽度 390；三页均未观察到横向溢出，标题及合作者名单正常换行。
- 两篇新增论文和 Combinatorica 待刊状态在页面中显示正确。
- 新论文 BIB 展开、收起和引用复制检查通过；修复后的两个 LK2026 引用按钮可分别展开自己的内容。
- 新论文的两个 arXiv 按钮实际打开对应 arXiv 页面。
- 手机导航菜单展开、站内搜索 Resolving 返回首页和论文页、深色模式切换检查通过；检查时读取的浏览器错误/警告日志为空。
- 发现待修问题：论文筛选只隐藏内部条目，外层列表仍留下空白编号和占位；输入 Combinatorica 可复现。
- 发现本地预览限制：导航使用 site.url 的绝对地址，会跳转到线上网站；建议站内导航采用 relative_url，canonical 等元数据保留正式网址。
- 本轮仅更新检查记录，未修改页面或交互源码，未推送或部署；手机宽度模拟不等同于手机实机测试。

## 2026-10-29 筛选与本地导航修复
- 筛选隐藏 Scholar 的完整列表行，移除空编号与占位；空分类标题及列表同时收起，无匹配时提示 No matching publications。
- 使用 esbuild 0.25.0 重新生成 site.min.js，Jekyll 构建成功；未修改依赖声明。
- 导航、404 返回链接、CV 与本地论文下载、博客站内链接采用 relative_url；正式网址元数据保留。
- 浏览器在 1440 和 390 宽度下测试通过：Combinatorica 筛选仅显示 1 条，空分类隐藏；无结果提示可见；清空恢复 14 条和两个分类；BIB 可展开，无横向溢出。
- 两种宽度均实际点击 Home、Coauthors、Publications，地址保持在 127.0.0.1:4000；手机菜单正常。
- 首页仍不包含两篇新增论文；浏览器检查时无错误或警告日志。
- 用户长期偏好已写入 AGENTS.md：新增论文默认只进 Publications，新合作者同步更新，合作超过 2 次时按现有格式更新 Frequent coauthors；已有 2 次条目保留。
- 未提交、推送或部署；浏览器宽度模拟不等同于手机实机测试。

## 2026-10-29 本地发布配置调整
- push 监听统一为 main；推送仅构建验证。
- Pages 产物上传和部署均要求 workflow_dispatch 且 github.ref == refs/heads/main，deploy 依赖 build 成功。
- 默认权限仅 contents: read，pages: write 与 id-token: write 仅授予 deploy。
- Rakefile 旧 publish 逻辑已移除，任务直接失败；generate 保留。
- README、AGENTS 和本维护流程已同步；CNAME 与原值完全一致，未改域名或后台设置。
- 本地检查通过：工作流 YAML 解析、触发分支与部署权限；条件矩阵为 push main 不部署、手动 main 可部署、手动其他分支不部署。
- Ruby 语法检查通过；实际调用停用后的 publish 任务退出状态为 1，未调用 generate，旧远程地址与强制推送命令均已移除。
- git diff --check 通过。未运行云端工作流、推送或部署；这些改动尚未在 GitHub 生效。

## 2026-10-29 示例 CNAME 清理
- 在只读核实后台使用默认域名、无自定义域名后，按用户授权删除根目录示例 CNAME，同步更新 AGENTS、README 和本维护文档。
- Jekyll 完整构建成功，退出码 0；整个 _site 中不再包含 CNAME 文件，也未检出示例域名 your-website-name.com。
- 首页、Publications 和 Coauthors 生成页面均存在，git diff --check 通过。
- 历史记录中的“保留 CNAME”描述仅反映当时状态；当前策略以本节及发布流程为准。
- 本次仅修改本地文件，未推送、部署或修改后台、DNS 设置。

## 2026-10-29 示例内容排除与页脚修复
- _config.yml 新增六个精确排除路径：_pages/allnews.md、两篇 _posts 模板文章及 papers 中三份模板 PDF；原文件与 _data/news.yml 均保留且内容未改。
- feed.xml 仅在新闻页实际生成时收录 site.data.news，避免隐藏新闻页后仍从 RSS 公开示例新闻或产生失效链接。当前无发布文章和新闻，RSS 保持有效空频道。
- 移除 _includes/footer.html 中多余的闭合 div。
- 完整构建成功；六个原文件存在，对应生成文件全部消失，搜索索引、站点地图和 RSS 不含被排除的 URL。
- 6 个生成 HTML 页面的内部目标和页脚标签平衡检查通过；14 条论文、首页精选范围、Combinatorica 状态及现有身份表述保留。
- 身份称谓按用户要求暂不修改；模板图片等其余资料保留范围仍可继续审查。
- 未提交、推送或部署。

## 2026-10-29 模板图片排除
- 按用户授权，将 6 张 screenshots PNG、5 张 team SVG 和 6 张 research SVG 加入 _config.yml 的精确排除清单；17 张原图完整保留，未改文件内容。
- 完整构建成功；_site 不含这 17 张图片，生成的 HTML、CSS、JS、JSON、XML 和 SVG 未检出对它们的引用。
- 6 个 HTML 页面与 12 个本地目标检查通过，头像、社交分享图片、样式和脚本仍存在，未发现缺失页面或资源链接。
- 14 条论文、新论文仅列于 Publications、待刊状态和新增合作者保留；身份称谓按用户要求不改。
- 未提交、推送或部署。

## 2026-10-29 成功发布与线上验收

本次按用户授权先推送验证，再单独手动部署；日期按 Australia/Sydney 记录。

| 项目 | 记录 |
| --- | --- |
| 发布分支 | main |
| 发布提交 | [8d008bef8f6714d02f426fcddb7b8c7350b1d983](https://github.com/chenhui-lv/chenhui-lv.github.io/commit/8d008bef8f6714d02f426fcddb7b8c7350b1d983) |
| 提交说明 | 更新学术内容并完善主页维护与受控发布流程 |
| push 验证 | [Actions 40782444784](https://github.com/chenhui-lv/chenhui-lv.github.io/actions/runs/40782444784)：event=push，build 成功，Upload artifact 与 deploy 均 skipped |
| 手动部署 | [Actions 40782916572](https://github.com/chenhui-lv/chenhui-lv.github.io/actions/runs/40782916572)：event=workflow_dispatch，main，build、Upload artifact 和 deploy 均成功 |
| 部署环境 | github-pages |
| 正式网址 | https://chenhui-lv.github.io/ |

线上验收结果：

- [首页](https://chenhui-lv.github.io/) 返回 200，保持原有精选范围，两篇新增论文未进入 Selected papers；To appear in Combinatorica 显示正确。
- [论文页](https://chenhui-lv.github.io/publications/) 返回 200，共 14 条论文，两篇新增论文 2610.37010、2609.12318 已显示；Combinatorica (to appear) 显示正确，BIB 展开及筛选检查通过。
- [合作者页](https://chenhui-lv.github.io/coauthors/) 返回 200，Zhen-Mu Hong、Fu-Tao Hu 已显示。
- 3 个示例页面、3 份模板 PDF 和 17 张模板图片共 23 个线上地址全部返回 404；原文件仍保留在源码中。
- 搜索索引、站点地图和 RSS 均返回 200，无被排除内容的残留引用；RSS 为有效空频道。
- 三页涉及的 11 个站内页面及资源地址均可访问。
- 身份称谓保持不变。本次发布未改自定义域名、DNS 或 Pages 后台设置。

以上发布与验收已完成。本节为发布后补充的维护记录；文档记录更新与网站部署分开进行，未再次触发部署。

## 2026-10-29 Windows / Ubuntu 锁文件准备

本节保留准备及本地验证阶段的历史记录，其中“Ubuntu 实际构建待验证”已由后续的“Ubuntu 锁文件实际构建验证”记录完成确认。

- 基线为最近成功的 [push 构建 40783801700](https://github.com/chenhui-lv/chenhui-lv.github.io/actions/runs/40783801700)，提交 c8aa507；Ubuntu 24.04、Ruby 3.2.11、Bundler 2.4.19。该次构建成功且部署跳过。
- 从该任务日志中的完整锁文件恢复 55 个 Linux 依赖条目，保留版本及依赖约束，补充 Windows x64-mingw-ucrt 平台。新增的三个原生包条目为 ffi 1.17.4、google-protobuf 4.36.2、sass-embedded 1.77.8 的 Windows 版本；Linux 条目未改变。
- 锁文件记录 Bundler 4.0.15，与本机现有版本一致；[官方兼容性要求](https://guides.rubygems.org/bundler-compatibility/)为 Ruby >= 3.2.0、RubyGems >= 3.4.1。现有 Actions Ruby 3.2 环境满足版本系列要求，但切换 Bundler 后仍须实际验证。
- 通过 bundle lock --add-checksums 补齐依赖 SHA256；移除 .gitignore 中的 Gemfile.lock 条目，按用户授权随本次本地提交纳入版本管理。
- [ruby/setup-ruby 官方说明](https://github.com/ruby/setup-ruby#bundler)确认会采用 BUNDLED WITH 指定版本；无需改动当前工作流或部署条件。

相比原先仅用于 Windows 的本地锁文件，以下 14 个版本对齐到成功的 Ubuntu 基线，并非在该基线上另行升级：

| 依赖 | 原 Windows 版本 | Ubuntu 基线 / 当前版本 |
| --- | --- | --- |
| bigdecimal | 4.1.2 | 4.1.3 |
| citeproc | 1.1.0 | 2.0.0 |
| citeproc-ruby | 2.1.8 | 2.3.0 |
| concurrent-ruby | 1.3.7 | 1.3.8 |
| csl | 2.2.1 | 2.5.0 |
| csl-styles | 2.0.2 | 2.1.0 |
| csv | 3.3.5 | 3.3.7 |
| date | 3.5.1 | 3.6.0 |
| google-protobuf | 4.35.1 | 4.36.2 |
| json | 2.20.0 | 3.1.0 |
| listen | 3.10.0 | 3.10.1 |
| rack | 3.2.6 | 3.2.7 |
| time | 0.4.2 | 0.5.0 |
| uri | 1.1.1 | 1.1.2 |

验证结果及范围：

- Windows Ruby 4.0.5 / RubyGems 4.0.10 / Bundler 4.0.15：BUNDLE_FROZEN=true 的 bundle install、bundle check 及生产模式 jekyll build 全部成功；冻结检查及构建前后 Gemfile.lock 的 SHA256 一致。
- 6 个 HTML 页面生成，14 个论文 BIB 按钮目标完整且无重复 ID；8 个不同的 HTML 站内页面及资源目标均存在，页面内锚点检查通过；JSON、XML 解析通过。
- 两篇新论文仅列于 Publications，Combinatorica 待刊状态及新合作者保留；23 份示例原文件仍存在，发布产物和文本引用中未发现残留，产物不含 CNAME 或 Gemfile.lock。
- Linux 55 个依赖条目与成功构建日志逐项比对一致，另有 3 个 Windows 原生包条目；所有 58 个依赖条目都有校验值（另记录 Bundler 的校验值）。
- 本机未发现可用的 Ubuntu 环境，因此未执行当前锁文件在 Ubuntu/Ruby 3.2 + Bundler 4.0.15 下的实际安装或构建；待获得相应验证条件或后续推送授权后确认。历史成功记录不能替代该项检查。
- 本轮未进行浏览器视觉复测或在线外链复查；页面源码、身份称谓、Gemfile、工作流及 package-lock.json 策略均未修改。README 同时纠正了工作流“尚未推送”的过时说明。
- 准备及验证阶段未提交、推送或部署；随后按用户授权，仅将 Gemfile.lock、.gitignore、README.md 和本维护文档提交到本地 Git，未推送或部署，Ubuntu 实际构建待办保留。旧 Windows 锁文件已备份至 D:/Temp/academic-site-Gemfile.lock-before-cross-platform-20261029；如需回退，可通过新的撤销提交恢复相关改动，并按需恢复该本地备份，旧版本 gem 未删除，无需修改线上网站。

## 2026-10-29 Ubuntu 锁文件实际构建验证

按用户授权，将本地 main 的 a74213e 推送到 GitHub，仅触发 push 构建验证；未手动触发部署。日期按 Australia/Sydney 记录。

| 项目 | 核对结果 |
| --- | --- |
| 分支及提交 | main / [a74213e6daab33936ae49f1cad961e751d10c66f](https://github.com/chenhui-lv/chenhui-lv.github.io/commit/a74213e6daab33936ae49f1cad961e751d10c66f) |
| Actions | [40791952977](https://github.com/chenhui-lv/chenhui-lv.github.io/actions/runs/40791952977)，event=push，status=completed，conclusion=success |
| 构建任务 | [build 123179066305](https://github.com/chenhui-lv/chenhui-lv.github.io/actions/runs/40791952977/job/123179066305)，success |
| 实际环境 | Ubuntu 24.04.5 / Ruby 3.2.11（x86_64-linux） |
| Bundler | 4.0.15，从已提交的 Gemfile.lock 的 BUNDLED WITH 读取并安装 |
| 安装方式 | deployment=true，bundle install --jobs 4；11 个直接依赖、55 个 gem 安装完成 |
| 生产构建 | JEKYLL_ENV=production 的 Jekyll 构建成功 |
| 发布保护 | Upload artifact=skipped；deploy=skipped；该次运行的产物列表为空 |

锁文件核对依据：

- 日志明确显示 `Using Bundler 4.0.15 from Gemfile.lock BUNDLED WITH 4.0.15`，并为后续配置和锁文件命令设置 BUNDLER_VERSION=4.0.15。
- Checkout 的完整提交号为 a74213e6daab33936ae49f1cad961e751d10c66f，与本次推送一致。
- 将任务日志中打印的完整 Gemfile.lock 去除日志时间戳后，与该提交中的 207 行锁文件逐行比对，内容一致，包含两个平台、依赖版本、校验值及 Bundler 版本。
- GitHub 提交中的锁文件与本地提交的 Git blob 均为 `95198fea8b73c92d88f6ec624934c8b1ec8fc4f6`；已确认使用本次纳入版本管理的锁文件。

当前锁文件的 Windows 与 Ubuntu 实际构建验证均已完成；此前待验证条目已勾选。该次运行只验证构建，没有上传 Pages 产物或发布网站。

本次仅补充维护记录，身份称谓、页面内容、依赖、工作流和后台设置均未修改；该记录按用户后续授权单独提交到本地 Git，不包含推送或部署。

## 2026-10-29 npm 锁文件与本地构建验证

- 按用户授权，将 package.json 的 esbuild 从 ^0.25.0 固定为 0.25.0；Bootstrap 仍为 5.3.3，未修改仓库内的 Bootstrap JS/Sass、身份称谓、JavaScript 源码或 Actions。
- 使用 Windows x64 / Node 24.18.0 / npm 11.16.0，从官方 registry.npmjs.org 生成 package-lock.json（lockfileVersion=3）；移除 .gitignore 中对该锁文件的忽略，保留 node_modules 忽略规则。按用户后续授权，锁文件随本次本地提交纳入版本管理。
- 锁文件有 29 个条目（含根项目），包含 Windows x64 与 Linux x64 的 esbuild 0.25.0 可选原生包；所有非根条目均有官方 registry 下载地址及 integrity 校验值。Bootstrap 固定为 5.3.3，间接依赖 @popperjs/core 锁定为 2.11.8。
- 按新锁文件执行 npm ci --include=dev --include=optional，安装成功；npm ls --depth=0 确认 bootstrap@5.3.3 与 esbuild@0.25.0，实际 esbuild --version 为 0.25.0。
- npm run build 和生成脚本的 node --check 均通过；生成的 assets/js/site.min.js 与原已提交文件完全一致，SHA256 为 72113A94306204F1627D8E7C271BA12BC08522CBADABED82E82AF907311273CA，无需提交新的压缩文件。
- 安装及 JavaScript 构建前后 package-lock.json 未变化，SHA256 为 37309B350C8683CD1E29DA96F5D39E976B3C970BBB316C5DDF868BE7D6E258EA。
- npm 安装出现非阻塞的 allow-scripts 警告：esbuild@0.25.0 的 postinstall 尚未在 allowScripts 中声明；安装、版本核实和构建均成功。本次未修改安装脚本策略，也未执行 npm approve-scripts。
- Bundler 4.0.15 在 BUNDLE_FROZEN=true 下检查依赖并完成 JEKYLL_ENV=production 的 Jekyll 构建，退出码 0；Gemfile.lock 未变化。
- 生成 6 个 HTML 页面、14 条论文，BIB 目标及 HTML ID 检查通过；8 个不同的 HTML 站内页面及资源目标存在，页面锚点、JSON 和 XML 检查通过。新论文仅列于 Publications，Combinatorica 待刊状态及新合作者保留。
- 23 份示例原文件仍存在，发布产物及其文本引用中未发现这些示例残留；根目录的 package.json、package-lock.json、维护文档及 node_modules 均未发布，整个产物不含 package-lock.json 或 node_modules，CNAME 未出现。仓库原有 assets/javascript/popper 下的第三方 package.json、README.md 与本项目根目录文件区分检查，未改动。
- 本轮只验证 Windows 的 npm 安装及 JavaScript 重建，未验证 Ubuntu npm 构建，未进行浏览器复测或安全审计；现有 site.min.js 字节未改变。既有 Ubuntu Ruby/Jekyll 成功记录不能替代 npm 跨平台验证。
- 后续回退可撤销本轮 package.json、.gitignore、README.md、本维护文档及新增 package-lock.json 的对应改动；无需修改线上网站或恢复 JavaScript 产物。
- 准备及验证阶段仅修改上述 5 个文件及本地忽略的依赖/构建目录；随后按用户授权将这 5 个文件提交到本地 Git，保留 Ubuntu npm 构建待验证记录，未推送或部署，未修改 Actions。

## 2026-10-29 npm 锁文件提交后的 push 构建验证

按用户后续授权，将 main 的 103955a 推送到 GitHub；本次使用原有 Actions 工作流，仅验证 Jekyll 构建，未手动触发部署。

| 项目 | 记录 |
| --- | --- |
| 分支及提交 | main / [103955aafcf3e5448c5b1219d3a20fa67e172fc2](https://github.com/chenhui-lv/chenhui-lv.github.io/commit/103955aafcf3e5448c5b1219d3a20fa67e172fc2) |
| Actions | [40796357284](https://github.com/chenhui-lv/chenhui-lv.github.io/actions/runs/40796357284)，event=push，status=completed，conclusion=success |
| Jekyll 构建任务 | [build 123193661414](https://github.com/chenhui-lv/chenhui-lv.github.io/actions/runs/40796357284/job/123193661414)，success；Setup Ruby 与 Build site 均成功 |
| Pages 产物上传 | Upload artifact=skipped |
| 部署任务 | deploy=skipped |

该次 Actions 未运行 npm 安装或 JavaScript 重建，因此只确认 Ubuntu 上的 Jekyll 构建通过；Ubuntu npm 锁文件安装及 JavaScript 重建仍待验证，对应待办保持未勾选，不能用本次成功替代该项检查。

该次推送未修改 Bootstrap、身份称谓、Actions 或后台设置，没有上传 Pages 产物或发布网站。以上为推送后的补充记录；该记录按用户后续授权单独提交到本地 Git，保留 Ubuntu npm 构建待验证事项，不包含推送或部署。

## 2026-10-29 本地 Actions 增加 Ubuntu npm 验证

- 按用户授权，仅在现有 build 任务的 Checkout 后、Setup Ruby 前新增 Setup Node 和 Verify JavaScript 两个步骤；actions/setup-node@v6 固定 Node 24.18.0，关闭额外 npm 缓存，不新增任务或工作流文件。
- Verify JavaScript 使用 bash，打印 Node/npm/esbuild 版本，按锁文件安装开发及可选依赖，执行现有 npm run build 和 node --check；随后用 git diff --exit-code 检查 package.json/package-lock.json，用 git show HEAD:assets/js/site.min.js 与 cmp 对生成文件逐字节比对。
- 不设置 continue-on-error，不添加自动提交、锁文件重生成或产物上传入口；任一步失败即停止。push 仍只验证，Upload artifact 和 deploy 均保留 workflow_dispatch 且 refs/heads/main 的原有条件，deploy 继续 needs: build。
- 本地检查通过：Ruby Psych 解析工作流 YAML；与修改前配置逐项对比，原有触发器、权限、并发配置、deploy 任务、Ruby/Jekyll/上传步骤均未改变，仅增加上述两步。
- 部署条件静态核对通过：push main 不部署；手动 main 且构建成功可部署；手动其他分支或构建失败不部署。这是本地配置核对，不是 GitHub Actions 实际执行结果。
- 在 Windows 的 Git Bash 中通过新增脚本的语法检查，并完整执行 Verify JavaScript 的原文脚本：Node 24.18.0 / npm 11.16.0 / esbuild 0.25.0，npm ci、重建、语法检查、清单/锁文件差异检查及压缩产物逐字节比较全部通过。package.json、package-lock.json、site.min.js、Gemfile.lock 的字节均未改变。
- 上述本地脚本验证临时关闭 npm audit/fund 输出，未改动项目 npm 配置；仍出现此前记录的非阻塞 esbuild postinstall allow-scripts 警告，未改变脚本策略。
- 随后在 BUNDLE_FROZEN=true / JEKYLL_ENV=production 下完成 Jekyll 构建，退出码 0；生成 6 个 HTML 页面，根目录维护文件、依赖文件、node_modules 和 CNAME 的排除检查通过，Gemfile.lock 未变化。
- README 与本维护文档同步区分“本地已配置”和“远端尚未生效”；Ubuntu npm 实际安装、JavaScript 重建及产物一致性仍待验证，相关待办未勾选。Windows Git Bash 的通过结果不代替 Ubuntu 实测，既有 Jekyll 云端成功记录也不代替该项检查。
- Bootstrap、身份称谓、package.json、两个依赖锁文件及页面/JavaScript 源码均未修改。本轮未进行浏览器复测；site.min.js 与原提交一致。
- 本轮仅修改 .github/workflows/deploy.yml、README.md 和本维护文档，按用户后续授权提交到本地 Git，未推送或部署，未触发远端 Actions；Ubuntu npm 实际验证仍待完成。若需回退，可通过新的撤销提交恢复本轮这 3 个文件的对应改动，线上网站不受影响。
