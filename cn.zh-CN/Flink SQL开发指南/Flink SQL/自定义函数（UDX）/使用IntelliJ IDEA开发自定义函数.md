# 使用IntelliJ IDEA开发自定义函数 {#concept_74943_zh .concept}

本文为您介绍如何使用IntelliJ IDEA开发实时计算自定义函数，包括搭建开发环境和将自定义函数引用到实时计算作业中。

**说明：** 

-   本文档基于IntelliJ IDEA开发工具，[请点击下载该工具](https://www.jetbrains.com/idea/download/#section=mac)。
-   目前阿里云实时计算共享模式暂不支持自定义函数，仅独享模式支持自定义函数。

## 下载及配置Maven {#section_uyh_lcy_dgb .section}

1.  下载Maven
    1.  打开[Maven官网下载页面](http://maven.apache.org/download.cgi) ，下载`apache-maven-3.5.3-bin.tar.gz`。
    2.  解压下载的安装包到指定目录，例如：`/Users/xxx/Documents/maven`。
2.  配置环境变量
    1.  打开**Terminal**，输入`vim ~/.bash\_profile`。
    2.  打开`.bash\_profile`文件，在次文件中添加设置环境变量的命令。

        ```language-java
        export M2_HOME=/Users/xxx/Documents/maven/apache-maven-3.5.3
        export PATH=$PATH:$M2_HOME/bin
        ```

    3.  添加之后保存并退出，执行以下命令使配置生效。 `source ~/.bash\_profile`
3.  查看配置是否生效

    输入`mvn -v`命令。如果打印如下信息，则说明配置生效。

    ```
    Apache Maven 3.5.0 (ff8f5e7444045639af65f6095c62210b5713f426; 2017-04-04T03:39:06+08:00)
    Maven home: /Users/****/Documents/maven/apache-maven-3.5.0
    Java version: 1.8.0_121, vendor: Oracle Corporation
    Java home: /Library/Java/JavaVirtualMachines/jdk1.8.0_121.jdk/Contents/Home/jre
    Default locale: zh_CN, platform encoding: UTF-8
    OS name: "mac os x", version: "10.12.6", arch: "x86_64", family: "mac"
    ```


## 开发环境搭建 {#section_ikn_zcy_dgb .section}

1.  在[环境搭建](cn.zh-CN/Flink SQL开发指南/Flink SQL/自定义函数（UDX）/UDX概述.md#section_ck2_gcm_cgb)页面直接下载Demo（`RealtimeCompute-udxDemo.gz`文件）。
2.  在Linux环境下解压`RealtimeCompute-udxDemo.gz`。

    ```
    tar xzvf RealtimeCompute-udxDemo.gz				
    ```

3.  打开**IntelliJ IDEA**，点击**Open**打开以上Demo即可。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41061/155594093634496_zh-CN.png)


## 创建Package {#section_lkh_1dy_dgb .section}

操作如下图所示。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41061/155594093634497_zh-CN.png)

本文以`com.hjc.test.blink.sql.udx`为例，如下图所示。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41061/155594093634503_zh-CN.png)

## 创建Class {#section_ksv_1dy_dgb .section}

操作如下图所示。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41061/155594093634498_zh-CN.png)

## 将代码粘贴进Class中测试 {#section_crt_bdy_dgb .section}

UDF示例代码如下所示。

```language-java
package com.hjc.test.blink.sql.udx;

import org.apache.flink.table.functions.FunctionContext;
import org.apache.flink.table.functions.ScalarFunction;

public class StringLengthUdf extends ScalarFunction {
    // 可选， open方法可以不写
    // 需要import org.apache.flink.table.functions.FunctionContext;
    @Override
    public void open(FunctionContext context) {
        }
    public long eval(String a) {
        return a == null ? 0 : a.length();
    }
    public long eval(String b, String c) {
        return eval(b) + eval(c);
    }
    //可选，close方法可以不写
    @Override
    public void close() {
        }
}
```

## 将项目写入JAR包 {#section_bqg_cdy_dgb .section}

1.  在Terminal中输入`mvn package` 或输入：`mvn assembly:assembly`。

    **说明：** 若需要将第三方依赖写入JAR包，请使用`mvn assembly:assembly`。

2.  编译后的JAR包为： `RealtimeCompute-udxDemo/target/RTCompute-udx-1.0-SNAPSHOT.jar`或 `RealtimeCompute-udxDemo/target/RTCompute-udx-1.0-SNAPSHOT-jar-with-dependencies.jar`（将第三方依赖写入JAR包）。

## 将JAR包引用到实时计算作业中 {#section_agx_cdy_dgb .section}

步骤请参见[注册使用](cn.zh-CN/Flink SQL开发指南/Flink SQL/自定义函数（UDX）/UDX概述.md#section_5q4_9pi_lny)。

