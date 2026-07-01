# Dendro

## English

Dendro is a volumetric modeling plug-in for Grasshopper 3D built on top of [OpenVDB](http://www.openvdb.org/). It provides components for wrapping points, curves, and meshes as volumetric data inside Grasshopper, then applying operations such as booleans, smoothing, offsets, morphing, file I/O, and mesh conversion.

More information and released builds are available on [Food4Rhino](https://www.food4rhino.com/app/dendro).

### Design

Dendro was created to make OpenVDB workflows feel native inside Grasshopper. Instead of treating voxel volumes as isolated data trapped inside a bounding box workflow, Dendro lets users move between Rhino geometry, Grasshopper data, and volumetric operations with as little friction as possible.

The repository contains two main projects:

* `DendroAPI`: a native C++ library that wraps OpenVDB operations.
* `DendroGH`: a C# Grasshopper plug-in that exposes the components and calls into `DendroAPI.dll`.

### Windows / Rhino 8 Build

The Windows build has been updated and verified for Rhino 8. The C# plug-in targets both Rhino 8 runtime options:

* `net7.0-windows` for Rhino 8's .NET Core runtime.
* `net48` for Rhino 8's .NET Framework runtime option.

The Rhino SDK references are pulled from NuGet packages:

* `RhinoCommon` `8.32.26160.13001`
* `Grasshopper` `8.32.26160.13001`

#### Required Tools

* Rhino 8 for Windows.
* Visual Studio or Build Tools for Visual Studio with the Desktop development with C++ workload.
* vcpkg. The project has been verified with the vcpkg bundled under Visual Studio at `E:\Microsoft Visual Studio\18\Community\VC\vcpkg`.
* .NET SDK.

Visual Studio C++ tools usually do not appear in a normal PowerShell `PATH`. Build the native project from a Visual Studio Developer Command Prompt, or run `VsDevCmd.bat` first:

```bat
call "E:\Microsoft Visual Studio\18\Community\Common7\Tools\VsDevCmd.bat" -arch=x64 -host_arch=x64
```

Adjust the path if Visual Studio is installed somewhere else.

#### Build the Full Solution

From the repository root:

```bat
msbuild Dendro.sln /m /p:Configuration=Release /p:Platform=x64 /v:minimal
```

The solution builds `DendroAPI` first, then `DendroGH`, so the generated `DendroAPI.dll` is copied into the Grasshopper output folders.

#### Build Only the Grasshopper Plug-in

If `DendroAPI.dll` has already been built, the C# plug-in can be rebuilt with:

```bat
dotnet restore DendroGH\DendroGH.csproj
dotnet build DendroGH\DendroGH.csproj -c Release
```

#### Windows Output Files

The verified release outputs are:

* `x64\Release\DendroAPI.dll`
* `x64\Release\net7.0-windows\DendroGH.gha`
* `x64\Release\net7.0-windows\DendroAPI.dll`
* `x64\Release\net48\DendroGH.gha`
* `x64\Release\net48\DendroAPI.dll`

Copy the `.gha` and the matching `DendroAPI.dll` from the same output folder into your Grasshopper Libraries folder.

#### Notes

* The first vcpkg/OpenVDB build can take a long time because OpenVDB pulls and builds a large native dependency graph, including Boost, TBB, OpenEXR, Imath, Blosc, zlib, zstd, lz4, and related packages.
* The C++ build may emit numeric conversion warnings from the existing native code. These warnings do not currently block the verified release build.
* Rhino 8 compatibility scanning has been verified with Rhino's `compat.exe` for the generated Grasshopper assembly.

### macOS Build

The repository still contains the existing macOS-oriented project files and CMake workflow.

Install native dependencies with Homebrew:

```bash
brew install boost cmake c-blosc openvdb tbb zlib
```

Build `DendroAPI` from the `DendroAPI` directory:

```bash
mkdir build
cd build
cmake ..
make
```

Build the C# project:

```bash
dotnet build DendroGH.Mac.csproj
```

## 中文

Dendro 是一个基于 [OpenVDB](http://www.openvdb.org/) 的 Grasshopper 3D 体积建模插件。它可以在 Grasshopper 中将点、曲线和网格转换为体积数据，并提供布尔运算、平滑、偏移、形变、文件读写和网格转换等组件。

更多信息和发布版本可以在 [Food4Rhino](https://www.food4rhino.com/app/dendro) 查看。

### 设计目标

Dendro 的目标是让 OpenVDB 工作流在 Grasshopper 中尽量接近原生体验。很多体素/体积工具会让用户围绕包围盒和孤立数据结构来思考，而 Dendro 希望用户可以自然地在 Rhino 几何、Grasshopper 数据和体积操作之间来回切换。

本仓库包含两个主要项目：

* `DendroAPI`：封装 OpenVDB 操作的原生 C++ 库。
* `DendroGH`：Grasshopper C# 插件，负责提供组件并调用 `DendroAPI.dll`。

### Windows / Rhino 8 构建

Windows 构建已更新并验证可用于 Rhino 8。C# 插件同时面向 Rhino 8 的两种运行时选项：

* `net7.0-windows`：用于 Rhino 8 的 .NET Core 运行时。
* `net48`：用于 Rhino 8 的 .NET Framework 运行时选项。

Rhino SDK 通过 NuGet 引用：

* `RhinoCommon` `8.32.26160.13001`
* `Grasshopper` `8.32.26160.13001`

#### 所需工具

* Rhino 8 for Windows。
* Visual Studio 或 Visual Studio Build Tools，并安装 Desktop development with C++ workload。
* vcpkg。本项目已使用 Visual Studio 自带的 vcpkg 验证，路径为 `E:\Microsoft Visual Studio\18\Community\VC\vcpkg`。
* .NET SDK。

Visual Studio C++ 工具通常不会出现在普通 PowerShell 的 `PATH` 中。构建原生项目时，请使用 Visual Studio Developer Command Prompt，或者先运行 `VsDevCmd.bat`：

```bat
call "E:\Microsoft Visual Studio\18\Community\Common7\Tools\VsDevCmd.bat" -arch=x64 -host_arch=x64
```

如果 Visual Studio 安装在其他位置，请相应调整路径。

#### 构建完整解决方案

在仓库根目录执行：

```bat
msbuild Dendro.sln /m /p:Configuration=Release /p:Platform=x64 /v:minimal
```

解决方案会先构建 `DendroAPI`，再构建 `DendroGH`，因此生成的 `DendroAPI.dll` 会被复制到 Grasshopper 输出目录。

#### 只构建 Grasshopper 插件

如果 `DendroAPI.dll` 已经构建完成，可以只重新构建 C# 插件：

```bat
dotnet restore DendroGH\DendroGH.csproj
dotnet build DendroGH\DendroGH.csproj -c Release
```

#### Windows 输出文件

已验证的发布产物包括：

* `x64\Release\DendroAPI.dll`
* `x64\Release\net7.0-windows\DendroGH.gha`
* `x64\Release\net7.0-windows\DendroAPI.dll`
* `x64\Release\net48\DendroGH.gha`
* `x64\Release\net48\DendroAPI.dll`

请将同一个输出目录中的 `.gha` 和对应的 `DendroAPI.dll` 一起复制到 Grasshopper Libraries 文件夹。

#### 注意事项

* 首次 vcpkg/OpenVDB 构建可能耗时较长，因为 OpenVDB 会拉取并编译较大的原生依赖图，包括 Boost、TBB、OpenEXR、Imath、Blosc、zlib、zstd、lz4 等。
* C++ 构建目前可能会输出已有原生代码中的数值转换警告，这些警告不会阻塞已验证的 Release 构建。
* 已使用 Rhino 8 的 `compat.exe` 对生成的 Grasshopper 程序集进行兼容性扫描，扫描通过。

### macOS 构建

仓库中仍保留了现有的 macOS 项目文件和 CMake 构建流程。

使用 Homebrew 安装原生依赖：

```bash
brew install boost cmake c-blosc openvdb tbb zlib
```

在 `DendroAPI` 目录中构建 `DendroAPI`：

```bash
mkdir build
cd build
cmake ..
make
```

构建 C# 项目：

```bash
dotnet build DendroGH.Mac.csproj
```
