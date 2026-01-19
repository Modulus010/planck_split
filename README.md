# Planck Split Keyboard - ZMK Firmware

这是一个基于 ZMK 固件的 Planck 分体键盘配置，采用直连 GPIO 扫描和多层布局设计。

## 硬件特性

- **布局**：48键正交列式分体键盘（每侧 4行×6列）
- **主控**：nRF52840 （左右各一块）
- **扫描方式**：直连 GPIO 扫描（每侧 24 个 GPIO）
- **连接方式**：
  - 左手：USB + 蓝牙（Central 角色）
  - 右手：仅蓝牙（Peripheral 角色）
- **额外功能**：
  - 旋转编码器支持（左右各一个）
  - PWM RGB LED（3通道）
  - 鼠标指针设备
  - ZMK Studio 动态配置

## 固件特性

- **7个独立层级**：基础层、导航层、鼠标层、媒体层、数字层、符号层、功能层
- **智能修饰键**：在基础层使用 `um` (mod-tap) 实现修饰键与字母键的组合
- **层切换键**：底行使用 `ul` (layer-tap) 实现按住切换层、点击发送按键
- **鼠标支持**：完整的鼠标移动、滚动和点击控制
- **媒体控制**：音量、播放控制和蓝牙设备切换
- **快捷操作**：剪切、复制、粘贴、撤销、重做等常用快捷键
- **电源优化**：DCDC 模式、睡眠管理、蓝牙功率调优

## 层级说明

### 1. 基础层 (Base Layer)

标准的 QWERTY 布局，包含以下特点：
- 第一行：QWERTY 键位，左上角切换到功能层，右上角切换到导航层
- 第二行：使用 `um` 实现修饰键与字母键组合 (A/S/D/F 对应 LGUI/LALT/LCTRL/LSHFT)
- 第三行：ZXCVB... 标准底排字母键
- 第四行：层切换键 (ESC/SPACE/TAB/RET/BSPC/DEL)

**层切换快捷键**：
- `ESC` (按住) → 数字层
- `SPACE` (按住) → 导航层
- `TAB` (按住) → 鼠标层
- `RET` (按住) → 符号层
- `BSPC` (按住) → 媒体层
- `DEL` (按住) → 功能层

### 2. 导航层 (Nav Layer)

提供方向键和编辑功能：
- 右侧：方向键 (←↓↑→)、Home/End、Page Up/Down、Insert
- 顶部右侧：撤销/重做、剪切/复制/粘贴
- 支持 Caps Word 功能

### 3. 鼠标层 (Mouse Layer)

完整的鼠标控制：
- 移动：鼠标指针四向移动
- 滚动：鼠标滚轮四向滚动
- 点击：左键、中键、右键点击

### 4. 媒体层 (Media Layer)

多媒体控制和蓝牙管理：
- 播放控制：上一首、下一首、播放/暂停
- 音量控制：音量减、音量增、静音
- 蓝牙设备：BT_SEL 0/1/2/3 选择设备，Shift+BT_SEL 清除配对
- 输出切换：OUT_TOG 切换 USB/蓝牙输出
- RGB 控制：开关、效果、色相、饱和度、亮度
- 电源管理：外部电源开关

### 5. 数字层 (Num Layer)

数字小键盘布局：
- 数字：1-9、0
- 运算符：-、=
- 符号：[ ]、;、`、\、.

### 6. 符号层 (Sym Layer)

常用符号输入：
- 运算符：! @ # $ % ^ & * ( ) - + = [ ] { } | \ : ; 
- 括号：圆括号、方括号、花括号

### 7. 功能层 (Fun Layer)

F键和系统功能：
- F1-F12：完整的功能键
- 系统键：Print Screen、Scroll Lock、Pause/Break
- 应用键：K_APP (菜单键)

## 使用说明

1. **日常输入**：在基础层进行常规打字
2. **方向导航**：按住 `SPACE` 进入导航层，使用右侧方向键
3. **鼠标操作**：按住 `TAB` 进入鼠标层，控制鼠标指针和滚动
4. **数字输入**：按住 `ESC` 进入数字层进行数字输入
5. **符号输入**：按住 `RET` 进入符号层输入特殊符号
6. **媒体控制**：按住 `BSPC` 进入媒体层控制播放
7. **F键使用**：按住 `DEL` 进入功能层使用 F1-F12

**提示**：层切换使用 `&to` 实现永久切换，点击左上角或右上角的层切换键可以在不同层级间跳转。

## 固件编译与刷写

### 自动编译（推荐）

本项目配置了 GitHub Actions 自动编译：

1. **Fork 本仓库**到你的 GitHub 账号
2. **修改配置文件**（`config/planck.keymap` 或其他配置）
3. **提交并推送**到你的仓库
4. GitHub Actions 会自动编译固件
5. 在仓库的 **Actions** 标签页下载编译好的 `firmware.zip`
6. 解压得到 `planck_left.uf2` 和 `planck_right.uf2`

**触发编译的条件：**
- 推送修改到 `config/` 目录
- 推送修改到 `build.yaml`
- 手动触发（Actions 页面点击 "Run workflow"）

### 手动编译（可选）

如果需要本地编译：

```bash
# 前置要求：安装 Zephyr SDK 和 West 工具
# 参考：https://zmk.dev/docs/development/setup

# 初始化工作区
west init -l config
west update

# 编译左手固件
west build -b planck_left

# 编译右手固件  
west build -b planck_right -p

# 固件文件位于 build/zephyr/zmk.uf2
```

### 刷写固件

1. 按住键盘上的 **RESET 按钮**（或双击 RESET）
2. 将键盘连接到电脑（会显示为 USB 存储设备）
3. 将对应的 `.uf2` 文件拖入 USB 存储设备
   - 左手刷写 `planck_left.uf2`
   - 右手刷写 `planck_right.uf2`
4. 设备自动重启，固件刷写完成

**注意**：首次使用需要分别刷写左右手固件。

## 配置文件说明

```
config/
├── planck.conf              # 用户配置（鼠标、睡眠、蓝牙等）
├── planck.keymap            # 键位映射（7层布局）
├── west.yml                 # 依赖清单
└── boards/arm/planck/       # 板级定义
    ├── planck_left.dts      # 左手硬件定义
    ├── planck_right.dts     # 右手硬件定义
    ├── planck.dtsi          # 共享硬件定义
    ├── Kconfig.*            # 编译配置
    └── leds_*.dtsi          # LED 配置
```

## 自定义

### 修改键位

编辑 `config/planck.keymap` 文件：
1. 修改基础层字母或修饰键位置
2. 调整各层的功能键位
3. 添加或移除层切换键
4. 修改媒体控制或蓝牙设置

### 调整功能

编辑 `config/planck.conf` 文件：
- 启用/禁用鼠标支持
- 调整蓝牙功率
- 配置睡眠时间
- 启用/禁用 RGB LED

## 硬件设计说明

### GPIO 使用

- **按键扫描**：每侧 24 个 GPIO（直连扫描，无重影）
- **编码器**：P0.29、P0.31
- **PWM LED**：P0.7、P1.4、P1.6
- **所有 GPIO 无冲突**

### 电源管理

- 使用 DCDC 降压模式延长电池寿命
- RC 时钟配置（无需外部 32kHz 晶振）
- 支持睡眠模式

### 蓝牙配置

- 蓝牙功率：+8dBm（增强信号）
- 支持 4 个蓝牙设备配对
- 左手作为中央设备同步右手电池电量
- 支持 USB/蓝牙输出切换

## 许可证

MIT License - 详见 [LICENSE](LICENSE) 文件

## 致谢

- [ZMK Firmware](https://zmk.dev/) - 开源键盘固件
- [zmk-tog-io](https://github.com/yangxing844/zmk-tog-io) - 额外功能模块
