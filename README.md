# FindWIX [![Build status](https://ci.appveyor.com/api/projects/status/4iagwdkceft3fb0o?svg=true)](https://ci.appveyor.com/project/sqzhr/findwix-73k2b)

Create a  new WIX cmake module
* [Introduction](#introduction)
* [Usage](#usage)
* [Samples](#samples) 
* [License](#license) 
* [Version History](#version-history)

# Introduction
FindWIX makes it possible to build the WIX project for creating installers for Windows Installer

Requirements:
- CMake 3.0 and higher
- WiX toolset

# Usage
Add FindWIX to the module search path and call find_package
```cmake
list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_LIST_DIR}/cmake")
find_package(WIX REQUIRED)
```
FindWIX will search for the installed WiX Toolset (WiX) and expose commands for creating installer file 
Also the following variables will be defined:
* ```WIX_FOUND``` -- if false, do not try to use WiX
* ```WIX_ROOT``` -- where WiX is installed

[findWiX.cmake](cmake/findWix.cmake) generates vars.wxi file of non-duplicated items list of all available variables and their values in xml format.
Fragment of generated file content:
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
```
Also paths to cmake dependencies are stored in generated depends.wxi file.
Fragment of generated file content:
```XML
<?xml version='1.0' encoding='UTF-8'?>

<Include>
	<?define TARGET_FILE:CppExecutable='C:/my_proj/WithExecutable/Debug/CppExecutable.exe' ?>
	<?define TARGET_PDB_FILE:CppExecutable='C:/my_proj/WithExecutable/Debug/CppExecutable.pdb' ?>
</Include>
```


##### wix add project
```cmake
wix_add_project(<name>
  source1 [source2 ...]
  [OUTPUT_NAME <msi_file_name>]
  [EXTENSIONS extension1 [extension2...]]
  [DEPENDS executable1 [executable2..]])
```
  * ```OUTPUT_NAME``` -- allow to set the name of a rezult .msi file, it can be set to an empty string, then the name of rezult file will be set to the <name> designation.
  * ```EXTENSIONS``` -- add one or more extensions
  * ```DEPENDS``` -- add files that should be built first
  
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
FindWIX is licensed under the OSI-approved 3-clause BSD license. You can freely use it in your commercial or opensource software.

# Version History
Also an example
