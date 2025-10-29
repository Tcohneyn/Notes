## 🎯 创建字符串选项列表条目（String Entry Widget）与通用旋钮（Common Rotator）

### 一、命名优化与准备工作

- 修改按钮命名：
  - 原名：`CommonButton_Decrease` / `CommonButton_Increase`
  - 新名：`CommonButton_PreviousOption` / `CommonButton_NextOption`
- 含义更直观：分别表示前一个选项与下一个选项。
- 启动项目进入编辑器，为该原生类创建蓝图子类。

------

### 二、创建列表条目蓝图（Widget Blueprint）

1. 路径：`UI/Widgets/OptionsList/Entries`
2. 创建新 Widget Blueprint：
   - 父类：`ListEntry_String`
   - 命名：`WBP_ListEntry_String`
3. 打开后：
   - 添加 `SizeBox` → 布局改为 *Desired* 尺寸。

------

### 三、构建横向布局与显示文本

1. 在 SizeBox 内：
   - 添加 `CommonText`（用于显示设置名称）。
   - 用 `HorizontalBox` 包裹，形成横向布局。
2. 对齐与属性设置：
   - Horizontal Alignment: **Fill**
   - Vertical Alignment: **Center**
3. 绑定变量名：
   - 从 C++ 基类复制变量名 → 粘贴至蓝图。
   - 第一个绑定完成。
4. 调整样式：
   - Text Style: `Text_ListEntry_Default`
   - Justification: Center
   - 预览文本：`Setting Name`

------

### 四、创建图像按钮（前一选项按钮）

1. 创建新按钮蓝图：
   - 路径：`Widgets/Buttons`
   - 父类：`FrontendCommonButtonBase`
   - 命名：`WBP_Button_ListEntry_Image`
2. 布局：
   - 添加 `Overlay`，改布局为 *Desired*
   - 设置样式：`Button_Clear_Style` → 去除背景
3. 添加内容：
   - 插入 `CommonLazyImage`（延迟加载图像）
   - 变量名：`CommonLazyImage_ButtonImage`

------

### 五、事件图表逻辑（Event Graph）

1. 在 **PreConstruct** 后添加逻辑：
   - 调用 `SetBrushFromLazyTexture` → 使用软引用加载按钮图片。
   - 创建变量：`SoftButtonImage`（Public）
2. 设置默认颜色：
   - 变量：`DefaultButtonImageColor`
   - 颜色：RGB = 1,1,1，A = 0.4
   - 用于悬停时颜色变化。
3. 分类整理变量：
   - 分类：`FrontendButton`
   - 公开 `SoftButtonImage` 与 `DefaultButtonImageColor`

------

### 六、绑定到主列表条目蓝图

1. 回到 `ListEntry_String` 蓝图：
   - 搜索并添加 `Button_ListEntry_Image`
   - 填入左箭头图标作为图片。
2. 设置尺寸：
   - Min Width/Height = 30
   - 对齐：水平与垂直均 Center
   - 绑定变量名：`PreviousOption`

------

### 七、创建旋钮控件（Common Rotator）

1. 新建 Widget Blueprint：
   - 路径：`Widgets/Buttons`
   - 父类：`FrontendCommonRotator`
   - 名称：`WBP_Rotator_Options`
2. 添加 `Overlay`，布局设为 Desired。

#### (1) 清除旋钮默认声音

- 创建新按钮样式：
  - 路径：`Styles/Button`
  - 复制 `Button_Clear` → 改名：`Style_Button_Clear_NoSound`
  - 删除按下与悬停声音。
- 在 Rotator 中应用该无声样式。

#### (2) 修复父类绑定错误

- 父类要求变量名：`MyText`（类型：CommonText）。
- 添加 CommonText → 勾选 `IsVariable` → 命名为 `MyText`。

#### (3) 样式与预览文本

- Text Style: `ListEntry_Default`
- 预览内容：`Options Display Name`

------

### 八、在主条目蓝图中加入 Rotator

1. 添加 `Rotator_Options` 到 HorizontalBox。
2. 绑定变量名：`CommonRotator_AvailableOptions`
3. 布局设置：
   - Size: Fill
   - 对齐：水平/垂直 Center
   - 按钮区域：Auto Fill

------

### 九、添加“下一选项”按钮

1. 复制前一选项按钮 → 改变量名为：`NextOption`
2. 更换图片：右箭头。

------

### 十、完成列表条目蓝图

- 所有部件绑定完成：
  - `DisplayNameText`
  - `PreviousOption`
  - `AvailableOptions`
  - `NextOption`
- 编译并保存。

------

### 十一、关联数据资产与测试

1. 打开 Data Asset：
   - 路径：`UI/Widgets/Options`
   - 填入新建的 `WB_ListEntry_String`。
2. 保存后运行游戏 → 进入 Options Screen。
   - 可正常显示字符串条目与左右切换按钮。
   - 切换标签时条目消失 → 功能验证完成。

------

### 十二、后续计划

- 下一步：
  - 从数据对象更新列表条目的文本显示内容。
  - 实现数据与显示的动态绑定逻辑。

