### IDEA安装教程

1. 下载ideaIU-2019.2.2.exe
2. 安装idea
   - 安装目录：D:\software\IntelliJ_IDEA2019
   - 勾选64-bit launcher
3. 配置idea
   - 导入settings.zip，可以导出一份保存
   - 激活idea：使用Activation code激活
   - 安装scala插件：Configure --> Plugins --> Scala
   - 配置全局JDK和Library：Configure --> Project Settings
4. settings配置
   - 关闭IDEA自动更新：Configure --> Settings --> Updates 取消勾选
   - 隐藏.idea等文件：Configure --> Settings --> File Types 添加\*.idea;\*.iml;
   - 修改字体大小：Configure --> Settings --> Font 修改Size为18
   - 修改文件编码：Configure --> Settings --> File Encodings
   - 配置Maven：Configure --> Settings --> Maven
   - 配置Git：Configure --> Settings --> Git
5. 其他问题
   - Plugins插件搜索不出：HTTP Proxy --> http://127.0.0.1:1080