## ⚙️ 键位重映射第一阶段：配置输入系统

### 1. 🔑 启用 Enhanced Input 用户设置

- **操作：** 进入 **Edit > Project Settings**，搜索 `enhanced`。
- **配置项：** 在 **Engine > Enhanced Input** 类别下，勾选 **User Settings** 选项。
- **效果：**
  - 启用了 `Enhanced Input User Settings` 类，用于存储所有输入键信息。
  - 启用了 `Player Mappable Key Profile Class`，这是 `Input User Settings` 的运行时实例，用于存储所有已映射的键位，可用于创建不同的键位预设。

------

### 2. 📝 创建输入动作（Input Actions, IA）

创建了五个用于模拟动作冒险游戏的核心输入动作（均设置了 **Player Mappable Key Settings**）：

| **Input Action (IA)** | **Mappable Key ID** | **Display Name** | **默认键盘键 (Default Key)** | **默认手柄键 (Gamepad Key)** |
| --------------------- | ------------------- | ---------------- | ---------------------------- | ---------------------------- |
| `IA_Fire`             | `ID_Fire`           | Fire             | Left Mouse Button            | Right Trigger Digital        |
| `IA_Jump`             | `ID_Jump`           | Jump/Climb       | Spacebar                     | Face Button Bottom           |
| `IA_Aim`              | `ID_Aim`            | Aim              | Right Mouse Button           | Left Trigger Digital         |
| `IA_Interact`         | `ID_Interact`       | Interact/Grab    | E Key                        | Face Button Right            |
| `IA_Melee`            | `ID_Melee`          | Melee            | Q Key                        | Face Button Left             |

- **关键配置：** 在每个 IA 资产中，展开 **User Settings** 类别下的 **Player Mappable Key Settings**：
  - 勾选 **Player Mappable**。
  - 设置唯一的 **Name**（作为 Data ID，例如 `ID_Fire`）。
  - 设置 **Display Name**（将在 UI 上显示给用户的名称）。

------

### 3. 📜 创建并配置 Input Mapping Context (IMC)

将所有 IA 绑定到 `IMC_Default` 并设置默认键位。

- **资产：** 创建 `IMC_Default`。
- **键位映射：**
  - 为上述五个 IA 分别添加映射。
  - 为每个 IA 至少设置一个 **键盘/鼠标键** 和一个 **手柄键** 作为默认输入。
  - 映射的 **Eliciting Behavior** 继承自 Input Action 的设置。

------

### 4. 📢 注册 Input Mapping Context

为了使键位信息可供 UI 查询，必须在游戏启动时注册 IMC。

- **位置：** `BP_FrontendController` 蓝图的 **Event OnPossess** 节点。
- **逻辑流程：**
  1. Get Enhanced Input Local Player Subsystem
  2. Get Enhanced Input User Settings
  3. 调用 **Register Input Mapping Context** 函数。
- **注册 vs. 添加：**
  - **Register Input Mapping Context：** 仅将 IA 标记为**可重映射**，键位不会立即激活，适用于主菜单等场景。
  - **Add Mapping Context：** 立即激活键位绑定，适用于游戏关卡。
- **目标 IMC：** 将 `IMC_Default` 资产连接到注册函数的 Pin 上。

------

### 🚀 后续计划

- 在下一讲中，将处理 C++ 代码部分，查询所有已注册的 **Mappable Keys**，并实现过滤键类型（键盘/手柄）的逻辑。