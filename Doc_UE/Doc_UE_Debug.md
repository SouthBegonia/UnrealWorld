介绍 UE 下的调试方法、问题定位等



## 崩溃闪退

UE 会在游戏**崩溃闪退时** 收集本次运行的日志文件、生成**崩溃转存文件（Crash Dump File）**，通过该文件我们可以可以做到 **定位崩溃代码调用堆栈信息**

日志文件目录位于 Saved/Crashes/UECC-XXXX/ 内，其包含4个文件：

- CrashContext.runtime-xml：dump平替，文本直接呈现崩溃信息
- CrashReportClient.ini
- [PROJECTNAME].log
- UEMinidump.dmp：使用VS分析、复现崩溃现场

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20251031110339850.png)

### 示例

例如下图示例：代码执行了 `NewObject<T>(UObject* Outer, const UClass* Class)` 但其中的 `const UClass* Class` 为 nullptr。在游戏崩溃后通过 `.xml` 文件内的调用堆栈信息，就可定位到具体出问题的代码行

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20251031111849259.png)

![](https://southbegonia.oss-cn-chengdu.aliyuncs.com/Pic/20251031111513772.png)

### 参考文章

- [Unreal 的 MiniDump 机制调研 - 知乎](https://zhuanlan.zhihu.com/p/649610542)
- [UE 崩溃调试指南和技巧 - 知乎](https://zhuanlan.zhihu.com/p/655457589)
