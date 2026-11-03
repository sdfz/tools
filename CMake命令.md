
### 第一组：高级配置命令（使用 VS2017）
```bash
cmake -S . -B build -G "Visual Studio 15 2017" -A x64 -T host=x64 -DCMAKE_BUILD_TYPE=RelWithDebInfo -DCMAKE_CXX_FLAGS_RELWITHDEBINFO="/Od /Zi" -DCMAKE_C_FLAGS_RELWITHDEBINFO="/Od /Zi"
1. 整体功能： 初始化项目，使用 Visual Studio 2017 编译器生成适用于 64位系统 的工程配置文件。同时修改了编译参数，使得在生成带有调试信息的发布版（RelWithDebInfo）时，禁用代码优化并生成最完整的调试信息（PDB 文件）。
2. 参数详解：
    • -S .：指定源码根目录（Source）。. 代表当前目录，CMake 会在这里寻找 CMakeLists.txt。
    • -B build：指定构建输出目录（Build）。所有生成的工程文件、编译中间产物都会存放在 build 文件夹中。
    • -G "Visual Studio 15 2017"：指定生成器（Generator）。显式指定使用 VS2017 编译器来创建解决方案（.sln）。
    • -A x64：指定目标架构（Architecture）。编译出 64 位的程序/库。
    • -T host=x64：指定工具集选项（Toolset）。host=x64 意味着让 CMake 强制调用 64 位版本的编译器本身（编译器自身也是个程序）来执行编译。这在编译超大型项目时能防止编译器自身内存不足（OOM）。
    • -DCMAKE_BUILD_TYPE=RelWithDebInfo：定义 CMake 变量。试图指定构建类型为 RelWithDebInfo（Release + 调试信息）。（注：对于 Visual Studio 这种多配置生成器，此参数其实会被忽略，需要在后面的编译阶段用 --config 指定，但写在这里通常用于兼容其他编译器）。
    • -DCMAKE_CXX_FLAGS_RELWITHDEBINFO="/Od /Zi"：自定义 C++ 在 RelWithDebInfo 模式下的编译参数。
        • /Od：关闭代码优化（方便调试时逐行对齐，代码不乱跳）。
        • /Zi：生成最完整的调试信息（PDB 文件）。
    • -DCMAKE_C_FLAGS_RELWITHDEBINFO="/Od /Zi"：自定义 C 语言在 RelWithDebInfo 模式下的编译参数（含义同上）。
```

### 第二组：基础配置命令（使用 VS2022 + 指定 SDK）
```bash
cmake -S . -B build -G "Visual Studio 17 2022" -A x64 -DCMAKE_SYSTEM_VERSION=10.0.22621.0
1. 整体功能： 初始化项目，使用现代的 Visual Studio 2022 编译器生成 64位 工程，并且强制指定使用特定版本的 Windows SDK 展开编译。
2. 参数详解：
    • -S . 和 -B build：同上（源码目录为当前，输出到 build）。
    • -G "Visual Studio 17 2022"：指定使用 VS2022 编译器生成器。
    • -A x64：指定目标程序为 64 位。
    • -DCMAKE_SYSTEM_VERSION=10.0.22621.0：核心参数。显式指定 Windows SDK 的版本号。CMake 会强制 VS 链接这个特定版本的 Windows 系统库和头文件（常用于团队开发或服务器部署时对齐环境）。
```

### 第三组：最简配置命令
```bash
cmake -S . -B build
1. 整体功能： 以最简单、最偷懒的方式初始化项目。
2. 参数详解：
    • 没有指定 -G、-A 等任何高级参数。CMake 会自动检测你当前电脑上最新安装的 Visual Studio 版本，并默认以 64位（x64）、最新版 SDK 自动完成配置。
```

### 第四组：编译/构建命令
```bash
cmake --build build
1. 整体功能： 真正开始调用编译器去编译源代码、生成 .dll / .lib / .exe 文件。
2. 参数详解：
    • --build：告诉 CMake 执行“构建”动作，而非配置动作。CMake 内部会自动去调用系统的底层编译工具（例如 VS 的 MSBuild.exe）。
    • build：指定去哪个目录下寻找配置好的工程。这里指向我们前面几步创建的 build 文件夹。
    • （注：因为没有加 --config 参数，VS 编译器此时会默认编译出 Debug 版本的程序）。
```

### 第五组：安装/打包命令
```bash
cmake --install build --config Debug --prefix ./bin
1. 整体功能： 将刚刚编译好的产物中，有用的头文件（.h）、库文件（.lib/.dll）和可执行文件过滤并提取出来，“洗干净”后整齐地打包到你指定的目录中。
2. 参数详解：
    • --install build：告诉 CMake 执行“安装打包”动作，并指定去 build 文件夹中提取编译好的文件（它会读取里面由 install() 命令生成的脚本）。
    • --config Debug：多配置环境专属参数。明确告诉 CMake ：“我去拿的是刚刚编译好的 Debug 版本的文件”。
    • --prefix ./bin：指定打包的目标终点（Prefix 根路径）。将提取出的文件全部复制到当前目录下的 bin 文件夹中。最终形式通常为 ./bin/include、./bin/lib。
```
