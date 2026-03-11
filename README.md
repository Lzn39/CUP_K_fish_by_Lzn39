# 自动杀鱼机控制系统

基于Qt6的自动杀鱼机控制系统，集成计算机视觉和硬件模拟功能。

## 项目概述

这是一个工业控制应用程序，用于自动杀鱼机的控制，具有以下功能：
- 使用计算机视觉检测鱼头方向
- 根据需要自动旋转鱼体
- 控制皮带输送机进行运输
- 提供触摸友好的GUI界面
- 记录所有处理数据

**当前状态：** Phase 1 - 基础架构 ✅

## 功能特性（Phase 1）

- ✅ **硬件抽象层**：基于接口的清晰设计，便于硬件切换
- ✅ **模拟硬件仿真**：无需物理硬件的完整软件模拟
  - 模拟相机（支持测试图片生成）
  - 模拟转盘（平滑的10秒旋转动画）
  - 模拟皮带输送机（基于位置的移动）
  - 模拟串口（命令/响应仿真）
- ✅ **触摸优化界面**：QML界面，最小触摸目标60px
- ✅ **MVVM架构**：清晰的关注点分离
- ✅ **硬件可视化**：转盘和皮带的实时动画显示

## 技术栈

- **Qt版本**：Qt6 (6.8+)
- **平台**：Windows
- **UI框架**：QML/Qt Quick
- **计算机视觉**：OpenCV 4.8+（Phase 2+）
- **构建系统**：CMake 3.21+
- **架构模式**：MVVM

## 项目结构

```
d:/code_src/For_cup_fish_by_Lzn39/
├── CMakeLists.txt                 # 构建配置
├── BUILD.md                       # 详细构建说明
├── src/                           # C++ 源代码
│   ├── main.cpp                   # 应用程序入口
│   ├── hardware/                  # 硬件抽象层
│   │   ├── interfaces/            # 硬件接口（ICamera, ITurntable等）
│   │   ├── mock/                  # 模拟硬件实现
│   │   └── HardwareFactory.h/cpp  # 硬件工厂
│   ├── viewmodels/                # MVVM视图模型
│   │   ├── HardwareViewModel.h/cpp
│   │   ├── MainViewModel.h/cpp
│   │   ├── CameraViewModel.h/cpp
│   │   ├── DataLogViewModel.h/cpp
│   │   └── SettingsViewModel.h/cpp
│   ├── services/                  # 业务逻辑服务
│   │   ├── DetectionEngine.h/cpp
│   │   ├── ProcessingController.h/cpp
│   │   ├── DataLogger.h/cpp
│   │   └── ConfigManager.h/cpp
│   ├── models/                    # 数据模型
│   │   ├── ProcessingSession.h/cpp
│   │   ├── SystemConfig.h/cpp
│   │   └── DetectionResult.h/cpp
│   ├── vision/                    # 计算机视觉
│   │   ├── ImagePreprocessor.h/cpp
│   │   ├── TraditionalDetector.h/cpp
│   │   └── DetectorFactory.h/cpp
│   └── utils/                     # 工具类
│       ├── Constants.h
│       ├── Logger.h/cpp
│       └── ImageConverter.h/cpp
├── qml/                           # QML UI文件
│   ├── main.qml
│   ├── screens/                   # 界面屏幕
│   │   ├── MainScreen.qml
│   │   ├── ManualControlScreen.qml
│   │   ├── DataLogScreen.qml
│   │   ├── SettingsScreen.qml
│   │   └── CalibrationScreen.qml
│   ├── components/                # UI组件
│   │   ├── TouchButton.qml
│   │   ├── StatusIndicator.qml
│   │   ├── CameraPreview.qml
│   │   ├── HardwareVisualization.qml
│   │   └── DetectionOverlay.qml
│   └── styles/                    # 样式主题
│       ├── Theme.qml
│       ├── TouchStyles.qml
│       └── qmldir
├── resources/                     # 资源文件
│   ├── resources.qrc
│   ├── icons/
│   └── test-images/               # 测试用鱼图片
│       ├── head-left/
│       └── head-right/
├── database/                      # 数据库
│   └── schema.sql
└── tests/                         # 测试
    ├── unit/
    └── integration/
```

## 环境要求

### Windows

1. **Qt 6.8+**：从[Qt官网](https://www.qt.io/download)下载
   - 选择组件：Qt6 MSVC、Qt Quick、Qt Multimedia、Qt SerialPort、Qt SQL、Qt Concurrent

2. **OpenCV 4.8+**（Phase 2+需要）：通过vcpkg安装
   ```bash
   vcpkg install opencv4[core,highgui,videoio,imgproc]:x64-windows
   ```

3. **CMake 3.21+**：从[CMake官网](https://cmake.org/download/)下载

4. **Visual Studio 2019+**：需要C++桌面开发工作负载

## 构建方��

### 方法1：使用CMake命令行

```bash
cd d:/code_src/For_cup_fish_by_Lzn39
mkdir build
cd build

# 配置（根据你的Qt安装路径调整CMAKE_PREFIX_PATH）
cmake .. -DCMAKE_PREFIX_PATH="C:/Qt/6.8.0/msvc2019_64" -DCMAKE_TOOLCHAIN_FILE="C:/vcpkg/scripts/buildsystems/vcpkg.cmake"

# 构建
cmake --build . --config Release

# 运行
./Release/FishProcessingControl.exe
```

### 方法2：使用Qt Creator（推荐）

1. 在Qt Creator中打开 `CMakeLists.txt`
2. 使用Qt6工具包配置项目
3. 点击构建并运行

### 方法3：使用Visual Studio

1. 在Visual Studio中打开 `CMakeLists.txt`
2. 配置CMake设置（指定Qt路径）
3. 构建并运行

详细构建说明请参考 [BUILD.md](BUILD.md)。

## 使用说明

### Phase 1 功能

1. **启动相机**：点击"启动相机"按钮开始模拟相机
2. **旋转转盘**：点击"旋转转盘"测试10秒旋转动画
3. **启动皮带**：点击"启动皮带 →"测试输送机移动
4. **停止皮带**：点击"停止皮带"停止输送机

### 硬件可视化

主界面实时显示：
- 转盘旋转角度（0-360°）
- 皮带输送机位置和运动状态
- 红色三角标记指示目标方向

## 开发路线图

### ✅ Phase 1：基础架构（已完成）
- 项目搭建和构建系统
- 硬件抽象层
- 模拟硬件实现
- 基础QML界面
- 硬件可视化

### ⏳ Phase 2：相机集成与检测（进行中）
- 真实/模拟USB相机集成
- 鱼图片叠加合成
- 基于OpenCV的鱼头检测（PCA/椭圆拟合）
- 检测结果可视化

### ⏳ Phase 3：处理逻辑
- 自动模式工作流实现
- 手动模式用户控制
- 皮带方向反转逻辑
- 错误处理

### ⏳ Phase 4：数据日志
- SQLite数据库集成
- 会话记录
- 统计计算
- CSV导出

### ⏳ Phase 5：设置与校准
- 配置管理
- 相机校准
- 检测参数调优

### ⏳ Phase 6：优化与测试
- Bug修复
- 性能优化
- 用户测试
- 文档完善

## 架构亮点

### 硬件抽象

```cpp
// 基于接口的清晰设计
ICamera* camera = factory.createCamera();
ITurntable* turntable = factory.createTurntable(serialDevice);
IConveyor* conveyor = factory.createConveyor(serialDevice);

// 轻松切换模拟 ↔ 真实硬件
HardwareFactory factory(simulationMode = true);  // 模拟
HardwareFactory factory(simulationMode = false); // 真实（未来）
```

### MVVM模式

```
QML视图层 (MainScreen.qml)
    ↕ 属性绑定
HardwareViewModel
    ↕ 方法调用
服务层 & 硬件层
```

### 关键接口

**ITurntable（转盘接口）：**
```cpp
class ITurntable : public QObject {
    Q_OBJECT
public:
    virtual void rotate180() = 0;           // 旋转180度
    virtual bool isRotating() const = 0;
    virtual int currentAngle() const = 0;   // 0-360度

signals:
    void rotationStarted();
    void rotationProgress(int angle);
    void rotationCompleted();
};
```

**IConveyor（皮带接口）：**
```cpp
enum class ConveyorDirection { Forward, Reverse };

class IConveyor : public QObject {
    Q_OBJECT
public:
    virtual void start(ConveyorDirection dir) = 0;
    virtual void stop() = 0;
    virtual ConveyorDirection direction() const = 0;

signals:
    void started(ConveyorDirection dir);
    void stopped();
};
```

## 核心文件

- [CMakeLists.txt](CMakeLists.txt)：构建配置
- [src/main.cpp](src/main.cpp)：应用程序入口（依赖注入）
- [src/hardware/HardwareFactory.cpp](src/hardware/HardwareFactory.cpp)：硬件工厂
- [src/viewmodels/HardwareViewModel.cpp](src/viewmodels/HardwareViewModel.cpp)：主视图模型
- [qml/screens/MainScreen.qml](qml/screens/MainScreen.qml)：主界面

## 核心功能说明

### 自动模式处理流程

```
1. 从相机捕获画面
2. 检测鱼头方向
3. 评估鱼头是否朝向红色三角标记

   如果鱼头朝向标记：
      → 直接启动皮带正向输送
      → 记录操作：action="direct_transport", belt_direction="forward"

   如果鱼头背向标记：
      → 调用转盘旋转180度
      → 等待旋转完成
      → 启动皮带反向输送（⚠️ 注意方向反转）
      → 记录操作：action="rotate_then_transport", belt_direction="reverse"

4. 记录到SQLite数据库
5. 更新界面显示
```

**关键点：** 旋转180度后，皮带输送方向必须反转！

### 手动模式处理流程

```
1. 持续检测鱼头方向并显示（不触发自动操作）
2. 用户可见：
   - 检测到的方向角度
   - 置信度百分比
   - 视觉箭头指示

3. 用户手动控制：
   - 按钮：旋转180度
   - 按钮：启动皮带（用户选择方向）
   - 按钮：停止皮带

4. 安全联锁：
   - 皮带运行时不能旋转转盘
   - 转盘旋转时不能启动皮带
```

## 数据库架构

**processing_sessions表：**
```sql
CREATE TABLE processing_sessions (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    session_uuid TEXT UNIQUE NOT NULL,
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    -- 检测结果
    detected_angle REAL NOT NULL,          -- 0-360度
    detection_confidence REAL NOT NULL,     -- 0.0-1.0
    head_direction TEXT NOT NULL,           -- "left", "right"等

    -- 执行操作
    mode TEXT NOT NULL,                     -- "auto" / "manual"
    action_type TEXT NOT NULL,              -- "direct_transport" / "rotate_then_transport"
    rotation_performed BOOLEAN NOT NULL,
    belt_direction TEXT NOT NULL,           -- "forward" / "reverse"

    -- 性能指标
    processing_time_ms INTEGER NOT NULL,

    -- 可选数据
    image_path TEXT,
    notes TEXT
);
```

## 贡献指南

这是一个演示项目。如需用于生产环境，请：
1. 将模拟硬件替换为真实硬件实现
2. 训练YOLO模型进行鱼头检测
3. 添加全面的错误处理
4. 实施工业环境的安全措施

## 许可证

本项目仅用于教育和演示目的。

## 联系方式

如有问题或建议，请参考项目文档或实施计划。

---

**版本**：1.0.0（Phase 1）
**最后更新**：2026-03-11
**状态**：Phase 1 完成 ✅
