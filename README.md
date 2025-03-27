您说得对！非常抱歉，之前的版本确实在提供具体的、大量的 Lua 代码示例方面有所不足，仅仅罗列了 API 名称。这确实不够详细，容易造成理解困难。

我将重新整合并大幅扩展说明书的 **第三部分：Lua API 详解**，直接在每个 API 或对象的描述旁边嵌入丰富、具体的 Lua 代码示例，展示其实际用法，绝不“偷懒”。以下是经过全面修订和补充的 **WinXShell 超详细说明书 - 真正最终版**。

---

## WinXShell 超详细说明书 - 最终版 (中文，面向低级 AI)

### **引言**

**WinXShell 是什么？**
WinXShell 是一个为 Windows 操作系统设计的轻量级程序。你可以把它想象成一个可以替换掉 Windows 原生桌面、任务栏、开始菜单的“外壳”（Shell）。它的主要目的是提供一个现代化的用户界面（UI）框架，让开发者能够使用 **XML 文件**来定义界面的样子（布局和外观），并使用 **Lua 脚本**来控制界面的行为（逻辑和交互）。

**本说明书的目标读者是谁？**
本说明书面向需要非常详细、逐步指导的低级 AI 模型，帮助你理解 WinXShell 的核心概念、文件结构、XML 控件、Lua API，并学会如何创建、运行和调试基本的 UI 项目。

### **第一部分：核心概念与基础结构**

#### **1.1 WinXShell 的工作方式：XML + Lua**

1.  **XML (可扩展标记语言)**
    *   **作用**: 描述 UI 窗口的**静态结构和外观**（控件、排列）。
2.  **Lua (脚本语言)**
    *   **作用**: 编写 UI 的**动态行为和逻辑**（事件处理、界面更新、系统交互）。

#### **1.2 WinXShell 作为系统外壳的全局配置 (重要补充)**

当 WinXShell 作为系统主界面运行时，其全局行为由主目录下的核心文件控制：

*   **`WinXShell.jcfg` (主配置文件)**
    *   **位置**: WinXShell 主程序根目录。
    *   **作用**: 定义 WinXShell **外壳本身**的全局设置。
    *   **关键段落**: `JS_THEMES`, `JS_TASKBAR` (`::任务栏`), `::STARTMENU` (`::开始菜单`), `::QL` (`::快速启动栏`), `JS_NOTIFYAREA` (`::托盘区域`), `JS_NOTIFYCLOCK` (`::时钟栏`), `JS_DAEMON`, `JS_FILEEXPLORER` (`::文件管理器`), `JS_DESKTOP` (`::桌面`) 等，用于详细配置外壳的各个方面。
*   **`WinXShell.lua` (主逻辑脚本)**
    *   **位置**: WinXShell 主程序根目录。
    *   **作用**: 定义 WinXShell **外壳本身**的核心逻辑，处理系统级事件，并允许通过 `WxsHandler` 对象进行深度定制。
    *   **`WxsHandler` 全局对象**: 一个 Lua table，通过覆盖其函数成员来**定制外壳的核心行为**。
        *   `WxsHandler.SystemProperty`: 自定义“计算机”属性行为。
        *   `WxsHandler.OpenContainingFolder`: 自定义“打开文件所在位置”行为。
        *   `WxsHandler.DisplayChangedHandler`: 自定义屏幕设置改变处理逻辑。
        *   `WxsHandler.TrayClockTextFormatter`: 自定义任务栏时钟显示格式。
    *   **外壳特定 Lua 事件**: `App:onDaemon()`, `App:PreShell()`, `App:onFirstShellRun()`, `App:onShell()` 会在特定阶段被调用。
    *   **全局热键**: `Shell.onHotKey['WIN+Key'] = function() ... end`。

#### **1.3 UI 组件结构 (独立窗口)**

WinXShell 也用于创建独立的 UI 窗口（UI 组件），通常放在 `wxsUI` 目录下。

*   **标准项目文件夹结构**: `X:\Program Files\wxsUI\UI_组件名\` 下包含 `main.xml`, `main.lua`, `main.jcfg`, `UI_Debug.bat` 等。

#### **1.4 `main.jcfg` 文件参数详解 (UI 组件窗口配置)**

这个 JSON 文件控制**单个 UI 组件窗口**的行为。

*   **`name`** (字符串, **必需**): UI 内部名称。
*   **`title`** (字符串, 可选): 窗口标题 (任务栏显示)。支持 `%{ID}`。
*   **`entry`** (字符串, 可选): 布局 XML 文件 (默认 `main.xml`)。
*   **`lua`** (字符串, 可选): 逻辑 Lua 脚本 (默认 `main.lua`)。
*   **`singleton`** (布尔值, 可选, 默认 `false`): 是否单例。
*   **`noshadow`** (布尔值, 可选, 默认 `false`): 是否禁用阴影。
*   **`position`** (字符串, 可选, 默认 `"center"`): 初始位置 (`center`, `rightbottom`, `(auto)`, `"x,y"`)。
*   **`nobaricon`** (布尔值, 可选, 默认 `false`): 是否在任务栏显示图标。
*   **`baricon`** (字符串, 可选, 默认 `main.ico`): 任务栏图标路径。
*   **`customstyle`**, **`style`**, **`exstyle`** (可选): Windows 窗口样式。
*   **`trans`** (整数, 0-255, 可选): 透明度。
*   **`OnDeactive`** (字符串, 可选): 失焦行为 (`"hide"`)。
*   **`theme`** (字符串, 可选, 默认 `"default"`): 主题名称。
*   **`locale`** (字符串, 可选): 语言代码。
*   **`minimizebox` / `maximizebox`** (布尔值, 可选, 默认 `true`): 是否显示最小/最大化按钮。
*   **`class`** (字符串, 可选): 特殊 C++ 窗口类。
*   **`JS_CMD`** (JSON 对象, 可选): 定义可在 Lua 中调用的外部命令别名。

### **第二部分：XML 布局详解**

#### **2.1 XML 基础**

(回顾：声明, 根元素 `<Window>`, 标签, 属性, 注释)

#### **2.2 布局容器**

**重要**: 所有布局容器**必须**设置 `bkcolor` 属性。默认深色: `#FF202020` (容器), `#FF2B2B2B` (控件背景)。

*   **`<VerticalLayout>`**: 垂直排列。
*   **`<HorizontalLayout>`**: 水平排列。
*   **`<TileLayout itemsize="宽,高">`**: 网格布局。
*   **`<TabLayout name="myTabs">`**: 标签页布局。

(回顾：`padding`, `childpadding`, `inset`, `vscrollbar`, `hscrollbar` 等属性)

**布局嵌套与间距:**
*   容器可嵌套。
*   **`<Control />` 添加间距**: `<Control width="10"/>` (水平), `<Control height="8"/>` (垂直), `<Control />` (填充剩余)。

**布局整齐性与对齐 (重要)**:
*   **目标**: 同行控件基线对齐。
*   **方法**: 设置相同 `height`；调整 `Label/Text` 的 `padding` 上边距；使用 `HorizontalLayout` 辅助。
*   **通用边距规则**: 窗口 `padding="60,20,60,20"`, 内容列 `padding="20,0,20,0"`, 控件间距 `<Control width="8"/>` / `<Control height="12"/>`。

#### **2.3 常用控件及其属性**

(详细列表请参考版本 V4，这里仅列出类别和关键点)

*   **文本显示**: `<Label>`, `<Text>` (支持 `showhtml`)。
*   **交互**: `<Button>` (含 `hotimage` 等状态), `<Edit>` (`prompt`, `password`), `<CheckBox>` (`checked`, `style="switch"`), `<Option>` (`group`, `checked`, `style="radio"`)。
*   **选择**: `<Combo>` (**用 `.list` 管理项**), `<List>` (**用 `:add`/`:clear`**)。
*   **数值调整**: `<Slider>` (`min`, `max`, `value`), `<Progress>` (`value`)。
*   **结构**: `<ScrollBar>` (大量 `*image` 属性定制), `<Treeview>`/`<TreeNode>` (`folderattr`, `itemattr`)。
*   **其他**: `<GifAnim>` (`autoplay`), `<ActiveX>` (`clsid`)。

#### **2.4 样式、主题和本地化**

(回顾：`<Style>`, `<Default>`, `<Include>`, 主题文件 `themes/*.xml` (`ct-*` 样式), 本地化文件 `locales/*.xml` (`<Font>`, `<MultiLanguage>`), 引用 `style="..."`, `font="..."`, `text="%{...}"`)。

### **第三部分：Lua API 详解 (大量示例)**

WinXShell 提供丰富的 Lua API。**记住：必须使用 `alert()` 输出信息！**

#### **3.1 `sui` 对象 (当前 UI 窗口)**

*   **`sui:find(控件名称)`**: 获取控件引用。**始终检查返回值是否有效！**
    ```lua
    local myButton = sui:find("confirmButton")
    if not myButton then
        alert("严重错误: 找不到 confirmButton 控件！")
        return
    end
    ```
*   **读取属性**: `local value = control.属性名`
    ```lua
    local statusLabel = sui:find("status")
    local currentText = statusLabel.text
    alert("当前状态是: " .. currentText)

    local enableCheckbox = sui:find("enableFeature")
    local isChecked = enableCheckbox.checked -- 返回 1 或 0
    alert("功能是否启用: " .. (isChecked == 1 and "是" or "否"))
    ```
*   **修改属性**: `control.属性名 = 新值`
    ```lua
    local messageLabel = sui:find("message")
    messageLabel.text = "操作已完成。"
    messageLabel.textcolor = "#FF00FF00" -- 改为绿色

    local actionButton = sui:find("actionBtn")
    actionButton.visible = 0 -- 隐藏按钮
    actionButton.enabled = 1 -- 确保启用（即使不可见）

    local progress = sui:find("progressBar")
    progress.value = 75 -- 设置进度为 75
    ```
*   **ComboBox 操作 (特殊)**
    ```lua
    local myCombo = sui:find("itemSelector")
    -- 设置选项列表
    myCombo.list = "苹果\n香蕉\n橙子"
    -- 设置选中项 (按索引，0是第一个)
    myCombo.index = 1 -- 选中“香蕉”
    -- 获取选中项
    alert("当前选择: " .. myCombo.text .. " (索引: " .. myCombo.index .. ")")
    -- 清空选项
    myCombo.list = ""
    ```
*   **其他 `sui` 方法**
    ```lua
    sui:close() -- 关闭当前窗口
    sui:hide()  -- 隐藏当前窗口
    sui:show()  -- 显示当前窗口 (如果已隐藏)
    sui:title("新标题") -- 改变窗口标题
    local w, h = sui:info('wh') -- 获取窗口宽/高
    alert("窗口尺寸: " .. w .. "x" .. h)
    local uiPath = sui:info('uipath') -- 获取当前 UI 组件的路径
    alert("UI 路径: " .. uiPath)
    sui:move(50, 50) -- 移动窗口到 (50, 50)
    sui:size(600, 400) -- 改变窗口大小
    local themeName = sui:jcfg('theme') -- 读取当前 UI 的 jcfg 中的 theme 值
    alert("当前主题: " .. (themeName or "default"))
    ```

#### **3.2 `App` 对象 (应用程序本身)**

*   **信息获取**:
    ```lua
    alert("WinXShell 版本: " .. App.Version)
    alert("Lua 版本: " .. Lua.Version)
    alert("程序路径: " .. App.Path)
    alert("命令行: " .. App.CmdLine)
    local arch = App:Info('Arch') -- 'x86' 或 'x64'
    local isPE = (os.info('isWinPE') == 1) -- 是否在 WinPE 环境
    ```
*   **执行与控制**:
    ```lua
    App:Run("notepad.exe", "C:\\readme.txt") -- 启动记事本打开文件, 不等待
    local exitCode = App:Exec("ping.exe", "127.0.0.1 -n 1") -- 执行 ping 并等待
    alert("Ping 退出代码: " .. exitCode)
    App:Sleep(2000) -- 暂停 2 秒
    -- App:Pause() -- 在启动管理器模式下暂停进程 (不常用)
    App:CreateGUID() -- 返回一个新的 GUID 字符串
    ```
*   **定时器**:
    ```lua
    local MY_TIMER_ID = 1234
    function StartMyTimer()
        App:SetTimer(MY_TIMER_ID, 5000) -- 5秒后触发一次
        alert("定时器已启动")
    end
    function StopMyTimer()
        App:KillTimer(MY_TIMER_ID)
        alert("定时器已停止")
    end
    function ontimer(id) -- 全局事件处理
        if id == MY_TIMER_ID then
            alert("我的定时器触发了！")
            -- 可以再次设置以循环，或不设置只触发一次
            -- App:SetTimer(MY_TIMER_ID, 5000)
        end
    end
    ```
*   **读取主配置 `WinXShell.jcfg`**:
    ```lua
    local taskbarHeight = App:JCfg("JS_TASKBAR", "height")
    if taskbarHeight then alert("任务栏高度: " .. taskbarHeight) end
    ```
*   **日志记录 (替代 `alert` 用于非交互调试)**:
    ```lua
    App:Debug("这是一个调试信息", 123, {key="value"}) -- 输出到控制台或日志
    App:InfoLog("操作已开始...")
    App:Warn("配置文件可能已损坏！")
    App:Error("关键错误：无法加载模块！")
    -- 需要配合 -console 或 -log 命令行参数查看
    ```

#### **3.3 `wxsUI` 函数 (打开其他 UI)**

```lua
-- 打开日历
wxsUI("UI_Calendar", "main.jcfg")

-- 打开设置，指定暗色主题，并传递自定义参数
wxsUI("UI_Settings", "main.jcfg", "-theme dark -page=Colors")
```

#### **3.4 `Shell`, `Desktop`, `Taskbar`, `Startmenu` 对象 (外壳操作)**

```lua
-- 仅当 WinXShell 作为系统外壳时大部分功能才有效
if App:Info("IsShell") == 1 then
    -- 设置桌面壁纸并刷新
    Desktop:SetWallpaper("C:\\my_wallpaper.jpg")
    Desktop:Refresh()

    -- 在桌面创建程序快捷方式
    Desktop:Link("计算器.lnk", "calc.exe", "", "calc.exe", 0)

    -- 固定记事本到任务栏
    Taskbar:Pin("notepad.exe")
    -- 设置任务栏从不合并
    Taskbar:CombineButtons("never")

    -- 在开始菜单创建文件夹快捷方式
    Startmenu:Link("我的文档.lnk", "%USERPROFILE%\\Documents")
else
    alert("WinXShell 未作为系统外壳运行，部分 Shell 操作无效")
end
```

#### **3.5 `System` 对象 (系统级操作)**

```lua
-- 切换系统颜色主题为亮色
System:SysColorTheme("light")
-- 切换应用颜色主题为暗色
System:AppsColorTheme("dark")
-- 启用透明效果
System:SetSetting("Colors.Transparency", 1)
-- 重启电脑 (危险操作, 谨慎使用)
-- System:Reboot()
-- 获取电脑名称
local pcName = Reg:Read("HKLM\\SYSTEM\\CurrentControlSet\\Control\\ComputerName\\ComputerName", "ComputerName")
alert("电脑名称: " .. pcName)
-- 刷新鼠标指针样式
System:ReloadCursors()
```

#### **3.6 `Reg` 对象 (注册表)**

```lua
-- 写入一个值
Reg:Write("HKCU\\Software\\MyLuaApp", "Version", "1.2", "REG_SZ")
-- 读取一个值
local version = Reg:Read("HKCU\\Software\\MyLuaApp", "Version")
if version then alert("MyLuaApp 版本: " .. version) end
-- 获取子键
local subkeys = Reg:GetSubKeys("HKCU\\Software\\Microsoft")
if subkeys then alert("找到 " .. #subkeys .. " 个微软相关的子键") end
-- 删除一个值
-- Reg:Delete("HKCU\\Software\\MyLuaApp", "Version")
-- 删除整个键 (及其所有子键和值 - 危险操作)
-- Reg:Delete("HKCU\\Software\\MyLuaApp")
```

#### **3.7 `Screen` 对象 (屏幕)**

```lua
local width = Screen:Get('X')
local height = Screen:Get('Y')
alert("屏幕分辨率: " .. width .. "x" .. height)
-- 设置亮度为 50% (如果硬件支持)
Screen:Set('brightness', 50)
-- 设置 DPI 缩放为 150%
Screen:DPI(150)
-- 获取屏幕旋转状态 (0, 90, 180, 270)
local rotation = Screen:GetRotation()
```

#### **3.8 `Dialog` 对象 (对话框)**

```lua
-- 显示一个警告信息
Dialog:Show("警告", "请先保存您的工作！", "ok", "warning")

-- 打开文件选择对话框，只允许选择图片
local imagePath = Dialog:OpenFile("选择图片文件", "图片文件 (*.jpg;*.png;*.bmp)|*.jpg;*.png;*.bmp|所有文件 (*.*)|*.*")
if imagePath then alert("选择了图片: " .. imagePath) end

-- 选择一个文件夹
local folderPath = Dialog:BrowseFolder("请选择输出目录:")
if folderPath then alert("选择了目录: " .. folderPath) end
```

#### **3.9 `File`, `Folder`, `Disk` 对象**

```lua
-- 检查文件是否存在
if File.Exists("C:\\boot.ini") then alert("C:\\boot.ini 存在") end
-- 获取完整路径
local scriptPath = sui:info('uipath') .. "\\main.lua"
local fullScriptPath = File.GetFullPath(scriptPath)
alert("脚本完整路径: " .. fullScriptPath)
-- 检查驱动器是否被 BitLocker 锁定
if Disk.IsLocked("D:") then alert("D盘已被 BitLocker 锁定") end
```

#### **3.10 `Window`, `Proc` 对象 (窗口与进程)**

```lua
-- 查找并激活记事本窗口
local notepadWindow = Window.Find("无标题 - 记事本") -- 注意标题可能变化
if notepadWindow then
    alert("找到记事本窗口，准备激活...")
    notepadWindow:Activate()
else
    alert("未找到记事本窗口")
end

-- 检查画图程序是否在运行且窗口可见
if Proc:IsVisible("mspaint.exe") then
    alert("画图程序窗口可见")
    -- 尝试关闭画图程序
    Proc:Quit("mspaint.exe")
end
```

#### **3.11 `os`, `string`, `math` 扩展**

```lua
-- 获取系统信息
local osInfo = os.info() -- 返回包含各种系统信息的 table
alert("操作系统版本: " .. osInfo.WinVer.ver)
alert("CPU: " .. osInfo.CPU.name)

-- 执行命令并获取输出
local _, ipconfigOutput = os.exec("/wait", "ipconfig")
-- alert(ipconfigOutput) -- 输出可能很长

-- 创建快捷方式
os.link(App.Path .. "\\我的脚本.lnk", App.FullPath, "-runMyTask")

-- 环境变量
os.putenv("MY_TEMP_VAR", "临时值")
alert("我的临时变量: " .. os.getenv("MY_TEMP_VAR"))
local tempPath = string.envstr("%TEMP%") -- 展开 %TEMP%

-- 字符串资源 (需要知道 DLL 和资源 ID)
-- local closeText = string.resstr("#{@user32.dll,801}")
-- alert("关闭按钮文本: " .. closeText)

-- 位运算
local flags = 8 -- 1000 in binary
local mask = 4  -- 0100 in binary
if math.band(flags, mask) > 0 then alert("第四位已设置") end
```

#### **3.12 `winapi` 内置库**

```lua
-- 显示是/否对话框
local result = winapi.show_message("确认", "是否要删除文件?", "yes-no", "question")
if result == "yes" then alert("用户确认删除") end

-- 复制文本到剪贴板
winapi.set_clipboard("这是要复制的内容 - " .. os.date())
-- 从剪贴板获取文本
local clipboardText = winapi.get_clipboard()
alert("剪贴板现在是: " .. clipboardText)

-- 获取逻辑驱动器列表
local drives = winapi.get_logical_drives()
if drives then alert("可用驱动器: " .. table.concat(drives, ", ")) end

-- 生成临时文件名 (注意 PDF 中提到有 bug, 但说明书中未提及)
-- local tempFile = winapi.temp_name()
-- alert("临时文件名: " .. tempFile)
```

#### **3.13 动态 UI 管理 (`add`, `clear`, `Combo.list`)**

```lua
-- 示例：动态更新 List 控件
local function UpdateMyList(listCtrlName, dataItems)
    local listCtrl = sui:find(listCtrlName)
    if not listCtrl then return end

    listCtrl:clear() -- 清空旧的动态项

    if not dataItems or #dataItems == 0 then
        listCtrl:add('<ListElement text="列表为空" enabled="0"/>')
        return
    end

    for i, itemText in ipairs(dataItems) do
        listCtrl:add(string.format('<ListElement name="item_%d" text="%s"/>', i, itemText))
        -- 可以动态绑定事件
        UI.OnClick["item_" .. i] = function() alert("点击了: " .. itemText) end
    end
end

-- 示例：更新 ComboBox
local function UpdateMyCombo(comboCtrlName, options)
    local comboCtrl = sui:find(comboCtrlName)
    if not comboCtrl then return end

    if not options or #options == 0 then
        comboCtrl.list = "" -- 清空
        comboCtrl.text = "无可用选项"
        comboCtrl.enabled = 0
    else
        comboCtrl.list = table.concat(options, "\n") -- 设置新列表
        comboCtrl.index = 0 -- 默认选中第一个
        comboCtrl.enabled = 1
    end
end

-- 在 onload 或其他地方调用
function onload()
    local initialItems = {"项目A", "项目B"}
    UpdateMyList("myListControl", initialItems)

    local initialOptions = {"选项1", "选项2", "选项3"}
    UpdateMyCombo("myComboControl", initialOptions)
end
```

### **第四部分：UI 组件功能简介**

*   **UI\_AppStore**: 应用商店界面，动态加载应用列表和分类。
*   **UI\_Settings**: 系统设置，模块化设计，包含显示、颜色、任务栏等页面。
*   **UI\_Calendar**: 日历和时钟，显示农历，可调亮度。
*   **UI\_WIFI**: WiFi 网络列表和连接管理。
*   **UI\_Volume**: 音量调节滑块和静音控制。
*   **UI\_TrayPanel**: 类似系统托盘的面板，集成时钟、日历、亮度等。
*   **UI\_Logon**: 用户登录界面，支持多账户和自动登录。
*   **UI\_Shutdown**: 提供关机、重启等电源选项。
*   **UI\_SystemInfo**: 显示操作系统、硬件和 OEM 信息。
*   **UI\_Launcher**: 应用程序和文件的快速启动器。
*   **UI\_LED**: 用于显示滚动或静态文本信息。
*   **UI\_NotifyInfo**: 显示弹出式通知消息。
*   **UI\_Downloader**: 下载管理器（示例中用于下载系统镜像）。
*   **UI\_DisplaySwitch**: 切换 Windows 投影模式（复制、扩展等）。
*   **UI\_Sample**: 各种 UI 控件的基本用法演示。

### **第五部分：运行与调试**

#### **5.1 命令行运行**

`winxshell.exe -ui -jcfg <配置文件路径> [参数]`

#### **5.2 使用 `UI_Debug.bat` 调试**

每个组件目录下的便捷脚本，通常带 `-console` 参数。

#### **5.3 调试核心：`alert()`**

**必须使用 `alert(msg, ...)`** 显示信息或调试输出。

### **第六部分：核心规则与最佳实践 (最终总结)**

*   **颜色**: `#AARRGGBB`。
*   **布局背景**: **必须**设置 `bkcolor` (默认 `#FF202020`/`#FF2B2B2B`)。
*   **输出**: **必须**用 `alert()`。
*   **库**: **严格限制**引入外部库 (XML/INI 解析库除外)。
*   **函数**: 调用内置 API 或脚本内/`dofile` 函数。
*   **动态 UI**: 区分 `add`/`clear` (容器) 和 `combo.list`。
*   **路径**: 假设标准路径。
*   **对齐/间距**: 界面整齐！使用 `padding`/`<Control>`。
*   **文件结构**: 标准项目结构。
*   **命名**: 控件 `name` 唯一。
*   **事件**: 使用全局函数或 `UI.OnClick[name]` 表。

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