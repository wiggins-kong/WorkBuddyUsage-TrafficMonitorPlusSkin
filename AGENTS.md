# AGENTS.md

## 项目概览

TrafficMonitor 的 XML 皮肤仓库，附带 WorkBuddy 剩余积分/今日已用的纯文字展示。
皮肤由无头 Edge 截图生成背景，**没有编译代码、没有单元测试、没有 lint**。
产物（`skins/`）提交进仓库，用户下载后直接复制到 TrafficMonitor 的 `skins\` 目录。

## 环境

- Windows + PowerShell 7（`pwsh`）
- 无头生成背景图需要 Edge：`C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`
- 预览 demo 需要 Node.js

## 命令

- 重新生成皮肤（背景图 + 遮罩 + `skin.xml`）：`pwsh -File gen\build.ps1`
- 本地预览 demo：`node demo\serve.js` → http://127.0.0.1:8765

## 关键事实

- 两套皮肤：`skins/WorkBuddy-Light`（强调色 `#0067C0`）、`skins/WorkBuddy-Dark`（`#4CC2FF`）。
- `layout_l` = 勾选「显示更多信息」，`layout_s` = 未勾选。
- **两种布局都包含全部 8 项硬件指标，坐标完全相同**：`up`、`down`、`cpu`、`cpu_temperature`、
  `gpu`、`gpu_temperature`、`memory`、`cpu_freq`。`layout_l` 只在底部多一张额度卡。
- 额度卡的两个插件条目（在 `plugin_map` 里映射）：
  - `wbu_left` → `wbucreditleft`（剩余积分）
  - `wbu_today` → `wbucredittoday`（今日已用）
- 标签、标题全部烘焙进背景图，`skin.xml` 里所有 `display_text` 留空。
- 窗口尺寸：详细 `260 × 396`，普通 `260 × 288`；背景图按 2 倍分辨率（520×792 / 520×576）。
- 遮罩 BMP 与背景图同尺寸，圆角半径 16（2 倍像素）。

## 坐标约定

- 行坐标在 `gen/bg.html` 的 `ROWS` 里：46、74、102…242，行高 28。
- `skin.xml` 里 item 的 `y = 行坐标 + 3`：宿主用 GDI+ 垂直居中画数值，基线比背景标签高约 3px，这个偏移别丢。
- 数值列统一在 `x=140`、`width=100`、`align=1`（右对齐）。
- 额度卡：卡片 `8,288,244×100`，标题 `298`，两行 `322`/`350`。

## 修改流程

1. 同时改 `gen/bg.html`（`ROWS`、`L`、`S`、文案）和 `gen/build.ps1`（`$DIM`、`layout_l`/`layout_s`、`preview`）。
2. 运行 `pwsh -File gen\build.ps1` 重新生成。
3. 打开 `skins/*/background_l.png`、`background.png` 检查标签有没有被裁切。
4. `node demo\serve.js` 用真实数值列预览两种模式。
5. 不要手改生成物：`skins/*/skin.xml`、`background*.png`、`background_mask*.bmp` 都会被 `gen\build.ps1` 覆盖。

## 风格约定

- 文案与变更说明用中文；命令、路径、标识符保持原样。
- 视觉沿用 Win11 Fluent：Segoe UI Variable Text 9pt 粗体、8px 圆角、半透明卡片、单列排版。
- 不引入前端框架或包管理器；`gen/` 和 `demo/` 都是单文件 HTML + 原生 JS。

## 发布

- 工作流：`.github/workflows/release.yml`，推 `v*` 标签触发。
- 它把 `skins/WorkBuddy-Light`、`skins/WorkBuddy-Dark` 打包成 zip，并从 `changelog.md`
  取与标签同名的小节作为 Release 说明。
- 发版前在 `changelog.md` 顶部加 `## v1.0` 这样的小节，再推标签。
- 工作流**不重新生成背景图**：截图在不同 Edge 版本/机器上会有像素差异，CI 只打包已提交的产物。

## 已知限制

- 主窗口皮肤没有图形节点，不能显示插件的占用图（进度条）；额度只能显示文字。
- `layout_s` 为单列 8 行，窗口高度 288，比旧版 OCGO 普通模式高。
- 插件未安装或未配置时，额度两行显示 `--`，皮肤本身不报错。
