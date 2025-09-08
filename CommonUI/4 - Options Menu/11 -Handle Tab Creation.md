# Unreal Engine 5 选项界面 Tab 列表实现教程

### 1. 前置准备

- 添加 `TabList_Widget_Options_Tabs` 变量绑定蓝图 Widget
- 创建 `RequestRegister_Tab` 函数

### 2. 实现 `RequestRegister_Tab` 函数

- 调用父类函数 `Register_Tab` 注册 Tab
- 填入参数：Tab ID、Button Widget 类型、Content Widget（可为空）、Tab 索引

### 3. 获取并设置 Tab 按钮

- 使用 `Get_Tab_Button_Base_By_ID` 获取按钮指针
- 转换为 `UFrontendCommonButtonBase`
- 调用 `Set_Button_Text` 设置按钮显示名称

### 4. 编译与检查

- 编译代码，确保无错误

### 5. 蓝图中处理 Tab 创建

- 在 `TabList_Options_Widget` 蓝图重写 `Handle_Tab_Creation` 函数
- 调用自定义 Helper 函数 `Add_Tabs_To_HorizontalBox`
- 设置 Tab 按钮输入、检查有效性、添加到 Horizontal Box
- 设置按钮 padding 并修正编辑器问题

### 6. 绑定与测试

- 将 `TabList_Widget_Options_Tabs` 变量绑定到 Options Screen 蓝图
- 编译并保存
- 在游戏中测试 Tab 创建和显示效果

### 7. 最终效果

- 四个 Tab：Audio、Video、Control、Gameplay
- 点击高亮显示，返回后仍保持状态
- Tab padding 正确显示