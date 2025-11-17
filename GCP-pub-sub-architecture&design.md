# Google Cloud Pub/Sub in this event-driven architecture

The ideal design for Google Cloud Pub/Sub in this event-driven architecture centers on robust decoupling, clear topic organization, and efficient multi-tenancy support. Pub/Sub acts as the central nervous system, enabling different microservices to communicate without direct knowledge of each other.

## 1. Topic Design Strategy: Domain-Oriented Topics

We should avoid a single, monolithic topic for everything. Instead, topics should be organized by the domain area or the type of data/event they carry. This improves clarity, allows services to subscribe only to relevant events, and supports multi-tenancy isolation.

We can use a hierarchical naming convention:

**{domain}.{entity}.{event}**

### Key Topic Examples:

**lab.status.update:** Notifications about physical equipment state changes (e.g., lab.status.update event: "liquid handler X moved to 'Running' status for job 42").

**lab.protocol.ready:** Events published by the EMS when a validated protocol is ready for orchestration.

**data.acquired.raw:** Published by the DIS when raw data is safely stored in Cloud Storage.

**data.acquired.processed:** Published by the DAS when data is ready in BigQuery for analytics/modeling.

**modeling.complete:** Published by the In Silico Modeling Service with prediction results.

**inventory.low.stock:** Published by the CLS for procurement alerts.

**system.error:** A shared topic for critical system errors where multiple services might need to listen.

## 2. Multi-Tenancy Implementation

Pub/Sub naturally supports multi-tenancy through subscription filtering, which is highly cost-effective and efficient for a SaaS model.

**- Tenant ID in Payload:** Every message published to Pub/Sub must include the tenant_id as a message attribute (metadata, not in the main payload body).

**- Subscription Filtering:** Each microservice consumer (e.g., the Lab Automation Orchestrator) creates a subscription with a filter.

**Example Filter (for a service dedicated to Tenant A):**
`sql`
`attributes.tenant_id = "tenant-A-uuid"`

**-Benefit:** The Pub/Sub service handles the filtering logic, ensuring that only messages relevant to a specific tenant are pushed to the correct microservice instance. This isolates tenant data efficiently without creating a topic per tenant (which would quickly become unmanageable).

## 3. Subscription Management

Subscriptions manage how messages are delivered to microservices (via push or pull) and handle reliability.

**Push Subscriptions (Recommended for GKE/Cloud Run):** Pub/Sub pushes messages via HTTP POST requests to a specified endpoint of the microservice. This is efficient and manages scalability naturally.

**Dead Letter Queues (DLQs):** Crucial for reliability. Every subscription must have an associated Dead Letter Queue topic. If a microservice fails to process a message a specified number of times (e.g., 5 retries), Pub/Sub automatically forwards that message to the DLQ for later debugging and reprocessing, preventing system bottlenecks.

## 4. Schema Registry (Optional but Recommended)

For an R&D platform handling complex data structures (e.g., protocol definitions, experimental results), using the GCP Schema Registry is highly recommended.

**Purpose:** Enforces data structure (using Avro or Protobuf schemas) on published events. This prevents one service from accidentally breaking another by changing the format of an event payload.

### Summary of Ideal Design:

The ideal design is a **decentralized system of domain-specific topics** where all messages are tagged with a **tenant_id attribute**. Subscriptions use filtering and are protected by **Dead Letter Queues**, ensuring a robust, scalable, multi-tenant event backbone for the entire GCP backend.
