### Maven安装教程

1. 下载apache-maven-3.3.9-bin.zip 
2. 解压

   - 目录：D:\software\Maven\apache-maven-3.3.9
   - 新建仓库目录：D:\software\Maven\repo
3. 配置环境变量

   - 新建MAVEN_HOME：D:\software\Maven\apache-maven-3.3.9

   - 新建M2_HOME：D:\software\Maven\apache-maven-3.3.9
   - 配置PATH：%M2_HOME%\bin
4. 验证运行
   
   - mvn -v
   - mvn help：生成.m2文件
5. 配置仓库

   - 全局配置：$M2_HOME/conf/settings.xml
   - 用户配置：$USER.HOME/.m2/settings.xml
   
   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
     <!--本地仓库-->
     <localRepository>D:/software/Maven/repo</localRepository>
     <mirrors>
       <!-- 阿里云仓库 -->
       <mirror>
           <id>alimaven</id>
           <mirrorOf>central</mirrorOf>
           <name>aliyun maven</name>
           <url>http://maven.aliyun.com/nexus/content/repositories/central/</url>
       </mirror>
     </mirrors>
      <profiles>
       <!--配置创建项目时maven默认的JDK版本-->
       <profile>     
           <id>JDK-1.8</id>       
           <activation>       
               <activeByDefault>true</activeByDefault>       
               <jdk>1.8</jdk>       
           </activation>       
           <properties>       
               <maven.compiler.source>1.8</maven.compiler.source>       
               <maven.compiler.target>1.8</maven.compiler.target>       
               <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>       
           </properties>       
       </profile>
     </profiles>
   </settings>
   ```