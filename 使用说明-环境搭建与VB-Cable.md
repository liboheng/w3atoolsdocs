# W3ATools 环境搭建与使用说明

本文档介绍从零开始搭建语音相关环境：安装 Python 3.11.9、安装 PyTorch/Transformers 依赖、下载安装 VB-Cable 虚拟声卡，以及完整使用流程。适合 Perle 语音录制、Voice SDK Pro 克隆语音等场景。

---

## 目录

1. [安装 Python 3.11.9](#一安装-python-3119)
2. [安装 PyTorch 与 Transformers](#二安装-pytorch-与-transformers)
3. [VB-Cable 下载、安装与使用](#三vb-cable-下载安装与使用)
4. [完整使用流程检查清单](#四完整使用流程检查清单)
5. [常见问题](#五常见问题)

---

## 一、安装 Python 3.11.9

### 1.1 下载安装包

- **官方直链（Windows 64 位）：**  
  https://www.python.org/ftp/python/3.11.9/python-3.11.9-amd64.exe  

- 或从官网进入： https://www.python.org/downloads/ → 选择 Python 3.11.9 → Windows installer (64-bit)。

### 1.2 运行安装程序

1. 双击 `python-3.11.9-amd64.exe` 运行。
2. **重要：** 在首屏勾选 **“Add python.exe to PATH”**，再点击 **“Install Now”**（或自定义安装路径后继续）。
3. 等待安装完成，点击 **“Close”**。
4. 若安装时未勾选 “Add to PATH”，可重新运行安装程序，选择 **“Modify”**，勾选 “Add Python to environment variables” 后完成。

### 1.3 验证安装

打开 **命令提示符（CMD）** 或 **PowerShell**，执行：

```bash
python --version
```

应显示：`Python 3.11.9`。

再执行：

```bash
pip --version
```

应显示 pip 版本信息（如 `pip 24.x from ...`）。

---

## 二、安装 PyTorch 与 Transformers

用于语音克隆（如 Voice SDK Pro / clone_voice）等能力，需要固定版本以避免兼容问题。

### 2.1 打开终端

在 **CMD** 或 **PowerShell** 中操作（建议以**非管理员**身份运行，使用用户目录）。

### 2.2 安装依赖（推荐使用国内镜像加速）

依次执行（可复制整段）：

```bash
pip install torch==2.1.2 torchaudio==2.1.2 transformers==4.40.0 -i https://pypi.tuna.tsinghua.edu.cn/simple
```

若不需要镜像，可直接：

```bash
pip install torch==2.1.2 torchaudio==2.1.2 transformers==4.40.0
```

### 2.3 验证安装

在终端中执行：

```bash
python -c "import torch; import torchaudio; import transformers; print('torch:', torch.__version__); print('torchaudio:', torchaudio.__version__); print('transformers:', transformers.__version__)"
```

应看到类似输出：

- `torch: 2.1.2`
- `torchaudio: 2.1.2`
- `transformers: 4.40.0`

---

## 三、VB-Cable 下载、安装与使用

VB-Cable（VB-Audio Virtual Cable）是虚拟声卡，用于将“系统播放的声音”变成“麦克风输入”，从而让浏览器/录音端录到 TTS 或克隆语音。Perle 语音任务依赖正确的播放设备 + 录音设备配置。

### 3.1 下载

1. 打开官网：**https://vb-audio.com/Cable/**
2. 在页面中找到 **“Download”**，下载 **`VBCABLE_Driver_Pack43.zip`**（或当前页面推荐的对应版本）。
3. 解压到任意文件夹（如桌面或 `C:\Tools\VBCable`）。

### 3.2 安装

1. 在解压目录中找到 **`VBCABLE_Setup_x64.exe`**。
2. **右键** → **“以管理员身份运行”**。
3. 按提示完成安装（若提示重启，选择稍后重启或立即重启）。
4. **安装完成后务必重启电脑**，否则设备可能不显示。

### 3.3 配置系统音频（使用 VB-Cable）

重启后，将系统默认播放和录音都设为 VB-Cable，这样“播放到 CABLE Input”的声音会从“CABLE Output”被录到。

#### 设置默认播放设备

1. 右键任务栏 **音量图标** → **“声音设置”**；或：**控制面板** → **硬件和声音** → **声音**。
2. 打开 **“播放”** 选项卡，找到 **“CABLE Input (VB-Audio Virtual Cable)”**。
3. 右键该设备 → **“设为默认设备”**（若有“设为默认通信设备”也可一并设置）。
4. 点击 **“应用”**。

#### 设置默认录音设备

1. 在同一“声音”窗口中，切换到 **“录音”** 选项卡。
2. 找到 **“CABLE Output (VB-Audio Virtual Cable)”**。
3. 右键 → **“设为默认设备”**。
4. 点击 **“应用”** → **“确定”**。

> 【建议配图：声音 → 录音 → CABLE Output 设为默认】

### 3.4 配置验证

确认当前状态为：

| 类型     | 设备名称                              | 状态   |
|----------|---------------------------------------|--------|
| 播放设备 | CABLE Input (VB-Audio Virtual Cable)  | ✓ 默认 |
| 录音设备 | CABLE Output (VB-Audio Virtual Cable) | ✓ 默认 |

### 3.5 浏览器麦克风权限（首次运行任务时）

首次运行 Perle 语音任务时，浏览器会请求麦克风权限：

1. 在麦克风下拉列表中选择 **“CABLE Output”**（或带 VB-Audio 字样的虚拟麦克风）。
2. 点击 **“允许”**。

### 3.6 使用流程简述

- **运行任务前**：确保系统播放 = CABLE Input，系统录音 = CABLE Output，浏览器已授权 CABLE Output。
- **任务中**：应用/脚本把 TTS 或克隆语音播放到“默认播放设备”（即 CABLE Input），浏览器从“默认麦克风”（CABLE Output）录制，实现无损内录。
- **任务完成后**：若需正常听声，将 **播放设备** 改回真实扬声器/耳机，否则会听不到系统声音。

更详细的故障排查见：[虚拟麦克风配置教程](./virtual-microphone-setup.md)。

---

## 四、完整使用流程检查清单

按顺序确认：

- [ ] **Python 3.11.9** 已安装，且 `python --version` 正确。
- [ ] **pip** 可用，已安装 `torch==2.1.2`、`torchaudio==2.1.2`、`transformers==4.40.0`。
- [ ] **VB-Cable** 已安装并已重启电脑。
- [ ] 系统 **播放** 默认设备为 **CABLE Input**。
- [ ] 系统 **录音** 默认设备为 **CABLE Output**。
- [ ] 浏览器已授权麦克风，并选择 **CABLE Output**。
- [ ] 其他会占用麦克风或扬声器的程序已关闭（避免杂音）。

---

## 五、常见问题

### Q: 安装 Python 时没勾选 “Add to PATH”，现在命令找不到 `python`？

重新运行安装程序 → 选择 **“Modify”** → 勾选 **“Add Python to environment variables”**（或 “Add python.exe to PATH”）→ 完成。然后**重新打开** CMD/PowerShell 再试。

### Q: `pip install torch` 很慢或超时？

使用国内镜像，例如：

```bash
pip install torch==2.1.2 torchaudio==2.1.2 transformers==4.40.0 -i https://pypi.tuna.tsinghua.edu.cn/simple
```

### Q: 浏览器麦克风列表里没有 CABLE Output？

- 确认 VB-Cable 已安装并已**重启电脑**。
- 重启浏览器；在系统 **隐私设置** 中确认麦克风权限已开启。
- 若仍没有，可查看 [虚拟麦克风配置教程](./virtual-microphone-setup.md) 中的“浏览器麦克风列表没有虚拟设备”部分。

### Q: 录音没有声音？

- 确认系统**播放**设备是 **CABLE Input**（否则声音没进虚拟线缆）。
- 确认系统**录音**设备是 **CABLE Output**，且浏览器选的是该设备。
- 检查系统与播放器音量未静音，且脚本/应用确实在播放音频。

### Q: 任务完成后听不到声音？

把系统 **播放设备** 从 “CABLE Input” 改回 **扬声器/耳机** 即可。

---

## 相关链接

| 项目           | 链接 |
|----------------|------|
| Python 3.11.9 直链 (Windows 64 位) | https://www.python.org/ftp/python/3.11.9/python-3.11.9-amd64.exe |
| Python 官网    | https://www.python.org/downloads/ |
| VB-Cable 官网  | https://vb-audio.com/Cable/ |
| 虚拟麦克风详细教程 | [virtual-microphone-setup.md](./virtual-microphone-setup.md) |
