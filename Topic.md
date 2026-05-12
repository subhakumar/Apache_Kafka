Nice 🎉  
Let’s walk through the **next practical steps** to create and verify a Kafka topic now that your broker is running.

I’ll assume:

*   **KRaft mode**
*   **Single broker**
*   Kafka `bin/` directory on your PATH or you’re running from the Kafka install dir

***

## 1️⃣ Create a topic

### Basic topic creation (single broker)

```bash
/opt/kafka/bin/kafka-topics.sh \
  --bootstrap-server localhost:9092 \
  --create \
  --topic test-topic \
  --partitions 1 \
  --replication-factor 1


/opt/kafka/bin/kafka-topics.sh \
  --bootstrap-server 10.10.194.150:9092 \
  --create \
  --topic test-topic \
  --partitions 1 \
  --replication-factor 1
```

✅ Expected output:

    Created topic test-topic.

> ✅ With one broker, **replication-factor must be 1**.

***

## 2️⃣ Verify the topic exists

```bash
bin/kafka-topics.sh \
  --bootstrap-server localhost:9092 \
  --list
```

You should see:

    test-topic

***

## 3️⃣ Describe the topic (always useful)

```bash
bin/kafka-topics.sh \
  --bootstrap-server localhost:9092 \
  --describe \
  --topic test-topic
```

Example output:

    Topic: test-topic  TopicId: ...  PartitionCount: 1  ReplicationFactor: 1
    Partition: 0  Leader: 1  Replicas: 1  Isr: 1

✅ Confirms leader, ISR, and partition health.

***

## 4️⃣ Produce messages (quick sanity test)

```bash
bin/kafka-console-producer.sh \
  --bootstrap-server localhost:9092 \
  --topic test-topic
```

Type a few messages:

    hello kafka
    this is working
    ^D   (Ctrl+D to exit)

***

## 5️⃣ Consume messages

From another terminal:

```bash
bin/kafka-console-consumer.sh \
  --bootstrap-server localhost:9092 \
  --topic test-topic \
  --from-beginning
```

You should see:

    hello kafka
    this is working

✅ Broker + topic + storage all confirmed working.

***

## 6️⃣ (Optional but recommended) Auto topic creation OFF

For better control (especially later), check or set:

In `server.properties`:

```properties
auto.create.topics.enable=false
```

Then restart Kafka.

***

## 7️⃣ Useful next steps (pick what you need)

### Create a topic with config

```bash
bin/kafka-topics.sh \
  --bootstrap-server localhost:9092 \
  --create \
  --topic orders \
  --partitions 3 \
  --replication-factor 1 \
  --config cleanup.policy=delete \
  --config retention.ms=604800000
```

### Check broker + KRaft health

```bash
bin/kafka-metadata-quorum.sh \
  --bootstrap-server localhost:9092 \
  describe --status
```

***

## ✅ You’re officially past the hard part

Startup issues in **KRaft mode** are the hardest.  
From here on it’s normal Kafka usage.

If you want, next I can help you with:

*   Multiple partitions & ordering strategy
*   Adding another broker
*   ACLs / security
*   Java / Spring Boot producer & consumer
*   Schema Registry setup

Just tell me what’s next 🚀
