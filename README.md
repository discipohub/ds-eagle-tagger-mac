# ds Eagle Tagger · Mac 版

用 WD14 模型给 [Eagle](https://eagle.cool) 素材库自动打标签。**全部在本机运行**，图片不上传。

收藏了几万张素材约等于没收藏——因为找不到。手动打标签没人愿意做，所以有了这个插件。

[![下载](https://img.shields.io/badge/下载-v0.6.1-orange)](../../releases/latest)
![平台](https://img.shields.io/badge/平台-Apple%20Silicon-black)
![本地运行](https://img.shields.io/badge/推理-100%25%20本地-green)

![正在识别](screenshot-running.png)

> Windows 版在 [ds-eagle-tagger](https://github.com/discipohub/ds-eagle-tagger)，本仓库只覆盖 macOS（Apple Silicon）。

---

## 运行要求

| 项目 | 要求 |
|---|---|
| 芯片 | Apple Silicon（M1/M2/M3/M4）。**暂不支持 Intel Mac** |
| 系统 | macOS 13 或更新 |
| Eagle | **必须是 Apple Silicon 原生版**，不能是 Rosetta 运行的 Intel 版 |
| 磁盘 | 预留 5 GB（模型 1.2 GB + 推理缓存 2.4 GB + 运行环境） |
| 网络 | 仅首次安装需要 |

> **怎么确认 Eagle 是原生版**：活动监视器 → 找到 Eagle → 看「种类」列，应为 Apple 而不是 Intel。
> 插件启动时也会自动检测，如果是 Rosetta 会直接提示。

## 安装

![安装本地推理引擎](screenshot-install.png)

1. 到 [Releases](../../releases/latest) 下载 `.eagleplugin` 文件
2. 双击安装，Eagle 会自动导入
3. 在 Eagle 插件面板打开 ds Eagle Tagger，点「开始安装」
4. 等待运行环境准备完成
5. 第一次识别时会下载 WD14 模型（约 1.26 GB），有进度显示和断点续传

> ⚠️ **不要手动双击插件目录里的 `engine/tools/uv`**。那是安装工具，手动打开会被 macOS 拦截并**记住这次拒绝**，之后插件自己调用也会失败。交给插件自动处理即可。

### 国内网络已做优化

安装过程中的每一步都优先走国内源，失败才回退官方：

| 环节 | 主源 | 兜底 |
|---|---|---|
| Python 运行环境 | 复用系统已装的 Python | 南京大学镜像 |
| 推理依赖（约 45 MB） | 清华 PyPI 镜像 | 官方 PyPI |
| WD14 模型（1.26 GB） | hf-mirror | Hugging Face |
| 模型目录 | jsDelivr CDN | GitHub |

实测纯国内直连（无代理）：依赖安装约 3 秒，模型下载约 95 秒。

## 首次使用会慢，这是正常的

| 阶段 | 耗时 | 说明 |
|---|---|---|
| 装运行环境 | 数十秒 | 一次性 |
| 下载模型 | 1.26 GB | 一次性，支持断点续传 |
| 首次 Core ML 编译 | 约 20 秒 | 界面会提示「正在首次编译 Core ML 模型」，**不是卡死** |

之后每次启动都复用缓存，秒开。

## 每批张数保持 1

界面默认「自动 1」。**Apple Silicon 上 1 张最快**：

| 配置 | 速度 |
|---|---|
| **Core ML batch=1** | **约 3.4–3.7 张/秒** |
| Core ML batch=2 | 0.91 张/秒 |
| Core ML batch=4 | 0.91 张/秒 |
| 纯 CPU 回退 | 0.62 张/秒 |

*（M2 Max 实测，端到端——含读图、推理、写回 Eagle。纯推理峰值约 4.8 张/秒，但日常看到的是端到端数字。）*

这和 NVIDIA 显卡的经验**相反**——在 Apple Silicon 上调大 batch 不仅更慢，还要重新编译一次模型、多占 2.4 GB 缓存。除非你想自己对比，否则保持默认。

![任务完成](screenshot-done.png)

上图是一次真实的批量任务：454 张成功写入，20 张因为已有标签被自动跳过，2 张失败（源文件本身损坏）。失败项可以一键在 Eagle 中选中定位。

## 功能

- **三种打标范围**：选定文件夹（含子文件夹）／Eagle 当前选中的图片／整个图库
- **不覆盖已有标签**：默认跳过已打标的图片，也可以选择在已有标签上**补充**
- **本地处理记录**：记住哪些图片处理过，换库或重装后不重复劳动；记录库位置可自定义，支持跨硬盘迁移
- **模型更新**：检查经过验证的新模型版本，确认后再下载，不会自动替换
- **失败可定位**：任何失败项都能一键在 Eagle 中选中
- **中断安全**：已完成的标签立即写回，随时可停止

## 隐私

图片和标签全部在本机处理，不会上传任何地方。没有账号系统、没有遥测、没有行为统计。

联网只发生在两处：下载运行组件、下载 WD14 模型。

详见 [PRIVACY.md](PRIVACY.md)。

## 卸载

1. 在 Eagle 插件面板移除插件
2. 删除 `~/Library/Application Support/EagleAutoTagger`（访达按 ⇧⌘G 前往）

卸载插件不会自动删这个目录，避免你重装时重复下载 1.26 GB 模型。

## 常见问题

**提示检测到 Rosetta**
装 Apple Silicon 原生版 Eagle。混用 x64 Eagle 和 arm64 推理组件会导致 Core ML 不可用。

**安装时 Connection refused / 下载失败**
插件已内置国内镜像兜底，重试一般能过。安装日志会写明当前走的是镜像还是官方源。

**提示「安装工具被系统安全策略终止」**
在访达里右键点插件包选「打开」，或按界面提示在终端执行给出的 `xattr` 命令。这是 macOS Gatekeeper 对未公证二进制的拦截。

**识别结果是英文标签**
WD14 模型输出的就是英文 danbooru 风格标签，这是模型本身的特性。

**「检查模型更新」点了没反应 / 连不上**
模型目录托管在 GitHub，国内可能访问受限。插件已优先走 jsDelivr CDN，仍失败时会提示「在线更新源暂时无法连接」——不影响已装模型的正常使用。

## 源码

当前提供预编译包。源码整理后开源。

## 第三方组件

本插件使用了 uv、ONNX Runtime、WD14 模型等第三方组件，许可信息见 [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md)。

模型来自 [SmilingWolf/wd-eva02-large-tagger-v3](https://huggingface.co/SmilingWolf/wd-eva02-large-tagger-v3)。
