### 将jupyter notebook作为pyspark的默认编辑器

1. 安装pyspark

   - 通过拷贝pyspark包安装
   - 源目录：D:\software\spark-2.2.0-bin-hadoop2.6\python\pyspark
   - 目标目录：D:\software\Anaconda3\Lib\site-packages
2. 配置环境变量
   - 新建变量PYSPARK_DRIVER_PYTHON：jupyter
   - 新建变量PYSPARK_DRIVER_PYTHON_OPTS：notebook
3. 启动pyspark
   - pyspark
   - 验证spark实例

### 启动pyspark警告

- 警告内容：

  Warning:This Python interpreter is in a conda environment, but the environment has
  not been activated.  Libraries may fail to load.  To activate this environment
  please see https://conda.io/activation

- 解决方法，执行：D:\software\Anaconda3\Scripts\activate base

### 修改Jupyter Notebook默认工作路径

1. 修改配置文件

   - 打开Anaconda Prompt，输入jupyter notebook --generate-config生成配置文件

   - 修改jupyter_notebook_config.py
   
     ```python
       ## The directory to use for notebooks and kernels.
       c.NotebookApp.notebook_dir = 'D:\WorkSpace\Jupyter'
     ```

2. 修改Jupyter Notebook快捷方式
   
   - 删除"%USERPROFILE%"
