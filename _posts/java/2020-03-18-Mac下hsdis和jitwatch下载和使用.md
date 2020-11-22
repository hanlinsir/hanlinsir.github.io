---
layout: post
title:  Mac下hsdis和itwatch下载和使用
category: java
copyright: java
---

网上搜寻资料，动手尝试构建反编译插件。

<!--more-->



## 1. HSDIS使用



### 1.1. 下载HSDIS反汇编插件 

参考个版本安装说明：https://github.com/AdoptOpenJDK/jitwatch/wiki/Building-hsdis



### 1.2. 指定HSDIS插件的位置

hsdis-amd64.dylib放在*$JAVA_PATH/jre/lib/server/*中，与*libjvm.dylib*同目录：

```less
luhanlindeMacBook-Pro:server luhanlin$ pwd
/Library/Java/JavaVirtualMachines/jdk1.8.0_162.jdk/Contents/Home/jre/lib/server
luhanlindeMacBook-Pro:server luhanlin$ ls
Xusage.txt		libjsig.dylib
hsdis-amd64.dylib	libjvm.dylib
```



### 1.3. 查看HSDIS是否工作

`java -XX:+UnlockDiagnosticVMOptions -XX:+PrintAssembly -version`

```less
Assembly -version
Java HotSpot(TM) 64-Bit Server VM warning: PrintAssembly is enabled; turning on DebugNonSafepoints to gain additional output
Loaded disassembler from /Library/Java/JavaVirtualMachines/jdk1.8.0_162.jdk/Contents/Home/jre/lib/server/hsdis-amd64.dylib
Decoding compiled method 0x000000011aa600d0:
Code:
[Disassembling for mach='i386:x86-64']
```



### 1.4. Idea 查看类汇编指令

- 创建一个类 VolatileDemo

  ```java
  public class VolatileDemo {
  
      private volatile static boolean flag = false;
  
      public static void main(String[] args) throws InterruptedException {
          new Thread(() -> {
              long i = 0L;
              while (!flag) {
                  i++;
              }
              System.out.println("count = " + i);
          }).start();
  
          System.out.println("Thread is running ... ");
          Thread.sleep(1000);
          flag = true;
      }
  }
  ```

  

- 在 idea 的启动类 VM_options中加入如下指令：

  ```java
  -server
  -Xcomp
  -XX:+UnlockDiagnosticVMOptions
  -XX:+PrintAssembly
  -XX:CompileCommand=compileonly,*VolatileDemo.*
  ```

  - 输入如下：

    ```java
    CompilerOracle: compileonly *VolatileDemo.*
    Loaded disassembler from /Library/Java/JavaVirtualMachines/jdk1.8.0_162.jdk/Contents/Home/jre/lib/server/hsdis-amd64.dylib
    Decoding compiled method 0x00000001147a1c50:
    Code:
    [Disassembling for mach='i386:x86-64']
    [Entry Point]
    [Verified Entry Point]
    [Constants]
      # {method} {0x0000000113f57e90} '<clinit>' '()V' in  com.hanlinsir.concurrent.thread06_volatile.VolatileDemo::<clinit>@0 (line 17)
    	... 
      0x00000001147a1dfb: nopl   0x0(%rax,%rax,1)
      0x00000001147a1e00: jmpq   0x00000001147a1e95  ;   {no_reloc}
      0x00000001147a1e05: add    %al,(%rax)
      0x00000001147a1e07: add    %al,(%rax)
      0x00000001147a1e09: add    %bh,0x0(%rdi)
      0x00000001147a1e0f: mov    %dil,0x68(%rsi)
      0x00000001147a1e13: lock addl $0x0,(%rsp)     ;*putstatic flag
        
    ```

    

##  2.使用 *JITWatch* 工具可视化展示

虽然通过HSDIS我们已经可以通过输出打印来查看汇编，但是这样显然不够方便。特别是在大项目中，终端输出会非常多。幸运的是*JITWatch*——JIT编译日志分析工具解决了这个痛点



### 2.1 下载包

如果不是jdk11+，不要选择新的release。

下载地址：https://github.com/AdoptOpenJDK/jitwatch/releases



### 2.2 使用启动脚本运行

```java
JITWATCH_HOME="/your path/JITWatch/jitwatch-master/lib";
JITWATCH_JAR="/your path/JITWatch/jitwatch-1.0.0-SNAPSHOT.jar"
java -cp $JAVA_HOME/lib/tools.jar:$JAVA_HOME/jre/lib/jfxrt.jar:$JITWATCH_JAR:$JITWATCH_HOME/hamcrest-core-1.3.jar:$JITWATCH_HOME/logback-classic-1.1.2.jar:$JITWATCH_HOME/logback-core-1.1.2.jar:$JITWATCH_HOME/slf4j-api-1.7.7.jar org.adoptopenjdk.jitwatch.launch.LaunchUI

```



### 2.3 **JITWatch的可视化分析**

为了能让JITWatch可视化的分析编译日志，我们还需要继续添加相关VM Option虚拟机参数来让反汇编后的汇编指令写到指定日志文件中:

```java
-server
-Xcomp
-XX:+UnlockDiagnosticVMOptions
-XX:+PrintAssembly
-XX:+LogCompilation
-XX:+TraceClassLoading
-XX:LogFile=jit.log
```

这样IDE运行后，汇编代码不仅打印输出到终端，还会生成我们下面所需的日志文件.

- 在JITWatch里，**Open Log**，打开生成的jit.log，然后配置config,配置项目的源代码目录和编译目录。最后就可以查看对比展示了。



### [官方wiki学习](https://github.com/AdoptOpenJDK/jitwatch/wiki)

