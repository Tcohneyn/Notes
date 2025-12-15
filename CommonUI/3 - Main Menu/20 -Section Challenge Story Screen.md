## 🎯 编程挑战：实现“故事屏幕”（Story Screen）功能

本次挑战要求学员独立创建一个新的可激活 Widget（Activatable Widget）——“故事屏幕”，并实现从主菜单按钮跳转、内容展示以及简单的按钮反馈逻辑。

------

### 1. 🖼️ 挑战目标与要求

| **目标项目**        | **详细要求**                                                 |
| ------------------- | ------------------------------------------------------------ |
| **创建主 Widget**   | 创建一个新的 **Activatable Widget Blueprint** 作为“故事屏幕”（Story Screen）。 |
| **父类选择**        | 思考并选择合适的 **父类** 进行继承（应是 `UCommonActivatableWidget` 的子类）。 |
| **布局设计**        | 保持与现有 UI 系统的**一致性布局**（例如，是否需要背景、标题等）。 |
| **Widget 标签**     | 确定是否需要创建新的 **Widget Tag**，并配置在 **Project Settings** 中，以便 UI Subsystem 能够识别和推送该 Widget。 |
| **Widget 推送位置** | 思考新的 Widget 应该被推送到哪个 **Widget 堆栈**（例如，是全屏堆栈还是模态堆栈）。 |

------

### 2. 🖱️ 故事屏幕内容与交互逻辑

- **添加按钮：** 在 Story Screen 中添加三个主要的按钮，例如：
  - **New Story**（新故事）
  - **Continue**（继续）
  - **New Game Plus**（新游戏+）
- **添加描述：** 根据需要添加描述文本或其他视觉元素。
- **按钮交互逻辑（核心要求）：**
  - 当点击其中任一按钮时，应该 **显示一个“确认屏幕”**（Confirmation Screen）。
  - 确认屏幕中应显示消息，例如 **“内容正在建设中（Content is under construction）”**。

------

### 3. 💡 挑战提示 (Hints)

- **参考现有设置：** 参照已实现的 **主菜单 (Main Menu)** 或 **确认屏幕 (Confirm Screen)** 的创建和调用流程。
- **分步完成：** 不要着急，按步骤依次完成 Widget 创建、标签配置、按钮添加和交互逻辑绑定。
- **知识应用：** 这是应用所学 **异步 Action**（`AsyncAction_PushConfirmScreen`）和 **UI Subsystem 堆栈管理** 知识的绝佳机会。