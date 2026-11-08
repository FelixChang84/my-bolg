---
title: "CAD 插件制作：从一条命令到可交付的产品"
date: 2026-11-08T16:40:00+08:00
draft: false
tags: ["CAD", "AutoCAD", "插件开发", "C#"]
categories: ["技术分享"]
description: "系统梳理 CAD 插件的制作方法：平台扩展能力对比、技术选型、AutoCAD .NET 与 LISP 实战、对象模型与事务、界面集成、Bundle 打包自动加载、多版本兼容与发布流程。"
---

## 🎯 为什么值得做 CAD 插件

CAD 是典型的「高频重复 + 强专业规则」场景：画图的人每天在同样的图层、同样的标注样式、同样的图框里做同样的事。一件在图纸上要花二十分钟的活，写成插件后往往是一行命令、三秒钟。

插件真正解决的从来不是「画得快一点」，而是三件事：

- **把规则固化下来**。图层命名、线宽、字高、比例，全部由代码校验，人不再犯错。
- **把数据打通**。图纸里的构件数量直接导出成 Excel、清单、加工文件，避免二次录入。
- **把经验复用**。老师傅的排布逻辑写成算法，新人也能一键出图。

下面按「选平台 → 选技术 → 写代码 → 做界面 → 打包发布」的顺序，把这条链路走完。

## 🧭 先搞清楚 CAD 平台的扩展能力

不同 CAD 的插件模型差异很大，选错平台会让后面的工作全部白费。常见的几类：

| 平台 | 主要扩展方式 | 语言 | 上手难度 | 适合场景 |
| --- | --- | --- | --- | --- |
| AutoCAD | AutoLISP / .NET / ObjectARX | LISP、C#、C++ | 低 → 高 | 建筑、机械、室内设计 |
| 中望 CAD / 浩辰 CAD | LISP 兼容、ZRX/GRX（类 ARX）、.NET | LISP、C#、C++ | 中 | 国产替代、AutoCAD 插件迁移 |
| BricsCAD | BRX（兼容 ARX）、.NET、LISP | C++、C#、LISP | 中 | 兼容 DWG 的替代方案 |
| Revit | Revit API（.NET） | C# | 中 | BIM 建模、构件算量 |
| Rhino | RhinoCommon（.NET）、Python、Grasshopper | C#、Python | 低 | 曲面、参数化设计 |
| FreeCAD | Python 工作台（Workbench） | Python | 低 | 开源、跨平台、二次开发学习 |
| SketchUp | Ruby API | Ruby | 低 | 室内、方案表达 |

一个实用判断：**如果用户主要用 AutoCAD 系，从 .NET 起步；如果是自己学习插件原理，从 FreeCAD 的 Python 工作台或 AutoCAD 的 LISP 起步最快。**

## 🔧 技术选型：四条路线的取舍

### AutoLISP：最快出结果

不用编译、不用装 SDK，在 CAD 命令行输入 `VLISP` 就能写。适合做小工具、批量改属性、临时脚本。

```lisp
;; 批量把选中对象移到指定图层
(defun c:MoveToLayer ( / lay ss i ent)
  (setq lay (getstring "\n目标图层名: "))
  (if (not (tblsearch "LAYER" lay))
    (command "_.-LAYER" "_M" lay "")
  )
  (setq ss (ssget))
  (if ss
    (progn
      (setq i 0)
      (repeat (sslength ss)
        (setq ent (ssname ss i))
        (entmod (append (entget ent) (list (cons 8 lay))))
        (setq i (1+ i))
      )
      (princ (strcat "\n已处理 " (itoa (sslength ss)) " 个对象"))
    )
  )
  (princ)
)
```

缺点是：没有强类型、没有像样的工程结构、几千行之后基本无法维护，也做不了复杂界面。

### .NET（C#）：性价比最高

AutoCAD 从 2006 起提供托管 API，用 C# 写业务逻辑，能调 `AcDb`/`AcAp` 全套对象模型，也能用 WinForms/WPF 做界面。**绝大多数商业化插件走这条路。**

### ObjectARX（C++）：只有它能做的事才用

需要自定义实体、自定义对象、深度参与图形数据库内核、极致性能时，才考虑 ARX。代价是编译环境与 CAD 版本强绑定、崩溃会直接把 CAD 一起带走、调试成本高。

### COM / VBA / 脚本：正在退场

VBA 已被官方逐步停止支持，仅用于维护老项目。跨进程 COM 调用慢且脆弱，新项目不建议。

> 结论：**先用 LISP 验证需求，再用 C# 做成产品，只有性能或自定义实体卡住时才下沉到 C++。**

## 🛠️ 实战：第一个 AutoCAD .NET 插件

### 环境准备

- Visual Studio 2022（社区版够用）
- .NET Framework 4.8（AutoCAD 2021+ 支持 .NET 8，但仍推荐 4.8 兼容更多版本）
- AutoCAD 主程序（调试时会被启动）
- 引用两个程序集，来自 CAD 安装目录：
  - `accoremgd.dll`（核心对象模型）
  - `acdbmgd.dll`（图形数据库）
  - `acmgd.dll`（应用层、编辑器）

这三个 DLL **必须设置 `Copy Local = False`**，否则会和 CAD 自带的版本冲突。

### 项目结构

```
CadPlugin.sln
└─ CadPlugin/
   ├─ Commands/        命令入口
   ├─ Services/        业务逻辑（不引用 CAD 程序集）
   ├─ UI/              窗体、面板
   ├─ Models/          纯数据模型
   └─ CadPlugin.csproj
```

关键分层原则：**`Services` 和 `Models` 里不要出现任何 `Autodesk.*` 的类型。** 这样业务逻辑可单元测试，将来迁移到中望/浩辰也只改命令层。

### 一条最小可用的命令

```csharp
using Autodesk.AutoCAD.ApplicationServices;
using Autodesk.AutoCAD.DatabaseServices;
using Autodesk.AutoCAD.EditorInput;
using Autodesk.AutoCAD.Runtime;

namespace CadPlugin.Commands
{
    public class HelloCommands
    {
        [CommandMethod("HELLOCAD", CommandFlags.Modal)]
        public void HelloCad()
        {
            Document doc = Application.DocumentManager.MdiActiveDocument;
            if (doc == null) return;
            Editor ed = doc.Editor;

            // 1. 让用户选一个对象
            PromptEntityOptions peo = new PromptEntityOptions("\n请选择一个对象: ");
            PromptEntityResult per = ed.GetEntity(peo);
            if (per.Status != PromptStatus.OK) return;

            // 2. 用事务安全地访问数据库
            using (Transaction tr = doc.TransactionManager.StartTransaction())
            {
                Entity ent = tr.GetObject(per.ObjectId, OpenMode.ForRead) as Entity;
                if (ent == null) return;

                ed.WriteMessage($"\n类型: {ent.GetType().Name}, 图层: {ent.Layer}");

                // 3. 修改必须先升级打开模式
                ent.UpgradeOpen();
                ent.ColorIndex = 1;   // 改成红色
                tr.Commit();
            }
        }
    }
}
```

生成后得到一个 `CadPlugin.dll`，在 CAD 里 `NETLOAD` 加载，然后命令行输入 `HELLOCAD` 即可运行。

### 调试配置

项目属性 → 调试 → 启动外部程序，指向 `acad.exe`，工作目录设为 CAD 安装目录。启动后在 VS 里点「附加到进程」，或直接在 `NETLOAD` 后命中断点。命令行参数建议加上 `/nologo` 加快启动。

## 📐 必懂的核心概念：事务与对象模型

新手 90% 的崩溃和「取不到值」都出在下面这几个点上。

### 一切修改都在 Transaction 里

AutoCAD 的数据访问是事务式的。读和写都要 `StartTransaction()`，结束必须 `Commit()`（或 `Abort()`）。

```csharp
using (Transaction tr = db.TransactionManager.StartTransaction())
{
    BlockTable bt = (BlockTable)tr.GetObject(db.BlockTableId, OpenMode.ForRead);
    BlockTableRecord btr =
        (BlockTableRecord)tr.GetObject(db.CurrentSpaceId, OpenMode.ForWrite);

    Line line = new Line(new Point3d(0, 0, 0), new Point3d(100, 0, 0));
    line.Layer = "0";
    btr.AppendEntity(line);          // 先加入空间
    tr.AddNewlyCreatedDBObject(line, true);  // 再登记到事务
    tr.Commit();
}
```

`AddNewlyCreatedDBObject` 漏写是最常见的错误，表现为对象「画出来了但不入库」或保存后消失。

### ObjectId 与 DBObject 的区别

- `ObjectId` 是轻量的句柄，可长期持有、可跨事务传递（推荐）。
- `DBObject` 是实体本身，只在事务打开期间有效，事务结束后使用它会抛异常。

**函数尽量传 `ObjectId`，不要传 `DBObject`。**

### 打开模式：ForRead / ForWrite / UpgradeOpen

默认用 `ForRead` 打开，需要写时再 `UpgradeOpen()`。这不仅是性能问题——大量对象用 `ForWrite` 打开会显著拖慢大图纸。

### 文档锁

在多文档环境（`SDI=0`）下操作非当前文档，必须先锁文档：

```csharp
using (DocumentLock dl = targetDoc.LockDocument())
{
    // 在这里做数据库操作
}
```

忘记加锁会得到 `eLockViolation`。

### 坐标系与单位

`Point3d` 永远是 WCS（世界坐标系）。用户输入的是 UCS 下的点，需要 `ed.GetPoint()` 之后自行转换。单位则完全取决于图纸设置 `INSUNITS`，插件里要显式处理，不要假设是毫米。

## 🎨 界面集成：让插件看起来像原生的

### Ribbon 按钮

```csharp
using Autodesk.Windows;   // 注意：这是 acmgd 里的 Ribbon 命名空间

public void AddRibbon()
{
    RibbonControl rc = ComponentManager.Ribbon;
    RibbonTab tab = new RibbonTab { Title = "我的工具", Id = "MY_TAB" };
    RibbonPanel panel = new RibbonPanel();
    RibbonPanelSource src = new RibbonPanelSource { Title = "标注" };

    RibbonButton btn = new RibbonButton
    {
        Text = "一键标注",
        ShowText = true,
        Size = RibbonItemSize.Large,
        CommandHandler = new MyHandler(),
        CommandParameter = "DIMALL"
    };

    src.Items.Add(btn);
    panel.Source = src;
    tab.Panels.Add(panel);
    rc.Tabs.Add(tab);
}
```

Ribbon 要在 `IExtensionApplication.Initialize()` 里挂载，并且延迟到 CAD 界面就绪后再加，否则可能拿不到 `ComponentManager.Ribbon`。

### 非模态面板（PaletteSet）

比模态对话框好用得多：用户可以边看图边调参数。

```csharp
PaletteSet ps = new PaletteSet("参数面板")
{
    Style = PaletteSetStyles.ShowPropertiesMenu | PaletteSetStyles.ShowAutoHideButton,
    Dock = DockSides.Right
};
ps.Add("主面板", new MyPanelControl());  // 传入 UserControl
ps.Visible = true;
```

### 让业务代码与界面解耦

面板里不要直接写 CAD 操作。推荐：界面收集参数 → 抛事件 → 命令层订阅事件 → 在 `DocumentLock` + `Transaction` 里执行。这样界面可以单独设计、单独测试。

## 📦 打包与自动加载：Bundle 是标准答案

不要指望用户每次都手动 `NETLOAD`。AutoCAD 2012+ 支持 **Autoloader（插件包）**：把文件按固定结构放进 `%APPDATA%\Autodesk\ApplicationPlugins\`，CAD 启动时自动加载。

### 目录结构

```
MyPlugin.bundle/
├─ PackageContents.xml
└─ Contents/
   ├─ Windows/
   │  ├─ MyPlugin.dll
   │  └─ Newtonsoft.Json.dll
   └─ Resources/
      └─ icon.png
```

### PackageContents.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationPackage
    SchemaVersion="1.0"
    AutodeskProduct="AutoCAD"
    Name="MyPlugin"
    AppVersion="1.0.0"
    Description="一键标注工具"
    Author="Your Name"
    ProductType="Application"
    ProductCode="{A1B2C3D4-1111-2222-3333-444455556666}"
    UpgradeCode="{A1B2C3D4-1111-2222-3333-444455557777}">

  <CompanyDetails Name="Your Company" Url="https://example.com" Email="hi@example.com" />

  <Components Description="AutoCAD 2021-2026">
    <RuntimeRequirements OS="Win64" Platform="AutoCAD*"
                         SeriesMin="R24.0" SeriesMax="R25.1" />
    <ComponentEntry AppName="MyPlugin" Version="1.0.0"
                    ModuleName="./Contents/Windows/MyPlugin.dll"
                    AppType=".NET"
                    LoadOnAutoCADStartup="True">
      <Commands GroupName="MY_PLUGIN">
        <Command Global="DIMALL" Local="DIMALL" />
        <Command Global="EXPORTLIST" Local="EXPORTLIST" />
      </Commands>
    </ComponentEntry>
  </Components>
</ApplicationPackage>
```

两个容易踩的点：

- `SeriesMin/SeriesMax` 必须覆盖目标 CAD 版本，写死了会导致新版本不加载。
- `Commands` 里的命令名要和代码里的 `[CommandMethod]` 完全一致，否则第一次输入命令会报「未知命令」。

安装脚本只需把 `.bundle` 目录复制到上述路径，卸载时删掉即可，不需要管理员权限（用户级安装）。

## 🔀 多版本兼容策略

这是商业化插件最麻烦的部分。AutoCAD 每年一个大版本，每次都可能改 API 签名。

常见做法：

1. **按版本编译多份 DLL**，用 `#if ACAD2020 ... #endif` 或独立 csproj 分支，在 `PackageContents.xml` 里用多个 `ComponentEntry` 按 `SeriesMin/SeriesMax` 分发。
2. **反射兜底**：对变化频繁的 API 用反射调用，编译期不绑定。
3. **最小依赖**：只依赖 `accoremgd/acdbmgd/acmgd` 的稳定子集，避免用内部未公开接口。
4. **国产 CAD 迁移**：中望、浩辰的 .NET API 与 AutoCAD 高度相似但不完全一致，把命令层单独抽出、用接口隔离，迁移时只重写这一层。

## 🐞 调试、测试与常见坑

### 单元测试怎么写

把业务逻辑放在不引用 CAD 程序集的 `Services` 里，纯输入输出，直接用 xUnit/NUnit 测。涉及 `Point3d` 这类结构体时，可以自己定义轻量 `Pt` 结构体在边界转换，或引一个几何抽象层。

### 高频坑位清单

| 现象 | 原因 | 处理 |
| --- | --- | --- |
| `eLockViolation` | 未锁文档 | `doc.LockDocument()` |
| `eNotOpenForWrite` | 用 ForRead 就改属性 | `UpgradeOpen()` |
| 对象保存后消失 | 漏 `AddNewlyCreatedDBObject` | 补上并 `Commit` |
| 命令重复注册报错 | 多次 NETLOAD 同一 DLL | 用 `[assembly: ExtensionApplication]` 幂等初始化，或重启 CAD |
| 大图纸卡死 | 全表 ForWrite 打开、循环里反复 `StartTransaction` | 一个事务批量处理，只读用 ForRead |
| 中文乱码 | 文件编码 / 字体 | 源码 UTF-8，`ed.WriteMessage` 前确认命令行字体 |
| 卸载后 DLL 被占用 | CAD 未退出，Assembly 被加载 | 属正常现象，提示用户重启 CAD |

### 性能优化的几个杠杆

- 事务合并：一个事务处理 N 个对象，而不是 N 个事务。
- 关闭屏幕刷新：大批量绘制时 `doc.Editor.UpdateScreen()` 只在最后调一次，或临时 `Application.SetSystemVariable("REGENMODE", 0)`。
- 避免 `SelectAll` 后遍历全库：用过滤器 `SelectionFilter` 在 CAD 侧就筛掉不需要的对象。
- 字符串拼接用 `StringBuilder`，命令回显不要每个对象打一行。

## 🌱 拓展到其他平台

- **Revit**：模型是参数化构件而非图元，插件核心是 `IExternalCommand` + `FilteredElementCollector`，思路与 AutoCAD 类似但对象模型完全不同。
- **Rhino**：`RhinoCommon` 的几何库非常好用，Python 脚本可以直接跑，适合做参数化工具和批量建模。
- **FreeCAD**：纯 Python 工作台，一个 `Init.py` + `InitGui.py` 就是插件，是最好的学习起点，也是零成本跨平台的方案。
- **中望 / 浩辰**：LISP 基本兼容，.NET API 命名接近 AutoCAD，多数插件改改引用就能跑。

## 🚀 交付：从代码到产品

一个能给别人用的插件，至少还要这几样东西：

1. **安装包**：Inno Setup 或 NSIS，做「复制 bundle → 写注册表 → 创建快捷方式」三步。带上静默安装参数，方便企业批量部署。
2. **许可与试用**：离线机器可用「硬件指纹 + 授权文件」，联网场景用 License 服务校验。别做过度加密，用户被折腾走比被破解损失更大。
3. **自动更新**：启动时请求版本接口，有新版则下载新 bundle 到临时目录，下次启动前替换。
4. **日志**：全局异常捕获写文件，路径放在 `%LOCALAPPDATA%`，用户反馈问题时要日志不要截图。
5. **文档与命令表**：一页纸的命令清单 + GIF 演示，比二十页手册有效。

## ✅ 上手路线（建议照这个顺序）

1. 用 LISP 写三个小工具，找找 CAD 数据操作的手感。
2. 用 C# 复刻其中一个，跑通 `NETLOAD` 和断点调试。
3. 加上 Ribbon 按钮和非模态面板，做成完整交互。
4. 打包成 `.bundle`，装到另一台没装 VS 的电脑上验证。
5. 找一个真实重复劳动，从头做一遍完整流程——**只有真需求才能逼出工程细节**。

写完第一个插件后你会发现，难的不是 API，而是把「人怎么画图」翻译成「程序怎么判断」。这部分没有捷径，只能多看图纸、多问画图的人。
