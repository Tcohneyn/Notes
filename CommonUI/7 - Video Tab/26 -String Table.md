## 🌐 实现设置项描述文本的本地化（Localization）

本讲座通过 String Table 管理所有设置项的描述文本，为后续的本地化奠定基础。

------

### 1. 📂 准备 String Table 资产

为了集中管理和翻译文本内容，所有描述文本被导入到 String Table 资产中。

#### A. 创建 String Table 资产

- **位置：** 在 Content 目录下新建 `StringTable` 文件夹。
- **资产：** 右键 -> Miscellaneous -> String Table。
- **命名：** `ST_OptionsScreenDescription`。

#### B. 导入文本数据

- **数据源：** 讲师提供了包含 **Key**（键）和 **Source String**（源字符串）的 CSV 文件。
  - **Key**：唯一的标识符，用于在代码中引用文本。
  - **Source String**：描述文本内容，已包含 Rich Text Style 标记。
- **导入操作：** 在 String Table 资产中点击 **Import from CSV**，导入提供的 CSV 文件。

------

### 2. 💻 C++ 集成：读取 String Table

在 C++ 代码中实现读取 String Table 文本的功能。

#### A. 引入头文件

- 在 `OptionsDataRegistry.cpp` 中引入：`#include "Internationalization/StringTableRegistry.h"`

#### B. 读取单个描述文本（Window Mode 示例）

- **宏的使用：** 使用原生 UE 宏 `LOCTABLE` 来引用 String Table 中的文本。
- **参数：**
  1. **String Table ID：** 字符串表资产的完整对象路径（通过右键资产 -> Copy Object Path 获得）。
  2. **Key：** 文本内容在 CSV 中对应的 Key（例如 `Video_WindowMode_Desc`）。
- **应用：** 创建 `const FText` 变量，并将其赋值给设置项的 `SetDescriptionRichText()` 函数。

#### C. 简化流程：创建自定义宏

为了避免在每个设置项中重复输入完整的 String Table 路径，创建了一个简化宏：

- **自定义宏：** `#define GET_DESCRIPTION(InKey)`
- **宏内容：** 封装了 `LOCTABLE` 宏，并硬编码了 String Table 的完整路径，只要求输入 Key。

C++

```
// 伪代码: GET_DESCRIPTION 宏的定义
#define GET_DESCRIPTION(InKey) LOCTABLE(
    /* String Table 路径 */, 
    InKey
)
```

#### D. 批量应用描述文本

- **操作：** 使用自定义宏 `GET_DESCRIPTION()`，并传入每个设置项对应的 Key（从 CSV 文件中获取），替换所有设置项（包括 Window Mode、Resolution、Gamma 以及所有 Graphics Quality 设置）原有的描述文本。
- **流程：** 讲师通过时间加速完成了对所有设置项描述的批量替换。

------

### 3. ✅ 验证与总结

- **验证结果：** 重新启动项目后，选择任一设置项（如 Window Mode、Screen Resolution 等），其 **Details View** 中会显示通过 String Table 导入的 Rich Text 描述内容（成功）。
- **本地化意义：**
  - 集中管理文本是本地化的 **关键步骤**。
  - 一旦文本进入 String Table，未来的本地化过程只需翻译 String Table 中的 Source String，无需修改任何 C++ 代码或 UI 蓝图。
  - 描述文本、设置名称、Tab 名称等项目中的所有文本内容，理想情况下都应放入 String Table 中进行管理。

**结论：** 成功地将 Video Tab 下所有设置项的描述文本迁移到 String Table，完成了本地化设置的基础准备。