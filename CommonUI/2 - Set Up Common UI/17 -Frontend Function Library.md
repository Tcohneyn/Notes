# ✅ 标题大纲式总结｜Blueprint 获取 FrontEnd UI 设置中的 Widget 引用

------

## **一、问题背景：Blueprint 仍无法访问 TMap 中的 Widget**

- 上一节我们创建了 **FrontEnd UI Settings（Developer Settings）**，成功将 Widget 引用存入 TMap。
- **问题：Blueprint 目前仍无法直接读取这个 TMap 中的 Widget Class。**
- 本节目标：创建一个 **蓝图可调用的函数库**，让 Blueprint 能通过 **GameplayTag** 获取对应的 Widget 类。

------

## **二、创建 Blueprint Function Library**

### **1. 创建类**

- 路径：C++ Classes → Public
- 新建类类型：**Blueprint Function Library**
- 类名：`FrontEndFunctionLibrary`

### **2. 函数声明**

```cpp
UFUNCTION(BlueprintPure, Category="Front End Function Library", meta=(Categories="FrontEnd.Widget"))
static TSoftClassPtr<UCommonActivatableWidget> GetFrontEndSoftWidgetClassByTag(FGameplayTag InWidgetTag);
```

- **BlueprintPure（纯函数）** → 不需要执行引脚，更符合调用习惯。
- **返回类型**：软引用 Widget 类（TSoftClassPtr）
- **参数**：FGameplayTag
- **meta 分类过滤**：只显示 `FrontEnd.Widget.*` 标签

------

## **三、函数实现逻辑（C++）**

### **1. 获取 Developer Settings 实例**

```cpp
const UFrontEndDeveloperSettings* Settings = GetDefault<UFrontEndDeveloperSettings>();
```

### **2. 确认 TMap 中存在对应 Tag**

```cpp
checkf(Settings->FrontEndWidgetMap.Contains(InWidgetTag),
       TEXT("Could not find widget for tag: %s"), *InWidgetTag.ToString());
```

### **3. 返回 TMap 对应的 Widget Class**

```cpp
return Settings->FrontEndWidgetMap.FindRef(InWidgetTag);
```

------

## **四、在 Blueprint 中使用**

### **1. 重新编译 & 启动项目后**

- 在 Blueprint 中可以搜索函数：
   **“Get Front End Soft Widget Class By Tag”**

### **2. 使用方法**

- Tag 参数选择：`FrontEnd.Widget.PressAnyKeyScreen`
- 将返回的**SoftClass**连接到原来的“Create Widget / Load Asset”节点输入。
- 成功替代原先的硬编码类引用。

------

## **五、BlueprintPure 修改原因**

- 原本函数设为 **BlueprintCallable**，会产生执行引脚。
- UI 获取类不需要执行流 → 改为 **BlueprintPure**
- 更加简洁、符合数据查询功能设计。

------

## **六、优势与最终效果**

✅ 不再使用硬编码 Widget 引用
 ✅ 统一改动入口 → Project Settings → FrontEnd UI Settings
 ✅ 任意 UI 替换无需修改 Controller、代码或多个蓝图
 ✅ 维护更加安全、可扩展

------

## **七、收尾工作**

- 删除测试用的临时 Widget
- File → Save All，保存所有修改
- 点击 Play，验证界面功能保持一致

------

如需我继续总结下一段、输出中英翻译、导出为 TXT/Word 文件，也可以继续告诉我！