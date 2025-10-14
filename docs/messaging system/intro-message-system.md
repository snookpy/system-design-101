

# Messaging System
Transfer data among services, applications, processes, or servers. Such a system helps decouple different parts of a distributed system, providing **asynchronous** way of communication messages between Sender and Receiver. So all senders (or producers) and receivers (or consumers) focus on the data/message without worrying about how the mechanism used to share the data.

```mermaid
flowchart LR
    A["Sender"] --> B["Messaging 
        System"]
    B --> C["Receiver"]
    style B fill:#FFE0B2
```

## Queue
Messages are stored sequentially in a queue. Producers push messages to the rear of the queue, and consumers pull messages from the front of the queue (FIFO - First In, First Out).

```mermaid
flowchart LR
    subgraph messaging_queue["Messaging Queue"]
        m3["m3"]
        m2["m2"]
        m1["m1"]
        m0["m0"]
    end
  P1["Producer 1"] -->messaging_queue --> C1["Consumer 1"]
  P2["Producer 2"] -->messaging_queue --> C2["Consumer 2"]
```

A message can be consumed by only one consumer. Once a consumer grabs a message from the queue, it is removed from the queue which the next consumer will get the next message. This limits the system as multiple consumers cannot read the same message.

```mermaid
flowchart LR
    subgraph messaging_queue["Messaging Queue"]
        m6["m6"]
        m5["m5"]
        m4["m4"]
        m3["m3"]
    end
  P1["Producer 1"] --> m7 --> messaging_queue
  P2["Producer 2"] --> m8 --> messaging_queue
  messaging_queue --> m0 --> C1["Consumer 1"]
  messaging_queue --> m1 --> C2["Consumer 2"]
  messaging_queue --> m2 --> C3["Consumer 3"]
```

## Publish-subscribe messaging system
In the pub-sub model, messages are divided into topics.
A publisher (or a producer) sends messages to a specific topic.Subscribers (or consumers) subscribe to a topic to receive messages from the publisher. Unlike the queue system, multiple subscribers can receive the same topic.
The messaging system that stores and maintains the messages is commonly known as the message **broker**. This loose coupling enables subscribers and publishers to read and write messages at different rates.

```mermaid
flowchart LR
    subgraph pub_sub["Pub Sub (**Broker**) **Topic: Sales**"]
        m5["m5"]
        m4["m4"]
        m3["m3"]
        m2["m2"]
    end
  P1["Publisher 1"] --> m7 --> pub_sub
  P2["Publisher 2"] --> m8 --> pub_sub
  pub_sub --> m11["m1"] --> S1["Subscriber 1"]
  pub_sub --> m12["m1"] --> S2["Subscriber 2"]
  pub_sub --> m13["m1"] --> S3["Subscriber 3"]
```

## Message Patterns
- **Point-to-point**: A message is sent from one producer to one consumer. The message is consumed by only one consumer.
- **Publish-subscribe**: The publish-subscribe pattern delivers each message to all interested subscribers like **One-to-Many**.
- **Fan-out/Fan-in (Scatter-Gather)**: A message is sent to multiple consumers. Each consumer receives a copy of the message. And then the responses are aggregated and sent back to the producer.
- **Request-Reply**: A message is sent from a producer to a consumer, and the consumer sends a response back to the producer. This pattern is commonly used in synchronous communication but implements through messaging. Tools Like gRPC are direct request-response(not messaging), but in messaging systems can implement this pattern explicitly.
- **Dead Letter Queue**: A dead-letter queue (DLQ) is a specialized queue that stores messages that cannot be processed successfully by the primary messaging system. When a message fails to be delivered or processed after a certain number of attempts, it is moved to the DLQ for further analysis and troubleshooting. This helps prevent message loss and allows for better error handling in messaging systems.