# WorkBuddy — TrafficMonitor Win11 皮肤

Win11 Fluent 风格的双主题皮肤，在 8 项硬件指标之外显示 **WorkBuddy 剩余积分** 和 **今日已用积分**。

## 显示内容

| 区域 | 项目 |
| --- | --- |
| 网络 | 上传、下载 |
| CPU | 使用率、温度、频率 |
| GPU | 使用率、温度 |
| 内存 | 占用率 |
| WorkBuddy | 剩余积分、今日已用（仅详细模式） |

- `skins/WorkBuddy-Light` — 浅色（强调色 `#0067C0`）
- `skins/WorkBuddy-Dark` — 深色（强调色 `#4CC2FF`）
- 是否勾选「显示更多信息」**只决定额度卡在不在**：两种模式都有完整的 8 项硬件指标。

文字全部烘焙在背景图里，程序只画数值，所以标签颜色和字重可以精确贴合 Win11 规范。

## 安装

1. 把 `skins/WorkBuddy-Light`、`skins/WorkBuddy-Dark` 复制到 TrafficMonitor 的 `skins\` 目录。
2. 额度需要 [`WorkBuddyUsage.dll`](https://github.com/wiggins-kong/WorkBuddyUsage-TrafficMonitorPlugin) 插件：
   把 DLL 放进 `plugins\`，重启 TrafficMonitor，在插件选项里填好 magpie 地址和 API Key。
3. 主窗口右键 → 皮肤 → 选择 `WorkBuddy-Light` 或 `WorkBuddy-Dark`。
4. 想跟随系统深浅色：皮肤对话框里勾选「自动切换」，深色选 `WorkBuddy-Dark`，浅色选 `WorkBuddy-Light`。

> 皮肤只负责排版，插件条目 ID 固定为 `wbucreditleft`（剩余积分）和 `wbucredittoday`（今日已用）。

## 两种模式

| 模式 | 窗口 | 内容 |
| --- | --- | --- |
| 详细（勾选「显示更多信息」） | 260 × 396 | 8 项硬件指标 + WorkBuddy 额度卡 |
| 普通（未勾选） | 260 × 288 | 8 项硬件指标 |

普通模式比旧版 OCGO 皮肤高 68px（220 → 288），因为 8 项指标在单列里全部保留。

## 预览

```powershell
node demo\serve.js
```

打开 http://127.0.0.1:8765 ，同屏对比深浅色、详细/普通两种模式。
`?values=0` 隐藏数值，`?theme=dark` 切预览页背景。

## 重新生成皮肤

```powershell
pwsh -File gen\build.ps1
```

`gen/bg.html` 是背景图模板（`?theme=light|dark&mode=l|s`），`gen/build.ps1` 用无头 Edge 按 2 倍分辨率截图，
再生成遮罩 BMP 和 `skin.xml`。

改布局时，`gen/bg.html` 里的 `ROWS`、`L`、`S` 坐标和 `gen/build.ps1` 里的 `$DIM`、`layout_*`
坐标要一起改，否则标签与数值会错位。

## 目录

- `gen/` — 皮肤生成器（背景模板 + 构建脚本）
- `skins/` — 生成产物（背景图、遮罩、`skin.xml`）
- `demo/` — 本地预览页

## 发版

推一个 `v*` 标签就会触发 `.github/workflows/release.yml`：把两个皮肤目录打包成 zip，
按 `changelog.md` 里同名小节的内容发 Release。

```powershell
git tag -a v1.0 -m "v1.0"
git push origin v1.0
```
