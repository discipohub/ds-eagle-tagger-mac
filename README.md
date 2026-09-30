# DS Eagle Tagger · Mac 版

用 WD14 模型给 [Eagle](https://eagle.cool) 素材库自动打标签，**可写中文、英文或中英双语**。全部在本机运行，图片不上传。

收藏了几万张素材约等于没收藏——因为找不到。手动打标签没人愿意做，所以有了这个插件。

[![下载](https://img.shields.io/badge/下载-v0.8.0-orange)](../../releases/latest)
![平台](https://img.shields.io/badge/平台-Apple%20Silicon%20%2F%20Intel-black)
![本地运行](https://img.shields.io/badge/推理-100%25%20本地-green)

![正在识别](screenshot-running.png)

> Windows 版在 [ds-eagle-tagger](https://github.com/discipohub/ds-eagle-tagger)，本仓库只覆盖 macOS。

---

## 运行要求

| 项目 | 要求 |
|---|---|
| 芯片 | Apple Silicon（M1/M2/M3/M4）或 Intel Mac |
| 系统 | macOS 13 或更新 |
| 磁盘 | Apple Silicon 预留 5 GB，Intel 预留 3 GB |
| 网络 | 仅首次安装需要 |

**Apple Silicon 和 Intel 是两个安装包**，下载时按你的机器选。装错了插件会直接提示，不会装出一个跑不起来的环境。

> 不确定自己是哪种：屏幕左上角苹果菜单 → 关于本机 → 看「芯片」是 Apple 还是 Intel。

Apple Silicon 走 Core ML，用得上神经引擎；Intel 没有神经引擎，走 CPU 推理，速度明显慢一截，但功能完全一样。

> **Apple Silicon 用户注意**：Eagle 必须是 Apple Silicon 原生版，不能是 Rosetta 运行的 Intel 版，否则 Core ML 用不上。
> 活动监视器 → 找到 Eagle → 看「种类」列，应为 Apple 而不是 Intel。插件启动时也会自动检测并提示。

## 安装

不想读步骤，可以让 AI 带你装；愿意自己动手，往下看手动安装。

### 方式一：让 AI 带你装

把下面这句复制给你的 AI 助手（Claude、ChatGPT、Claude Code、Cursor 等都行），
它会读懂本说明、先确认你的机器能不能装，再一步步带你完成：

```
帮我安装 Mac 版 Eagle 插件 DS Eagle Tagger，安装指南：https://github.com/discipohub/ds-eagle-tagger-mac
先读指南确认我的 Mac 和 Eagle 是否满足要求、该下 Apple Silicon 还是 Intel 版，再一步步带我装（下载和双击安装由我来点）。
```

> 下载 `.eagleplugin`、双击安装、在插件里点按钮，这几步仍然是你本人操作——
> AI 负责讲清楚每一步、在报错时帮你判断，不会替你动系统。

### 方式二：手动安装

![安装本地推理引擎](screenshot-install.png)

1. 到 [Releases](../../releases/latest) 下载对应你机器的 `.eagleplugin`：Apple Silicon 选 `Apple-Silicon`，Intel 选 `Intel`
2. 双击安装，Eagle 会自动导入
3. 在 Eagle 插件面板打开 DS Eagle Tagger，点「开始安装」
4. 等待运行环境准备完成
5. 选择打标范围 → 在「设置并确认」页选标签语言和处理方式 → 开始识别
6. 第一次识别时会下载 WD14 模型（约 1.26 GB），有进度显示和断点续传

> ⚠️ **不要手动双击插件目录里的 `engine/tools/uv`**。那是安装工具，手动打开会被 macOS 拦截并**记住这次拒绝**，之后插件自己调用也会失败。交给插件自动处理即可。

国内网络已做优化，安装和模型下载优先走国内源，无需代理。

首次使用需要安装运行环境和下载模型（约 1.26 GB），之后秒开。批处理张数保持默认即可。

Apple Silicon 首次识别还要编译一次 Core ML 模型（约 20 秒），界面会提示，不是卡死；Intel 走 CPU 推理，没有这一步。

![任务完成](screenshot-done.png)

## 功能

- **中文标签**：可选中文（默认）/ English / 中英双语。内置约 8,000 条中文词表，角色名和颜文字保留英文原文
- **三种打标范围**：选定文件夹（含子文件夹）／Eagle 当前选中的图片／整个图库
- **不覆盖已有标签**：默认跳过已打标的图片，也可以选择在已有标签上**补充**。补充时选中文，图上已有的英文标签会一并转成中文；开始前会提示受影响张数并要求确认，你自己加的标签不受影响
- **不重复处理**：记住哪些图片处理过，换库或重装后不重复劳动
- **模型更新**：支持检查和下载新版本模型
- **失败可定位**：任何失败项都能一键在 Eagle 中选中
- **中断安全**：已完成的标签立即写回，随时可停止

## 隐私

图片和标签全部在本机处理，不会上传任何地方。没有账号系统、没有遥测、没有行为统计。

联网只发生在两处：下载运行组件、下载 WD14 模型。中文翻译用的是插件内置词表，不调用任何在线翻译。

详见 [PRIVACY.md](PRIVACY.md)。

## 卸载

1. 在 Eagle 插件面板移除插件
2. 删除 `~/Library/Application Support/EagleAutoTagger`（访达按 ⇧⌘G 前往）

卸载插件不会自动删这个目录，避免你重装时重复下载 1.26 GB 模型。

## 常见问题

**提示装错了架构的插件包**
到 Releases 下载另一个包重装：Apple Silicon 机器用 `Apple-Silicon`，Intel 机器用 `Intel`。

**提示检测到 Rosetta**
这条只会出现在 Apple Silicon 机器上——说明你装的 Eagle 是 Intel 版，正被 Rosetta 翻译运行，Core ML 用不上。装 Apple Silicon 原生版 Eagle 即可。Intel Mac 上不会遇到这个提示。

**安装时 Connection refused / 下载失败**
插件已内置国内镜像兜底，重试一般能过。安装日志会写明当前走的是镜像还是官方源。

**提示「安装工具被系统安全策略终止」**
在访达里右键点插件包选「打开」，或按界面提示在终端执行给出的 `xattr` 命令。这是 macOS Gatekeeper 对未公证二进制的拦截。

**想要英文标签 / 中英双语**
在「设置并确认」页切换标签语言，插件会记住你的选择。从旧版升级的用户默认保持 English，不会突然改变已有习惯。

**有些标签还是英文**
角色名、颜文字和极少数没有对应译法的标签保留英文原文，这是有意为之——硬翻反而搜不到。

**rating 分级标签去哪了**
默认关闭。需要时在设置里打开，会写成「分级:全年龄」「分级:擦边」等。

**「检查模型更新」点了没反应 / 连不上**
模型目录托管在 GitHub，国内可能访问受限。插件已优先走 jsDelivr CDN，仍失败时会提示「在线更新源暂时无法连接」——不影响已装模型的正常使用。

## 许可

源码采用 [PolyForm Noncommercial 1.0.0](LICENSE)，作者 discipo。你可以自由查看、学习、修改和非商业使用；商业用途需[联系作者](https://github.com/discipohub/ds-eagle-tagger-mac/issues)获得授权。

模型与其它第三方组件沿用各自的许可条款，见下。

## 第三方组件

本插件使用了 uv、ONNX Runtime、WD14 模型等第三方组件，许可信息见 [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md)。

模型来自 [SmilingWolf/wd-eva02-large-tagger-v3](https://huggingface.co/SmilingWolf/wd-eva02-large-tagger-v3)。
