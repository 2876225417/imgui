Dear ImGui
=====

<center><b><i>"Give someone state and they'll have a bug one day, but teach them how to represent state in two separate locations that have to be kept in sync and they'll have bugs for a lifetime."</i></b></center> <a href="https://twitter.com/rygorous/status/1507178315886444544">-ryg</a>

----

[![Build Status](https://github.com/ocornut/imgui/workflows/build/badge.svg)](https://github.com/ocornut/imgui/actions?workflow=build) [![Static Analysis Status](https://github.com/ocornut/imgui/workflows/static-analysis/badge.svg)](https://github.com/ocornut/imgui/actions?workflow=static-analysis) [![Tests Status](https://github.com/ocornut/imgui_test_engine/workflows/tests/badge.svg)](https://github.com/ocornut/imgui_test_engine/actions?workflow=tests)


### The Pitch

Dear ImGui 是一个用于 C++ 的无臃肿图形用户界面库。它输出优化的顶点缓冲，您可以在启用了 3D 渲染管线的应用程序中随时渲染这些顶点缓冲。它快速、可移植、渲染器无关并且自包含（没有外部依赖）。

Dear ImGui 旨在实现快速迭代并助力程序员创建内容创作工具和可视化/调试工具（而不是为普通最终用户设计的用户界面）。为了实现这一目标，它侧重于简洁性和生产力，因此缺乏一些在更高级别库中常见的功能。除此之外，它不支持完整的国际化（例如从右到左的文本、双向文本、文本塑形等）和无障碍功能。

Dear ImGui 特别适用于集成到游戏引擎中（用于工具开发）、实时 3D 应用、全屏应用、嵌入式应用，或任何操作系统功能非标准的控制台平台上的应用。

 - 减少状态同步流程。
 - 减少用户侧 UI 相关的状态。
 - 减少开发和维护的流程。
 - 易于用于创建反映动态数据集的动态 UI。
 - 易于用于创建代码驱动和数据驱动的工具。
 - 易于用于创建即席的、短生命周期的工具以及长期的、更复杂的工具。
 - 易于修改和改进。
 - 可移植，依赖最少，可在目标设备（如游戏主机、手机等）上运行。
 - 高效的运行时性能和内存占用。
 - 经过实战检验，被[游戏行业的许多主要参与者](https://github.com/ocornut/imgui/wiki/Software-using-dear-imgui)使用。

### Usage
Dear ImGui 的核心代码是自包含的，仅由少量与平台无关的文件组成，您可以轻松地在您的应用程序/引擎中编译它们。这些文件是仓库根目录下的所有文件（imgui*.cpp, imgui*.h）。不需要特定的构建过程。您可以将 .cpp 文件添加到您现有的项目中。

在 backends/ 文件夹中提供了适用于各种图形 API 和渲染平台的后端，同时在 examples/ 文件夹中提供了示例应用程序。您也可以创建自己的后端。任何可以渲染带纹理三角形的地方，都可以渲染 Dear ImGui。

更多详情请参阅本文档的入门与集成部分。

在您的应用程序中设置好 Dear ImGui 后，您可以在程序循环的_任何_地方使用它：

```cpp
ImGui::Text("你好, 世界 %d", 123);
if (ImGui::Button("保存"))
    MySaveFunction(); // 我的保存函数
ImGui::InputText("字符串", buf, IM_ARRAYSIZE(buf));
ImGui::SliderFloat("浮点数", &f, 0.0f, 1.0f);
```
![sample code output (dark, segoeui font, freetype)](https://user-images.githubusercontent.com/8225057/191050833-b7ecf528-bfae-4a9f-ac1b-f3d83437a2f4.png)
![sample code output (light, segoeui font, freetype)](https://user-images.githubusercontent.com/8225057/191050838-8742efd4-504d-4334-a9a2-e756d15bc2ab.png)


```cpp
// 创建一个名为“我的第一个工具”的窗口，并带有菜单栏。
ImGui::Begin("我的第一个工具", &my_tool_active, ImGuiWindowFlags_MenuBar);
if (ImGui::BeginMenuBar())
{
    if (ImGui::BeginMenu("文件"))
    {
        if (ImGui::MenuItem("打开..", "Ctrl+O")) { /* 执行操作 */ }
        if (ImGui::MenuItem("保存", "Ctrl+S"))   { /* 执行操作 */ }
        if (ImGui::MenuItem("关闭", "Ctrl+W"))  { my_tool_active = false; }
        ImGui::EndMenu();
    }
    ImGui::EndMenuBar();
}

// 编辑一个存储为4个浮点数的颜色
ImGui::ColorEdit4("颜色", my_color);

// 生成采样点并绘制它们
float samples[100];
for (int n = 0; n < 100; n++)
    samples[n] = sinf(n * 0.2f + ImGui::GetTime() * 1.5f);
ImGui::PlotLines("采样点", samples, 100);

// 在滚动区域显示内容
ImGui::TextColored(ImVec4(1,1,0,1), "重要内容");
ImGui::BeginChild("Scrolling"); // 开始滚动区域
for (int n = 0; n < 50; n++)
    ImGui::Text("%04d: 一些文本", n);
ImGui::EndChild(); // 结束滚动区域
ImGui::End();
```

![my_first_tool_v188](https://user-images.githubusercontent.com/8225057/191055698-690a5651-458f-4856-b5a9-e8cc95c543e2.gif)


Dear ImGui 允许您创建精密的工具以及非常临时的工具。对于极其临时的场景：利用现代编译器的“编辑并继续”（热代码重载）功能，您可以在应用程序运行时添加一些小部件来调整变量，然后在几分钟后移除这些代码！Dear ImGui 不仅仅用于调整数值。您可以通过仅发出文本命令来用它跟踪正在运行的算法。您可以将它与您自己的反射数据结合使用，以实时浏览您的数据集。您可以用它来暴露引擎中子系统的内部结构，创建日志记录器、检查工具、性能分析器、调试器、一个完整的游戏制作编辑器/框架等。

### How it works

工作原理
从用户的角度来看，IMGUI 范式通过其 API 试图最小化多余的状态复制、状态同步和状态保留。与传统的保留模式 (Retained Mode) 界面相比，它更不容易出错（代码更少，bug 更少），并且非常适合创建动态用户界面。更多详情请查阅 Wiki 中的关于 IMGUI 范式部分。

Dear ImGui 输出顶点缓冲和命令列表，您可以轻松地在您的应用程序中渲染它们。渲染它们所需的绘制调用 (draw calls) 和状态更改的数量相当少。因为 Dear ImGui 不直接了解或接触图形状态，所以您可以在代码中的任何位置调用其函数（例如，在正在运行的算法中间，或者在您自己的渲染流程中间）。有关如何将 Dear ImGui 与您现有代码库集成的说明，请参阅 examples/ 文件夹中的示例应用程序。

一个常见的误解是，将立即模式 GUI (Immediate Mode GUI) 错误地理解为立即模式渲染 (Immediate Mode Rendering)，后者通常意味着在调用 GUI 函数时，会用大量低效的绘制调用和状态更改来冲击您的驱动程序/GPU。Dear ImGui 并非如此。Dear ImGui 输出的是顶点缓冲和一个简短的绘制调用批处理列表。它从不直接接触您的 GPU。这些绘制调用批处理经过了适当的优化，您可以稍后在您的应用程序中甚至远程渲染它们。

### Releases & Changelogs

请参阅[版本发布](https://github.com/ocornut/imgui/releases)页面查看详细的更新日志。
阅读更新日志是了解 Dear ImGui 最新功能的好方法，也许还能让您发现一些至今忽略了的特性！

### 演示

调用 `ImGui::ShowDemoWindow()` 函数将创建一个演示窗口，展示各种功能和示例。其代码始终可以在 `imgui_demo.cpp` 中找到以供参考。[这里是演示窗口的样子](https://raw.githubusercontent.com/wiki/ocornut/imgui/web/v167/v167-misc.png)。

您应该能够从源代码构建这些示例。如果不行，请告诉我们！如果您想快速浏览 Dear ImGui 的一些功能，可以在此处下载演示应用程序的 Windows 二进制文件：
- [imgui-demo-binaries-20241211.zip](https://www.dearimgui.com/binaries/imgui-demo-binaries-20241211.zip) (Windows, 1.91.6, 构建于 2024/11/11, master 分支) 或 [更早的二进制文件](https://www.dearimgui.com/binaries)。

演示应用程序不支持 DPI 感知，因此在 4K 屏幕上可能会有些模糊。要在您的应用程序中实现 DPI 感知，您可以以不同的缩放比例加载/重新加载字体，并使用 `style.ScaleAllSizes()` 缩放您的样式（请参阅[常见问题解答](https://www.dearimgui.com/faq)）。

好的，这是该部分内容的中文翻译：

### Getting Started & Integration

详情请参阅[入门指南](https://github.com/ocornut/imgui/wiki/Getting-Started)。

在大多数平台上以及使用 C++ 时，**您应该能够直接使用 [imgui_impl_xxxx](https://github.com/ocornut/imgui/tree/master/backends) 后端的组合，无需修改**（例如 `imgui_impl_win32.cpp` + `imgui_impl_dx11.cpp`）。如果您的引擎支持多个平台，请考虑使用更多的 imgui_impl_xxxx 文件，而不是重写它们：这将为您减少工作量，并且您可以立即让 Dear ImGui 运行起来。如果您愿意，可以*稍后*决定使用您的自定义引擎函数重写一个自定义后端。

在您的自定义引擎中集成 Dear ImGui 主要涉及以下几点：1) 连接鼠标/键盘/游戏手柄输入；2) 上传纹理到您的 GPU/渲染引擎；3) 提供一个可以绑定纹理并渲染带纹理三角形的渲染函数，这基本上就是后端所做的事情。[examples/](https://github.com/ocornut/imgui/tree/master/examples) 文件夹中充满了正是这样做的应用程序：设置一个窗口并使用后端。如果您遵循[入门指南](https://github.com/ocornut/imgui/wiki/Getting-Started)，理论上集成 Dear ImGui 不会超过一个小时。**请务必花时间阅读[常见问题解答](https://www.dearimgui.com/faq)、代码注释和示例应用程序！**

官方维护的后端/绑定 (仓库中)：
- 渲染器 (Renderers): DirectX9, DirectX10, DirectX11, DirectX12, Metal, OpenGL/ES/ES2, SDL_GPU, SDL_Renderer2/3, Vulkan, WebGPU.
- 平台 (Platforms): GLFW, SDL2/SDL3, Win32, Glut, OSX, Android.
- 框架 (Frameworks): Allegro5, Emscripten.

[第三方后端/绑定](https://github.com/ocornut/imgui/wiki/Bindings) Wiki 页面：
- 语言 (Languages): C, C# 以及: Beef, ChaiScript, CovScript, Crystal, D, Go, Haskell, Haxe/hxcpp, Java, JavaScript, Julia, Kotlin, Lobster, Lua, Nim, Odin, Pascal, PureBasic, Python, ReaScript, Ruby, Rust, Swift, Zig...
- 框架 (Frameworks): AGS/Adventure Game Studio, Amethyst, Blender, bsf, Cinder, Cocos2d-x, Defold, Diligent Engine, Ebiten, Flexium, GML/Game Maker Studio, GLEQ, Godot, GTK3, Irrlicht Engine, JUCE, LÖVE+LUA, Mach Engine, Magnum, Marmalade, Monogame, NanoRT, nCine, Nim Game Lib, Nintendo 3DS/Switch/WiiU (homebrew), Ogre, openFrameworks, OSG/OpenSceneGraph, Orx, Photoshop, px_render, Qt/QtDirect3D, raylib, SFML, Sokol, Unity, Unreal Engine 4/5, UWP, vtk, VulkanHpp, VulkanSceneGraph, Win32 GDI, WxWidgets.
- 许多绑定是自动生成的（通过经典的 [cimgui](https://github.com/cimgui/cimgui) 或较新的/实验性的 [dear_bindings](https://github.com/dearimgui/dear_bindings)），您可以使用它们的元数据输出来为其他语言生成绑定。

[有用的扩展/小部件](https://github.com/ocornut/imgui/wiki/Useful-Extensions) Wiki 页面：
- 自动化/测试、文本编辑器、节点编辑器、时间轴编辑器、绘图、软件渲染器、远程网络访问、内存编辑器、Gizmos 等。知名且支持良好的扩展包括 [ImPlot](https://github.com/epezent/implot) 和 [Dear ImGui Test Engine](https://github.com/ocornut/imgui_test_engine)。

另请参阅 [Wiki](https://github.com/ocornut/imgui/wiki) 获取更多链接和想法。

好的，这是这些部分的中文翻译：

### 支持与常见问题解答 (FAQ)

请参阅：[常见问题解答 (FAQ)](https://github.com/ocornut/imgui/blob/master/docs/FAQ.md)，其中回答了一些常见问题。

请参阅：[入门指南](https://github.com/ocornut/imgui/wiki/Getting-Started) 和 [Wiki](https://github.com/ocornut/imgui/wiki) 获取许多链接、参考资料和文章。

请参阅：[关于 IMGUI 范式的文章](https://github.com/ocornut/imgui/wiki#about-the-imgui-paradigm) 以阅读/学习关于立即模式 GUI (Immediate Mode GUI) 范式的内容。

请参阅：[即将发生的变更](https://github.com/ocornut/imgui/wiki/Upcoming-Changes)。

请参阅：[Dear ImGui Test Engine + 测试套件](https://github.com/ocornut/imgui_test_engine) 用于自动化和测试。

为了让搜索引擎能够抓取 wiki 内容，这里有一个指向[可抓取 Wiki](https://github-wiki-see.page/m/ocornut/imgui/wiki) 的链接（不适合人类阅读，[原因在此](https://github-wiki-see.page/)）。

刚开始使用？对于初次使用的用户，如果遇到编译/链接/运行问题或加载字体问题，请使用 [GitHub Discussions](https://github.com/ocornut/imgui/discussions)。对于任何其他问题、错误报告、功能请求、反馈，请发布在 [GitHub Issues](https://github.com/ocornut/imgui/issues)。请仔细阅读并填写新 Issue 的模板。

付费商业客户可获得私人支持（电子邮件：*contact @ dearimgui dot com*）。

**我应该获取哪个版本？**

我们偶尔会标记[版本发布](https://github.com/ocornut/imgui/releases)（附有详细的发布说明），但通常建议并安全地同步到最新的 `master` 或 `docking` 分支。该库相当稳定，报告的回归问题往往会很快得到修复。高级用户可能希望使用带有[多视口 (Multi-Viewport)](https://github.com/ocornut/imgui/wiki/Multi-Viewports) 和[停靠 (Docking)](https://github.com/ocornut/imgui/wiki/Docking) 功能的 `docking` 分支。该分支会定期与 master 分支保持同步。

**谁在使用 Dear ImGui？**

请参阅 [评价](https://github.com/ocornut/imgui/wiki/Quotes)、[资金与赞助商](https://github.com/ocornut/imgui/wiki/Funding) 和 [使用 Dear ImGui 的软件](https://github.com/ocornut/imgui/wiki/Software-using-dear-imgui) Wiki 页面，了解哪些人正在使用 Dear ImGui。如果可以，请添加您的游戏/软件！另外，也请查看[相册主题帖 (Gallery Threads)](https://github.com/ocornut/imgui/issues?q=label%3Agallery)！

## 如何提供帮助

**我能如何提供帮助？**

  - 查看 [GitHub 论坛/Issues](https://github.com/ocornut/imgui/issues)。
  - 您可以帮助开发并提交拉取请求 (pull requests)！请理解，提交 PR 的同时也意味着您请求维护者审查您的代码，并在此后永久接管其维护。PR 的构建应同时考虑最终用户的利益，并易于维护者理解和接受。
  - 查看 [Wiki](https://github.com/ocornut/imgui/wiki/) 上的 [需要帮助 (Help wanted)](https://github.com/ocornut/imgui/wiki/Help-Wanted) 获取更多想法。
  - 成为一名[资金支持者 (Funding Supporter)](https://github.com/ocornut/imgui/wiki/Funding)！让您的公司通过带发票的赞助/维护合同，或购买 [Dear ImGui Test Engine](https://github.com/ocornut/imgui_test_engine) 的许可证来为该项目提供财务支持（请联系：omar AT dearimgui DOT com）。

## 赞助商

Dear ImGui 的持续开发一直得到用户和私人赞助商的财务支持。
有关当前和过去 Dear ImGui 资金支持者和赞助商的[详细列表](https://github.com/ocornut/imgui/wiki/Funding)，请参阅详情。
从 2014 年 11 月到 2019 年 12 月，持续的开发也得到了其用户在 Patreon 上以及通过个人捐赠的财务支持。

**感谢所有过去和现在的支持者，帮助这个项目保持活力和繁荣！**

Dear ImGui 使用了以下免费为开源项目提供的软件和服务：

  - [PVS-Studio](https://pvs-studio.com/en/pvs-studio/?utm_source=website&utm_medium=github&utm_campaign=open_source) 用于静态分析 (支持 C/C++/C\#/Java)。
  - [GitHub actions](https://github.com/features/actions) 用于持续集成系统。
  - [OpenCppCoverage](https://github.com/OpenCppCoverage/OpenCppCoverage) 用于代码覆盖率分析。

## 致谢

由 [Omar Cornut](https://www.miracleworld.net) 以及 GitHub 上每一位直接或间接的[贡献者](https://github.com/ocornut/imgui/graphs/contributors)开发。该库的早期版本是在 [Media Molecule](https://www.mediamolecule.com) 的支持下开发的，并首次在游戏 [Tearaway](https://tearaway.mediamolecule.com) (PS Vita) 内部使用。

经常性贡献者包括 Rokas Kupstys [@rokups](https://github.com/rokups) (2020-2022)：他在自动化系统和回归测试方面做了大量工作，这些现在已集成到 [Dear ImGui Test Engine](https://github.com/ocornut/imgui_test_engine) 中。

维护/支持合同、赞助发票和其他 B2B 交易由 [Disco Hello](https://www.discohello.com) 托管和处理。

Omar：“我最初是在 [Q-Games](https://www.q-games.com) 发现了 IMGUI 范式，当时 Atman Binstock 在代码库中加入了他自己的简单实现，我花了很多时间改进和思考它。后来发现 Atman 是通过与 Casey 一起工作直接接触到这个概念的。当我搬到 Media Molecule 后，我重写了一个新的库，试图克服我之前用过的第一个库的缺陷和局限性。它就成了现在的这个库，从那时起，我花了大量的时间迭代和改进它。”

内嵌了 Tristan Grimmer 的 [ProggyClean.ttf](https://www.proggyfonts.net) 字体 (MIT 许可证)。
内嵌了 Sean Barrett 的 [stb\_textedit.h, stb\_truetype.h, stb\_rect\_pack.h](https://github.com/nothings/stb/) (公共领域)。

早期版本的灵感、反馈和测试来自：Casey Muratori, Atman Binstock, Mikko Mononen, Emmanuel Briney, Stefan Kamoda, Anton Mikhailov, Matt Willis。同时也感谢在 GitHub 上发布反馈、问题和补丁的每一个人。

## 许可证

Dear ImGui 根据 MIT 许可证授权，更多信息请参阅 [LICENSE.txt](https://github.com/ocornut/imgui/blob/master/LICENSE.txt)。