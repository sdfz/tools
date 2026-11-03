### 经典踩坑场景
* * *
  你在 .exe 旁边放了一个 config.txt 配置文件，然后在代码里写 open("config.txt")。在 VS 里按 F5 调试时会提示找不到文件。因为 VS 此时正站在项目目录 $(ProjectDir) 里往外看，它找不到 .exe 旁边的文件。
  要想在调试 F5 执行的时候，配置文件和 exe 在同一个目录，需要设置项目属性里面的调试
  
> 1. 在右侧项目名称上右键 -> 属性。
> 2. 查看 .exe 生成路径：选择 配置属性 -> 常规，查看 输出目录 (Output Directory)。
> 3. 查看/修改调试运行路径：选择 配置属性 -> 调试。
> 	 命令 (Command)：表示 VS F5 调试时要去启动哪个 .exe（默认是 $(TargetPath)）。


### VS 项目属性路径变量设置
* * *
1. $(SolutionDir)：当前解决方案（.sln）所在的目录。
2. $(ProjectDir)：当前项目文件（.vcxproj）所在的目录。
3. $(Configuration)：当前的生成配置（如 Debug 或 Release）。
4. $(Platform)：当前的目标平台（如 Win32 或 x64）。
