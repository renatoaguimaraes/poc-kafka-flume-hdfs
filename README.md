# kafka-flume-hdfs-stream

## Kafka
Kafka is used for building real-time data pipelines and streaming apps. It is horizontally scalable, fault-tolerant, wicked fast, and runs in production in thousands of companies.
### Zookeeper
ZooKeeper is a centralized service for maintaining configuration information, naming, providing distributed synchronization, and providing group services.
```
bin/zookeeper-server-start.sh config/zookeeper.properties
```
### Kafka server
```
bin/kafka-server-start.sh config/server.properties
```
### Topic
```
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
```
### Listing topics
```
bin/kafka-topics.sh --list --zookeeper localhost:2181
```
### Productor
```
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
```
### Consumer
```
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
```
### Consumer File Connector 
```
bin/connect-standalone.sh config/connect-standalone.properties config/connect-file-source.properties
```
## Flume
Apache Flume is a distributed, reliable, and available system for efficiently collecting, aggregating and moving large amounts of log data from many different sources to a centralized data store.

kafka-source.properties

```
flume1.sources = kafka-source-1
flume1.channels = memoryChannel
flume1.sinks = loggerSink

flume1.sources.kafka-source-1.type = org.apache.flume.source.kafka.KafkaSource
flume1.sources.kafka-source-1.zookeeperConnect = localhost:2181
flume1.sources.kafka-source-1.topic = test
flume1.sources.kafka-source-1.batchSize = 100
flume1.sources.kafka-source-1.channels = memoryChannel
flume1.sources.kafka-source-1.interceptors = i1
flume1.sources.kafka-source-1.interceptors.i1.type = timestamp
flume1.sources.kafka-source-1.kafka.consumer.timeout.ms = 100

#flume1.sinks.loggerSink.channel = memoryChannel
#flume1.sinks.loggerSink.type = file_roll
#flume1.sinks.loggerSink.sink.directory = /Users/renato/Downloads/poc-kafka
#flume1.sinks.loggerSink.sink.serializer = text

flume1.sinks.loggerSink.type = hdfs
flume1.sinks.loggerSink.hdfs.path = hdfs://localhost:9000/user/renato/%{topic}/%y-%m-%d
flume1.sinks.loggerSink.hdfs.rollInterval = 5
flume1.sinks.loggerSink.hdfs.rollSize = 0
flume1.sinks.loggerSink.hdfs.rollCount = 0
flume1.sinks.loggerSink.hdfs.threadsPoolSize = 10
flume1.sinks.loggerSink.hdfs.fileType = DataStream
flume1.sinks.loggerSink.channel = memoryChannel

flume1.channels.memoryChannel.type = memory
flume1.channels.memoryChannel.capacity = 10000
flume1.channels.memoryChannel.transactionCapacity = 1000
```

```
bin/flume-ng agent -n flume1 -c conf -f conf/kafka-source.properties -Dflume.root.logger#INFO,console --classpath /Users/renato/Downloads/poc-kafka/hadoop-2.7.3/share/hadoop/common/lib/zookeeper-3.4.6.jar:/Users/renato/Downloads/poc-kafka/hadoop-2.7.3/share/hadoop/common/hadoop-common-2.7.3.jar:/Users/renato/Downloads/poc-kafka/hadoop-2.7.3/share/hadoop/common/lib/commons-configuration-1.6.jar:/Users/renato/Downloads/poc-kafka/hadoop-2.7.3/share/hadoop/common/lib/hadoop-auth-2.7.3.jar:/Users/renato/Downloads/poc-kafka/hadoop-2.7.3/share/hadoop/hdfs/hadoop-hdfs-2.7.3.jar:/Users/renato/Downloads/poc-kafka/hadoop-2.7.3/share/hadoop/hdfs/lib/htrace-core-3.1.0-incubating.jar:/Users/renato/Downloads/poc-kafka/hadoop-2.7.3/share/hadoop/hdfs/lib/commons-io-2.4.jar 
```

## Hadoop
The Apache Hadoop software library is a framework that allows for the distributed processing of large data sets across clusters of computers using simple programming models. It is designed to scale up from single servers to thousands of machines, each offering local computation and storage. Rather than rely on hardware to deliver high-availability, the library itself is designed to detect and handle failures at the application layer, so delivering a highly-available service on top of a cluster of computers, each of which may be prone to failures.

core-site.xml
```
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```
hdfs-site.xml
```
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
</configuration>
```
mapred-site.xml
```
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```
yarn-site.xml
```
<configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
</configuration>
```
```
sbin/start-dfs.sh

sbin/start-yarn.sh

bin/hdfs dfs -mkdir /user

bin/hdfs dfs -mkdir /user/renato

bin/hdfs dfs -rmr hdfs://localhost:9000/user/renato/test

bin/hdfs dfs -find hdfs://localhost:9000/user/renato/test/16-11-02/ -name Flume* -print

bin/hdfs dfs -cat hdfs://localhost:9000/user/renato/test/16-11-02/FlumeData.1478134513188
```

## Links
https://kafka.apache.org/documentation#quickstart

http://zhongyaonan.com/hadoop-tutorial/setting-up-hadoop-2-6-on-mac-osx-yosemite.html

https://hadoop.apache.org/docs/r2.7.2/hadoop-project-dist/hadoop-common/FileSystemShell.html

https://www.cloudera.com/documentation/kafka/latest/topics/kafka_flume.html

http://blog.cloudera.com/blog/2014/11/flafka-apache-flume-meets-apache-kafka-for-event-processing/

https://flume.apache.org/FlumeUserGuide.html

http://howtoprogram.xyz/2016/08/06/apache-flume-kafka-source-and-hdfs-sink/

https://tools.ietf.org/html/rfc4180

