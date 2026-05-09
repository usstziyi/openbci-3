# OpenBCI Cyton 脑机接口系统化学习计划

## 总体概述

本学习计划专为具备**基础 Python 编程能力**和**神经科学基础知识**的学习者设计，基于 OpenBCI 官方文档与 GitHub 开源仓库，提供从设备连接到高级 BCI 应用的完整学习路径。

### OpenBCI Cyton 简介

OpenBCI Cyton 是一款开源 8 通道（可扩展至 16 通道）脑电（EEG）采集设备，核心组件包括：

- **ADS1299**：24 位高精度模拟前端（AFE），由 Texas Instruments 制造
- **PIC32MX250F128B**：32 位微控制器
- **RFduino**：BLE 蓝牙无线传输模块
- **LIS3DH**：三轴加速度计
- **采样率**：250 Hz（标准模式），支持多种降采样选项
- **通信方式**：USB Dongle（串口 115200 baud）或 WiFi Shield

### 应用场景

- 脑电（EEG）研究与实验
- 肌电（EMG）与心电（EKG）信号采集
- 实时脑机接口（BCI）应用开发
- 神经反馈训练系统
- 睡眠研究与认知科学实验
- 教学与原型开发

### 学习路径图

```
Unit 1: 设备连接与基础命令
    │
    ▼
Unit 2: 数据流解析与格式处理
    │
    ▼
Unit 3: 信号处理与滤波技术
    │
    ▼
Unit 4: 实时数据可视化
    │
    ▼
Unit 5: EEG 特征提取与分析
    │
    ▼
Unit 6: 高级 BCI 应用 (SSVEP/P300)
```

### 前置知识要求

- Python 3.8+ 基础编程能力（函数、类、模块、异常处理）
- NumPy / Pandas 基础
- 神经科学基础（了解 EEG、神经元活动、脑区功能）
- 基础信号处理概念（采样率、频域、傅里叶变换）

---

## 环境依赖说明

### Python 环境推荐配置

- **Python 版本**：3.8 - 3.11（推荐 3.10）
- **包管理器**：pip 或 conda
- **虚拟环境**：推荐使用 `venv` 或 `conda` 创建独立环境

### 核心依赖库

| 库名称 | 版本 | 用途 |
|-------|------|------|
| `numpy` | >=1.21.0 | 数值计算与数组操作 |
| `scipy` | >=1.7.0 | 信号处理（滤波器、FFT） |
| `matplotlib` | >=3.4.0 | 数据可视化 |
| `pyserial` | >=3.5 | 串口通信（Cyton USB Dongle） |
| `pandas` | >=1.3.0 | 数据存储与处理 |
| `mne` | >=1.0.0 | EEG 数据处理与分析 |
| `brainflow` | >=5.0.0 | OpenBCI 官方推荐的现代数据驱动 |
| `jupyter` | >=1.0.0 | Notebook 运行环境 |
| `ipywidgets` | >=7.6.0 | 交互式可视化组件 |

### 安装命令

```bash
# 创建并激活虚拟环境
python -m venv openbci_env
source openbci_env/bin/activate  # Linux/macOS
openbci_env\Scripts\activate     # Windows

# 安装核心依赖
pip install numpy>=1.21.0 scipy>=1.7.0 matplotlib>=3.4.0
pip install pyserial>=3.5 pandas>=1.3.0
pip install mne>=1.0.0 brainflow>=5.0.0
pip install jupyter>=1.0.0 ipywidgets>=7.6.0
```

### OpenBCI Cyton 设备连接要求

#### 硬件连接步骤

1. **组装设备**：将 USB Dongle 插入电脑 USB 端口，确保 Cyton 板已安装电池（3-6V DC）
2. **安装驱动**：
   - **Windows**：安装 FTDI VCP 驱动（[下载链接](https://ftdichip.com/drivers/vcp-drivers/)）
   - **macOS/Linux**：通常无需额外驱动
3. **确认端口**：
   - Windows：设备管理器中查找 `COM*` 端口
   - macOS：`/dev/tty.usbserial-*`
   - Linux：`/dev/ttyUSB*`
4. **固件版本**：推荐使用 `v3.0.0` 或更高版本（通过 OpenBCI GUI 更新）

#### 连接测试

```python
import serial
import serial.tools.list_ports

# 列出所有可用串口
ports = serial.tools.list_ports.comports()
for port in ports:
    print(f"{port.device}: {port.description}")
```

### 重要注意事项

- Cyton 通过 USB Dongle 通信时，数据流上限为 **250 SPS**
- 确保电池电压在 3-6V 范围内
- Windows 用户需要正确安装 FTDI 驱动
- 首次使用前，建议通过 OpenBCI GUI 验证硬件工作正常

---

## 单元详细结构

### Unit 1: 设备连接与基础命令

| 项目 | 内容 |
|------|------|
| **学习目标** | 掌握 Cyton 设备初始化、串口通信、基础命令发送 |
| **核心概念** | 串口协议、ASCII 命令集、设备响应解析 |
| **关键技术** | pyserial、设备复位、通道开关、采样率设置 |
| **实践任务** | 连接设备、发送命令、读取设备信息 |

### Unit 2: 数据流解析与格式处理

| 项目 | 内容 |
|------|------|
| **学习目标** | 理解二进制数据格式，实现数据包解析与电压转换 |
| **核心概念** | 数据包结构、24-bit ADC、电压缩放公式、辅助数据 |
| **关键技术** | 字节流解析、结构体 unpack、数据校准 |
| **实践任务** | 解析原始数据流、转换为电压值、保存为 CSV |

### Unit 3: 信号处理与滤波技术

| 项目 | 内容 |
|------|------|
| **学习目标** | 掌握 EEG 信号预处理：去噪、滤波、伪影去除 |
| **核心概念** | 带通滤波、陷波滤波、IIR/FIR 滤波器设计 |
| **关键技术** | scipy.signal、Butterworth 滤波器、50/60Hz 工频去除 |
| **实践任务** | 对原始 EEG 数据应用滤波、对比滤波前后效果 |

### Unit 4: 实时数据可视化

| 项目 | 内容 |
|------|------|
| **学习目标** | 实现多通道 EEG 信号的实时滚动显示 |
| **核心概念** | 动画绘图、缓冲区管理、多通道布局 |
| **关键技术** | matplotlib.animation、FuncAnimation、实时刷新 |
| **实践任务** | 构建 8 通道实时 EEG 波形显示器 |

### Unit 5: EEG 特征提取与分析

| 项目 | 内容 |
|------|------|
| **学习目标** | 掌握频域分析、功率谱密度计算、脑波频段特征提取 |
| **核心概念** | FFT、PSD、Delta/Theta/Alpha/Beta/Gamma 频段、不对称指数 |
| **关键技术** | scipy.signal.welch、频带功率计算、特征矩阵构建 |
| **实践任务** | 计算各脑波频段功率、生成特征报告、可视化频谱图 |

### Unit 6: 高级 BCI 应用

| 项目 | 内容 |
|------|------|
| **学习目标** | 实现 SSVEP 刺激范式与频率识别 |
| **核心概念** | SSVEP 原理、FFT 频率检测、分类器基础 |
| **关键技术** | 视觉刺激生成、实时频率分析、决策逻辑 |
| **实践任务** | 构建 2-class SSVEP BCI 系统 |

---

## 参考资源

### 官方文档

- [OpenBCI Cyton 官方文档](https://docs.openbci.com/Cyton/CytonLanding/)
- [Cyton SDK 命令协议](https://docs.openbci.com/Cyton/CytonSDK/)
- [Cyton 数据格式规范](https://docs.openbci.com/Cyton/CytonDataFormat/)
- [Cyton 编程教程](https://docs.openbci.com/Cyton/CytonProgram/)

### GitHub 仓库

- [OpenBCI_Python](https://github.com/OpenBCI/OpenBCI_Python) - 官方 Python 驱动
- [pyOpenBCI](https://github.com/OpenBCI/pyOpenBCI) - 简化版 Python 库
- [BrainFlow](https://github.com/brainflow-dev/brainflow) - 现代统一数据驱动
- [openbci-stream](https://github.com/alejandroVae/openbci-stream) - 高级流处理库

### 补充学习

- [MNE-Python 教程](https://mne.tools/stable/auto_tutorials/index.html)
- [SciPy 信号处理文档](https://docs.scipy.org/doc/scipy/reference/signal.html)
- [Neuroscience Online](https://nba.uth.tmc.edu/neuroscience/)
