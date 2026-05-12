Here’s a **clean, production‑ready `systemd` service** to run Kafka using:

    /opt/kafka/bin/kafka-server-start.sh /opt/kafka/config/server.properties

This works for **KRaft or ZooKeeper mode** and follows systemd best practices.

***

## 1️⃣ Create the systemd service file

```bash
sudo vi /etc/systemd/system/kafka.service
```

Paste the following:

```ini
[Unit]
Description=Apache Kafka Server
After=network.target

[Service]
Type=simple
User=kafka
Group=kafka

# Kafka installation directory
Environment=KAFKA_HOME=/opt/kafka
Environment=JAVA_HOME=/usr/lib/jvm/java-11-openjdk

WorkingDirectory=/opt/kafka

ExecStart=/opt/kafka/bin/kafka-server-start.sh /opt/kafka/config/server.properties
ExecStop=/opt/kafka/bin/kafka-server-stop.sh

Restart=on-failure
RestartSec=10

LimitNOFILE=100000

[Install]
WantedBy=multi-user.target
```

***

## 2️⃣ Create Kafka user (if not already present)

```bash
sudo useradd kafka
sudo chown -R kafka:kafka /opt/kafka
```

*(Kafka should not run as root.)*

***

## 3️⃣ Reload systemd and enable Kafka

```bash
sudo systemctl daemon-reload
sudo systemctl enable kafka
```

***

## 4️⃣ Start Kafka

```bash
sudo systemctl start kafka
```

Check status:

```bash
sudo systemctl status kafka
```

***

## 5️⃣ View Kafka logs

```bash
journalctl -u kafka -f
```

***

## ✅ Common adjustments (optional)

### 🔹 Change Java version

Update this line if needed:

```ini
Environment=JAVA_HOME=/usr/lib/jvm/java-17-openjdk
```

### 🔹 Delay startup until disks are ready

```ini
After=network.target local-fs.target
```

### 🔹 Increase memory (recommended)

Instead of `ExecStart`, use:

```ini
ExecStart=/opt/kafka/bin/kafka-server-start.sh \
  /opt/kafka/config/server.properties \
  --override KAFKA_HEAP_OPTS="-Xms2G -Xmx2G"
```

***

## ✅ Verify Kafka is listening

```bash
ss -lntp | grep 9092
```

Test:

```bash
/opt/kafka/bin/kafka-broker-api-versions.sh --bootstrap-server localhost:9092
```

***

## ✅ TL;DR

*   Service file: `/etc/systemd/system/kafka.service`
*   Kafka runs as `kafka` user
*   Auto‑restart enabled
*   Production‑safe defaults

If you want, I can provide:

*   **Separate controller + broker services**
*   **KRaft‑only service**
*   **Environment‑specific tuning (dev vs prod)**
