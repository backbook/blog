---
title: 使用Livy提交spark任务
date: 2019-12-21 00:41:18
tags:
  - RunMode
  - bigdata
  - Spark
categories:
  - livy
---



### 创建交互式会话

<!--more-->

- 创建sessions的命令是通过post请求

> <b>使用macbook/linux/windows的终端进行创建 </b>

```shell
curl -X POST -d '{"kind": "spark"}' -H "Content-Type: application/json" hdp2:8999/sessions
```

> <b>我们可以通过livy的get的方式进行查询多少sessions</b>

```shell
#在chrome中可以使用
hdp2:8999/sessions
```

```shell
#在终端中可以使用
curl -X GET hdp2:8999/sessions
```



```powershell
{
from: 0,
total: 1,
sessions: [
{
id: 1,
appId: null,
owner: null,
proxyUser: null,
state: "dead",
kind: "spark",
appInfo: {
driverLogUrl: null,
sparkUiUrl: null
},
log: [
"stdout: ",
"2018-10-15 10:01:00 WARN NativeCodeLoader:62 - Unable to load native-hadoop library for your platform... using builtin-java classes where applicable",
"2018-10-15 10:01:00 INFO RSCDriver:157 - Connecting to: 192.168.7.208:10001",
"2018-10-15 10:01:00 INFO RSCDriver:179 - Starting RPC server...",
"2018-10-15 10:01:00 INFO RpcServer:105 - Connected to the port 10002",
"2018-10-15 10:01:00 WARN RSCConf:146 - Your hostname, 192.168.7.208, resolves to a loopback address, but we couldn't find any external IP address!",
"2018-10-15 10:01:00 WARN RSCConf:148 - Set livy.rsc.rpc.server.address if you need to bind to another address.",
" stderr: "
]
}
]
}
```

> <b> 根据id进行查询相应的session</b>


```powershell
#在终端输入，输入id，创建时候的id，例如 id = 1

curl localhost:8998/sessions/{id}

```


```powershell
#在chrome中输入的，输入id，创建时候的id，例如 id = 1

hdp2:8999/sessions/{id}

```





> <b>提交代码：</b>


```powershell
curl hdp2:8999/sessions/2/statements -X POST -H 'Content-Type: application/json' -d '{"code":"1 + 1"}'

```

```powershell
curl hdp2:8999/sessions/2/statements -X POST -H 'Content-Type: application/json' -d '{"code":"sc.parallelize(1 to 2).count()"}'

```

###  使用jar包的方式



>  <b>参数解释：file:是放在了hdfs上</b>

```powershell


curl -X POST --data '{"file": "/jars/spark-examples_2.11-2.3.0.2.6.5.0-292.jar", "className": "org.apache.spark.examples.SparkPi"}' -H "Content-Type: application/json" hdp2:8999/batches

```