# **📌 标题大纲式总结｜Press Any Key 文本闪烁动画制作**

------

## **一、目标与准备**

- 上一节完成了 Press Any Key 界面与文字样式 Text Style。
- 本节目标：为 **Press Any Key 文本添加闪烁/呼吸动画效果（Pulsing Effect）**。
- 打开对应 Widget 蓝图，准备开始动画设置。

------

## **二、创建动画（Animation）**

1. 打开 **Window → Animations** 面板。
2. 点击 **“Add Animation”**，将动画命名为：`Posing`。
3. 选中文本组件 `CommonTextBlock_PressAnyKey`，加入到动画轨道中（Track）。
4. 为其命名以便识别（如上面名称）。

------

## **三、设置关键帧与透明度动画（Opacity Track）**

1. 添加 **Render Opacity** 属性轨道。
2. 开启 **Auto Key 自动关键帧**。
3. 设置关键帧流程：
   - 时间 **0s** → Opacity = 1（完全可见）
   - 时间 **0.5s** → Opacity = 0（完全透明）
   - 时间 **1s** → Opacity = 1（再次可见）
4. 点击播放预览动画 → 完成“逐渐消失-再出现”循环效果。

------

## **四、在 Event Graph 中自动播放并循环动画**

1. 打开 **Event Graph**。
2. 在 **Event Construct** 后拖出动画变量 `Posing`。
3. 连接到 **Play Animation 节点**。
4. 设置：
   - **Start Time = 0**
   - **Num Loops To Play = 0（无限循环）**
5. Compile & Save。

------

## **五、播放速度优化（动画节奏调整）**

- 初始效果偏快，返回 Widget 调整：
  - 将 **Play Rate 从 1 改为 0.5**
- 保存后测试 → 闪烁节奏更自然、柔和。

------

## **六、最终结果**

- Press Any Key 文字现在拥有柔和闪烁动画。
- UI 更具动感、适合作为启动界面等待输入提示。
- 下一节将进行后续 UI 功能开发。