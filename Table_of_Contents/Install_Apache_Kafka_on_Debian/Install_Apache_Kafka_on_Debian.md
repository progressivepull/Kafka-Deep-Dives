# How to install Apache Kafka on Debian

To install Apache Kafka on Debian, follow these steps.

## 1. Update the system

```bash
sudo apt update
sudo apt upgrade -y
```


## 2. Install Java

Kafka requires Java.

```bash
sudo apt install default-jdk -y
``` 

Verify:

```bash
java -version
```

## 3. Download Kafka

Check the latest version at https://kafka.apache.org/downloads, then download it. Example:

```bash
wget https://downloads.apache.org/kafka/3.9.1/kafka_2.13-3.9.1.tgz
```

Extract it:

```bash
tar -xzf kafka_2.13-3.9.1.tgz
mv kafka_2.13-3.9.1 kafka
cd kafka
```

## 4. Start Kafka

Modern Kafka can run without ZooKeeper using KRaft mode.

Generate a cluster ID:

```bash
KAFKA_CLUSTER_ID="$(bin/kafka-storage.sh random-uuid)"
```

Format the storage directory:

```bash
bin/kafka-storage.sh format \
--standalone \
-t $KAFKA_CLUSTER_ID \
-c config/kraft/reconfig-server.properties
```

Alternatively, for a single-node server:

```bash
bin/kafka-storage.sh format \
-t $KAFKA_CLUSTER_ID \
-c config/kraft/server.properties
```

Start Kafka:

```bash
bin/kafka-server-start.sh config/kraft/server.properties
```

## 5. Test Kafka

Open another terminal.

Create a topic:

```bash
bin/kafka-topics.sh \
--create \
--topic test \
--bootstrap-server localhost:9092
```

List topics:

```bash
bin/kafka-topics.sh \
--list \
--bootstrap-server localhost:9092
```

Start a producer:

```bash
bin/kafka-console-producer.sh \
--topic test \
--bootstrap-server localhost:9092
```

Start a consumer:

```bash
bin/kafka-console-consumer.sh \
--topic test \
--from-beginning \
--bootstrap-server localhost:9092
```

Type messages in the producer terminal and verify they appear in the consumer terminal.

## Optional: Run Kafka as a service

Create a systemd service file:

```bash
sudo nano /etc/systemd/system/kafka.service
```

Example:

```
[Unit]
Description=Apache Kafka
After=network.target
 
[Service]
Type=simple
User=kafka
ExecStart=/opt/kafka/bin/kafka-server-start.sh /opt/kafka/config/kraft/server.properties
ExecStop=/opt/kafka/bin/kafka-server-stop.sh
Restart=on-failure
 
[Install]
WantedBy=multi-user.target
```

Enable and start:

```bash
sudo systemctl daemon-reload
sudo systemctl enable kafka
sudo systemctl start kafka
sudo systemctl status kafka
```


# [README](./../../README.md)