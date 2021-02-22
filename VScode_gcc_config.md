# vscode 配置 C/C++ 编译环境

### Step 1：Download MinGW-W64 GCC-8.1.0 compiler

[Online install](https://sourceforge.net/projects/mingw-w64/files/Toolchains targetting Win32/Personal Builds/mingw-builds/installer/mingw-w64-install.exe)

[offline install](https://sourceforge.net/projects/mingw-w64/files/Toolchains%20targetting%20Win64/Personal%20Builds/mingw-builds/8.1.0/threads-win32/seh/x86_64-8.1.0-release-win32-seh-rt_v6-rev0.7z/download)



### step 2: Environment Variables Config

Open **control panal -> system -> Advanced system settings**

![set_image](https://raw.githubusercontent.com/XBY-x/Markdown-pictures/main/cpp_Learning/vscode_config_1.PNG)

直接将下载下来的 .\mingw64\bin 所在路径加入 path



### step 3: restart  computer

restart



### step 4: install VScode & 相关插件

`C/C++`     ^ms-vscode.cpptools^

by Microsoft

restart VScode



### step 5: Config Project

open code folder

Add following files to `.vscode` folder



#### launch.json

``` json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) Launch", // 配置名称，将会在启动配置的下拉菜单中显示
            "type": "cppdbg", // 配置类型，这里只能为cppdbg
            "request": "launch", // 请求配置类型，可以为launch（启动）或attach（附加）
            "program": "${workspaceFolder}/${fileBasenameNoExtension}.exe", // 将要进行调试的程序的路径
            "args": [], // 程序调试时传递给程序的命令行参数，一般设为空即可
            "stopAtEntry": false, // 设为true时程序将暂停在程序入口处，一般设置为false
            "cwd": "${workspaceFolder}", // 调试程序时的工作目录，一般为${workspaceRoot}即代码所在目录 workspaceRoot已被弃用，现改为workspaceFolder
            "environment": [],
            "externalConsole": true, // 调试时是否显示控制台窗口，一般设置为true显示控制台
            "MIMode": "gdb",
            "miDebuggerPath": "C:/Program Files (x86)/mingw64/bin/gdb.exe", // miDebugger的路径，注意这里要与MinGw的路径对应
            "preLaunchTask": "gcc", // 调试会话开始前执行的任务，一般为编译程序，c++为g++, c为gcc
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": false
                }
            ]
        }
    ]
}
```



#### tasks.json

```json
{
    "version": "2.0.0",
    "command": "gcc",
    "args": [
        "-g",
        "${file}",
        "-o",
        "${fileBasenameNoExtension}.exe"
    ]
}
```



