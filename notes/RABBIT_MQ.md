# RabbitMQ
---

### Installation
Follow the links to install RabbitMQ on specific operating systems:
* [Debian and Ubuntu](https://www.rabbitmq.com/docs/install-debian)
* [RedHat](https://www.rabbitmq.com/docs/install-rpm)
* [Windows](https://www.rabbitmq.com/docs/install-windows)
* [MacOS](https://www.rabbitmq.com/docs/install-homebrew)

### Management UI
RabbitMQ comes with a web-based management interface that enables you to monitor and manage your RabbitMQ server.

#### Enabling the Management Plugin
To enable the RabbitMQ Management Plugin, run the following command:
```bash
rabbitmq-plugins enable rabbitmq_management
```

Once enabled, the Management UI will be accessible at `http://localhost:15672/`.

#### Accessing the Management UI
1. Open a web browser and navigate to `http://localhost:15672/`.
2. Log in with the default credentials:
   * **Username**: guest
   * **Password**: guest

#### Features of the Management UI
* **Overview**: Provides an at-a-glance view of your RabbitMQ instance, including node status, message rates, and more.
* **Connections**: Shows the current connections to the RabbitMQ broker.
* **Channels**: Displays channels for each connection.
* **Exchanges**: Lists all exchanges, allows creation of new exchanges, and provides details about each.
* **Queues**: Lists all queues, allows creation of new queues, and provides details about each.
* **Bindings**: Shows bindings between exchanges and queues.
* **Admin**: Allows user management, virtual host management, and permissions configuration.
* **Policies**: Enables setting policies for queues and exchanges to manage features like message TTL, max length, etc.

### Important Concepts
1. **Exchange**: Receives messages from producers and routes them to queues. Types include:
   * **Direct**: Routes messages with a specific routing key.
   * **Fanout**: Routes messages to all bound queues indiscriminately.
   * **Topic**: Routes messages based on wildcard matches between the routing key and the routing pattern specified in the binding.
   * **Headers**: Uses headers to route messages.

2. **Queue**: Stores messages until they are consumed by a consumer.

3. **Binding**: A link between a queue and an exchange.

4. **Routing Key**: Used by exchanges to determine how to route the message to queues.

5. **Consumer**: Receives messages from queues.

6. **Producer**: Sends messages to exchanges.

### Working with RabbitMQ in Node.js
1. **amqplib**: A popular library for working with RabbitMQ in Node.js.
    ```bash
    npm install amqplib
    ```

2. **Basic Example**: A simple producer and consumer in Node.js.
   ```javascript
   // Producer
   const amqp = require('amqplib/callback_api');

   amqp.connect('amqp://localhost', (error0, connection) => {
       if (error0) {
           throw error0;
       }
       connection.createChannel((error1, channel) => {
           if (error1) {
               throw error1;
           }
           const queue = 'hello';
           const msg = 'Hello World!';

           channel.assertQueue(queue, {
               durable: false
           });

           channel.sendToQueue(queue, Buffer.from(msg));

           console.log(" [x] Sent %s", msg);
       });

       setTimeout(() => {
           connection.close();
           process.exit(0);
       }, 500);
   });

   // Consumer
   const amqp = require('amqplib/callback_api');

   amqp.connect('amqp://localhost', (error0, connection) => {
       if (error0) {
           throw error0;
       }
       connection.createChannel((error1, channel) => {
           if (error1) {
               throw error1;
           }
           const queue = 'hello';

           channel.assertQueue(queue, {
               durable: false
           });

           console.log(" [*] Waiting for messages in %s. To exit press CTRL+C", queue);

           channel.consume(queue, (msg) => {
               console.log(" [x] Received %s", msg.content.toString());
           }, {
               noAck: true
           });
       });
   });
   ```

### Best Practices
1. **Connection Management**: Reuse connections and channels. Avoid creating new connections/channels for each operation.
2. **Error Handling**: Implement proper error handling for connection and channel operations.
3. **Message Acknowledgement**: Use acknowledgements to ensure messages are not lost. RabbitMQ supports manual and automatic acknowledgements.
4. **Durability**: Ensure queues and messages are durable to survive broker restarts.
5. **Prefetch Count**: Use prefetch to limit the number of messages sent to a consumer before an acknowledgement is received.
6. **Dead Letter Exchanges (DLX)**: Use DLX for handling messages that cannot be delivered.

### Advanced Features
1. **Priority Queues**: Use priority queues to handle messages with different priorities.
2. **TTL (Time-to-Live)**: Set TTL for messages and queues.
3. **Delayed Message Plugin**: Use this plugin to delay message delivery.
4. **Shovel and Federation**: Use these features for data replication and distribution across different RabbitMQ nodes/clusters.
5. **Monitoring and Management**: Use RabbitMQ management plugin for monitoring, and tools like Prometheus and Grafana for advanced monitoring.

# RabbitMQ Best Practices and Advanced Features

## Best Practices

### 1. Connection Management
Reuse connections and channels to avoid the overhead of creating new ones for each operation.

**Example:**
```javascript
const amqp = require('amqplib');

let connection;
let channel;

async function connectRabbitMQ() {
    try {
        connection = await amqp.connect('amqp://localhost');
        channel = await connection.createChannel();
    } catch (error) {
        console.error("Connection error:", error);
    }
}

async function closeRabbitMQ() {
    try {
        await channel.close();
        await connection.close();
    } catch (error) {
        console.error("Closing error:", error);
    }
}

process.on('exit', () => closeRabbitMQ());

// Usage
(async () => {
    await connectRabbitMQ();
    // Use the `channel` for operations
})();
```

### 2. Error Handling
Implement proper error handling for connection and channel operations.

**Example:**
```javascript
async function sendMessage(queue, message) {
    try {
        await channel.assertQueue(queue, { durable: true });
        await channel.sendToQueue(queue, Buffer.from(message), { persistent: true });
        console.log("Message sent:", message);
    } catch (error) {
        console.error("Send error:", error);
    }
}
```

### 3. Message Acknowledgement
Use acknowledgements to ensure messages are not lost.

**Example:**
```javascript
async function consumeMessages(queue) {
    try {
        await channel.assertQueue(queue, { durable: true });
        await channel.consume(queue, (msg) => {
            if (msg !== null) {
                console.log("Message received:", msg.content.toString());
                channel.ack(msg); // Acknowledge the message
            }
        });
    } catch (error) {
        console.error("Consume error:", error);
    }
}
```

### 4. Durability
Ensure queues and messages are durable to survive broker restarts.

**Example:**
```javascript
async function createDurableQueue(queue) {
    try {
        await channel.assertQueue(queue, { durable: true });
        console.log(`Durable queue "${queue}" created`);
    } catch (error) {
        console.error("Queue creation error:", error);
    }
}
```

### 5. Prefetch Count
Use prefetch to limit the number of messages sent to a consumer before an acknowledgement is received.

**Example:**
```javascript
async function setPrefetch(count) {
    try {
        await channel.prefetch(count);
        console.log(`Prefetch count set to ${count}`);
    } catch (error) {
        console.error("Prefetch error:", error);
    }
}
```

### 6. Dead Letter Exchanges (DLX)
Use DLX for handling messages that cannot be delivered.

**Example:**
```javascript
async function createDLXQueue(queue, dlx) {
    try {
        await channel.assertQueue(queue, {
            durable: true,
            arguments: {
                'x-dead-letter-exchange': dlx
            }
        });
        console.log(`Queue "${queue}" with DLX "${dlx}" created`);
    } catch (error) {
        console.error("DLX queue creation error:", error);
    }
}
```

## Advanced Features

### 1. Priority Queues
Use priority queues to handle messages with different priorities.

**Example:**
```javascript
async function createPriorityQueue(queue, maxPriority) {
    try {
        await channel.assertQueue(queue, {
            durable: true,
            maxPriority: maxPriority
        });
        console.log(`Priority queue "${queue}" created with max priority ${maxPriority}`);
    } catch (error) {
        console.error("Priority queue creation error:", error);
    }
}
```

### 2. TTL (Time-to-Live)
Set TTL for messages and queues.

**Example:**
```javascript
async function createTTLQueue(queue, ttl) {
    try {
        await channel.assertQueue(queue, {
            durable: true,
            arguments: {
                'x-message-ttl': ttl
            }
        });
        console.log(`TTL queue "${queue}" created with TTL ${ttl} ms`);
    } catch (error) {
        console.error("TTL queue creation error:", error);
    }
}
```

### 3. Delayed Message Plugin
Use this plugin to delay message delivery.

**Installation:**
To use the delayed message plugin, you need to enable the plugin:
```bash
rabbitmq-plugins enable rabbitmq_delayed_message_exchange
```

**Example:**
```javascript
async function sendDelayedMessage(exchange, routingKey, message, delay) {
    try {
        await channel.assertExchange(exchange, 'x-delayed-message', { durable: true, arguments: { 'x-delayed-type': 'direct' } });
        await channel.publish(exchange, routingKey, Buffer.from(message), { headers: { 'x-delay': delay } });
        console.log(`Delayed message sent with delay ${delay} ms`);
    } catch (error) {
        console.error("Delayed message send error:", error);
    }
}
```

### 4. Shovel and Federation
Use these features for data replication and distribution across different RabbitMQ nodes/clusters.

**Shovel Example Configuration:**
Create a configuration file (`rabbitmq.conf`):
```ini
[
  {rabbitmq_shovel,
    [{shovels, [
      {my_shovel,
        [{sources, [{broker, "amqp://source_user:source_password@source_host/source_vhost"}]},
         {destinations, [{broker, "amqp://dest_user:dest_password@dest_host/dest_vhost"}]},
         {queue, <<"source_queue">>}
        ]}
    ]}
  ]}
].
```

Enable the Shovel plugin:
```bash
rabbitmq-plugins enable rabbitmq_shovel
```

**Federation Example Configuration:**
Create a configuration file (`rabbitmq.conf`):
```ini
[
  {rabbitmq_federation, [
    {exchanges, [
      {my_federation,
        [{uri, "amqp://remote_user:remote_password@remote_host/remote_vhost"},
         {exchange, <<"source_exchange">>},
         {type, <<"direct">>}
        ]}
    ]}
  ]}
].
```

Enable the Federation plugin:
```bash
rabbitmq-plugins enable rabbitmq_federation
```

### 5. Monitoring and Management
Use RabbitMQ management plugin for monitoring, and tools like Prometheus and Grafana for advanced monitoring.

**Enabling Management Plugin:**
```bash
rabbitmq-plugins enable rabbitmq_management
```

**Prometheus and Grafana Setup:**
1. Enable the Prometheus plugin:
    ```bash
    rabbitmq-plugins enable rabbitmq_prometheus
    ```

2. Configure Prometheus to scrape RabbitMQ metrics:
    ```yaml
    scrape_configs:
      - job_name: 'rabbitmq'
        static_configs:
          - targets: ['localhost:15692']
    ```

3. Use Grafana to visualize metrics by importing a RabbitMQ dashboard from Grafana's dashboard repository.


### Requirements
1. Erlang - Version 26.x or 25.x - Used to run RabbitMQ as it is written in Erlang language.
2. gnupg - Needed for secure communication.

### Ports Information
* **5672, 5671**: AMQP 0-9-1 and AMQP 1.0 clients.
* **15672, 15671**: Management UI and HTTP API.

#### Other Ports:
* **4369**: epmd, a peer discovery service used by RabbitMQ nodes and CLI tools.
* **5552, 5551**: RabbitMQ Stream protocol clients without and with TLS.
* **6000-6500**: Stream replication.
* **25672**: Inter-node and CLI tools communication (Erlang distribution server port).
* **35672-35682**: CLI tools (Erlang distribution client ports).
* **61613, 61614**: STOMP clients without and with TLS (if the STOMP plugin is enabled).
* **1883, 8883**: MQTT clients without and with TLS (if the MQTT plugin is enabled).
* **15674**: STOMP-over-WebSockets clients (if the Web STOMP plugin is enabled).
* **15675**: MQTT-over-WebSockets clients (if the Web MQTT plugin is enabled).
* **15692, 15691**: Prometheus metrics without and with TLS (if the Prometheus plugin is enabled).
