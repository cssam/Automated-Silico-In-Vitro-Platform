# User Interface (UI) layer

The User Interface (UI) layer serves as the primary interaction point for researchers, lab technicians, and administrators. Since we are implementing a multi-tenant SaaS platform entirely on GCP, the UI must be scalable, secure, and intuitive.


We will use **Firebase Hosting** for the web application and target cross-platform frameworks for a unified mobile experience.

## 1. Web Frontend (Primary Interface)

This is the main "research dashboard" where complex tasks occur.

**. Technology Stack:** A modern frontend framework like **React, Vue.js, or Angular**, utilizing a component library (e.g., Material UI, Ant Design) for consistent styling.

**. Hosting:** Firebase Hosting and Cloud CDN for global delivery with low latency.

**. Key Features:**

- **Experiment Design Studio:** An interactive interface for researchers to define parameters for in silico and in vitro runs, linking compounds from the library and selecting protocols.

- **Data Visualization Tools:** Interactive charts and graphs powered by libraries like D3.js or Plotly, pulling processed data from the Analytics Service API.

- **Real-time Status Dashboard:** A dynamic view that updates using WebSockets or SSE (Server-Sent Events) to show live status of running lab experiments and modeling jobs (consuming status updates via the REST API endpoints of the respective microservices).

- **Admin & Billing Portal:** A section for tenant administrators to manage users (via Firebase Auth API) and view resource consumption/billing metrics.

[Web App](./UI-Web-portal.md)

## 2. Mobile App (Monitoring & Alerts)

The mobile app focuses on essential monitoring, notifications, and simple interactions.

**- Technology Stack:** A cross-platform framework like React Native or Flutter to maintain a single codebase for both iOS and Android, deployed via respective app stores.

- Key Features:

  - **Push Notifications:** Utilizing **Firebase Cloud Messaging (FCM)** to deliver critical alerts instantly (e.g., "Experiment complete," "Hardware error detected," "Low stock alert"). These notifications are triggered by the system.error, lab.status.update, and inventory.low.stock Pub/Sub events routed through a notification microservice.

  - **Remote Monitoring:** A simplified view of the status dashboard showing high-level information about ongoing jobs.

  - **Simple Approvals:** A quick "Approve Run" button that sends an API call back to the Experiment Management Service.

## 3. Authentication & Security in the UI

Security is paramount in a SaaS environment handling sensitive R&D data.

- **Firebase Authentication SDK:** The UI uses the Firebase Auth client SDK to handle user login. This securely manages the authentication flow and receives a JWT (JSON Web Token) upon successful login.

- **JWT Management:** The UI securely stores this JWT and automatically attaches it to every subsequent API request made to the REST endpoints of the GKE microservices (e.g., calling /experiments/design requires a valid JWT).

- **Tenant Context:** As discussed, the JWT will contain the tenant_id claim, which backend services will use to enforce data isolation.

- **API Gateway:** All UI traffic should pass through a GCP API Gateway (like API Gateway or Apigee X) which validates the JWT before forwarding the request to the internal microservices, adding a layer of protection and centralizing security policy enforcement.

[Mobile App](./UI-Mobile-app.md)