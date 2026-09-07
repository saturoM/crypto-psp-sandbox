# ADR 0001 · Use RabbitMQ as the message broker

## Status

Accepted (theory phase)

## Context

Need async webhook dispatch and domain events. Options: RabbitMQ, Redis Streams, NATS, in-process queue.

## Decision

**RabbitMQ** via Docker Compose.

## Consequences

+ Familiar payments-style async story for interviews  
+ DLQ / retry patterns easy to demo  
− Slightly heavier than Redis for a tiny demo — acceptable for portfolio clarity
