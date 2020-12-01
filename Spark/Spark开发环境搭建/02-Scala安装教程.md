### Scala安装教程

1. 下载scala-2.11.8.msi
2. 安装Scala
   - 安装目录：D:\software\Scala
3. 配置环境变量
   - 新建SCALA_HOME：D:\software\Scala
   - 配置PATH：%SCALA_HOME%\bin、%SCALA_HOME%\jre\bin
   - 配置CLASSPATH：.;%SCALA_HOME%\bin;%SCALA_HOME%\lib\dt.jar;%SCALA_HOME%\lib\tools.jar
4. 验证运行
   - scala -version