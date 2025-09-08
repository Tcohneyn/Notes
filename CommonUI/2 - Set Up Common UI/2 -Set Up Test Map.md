# 课程大纲：创建测试地图并设置场景

## 一、回顾

- 上一节：创建了空的 C++ 项目，并导入了课程所需资源。
- 本节目标：新建 **测试地图（Test Map）**，用于绘制和展示 UI。

------

## 二、创建测试地图

1. **新建关卡**
   - File → New Level → 选择 Empty Level → Create。
2. **保存关卡**
   - File → Save Current Level As。
   - 在 Content 文件夹下新建 **Maps** 文件夹。
   - 命名地图为 **Frontend_TestMap** 并保存。

------

## 三、场景基础搭建

1. **添加光照（Lighting）**
   - 资源路径：Assets → Level Visuals → **BP_level_visuals**。
   - 拖入关卡即可完成光照设置（包含 SkyLight、Directional Light、Exponential Height Fog）。
2. **添加地面（Floor）**
   - Place Actor → 搜索 **Cube**，拖入关卡。
   - 调整缩放：锁定比例，将 X/Y 设为 200，Z 改回 1。
   - 材质：使用 Assets → Level Visuals → **MI_Floor** 替换默认材质。
3. **调整光照方向**
   - 快捷键 **Ctrl + L** 调整光照角度与位置，直至合适。

------

## 四、背景元素填充

1. **添加角色模型**
   - 路径：Assets → Characters → Meshes → **SKM_Mannequin_Simple**。
   - 拖入关卡，旋转 90°。
2. **调整动画**
   - Details → Animation Mode 改为 **Animation Asset**。
   - 下拉选择 **MM_Idle** 动画，角色进入待机状态。

------

## 五、效果与后续

- 点击 **Play** 可在场景中自由移动。
- 当前测试地图已有：光照、地面、背景人偶。
- 问题：UI 展示时，不希望玩家能移动，需要固定视角并禁用输入。
- 解决方案将在 **下一节课程** 讲解。

