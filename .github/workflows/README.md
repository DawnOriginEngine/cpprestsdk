# GitHub Actions 工作流说明

本目录包含了为 cpprestsdk 项目配置的 GitHub Actions 工作流文件，支持多平台多架构的自动化构建。

## 工作流文件

### 1. build.yml - 持续集成构建

**触发条件：**
- 推送到 master/main/develop 分支
- 针对 master/main/develop 分支的 Pull Request
- 手动触发

**支持的平台和架构：**
- **Windows**
  - x64 (Debug/Release)
  - ARM64 (Debug/Release)
- **Linux**
  - x64 (Debug/Release)
  - ARM64 (Debug/Release)
- **Android**
  - x64 (Debug/Release)
  - ARM64 (Debug/Release)

**特性：**
- 自动运行测试（Windows 和 Linux x64）
- 使用 vcpkg 管理依赖
- 支持 Brotli 压缩（除 Android 外）
- 交叉编译支持

### 2. release.yml - 发布构建

**触发条件：**
- GitHub Release 发布时
- 手动触发（可指定版本号）

**输出产物：**
- Windows: `cpprestsdk-{version}-windows-{arch}.zip`
- Linux: `cpprestsdk-{version}-linux-{arch}.tar.gz`
- Android: `cpprestsdk-{version}-android-{arch}.tar.gz`

**特性：**
- 仅构建 Release 版本
- 禁用测试和示例以加快构建
- 自动打包构建产物
- 上传为 GitHub Artifacts

## 依赖管理

项目使用 vcpkg 管理以下依赖：
- zlib
- openssl
- boost (locale, system, date-time, regex, thread, filesystem, random, chrono, interprocess)
- websocketpp
- brotli（Android 除外）

## 构建要求

### Windows
- Visual Studio 2019 或更新版本
- CMake 3.9+
- vcpkg

### Linux
- GCC 或 Clang
- CMake 3.9+
- Ninja 构建系统
- pkg-config
- 交叉编译工具链（ARM64）

### Android
- Android NDK r25c
- CMake 3.9+
- Ninja 构建系统
- 最低 API 级别：21

## 使用说明

### 自动构建
工作流会在代码推送或 PR 时自动触发，无需手动干预。

### 手动触发构建
1. 进入 GitHub 仓库的 Actions 页面
2. 选择相应的工作流
3. 点击 "Run workflow" 按钮
4. 对于发布构建，可以指定版本号

### 下载构建产物
1. 进入 Actions 页面
2. 选择相应的工作流运行
3. 在 Artifacts 部分下载所需的构建产物

## 自定义配置

### 修改支持的平台
编辑 `build.yml` 或 `release.yml` 文件中的 `strategy.matrix` 部分。

### 修改依赖
更新工作流文件中的 vcpkg 安装命令。

### 修改构建选项
调整 CMake 配置参数，例如：
- `-DCPPREST_EXCLUDE_BROTLI=ON/OFF`
- `-DBUILD_TESTS=ON/OFF`
- `-DBUILD_SAMPLES=ON/OFF`

## 故障排除

### 常见问题

1. **vcpkg 依赖安装失败**
   - 检查网络连接
   - 验证 triplet 名称是否正确
   - 查看是否有依赖冲突

2. **交叉编译失败**
   - 确认交叉编译工具链已正确安装
   - 检查 CMake 工具链文件路径
   - 验证目标架构设置

3. **Android 构建失败**
   - 确认 NDK 版本兼容性
   - 检查 Android API 级别设置
   - 验证 ABI 配置

### 调试技巧

1. **启用详细日志**
   在 CMake 配置中添加 `--verbose` 或 `-DCMAKE_VERBOSE_MAKEFILE=ON`

2. **检查构建环境**
   在工作流中添加步骤打印环境变量和工具版本

3. **本地复现**
   使用相同的 CMake 命令在本地环境中复现问题

## 贡献

如需修改工作流配置，请：
1. Fork 仓库
2. 创建功能分支
3. 测试修改
4. 提交 Pull Request

确保修改后的工作流能够成功运行所有目标平台的构建。