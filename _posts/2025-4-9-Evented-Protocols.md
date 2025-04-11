---
layout: post
title:  "MQTT and AMQP: The Protocols Nobody is Taught"
date: 2025-04-9 -600
categories: jekyll update
---
In a world obsessed with REST and GraphQL, event-driven communication protocols like MQTT and AMQP often fly under the radar. But if you've ever needed to build something that reacts to the world in real time—IoT sensors, chat apps, or even backend event streams—you've probably brushed up against the limitations of traditional request-response models.

### Why Evented Protocols Matter

Modern apps are chatty. They want to be notified when things change—not stuck polling a server every few seconds for updates.

Enter evented protocols: instead of asking “Has anything changed yet?”, clients can just say, “Wake me up when something does.”

That’s the entire premise of MQTT and AMQP—two protocols designed for high-throughput, low-latency, and asynchronous messaging.

### MQTT and AMQP: Lightweight and Built for IoT
MQTT (Message Queuing Telemetry Transport) is a barebones, ultra-efficient protocol originally designed for sending small bits of data over unreliable networks. 

At the heart of MQTT is the Publish/Subscribe pattern:
- Publisher sends messages to a Topic

- Subscriber subscribes to one or more Topics

- Broker acts as the middleman — receiving, filtering, and distributing messages

```mermaid
[Sensor] --> publishes to --> "room/temperature"
                           ↓
                     [MQTT Broker]
                           ↓
       [Mobile App] <-- subscribed to -- "room/temperature"
```

AMQP (Advanced Message Queuing Protocol) is a much more feature-rich messaging protocol designed for robust, mission-critical systems. It’s all about guarantees, routing logic, and message integrity—often used where getting the message through matters.
Unlike MQTT’s simpler pub/sub system, AMQP introduces a more flexible and powerful messaging model. It decouples message producers and consumers via a set of well-defined entities:

1. Producer:
Sends messages to an exchange, not directly to a queue.

2. Exchange:
A routing mechanism. Takes messages and determines which queue(s) to route them to, based on the routing key and exchange type.

3. Queue:
Buffers messages for consumption. Consumers read from queues.

4. Consumer:
Subscribes to one or more queues and processes the messages.


```mermaid
         +-----------+
         | Producer  |
         +-----------+
              |
              | Publishes:
              |   - routing key: user.signup
              |   - routing key: user.login
              v
         +----------------+
         |   Exchange     |   (type: direct or topic)
         +----------------+
              |        |
    +---------+        +----------+
    |                              |
    v                              v
+-----------+              +-----------+
|  Queue A  |              |  Queue B  |
| (user.signup)            | (user.login)
+-----------+              +-----------+
     |                          |
     v                          v
+-----------+              +-----------+
|Consumer A |              |Consumer B |
+-----------+              +-----------+
```



### Why Don’t We Talk About Them More?
Honestly? They’re not “cool” in the web dev world. They're not baked into your browser or auto-injected into your favorite JS framework. You don’t see TikTok tutorials on AMQP.

But for infrastructure-level problems, these protocols solve real pain points:
- High-throughput messaging across systems
- Real-time updates to thousands of clients
- Guaranteed message delivery and audit trails

However there is a solution for the web devs! 

### Events for Javascript Developers

When it comes to real-time communication on the web, most developers immediately reach for WebSockets or Server-Sent Events (SSE)—both solid, well-defined options. But if you’ve ever gone hunting through npm, you'll find no shortage of abandoned, half-documented WebSocket libraries (I’d bet there are 30+ deprecated ones just waiting to mess up your package-lock.json in some React project). So instead I want to demonstrate the principle can be implemented under the hood with "long polling" or hanging the request.

In Express, an endpoint handler typically looks like this:
 
```javascript
app.get('/status', (req, res) => {
  // do something
});
```
Now here’s the trick that blew my mind when I first saw it:
> What if you just... save the res variable?

```javascript
const express = require('express');
const app = express();
const PORT = 3000;

app.use(express.json());

// This will store all open client connections
let pendingClients = [];

app.get('/', (req, res) => {
  // Instead of sending a response right away, we save it
  pendingClients.push({ res });
});
```

due to the socket connections staying in RAM unclosed, we now have unlimited power to send data back through.


```javascript
app.post('/push', (req, res) => {
  const message = req.body.message || 'Hello from server!';
  console.log('Pushing message to clients:', message);

  // Respond to all hanging requests
  pendingClients.forEach(({ res }) => {
    res.json({ message });
  });

  // Reset the list
  pendingClients = [];

  res.sendStatus(200);
});
```

Kinda cool ikr.


