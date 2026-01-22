# ImmortalWrt AutoBuilder

[English](#english) | [简体中文](#简体中文)

---

## English

### Overview

This repository is an automated system for building highly customized ImmortalWrt firmwares. It leverages GitHub Actions to automatically compile, package, and release firmwares for a variety of router devices based on user-defined configurations.

### Features

1.  **Automated Build Process:** The entire firmware building process is fully automated, from downloading the ImmortalWrt ImageBuilder to packaging and releasing, requiring no manual intervention.
2.  **Multi-Device Support:** Manages a list of supported devices through a simple JSON file (`config/devices.json`), allowing for parallel builds for multiple different routers.
3.  **Version Control:** Precisely controls the ImmortalWrt version to be used via the `config/version.json` file, ensuring build consistency.
4.  **Rich Package Integration:**
    *   **Base Packages:** Automatically includes essential components like the LuCI web interface.
    *   **Custom Package Lists:** Allows for creating specific package lists for each device or group of devices (`config/packages_*.txt`) for high-level customization.
    *   **Pre-built Package Injection:** Supports injecting third-party `.ipk` packages (stored in `zdy_pkg` and `zdy_fk_pkg`) directly into the firmware, greatly extending functionality.
5.  **Post-Build Processing:**
    *   **Automatic Naming:** The generated firmwares are automatically renamed based on version, device, and build date for easy archiving and identification.
    *   **Hash Verification:** Automatically generates multiple hash values (SHA256, MD5, etc.) for all firmware files and provides verification files to ensure firmware integrity and security.
6.  **Automatic Releases:** Each successful build automatically creates a new GitHub Release, publishing the firmware, hash files, and detailed build information for easy user access.
7.  **Flexible Triggering Mechanism:** Builds can be triggered manually or automatically when configuration files or custom packages are changed.

### Strengths

1.  **Highly Automated, Time-Saving:** Eliminates the tedious process of manually downloading, configuring, and compiling firmware. Get customized firmware by simply modifying a few configuration files.
2.  **Extreme Customizability:** Users have full control over the packages included in the firmware, whether from the official repositories or third-party `.ipk` files. This makes it possible to build a "full-featured" or "minimalist" firmware.
3.  **Easy to Extend and Maintain:**
    *   **Adding New Devices:** Simply add the new device to `config/devices.json` and the `target` mapping in the workflow.
    *   **Updating Packages:** Just modify the corresponding `packages_*.txt` file or replace the `.ipk` files in `zdy_pkg`.
    *   **Upgrading Versions:** Simply update the version number in `config/version.json`.
4.  **Robust and Reliable Build Process:** The workflow includes error handling, download retries, and fallback download options, increasing the success rate of builds.
5.  **Standardized Output for Traceability:** The auto-generated Releases contain detailed build information, dates, and hash values, providing a clear record for each firmware version for version control and troubleshooting.
6.  **Friendly to Chinese Users:** The selection of packages (like `luci-i18n-*-zh-cn`) shows that this project gives special consideration to the user experience of Chinese users.

### Weaknesses

1.  **Dependency on Pre-built Third-Party Packages:** The project heavily relies on pre-built `.ipk` files in `zdy_pkg`. This introduces several potential issues:
    *   **Security Risks:** The origin and security of these binary files are unknown. If they contain malicious code, it will be directly integrated into the firmware.
    *   **Update Lag:** Updates to these packages depend on the repository maintainer manually replacing the files, and may not keep up with the latest upstream versions and security patches.
    *   **Architectural Limitations:** Some packages in `zdy_pkg` (like `fastnet_*.ipk`) are compiled for a specific CPU architecture (`aarch64_cortex-a53`). If devices with different architectures (e.g., `mips`) are added in the future, these packages will be incompatible and will require finding or compiling corresponding versions.
2.  **Learning Curve for Newcomers:** Although easy to use, for users unfamiliar with OpenWrt/ImmortalWrt ImageBuilder and GitHub Actions, there may be a learning curve to understand its workings and perform deep customizations (e.g., troubleshooting build failures).
3.  **Lack of Documentation:** Before this README, the project had no documentation, making it difficult for new users to quickly understand its features and usage.
4.  **Uncertainty of `gxjc/openclash.ipk`:** This file exists in the repository, but the current build logic does not seem to use it, which may confuse users.

### Usage Tutorial

This tutorial will guide you on how to use this repository to build your own custom ImmortalWrt firmware.

#### Step 1: Fork This Repository

Click the **Fork** button in the upper right corner of this page to copy this repository to your own GitHub account. All subsequent operations will be performed in your own repository.

#### Step 2: Enable GitHub Actions

By default, GitHub Actions may be disabled for a forked repository. Go to your forked repository's page, click the **Actions** tab, and if prompted, click the **"I understand my workflows, go ahead and enable them"** button to enable it.

#### Step 3: Customize Your Firmware
####  Please note that this project supports a very limited range of devices.

All customization is done by modifying files in the `config/` directory.

1.  **Select Target Devices (`config/devices.json`)**
    *   Open the `config/devices.json` file.
    *   This is a JSON array containing the identifiers of all devices for which firmware needs to be built.
    *   You can remove devices you don't need or add new ones following the existing format.
    *   **Important:** The device identifier you add must be a `PROFILE` name supported by the ImmortalWrt ImageBuilder. You can find some example mappings in the `Determine Target Architecture` step of the workflow, or get them from the official ImmortalWrt documentation.

3.  **Specify ImmortalWrt Version (`config/version.json`)**
    *   Open the `config/version.json` file.
    *   Modify the `version` field to the ImmortalWrt version you wish to build (e.g., `"23.05.2"`).
    *   Make sure the version you enter has a corresponding ImageBuilder on the [ImmortalWrt official release page](https://immortalwrt.kyarucloud.moe/releases/).

4.  **Manage Packages (`config/packages_*.txt` and `zdy_pkg/`)**
    *   **Device-Specific Packages:**
        *   To customize packages for a specific device, create a file named `config/packages_<device_identifier>.txt` (e.g., `config/packages_glinet_gl-mt3000.txt`).
        *   In this file, list one package name you want to include per line.
        *   Lines starting with `#` will be treated as comments.
    *   **Default Packages:**
        *   If a device does not have its own specific package list file, the build system will use the contents of `config/default_packages.txt`.
    *   **Add/Remove Third-Party Pre-built Packages:**
        *   You can place your own `.ipk` packages into the `zdy_pkg` or `zdy_fk_pkg` directory.
        *   Then, edit the `zdy_rjb.txt` or `zdy_rjb_fk.txt` file to ensure your target device identifier is included. This way, these `.ipk` files will be automatically integrated during the build.

#### Step 4: Trigger a Build

You have several ways to trigger a firmware build:

1.  **Automatic Trigger (Recommended):**
    *   After you have finished modifying the `config/` or `zdy_pkg/` directories, `commit` and `push` these changes to the `main` branch of your own repository.
    *   `git add .`
    *   `git commit -m "My custom configuration"`
    *   `git push`
    *   After pushing, GitHub Actions will automatically start a new build task.

2.  **Manual Trigger:**
    *   Go to the **Actions** tab of your repository.
    *   In the list of workflows on the left, click **"🔧 ImmortalWrt AutoBuilder"**.
    *   On the right, you will see a **"Run workflow"** dropdown button. Click it, and then click the green **"Run workflow"** button again.

#### Step 5: Monitor the Build Process and Download the Firmware

1.  **Monitor Progress:**
    *   After triggering a build, you can see a running workflow in the **Actions** tab.
    *   Click on it to see that the build matrix has created a separate `build-firmware` job for each of your devices. You can view the logs for each job in real time.

2.  **Download Firmware:**
    *   When all build jobs have completed successfully, the system will automatically create a GitHub Release for each device.
    *   Go to the **Releases** page of your repository (usually on the right sidebar of the homepage).
    *   You will see new versions like `build-<run_id>-<device_name>`.
    *   Click on that version, and in the **Assets** section, you will find all the generated firmware files (`.bin`, `.img`, etc.), the `sha256sums.txt` verification file, and detailed `.hashes` files for each firmware.

---

## 简体中文

### 概述

本仓库是一个用于构建高度定制化 ImmortalWrt 固件的自动化系统。它利用 GitHub Actions，根据用户定义的配置，为一系列路由器设备自动编译、打包和发布固件。

### 功能特性

1.  **自动化构建流程：** 整个固件构建过程完全自动化，从下载 ImmortalWrt ImageBuilder 到打包和发布，无需人工干预。
2.  **多设备支持：** 通过一个简单的 JSON 文件 (`config/devices.json`) 来管理支持的设备列表，可以轻松地为多个不同的路由器并行构建固件。
3.  **版本管理：** 通过 `config/version.json` 文件精确控制所使用的 ImmortalWrt 版本，确保构建的一致性。
4.  **丰富的软件包集成：**
    *   **基础包：** 自动包含 LuCI 网页界面等基础组件。
    *   **自定义包列表：** 允许为每个设备或一组设备创建特定的软件包列表 (`config/packages_*.txt`)，从而实现高度定制。
    *   **预编译包注入：** 支持将第三方的 `.ipk` 软件包（存放于 `zdy_pkg` 和 `zdy_fk_pkg`）直接注入到固件中，极大地扩展了功能。
5.  **构建后处理：**
    *   **自动命名：** 生成的固件会根据版本、设备和构建日期自动重命名，便于归档和识别。
    *   **哈希校验：** 自动为所有固件文件生成多种哈希值（SHA256, MD5 等），并提供校验文件，确保固件的完整性和安全性。
6.  **自动发布：** 每次成功的构建都会自动创建一个 GitHub Release，并将固件、哈希文件和详细的构建信息一同发布，方便用户下载和使用。
7.  **灵活的触发机制：** 构建可以手动触发，也可以在配置文件或自定义软件包发生变化时自动触发。


### 使用教程

本教程将指导您如何使用此仓库来构建您自己的自定义 ImmortalWrt 固件。

#### 步骤 1: Fork 本仓库

点击此页面右上角的 **Fork** 按钮，将此仓库复制到您自己的 GitHub 账户下。所有的后续操作都将在您自己的仓库中进行。

#### 步骤 2: 启用 GitHub Actions

默认情况下，Fork 后的仓库的 GitHub Actions 可能是禁用的。请进入您 Fork 后的仓库页面，点击 **Actions** 标签页，如果看到提示，请点击 **"I understand my workflows, go ahead and enable them"** 按钮来启用它。

#### 步骤 3: 定制您的固件
#### 注意，该项目支持的设备非常有限
所有的定制化操作都通过修改 `config/` 目录下的文件来完成。

1.  **选择目标设备 (`config/devices.json`)**
    *   打开 `config/devices.json` 文件。
    *   这是一个 JSON 数组，包含了所有需要构建固件的设备标识符。
    *   您可以根据您的需求，删除您不需要的设备，或者参照已有格式添加新的设备。
    *   **重要:** 您添加的设备标识符必须是 ImmortalWrt ImageBuilder 支持的 `PROFILE` 名称。您可以在工作流的 `Determine Target Architecture` 步骤中找到一些示例映射，或者从 ImmortalWrt 官方获取。

3.  **指定 ImmortalWrt 版本 (`config/version.json`)**
    *   打开 `config/version.json` 文件。
    *   修改 `version` 字段为您希望构建的 ImmortalWrt 版本号（例如 `"23.05.2"`）。
    *   请确保您填写的版本在 [ImmortalWrt 官方发布页面](https://immortalwrt.kyarucloud.moe/releases/) 上存在对应的 ImageBuilder。

4.  **管理软件包 (`config/packages_*.txt` 和 `zdy_pkg/`)**
    *   **设备专属软件包:**
        *   要为特定设备定制软件包，请创建一个名为 `config/packages_<device_identifier>.txt` 的文件（例如 `config/packages_glinet_gl-mt3000.txt`）。
        *   在此文件中，每行列出一个您想要包含的软件包名称。
        *   以 `#` 开头的行将被视为注释。
    *   **默认软件包:**
        *   如果某个设备没有自己的专属软件包列表文件，构建系统将会使用 `config/default_packages.txt` 的内容。
    *   **添加/删除第三方预编译包:**
        *   您可以将您自己的 `.ipk` 软件包放入 `zdy_pkg` 或 `zdy_fk_pkg` 目录。
        *   然后，编辑 `zdy_rjb.txt` 或 `zdy_rjb_fk.txt` 文件，确保您的目标设备标识符包含在其中。这样，构建时这些 `.ipk` 文件就会被自动集成。

#### 步骤 4: 触发构建

您有多种方式来触发固件构建：

1.  **自动触发 (推荐):**
    *   在您完成对 `config/` 或 `zdy_pkg/` 目录的修改后，将这些变更 `commit` 并 `push` 到您自己仓库的 `main` 分支。
    *   `git add .`
    *   `git commit -m "My custom configuration"`
    *   `git push`
    *   推送后，GitHub Actions 会自动开始一个新的构建任务。

2.  **手动触发:**
    *   进入您仓库的 **Actions** 标签页。
    *   在左侧的 workflows 列表中，点击 **"🔧 ImmortalWrt AutoBuilder"**。
    *   在右侧，您会看到一个 **"Run workflow"** 的下拉按钮，点击它，然后再次点击绿色的 **"Run workflow"** 按钮。

#### 步骤 5: 监控构建过程并下载固件

1.  **监控进度:**
    *   触发构建后，您可以在 **Actions** 标签页看到一个正在运行的工作流。
    *   点击进入，您可以看到构建矩阵为您的每个设备创建了一个独立的 `build-firmware` 任务。您可以实时查看每个任务的日志。

2.  **下载固件:**
    *   当所有构建任务成功完成后，系统会自动为每个设备创建一个 GitHub Release。
    *   进入您仓库的 **Releases** 页面（通常在首页右侧栏）。
    *   您会看到类似 `build-<run_id>-<device_name>` 的新版本。
    *   点击该版本，在 **Assets** 部分，您就可以找到所有生成的固件文件 (`.bin`, `.img` 等)、`sha256sums.txt` 校验文件以及每个固件的详细 `.hashes` 文件。


