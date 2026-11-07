# xlzh5 · xlzawa 的个人小站

---


## 写在前面

这个项目是我用来  上手git、github、开源相关内容、简单项目开发  的，算是我第一次vibe coding，于是选择了dsh，嘻嘻。

*这个项目99%由大肥鱼完成*，忘了使用现成的框架了(造轮子ing)，消耗**4.3块**(均在谷时进行)，感觉有点肉疼(躺)。

自用项目，所以大多都是我自己要用的,可以拿去二改。

项目应该遵循 [GPL v3](https://gnu.net.cn/licenses/gpl-3.0.html) ,如果有哪些地方未能遵循到我使用的内容要求遵循的，请及时联系我！~~(话说issue怎么用)~~

---

### 接下来交给大肥鱼



一个**单文件**的个人主页 —— 全部内容（样式、脚本、3D 渲染器、皮肤贴图）都内联在 `xlzawa.html` 里，双击即可打开。
不依赖任何构建工具、包管理器或外部静态资源~~(其实用到了)~~。

```
xlzawa.html   ← 整站（约 136 KB）
```

## 快速开始

直接双击 `xlzawa.html`，或起个本地服务器（推荐，避免个别浏览器的 `file://` 限制）：

```bash
python -m http.server 8765
# 然后访问 http://127.0.0.1:8765/xlzawa.html
```

部署时把 `xlzawa.html` 丢到任意静态托管（GitHub Pages / Vercel / 对象存储）即可，单文件无依赖。

## 功能

### 首页 · 3D 皮肤预览

使用 [@daidr/minecraft-skin-renderer](https://github.com/daidr/minecraft-skin-renderer)（v0.3.2，浏览器全局包已整包内联）通过 WebGL2 渲染 Minecraft 皮肤模型。

- **模型**：`slim`（3px 细手臂），对应玩家 `XiaoLiZiawa2333`
- **交互**：拖动旋转视角、滚轮缩放
- **左侧按钮**
  - 视角：`手动`（固定 3/4 视角）/ `自动`（匀速自转）
  - 动作：`站立` / `走路` / `跑步` / `飞行`
- **皮肤来源**：优先在线拉取 `https://s.namemc.com/i/5e0d50081eb7638d.png`；该站未返回 CORS 头，浏览器通常取不到，此时自动回退到页面内嵌的 base64 皮肤（同一张图），所以**离线也能正常显示**
- 底部是蓝黑斜切铭牌，显示玩家名

*让我侃一句* ：*大肥鱼不知道怎么想的，把皮肤的base64塞到文件里了，我也懒得管它了*

### 随机二次元图片

调用 `https://www.dmoe.cc/random.php`，点击图片即可换一张。*(真的做到了吗)*

### 常用链接 / 友情链接

右栏两张卡片，样式一致。友情链接里带灰色倾斜中划线小标签（`.placeholder-tag`）做备注。

### 编程相关

24 个常用编程 / 竞赛 / 学习站点的卡片墙（洛谷、GitHub、Gitee、CSDN、各 OJ、AtCoder、Codeforces、CCF、蓝桥杯……）。

每张卡片 = 图标 + 站点名，鼠标悬停会轻微上浮、边框变色。图标按三级回退：

1. 站点自身的 `/favicon.ico`
2. `https://logos.hunter.io/<域名>`（Hunter Logo API）
3. 内联 SVG 地球占位图

因为有些站点（如 Codeforces）的请求会一直挂起而不报错，脚本给每个图标加了 **6 秒超时**，到点就自动降级到下一级，不会一直空白。

*让我再侃一句，冗余做这么好，我的钱包它不怎么好啊~~*

### 暗色模式

顶栏最右侧的圆角按钮切换，选择记在 `localStorage` 的 `xlzawa-theme` 里。

- 亮色：按钮底色 `#000033`，文案「变黑~🌙」
- 暗色：按钮底色 `#ccffff`，文案「开灯！☀」
- 暗色下只把黑色文字转白，灰字（中划线标签、注释文案等）保持原样；左上角 logo 变为 `#64748b`

`<head>` 里有一段内联脚本会在首屏渲染前套用主题，避免刷新时闪白。

## 自定义

全部改动都在 `xlzawa.html` 里，按注释分块查找：

| 想改什么 | 位置 |
| --- | --- |
| 页面标题 / 站点名 | `<title>`、`.logo` 文本 |
| 玩家名 / 铭牌 | `.mc-nameplate` 里的 `.player-name` |
| 皮肤 | 搜索 `SKIN_ONLINE`（在线地址）与 `SKIN_B64`（内嵌 base64） |
| 3D 视角参数 | `createSkinViewer({ ... zoom, fov })` 与 `setRotation(0.32, Math.PI/2 - 0.06)` |
| 常用链接 / 友情链接 | `<!-- 常用链接 -->`、`<!-- 友情链接 -->` 两段 |
| 编程相关卡片 | `<!-- 编程相关 -->` 里的 `.site-grid`，复制一个 `<a class="site-card">` 即可 |
| 配色 | 各组件里的颜色值，以及 `html.dark` 覆盖块（无 CSS 变量，直接用色值） |
| 顶栏导航 | `.nav-links` 里的 `data-page` |

*让我再再侃一句，这些东西真的要写道readme.md里吗*

## 技术细节

- **零依赖**：`xlzawa.html` 内含 CSS、JS、3D 渲染器（约 88 KB minified）、皮肤贴图（base64）。没有 `<script src>`，也没有 CDN 请求（图标除外）。
- **页面切换**：三个 `data-page` 页面块靠 class 显隐，不刷新、不改 URL。
- **3D 渲染**：`MSR.use(MSR.WebGLRendererPlugin)` + `createSkinViewer({ preferredBackend: 'webgl', slim: true, antialias: true })`；`ResizeObserver` 跟随容器尺寸调用 `viewer.resize()`。
- **WebGL 不可用**时会在画布位置显示提示文案，不会白屏。
- 响应式：≤800px 单列排列（玩家卡 → 随机图 → 常用链接 → 友情链接），≤500px 进一步压缩 3D 舞台高度与按钮尺寸。

*搞得好高端一样*

## 已知限制

- 部分站点确实没有可用图标（`cspsjtest.noi.cn`、`loj.ac`、`qoj.ac`：`/favicon.ico` 返回 404 或 HTML 页面，Hunter 也查不到），统一显示地球占位图。
- 图标是站点 favicon（多为 32×32），放大到 64×64 会略有模糊。
- `favicon.im` 之类的图标 CDN 在部分网络环境（含本机）不可达，故未采用。
- 在线皮肤源 `s.namemc.com` 无 CORS 头，实际总是走内嵌回退；若日后要真正在线换皮肤，需要一个允许跨域的图床。

*怎么连试错的部分也写进来了*

## 提交记录

```
12f61fb  增加编程相关
94782c7  友链修改&黑色模式加入
b910a64  主页和头尾修改
473c21a  3D皮肤预览视觉修改
c8fec87  初始化 xlzh5 项目
```

仓库只跟踪 `xlzawa.html` 与 `.gitignore`；截图、工作区配置等都已忽略。

## 写在后面

后续考虑再加一个L2D功能，嘿嘿。

这个项目就是自娱自乐了，图个开心装个B吧。