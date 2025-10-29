# ✅ **标题大纲式总结｜Options Screen 详情视图添加说明图片与文字**

------

### 一、功能目标

- 为 **选项详情视图（Details View）** 添加一张描述图片（Description Image）。
- 实现方式与现代游戏 UI 一致，让选中设置项展示配图与文字说明。

------

### 二、在 Options Data Registry 指定图片

1. 打开 `OptionsDataRegistry.cpp`。

2. 找到测试项 `Test Item` → 改名为 **Test Image Item**。

3. 调用函数：

   ```cpp
   SetSoftDescriptionImage(...)
   ```

   - 需要传入 `TSoftObjectPtr<UTexture2D>` 类型参数。
   - 用于指定显示图片的引用。

------

### 三、准备图片引用存储结构（FrontEnd Developer Settings）

1. 打开 `FrontEndDeveloperSettings.h`。

2. 新建容器：

   ```cpp
   TMap<FGameplayTag, TSoftObjectPtr<UTexture2D>> OptionsScreenSoftImageMap;
   ```

3. 分类设置为 `"Options Image Reference"`。

4. 用于存储所有与 GameplayTag 对应的软图片引用。

------

### 四、添加 GameplayTag 标签

1. 打开 `FrontEndGameplayTags.h`：

   - 声明新标签：

     ```cpp
     FrontEnd.Options.Image
     FrontEnd.Image.TestImage
     ```

2. 在 `.cpp` 中定义：

   ```cpp
   UE_DEFINE_GAMEPLAY_TAG(FrontEnd.Image.TestImage, "FrontEnd.Image.TestImage");
   ```

3. 未来如需更多图片，只需新增相应标签即可。

------

### 五、在函数库中创建辅助函数

**文件：** `FrontEndFunctionLibrary`

1. 新建函数：

   ```cpp
   static TSoftObjectPtr<UTexture2D> GetOptionsSoftImageByTag(FGameplayTag InImageTag);
   ```

2. 逻辑实现：

   - 获取 `FrontEndDeveloperSettings`。
   - 检查 Map 是否包含对应标签。
   - 若不存在 → 输出错误日志。
   - 若存在 → 返回对应图片引用。

------

### 六、在 Data Registry 中使用辅助函数

1. 在顶部包含头文件：

   ```cpp
   #include "FrontEndFunctionLibrary.h"
   ```

2. 调用：

   ```cpp
   UFrontEndFunctionLibrary::GetOptionsSoftImageByTag(FrontEndGameplayTags::FrontEnd_Image_TestImage);
   ```

3. 将返回值传入 `SetSoftDescriptionImage()`。

------

### 七、在编辑器中设置图片资源

1. 打开 **Edit → Project Settings → FrontEnd UI Settings**。
2. 找到 `Options Screen Soft Image Map`。
3. 添加键值对：
   - **Tag:** `FrontEnd.Image.TestImage`
   - **Value:** 任意 `UTexture2D` 图片（如 Logo）。
4. 点击播放测试 → 选中第二个选项 → 成功显示图片。

------

### 八、动态更换测试图片

- 回到 Project Settings，修改图片为 `Foliage` 等任意资源。
- 播放验证 → 图片成功替换。
- 再改回 `Logo` → 图片正常显示。

------

### 九、添加文字说明

1. 在 `OptionsDataRegistry.cpp` 中：

   ```cpp
   SetDescriptionRichText(FText::FromString("The image to display can be specified in the Project Settings. It can be anything the developer assigned in there."));
   ```

2. 触发 Live Coding 并运行游戏。

3. 在 Options Screen → 第二项显示说明文字与图片。

------

### 🔟 总结

✅ **本节成果：**

- 成功在详情视图中添加说明图片。
- 实现通过 **GameplayTag + SoftObjectPtr** 动态绑定图片。
- 支持编辑器中自由更换显示图片。
- 添加描述文本，实现完整的视觉说明展示。

🎯 **下节预告：**
 将继续扩展 UI 功能，提升设置菜单的交互性与细节表现。