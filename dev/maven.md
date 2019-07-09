{{indexmenu_n>1}}

# 基于Maven开发指南

本节主要介绍如何创建项目，并开发简单的Flink应用，从而使该Flink应用可以被提交到UFlink平台运行。目前UFlink托管集群仅支持1.6.4版本，更高版本的支持后期会陆续开放。

\==== 自动生成代码框架 ==== 对于Java开发者，可以使用maven自动生成flink项目：

    mvn archetype:generate                                  \
          -DarchetypeGroupId=org.apache.flink            \
          -DarchetypeArtifactId=flink-quickstart-java  \
          -DarchetypeVersion=1.6.4

对于scala开发者，可以使用maven自动生成flink项目：

    mvn archetype:generate                                  \
          -DarchetypeGroupId=org.apache.flink            \
          -DarchetypeArtifactId=flink-quickstart-scala  \
          -DarchetypeVersion=1.6.4

在生成flink项目框架之后，请注意更改mainClass的值，使其符合您的应用。

### 手动配置maven项目

如果想要自己创建Java项目，则需要手动添加如下依赖：

    <dependency>
      <groupId>org.apache.flink</groupId>
      <artifactId>flink-scala_2.11</artifactId>
      <version>1.6.4</version>
      <scope>provided</scope>
    </dependency>
    <dependency>
      <groupId>org.apache.flink</groupId>
      <artifactId>flink-streaming-scala_2.11</artifactId>
      <version>1.6.4</version>
      <scope>provided</scope>
    </dependency>

如果想要自己创建Scala项目，则需要手动添加如下依赖：

    <dependency>
      <groupId>org.apache.flink</groupId>
      <artifactId>flink-java</artifactId>
      <version>1.6.4</version>
      <scope>provided</scope>
    </dependency>
    <dependency>
      <groupId>org.apache.flink</groupId>
      <artifactId>flink-streaming-java_2.11</artifactId>
      <version>1.6.4</version>
      <scope>provided</scope>
    </dependency>

> 注解:
> 此处的scope为provided，因为flink应用在提交时会把这些依赖自动添加到classpath中。如果不设置为provided，可能会导致Java的类冲突，产生不可预见的问题。在自动生成的pom文件中，使用了profile来更方便的控制flink依赖的可见性。使用maven打包后会生成两个jar包，请使用不带original前缀的jar包，因为该包的MANIFEST.MF文件中不包含入口类信息。
