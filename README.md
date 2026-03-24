Android NDK r30 (Integrated with Arkari Obfuscator)
本仓库提供了一个自定义编译的 Android NDK (r30) 版本，内部集成了 Arkari 混淆工具链。

Arkari 是一个基于 LLVM 的现代化高级混淆器（衍生自 goron）。相比于传统的 OLLVM，它不仅修复了大量已知 Bug，还提供了更强力的间接调用加密和常量加密功能，专为提升 Native 层代码的安全逆向成本而设计。

资产下载 (Assets)
本 Release 提供了两个版本的 NDK 工具链压缩包，请根据开发需求下载附件：

android-ndk-r30-windows-x86_64-arkari.zip
描述： 核心产物。集成了 Arkari 混淆功能的修改版 NDK。
用途： 用于编译 Release 版本，为 JNI/C++ 核心代码（如环境检测、反作弊逻辑、加解密算法）提供强有力的底层安全防护。
android-ndk-r30-windows-x86_64-official.zip
描述： 未做任何修改的官方纯净版 NDK。
用途： 作为对照组使用，或用于日常的 Debug 调试（开启混淆会极大影响断点调试和内存 Dump 分析）。
##可用的混淆参数 (Flags)

在编译时，向编译器传递以下参数即可精确控制所需的混淆特性：

-mllvm -irobf-indbr : 间接跳转，并加密跳转目标
-mllvm -irobf-icall : 间接函数调用，并加密目标函数地址
-mllvm -irobf-indgv : 间接全局变量引用，并加密变量地址
-mllvm -irobf-cse : C 字符串 (C-String) 加密
-mllvm -irobf-fla : 过程相关控制流平坦化 (Control-Flow Flattening)
-mllvm -irobf-cie : 整数常量加密
-mllvm -irobf-cfe : 浮点常量加密
-mllvm -irobf-rtti : 擦除 Microsoft CXXABI RTTI 名称 (实验性)
安全组合 (全开)：
-mllvm -irobf-indbr -mllvm -irobf-icall -mllvm -irobf-indgv -mllvm -irobf-cse -mllvm -irobf-fla -mllvm -irobf-cie -mllvm -irobf-cfe

高强度混淆会导致编译耗时显著增加，且生成的 .so 文件体积会变大。建议针对安全需求高的特定函数使用 attribute((annotate("..."))) 进行局部混淆，避免对高频计算模块造成性能瓶颈。

Android 项目配置示例
方式一：使用 CMake (CMakeLists.txt)

# 全局开启字符串加密和控制流平坦化
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -mllvm -irobf-fla -mllvm -irobf-cse")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -mllvm -irobf-fla -mllvm -irobf-cse")