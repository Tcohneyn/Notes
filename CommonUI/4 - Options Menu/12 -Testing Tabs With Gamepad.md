# Unreal Engine 5 UI：Options Screen 标签页与手柄支持

## 1. 回顾上节内容

- 成功创建 Options Screen 标签页
- 四个不同的标签按钮可用鼠标点击选择

## 2. 手柄测试准备

- 切换到主菜单并连接手柄
- 发现手柄无法切换标签，需要修复

## 3. 设置 Tab List Widget 输入数据

- 打开 Options Screen 的 Tab List Widget
- 为 Next/Previous Tab Input Action Data 分配数据行

## 4. 创建输入映射表行

- 在 Common Input Key Mapping 表中新增 Next/Previous Tab Action
- 分别映射 R1、L1 按钮
- 键盘输入可留空

## 5. 配置 Tab Widget 图标显示

- 在 Virtual Blueprint 的 Event Graph → Preconstruct 设置 Common Action Widget
- 指定 Next/Previous Tab 的输入图标

## 6. 为手柄肩键分配图标

- 在 ControllerData_GamePad 中添加 R1、L1 图标
- 调整尺寸到 50×50

## 7. 解决手柄操作问题

- 勾选 Tab List Widget → Auto listen for input
- 编译保存后重新测试

## 8. 测试结果

- R1 → 切换到下一个标签
- L1 → 切换到上一个标签
- 鼠标/键盘切换时图标消失，切换回手柄时恢复