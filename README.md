# 以太网到WiFi AP转发示例 (eth2ap)

## 项目简介

本项目实现了以太网到WiFi AP的数据包转发功能，适用于ESP32-P4开发板配合ESP32-C6模块的场景。

### 硬件要求

- **主控芯片**: ESP32-P4
- **WiFi模块**: ESP32-C6
- **连接方式**: SDIO接口

### 功能特性

- 以太网数据包自动转发到WiFi AP
- WiFi AP数据包自动转发到以太网
- 开放式WiFi热点（无需密码），适合家庭使用
- 使用官方`esp_hosted`和`esp_wifi_remote`组件管理P4与C6之间的通信

## 编译说明

**重要提示**: 此例程不能使用VS Code插件进行编译，否则会编译报错。请使用命令行工具编译。

### 编译步骤

1. 确保已安装ESP-IDF开发环境
2. 在终端中进入项目目录
3. 执行以下命令：

```bash
idf.py set-target esp32p4
idf.py build
```

### 烧录

```bash
idf.py flash
```

### 查看日志

```bash
idf.py monitor
```

## 配置说明

### WiFi配置

默认配置如下（在`sdkconfig.defaults`中）：

- **WiFi SSID**: `ESP32-P4-NANO-WiFi`
- **WiFi密码**: 无（开放式网络）
- **WiFi信道**: 1
- **最大连接数**: 4

### 自定义配置

如需修改配置，可以通过以下方式：

1. 运行`idf.py menuconfig`
2. 进入`Example Configuration`菜单
3. 修改相应的配置项

或者直接编辑`sdkconfig.defaults`文件。

### 以太网配置

以太网PHY引脚配置（适用于ESP32-P4）：

- **MDC GPIO**: 31
- **MDIO GPIO**: 52
- **PHY RST GPIO**: 51

## 工作原理

1. **以太网接收**: 当以太网接收到数据包时，会通过流控队列转发到WiFi AP
2. **WiFi接收**: 当WiFi AP接收到数据包时，直接转发到以太网
3. **MAC地址同步**: WiFi AP使用以太网的MAC地址，确保网络层面的透明转发

## 组件依赖

项目使用以下ESP-IDF组件：

- `espressif/esp_wifi_remote`: 版本^1.2.0 - 处理WiFi远程控制
- `espressif/esp_hosted`: 版本^2.5.0 - 管理P4和C6之间的SDIO通信
- `espressif/ethernet_init`: 版本^1.0.0 - 官方以太网初始化组件

这些组件已经内置了P4和C6之间通过SDIO接口通信的所有必要控制逻辑，无需额外编写。

## 故障排除

### 编译错误

如果遇到编译错误，请确认：

1. 使用命令行而非VS Code插件编译
2. ESP-IDF版本是否满足要求
3. 所有依赖组件是否正确安装

### WiFi连接问题

如果设备无法连接WiFi：

1. 检查SSID是否正确
2. 确认WiFi信道设置
3. 查看串口日志排查问题

### 以太网连接问题

如果以太网无法连接：

1. 检查PHY引脚配置是否正确
2. 确认网线连接正常
3. 查看日志中的以太网事件

## 许可证

本示例代码遵循Espressif的许可证（Unlicense或CC0-1.0）。

## 技术支持

如有问题，请参考：
- [ESP-IDF编程指南](https://docs.espressif.com/projects/esp-idf/zh_CN/latest/)
- [ESP32-P4技术规格书](https://www.espressif.com/zh-hans/products/socs/esp32-p4)
