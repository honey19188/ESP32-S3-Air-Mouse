# ESP32-S3 Air Mouse (飞鼠)

基于ESP32-S3的蓝牙空中鼠标项目，使用MPU-6050传感器捕捉手势动作，通过蓝牙HID协议模拟鼠标信号。

## 硬件清单

- ESP32-S3开发板
- MPU-6050 六轴传感器模块
- ZX-4位独立按键模块
- HW-504 摇杆模块
- 3.7V锂聚合物电池（可选，用于无线供电）

## 硬件连接

### MPU-6050 (I2C接口)

| MPU-6050 | ESP32-S3 |
|----------|----------|
| VCC      | 3.3V     |
| GND      | GND      |
| SCL      | GPIO 9   |
| SDA      | GPIO 8   |

### ZX-4位独立按键

| 按键功能 | ESP32-S3 |
|----------|----------|
| 左键     | GPIO 12  |
| 右键     | GPIO 13  |
| 中键     | GPIO 14  |
| 功能键   | GPIO 15  |

**注意**: 每个按键的另一端连接到GND，代码中已启用内部上拉电阻。

### HW-504 摇杆模块

| HW-504 | ESP32-S3 |
|--------|----------|
| VCC    | 5V       |
| GND    | GND      |
| VRx    | GPIO 4   |
| VRy    | GPIO 5   |
| SW     | GPIO 6   |

**注意**: HW-504使用5V供电时，VRx和VRy输出的模拟信号范围为0-5V。由于ESP32-S3的ADC引脚最大输入电压为3.3V，建议在VRx、VRy与ESP32-S3之间串联分压电阻（如1kΩ和2kΩ），或使用电平转换器，以保护ESP32-S3的ADC引脚。

## 软件配置

### 安装必要的库

在Arduino IDE中，通过"工具" -> "管理库"搜索并安装以下库：

- **Adafruit MPU6050**: 用于驱动MPU-6050传感器
- **Adafruit Sensor**: Adafruit MPU6050库的依赖库
- **Wire**: I2C通信库（Arduino核心已自带）

**注意**: BLE相关库已包含在ESP32 Arduino核心中，无需额外安装。

### Arduino IDE配置

1. 在"工具" -> "开发板"中选择"ESP32S3 Dev Module"
2. 设置正确的端口号
3. 上传代码

## 功能特性

- **MPU-6050手势控制**: 通过陀螺仪检测设备倾斜，控制鼠标移动方向和速度
- **HW-504摇杆辅助**: 摇杆可用于微调鼠标位置或替代手势控制
- **四按键支持**: 左键、右键、中键、功能键（发送Enter键）
- **BLE HID协议**: 标准蓝牙鼠标+键盘协议，支持Windows、macOS、Linux等系统
- **连接状态反馈**: 串口打印连接状态和HID就绪信息
- **智能HID就绪检测**: 结合SetReport回调和超时机制，确保Windows系统正常工作

## 使用方法

1. 将代码上传到ESP32-S3开发板
2. 打开串口监视器（波特率115200）
3. 在电脑蓝牙设置中搜索"ESP32 Air Mouse"并配对
4. 配对成功后，串口会显示"HID enabled via SetReport"或"HID enabled via timeout"
5. 倾斜设备控制鼠标移动，按下按键进行点击

## 灵敏度调整

在代码中调整以下参数：

```cpp
const float SENSITIVITY = 40.0f;  // 鼠标移动灵敏度
const float DEAD_ZONE = 1.0f;     // 陀螺仪死区（防止抖动）
const float JOY_DEAD_ZONE = 20.0f; // 摇杆死区
```

## 常见问题

### Q: 蓝牙连接后鼠标无动作？

A: 请检查：
1. 串口监视器是否显示"HID enabled"
2. 蓝牙配对是否成功完成
3. MPU-6050是否正常初始化

### Q: 鼠标移动有漂移？

A: 可尝试：
1. 增大DEAD_ZONE值
2. 降低SENSITIVITY值
3. 确保MPU-6050水平放置初始化

### Q: Windows系统下蓝牙驱动错误？

A: 代码已针对Windows系统优化，包含完整的HID报告描述符和连接状态管理。确保使用ESP32 Arduino核心版本2.0.0以上。

## 参考资源

- [ESP32 Arduino Core](https://github.com/espressif/arduino-esp32)
- [Adafruit MPU6050 Library](https://github.com/adafruit/Adafruit_MPU6050)
- [BLE HID Specification](https://developer.bluetooth.org/TechnologyOverview/Pages/HID.aspx)