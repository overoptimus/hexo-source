---
title: spark学习之旅(二)
date: 2019-03-18 10:51:31
id: 20190318105131
categories: spark
tags: 
    - spark
    - 机器学习
    - spark环境搭建
---
<!-- more -->

# 1.回顾

学习之旅（一）中主要谈到了spark框架和hadoop的联系和区别，及其在大数据中的优势，在本章中，主要实现以scala和python的方式写一个spark的小例子。
主要的部分在最后的踩坑部分。

# 2.scala实现spark小例子
## 通过idea+sbt实现
1. 首先在idea中下载好支持scala的插件，在插件管理中直接搜索`scala`下载安装就可以
2. 新建项目，选择scala下的sbt，新建好后等待项目加载完所有依赖。
3. 在build.sbt下添加下面这段

        libraryDependencies += "org.apache.spark" %% "spark-core" % "2.4.0"
   
    其中最后的版本号在[spark](http://spark.apache.org/downloads.html)上找
4. 在src/main/scala下创建Test.scala

        import org.apache.spark.{SparkContext, SparkConf}
        
        object HelloWorld {
           def main(args: Array[String]): Unit = {
                val file = "F:\\pythonProgram\\data\\UserPurchaseHistory.csv"
                val conf = new SparkConf().setAppName("First Spark App").setMaster("local[2]")
                val sc = new SparkContext(conf)
                val data = sc.textFile(file).map(line => line.split(",")).map(record => (record(0), record(1), record(2)))
                val numParchases = data.count()
                val uniqueUser = data.map{case (user, product, price) => user}.distinct().count()
                val totalRevenue = data.map{case (user, product, price) => price.toDouble}.sum()
                val productByPopularity = data.map{case (user, product, price) => (product, 1.0)}.reduceByKey(_ + _).collect().sortBy(-_._2)
                val mostPopularityProduct = productByPopularity(0)(0)
        
                println("total parchases:" + numParchases)
                println("unique users: " + uniqueUser)
                println("total revenue is :" + totalRevenue)
                println("most popular product is " + mostPopularityProduct)
        
            }
        }

5. 在idea下方找到sbt的终端输入`run`，得到结果

# 3.python实现spark小例子
## 使用Atom实现
python较为简单直接上代码

        from pyspark import SparkContext
    
        sc = SparkContext(master='local[2]', appName='First Spark App')
        data = sc.textFile(r'data\UserPurchaseHistory.csv').map(lambda line: line.split(',')).map(lambda record: (record[0], record[1], record[2]))
        numPurchase = data.count()
        uniqueUsers = data.map(lambda record: record[0]).distinct().count()
        totalRevenue = data.map(lambda record: float(record[2])).sum()
        products = data.map(lambda record: (record[1], 1.0)).reduceByKey(lambda a, b: a + b).collect()
        mostPopular = sorted(products, key=lambda x: x[1], reverse=True)[0][0]
    
        print('totalPurchase is: %d' %(numPurchase))
        print('userNum is: %d' %(uniqueUsers))
        print('totalRevenue is: %d' %(totalRevenue))
        print('mostPopular product is: %s' %(mostPopular))

之后到环境下

`spark-submit XXX.py`
# 4.环境安装中爬过的坑
1. 首先spark的版本别用2.4.0，该版本的pyspark中，windows环境下会调用resource这个模块，会报错找不到。所以我最后使用的是2.3.3版本
2. 会调用到`winutils`，这个在[hadoop-common-2.2.0-bin](https://github.com/srccodes/hadoop-common-2.2.0-bin)上下载

    在环境变量中添加HADOOP_HOME       
    > `E:\dev\hadoop-common-2.2.0-bin`

    在PATH下添加
    
    > `%HADOOP_HOME%\bin`

