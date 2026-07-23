# 更新记录（Mac 版）

版本号与 Windows 版 [ds-eagle-tagger](https://github.com/discipohub/ds-eagle-tagger) 对齐，功能保持一致；
差异只在平台相关部分（推理后端、安装器、路径、镜像）。

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
