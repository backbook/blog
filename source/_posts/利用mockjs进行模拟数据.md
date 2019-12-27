---
title: 利用mockjs进行模拟数据
date: 2019-12-21 20:43:32
tags: 
    - JavaScript
    - Kafka
categories:
    - Kafka
---

### 使用mockjs进行数据的模拟

#### 是什么原因让我选择了mockjs进行测试数据的构建？

<!--more-->

- 在使用多个库模拟数据之后，接触到了mockJs这个库，在这个库中他的生成结构简单，易用，能为我们带来很多的便利。
- 分别列举几大数据库的开发使用测试，第一就是原生的java的写法，这种需要你去开发一个资源库，然后进行模拟使用，这种方式有个缺点，就是需要去编写库，周期长。
- 第二个就是python的faker库，在python写脚本的时候，确实能够用来模拟一些响应的数据，但是数据还是很多缺陷，往往就是库里面的东西太少。
- mockjs推荐的原因更多是因为其语法简单，和scala的语法很像，本人更偏喜欢scala的编程风格，面向函数式编程，可以规避到很多代码的基本使用，仅仅对于数据而言。

#### 生成的数据用途

- 往往在前端代码可以使用数据进行测试你的代码健壮性，而不依赖于其他的数据，比如线上数据，比如对接方数据，这样就可以让我们在开发的时候，可以直接进行测试而不局限于数据的不可预见
- 在大数据的使用的时候，大数据更多的是线上的真实数据，很多是难以拷贝大量测试，这样就我们就需要将我们的代码拷贝到线上，然后打包测试，然后看报错异常，然后再调试，反复过程，假如你没有一键部署的时候。
- 当然我们更希望是整体集成测试的，而不是这种方式，不过这种方式是更加灵活的，对于后期也许可以加入到集成系统中，然后进行整体的规划测试使用。

#### 依赖此模块【mockjs】的数据产品

- Easy-mock <https://www.easy-mock.com/login>

#### 使用该数据的脚本

> 说明：这个数据模块是依赖nodejs，需要有npm库

```javascript
var kafka = require('kafka-node');
var Mock = require('mockjs');
const Random = Mock.Random;

var hostName = "127.0.0.1:9092"
//topic 名称写入时候，如果不存在的话，会先创建topic
var topicName = "asetloan_db_t_cash_loan_v3"
var type=new Array(3)
type[0]="INSERT"
type[1]="UPDATE"
type[2]="DELETE"          



let conn = { 'kafkaHost':hostName};
var MQ = function () {
    this.mq_producers = {};
    this.client = {};
}
MQ.prototype.AddProducer = function (conn, handler) {
    console.log('增加生产者', conn, this);
    this.client = new kafka.KafkaClient(conn);
    let producer = new kafka.Producer(this.client);
    producer.on('ready', function () {
        if (!!handler) {
            handler(producer);
        }
    });
    producer.on('error', function (err) {
        console.error('producer error ', err.stack);
    });

    this.mq_producers['common'] = producer;
    return producer;
}
console.log(MQ);
var mq = new MQ();

mq.AddProducer(conn, function (producer) {
    producer.createTopics([topicName], function () {
        setInterval(function () {
            //只需要改这开就可以了，了解mockjs的数据用法
            let data = Mock.mock(
              
                {
                'id|+1': 1,
                'email': '@email',
                'age|28-35': 0,
                'address': '@county(false)',
                'date': '@date'
                 }
                  
            )
            let msg = JSON.stringify(data)
            var _msg = {   topic: [topicName], messages: msg  }
            // console.log('clientId : ',mq.client.clientId);
            // console.log('topicMetadata ',mq.client.topicMetadata);
            // console.log('brokerMetadata ',mq.client.brokerMetadata);
            // console.log('clusterMetadata ',mq.client.clusterMetadata);
            // console.log('brokerMetadataLastUpdate ',mq.client.brokerMetadataLastUpdate);
            mq.mq_producers['common'].send([_msg], function (err, data) {
                console.log("send you can check \n kafka-console-consumer.sh --bootstrap-server "+hostName+" --topic "+topicName+" --from-beginning \n", data);
            })
        }, 2000);
    })
});
```

