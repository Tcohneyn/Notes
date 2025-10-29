以下是这一整段字幕的**标题式大纲总结（详细版）**👇

------

## 🎯 本节主题

**创建并配置自定义 GameUserSettings 以保存选项数据**

------

### 一、功能目标

- 让列表项（List Entry Widget）按钮点击时更新 DataObject 的值。
- 实现保存这些值，使其在下次打开选项界面时仍然存在。
- 使用 **Game User Settings** 实现持久化保存。

------

### 二、Game User Settings 概念说明

- `UGameUserSettings` 是用于**保存游戏设置**的内置类。
- 可存储图像、音频、游戏玩法等设置。
- 支持：
  - 将设置值保存到文件（Save Config）
  - 从文件加载设置（Load Config）
- 目的是：
   让选项数据具有**持久化能力**（Persistent Data）。

------

### 三、创建自定义 GameUserSettings 类

1. 打开 C++ Classes → FrontendSettings 文件夹。
2. 新建类：
   - 基类：`UGameUserSettings`
   - 类名：`UFrontEndGameUserSettings`
3. 创建完成后关闭项目以准备编辑代码。

------

### 四、实现静态 Getter 函数

#### 1. 在头文件添加 Public 区域

#### 2. 声明并定义静态获取函数：

```cpp
static UFrontEndGameUserSettings* Get();
```

#### 3. 函数逻辑：

- 检查 `GEngine` 是否有效。
- 调用 `GEngine->GetGameUserSettings()` 获取默认实例。
- 使用 `Cast<UFrontEndGameUserSettings>` 转换为自定义类型。
- 返回结果指针。
   ✅ 编译成功。

🧠 **作用**：
 以后可直接调用 `UFrontEndGameUserSettings::Get()` 快速访问当前设置对象。

------

### 五、定义可保存的变量

1. 在 `private:` 区块内新增：

```cpp
UPROPERTY(Config)
FString CurrentGameDifficulty;
```

1. `Config` 说明：
   - 标记此变量会被保存到 `.ini` 配置文件中。
   - 下次启动时会自动读取上次保存的值。

✅ 编译成功。

------

### 六、在项目中启用自定义设置类

1. 打开 **Edit → Project Settings**。

2. 搜索 **Game User**。

3. 将默认类替换为：

   ```
   FrontEndGameUserSettings
   ```

4. 保存并 **重启编辑器**。

------

### 七、验证配置文件生成

1. 打开项目目录 → `Saved/Config/WindowsEditor`。

2. 打开对应 `.ini` 文件。

3. 观察：

   - 上方为默认引擎图像设置变量。

   - 下方新增：

     ```ini
     [/Script/RunEndSettings.FrontEndGameUserSettings]
     CurrentGameDifficulty=
     ```

4. 当前值为空，说明变量已注册但未写入。

------

### 八、下一步预告

- 在下节课中，将实现：
  - 从 DataObject 向 GameUserSettings 写入数值。
  - 实现真正的持久化保存逻辑。

------

✅ **本节核心总结**

| 模块     | 内容                             |
| -------- | -------------------------------- |
| 核心任务 | 创建可保存游戏设置的自定义类     |
| 类名     | `UFrontEndGameUserSettings`      |
| 关键点   | 静态获取函数 + Config属性变量    |
| 配置操作 | 在 Project Settings 指定新类     |
| 检查结果 | `.ini` 文件中出现自定义字段      |
| 下一步   | 将 DataObject 值写入配置文件保存 |

------