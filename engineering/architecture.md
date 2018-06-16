# Architecture

The Klira architecture is primarily based on
Event-sourcing. Event-sourcing, while still a somewhat obscure term,
is gaining traction. Event sourcing as a technique is desirable for our domain for a
couple of reasons:

- It ensures that complete record of all interactions are kept. This
is important in order to demonstrate compliance.
- It allows us to correct the interpretation of an event in
the case an error, that way bug-fixes are applied retroactively.
- It allows us to separate different reactions to an event into
  separate services ensuring graceful degradation.

To implement this technique we rely on a couple of key-technologies:

- **Kafka** is a distributed message queue with an important
twist. Unlike traditional message queues like RabbitMQ or ActiveMQ it
doesn't throw away messages after they have been acked. Instead it
stores messages for a fixed duration (or indefinitely). Consumers are
responsible for keeping track of their current message.
- **Protobuf** is our serialization format of choice. It is what we use format our event-messages.
- **CloudEvents** is the wrapper format we use for exposing our events to external systems.

## Languages

The most common languages at Klira are:

- **Kotlin**: Used for back-end systems
- **Java**: For bank-integrations
- **Elm**: Used for most front-end applications
- **JavaScript**: Some front-end applications.
- **Python**: Mostly used for DevOps scripts.

For CI we use CircleCI.

## Libraries

In Kotlin we use:
- [`ktorio/ktor`](https://github.com/ktorio/ktor) - HTTP Server and client
- [`zensum/franz`](https://github.com/zensum/franz) - A light-weight library for building Kafka consumer, maintained by us :)

For Java we use
- [`spring-boot`](https://github.com/spring-projects/spring-boot) for micro-services

For Python we use
- [`fabric/fabric`](https://github.com/fabric/fabric) for automation

## Services

We deploy a lot of micro-services in production but some off our
services are more important than others these are:

- `idempotence-store`: A simple service for making non-idempotent
  things idempotent. For example if we have a task for sending an
  email, we give it a unique identifier. When the email-sending
  service has sent it stores adds it to `idempotence-store` that way
  if the same message is delivered twice to the email-sending service
  the email sending service can check if the ID been consumed already.
- `secret-store`: Privacy legislation requires us to be able to delete
  user identifiable data. Kafka by design doesn't allow for deleting
  or redacting data. secret-store allows substituting a real value for
  pointer for it in `secret-store`. If the data is to be deleted the
  real value is simply deleted while the pointer values are kept.
- `leia`: A service for receiving HTTP-requests and putting them into
  Kafka. It is used for receiving web-hooks with little risk of
  data-loss. The received data is then processed with a Kafka worker.
