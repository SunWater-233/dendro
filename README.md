# Dendro
Dendro is a volumetric modeling plug-in for Grasshopper-3D built on top of the OpenVDB library. It provides multiple ways to wrap points, curves, and meshes as a volumetric data within Grasshopper-3D, allowing you to perform various operations on those volumes. Dendro includes components for boolean, smoothing, offsets, and morphing operations. You can find out more details of its features and download a working version [here](https://www.food4rhino.com/app/dendro)

## Design

I have been using the OpenVDB library for a couple years, but needed something to prototype quicker with. I had built a rough version of this for Grasshopper-3D, but decided to package it up nicer and put a release together. Hopefully it is something to build upon and the hope was it could serve as a starting point to add more features and functionality to.

The goal was to make Dendro integrate into Grasshopper-3D as seamlessly as possible. Whereas many voxel solutions require you to think of geometry as living with a bounding box, Dendro makes working with volumes no different than handling any other geometry in Grasshopper-3D. Dendro works with many native Grasshopper-3D components, avoiding the 'blocking' found in other plugins, and allowing you to move in and out of volume operations very quickly.

## Installation

Dendro contains two projects, a C++ project for working with OpenVDB and a C# project creating the Grasshopper-3D plugin.

### DendroAPI (C++)
OpenVDB and all its dependencies are added to the supplied VCPKG manifest. Upon build, it should automatically download and install everything required.

##### DendroAPI (C++) on Windows

Rhino 8 for Windows is 64-bit, so build the native library with the `Release|x64` solution configuration. The Visual Studio C++ project is configured for the current Visual Studio C++ toolset, C++17, and the `x64-windows-static` vcpkg triplet.

Required tools:

* Visual Studio or Build Tools for Visual Studio with the Desktop development with C++ workload
* vcpkg with Visual Studio integration enabled

The native output is expected at `x64\Release\DendroAPI.dll`.

##### DendroAPI (C++) on MacOS

Use Homebrew (`brew`) to install the dependencies for the C++ library:

```
brew install boost cmake c-blosc openvdb tbb zlib
```

Run the following from the DendroAPI directory to compile using `cmake`:

```
mkdir build
cd build
cmake ..
make
```

### DendroGH (C#)
Since there are multiple versions of Rhino, each with their specific SDK, the Rhinocommon and Grasshopper-3D libraries are referenced as NuGet packages. The Windows project is configured for Rhino 8.32 by default and multi-targets `net48` and `net7.0-windows`, matching Rhino 8's .NET Framework and .NET Core runtime options on Windows.

To build only the Grasshopper plug-in on Windows:

```
dotnet restore DendroGH\DendroGH.csproj
dotnet build DendroGH\DendroGH.csproj -c Release
```

The Grasshopper outputs are written to `x64\Release\net48\DendroGH.gha` and `x64\Release\net7.0-windows\DendroGH.gha`. Put the `.gha` and the matching `DendroAPI.dll` in the same Grasshopper library folder.

##### DendroGH (C#) on MacOS

* Install .Net Core (currently v8, v6+ is required for Apple Silicon) via brew: `brew install dotnet`
* Compile with `dotnet build DendroGH.Mac.csproj`

## Building

Dendro was built using Microsoft Visual Studio. Make sure to build the solution as `Release|x64`. You will need to bring `DendroGH.gha` and `DendroAPI.dll` into your Grasshopper library folder.

## More Info

Dendro is using OpenVDB. For more information on the library, please visit [here](http://www.openvdb.org/).

