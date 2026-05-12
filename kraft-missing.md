This is actually **the key clue** 👍  
Nothing is broken — it just means **this Kafka distribution does not have a `config/kraft/` directory**.

Let’s fix this **cleanly and correctly**, step by step.

***

## ✅ Why `/opt/kafka/config/kraft` does not exist

In your Kafka installation:

```text
ls: cannot access '/opt/kafka/config/kraft': No such file or directory
```

This means:

*   Your Kafka **does not ship with pre‑split KRaft configs**
*   Instead, it uses a **single config file**:

👉 **`/opt/kafka/config/server.properties`**

This is **normal** for some Kafka distributions.

***

## ✅ Confirm what config files you actually have

Run:

```bash
ls /opt/kafka/config
```

You should see something like:

```text
server.properties
producer.properties
consumer.properties
```

✅ This confirms your Kafka is installed correctly.

***

## ✅ Correct approach for YOUR installation (Single‑broker KRaft)

You will use:

    /opt/kafka/config/server.properties

and explicitly enable **KRaft mode** inside it.

***

# ✅ Step‑by‑Step Fix (WORKING)

## Step 1 — Edit the correct config file

```bash
nano /opt/kafka/config/server.properties
```

***

## Step 2 — Replace / ensure these KRaft settings exist

For a **single broker VM**, use this **minimal but correct** config:

```properties
############################# KRaft #############################

process.roles=broker,controller
node.id=1

controller.listener.names=CONTROLLER
controller.quorum.voters=1@localhost:9093

############################# Network #############################

listeners=PLAINTEXT://0.0.0.0:9092,CONTROLLER://0.0.0.0:9093
advertised.listeners=PLAINTEXT://<BROKER_IP>:9092

############################# Logs #############################

log.dirs=/tmp/kafka-logs

############################# Topic settings #############################

default.replication.factor=1
offsets.topic.replication.factor=1
transaction.state.log.replication.factor=1
transaction.state.log.min.isr=1

############################# Safety #############################

unclean.leader.election.enable=false
```

🔁 Replace `<BROKER_IP>` with:

*   VM private IP (recommended)
*   OR hostname

***

## ✅ Step 3 — Generate a cluster ID (REQUIRED)

```bash
kafka-storage.sh random-uuid
```

Example:

```text
Y7n5xA1qSFiJf8K0XkJVZQ
```

***

## ✅ Step 4 — Format Kafka storage (MANDATORY)

```bash
kafka-storage.sh format \
  -t Y7n5xA1qSFiJf8K0XkJVZQ \
  -c /opt/kafka/config/server.properties
```

✅ This initializes KRaft metadata  
✅ Must run **once only**

***

## ✅ Step 5 — Start Kafka broker

```bash
kafka-server-start.sh /opt/kafka/config/server.properties
```

If successful, you’ll see logs like:

    Kafka Server started

🎉 Your broker is running.

***

## ✅ Step 6 — Verify broker is reachable

```bash
kafka-broker-api-versions.sh --bootstrap-server <BROKER_IP>:9092
```

✅ Output = success

***

# ✅ What happened (simple explanation)

*   ❌ `config/kraft/` does not exist in your Kafka package
*   ✅ Kafka is configured via **`server.properties` instead**
*   ✅ KRaft is enabled via properties, not directory structure
*   ✅ This is a **supported and correct setup**

***

# ✅ You are now here in the lifecycle

✅ Kafka installed  
✅ KRaft enabled  
✅ Broker running

Next logical steps:

1.  ✅ Create topics
2.  ✅ Start producer
3.  ✅ Start consumer
4.  ✅ Move logs out of `/tmp`
5.  ✅ Convert to systemd service

***

## 🚀 What do you want to do next?

I can guide you **next** through:

*   ✅ Topic creation
*   ✅ Producer / consumer setup
*   ✅ Fixing `advertised.listeners`
*   ✅ Moving to multi‑broker
*   ✅ Production hardening

Just tell me 👍
