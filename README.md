好的，这是为您准备的 WinXShell 最终版超详细说明书。本版本整合了所有已知信息，增加了大量 Lua 示例和详细解释，并严格遵循您提供的所有规范和注意事项，特别适合需要明确、逐步指导的低级 AI 模型学习和使用。

---

## WinXShell 超详细说明书 - 最终版 (中文，面向低级 AI)

### **引言**

**WinXShell 是什么？**
WinXShell 是一个为 Windows 操作系统设计的轻量级程序。你可以把它想象成一个可以替换掉 Windows 原生桌面、任务栏、开始菜单的“外壳”（Shell）。它的主要目的是提供一个现代化的用户界面（UI）框架，让开发者能够使用 **XML 文件**来定义界面的样子（布局和外观），并使用 **Lua 脚本**来控制界面的行为（逻辑和交互）。

**本说明书的目标读者是谁？**
本说明书面向需要非常详细、逐步指导的低级 AI 模型，帮助你理解 WinXShell 的核心概念、文件结构、XML 控件、Lua API，并学会如何创建、运行和调试基本的 UI 项目。

### **第一部分：核心概念与基础结构**

#### **1.1 WinXShell 的工作方式：XML + Lua**

WinXShell 的 UI 开发模式核心是 **XML** 和 **Lua** 的结合：

1.  **XML (eXtensible Markup Language - 可扩展标记语言)**
    *   **作用**: 用来描述 UI 窗口的**静态结构和外观**。它定义了窗口里有哪些控件（比如按钮 `Button`、文本标签 `Label`、输入框 `Edit`），以及这些控件如何排列（比如垂直排列 `VerticalLayout`、水平排列 `HorizontalLayout`）。
    *   **特点**: 结构化、类似 HTML，但标签是自定义的，专注于数据或结构描述。
2.  **Lua (一种轻量级脚本语言)**
    *   **作用**: 用来编写 UI 的**动态行为和逻辑**。它负责处理用户的操作（比如点击按钮）、从系统获取信息、动态地修改界面显示（比如更新列表、改变文本）、执行外部程序等。
    *   **特点**: 语法简单、易于嵌入、执行效率高。

#### **1.2 核心配置文件**

WinXShell 运行时会读取一些重要的配置文件来决定其行为：

*   **`WinXShell.jcfg`**
    *   **位置**: WinXShell 主程序目录下。
    *   **格式**: JSON (JavaScript Object Notation)。
    *   **作用**: WinXShell **外壳本身**的全局配置。定义了诸如桌面背景、任务栏设置（高度、颜色、透明度、是否自动隐藏）、开始菜单样式、系统级热键等。
*   **`WinXShell.lua`**
    *   **位置**: WinXShell 主程序目录下。
    *   **格式**: Lua 脚本。
    *   **作用**: WinXShell **外壳本身**的核心逻辑脚本。处理系统级事件（如屏幕分辨率改变）、定义外壳的启动流程、管理核心组件的交互。

#### **1.3 UI 组件结构 (重要)**

WinXShell 将不同的界面功能封装成独立的 **UI 组件**，通常放在主程序目录下的 `wxsUI` 文件夹内，每个组件对应一个子文件夹。

*   **标准项目文件夹结构（假设项目名为 `UI_MyComponent`）:**
    ```
    X:\Program Files\
    ├── winxshell.exe
    └── wxsUI\
        └── UI_MyComponent\
            ├── main.xml         (必需 - 界面布局)
            ├── main.lua         (必需 - 界面逻辑)
            ├── main.jcfg        (必需 - 窗口配置)
            ├── UI_Debug.bat     (必需 - 调试运行脚本)
            ├── icon.ico         (可选 - 任务栏图标)
            ├── themes\          (可选 - 主题文件夹)
            │   └── default.xml
            │   └── dark.xml
            └── locales\         (可选 - 语言文件夹)
                └── zh-CN.xml
                └── en-US.xml
            └── parts\           (可选 - 包含的子XML/Lua)
            └── 其他资源文件... (如图片)
    ```

*   **组件核心文件详解:**
    *   **`main.xml`**: 定义该 UI 组件窗口的**布局和控件**。这是界面的“骨架”。
    *   **`main.lua`**: 包含该 UI 组件的**交互逻辑**。处理按钮点击、数据更新等。这是界面的“大脑”。
    *   **`main.jcfg`**: **配置该 UI 组件窗口**的行为，如窗口名称、标题、大小、位置、图标等。这决定了窗口如何呈现和管理。

#### **1.4 `main.jcfg` 文件参数详解 (UI 组件配置)**

这个 JSON 文件控制单个 UI 窗口的属性。以下是所有已知参数的详细说明：

*   **`name`** (字符串, **必需**):
    *   **描述**: UI 组件的唯一内部名称。用于 WinXShell 识别窗口类型。
    *   **示例**: `"UI_Calendar"`
*   **`title`** (字符串, 可选):
    *   **描述**: 窗口的标题，会显示在窗口标题栏（如果可见）和 Windows 任务栏上。支持使用 `%{ResourceID}` 格式引用语言文件 (`locales/*.xml`) 中的字符串。如果省略，通常默认使用 `name` 的值。
    *   **示例**: `"系统设置"` 或 `"%{SettingsTitle}"`
*   **`entry`** (字符串, 可选):
    *   **描述**: 指定定义窗口布局的 XML 入口文件路径（相对于 `main.jcfg` 文件）。如果省略，默认加载同目录下的 `main.xml`。
    *   **示例**: `"SettingsLayout.xml"`
*   **`lua`** (字符串, 可选):
    *   **描述**: 指定处理窗口逻辑的 Lua 脚本文件路径。如果省略，默认加载同目录下的 `main.lua`。
    *   **示例**: `"SettingsLogic.lua"`
*   **`singleton`** (布尔值, 可选, 默认 `false`):
    *   **描述**: `true` 表示该类型的窗口全局只能存在一个实例。如果尝试打开第二个，会激活已存在的窗口而不是创建新的。常用于设置、主面板等。
    *   **示例**: `true`
*   **`noshadow`** (布尔值, 可选, 默认 `false`):
    *   **描述**: `true` 表示禁用窗口的阴影效果（通常由 `Shadow.png` 提供）。`false` 表示启用。
    *   **示例**: `false`
*   **`position`** (字符串, 可选, 默认 `"center"`):
    *   **描述**: 窗口的初始显示位置。
        *   `"center"`: 屏幕正中央。
        *   `"rightbottom"`: 屏幕右下角。
        *   `"(auto)"`: WinXShell 自动决定位置，常用于托盘弹出窗口（如音量、日历）。
        *   `"x,y"`: 指定左上角精确坐标，如 `"100,150"`。
    *   **示例**: `"(auto)"`
*   **`nobaricon`** (布尔值, 可选, 默认 `false`):
    *   **描述**: `true` 表示不在 Windows 任务栏上显示该窗口的图标。`false` 表示显示。
    *   **示例**: `true` (常见于托盘弹出窗口)
*   **`baricon`** (字符串, 可选, 默认 `main.ico`):
    *   **描述**: 指定在任务栏上显示的图标文件路径（相对于 `main.jcfg`）。
    *   **示例**: `"app_icon.ico"`
*   **`customstyle`** (布尔值, 可选, 默认 `false`):
    *   **描述**: `true` 表示不使用 WinXShell 的标准窗口样式，需要手动通过 `style` 和 `exstyle` 指定 Windows 窗口样式。
    *   **示例**: `true` (较少使用，除非需要特殊窗口行为)
*   **`style`** (整数, 可选):
    *   **描述**: 当 `customstyle` 为 `true` 时，指定窗口的 Windows 样式 (WS\_*)。这是一个底层 Windows API 参数。
    *   **示例**: `2415919104` (这是一个特定的组合值)
*   **`exstyle`** (整数, 可选):
    *   **描述**: 当 `customstyle` 为 `true` 时，指定窗口的 Windows 扩展样式 (WS\_EX\_*)。例如，`8` (WS\_EX\_TOPMOST) 表示顶层窗口。
    *   **示例**: `264`
*   **`trans`** (整数, 可选, 0-255):
    *   **描述**: 窗口的透明度。`0` 为完全透明，`255` 为完全不透明。
    *   **示例**: `240` (轻微透明)
*   **`OnDeactive`** (字符串, 可选):
    *   **描述**: 当窗口失去焦点（用户点击了其他地方）时的行为。`"hide"` 表示自动隐藏窗口。常用于托盘弹出窗口。
    *   **示例**: `"hide"`
*   **`theme`** (字符串, 可选, 默认 `"default"`):
    *   **描述**: 指定要加载的主题文件名称（不含路径和 `.xml` 后缀）。WinXShell 会在 `themes/` 目录下查找对应的 XML 文件（如 `themes/dark.xml`）。主题文件定义了颜色、字体等。
    *   **示例**: `"dark"`
*   **`locale`** (字符串, 可选):
    *   **描述**: 指定界面使用的语言资源文件名称（不含路径和 `.xml` 后缀）。WinXShell 会在 `locales/` 目录下查找对应的 XML 文件（如 `locales/zh-CN.xml`）。如果省略，则使用系统默认语言。
    *   **示例**: `"en-US"`
*   **`minimizebox` / `maximizebox`** (布尔值, 可选, 默认 `true`):
    *   **描述**: 控制窗口标题栏是否显示最小化/最大化按钮。
    *   **示例**: `"maximizebox": false` (禁用最大化按钮)

```json
// 综合示例: UI_Settings/main.jcfg
{
  "name": "UI_Settings",          // 内部名称
  "title": "%{SettingsTitle}",    // 窗口标题 (来自语言文件)
  "baricon": "settings.ico",     // 任务栏图标
  "singleton": true,             // 只允许一个实例
  "position": "center",          // 居中显示
  "theme": "dark",               // 使用暗色主题
  "minimizebox": true,           // 显示最小化按钮
  "maximizebox": false           // 禁用最大化按钮
}
```

### **第二部分：XML 布局详解**

XML 文件定义了 UI 的视觉元素和结构。

#### **2.1 XML 基础**

*   **声明**: 文件通常以 `<?xml version="1.0" encoding="utf-8"?>` 开头。
*   **根元素**: 必须有一个唯一的根元素，通常是 `<Window>`。
*   **标签**: `<标签名 属性="值">子元素</标签名>` 或 `<标签名 属性="值" />` (自闭合)。
*   **属性**: 在开始标签中定义控件的特性，如 `width`, `height`, `text`, `name`。
*   **注释**: `<!-- 这是注释 -->`。

#### **2.2 布局容器**

容器用于组织和排列其他控件。**重要：** 按照说明书要求，所有布局容器都应明确指定 `bkcolor` 属性，默认使用深色主题颜色。

*   **`<VerticalLayout>`**: 垂直排列子控件（从上到下）。
    *   **`bkcolor="#FF202020"`** (必需): 背景色，深灰色。
    *   `padding="左,上,右,下"`: 容器的内边距，内容与边框的距离。
    *   `childpadding="间距"`: 子控件之间的垂直间距。
    *   `inset="左,上,右,下"`: 子控件整体的外边距，在 `padding` 之内。
    *   `vscrollbar="true"`: 显示垂直滚动条。
    ```xml
    <VerticalLayout bkcolor="#FF202020" padding="10,10,10,10" childpadding="5">
        <Label text="第一行" height="30"/>
        <Label text="第二行" height="30"/>
    </VerticalLayout>
    ```
*   **`<HorizontalLayout>`**: 水平排列子控件（从左到右）。
    *   **`bkcolor="#FF202020"`** (必需): 背景色，深灰色。
    *   `padding`, `childpadding`, `inset`, `hscrollbar` 属性同上。
    ```xml
    <HorizontalLayout bkcolor="#FF202020" height="50" padding="5,5,5,5" childpadding="10">
        <Button text="按钮1" width="80"/>
        <Button text="按钮2" width="80"/>
    </HorizontalLayout>
    ```
*   **`<TileLayout itemsize="宽,高">`**: 网格布局，将子控件按指定尺寸排列。
    *   **`bkcolor="#FF202020"`** (必需): 背景色。
    *   `itemsize="100,100"`: 每个子项占据 100x100 的空间。
*   **`<TabLayout name="myTabs">`**: 标签页布局。
    *   **`bkcolor="#FF202020"`** (必需): 背景色。
    *   `selectedid="0"`: 默认选中的标签页索引（0是第一个）。
    *   内部包含多个布局容器（如 `VerticalLayout`），每个代表一个标签页，用 `name` 属性标识。
    ```xml
    <TabLayout name="mainTabs" selectedid="0" bkcolor="#FF202020">
        <VerticalLayout name="tab1" bkcolor="#FF2B2B2B">
            <Label text="这是标签页1的内容"/>
        </VerticalLayout>
        <VerticalLayout name="tab2" bkcolor="#FF2B2B2B">
            <Label text="这是标签页2的内容"/>
        </VerticalLayout>
    </TabLayout>
    ```

**布局嵌套与间距:**

*   可以将布局容器相互嵌套以创建复杂界面。
*   **使用 `<Control />` 添加间距 (重要):**
    *   在 `HorizontalLayout` 中: `<Control width="10"/>` 添加 10 像素宽的空白。
    *   在 `VerticalLayout` 中: `<Control height="8"/>` 添加 8 像素高的空白。
    *   `<Control />` (无尺寸): 在 `HorizontalLayout` 或 `VerticalLayout` 中自动填充剩余空间，常用于将控件推到两端。

**布局整齐性与对齐:**

*   **关键**: 确保同一行的控件在**同一水平线上**。
*   **方法**:
    1.  **设置相同高度**: 尽量让同一行控件（如 `Button`, `Edit`, `Combo`）具有相同 `height`。
    2.  **调整 `padding` 和 `textpadding`**:
        *   对于 `Label` 或 `Text`，调整其 `padding` 的**上边距**，使其文本基线与其他控件（如 `Edit` 或 `Button` 的文本）对齐。通常需要试验，`padding="0,8,0,0"` 或 `padding="0,5,0,0"` 是常见的尝试值。
        *   `textpadding` 控制文本内容与其自身边框的距离，也可以用来微调对齐。
    3.  **使用容器**: 将需要对齐的控件放入同一个 `HorizontalLayout` 中，容器会自动处理部分对齐。可以通过设置容器的 `padding` 和 `childpadding` 来控制整体间距。

*   **通用边距规则 (推荐):**
    *   **窗口边缘**: 最外层容器使用 `padding="60,20,60,20"` (左右60, 上下20)。
    *   **内容列控制**: 在主容器内再嵌套一层 `VerticalLayout`，使用 `padding="20,0,20,0"` 进一步约束内容宽度。
    *   **控件间距**: 水平使用 `<Control width="8"/>` 或 `10`，垂直使用 `<Control height="12"/>` 或 `15`。

```xml
<!-- 对齐示例 -->
<HorizontalLayout height="40" bkcolor="#FF202020" childpadding="8">
    <Label text="用户名:" width="80" height="30" textcolor="#FFFFFFFF" padding="0,8,0,0"/> <!-- 上内边距8使文字对齐 -->
    <Edit name="usernameEdit" width="200" height="30" bkcolor="#FF2B2B2B" textcolor="#FFFFFFFF"/>
</HorizontalLayout>
```

#### **2.3 常用控件及其属性**

(这里仅列出关键属性和说明书中提到的控件，完整列表请参考上一版本)

*   **`<Label>`**: 显示静态文本。
    *   `text`, `textcolor`, `font`, `align`, `height`, `width`, `padding`, `bkcolor`, `bkimage`, `showhtml="true"` (支持 `<b>`, `<a>` 等)。
*   **`<Text>`**: 显示多行静态文本。
    *   `text`, `textcolor`, `font`, `height`, `width`, `padding`, `bkcolor`, `multiline="true"`.
*   **`<Button>`**: 可点击按钮。
    *   `name`, `text`, `textcolor`, `bkcolor`, `font`, `width`, `height`, `padding`, `align`, `style`。
    *   **状态图片**: `normalimage`, `hotimage` (悬停), `pushedimage` (按下), `disabledimage` (禁用)。可以使用 `color='#RRGGBB'` 或 `file='path.png'`。
    *   **特殊名称**: `::closebtn`, `::minbtn`, `::maxbtn` 分别对应窗口的关闭、最小化、最大化功能。
*   **`<Edit>`**: 输入框。
    *   `name`, `text` (初始文本), `prompt` (占位符), `password="true"` (密码模式), `readonly="true"` (只读), `textcolor`, `bkcolor`, `width`, `height`, `padding`, `maxchar` (最大字符数)。
*   **`<CheckBox>`**: 复选框。
    *   `name`, `text`, `textcolor`, `font`, `height`, `width`, `padding`, `checked="1"` (默认选中)。
    *   `style="switch"`: 显示为开关样式。
    *   **状态图片**: `normalimage`, `selectedimage` (选中状态)。
*   **`<Option>`**: 单选按钮。
    *   `name`, `text`, `textcolor`, `font`, `height`, `width`, `padding`, `checked="1"`.
    *   `group="groupName"`: **必需**，将多个 Option 归为一组，实现互斥选择。
    *   `style="radio"`: 显示为标准圆形单选按钮样式。
    *   **状态图片**: `normalimage`, `selectedimage`。
*   **`<Combo>`**: 下拉列表。
    *   `name`, `width`, `height`, `padding`, `textpadding`, `bkcolor`, `textcolor`, `bordercolor`, `style="ct-combo"`.
    *   **项**: 静态项用 `<Option text="..."/>` 或 `<ListLabelElement text="..."/>` 定义在 `<Combo>` 内部。
    *   **Lua 操作**: 使用 `.list` 属性设置/清空项，`.index` 获取/设置选中索引 (0基)，`.text` 获取选中文本。**不支持 `:add()` / `:clear()`**。
*   **`<List>`**: 列表控件 (可带表头)。
    *   `name`, `width`, `height`, `bkcolor`, `bordercolor`, `vscrollbar`, `hscrollbar`, `style="ct-list"`.
    *   **项**: 静态项用 `<ListElement>` 或 `<ListHeaderItem>` (表头) 定义。
    *   **Lua 操作**: 支持 `:add("<ListElement ... />")` 和 `:clear()`。
*   **`<Slider>`**: 滑块。
    *   `name`, `width`, `height`, `bkcolor`, `min`, `max`, `value`, `step` (步进值), `thumbimage` (滑块图片), `thumbsize` (滑块大小)。
*   **`<Progress>`**: 进度条。
    *   `name`, `width`, `height`, `bkcolor`, `min`, `max`, `value`, `foreimage` (进度条前景图片/颜色)。

#### **2.4 样式、主题和本地化**

*   **`<Style>`**: 定义可复用的样式。
    *   `<Style name="myButtonStyle" width="100" height="30" bkcolor="#FF0078D7" textcolor="#FFFFFFFF"/>`
    *   应用: `<Button style="myButtonStyle"/>`
*   **`<Default>`**: 定义某类控件的默认属性。
    *   `<Default name="Button" height="32" bkcolor="#FF2B2B2B" textcolor="#FFFFFFFF"/>` (所有未指定样式的按钮都应用这些默认值)
*   **`<Include>`**: 包含外部 XML 文件（通常是样式或公共部分）。
    *   `<Include source="scrollbar_ltwh.xml"/>`
*   **主题文件 (`themes/*.xml`)**:
    *   包含大量 `<Style>` 和 `<Default>` 定义，决定了 UI 的整体视觉风格（颜色、字体、控件外观）。通常有 `default.xml`, `light.xml`, `dark.xml` 等。
    *   常使用 `ct-` 前缀的样式名，如 `ct-button`, `ct-combo`, `ct-list`, `ct-bkcontent`。
*   **本地化文件 (`locales/*.xml`)**:
    *   包含 `<Font>` 定义和 `<MultiLanguage>` 标签。
    *   `<Font id="16" size="16" name="微软雅黑" ... />` 定义字体。
    *   `<MultiLanguage id="WindowTitle" value="设置" />` 定义文本字符串。
    *   在 XML 中使用 `font="16"` 引用字体，`text="%{WindowTitle}"` 引用文本。

### **第三部分：Lua API 详解**

(此部分包含所有已知 API 的示例，请参考上一版本的详细列表。以下补充说明和强调。)

#### **3.1 核心 Lua 对象**

*   **`App`**: 代表 WinXShell 应用程序本身。
    *   `App:Info(key)`: 获取信息 (如 'Path', 'Version', 'CmdLine', 'Arch')。
    *   `App:Run(exe, params)`: 启动程序不等待。
    *   `App:Exec(exe, params)`: 启动程序并等待。
    *   `App:Sleep(ms)`: 暂停。
    *   `App:SetTimer(id, interval)`, `App:KillTimer(id)`: 定时器。
    *   `App:JCfg(section, key)`: 读取 `WinXShell.jcfg`。
    *   `alert(message, ...)`: **必需**，用于显示信息给用户或调试，替代 `print`。
*   **`sui`**: 代表当前的 UI 窗口。
    *   `sui:find(name)`: 获取控件。
    *   `sui:close()`: 关闭窗口。
    *   `sui:hide()`: 隐藏窗口。
    *   `sui:show()`: 显示窗口。
    *   `sui:title(newTitle)`: 设置窗口标题。
    *   `sui:info(key)`: 获取窗口信息 ('wh', 'rect', 'uipath')。
    *   `sui:move(x, y, w, h)`: 移动和/或调整大小。
    *   `sui:jcfg(key)`: 读取当前 UI 的 `main.jcfg` 中的值。
*   **`Shell`**: 代表 Windows 外壳（如果 WinXShell 作为外壳运行）。
    *   包含 `Desktop`, `Taskbar`, `Startmenu` 子对象。
*   **`Desktop`**: 桌面操作。
    *   `Desktop:SetWallpaper(path)`, `Desktop:GetWallpaper()`: 壁纸。
    *   `Desktop:Link(lnkName, target, params, icon, index, showcmd)`: 创建快捷方式。
    *   `Desktop:Refresh()`: 刷新。
*   **`Taskbar`**: 任务栏操作。
    *   `Taskbar:Pin(path)`, `Taskbar:Unpin(path)`: 固定/取消固定。
    *   `Taskbar:CombineButtons(value)`, `Taskbar:UseSmallIcons(value)`, `Taskbar:AutoHide(value)`: 设置。
*   **`Startmenu`**: 开始菜单操作。
    *   `Startmenu:Pin(path)`, `Startmenu:Link(...)`: 固定/创建快捷方式。
*   **`System`**: 系统级操作。
    *   `System:Reboot()`, `System:Shutdown()`: 重启/关机。
    *   `System:SetSetting(key, value)`, `System:GetSetting(key)`: 读写系统设置（如颜色主题）。
    *   `System:NetJoin(name)`: 加入域/工作组。
*   **`Reg`**: 注册表操作。
    *   `Reg:Read(key, value)`, `Reg:Write(key, value, data, type)`, `Reg:Delete(key, value)`, `Reg:GetSubKeys(key)`。
*   **`Screen`**: 屏幕设置。
    *   `Screen:Get(key)` ('X', 'Y', 'DPI', 'brightness'), `Screen:Set(key, value)`。
    *   `Screen:DPI(scale)`, `Screen:DispTest(resolutionList)`。
*   **`Dialog`**: 对话框。
    *   `Dialog:Show(title, msg, buttons, icon)`, `Dialog:OpenFile(...)`, `Dialog:SaveFile(...)`, `Dialog:BrowseFolder(...)`。
*   **`File` / `Folder`**: 文件/文件夹操作。
    *   `File.Exists(path)`, `File.GetShortPath(path)`, `File.GetFullPath(path)`, `Folder.Exists(path)`。
    *   **`File_ReadAll(path)` (用户定义)**: 读取文件内容，需要在使用前定义（如下）。
        ```lua
        function File_ReadAll(path)
            local file = io.open(path, "r")
            if file == nil then return "" end
            local text = file:read("*a")
            file:close()
            return text
        end
        ```
*   **`Window` / `Proc`**: 窗口和进程操作。
    *   `Window.Find(title, class)`, `Window.Match(title, class)`: 查找窗口。
    *   `Proc:Activate()`, `Proc:Close()`, `Proc:Kill()`, `Proc:IsVisible()`。
*   **`os` (扩展)**: 系统信息、命令执行、快捷方式、环境变量。
    *   `os.info(key)`, `os.exec(options, cmd)`, `os.link(...)`, `os.putenv(var, value)`, `os.getenv(var)`, `os.rundll(...)`。
*   **`string` (扩展)**: 字符串处理。
    *   `string.envstr(str)`, `string.resstr(str)`.
*   **`math` (扩展)**:
    *   `math.band(a, b)` (按位与)。
*   **`winapi` (内置)**: 底层 Windows API 访问。
    *   `winapi.execute(cmd)`, `winapi.show_message(...)`, `winapi.set_clipboard(text)`, `winapi.get_clipboard()`, `winapi.get_logical_drives()` 等。
*   **`cjson` (内置)**: JSON 解析库。

#### **3.4 动态 UI 管理 (`add`, `clear`, `Combo.list`)**

*   **`container:add(xml_string)`**: 向支持的容器（`VerticalLayout`, `HorizontalLayout`, `List`, `ListContainer` 等）**动态添加**子控件。
*   **`container:clear()`**: **清空**容器中所有**动态添加**的子控件。**不能**清空 XML 文件中静态定义的控件。**不支持** `Button`, `Label`, `Edit`, `Combo` 等非容器控件。
*   **`combo.list = "项1\n项2"`**: **设置或替换** ComboBox 的所有选项。
*   **`combo.list = ""`**: **清空** ComboBox 的所有选项。

### **第四部分：UI 组件简介**

(参考上一版本回答中的列表，描述了 `UI_AppStore` 到 `UI_Sample` 等组件的功能。)

### **第五部分：运行与调试**

#### **5.1 命令行运行**

基本格式：
`X:\路径\winxshell.exe -ui -jcfg X:\路径\wxsUI\组件名\main.jcfg [其他参数]`

常用参数：
*   `-ui -jcfg <配置文件路径>`: 加载指定 UI 组件。
*   `-console`: 显示调试控制台（用于看 `alert` 输出和错误）。
*   `-theme <主题名>`: 指定主题 (如 `dark`, `light`)。
*   `-locale <语言代码>`: 指定语言 (如 `zh-CN`, `en-US`)。
*   `-code "<lua代码>"`: 执行单行 Lua 代码。
*   `-script <脚本路径>`: 执行指定的 Lua 文件。
*   `-log`: 将输出记录到 `%temp%\WinXShell.<PID>.log` 文件。
*   `<UI自定义参数>`: 例如 `-brightness=false` (用于 `UI_Calendar`) 或 `-user Administrator` (用于 `UI_Logon`)。

#### **5.2 使用 `UI_Debug.bat` 调试**

每个 UI 组件目录下的 `UI_Debug.bat` 是最方便的测试方式。它会自动查找 `winxshell.exe` 并加载当前组件的 `main.jcfg`，通常还带有 `-console` 参数。

```bat
REM UI_MyComponent/UI_Debug.bat 内容模板
@echo off
cd /d "%~dp0..\.."
call :GetWinXShell
call :GetFolderName "%~p0"

start "" %WINXSHELL% -console -ui -jcfg wxsUI\%p0%\main.jcfg

pause
goto :EOF

:GetWinXShell
REM ... (查找 winxshell.exe 的代码) ...
goto :EOF

:GetFolderName
REM ... (提取文件夹名到 %p0% 的代码) ...
goto :EOF
```

#### **5.3 调试核心：`alert()`**

由于 `print()` 在 WinXShell 的 UI 模式下不可见，**必须使用 `alert()`** 来显示调试信息或给用户提示。

```lua
function onclick(ctrl)
    if ctrl == "debugButton" then
        local combo = sui:find("myCombo")
        local selectedText = combo.text
        alert("当前选中的是: " .. selectedText .. "\n索引是: " .. combo.index)
    end
end
```

### **第六部分：核心规则与最佳实践 (总结)**

*   **颜色**: 严格使用 8 位十六进制 `#AARRGGBB`。
*   **布局背景**: 所有布局容器 (`VerticalLayout`, `HorizontalLayout` 等) 必须设置 `bkcolor` 属性，默认使用深色主题 (`#FF202020` 或 `#FF2B2B2B`)。
*   **输出**: Lua 脚本中**必须使用 `alert()`** 进行输出或调试，`print()` 无效。
*   **库**: **禁止**引入除 XML (`xml2lua`) 或 INI (`inifile`) 解析库之外的任何外部 Lua 库。`winapi` 和 `cjson` 是内置的，可以直接使用。
*   **动态 UI**:
    *   使用容器的 `add(xml_string)` 添加动态元素。
    *   使用容器的 `clear()` 清空**动态添加**的元素。**只能**用于支持的容器控件。
    *   ComboBox 使用 `.list` 属性管理选项，**不用** `add/clear`。
    *   清空非容器控件（如 Edit）用 `control.text = ""`。
*   **路径**: 假设项目在 `X:\Program Files\wxsUI\`，主程序在 `X:\Program Files\`。
*   **对齐与间距**:
    *   控件要对齐，特别是同一行的。调整 `padding`/`textpadding`。
    *   使用 `<Control width/height="..."/>` 添加固定间距。
    *   遵循推荐的窗口和控件间距值。
*   **文件结构**: 每个项目包含 `main.xml`, `main.lua`, `main.jcfg`, `UI_Debug.bat`。
*   **函数**: Lua 代码只能调用 WinXShell 提供的内置函数/对象方法，或在同一脚本/`dofile` 包含的文件中定义的函数。

---

#### **注意事项** 开始
- 因为在winxshell项目中，print并不可见，所以除需要调试时App:Print来输出控制台,其它场景用alert()来替代
- 上面项目示例的函数都不是内置的，写项目时需要重新写在lua文件中才能调用
- 如果需要清空非容器控件的内容（如 `Edit` 或 `Text`），应使用 `text = ""` 而不是 `clear()`。
- 写出的项目，要假设默认项目都放在"X:\Program Files\wxsUI"下，winxshell.exe文件在"X:\Program Files\winxshell.exe"
- 默认每个项目都是"UI_项目英文名称"为目录,目录中包括main.jcfg,main.lua,main.xml,UI_Debug.bat文件,其中UI_Debug.bat中是命令行执行项目,内容为"X:\Program Files\winxshell.exe -ui -jcfg X:\Program Files\wxsUI\UI_项目英文名称\main.jcfg"
- 每次写项目，必需要先给出main.xml内容，再给出main.lua和命令行执行语句供复制
- 统一使用8位颜色值，所有颜色都是#AARRGGBB的形式
- lua文件开头winapi和cjson库直接能用，不用引用，除解析xml、ini文件的库允许引用外，不要引入其它库，
- lua文件不要引用示例中没有的函数
- 窗口背景色指的是布局容器的颜色，说明开始如果没有特殊要求，请指定所有布局的颜色为如下内容
如果没有特殊要求，请指定所有布局的颜色为如下内容
容器背景色：深灰色，典型值为 RGB(32, 32, 32)，十六进制 #FF202020。
控件背景色（如按钮默认状态）：稍浅的深灰色，典型值为 RGB(43, 43, 43)，十六进制 #FF2B2B2B。
高亮/选中色：蓝色，典型值为 RGB(0, 120, 215)，十六进制 #FF0078D7。
按下状态色：稍深的蓝色，典型值为 RGB(0, 95, 184)，十六进制 #FF005FB8。
文字颜色：
主要文本：白色，#FFFFFFFF。
次要文本（如描述）：浅灰色，#FFCCCCCC 或 #FFDADADA。
边框色（若有）：非常深的灰色，接近黑色，如 #FF1F1F1F。
-  窗口背景色指的是布局容器的颜色 说明结束
#### **注意事项** 结束

## **重要说明**
界面要画得非常整齐，控件同一行的话，要在同一水平线上，请每次调整控件大小
clear()只能上面提到过的控件使用，其它控件不支持，不能使用!
combo控件添加列表只支持改变.list属性来添加和删除,不支持:add这种方式

布局写的时候都要有颜色属性，不然不能用!

---

这份最终版的说明书应该足够详细，能够指导低级 AI 模型理解和使用 WinXShell 进行基本的 UI 项目开发。
