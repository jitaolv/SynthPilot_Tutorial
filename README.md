# SynthPilot Tutorial

SynthPilot 工具集系列教程的配套资料仓库。

## 目录结构

```
├── doc/                        ← 各期教程的任务书与设计文档
│   ├── 05_多时钟域.../          ← 多时钟域数据采集系统文档
│   └── 06_xdma_fft_.../        ← XDMA FFT 频谱分析仪文档（Phase 1~7）
├── prj/                        ← 各期项目工程
│   ├── 01/                     ← LED 闪烁（Vivado 基础操作）
│   │   ├── prj/  rtl/  xdc/
│   ├── 02/                     ← I2C 收发器（仿真验证）
│   │   ├── prj/  rtl/  sim/
│   ├── 03/                     ← 多时钟域数据采集与串口输出
│   │   ├── prj/  host/
│   └── 04/                     ← XDMA FFT 音乐频谱分析仪
│       ├── rtl/                ← RTL 源码（top.v, magnitude_calc.v）
│       ├── sim/                ← 仿真文件（FFT IP / 幅度计算 / 数据通路）
│       ├── host/               ← 上位机（Flask + WebSocket 频谱可视化）
│       └── prj/                ← Vivado 工程（BD / IP 配置 / 约束）
├── src/                        ← 独立示例源码
└── sim/                        ← 独立示例仿真
```

## 教程列表

| 期号 | 主题 | 文档 |
|------|------|------|
| 01 | Vivado 基础工程创建 | [doc/05_.../01_project.md](doc/05_多时钟域数据采集与串口输出系统/01_project.md) |
| 02 | I2C 设计与仿真验证 | [doc/05_.../02_i2c_design.md](doc/05_多时钟域数据采集与串口输出系统/02_i2c_design.md) |
| 03 | 多时钟域数据采集与串口输出 | [doc/05_.../05_多时钟域...md](doc/05_多时钟域数据采集与串口输出系统/05_多时钟域数据采集与串口输出系统.md) |
| 04 | XDMA FFT 音乐频谱分析仪 | [doc/06_.../主任务书.md](doc/06_xdma_fft_音乐频谱分析仪/主任务书.md) |

## 项目 04：XDMA FFT 音乐频谱分析仪

基于 Xilinx KU3P FPGA，通过 PCIe Gen3 x4 DMA 实现硬件加速 1024 点 FFT 实时频谱分析。

### 系统架构

```
MP3/FLAC ──→ PCM ──→ PCIe DMA (H2C) ──→ FPGA FFT ──→ 幅度计算
                                                          │
浏览器 ←── WebSocket ←── Flask Server ←── PCIe DMA (C2H) ←┘
  │
  └── Canvas 对数频谱曲线 + 同步音频播放
```

### Phase 完成情况

| Phase | 内容 | 关键成果 |
|-------|------|----------|
| 1 | Block Design | XDMA + AXI-S FIFO (CDC) + IBUFDSGTE |
| 2 | FFT IP 仿真 | 1024-pt Pipelined Streaming, 13 项测试全通过 |
| 3 | 幅度计算模块 | L1 近似 \|Re\|+\|Im\|, 级联仿真验证 |
| 4 | 顶层集成 | 128→16 bit 适配, 自动 config 驱动, 数据通路仿真 |
| 5 | 综合与实现 | WNS=0.631ns, LUT 9.75%, 比特流生成 |
| 6 | 硬件验证 | PCIe 枚举, 驱动加载, DMA+FFT 端到端测试 |
| 7 | 上位机应用 | Flask+WebSocket, 对数频谱, dB 幅度, 浏览器音频同步 |

### 快速使用

```bash
# 远程机（已烧录比特流、安装驱动）
cd ~/workplace/fft
pip3 install --user -r requirements.txt
python3 fft_spectrum.py --mp3 your_music.mp3

# 本地浏览器访问
# http://<remote-ip>:8000
```

## 工具

- [SynthPilot](https://github.com/SynthPilot) — FPGA 开发 AI 辅助工具集
- Vivado 2024.2
- Python 3.6+
