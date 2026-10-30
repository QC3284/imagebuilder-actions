# 🔧 ImmortalWrt AutoBuilder

[![🔧 ImmortalWrt AutoBuilder](https://github.com/QC3284/imagebuilder-actions/actions/workflows/Build-immortalwrt.yml/badge.svg)](https://github.com/QC3284/imagebuilder-actions/actions/workflows/Build-immortalwrt.yml)
[![OpenClash Update Check](https://github.com/QC3284/imagebuilder-actions/actions/workflows/Auto-update.yml/badge.svg)](https://github.com/QC3284/imagebuilder-actions/actions/workflows/Auto-update.yml)
[![Clear All Releases](https://github.com/QC3284/imagebuilder-actions/actions/workflows/CLEAR.yml/badge.svg)](https://github.com/QC3284/imagebuilder-actions/actions/workflows/CLEAR.yml)

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
├── gxjc/                     # OpenClash IPK（由 Auto-update 维护，构建流程未读取）
├── zdy_pkg/                  # 自定义软件包目录
├── zdy_fk_pkg/               # 分控自定义软件包目录
├── zdy_rjb.txt               # 主自定义包设备列表
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
设备名数组，数组里每个设备都会建一个独立的构建 job：
```json
[
  "glinet_gl-mt3000",
  "glinet_gl-mt6000",
  "xiaomi_mi-router-cr6606"
]
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
- **推送触发**: 修改 `config/`、`zdy_pkg/` 或 `zdy_fk_pkg/` 目录下的文件时

### 🤖 工作流
| 工作流 | 触发 | 作用 |
| --- | --- | --- |
| `Build-immortalwrt.yml` | 定时（周一 / 周三 04:00 UTC）、手动、`config/` `zdy_pkg/` `zdy_fk_pkg/` 变更 | 并行构建并发布全部设备固件 |
| `Auto-update.yml` | 定时（每天 03:00 / 15:00 UTC）、手动 | 检查并更新 `gxjc/openclash.ipk`，有变化时触发一次构建 |
| `CLEAR.yml` | 定时（每周日 01:00 UTC）、手动 | 删除全部 Release 与 tag，控制存储占用 |

### 🖥️ 构建环境要求
- 工作流固定 `runs-on: ubuntu-24.04`，**不要改回 `ubuntu-latest`**：2026-10-25 起 `ubuntu-latest` 指向 Ubuntu 26.04，其默认 coreutils 换成了 uutils 实现，`install --version` 不含 "GNU"，ImageBuilder 的前置检查会直接失败（曾导致全部设备构建中断）。
- 构建流程含「🧪 Verify GNU coreutils」自检步骤，运行器上的 `install` 不是 GNU 版本时会立即报错并给出修复提示。
- 第三方 Action 全部固定到 commit SHA，避免上游变动静默打断构建。
