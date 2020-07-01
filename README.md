# homework2
https://youyou-tech.com/2019/12/08/人工智能预测电影票房（kaggle竞赛）/
https://www.getit01.com/p2018081334010009/
https://blog.csdn.net/gyu357nwh91/article/details/80137433

https://www.cnblogs.com/agilestyle/p/12145936.html
https://blog.csdn.net/qq_43608549/article/details/102583171
kafka  https://www.cnblogs.com/kaituorensheng/p/12349273.html https://juejin.im/post/5c9afd35e51d455154343153 https://blog.csdn.net/ywdhzxf/article/details/83185828

zk https://www.cnblogs.com/cc11001100/p/10230608.html
https://www.cnblogs.com/feifeicui/p/11017525.html
https://forum.huawei.com/enterprise/zh/thread-451835.html
https://blog.csdn.net/napoay/article/details/68626188
# -*- coding: utf-8 -*

import sys

from pyspark import SparkConf, SparkContext
from pyspark.sql import SparkSession

def contains(str, substr):
	if substr in str:
		return True
	return False

if __name__ == "__main__":
	if len(sys.argv) < 2:
		print ("Usage: CollectFemaleInfo <file>")
		exit(-1)

	# Create SparkContext and set AppName.
	spark = SparkSession \
		.builder \
		.appName("CollectFemaleInfo") \
		.getOrCreate()

	"""
	The following programs are used to implement the following functions:
	1. Read data. This code indicates the data path that the input parameter argv[1] specifies. - text
	2. Filter data about the time that female netizens spend online. - filter
	3. Aggregate the total time that each female netizen spends online. - map/map/reduceByKey
	4. Filter information about female netizens who spend more than 2 hours online. - filter
	"""
	inputPath = sys.argv[1]
	result = spark.read.text(inputPath).rdd.map(lambda r: r[0]) \
		.filter(lambda line: contains(line, "female")) \
		.map(lambda line: line.split(',')) \
		.map(lambda dataArr: (dataArr[0], int(dataArr[2]))) \
		.reduceByKey(lambda v1, v2: v1 + v2) \
		.filter(lambda tupleVal: tupleVal[1] > 120) \
		.collect()
	for (k, v) in result:
		print (k + "," + str(v))

	# Stop SparkContext
	spark.stop()
	
spark.yarn.appMasterEnv.PYSPARK_PYTHON=./anaconda3/anaconda3/bin/python --master yarn-client --archives /opt/anaconda.tar.gz#anaconda3 /opt/collectFemaleInfo.py /spark/testdata
