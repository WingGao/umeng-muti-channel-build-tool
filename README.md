#友盟渠道打包工具(windows .net 3.5)

友盟渠道打包工具开放源码使用 GPL2 许可分发

##1. 工程结构

需要把`adds_on` 里面的文件和文件夹拷贝到功能目录的 `bin\debug\` 和 `bin\release\` 目录下， adds_on 文件内容为：
   
   1. ant 文件夹包含 ant 源码打包的相关工具
   2. apktool 文件夹包含 apk 打包的相关工具
   3. ClassLibrary1.dll 一个简单的基于.net 3.5 的统计分析SDK 

src 目录是打包的主要代码：
   
   1. Configration.cs 工程配置相关
   2. Utils.cs 工具类
   3. Worker.cs 打包主要流程

##2. 工程管理
左边栏工程列面，对应右侧显示当前工程的配置信息，包括工程配置和一般配置
系统配置属于系统级别的配置，所有工程共享

右边栏渠道管理，输入渠道后显示在渠道列面里面，点击删除渠道会删除当前选中的渠道（单选），打开文件夹会打开打包后的apk文件。

##3. 打包流程

### 1. 从源码打包
源码打包，是用  `ant`  执行 `Android` 系统目录下的 `build.xml` 文件 , `build.xml` 会自动生成未签名的 `.apk` 文件。 

1. 设置当前process 的环境变量，保证 ant ，java ， android tools 可以直接运行需要设置 ANT_HOME, JAVA_HOME, PATH = …
2. 备份 工程目录下的 AndroidManifest.xml , ant.properties, build.xml,project.properties四个文件, 如果文件不存在则不备份。
3. 删除 ant.properties  禁止 build 文件自动对apk 进行签名。不用自动签名是因为，在jdk 1.7 中自动签名会失败，所以采用主动签名的方式。
4. 替换或者添加 AndroidManifest.xml 中的 友盟 channel
5. 执行 android update project  –p XXXX  –t  XXXX 更新工程目录
6. 执行 ant clean –f XXXX\build.xml清空 bin , obj 目录
7. 执行 ant release –f XXXX\build.xml 打包生成未签名的 apk
8. 执行 jarsigner 生成签名后的 apk 文件
9. 执行 zipAlign 生成对齐优化后的 apk 文件
10. 回到 4 替换新的渠道。
11. Restore 备份文件
12. 完成打包

### 2.  从APK打包

通过 `.apk` 打包，本质上对  `apk` 文件进行反编译，修改 `AndroidManifest.xml` 文件后，再重新打包，我们使用的工具是开源的拆包工具 [Apktool](https://code.google.com/p/android-apktool/)

1. 设置当前process 的环境变量，保证 apktool 可以正常工作
2. 执行 apktool d --no-src -f xxxx.apk temp 拆解apk
3. 替换或者添加 AndroidManifest.xml 中的 友盟 channel
4. 执行 apktool b temp  unsigned.apk 重新打包apk
5. 执行 jarsigner 生成签名后的 apk 文件
6. 执行 zipAlign 生成对齐优化后的 apk 文件
7. 回到 3 替换新的渠道
8. 完成打包

## 4. 更新日志

### V1.2
>* 添加通过 `apk` 打包的功能

### V1.0
 提供基本的通过工程源码打包功能


