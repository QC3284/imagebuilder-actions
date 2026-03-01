# 🔧 ImmortalWrt AutoBuilder

[![Build Status](https://github.com/QC3284/imagebuilder-actions/workflows/%F0%9F%94%A7%20ImmortalWrt%20AutoBuilder/badge.svg)](https://github.com/QCC3284/imagebuilder-actions/actions)

## 🇨🇳 中文说明

### 简介
ImmortalWrt AutoBuilder 是一个自动化的固件构建系统，用于定期编译和发布 ImmortalWrt 固件。该系统通过 GitHub Actions 实现自动化构建流程，支持多种设备并提供自定义软件包功能。

### 🚀 特性
- **定时构建**: 每周一和周三中午12点自动运行（北京时间）
- **多设备支持**: 通过配置文件管理多个设备型号
- **自定义软件包**: 支持为特定设备添加自定义 IPK 软件包
- **智能下载**: 使用 axel 多线程加速下载 ImageBuilder
- **文件验证**: 自动生成 SHA256、SHA512、MD5 等多种哈希校验
- **日期标记**: 在固件文件名中加入构建日期戳
- **自动发布**: 构建完成后自动发布到 GitHub Releases

### 📁 目录结构
```
├── config/
│   ├── devices.json          # 设备列表配置
│   ├── mirrors_list.txt      # 镜像源列表
│   ├── use_mirrors.json      # 镜像使用配置文件
│   ├── version.json          # 版本信息配置  
│   ├── device_targets.json   # 设备目标映射
│   └── packages_*.txt        # 设备专用软件包列表
├── zdy_pkg/                  # 自定义软件包目录
├── zdy_fk_pkg/               # 分控自定义软件包目录
└── zdy_rjb.txt               # 主自定义包设备列表
└── zdy_rjb_fk.txt            # 分控自定义包设备列表
```

### ⚙️ 配置文件说明

#### `config/mirrors_list.txt`
```text
https://mirrors.xxx.cn/immortalwrt
https://mirrors.xxx.net/immortalwrt
...
```

#### `config/use_mirrors.json`
```json
{
  "use_mirrors": false,
  "timeout_seconds": 30,
  "connection_timeout": 15
}
```

#### `config/devices.json`
```json
{
  "devices": [
    "device1",
    "device2",
    "device3"
  ]
}
```

#### `config/version.json`
```json
{
  "version": "24.10.5"
}
```

#### `config/device_targets.json`
```json
{
  "device1": "target/subtarget",
  "device2": "target2/subtarget2"
}
```

#### `config/packages_*.txt`
每行一个软件包名称，支持注释（#开头）

### 📦 自定义软件包
- **主自定义包**: 将 IPK 文件放入 `zdy_pkg/` 目录，设备列表写入 `zdy_rjb.txt`
- **分控自定义包**: 将 IPK 文件放入 `zdy_fk_pkg/` 目录，设备列表写入 `zdy_rjb_fk.txt`
- 只有在对应列表中的设备才会应用相应的自定义软件包

### 🔄 触发方式
- **定时触发**: 每周一和周三 UTC 时间凌晨4点（北京时间中午12点）
- **手动触发**: 通过 GitHub Actions 手动运行
- **推送触发**: 修改 `config/` 或 `zdy_pkg/` 目录下的文件时

---

## 🇺🇸 English Documentation

### Introduction
ImmortalWrt AutoBuilder is an automated firmware building system designed to regularly compile and publish ImmortalWrt firmware. The system implements automated build processes through GitHub Actions, supporting multiple devices with custom package functionality.

### 🚀 Features
- **Scheduled Builds**: Automatically runs every Monday and Wednesday at 12:00 PM Beijing Time (4:00 AM UTC)
- **Multi-device Support**: Manages multiple device models through configuration files
- **Custom Packages**: Supports adding custom IPK packages for specific devices
- **Smart Downloads**: Uses axel multi-threaded acceleration for ImageBuilder downloads
- **File Verification**: Automatically generates SHA256, SHA512, MD5 and other hash checks
- **Date Tagging**: Includes build timestamp in firmware filenames
- **Auto Publishing**: Automatically publishes to GitHub Releases after build completion

### 📁 Directory Structure
```
├── config/
│   ├── devices.json          # Device list configuration
│   ├── version.json          # Version information configuration
│   ├── device_targets.json   # Device target mapping
│   └── packages_*.txt        # Device-specific package lists
├── zdy_pkg/                  # Custom packages directory
├── zdy_fk_pkg/               # Sub-control custom packages directory
└── zdy_rjb.txt               # Main custom package device list
└── zdy_rjb_fk.txt            # Sub-control custom package device list
```

### ⚙️ Configuration Files

#### `config/devices.json`
```json
{
  "devices": [
    "device1",
    "device2", 
    "device3"
  ]
}
```

#### `config/version.json`
```json
{
  "version": "24.10.4"
}
```

#### `config/device_targets.json`
```json
{
  "device1": "target/subtarget",
  "device2": "target2/subtarget2"
}
```

#### `config/packages_*.txt`
One package name per line, supports comments (starting with #)

### 📦 Custom Packages
- **Main Custom Packages**: Place IPK files in `zdy_pkg/` directory, add device list to `zdy_rjb.txt`
- **Sub-control Custom Packages**: Place IPK files in `zdy_fk_pkg/` directory, add device list to `zdy_rjb_fk.txt`
- Only devices in the corresponding list will apply the respective custom packages

### 🔄 Trigger Methods
- **Scheduled**: Every Monday and Wednesday at 4:00 AM UTC (12:00 PM Beijing Time)
- **Manual**: Run manually through GitHub Actions
- **Push**: When modifying files under `config/` or `zdy_pkg/` directories

### 🛠️ Requirements
- GitHub repository with proper configuration files
- GitHub Secrets configured (if needed for publishing)
- Access to ImmortalWrt release servers
