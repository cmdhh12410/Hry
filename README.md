# Firefly 本地管理器

`firefly-config-manager.html` 是一个用于管理 Firefly / Fuwari / Astro 博客的本地图形化工具。它通过浏览器的本地目录读写能力修改仓库里的配置文件和文章文件，不会上传任何内容。
注意!!!由于本人的博客已在Firefly的基础上加入其他功能,博客管理器和原版有出入因此不再更新该管理器.后续管理器可能和新版Firefly的新功能有兼容性问题,只保证原版功能表单填写正常.
## 打开方式

1. 使用最新版 Chrome 或 Edge 打开 `firefly-config-manager.html`。
2. 点击左侧的“选择网站仓库”。
3. 选择 Firefly 网站仓库目录，例如：

```text
E:\Blog\Firefly
```

4. 在界面中修改配置或文章。
5. 点击右上角“保存全部修改”写入磁盘。

Firefox 和 Safari 通常不支持这个 HTML 文件所需的目录写入 API，因此建议使用 Chrome 或 Edge。

## 主要功能

### 配置管理

工具会读取 `src/config` 下的主要配置文件，并把常用设置做成表单：

左侧导航栏可以折叠，适合在小屏幕或编辑长配置时腾出更多横向空间。

- 站点与 SEO：标题、副标题、站点 URL、语言、描述、关键词、favicon、建站日期、时区、OpenGraph、分享海报、最后修改时间。
- 主题与布局：主题色、默认亮暗模式、页面宽度、卡片边框、分类栏、文章列表布局、分页、图片优化。
- 首页与壁纸：banner / overlay / none 模式、桌面和移动端壁纸、首页标题和副标题、打字机、署名、透明导航栏、轮播、水波、遮罩透明度。
- 导航与页面：导航 Logo、导航标题、菜单对齐、粘性导航栏、搜索方式、朋友页、赞助页、留言页、Bangumi、画廊、侧边栏组件。
- 导航栏高级配置：`navBarConfig.ts` 中 `getDynamicNavBarConfig()` 的基础 `links` 数组和后续 `links.push(...)` 动态追加逻辑都可以在“导航与页面”页直接编辑，支持 `LinkPreset.*`、自定义链接、多级菜单、外链和图标。上游新增的文章子菜单、分类页、标签页预设也会被保留和读取。
- 个人资料：头像、名称、简介、社交链接、许可证、页脚配置、页脚 HTML。
- 内容组件：公告、随机封面、友链、画廊、赞助、广告。赞助者列表支持上游新增的 `avatar` 头像字段，并会像其他图片路径一样显示紧凑预览。
- 评论系统：none、Twikoo、Waline、Giscus、Disqus、Artalk 及其详细参数。
- 音乐播放器：Meting、本地播放列表、平台、歌单 ID、音量、循环模式、歌词、备用 API。
- 外观增强：字体、樱花、Spine、新版 `live2dWidgetConfig` / l2d-widget、代码块、PlantUML。
- 统计分析：Google Analytics、Microsoft Clarity、Umami、Replays、51la。上游把旧拼写 `relpays` 修正为 `replays`，管理器已改为写入新字段，并支持 `replaysScriptUrl` 回放脚本地址。

新版 Firefly 的部分配置支持多种写法，例如壁纸 `src` 可以是字符串、数组或 `{ desktop, mobile }` 对象，Banner 署名、水波、Overlay 可切换项也可以是布尔值或桌面/移动端对象。管理器会优先提供直观字段，同时保留“完整配置”编辑框，方便处理这些联合类型结构。

新版 `siteConfig.ts` 顶部使用 `SITE_LANG` 常量定义站点语言，表单里的“站点语言”会直接读写这个常量，不再把底部 `lang: SITE_LANG` 改成固定字符串。

新版 `navBarConfig.ts` 的导航链接由 `getDynamicNavBarConfig()` 动态生成。管理器会在“导航与页面”中提供基础 `links` 数组和动态追加逻辑两个源码片段编辑区，适合调整文章子菜单、分类/标签入口、自定义菜单和页面开关联动；仍然可以到“源码编辑”页打开完整 `navBarConfig.ts` 做更细的 TypeScript 修改。

如果某些设置过于复杂，或者配置文件中有动态函数，可以到“源码编辑”页直接修改原始 TypeScript / HTML。

新版进一步把赞助方式、赞助者列表、广告、看板娘、页脚开关等配置做成了结构化表单，减少直接写 JSON 的情况。Bangumi 新增的 `mode`、`apiUrl`、`subjectBaseUrl`，Umami 新增的 `replaysScriptUrl` 和 `replays.*`，以及日历组件的 `responsive.showHeatmap` 都已纳入表单。

如果某个配置字段在当前文件中没有显式写出，但类型定义允许它作为可选字段存在，管理器会先显示一个默认空值；修改后会自动把该属性插入到对应配置对象中。这样上游新增的可选字段不会再因为“未找到”而只能去源码页手改。

上游近期把樱花特效配置从 `sakuraConfig.ts` 重命名并集中到 `effectsConfig.ts`，壁纸配置也把首页文字、导航透明、水波和渐变等通用项移动到了 `backgroundWallpaper.common.*`，并新增 `fullscreen` 壁纸模式。当前管理器已按这些新字段读取和写入。

上游新版把旧的 `live2dModelConfig` 替换为 `live2dWidgetConfig`，底层使用 `l2d-widget`。管理器已改为读取新版字段，支持模型配置、位置、尺寸、UI 主题色、入场动画、菜单项、额外菜单项、提示气泡、打字嘴型和移动端隐藏等参数。若 `model` 或 `size` 使用联合类型（例如数组模型或数字尺寸），可以用“模型配置 / 尺寸”的完整 JSON 区域编辑。

侧边栏组件数组支持编辑组件类型、启用状态、位置、文章页/非文章页显示、广告配置 ID、响应式隐藏设备、折叠阈值、日历热力图显示开关和自定义属性。复杂对象仍可通过对应完整配置或源码页兜底编辑。

### 文章元数据管理

工具会读取：

```text
src/content/posts
```

支持管理 `.md` 和 `.mdx` 文件的 Frontmatter：

- 文章列表搜索。
- 在文章详情中提供“打开源文件”入口，方便直接跳到 `.md` / `.mdx` 文件。
- 新建文章。
- 编辑标题、发布时间、更新时间、分类、标签、封面、描述、slug、语言、作者、来源链接。
- 切换草稿、置顶、评论。
- 编辑许可证字段。
- 编辑文章密码和密码提示。

正文内容不会在这个工具中编辑，保存文章时会保留原正文，只重写 Frontmatter。文章保存时同样会进入备份会话。

### 资源预览

- 图片、头像、封面、广告图、二维码、Logo 等图片类路径会尽量显示紧凑缩略图。
- 外链图片会直接预览。
- 本地图片路径会尝试从仓库根目录、`public`、`src` 中读取，并用浏览器 `blob:` URL 显示。
- 壁纸配置的桌面和移动端路径支持图库式预览，缩略图按比例缩小，不会拉长界面。
- 修改图片类路径后，界面会自动刷新预览。
- 在友链头像、画廊封面、赞助二维码、本地音乐封面、广告图片、文章封面等列表项中输入图片路径时，对应缩略图会原地刷新，不会重建整个页面或打断光标位置。
- 壁纸路径输入后会即时刷新对应预览区，不会重建输入框，也不会把光标跳回旧位置。
- 修改主题色 Hue 后，管理器界面的主色和主题色预览会实时同步变化。
- 主题色 Hue 会保存到 `src/config/siteConfig.ts` 的 `siteConfig.themeColor.hue`。预览按 Firefly 当前 `src/styles/variables.styl` 中的 OKLCH 变量计算：亮色 `oklch(0.70 0.14 hue)`，暗色 `oklch(0.75 0.14 hue)`，不是旧的 HSL 算法。
- Firefly 网站前端会优先读取访问者浏览器里的 `localStorage.hue`，所以如果你曾在网站显示设置里手动调过主题色，构建后仍可能看到旧颜色；需要在网站显示设置中重置主题色，或清除该站点的 `localStorage.hue`，才会显示新的配置默认色。

### 备份恢复

每次点击“保存全部修改”前，工具会在仓库根目录创建备份：

```text
.firefly-config-manager-backups
```

备份包含一个 JSON manifest 和对应文件的 `.bak` 内容。manifest 会记录本次保存前备份的原因、涉及文件，以及每个文件对应的改动说明；为了避免泄露文章密码、API token 等内容，说明只记录字段名和来源，不记录具体新旧值。界面中的“备份恢复”页可以按保存会话整体恢复，并显示这些改动说明。

保存前可以在右上角填写“备份名称”。名称会写入 manifest，也会作为安全化后的片段加入备份文件名，方便在 `.firefly-config-manager-backups` 文件夹中直接识别。备份页支持重命名和删除备份；删除会同时移除 manifest 和对应的 `.bak` 文件。

备份页支持全选、取消选择和批量删除。批量删除会并行移除选中会话的 manifest 和 `.bak` 文件，适合清理多次试错产生的备份。

为减少保存卡顿，保存后不会重新全量扫描配置和文章；只会清空当前脏状态、追加本次备份会话并刷新当前界面。多个待保存文件和备份文件会并行写入。

恢复备份前，工具还会再创建一次“恢复前备份”，用于避免误恢复后无法找回当前状态。

说明：旧版本工具曾把备份放在 `src/config/.firefly-config-manager-backups`，新版不会删除旧备份，但“备份恢复”页只管理新版根目录备份。

## 安全建议

- 修改前建议先查看 Git 工作区状态。
- 保存后建议运行博客项目自己的检查命令，例如 `pnpm build`。
- “源码编辑”和 JSON 编辑区域不会自动理解所有业务规则，只负责写回文件，请保持 TypeScript / JSON / Markdown 语法有效。
- 如果需要回退，优先使用 Git；没有 Git 提交时，可使用“备份恢复”。

## 参考

- Firefly 文档：https://docs-firefly.cuteleaf.cn/zh/guide/getting-started.html
- Astro 中文文档：https://docs.astro.build/zh-cn/getting-started/
- Firefly GitHub：https://github.com/CuteLeaf/Firefly

## 本次新增：画廊图片管理与保存性能优化

- 在“内容 / 画廊”区域新增“画廊图片管理”，按 `galleryConfig.albums[].id` 自动对应到 `public/gallery/{相册 ID}/`。
- 每个相册会显示本地图片缩略图，支持 `jpg`、`jpeg`、`png`、`webp`、`avif`、`gif`。
- 上游新版画廊还会读取 `public/gallery/{相册 ID}/urls.txt` 中的外链图片。管理器现在会在每个相册下显示“外链图片 urls.txt”，支持逐行编辑 URL、即时预览并自动写回该文件。
- 支持直接上传多张图片到相册目录；如果目录不存在，上传时会自动创建。
- 支持勾选多张图片后删除。删除会立即操作 `public/gallery/{相册 ID}/` 里的文件，不走配置备份；需要删除前自行确认。
- 支持勾选一张图片后“设为封面”。这个操作会把相册的 `cover` 写成 `/gallery/{相册 ID}/{文件名}`，然后需要点击“保存全部修改”写入 `src/config/galleryConfig.ts`。
- Firefly 网站本身读取画廊图片的逻辑是：先看 `album.cover`，再找目录里的 `cover.*`，最后使用第一张图片。因此如果不手动设封面，也可以把文件命名成 `cover.avif`、`cover.webp` 等。
- 保存性能已优化：点击保存后不再完整重绘当前设置页，避免大量图片预览或复杂配置控件导致保存后卡顿。仪表盘、备份页、源码页等需要刷新状态的页面仍会做轻量更新。

## 随机封面和友链申请示例

- Firefly 的随机封面不是全站自动补图。必须在文章 Frontmatter 中设置 `image: api`，并且 `coverImageConfig.randomCoverImage.enable` 开启、API 列表不为空，文章卡片和文章详情页才会调用随机封面。
- 文章管理里新增了“当前文章使用 image: api”和“无封面文章使用随机封面”，用于快速给文章写入 `image: api`。
- 友链页截图中的“申请友链 / 本站信息 / 注意事项”来自 `src/content/spec/friends.mdx`，不是 `friendsConfig.ts`。管理器已新增“友链申请示例”编辑区，可修改 `site` 信息和 `notes` 注意事项，并保留原 MDX 布局。
