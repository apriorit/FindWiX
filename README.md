# FindWiX [![Build status](https://ci.appveyor.com/api/projects/status/4iagwdkceft3fb0o?svg=true)](https://ci.appveyor.com/project/sqzhr/findwix-73k2b)

[CMake](https://cmake.org) module for building [Windows Installer](https://en.wikipedia.org/wiki/Windows_Installer) packages with [WiX toolset](http://wixtoolset.org)
* [Introduction](#introduction)
* [Usage](#usage)
* [Samples](#samples) 
* [License](#license) 
* [Version History](#version-history)

# Introduction
A native solution for building [Windows Installer](https://en.wikipedia.org/wiki/Windows_Installer) packages in [CMake](https://cmake.org) is [CPack](https://cmake.org/cmake/help/v3.0/module/CPack.html). However it has several drawbacks:
- limited to one installer project (cannot created several installers, for example `client.msi` and `server.msi`)
- cannot directly work with `wsx` files (hard to convert existings installer source code to [CMake](https://cmake.org))

[FindWiX](https://github.com/apriorit/FindWiX) comes to rescue in such cases.

##### Requirements:
- [CMake 3.0](https://cmake.org/download/) or higher
- [WiX toolset 3.0](http://wixtoolset.org/releases/) or higher

# Usage
## find_package()
Add [FindWiX](https://github.com/apriorit/FindWiX) to the module search path and call `find_package`:
```cmake
list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_LIST_DIR}/cmake")
find_package(WIX REQUIRED)
```
[FindWiX](https://github.com/apriorit/FindWiX) will search for the installed [WiX toolset](http://wixtoolset.org), expose functions for creating installer packages and define the following variables:
- `WIX_FOUND` - if false [WiX toolset](http://wixtoolset.org) is absent
- `WIX_ROOT` - path where [WiX toolset](http://wixtoolset.org) is installed
- `WIX_COMPILE_FLAGS` - flags to be used when compiling `wxs` source files
- `WIX_LINK_FLAGS` - flags to be used when linking `wixobj` files

## WiX compile and link flags
You could do some fine tuning, for example treat warnings as errors:
```cmake
set(WIX_COMPILE_FLAGS ${WIX_COMPILE_FLAGS} -wx)
set(WIX_LINK_FLAGS ${WIX_LINK_FLAGS} -wx)
```

## CMake variables in WiX
[FindWiX](https://github.com/apriorit/FindWiX) generates `vars.wxi` to make [CMake](https://cmake.org) variables available in [WiX toolset](http://wixtoolset.org). Here is a fragment of `vars.wxi`:
```xml
<?xml version='1.0' encoding='UTF-8'?>
<Include>
    <?define ARGC='4' ?>
    <?define ARGN='Main.wxs;DEPENDS;CppExecutable' ?>
    <?define ARGV='WithExecutable;Main.wxs;DEPENDS;CppExecutable' ?>
    <?define ARGV0='WithExecutable' ?>
    <?define ARGV1='Main.wxs' ?>
    <?define ARGV2='DEPENDS' ?>
    <?define ARGV3='CppExecutable' ?>
    <?define CMAKE_AR='' ?>
    <?define CMAKE_AUTOMOC_COMPILER_PREDEFINES='ON' ?>
    <?define CMAKE_AUTOMOC_MACRO_NAMES='Q_OBJECT;Q_GADGET;Q_NAMESPACE' ?>
</Include>
```
To get access to those variables include `vars.wxi` into your `wxs` file:
```xml
<?include vars.wxi?> <!--cmake variables and their values-->
```

## CMake project dependencies in WiX
Also [FindWiX](https://github.com/apriorit/FindWiX) generates `depends.wxi` with file paths to [CMake](https://cmake.org) project dependencies. Here is a fragment of `depends.wxi`:
```xml
<?xml version='1.0' encoding='UTF-8'?>
<Include>
    <?define TARGET_FILE:CppExecutable='C:/my_proj/WithExecutable/Debug/CppExecutable.exe' ?>
    <?define TARGET_PDB_FILE:CppExecutable='C:/my_proj/WithExecutable/Debug/CppExecutable.pdb' ?>
</Include>
```
To get access to those variables include `depends.wxi` into your `wxs` file:
```xml
<?include depends.wxi?> <!--paths to cmake dependencies-->
```

## wix_add_project()
This function creates a new target for [WiX](http://wixtoolset.org) project. It compiles one or several `wsx` files to `wixobj` files and then links them together into the resulting `msi` file.

```cmake
wix_add_project(<name>
    source1 [source2 ...]
    [OUTPUT_NAME <msi_file_name>]
    [EXTENSIONS extension1 [extension2...]]
    [DEPENDS target1 [target2...]])
```

Where:
- `<name>` - name of the project target
- `source1 [source2 ...]` - one or several `wsx` files
- `OUTPUT_NAME` - allows to set a name for the resulting `msi` file, if omitted the name is set to `<name>`
- `EXTENSIONS` - add one or more WiX extensions (for example `WixUIExtension`)
- `DEPENDS` - add project dependencies for the correct build order
  
Example:
```cmake
wix_add_project(my_project 
    main.wxs one_more.wxs 
    OUTPUT_NAME "NameSample" 
    EXTENSIONS WixUIExtension WixUtilExtension
    DEPENDS CppExecutable)
```

# Samples 
Take a look at the [samples](samples/) folder to see how to use [FindWiX](https://github.com/apriorit/FindWiX).

# License
[FindWiX](https://github.com/apriorit/FindWiX) is licensed under the OSI-approved 3-clause BSD license. You can freely use it in your commercial or opensource software.

# Version History

## Version 1.0.0 (TDB)
- Initial public release