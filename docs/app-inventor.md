---
title: App-inventor 
---

## 现有文件结构介绍
```
├─app-inventor // 本地开发时
├─app-inventor-electron-build // 本地修改完后使用electron打包
├─app-inventor-installer // electron 打包后生成可自定义启动和安装界面的exe文件
├─app-inventor-packages // 离线运行服务所需要的相关包
```


## 开发流程

1. 启动本地服务进行本地开发（详细请参照app-inventor中的READM操作步骤）
2. 本地开发完了之后进行编译，命令： `ant`
3. build 完之后 将build的文件进行： `cd appinventor\appengine\build\war`  `git push`
4. 切换到app-inventor-electron-build 更新子模块 `git submodule update --remote`
5. `yarn start` 开发环境进行 electron 相关的工作
6. `yarn build` electron builder
7. `yarn copy` 将打包好的文件自动copy到 app-inventor-electron-installer 下 uApp-Creator-application目录
8. 进入到 app-inventor-electron-installer , 使用innosetup 自定义安装包的安装相关操作

## 最理想的开发方式构想

- windows上面使用 docker for windows 
- 创建一个docker镜像将上面的依赖环境集成
- 直接将这个新镜像打包发布共享

## issues

#### 问题一 : [ant PlayApp 2.5.1 on win 10 error](https://groups.google.com/forum/#!searchin/app-inventor-open-source-dev/playApp%7Csort:date/app-inventor-open-source-dev/eQyllvP0hDs/1315l8iXFwAJ)

```java
[java] java.lang.IllegalArgumentException: Unable to parse file - cannot locate beginning of $JSON section
[java] at com.google.appinventor.buildserver.FormPropertiesAnalyzer.parseSourceFile(FormPropertiesAnalyzer.java:62)
[java] at com.google.appinventor.buildserver.FormPropertiesAnalyzer.getComponentBlocksFromSchemeFile(FormPropertiesAnalyzer.java:123)
[java] at com.google.appinventor.buildserver.ProjectBuilder.getComponentBlocks(ProjectBuilder.java:279)
[java] at com.google.appinventor.buildserver.ProjectBuilder.build(ProjectBuilder.java:155)
[java] at com.google.appinventor.buildserver.Main.main(Main.java:82)
```
1. 引起问题的原因：

    >The issue here is that when you check out on Windows using Git, it will rewrite all of the line endings from Unix to Windows. This breaks the FormSourceAnalyzer. We need to make it more robust, but the correct thing here isn't to just change the lines from Unix to Windows line endings as that just breaks it for those of us using macOS and Linux. Instead, it should be robust to either line ending.

    windows上安装的git默认换行符会被转换为windows换行符
2. 解决方案：
     
     [相关链接地址](https://github.com/mit-cml/appinventor-sources/issues/1531)

#### 问题二： [Error Compiling: Kawa compile has failed (Stopped at PlayApp)](https://groups.google.com/forum/#!searchin/app-inventor-open-source-dev/Kawa$20compile$20has$20failed|sort:date/app-inventor-open-source-dev/O53xaP1PlsM/rsPcvh3zEgAJ)
```java
[java] SEVERE: Kawa compile has failed.
[java] (compiling T:\Temp\1545444563936_0.33611455224518594-0\youngandroidproject\..\src\edu\mit\appinventor\aicompanion3\Screen1.yail to edu.mit.appinventor.aicompanion3.Screen1)
[java] internal error while compiling T:\Temp\1545444563936_0.33611455224518594-0\youngandroidproject\..\src\edu\mit\appinventor\aicompanion3\Screen1.yail
```
1. 引起问题的原因：
    使用了 jdk 7版本
2. 解决方案：
    更换 jdk 为8 版本 （包括环境变量相关指向）

#### 问题三：本地dev开发环境
    ```shell
    java_dev_appserver --port=8891 --address=0.0.0.0 E:\workspace\uApp-creator\app-inventor\appinventor\appengine\build\war

    ```

####  问题四：使用electron打包现有应用时，执行.exe 文件无法调用到相关bat命令

####  问题五： 打包app时报错，版本不一致的问题
1. 引起问题的原因： 

    执行 `ant` 命令时 等于是重新构建了一次，这个时候没有对buildserver重新同步构建，所以导致会出现版本不一致的情况

2. 解决方案:

    ```
    cd buildserver
    ant BuildDeploymentTar
    ```
     解压 - 将lib下的所以东西覆盖 BuildServer目录
     
3. 参考文档

    [help](https://docs.google.com/document/pub?id=1Xc9yt02x3BRoq5m1PJHBr81OOv69rEBy8LVG_84j9jc)



## 问题五：build app 时进度条停留在某一个值不动

1. 问题描述

```java
      [java] Feb 17, 2018 7:12:17 PM com.google.appinventor.buildserver.Compiler runAaptPackage
     [java] WARNING: YAIL compiler - AAPT execution failed.
     [java] /tmp/aapt9173588917414941556: error while loading shared libraries: libz.so.1: wrong ELF class: ELFCLASS64
     [java] Feb 17, 2018 7:12:17 PM com.google.appinventor.buildserver.BuildServer build
     [java] INFO: Build output: ________Preparing application icon<br>________Creating animation xml<br>________Creating style xml<br>________Generating manifest                file<br>________Attaching native libraries<br>________Attaching Android Archive (AAR) libraries<br>________Attaching component assets<br>________Invoking           AAPT<br>YAIL compiler - AAPT execution failed.<br>
     [java] Feb 17, 2018 7:12:17 PM com.google.appinventor.buildserver.BuildServer build
     [java] INFO: Build error output: Error: Your build failed due to an error in the AAPT stage, not because of an error in your program.
     [java] 
     [java] Feb 17, 2018 7:12:17 PM com.google.appinventor.buildserver.BuildServer checkMemory
     [java] INFO: Build 1 current used memory: 13399336 bytes
     [java] Feb 17, 2018 7:12:17 PM com.google.appinventor.buildserver.BuildServer buildAndCreateZip
     [java] SEVERE: Build 1 Failed: 1 Error: Your build failed due to an error in the AAPT stage, not because of an error in your program.
     [java] 
     [java] Feb 17, 2018 7:12:17 PM com.google.appinventor.buildserver.BuildServer$1 run
     [java] INFO: CallbackURL: http://localhost:8888/ode2/receivebuild/                 5kivunvmyfk1e6t6tui9sncvrynpyw17kvsgtc65rhp4udbuxn3l6eykuknrbjez80f9rf4tzilzyrtzmx9hqp7q0ce4gd2sxr1nm7m9w4wuue67wvu543q8mhhr1sv5vqaj2c51lfojfc4pgrrnlfhlzpe08afkqmf/build/Android
     [java] Feb 17, 2018 7:12:19 PM com.google.appinventor.buildserver.BuildServer$1 run
     [java] SEVERE: Exception: Connection refused (Connection refused) and the length is of inputZip is 1786
     [java] Feb 17, 2018 7:12:19 PM com.google.appinventor.buildserver.BuildServer checkMemory
     [java] INFO: Build 1 current used memory: 10900536 bytes
     [java] Feb 17, 2018 7:12:19 PM com.google.appinventor.buildserver.BuildServer$1 run
     [java] INFO: BUILD 1 FINISHED
```

2. 问题原因

   因为改了端口号，原始的端口号是8888，在自己的程序中改成了其他端口号，导致默认的依赖端口号的任务跑不起来。下面是依赖代码块：

```java
    private String getCurrentHost() {
      if (Server.isProductionServer()) {
        if (appengineHost.get()=="") {
          String applicationVersionId = SystemProperty.applicationVersion.get();
          String applicationId = SystemProperty.applicationId.get();
          return applicationVersionId + "." + applicationId + ".appspot.com";
        } else {
          return appengineHost.get();
        }
      } else {
        // TODO(user): Figure out how to make this more generic
        return "localhost:8888"; //这一段代码的坑导致的问题
      }
    }
```

3. 解决方案

   将main.js的端口号改回来



