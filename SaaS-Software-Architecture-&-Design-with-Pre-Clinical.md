# SaaS Software Architecture & Design for Pharmaceutical Receasrch and Pre Clinical

Implementing the automated in silico-in vitro platform as a **SaaS (Software as a Service)** offering fundamentally changes the architectural requirements. The primary concern shifts to robust **multi-tenancy**—safely and efficiently serving multiple distinct clients from a single, shared infrastructure.

## 1. Multi-Tenancy Strategy

Multi-tenancy is the most critical design decision for SaaS. There are three common isolation models:

- **Silo Model (Highest Isolation):** Each client gets their own dedicated stack (database, services, etc.).

  - Pros: Maximum security, easy compliance.
  - Cons: Most expensive, highest operational overhead, inefficient resource use.

- **Shared Database, Separate Schema (Good Balance):** The preferred middle ground. Clients share the same physical database server, but each has their own logical database or schema.

  - Pros: Good isolation, better cost efficiency than Silo.

- **Shared Database, Shared Schema (Lowest Isolation):** A "tenant_id" column separates all data in the same tables.

  - Pros: Most cost-effective, highest density.
  - Cons: Risk of "noisy neighbors," complex code required to enforce tenant isolation in every query.

**Recommendation:** The **Shared Database, Separate Schema** model offers the best balance for sensitive R&D data in a SaaS setting.

## 2. Microservices Layer Adjustments

Every microservice needs to become tenant-aware.

**. Tenant Context:** When a user logs in, the Authentication Service (e.g., Auth0/Cognito) provides a JWT that includes a `tenant_id` claim.

**. Enforcement Middleware:** Every microservice must have middleware that extracts the `tenant_id` from the JWT and ensures that every data access request is scoped to that tenant. This prevents "data leakage" between clients.

**. Tenant Provisioning Service:** A new service is required to manage the lifecycle of clients—onboarding new tenants, provisioning their resources (e.g., creating a new database schema), managing subscriptions, and offboarding.

## 3. Data and IoT Layer Adjustments

This is where multi-tenancy gets complex due to physical hardware.

**. Data Lake/Warehouse:** Data needs tagging with the `tenant_id`. For GCP BigQuery, this is simple using partitioning/clustering. For raw storage (S3/GCP Storage), use tenant-specific folder structures to maintain isolation.

**. IoT and Physical Lab Access (Siloed):** This is difficult to share in real-time. The most practical approach for lab automation SaaS is often to provide dedicated IoT gateways and potentially dedicated hardware capacity per client, managed virtually by the shared backend services. The `Lab Automation Orchestrator` must queue jobs based on hardware availability and tenant priority.

**. Billing/Metering Service:** A new service must track resource consumption (e.g., CPU time for _in silico_ modeling, robot time for _in vitro_ testing) for billing purposes.

## 4. User Interfaces and Platform Management

The frontends must manage the multi-tenant context.

**. Multi-Tenant Login:** Users might belong to multiple client organizations; the UI must prompt them to select which "tenant" they wish to operate under after authenticating.

**. Admin Portal:** A super-admin portal is required for the SaaS provider's team to manage all clients, monitor overall system health, and handle billing.

By shifting to a multi-tenant design, the platform can efficiently scale to serve many pharmaceutical clients while keeping their valuable research data isolated and secure.

![Enhanced Software Architecture Pre Clinical](./saas-design-pre-clinical-silico-vitro-platform.svg)