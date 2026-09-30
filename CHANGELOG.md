# 更新记录（Mac 版）

版本号与 Windows 版 [ds-eagle-tagger](https://github.com/discipohub/ds-eagle-tagger) 对齐，功能保持一致；
差异只在平台相关部分（推理后端、安装器、路径、镜像）。

## 0.8.0

**中文标签**

- 标签语言可选中文 / English / 中英双语。新装默认中文；从旧版升级默认保持 English。
- 中文词表随插件分发，覆盖约 8,000 个普通标签：先用 LLM 翻译，撞车的译名再过一轮，
  高频标签人工校对。角色名和颜文字保留英文原文。翻译完全在本地查表，不联网。
- 「补充已有标签」配合中文时，图上已有的 WD14 英文标签原地转成中文，不会中英两套并存；
  开始前提示受影响张数并要求确认。你自己加的标签不动。
- 英文模式的处理记录与旧版兼容，升级后已处理过的图片不会被重跑。
- rating 分级标签改为默认关闭；开启后写为「分级:全年龄」等。

**界面**

- 插件更名为 DS Eagle Tagger。
- 流程重排：第一步只选范围；第二步「设置并确认」集中放标签语言、处理方式和推理张数，
  待处理张数实时更新。
- 每一页的前进按钮固定在右下角同一位置；窗口默认 1050×740，各页一屏放下不用滚动。

**修复**

- 按文件夹选图时，Eagle 运行期间新加入文件夹的图片读不到的问题。

> Mac 版先行发布 0.8.0，Windows 版随后跟进。

## 0.7.0

**支持 Intel Mac**

- Intel Mac 现在可以使用，功能与 Apple Silicon 完全一致。
- Apple Silicon 与 Intel 分成两个安装包。合成一个通用包会让每个用户都多下一份
  自己用不到的 uv 二进制（每个架构约 50MB），不划算。
- Intel 强制走 CPU 推理。x86_64 版 onnxruntime 其实也带 `CoreMLExecutionProvider`，
  但 Intel Mac 没有神经引擎，Core ML 只能落到 CPU / 核显，却照样要付约 20 秒的
  首次编译和约 2.4GB 的缓存——不如直接走 CPU。
- 装错架构的包会在启动时明确提示该下哪个版本。放着不管的话，uv 只会以
  「Bad CPU type in executable」被系统杀掉，报错里看不出是下错了包。
- 界面按机器如实显示：Intel 显示 CPU 型号和「内存」，不再套用「统一内存」；
  「实测最快」是 M2 Max 上量出来的结论，Intel 上不再跟着显示。
- Rosetta 检测逻辑不变。真 Intel Mac 不会触发，只有 Apple Silicon 上跑 Intel 版
  Eagle 才会——那种情况仍然应该换成原生版 Eagle。

## 0.6.1

与 Windows 0.6.1 功能对齐，另含以下 Mac 专属实现：

**推理后端**
- 改用 Core ML（`CoreMLExecutionProvider`），CPU 作为回退。
- 会话建立前把模型的动态 batch 维冻结为定值——Core ML 不接受未定界动态维，不冻结会在编译期直接失败。
- Core ML 编译缓存按「模型版本 + batch」分目录存放，只保留当前在用的一份。
- 冻结 batch 后，不足一批的尾批自动补齐再丢弃多余结果。
- Core ML 初始化失败时回退 CPU，并在界面如实显示「CPU 推理（较慢）」，不会名义加速实际跑 CPU。

**Batch 策略（与 Windows 相反）**
- 自动模式恒定推荐 1。M2 Max 实测：batch=1 约 3.4–3.7 张/秒，batch=2/4 掉到 0.91 张/秒。
- 统一内存不参与 batch 推荐，界面显示芯片型号与统一内存总量，不再显示「可用显存」。

**安装器**
- 安装位置改为 `~/Library/Application Support/EagleAutoTagger`。
- 附带 Apple Silicon 版 `uv`；执行前清除 Gatekeeper 隔离标记并回读校验，被拦截时给出可操作的提示而不是无意义的退出码。
- 优先复用系统已装的 Python（3.11–3.13），没有才下载。

**国内网络优化**
- 推理依赖优先走清华 PyPI 镜像（实测直连官方源仅约 72 KB/s，镜像 20+ MB/s），失败回退官方。
- 独立 Python 下载回退南京大学镜像。
- 模型下载 hf-mirror 优先。
- 模型目录改走 jsDelivr CDN（`raw.githubusercontent.com` 国内基本不可达），保留 GitHub 作为兜底。

**升级处理**
- 自动迁移 0.3.x 的模型缓存到新的版本目录。
- 自动清理 0.3.x 遗留的 Core ML 缓存（约 2.4 GB）。

## 0.3.1

- Mac 首个公开版本，基于 Windows 0.3.1。
- Apple Silicon 原生，Core ML 推理，本地打标与写回 Eagle。
