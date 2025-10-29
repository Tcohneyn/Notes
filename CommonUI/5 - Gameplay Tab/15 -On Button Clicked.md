### 🎯 本节标题：绑定 Rotator 按钮点击事件（左右切换功能的前置准备）

------

#### 一、回顾与目标

- 上节成果：Rotator 已能正确显示当前选项文字。
- 本节目标：
  - 让左右按钮点击时能触发回调函数。
  - 验证按钮绑定逻辑是否正确执行。

------

#### 二、准备工作

1. 关闭 Unreal 项目，进入 **ListEntry_String** Widget 的头文件。

2. 在 `protected` 区重写函数：

   ```cpp
   virtual void NativeOnInitialized() override;
   ```

   - 可通过 **Ctrl+T** 搜索。
   - 用于在 Widget 初始化时绑定按钮事件。

------

#### 三、创建函数定义并调用父类

- 在 `.cpp` 中实现：

  ```cpp
  void UListEntry_String::NativeOnInitialized()
  {
      Super::NativeOnInitialized();
      // 接下来进行按钮绑定
  }
  ```

------

#### 四、绑定按钮点击事件

1. 先包含按钮头文件：

   ```cpp
   #include "FrontEndCommonButtonBase.h"
   ```

2. 获取按钮的 OnClicked 委托：

   ```cpp
   CommonButton_PreviousOption->OnClicked().AddUObject(this, &ThisClass::OnPreviousOptionButtonClicked);
   CommonButton_NextOption->OnClicked().AddUObject(this, &ThisClass::OnNextOptionButtonClicked);
   ```

   - `AddUObject`：绑定成员函数为回调。
   - 第一个参数 `this`：当前对象。
   - 第二个参数：回调函数指针。

------

#### 五、定义两个回调函数

在 `private` 区添加：

```cpp
void OnPreviousOptionButtonClicked();
void OnNextOptionButtonClicked();
```

随后在 `.cpp` 中实现。

------

#### 六、添加调试输出验证

1. 包含调试头文件：

   ```cpp
   #include "FrontEndDebugHelper.h"
   ```

2. 在回调中添加输出：

   ```cpp
   Debug::Print("Previous Option Button Clicked");
   Debug::Print("Next Option Button Clicked");
   ```

------

#### 七、编译与运行验证

- 编译成功 ✅
- 启动项目 → 进入 Options 界面。
- 点击“上一项/下一项”按钮：
  - 终端输出对应调试信息。
  - 证明事件绑定成功执行。

------

#### 🔚 八、总结与下一步

- 本节完成：
  - 绑定 Rotator 左右按钮事件。
  - 验证点击回调成功。
- 下一节目标：
  - 实现按钮点击后 **切换选项逻辑**（真正改变显示内容）。