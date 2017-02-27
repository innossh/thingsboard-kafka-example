# thingsboard-kafka-example

This is an example of Thingsboard with Kafka plugin.


## Getting Started

First you can start the containers by docker-compose.

```
$ docker-compose up -d

# Wait a minute

$ docker-compose ps
                     Name                                    Command               State                                    Ports
------------------------------------------------------------------------------------------------------------------------------------------------------------------
thingsboardkafkaexample_db_1                      /docker-entrypoint.sh cass ...   Up       7000/tcp, 7001/tcp, 7199/tcp, 9042/tcp, 9160/tcp
thingsboardkafkaexample_kafka_1                   start-kafka.sh                   Up       0.0.0.0:32809->9092/tcp
thingsboardkafkaexample_kafka_zookeeper_1         /bin/sh -c /usr/sbin/sshd  ...   Up       0.0.0.0:2181->2181/tcp, 22/tcp, 2888/tcp, 3888/tcp
thingsboardkafkaexample_thingsboard-db-schema_1   ./install_schema.sh              Exit 0
thingsboardkafkaexample_thingsboard_1             ./run_thingsboard.sh             Up       0.0.0.0:1883->1883/tcp, 0.0.0.0:5683->5683/tcp, 0.0.0.0:8080->8080/tcp
thingsboardkafkaexample_zk_1                      /docker-entrypoint.sh zkSe ...   Up       2181/tcp, 2888/tcp, 3888/tcp
```

Then you can login to Web UI of Thingsboard with default username and password `tenant@thingsboard.org/tenant`.  
Some devices have already registered in Thingsboard, so you can use the device `Test Device A1` and the access token `A1_TEST_TOKEN`.

...

Next you will configure and activate new Kafka plugin, then you will create and activate new Kafka rule.  
Please refer to the following link.
[https://thingsboard.io/docs/reference/plugins/kafka/](https://thingsboard.io/docs/reference/plugins/kafka/)

NOTE: Please set correctly Kafka Bootstrap Servers URL as below.

> Plugin configuration
> 
> Bootstrap Servers: `172.17.0.1:9092`

...

You will post new telemetry of Test Device A1.

```
# Prepare consuming on terminal A
$ cd kafka
$ ./start-kafka-shell.sh 172.17.0.1 172.17.0.1:2181
bash-4.3# $KAFKA_HOME/bin/kafka-console-consumer.sh --topic=test-topic --zookeeper=$ZK
Using the ConsoleConsumer with old consumer is deprecated and will be removed in a future major release. Consider using the new consumer by passing [bootstrap-server] instead of [zookeeper].
```

```
# Post new telemetry on terminal B
$ THINGSBOARD_HOST=localhost
  THINGSBOARD_PORT=8080
  ACCESS_TOKEN=A1_TEST_TOKEN
  curl -s -X POST http://$THINGSBOARD_HOST:$THINGSBOARD_PORT/api/v1/$ACCESS_TOKEN/telemetry -H "Content-Type:application/json" -d '{"temp":73.4}'
```

Finally you can consume temp telemetry!

```
# terminal A
73.4
```

## References

- [Installing Thingsboard using Docker (Linux or Mac OS)](https://thingsboard.io/docs/user-guide/install/docker/)
- [Dockerfile for Apache Kafka](https://github.com/wurstmeister/kafka-docker)
