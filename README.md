# kafka-flume-hdfs-stream
## Kafka

# Zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

# Kafka server
bin/kafka-server-start.sh config/server.properties

# Criando tópico
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test

# Listando tópicos
bin/kafka-topics.sh --list --zookeeper localhost:2181

# Produtor
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test

# Consumidor
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning

# Produtor conectado no arquivo 
bin/connect-standalone.sh config/connect-standalone.properties config/connect-file-source.properties

## Flume

bin/flume-ng agent -n flume1 -c conf -f conf/kafka-source.properties -Dflume.root.logger#INFO,console --classpath /Users/renato/Downloads/poc-kafka/hadoop-2.7.3/share/hadoop/common/lib/zookeeper-3.4.6.jar:/Users/renato/Downloads/poc-kafka/hadoop-2.7.3/share/hadoop/common/hadoop-common-2.7.3.jar:/Users/renato/Downloads/poc-kafka/hadoop-2.7.3/share/hadoop/common/lib/commons-configuration-1.6.jar:/Users/renato/Downloads/poc-kafka/hadoop-2.7.3/share/hadoop/common/lib/hadoop-auth-2.7.3.jar:/Users/renato/Downloads/poc-kafka/hadoop-2.7.3/share/hadoop/hdfs/hadoop-hdfs-2.7.3.jar:/Users/renato/Downloads/poc-kafka/hadoop-2.7.3/share/hadoop/hdfs/lib/htrace-core-3.1.0-incubating.jar:/Users/renato/Downloads/poc-kafka/hadoop-2.7.3/share/hadoop/hdfs/lib/commons-io-2.4.jar 

## Hadoop

sbin/start-dfs.sh

sbin/start-yarn.sh

bin/hdfs dfs -mkdir /user

bin/hdfs dfs -mkdir /user/renato

bin/hdfs dfs -rmr hdfs://localhost:9000/user/renato/test

bin/hdfs dfs -find hdfs://localhost:9000/user/renato/test/16-11-02/ -name Flume* -print

bin/hdfs dfs -cat hdfs://localhost:9000/user/renato/test/16-11-02/FlumeData.1478134513188

## Links úteis
https://kafka.apache.org/documentation#quickstart

http://zhongyaonan.com/hadoop-tutorial/setting-up-hadoop-2-6-on-mac-osx-yosemite.html

https://hadoop.apache.org/docs/r2.7.2/hadoop-project-dist/hadoop-common/FileSystemShell.html

https://www.cloudera.com/documentation/kafka/latest/topics/kafka_flume.html

http://blog.cloudera.com/blog/2014/11/flafka-apache-flume-meets-apache-kafka-for-event-processing/

https://flume.apache.org/FlumeUserGuide.html

http://howtoprogram.xyz/2016/08/06/apache-flume-kafka-source-and-hdfs-sink/

https://tools.ietf.org/html/rfc4180

