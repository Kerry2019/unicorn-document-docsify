> **JDK**

1. jdk[下载]([https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.oracle.com%2Ftechnetwork%2Fjava%2Fjavase%2Fdownloads%2Fjdk8-downloads-2133151.html))，推荐版本 jdk 8u191 +。
2. jdk安装，按照客户端步骤安装
3. 环境变量配置。
4. cmd验证，java -version。

> **本地 maven**

1. maven下载，如：[apache-maven-3.5.4-bin.zip。](http://maven.apache.org/download.cgi)
2. 本地解压压缩包。
3. 配置环境变量
4. cmd验证，mvn -version。
5. 设置本地maven仓库（\conf\settings.xml中）。
6. 常用命令
   （1）将jar包入本地maven仓库。
   ```shell
   mvn install:install-file -Dfile=jar包的位置(参数一) -DgroupId=groupId(参数二) -DartifactId=artifactId(参数三) -Dversion=version(参数四) -Dpackaging=jar）
   ```

   

> **IntelliJ IDEA**

1. IntelliJ IDEA [下载](https://www.jetbrains.com/idea/download/index.html)。
2. 按照客户端步骤安装，买版权 or 破解。
3. 设置默认jdk：file->Other Settings->Default Project Structure->Project->Project SDK
4. 设置默认maven仓库：file->Other Settings->Default Settings->Maven