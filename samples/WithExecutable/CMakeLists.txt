add_executable(CppExecutable Main.cpp)

wix_add_project(WithExecutable 
    Main.wxs
    DEPENDS CppExecutable #the way to build CppExecutable first
    )