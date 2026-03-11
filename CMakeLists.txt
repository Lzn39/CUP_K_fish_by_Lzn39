cmake_minimum_required(VERSION 3.21)
project(FishProcessingControl VERSION 1.0.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Qt自动化工具
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

# Qt6包
find_package(Qt6 REQUIRED COMPONENTS
    Core
    Gui
    Qml
    Quick
    QuickControls2
    Multimedia
    SerialPort
    Sql
    Concurrent
)

# OpenCV (Phase 2+, 暂时可选)
find_package(OpenCV QUIET)
if(OpenCV_FOUND)
    message(STATUS "OpenCV found: ${OpenCV_VERSION}")
    add_definitions(-DHAVE_OPENCV)
else()
    message(STATUS "OpenCV not found - Phase 1 features only")
endif()

# 源文件
set(SOURCES
    # 主程序
    src/main.cpp

    # 工具类
    src/utils/Constants.h
    src/utils/Logger.h
    src/utils/Logger.cpp
    src/utils/ImageConverter.h
    src/utils/ImageConverter.cpp

    # 硬件接口
    src/hardware/interfaces/ICamera.h
    src/hardware/interfaces/ITurntable.h
    src/hardware/interfaces/IConveyor.h
    src/hardware/interfaces/ISerialDevice.h

    # Mock硬件实现
    src/hardware/mock/MockSerialPort.h
    src/hardware/mock/MockSerialPort.cpp
    src/hardware/mock/MockTurntable.h
    src/hardware/mock/MockTurntable.cpp
    src/hardware/mock/MockConveyor.h
    src/hardware/mock/MockConveyor.cpp
    src/hardware/mock/MockCamera.h
    src/hardware/mock/MockCamera.cpp

    # 硬件工厂
    src/hardware/HardwareFactory.h
    src/hardware/HardwareFactory.cpp

    # 数据模型
    src/models/ProcessingSession.h
    src/models/ProcessingSession.cpp
    src/models/SystemConfig.h
    src/models/SystemConfig.cpp
    src/models/DetectionResult.h
    src/models/DetectionResult.cpp

    # 服务层
    src/services/ConfigManager.h
    src/services/ConfigManager.cpp
    src/services/DataLogger.h
    src/services/DataLogger.cpp
    src/services/DetectionEngine.h
    src/services/DetectionEngine.cpp
    src/services/ProcessingController.h
    src/services/ProcessingController.cpp

    # 视图模型
    src/viewmodels/MainViewModel.h
    src/viewmodels/MainViewModel.cpp
    src/viewmodels/CameraViewModel.h
    src/viewmodels/CameraViewModel.cpp
    src/viewmodels/HardwareViewModel.h
    src/viewmodels/HardwareViewModel.cpp
    src/viewmodels/DataLogViewModel.h
    src/viewmodels/DataLogViewModel.cpp
    src/viewmodels/SettingsViewModel.h
    src/viewmodels/SettingsViewModel.cpp

    # 计算机视觉
    src/vision/ImagePreprocessor.h
    src/vision/ImagePreprocessor.cpp
    src/vision/TraditionalDetector.h
    src/vision/TraditionalDetector.cpp
    src/vision/DetectorFactory.h
    src/vision/DetectorFactory.cpp
)

# QML资源文件
set(QML_RESOURCES
    resources/resources.qrc
)

# 创建可执行文件
add_executable(${PROJECT_NAME}
    ${SOURCES}
    ${QML_RESOURCES}
)

# 链接库
target_link_libraries(${PROJECT_NAME} PRIVATE
    Qt6::Core
    Qt6::Gui
    Qt6::Qml
    Qt6::Quick
    Qt6::QuickControls2
    Qt6::Multimedia
    Qt6::SerialPort
    Qt6::Sql
    Qt6::Concurrent
)

# 如果找到OpenCV则链接
if(OpenCV_FOUND)
    target_link_libraries(${PROJECT_NAME} PRIVATE ${OpenCV_LIBS})
endif()

# 包含目录
target_include_directories(${PROJECT_NAME} PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/src
)

# 如果找到OpenCV则添加包含目录
if(OpenCV_FOUND)
    target_include_directories(${PROJECT_NAME} PRIVATE ${OpenCV_INCLUDE_DIRS})
endif()

# Windows特定设置
if(WIN32)
    set_target_properties(${PROJECT_NAME} PROPERTIES
        WIN32_EXECUTABLE TRUE
    )
endif()

# 复制测试图片到构建目录
file(COPY resources/test-images DESTINATION ${CMAKE_BINARY_DIR})

# 安装规则
install(TARGETS ${PROJECT_NAME}
    RUNTIME DESTINATION bin
)
