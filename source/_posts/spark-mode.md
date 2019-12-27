---
title: spark-mode
date: 2019-12-21 00:19:18
tags:
  - run mode
  - spark
  - bigdata
categories:
  - spark
---

## spark运行模式大全

<!--more-->

1. spark on k8s

> ```powershell
> bin/spark-submit \
>  --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
>  --deploy-mode cluster \
>  --name spark-pi \
>  --class org.apache.spark.examples.SparkPi \
>  --conf spark.executor.instances=5 \
>  --conf spark.kubernetes.container.image=<spark-image> \
>  local:///path/to/examples.jar
> ```

- 以本地的例子为例子

  > ```powershell
  > $ kubectl cluster-info
  > Kubernetes master is running at http://127.0.0.1:6443
  > ```

- 参考例子可以写成如下：

  > > ```powershell
  > > bin/spark-submit \
  > >   --master k8s://http://127.0.0.1:6443 \
  > >   --deploy-mode cluster \
  > >   --name spark-pi \
  > >   --class org.apache.spark.examples.SparkPi \
  > >   --conf spark.executor.instances=5 \
  > >   --conf spark.kubernetes.container.image=<spark-image> \
  > >   local:///Users/backbook/bigdata/spark-2.4.0/examples/jars/spark-examples_2.11-2.4.0.jar
  > > ```

