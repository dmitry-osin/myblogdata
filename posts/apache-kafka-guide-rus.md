# Apache Kafka: Полное Руководство (Zero to Hero)

Это руководство предназначено для того, чтобы провести вас от полного непонимания Kafka до уровня, позволяющего проектировать архитектуру и писать код для высоконагруженных систем.

---

## Оглавление

- [Часть 1: Фундамент и Теория](#часть-1-фундамент-и-теория)  
  - [Что такое Kafka и зачем она нужна?](#что-такое-apache-kafka)  
  - [Ключевая архитектура: брокеры, топики, партиции](#ключевая-архитектура)  
  - [Словарь терминов](#словарь-терминов)
- [Часть 2: Настройка окружения (Docker)](#часть-2-настройка-окружения-docker)  
  - [Полный docker-compose.yml (Kafka + UI + Schema Registry)](#файл-docker-composeyml)  
  - [Инструкция по запуску](#запуск)
- [Часть 3: Практика разработки (Code Examples)](#часть-3-практика-разработки-code-examples)  
  - [Java (Native Client)](#1-java-standard)  
  - [Python (Scripting)](#2-python)  
  - [Node.js (Event Driven)](#3-nodejs)
- [Часть 4: Уровень Эксперта (Deep Dive)](#часть-4-уровень-эксперта-deep-dive)  
  - [Consumer Groups и масштабирование](#consumer-groups-группы-потребителей)  
  - [Гарантии доставки (Semantics)](#гарантии-доставки)  
  - [Log Compaction](#log-compaction)
- [Часть 5: Экосистема (Beyond Code)](#часть-5-экосистема-beyond-code)  
  - [Kafka Connect](#1-kafka-connect)  
  - [Schema Registry](#2-schema-registry)  
  - [Kafka Streams](#3-kafka-streams)
- [Следующий шаг (Action Plan)](#следующий-шаг-action-plan)

---

## Часть 1: Фундамент и Теория

### Что такое Apache Kafka?

Kafka — это распределенная платформа потоковой передачи событий (Distributed Event Streaming Platform).

В её основе лежит Commit Log (журнал фиксации). В отличие от классических очередей (RabbitMQ), Kafka:

- Не удаляет сообщения сразу после прочтения.
- Хранит историю событий на диске заданное время (retention period).
- Оптимизирована для пропускной способности (Millions msg/sec).

### Ключевая Архитектура

Чтобы понять Kafka, нужно понять иерархию хранения данных:

- **Topic (Топик)**: логическая категория сообщений (аналог таблицы в БД).
- **Partition (Партиция)**: топик разбивается на шарды. Партиция — это единица параллелизма.
- **Offset (Смещение)**: уникальный ID сообщения внутри партиции (0, 1, 2 ...). Гарантирует порядок строго внутри партиции.

### Словарь терминов

- **Producer**: пишет данные в топик.
- **Consumer**: читает данные из топика.
- **Broker**: сервер, где хранятся данные.
- **Cluster**: группа брокеров.
- **Leader & Follower**: у каждой партиции есть один Лидер (принимает запись/чтение) и Фолловеры (просто копируют данные для надежности).

---

## Часть 2: Настройка окружения (Docker)

Мы используем современный режим KRaft (без Zookeeper), веб-интерфейс Kafka UI и Schema Registry.

### Файл docker-compose.yml

Создайте файл `docker-compose.yml` и вставьте в него этот код:

```yaml
version: "3"
services:
  kafka:
    image: bitnami/kafka:latest
    container_name: kafka
    ports:
      - '9092:9092'
    environment:
      # Настройки KRaft
      - KAFKA_CFG_NODE_ID=0
      - KAFKA_CFG_PROCESS_ROLES=controller,broker
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=0@kafka:9093
      # Слушатели
      - KAFKA_CFG_LISTENERS=PLAINTEXT://:9092,CONTROLLER://:9093,INTERNAL://:29092
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092,INTERNAL://kafka:29092
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT,INTERNAL:PLAINTEXT
      - KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
      - KAFKA_CFG_INTER_BROKER_LISTENER_NAME=INTERNAL
    volumes:
      - kafka_data:/bitnami/kafka

  schema-registry:
    image: confluentinc/cp-schema-registry:latest
    container_name: schema-registry
    depends_on:
      - kafka
    ports:
      - "8081:8081"
    environment:
      SCHEMA_REGISTRY_HOST_NAME: schema-registry
      SCHEMA_REGISTRY_KAFKASTORE_BOOTSTRAP_SERVERS: 'kafka:29092'
      SCHEMA_REGISTRY_LISTENERS: http://0.0.0.0:8081

  kafka-ui:
    image: provectuslabs/kafka-ui:latest
    container_name: kafka-ui
    ports:
      - "8080:8080"
    depends_on:
      - kafka
      - schema-registry
    environment:
      KAFKA_CLUSTERS_0_NAME: local-kraft
      KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS: kafka:29092
      KAFKA_CLUSTERS_0_SCHEMAREGISTRY: http://schema-registry:8081

volumes:
  kafka_data:
```

### Запуск

- Выполните: `docker-compose up -d`
- Kafka UI: <http://localhost:8080>
- Адрес брокера для кода: `localhost:9092`

---

## Часть 3: Практика разработки (Code Examples)

### 1. Java (Standard)

Самый производительный вариант.  
Зависимости (Maven): `kafka-clients`, `slf4j-simple`.

**Producer:**

```java
Properties props = new Properties();
props.put("bootstrap.servers", "localhost:9092");
props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");

KafkaProducer<String, String> producer = new KafkaProducer<>(props);
producer.send(new ProducerRecord<>("java_topic", "key1", "Hello Java"), (m, e) -> {
    if (e == null) System.out.println("✅ Sent offset: " + m.offset());
});
producer.close();
```

**Consumer:**

```java
Properties props = new Properties();
props.put("bootstrap.servers", "localhost:9092");
props.put("group.id", "java_group"); // Важно!
props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");

KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
consumer.subscribe(Collections.singletonList("java_topic"));

while (true) {
    ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(100));
    for (var r : records) System.out.println("📩 Recv: " + r.value());
}
```

---

### 2. Python

Идеально для скриптов и ML. Библиотека: `kafka-python`.

**Producer:**

```python
from kafka import KafkaProducer
import json

producer = KafkaProducer(
    bootstrap_servers=['localhost:9092'],
    value_serializer=lambda x: json.dumps(x).encode('utf-8')
)
producer.send('py_topic', {'event': 'login', 'user': 'admin'})
producer.flush()
```

**Consumer:**

```python
from kafka import KafkaConsumer
import json

consumer = KafkaConsumer(
    'py_topic',
    bootstrap_servers=['localhost:9092'],
    group_id='py_group',
    value_deserializer=lambda x: json.loads(x.decode('utf-8'))
)
for msg in consumer:
    print(f"📩 {msg.value}")
```

---

### 3. Node.js

Асинхронная обработка. Библиотека: `kafkajs`.

**Producer:**

```js
const { Kafka } = require('kafkajs');
const kafka = new Kafka({ clientId: 'node-app', brokers: ['localhost:9092'] });
const producer = kafka.producer();

const run = async () => {
  await producer.connect();
  await producer.send({
    topic: 'node_topic',
    messages: [{ value: 'Hello Node' }],
  });
  await producer.disconnect();
};
run();
```

---

## Часть 4: Уровень Эксперта (Deep Dive)

### Consumer Groups (Группы потребителей)

Это механизм масштабирования Kafka.

- Все консьюмеры с одинаковым `group.id` объединяются в группу.
- Kafka распределяет партиции между участниками группы.
- Правило: **1 партиция может читаться только 1 консьюмером внутри группы**.
  - Есть 4 партиции, 2 консьюмера: каждому по 2.
  - Есть 4 партиции, 5 консьюмеров: один будет бездельничать (Idle).

### Гарантии доставки

- **At most once (Максимум один раз)**: `acks=0`. Сообщение может потеряться, но не дублируется.
- **At least once (Минимум один раз)**: `acks=all`. Сообщение не потеряется, но может прийти дважды (нужна идемпотентность на клиенте). Это стандарт.
- **Exactly Once (Ровно один раз)**: требует транзакций и специальной настройки продюсера (`enable.idempotence=true`).

### Log Compaction

Для топиков, хранящих состояние (например, "текущий адрес пользователя"), Kafka может удалять старые сообщения, оставляя только последнее значение для каждого ключа. Это экономит место.

---

## Часть 5: Экосистема (Beyond Code)

### 1. Kafka Connect

Инструмент для интеграции без написания кода.

- **Source**: База данных → Kafka (например, через Debezium CDC).
- **Sink**: Kafka → Elasticsearch / S3 / Snowflake.
- Работает через конфигурационные JSON-файлы.

### 2. Schema Registry

Решает проблему "грязных данных" и изменений форматов.

- Использует формат Avro или Protobuf.
- Хранит структуру данных (схему) отдельно от самих данных.
- Продюсер валидирует данные перед отправкой. Если поле `age` должно быть `int`, а вы шлете `string`, отправка не пройдёт.

### 3. Kafka Streams

Библиотека Java для аналитики "на лету".

- Позволяет делать `map`, `filter`, `join`, `aggregation` (подсчет сумм, средних значений) в реальном времени.
- Примеры: Word Count, Fraud Detection.

**Пример (Word Count на Kafka Streams):**

```java
KStream<String, String> text = builder.stream("input");
KTable<String, Long> counts = text
    .flatMapValues(v -> Arrays.asList(v.split("\\W+")))
    .groupBy((key, word) -> word)
    .count();
counts.toStream().to("output");
```

---

## Следующий шаг (Action Plan)

- Запустите Docker Compose.
- Создайте топик `test-topic` с 3 партициями через Kafka UI.
- Напишите Python Producer, который шлет туда случайные числа.
- Запустите 3 экземпляра Java Consumer (один и тот же код, запущенный в 3 разных терминалах) и посмотрите, как они поделят нагрузку.