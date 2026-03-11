# 构建说明 (BUILD.md)

## Phase 1 构建指南

本文档提供详细的构建步骤，帮助您在Windows上成功编译和运行Fish Processing Control System。

## 前置条件

### 1. Qt 6.8+ 安装

**下载地址**: https://www.qt.io/download-qt-installer

**安装步骤**:
1. 运行Qt在线安装器
2. 选择Qt 6.8.0（或更高版本）
3. 必选组件：
   - ✅ MSVC 2019 64-bit
   - ✅ Qt Quick
   - ✅ Qt Multimedia
   - ✅ Qt SerialPort
   - ✅ Qt SQL
   - ✅ Qt Concurrent
4. 安装路径示例：`C:\Qt\6.8.0\msvc2019_64`

### 2. CMake 3.21+

**下载地址**: https://cmake.org/download/

- 下载Windows x64 Installer
- 安装时选择"Add CMake to system PATH"

### 3. Visual Studio 2019+

**下载地址**: https://visualstudio.microsoft.com/downloads/

**必需工作负载**:
- ✅ Desktop development with C++

### 4. OpenCV（可选 - Phase 2+才需要）

Phase 1不需要OpenCV，可以跳过此步骤。

## 构建步骤

### 方法1：使用命令行（推荐）

```bash
# 1. 打开"x64 Native Tools Command Prompt for VS 2019"
#    (在开始菜单中搜索)

# 2. 进入项目目录
cd d:\code_src\For_cup_fish_by_Lzn39

# 3. 创建构建目录
mkdir build
cd build

# 4. 配置CMake（替���Qt路径为您的实际安装路径）
cmake .. -G "Visual Studio 16 2019" -A x64 ^
    -DCMAKE_PREFIX_PATH="C:/Qt/6.8.0/msvc2019_64"

# 5. 构建项目
cmake --build . --config Release

# 6. 运行程序
cd Release
FishProcessingControl.exe
```

### 方法2：使用Qt Creator

1. 启动Qt Creator
2. 打开项目：`File` → `Open File or Project`
3. 选择 `d:\code_src\For_cup_fish_by_Lzn39\CMakeLists.txt`
4. 配置Kit：选择"Desktop Qt 6.8.0 MSVC2019 64bit"
5. 点击左下角绿色三角按钮运行

### 方法3：使用Visual Studio 2019+

1. 启动Visual Studio
2. `File` → `Open` → `CMake...`
3. 选择 `d:\code_src\For_cup_fish_by_Lzn39\CMakeLists.txt`
4. 等待CMake配置完成
5. 在`CMakeSettings.json`中设置Qt路径：
   ```json
   {
     "configurations": [{
       "name": "x64-Release",
       "generator": "Ninja",
       "configurationType": "Release",
       "cmakeCommandArgs": "-DCMAKE_PREFIX_PATH=C:/Qt/6.8.0/msvc2019_64"
     }]
   }
   ```
6. `Build` → `Build All`
7. `Debug` → `Start Without Debugging`

## 常见问题排查

### 问题1：找不到Qt6

**错误信息**:
```
CMake Error at CMakeLists.txt:13 (find_package):
  Could not find a package configuration file provided by "Qt6"
```

**解决方案**:
- 确认Qt已正确安装
- 检查CMAKE_PREFIX_PATH是否指向正确的Qt安装目录
- 示例：`-DCMAKE_PREFIX_PATH="C:/Qt/6.8.0/msvc2019_64"`

### 问题2：找不到MSVC编译器

**错误信息**:
```
No CMAKE_CXX_COMPILER could be found
```

**解决方案**:
- 使用"x64 Native Tools Command Prompt for VS 2019"而不是普通cmd
- 或在Visual Studio中打开CMake项目

### 问题3：QML文件加载失败

**错误信息**:
```
qrc:/qml/main.qml: File not found
```

**解决方案**:
- 确认resources.qrc已正确配置
- 重新构建项目：`cmake --build . --config Release --clean-first`

### 问题4：OpenCV相关错误

**错误信息**:
```
Could not find a package configuration file provided by "OpenCV"
```

**解决方案**:
- Phase 1不需要OpenCV，此警告可以忽略
- CMakeLists.txt已配置为可选依赖
- 如果要安装OpenCV（Phase 2+）：
  ```bash
  vcpkg install opencv4[core,highgui,videoio,imgproc]:x64-windows
  cmake .. -DCMAKE_TOOLCHAIN_FILE=C:/vcpkg/scripts/buildsystems/vcpkg.cmake
  ```

### 问题5：运行时找不到Qt DLL

**错误信息**:
```
The code execution cannot proceed because Qt6Core.dll was not found
```

**解决方案**:

**方法A：使用windeployqt（推荐）**
```bash
cd build\Release
C:\Qt\6.8.0\msvc2019_64\bin\windeployqt.exe FishProcessingControl.exe --qmldir ..\..\qml
```

**方法B：添加Qt到PATH**
```bash
set PATH=C:\Qt\6.8.0\msvc2019_64\bin;%PATH%
FishProcessingControl.exe
```

## 验证构建成功

运行程序后，您应该看到：

1. ✅ 1024x768窗口打开
2. ✅ 顶栏显示"Fish Processing Control"
3. ✅ 左侧相机预览区域（带红色三角标记）
4. ✅ 右侧硬件可视化区域
5. ✅ 底部导航栏

**测试功能**:
- 点击"START CAMERA"按钮 → 状态指示灯变绿
- 点击"ROTATE TURNTABLE"按钮 → 转盘开始10秒旋转动画
- 点击"START BELT →"按钮 → 皮带开始移动动画

## 构建输出

成功构建后的文件结构：

```
build/
├── Release/
│   ├── FishProcessingControl.exe    # 主程序
│   ├── Qt6Core.dll                  # Qt运行时库（windeployqt后）
│   ├── Qt6Gui.dll
│   ├── Qt6Qml.dll
│   ├── Qt6Quick.dll
│   └── ... (其他Qt DLL)
└── test-images/                     # 测试图片（自动复制）
```

## 开发模式 vs 发布模式

### Debug模式（开发）
```bash
cmake --build . --config Debug
cd Debug
FishProcessingControl.exe
```
- 包含调试符号
- 启用断言
- 性能较慢
- 日志更详细

### Release模式（发布）
```bash
cmake --build . --config Release
cd Release
FishProcessingControl.exe
```
- 优化性能
- 体积更小
- 适合最终用户

## 下一步

构建成功后，您可以：

1. **测试Phase 1功能**：
   - 硬件模拟动画
   - 触摸按钮交互
   - 状态指示灯

2. **准备Phase 2开发**：
   - 安装OpenCV
   - 准备测试鱼图片
   - 阅读Phase 2实施计划

3. **自定义开发**：
   - 修改QML界面
   - 添加新的硬件模拟
   - 扩展功能

## 技术支持

如遇到其他问题，请检查：
- [README.md](README.md) - 项目概述
- [实施计划](C:\Users\Lzn39\.claude\plans\zesty-sparking-lovelace.md) - 详细架构设计
- Qt官方文档：https://doc.qt.io/qt-6/
- CMake文档：https://cmake.org/documentation/

---

**最后更新**: 2026-03-11
**适用版本**: Phase 1
