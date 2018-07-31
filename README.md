# FindWiX [![Build status](https://ci.appveyor.com/api/projects/status/4iagwdkceft3fb0o?svg=true)](https://ci.appveyor.com/project/sqzhr/findwix-73k2b)

Create a new WiX cmake module
* [Introduction](#introduction)
* [Usage](#usage)
* [Samples](#samples) 
* [License](#license) 
* [Version History](#version-history)

# Introduction
Every time when, you make application more complex that single executive or library you need an installer for your project. 
If you like to use free flexible and extensible tool as we are, you could already use CMake.
[Cmake](https://cmake.org) is a cross-platform free software for managing the build process, it doesn`t build project directly, only generates necessary build management files according to CMakeLists.txt file, that controls the build process and should be created in every project directory. The generated files can be used for building the project with Visual Studio or other toolset.

### Existing methods for generating installer:
The main solution for CMake is [CPack](https://cmake.org/cmake/help/v3.0/module/CPack.html). It's a module used for build binary and source package installers including msi files. But it allows to work with only one project, therefore CPack is not suitable for the purpose of creation several msi files at once. The inability to generate two or more msi files at the same time is a significant drawback.

[WiX (Windows Installer XML Toolset)](http://wixtoolset.org) is tool, which used for building Windows installer packages. It consists of a command-line environment that developers may integrate into their build processes to build MSI and MSM packages. It uses for creating installers for all main Microsofts products which is a proof of usability, at least.
So we decided to develop own module for CMake which combine advantages of both tools.

### FindWiX
Behold FindWiX. It makes your life easier and installer creation more comfy. It possible to build the WiX project for creating Windows Installers. You could create as much as you want installers for single solution.

Requirements:
- [CMake 3.0](https://cmake.org/download/) or higher
- [WiX toolset 3.0](http://wixtoolset.org/releases/) or higher

# Usage
Add FindWiX to the module search path and call `find_package`
```cmake
list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_LIST_DIR}/cmake")
find_package(WIX REQUIRED)
```
FindWiX will search for the installed WiX Toolset and expose commands for creating installer file.
Also the following variables will be defined:
* ```WIX_FOUND``` -- if false, WiX is absent
* ```WIX_ROOT``` -- path where WiX is installed

* ```WIX_COMPILE_FLAGS``` -- flags to be used when compiling WiX source files.
* ```WIX_LINK_FLAGS``` -- flags to be used when linking an wixobj files.

You could do some fine tuning, for example treats all warnings as an error:
```cmake
set(WIX_COMPILE_FLAGS ${WIX_COMPILE_FLAGS} -wx)
set(WIX_LINK_FLAGS ${WIX_LINK_FLAGS} -wx)
```

[FindWiX.cmake](cmake/FindWix.cmake) generates `vars.wxi` file of non-duplicated items list of all available variables and their values as WiX include.
Fragment of `vars.wxi`:
```XML
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
Also paths to CMake dependencies are stored in generated `depends.wxi` file.

Fragment of `depends.wxi`:
```XML
<?xml version='1.0' encoding='UTF-8'?>

<Include>
	<?define TARGET_FILE:CppExecutable='C:/my_proj/WithExecutable/Debug/CppExecutable.exe' ?>
	<?define TARGET_PDB_FILE:CppExecutable='C:/my_proj/WithExecutable/Debug/CppExecutable.pdb' ?>
</Include>
```

##### wix_add_project()
The only function takes at least one parameter - wxs source file, rest is optional. It generates wixobj files corresponding to the input data, then launches the WiX command (as custom command of CMake file) to create msi file. It is performed for each subdirectory relative to main CmakeLists.txt file.

```cmake
wix_add_project(<name>
  source1 [source2 ...]
  [OUTPUT_NAME <msi_file_name>]
  [EXTENSIONS extension1 [extension2...]]
  [DEPENDS executable1 [executable2..]])
```
  * `OUTPUT_NAME` -- allow to set the name of a result msi file, it can be set to an empty string, then the name of result file will be set to the <name> designation.
  * `EXTENSIONS` -- add one or more extensions
  * `DEPENDS` -- add files that should be built first
  
  Example:
  ```cmake
  wix_add_project(my_project 
    main.wxs one_more.wxs 
    OUTPUT_NAME "NameSample" 
    EXTENSIONS WixUIExtension WixUtilExtension
    DEPENDS CppExecutable)
  ```  

# Samples 
Take a look at the [samples](samples/) folder to see how FindWiX is built.

# License
FindWiX is licensed under the OSI-approved 3-clause BSD license. You can freely use it in your commercial or opensource software.

# Version History

## Version 1.0.0 (TDB)
- Initial public release
