# 🤖 ROS Autonomous Mobile Manipulator

> 自主导航、视觉识别与机械臂抓取系统。

<p align="left">
  <img src="https://img.shields.io/badge/ROS-Noetic-blue?logo=ros" />
  <img src="https://img.shields.io/badge/Ubuntu-20.04-orange?logo=ubuntu" />
  <img src="https://img.shields.io/badge/Python-Robotics-blue?logo=python" />
  <img src="https://img.shields.io/badge/RGB--D-Berxel-green" />
  <img src="https://img.shields.io/badge/Platform-Bobac-purple" />
</p>

## 🎬 项目演示

[![Bilibili](https://img.shields.io/badge/Bilibili-观看完整演示-00A1D6?logo=bilibili&logoColor=white)](https://www.bilibili.com/video/BV1Jgbw6LE9Y/)

▶ **演示视频：**  
https://www.bilibili.com/video/BV1Jgbw6LE9Y/

---

## 📖 项目简介

本项目为 **第二十五届全国大学生机器人大赛 ROBOTAC AIROBOTIC 创新挑战赛全国总决赛 B 平台参赛工程**。

项目基于 **TuringStack Bobac 单臂轮式机器人** 开发，围绕真实赛场中的移动操作任务，完成机器人从起始区域出发，经自主导航到达物料区，利用 RGB-D 相机识别并定位目标物料，再控制机械臂完成抓取、转运和放置的完整任务流程。

系统主要由以下部分组成：

- Bobac3 全向移动底盘
- ECO65 六自由度机械臂
- 末端夹爪
- 二维激光雷达
- Berxel iHawk100 RGB-D 相机
- ROS Noetic 导航与机器人控制系统

---

## 🗺️ 任务流程

```text
机器人启动与状态检查
        ↓
加载地图并完成定位
        ↓
自主导航至物料区域
        ↓
RGB-D 相机识别目标
        ↓
获取目标三维位置
        ↓
机械臂执行抓取
        ↓
机器人进行物料转运
        ↓
机械臂完成精准放置
        ↓
机械臂安全复位
```

---

## ✨ 核心功能

| 模块 | 功能 |
| --- | --- |
| 自主导航 | 地图定位、目标点导航、自主避障、末端精确停车 |
| 环境感知 | 激光雷达、RGB-D 相机、TF 坐标关系 |
| 视觉识别 | 黑色笔状物检测、轮廓筛选、深度获取 |
| 三维定位 | RGB + Depth + CameraInfo 获取目标相机坐标 |
| 机械臂控制 | 识别姿态、抓取、转运、放置及复位 |
| 真机适配 | ROS1 真机驱动、导航、相机和机械臂链路联调 |
| 工程保障 | 一键启动、环境恢复、参数配置与现场检查 |

---

## 🚗 自主导航

机器人使用 ROS Navigation Stack 完成现场移动任务：

```text
地图
 ↓
AMCL 定位
 ↓
move_base
 ↓
路径规划 / 动态避障
 ↓
目标点停车
```

现场通过保存地图与实际目标点坐标，完成从起始区域到物料识别区域的稳定导航。

### 现场实测

| 项目 | 结果 |
| --- | --- |
| 目标点 | x = 2.798 m，y = -1.645 m |
| 实际到达 | x = 2.776 m，y = -1.640 m |
| 位置误差 | **约 2.3 cm** |
| 朝向误差 | **约 0.9°** |
| 单次导航耗时 | **约 35 s** |

---

## 👁️ RGB-D 视觉识别

视觉模块使用 **Berxel iHawk100 RGB-D 相机** 获取彩色图像、深度图和相机参数。

目标识别流程：

```text
RGB 图像
   ↓
ROI 区域筛选
   ↓
颜色 / 轮廓 / 长宽比判断
   ↓
获取目标中心像素
   ↓
读取 Depth 深度
   ↓
计算三维位置
```

针对比赛中的笔状物目标，通过颜色、形状和几何特征联合筛选，降低复杂背景下的误识别。

识别成功后输出：

- 目标检测框
- 图像中心坐标
- 深度距离
- 相机坐标系下三维位置

---

## 🦾 机械臂抓取

机械臂任务流程：

```text
机器人停车
   ↓
机械臂进入识别姿态
   ↓
RGB-D 获取目标位置
   ↓
计算抓取位置
   ↓
机械臂移动至目标
   ↓
夹爪闭合
   ↓
抓取并转运
   ↓
目标区域放置
   ↓
机械臂复位
```

项目同时进行了 Isaac Sim / ROS 2 仿真验证，用于提前验证任务状态机、机械臂运动规划和抓取流程。

最终真机执行则保留基于 **ROS1 官方接口 + 固定识别姿态 + 实测参数** 的稳定运行方案，降低现场环境差异带来的风险。

---

## 🔄 仿真到真机

项目采用“仿真验证 + 真机落地”的开发方式：

```text
Isaac Sim 仿真
      ↓
任务逻辑验证
      ↓
导航 / 抓取流程调试
      ↓
ROS1 真机接口适配
      ↓
现场地图重建
      ↓
目标点重新标定
      ↓
RGB-D 相机实测
      ↓
Bobac 真机运行
```

仿真侧主要用于算法与流程验证，最终比赛任务运行于真实 Bobac 平台。

---

## 🛠 技术栈

### 机器人系统

`Ubuntu 20.04` · `ROS Noetic` · `TF` · `RViz`

### 导航

`AMCL` · `move_base` · `map_server` · `Cartographer`

### 视觉

`Berxel RGB-D` · `OpenCV` · `Depth Image` · `CameraInfo`

### 机械臂

`ECO65` · `rm_driver` · `ROS Control`

### 仿真

`NVIDIA Isaac Sim` · `ROS 2` · `cuRobo`

### 开发

`Python` · `Bash` · `YAML` · `ROS launch`

---

## 📁 项目结构

```text
robotac-airobotic-bobac-2026/
│
├── scripts/
│   ├── 现场导航与识别执行脚本
│   ├── 真机环境恢复与启动脚本
│   ├── legacy_20260827/
│   │   └── 早期现场迭代版本
│   └── sim_tools/
│       └── Isaac Sim / ROS 2 仿真辅助工具
│
├── configs/
│   ├── 真机参数配置
│   ├── 机械臂与抓取参数
│   └── RViz 配置
│
├── maps/
│   └── site_0827_new
│       └── 现场导航地图
│
├── docs/
│   ├── 现场执行说明
│   ├── 开发与验证记录
│   └── 运行流程文档
│
├── third_party/
│   └── official_demo/
│       └── 官方示例代码索引
│
├── .gitignore
└── README.md
```

---

## 🚀 快速开始

### 1. 配置现场环境

```bash
cp scripts/env.example.sh scripts/env.local.sh
vi scripts/env.local.sh

source scripts/env.local.sh
```

### 2. 恢复项目文件

```bash
bash scripts/00_restore_to_robot.sh
```

### 3. 启动导航与 RViz

```bash
bash scripts/01_start_nav_terminal1.sh
```

确认：

- 地图正常加载
- 激光雷达与地图边界基本重合
- AMCL 定位正常
- TF 链路正常

### 4. 导航并启动视觉识别

```bash
bash scripts/02_go_detect_then_recognition_terminal2.sh
```

正常情况下可看到：

```text
NAV_OK=True
CAMERA_TOPICS_OK=True
PEN_DETECT_OK=True
```

以及目标对应的三维相机坐标。

---

## 🏆 项目成果

完成 Bobac 单臂机器人从：

```text
环境感知
   ↓
自主定位
   ↓
导航避障
   ↓
目标识别
   ↓
三维定位
   ↓
机械臂抓取
   ↓
物料转运
   ↓
精准放置
```

的完整移动操作任务链。

项目完成了从仿真方案验证到 Bobac 真机部署的迁移，并针对现场地图、导航目标点、RGB-D 相机、机械臂及 ROS 通信链路进行了实际调试。

---

## 📌 第三方与官方资料

`third_party/official_demo/` 中仅保留比赛平台官方示例的必要参考内容。

相关官方代码、配置、机器人驱动及文档版权归原作者或设备厂商所有，不计入本项目原创代码。

本仓库主要展示参赛队伍自行开发的：

- 任务执行脚本
- 真机适配配置
- 现场地图
- 视觉识别程序
- 仿真工具
- 调试与运行流程

---

## 📄 说明

本仓库用于 **ROBOTAC AIROBOTIC Bobac B 平台项目展示、学习与技术交流**。

部分设备驱动、官方依赖、大体积仿真资源及比赛现场资料未包含在仓库中。
