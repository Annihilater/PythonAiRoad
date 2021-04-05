

# 动手学数据挖掘生产力怪兽Zeppelin


Apache Zeppelin是一款类似Jupyter notebook的交互式代码编辑器。

和jupyter 一样，它基于web环境，在同一个notebook中逐段调试运行代码并显示运行结果，支持图形可视化和markdown文档注释。

Zeppelin强大的交互式编程特性可以帮助用户按照REPL(read-evaluate-print-loop)最佳实践方式进行数据挖掘代码开发，是一头当之无愧的生产力怪兽。

Zeppelin内置了对Python,markdown,spark,shell,pyspark,mysql,hive,flink等解释器。

和jupyter-notebook/jupyterlab相比，zeppelin具有如下非常吸引我的优点：

* 更加完善的对spark-scala的可视化支持。

* 在一个notebook中可以同时使用python,scala,sql等不同的解释器。

* 支持对flink代码的调试。

在以下一些方面，zeppelin体验不如jupyter notebook:

* 缺少Web界面对文件的上传下载，查看内容等支持。

* 缺少对Terminal命令行的支持。

* 对python的支持不如jupyter notebook，无法对PIL，shapely等库的对象直接在html中渲染。

因此主要推荐一些需要使用spark-scala进行数据挖掘或者flink进行流计算的同学使用zeppelin，可以和jupyter notebook一起使用。

单纯以python作主要开发的话，zeppelin是不如jupyter notebook的。




### 一，Zeppelin的安装


安装Zeppelin可以在Linux操作系统或者MacOS操作系统安装，目前暂不支持Windows操作系统。

1，下载Zeppelin安装包

Zeppelin安装包下载地址：

* 官方地址：https://zeppelin.apache.org/download.html

* 国内镜像：https://mirrors.tuna.tsinghua.edu.cn/apache/zeppelin/ 

2，解压并重命名安装包

```
tar -xzvf zeppelin-0.9.0-bin-all.tgz 
mv zeppelin-0.9.0-bin-all zeppelin
```

3，修改配置文件

```
cd zeppelin/conf 
mv zeppelin-site.xml.template zeppelin-site.xml
```
用vim 编辑 zeppelin-site.xml, 建议按照如下方式修改server地址(默认是127.0.0.1)和端口号(默认是8080)

```
<property>
  <name>zeppelin.server.addr</name>
  <value>0.0.0.0</value>
  <description>Server binding address</description>
</property>

<property>
  <name>zeppelin.server.port</name>
  <value>8082</value>
  <description>Server port.</description>
</property>
```

4，修改环境变量

将zeppelin/bin对应的路径添加到 环境变量PATH中。

vim ~/.bashrc
export PATH=/opt/zeppelin/bin:${PATH}

然后执行source ~/.bashrc 让修改生效。

5，启动Zeppelin服务

命令行中输入如下命令即可启动Zeppelin服务。

zeppelin-daemon.sh start

然后在浏览器中输入机器对应的地址和端口号即可。

http://127.0.0.1:8082/

关闭Zeppelin服务

zeppelin-daemon.sh stop 


6，环境备注

Zeppelin依赖Java8环境，并且需要是jdk1.8.0_151以上版本的Java环境。

如果缺少相应环境，或者版本过低，在运行代码时候会报错。





### 二，Zeppelin的界面

1，主界面

Zeppelin在浏览器的主界面如下.

已经默认加载了安装包zeppelin/notebook目录下自带的一些教程笔记本。

可以通过Import note加载已有的zpln文件或者ipynb文件。

注意Zepplin的notebook文件默认都是存储在zeppelin/notebook目录下，不是在启动Zeppelin时候的当前目录。

这一点与jupyter有所不同。



2，notebook界面

Zeppelin的notebook界面以段落(paragraph)为单位，每个段落可以使用不同的解释器(interpreter)。

按住shift+Enter键执行当前所在段落。

鼠标移动到段落之间的空隙可以插入一个新的段落。

可以点击段落右上角的设置按钮，可以添加段落标题，改变字体大小，改变段落宽度等。



### 三，Zeppelin和markdown


Zeppelin支持markdown解释器，在paragragh的开始使用 %md标记将使用markdown解释器。下面是一个示范。

```md 
%md

* 支持公式
$$E=mc^2$$

* 支持图片 
![](https://avatars.githubusercontent.com/u/30676974?s=400&u=0aa18bc255ebfcfacd9d71dd3f5750dbadc4511b&v=4)

* 支持链接 
[《30天吃掉那只Tensorflow2》](https://github.com/lyhue1991/eat_tensorflow2_in_30_days)

* 支持表格

|:--:|:--:|:--:|
| name| age |gender  |
| LiLei |12  |male |
| HanMeimei |13  |female |

* 支持表情😋😋😋
```



### 四，Zeppelin和Shell


Zeppelin支持Shell解释器，在paragragh的开始使用 %sh标记将使用shell解释器。下面是一个示范。

```bash
%sh 

pwd
pip install torchkeras 
```



### 五，Zeppelin和Python


Zeppelin支持Python解释器，在paragragh的开始使用 %python标记将使用python解释器。下面是一个示范。

注意，可以用%matplotlib inline 来显示matplotlib绘制的图片，也可以使用zepplin提供的z.show(plt)方法来显示图片。

另外，还可以调用Zeppelin提供的z.show(df)来对Pandas中的DataFrame进行可视化。



```scala
%python

%matplotlib inline 

import matplotlib.pyplot as plt

fig = plt.plot([1,2,3],[1,4,9])
```

```scala
%python
import numpy as np 
import pandas as pd 
df = pd.DataFrame({"name":["LiLei","HanMeimei","LiLy","Jim"],"score":[80,90,70,75],"age":[13,15,12,13]})

z.show(df) //zeppelin提供的对DataFrame的可视化方法
```



### 六，Zeppelin和Spark 


Zeppelin提供了非常强大且友好的Spark支持，可以使用Spark-Scala,SparkSQL,PySpark，SparkR解释器。

并且在不同的解释器注册的临时表和视图是共享的，非常强大。

可以调用Zeppelin提供的z.show(df)来对Spark-Scala中的DataFrame进行可视化。

如果需要非常灵活的可视化，可以将该DataFrame注册成视图，然后再用PySpark读取该视图，转换成Pandas中的DataFrame后，利用matplotlib来进行可视化。真的是无比的灵活。

```scala
%spark 
import spark.implicits._ 

val df = spark.createDataFrame(Array(("LiLei",80,13),("HanMeimei",90,15),("Lily",70,12),("Jim",75,13))).toDF("name","score","age")
df.show() 
df.createOrReplaceTempView("students")
z.show(df) //zeppelin提供的dataframe可视化方法
```

```scala
%sql 
select * from students where score>75 
```

```scala
%pyspark 

df = spark.sql("select * from students")
df.show() 
```

如果需要设置Spark的相关executor数量，内存大小等配置参数，自定义的jar包等，可以在interpreter里设置。

interpreter可以在zeppelin主界面右上角下拉菜单处找到。





### 七，Zeppelin和Flink 


Zeppelin提供了非常强大的对Flink的支持。需要下载安装Flink并在interpreter中配置Flink的Flink_home参数。

以下是Flink下批处理和流处理的WordCount示范。

```scala
%flink

val data = benv.fromElements("hello world", "hello flink", "hello hadoop")
data.flatMap(line => line.split("\\s"))
             .map(w => (w, 1))
             .groupBy(0)
             .sum(1)
             .print()
```

```scala
%flink

val data = senv.fromElements("hello world", "hello flink", "hello hadoop")
data.flatMap(line => line.split("\\s"))
  .map(w => (w, 1))
  .keyBy(0)
  .sum(1)
  .print

senv.execute("word count example")
```

