# Web Portal

We will be discussing the three primary operational interfaces of the Web Portal that facilitate the **Design-Build-Test-Learn (DBTL) cycle:**

**1. Experiment Design Studio (EDS):** For initiating new experiments (Design phase).
[Experiment Design Studio](./UI-webportal-Experimental-Design-Studio.md)

**2. Real-time Status Dashboard:** For monitoring active operations (Build/Test phases).

[Realtime Status](./UI-web-portal-Real-time-Status-Dashboard.md)

**3. Data Visualization Tools:** For analyzing results and insights (Learn phase).

[Data Visualization Tool](./UI-webportal-data-visualization-tool.md)

The remaining UIs required for a complete, production-ready SaaS platform are the foundational and administrative UIs. These UIs focus on user management, system administration, billing, and intellectual property (IP) management.

The following UIs are left to discuss for the Web Portal:

## 1. Authentication and Onboarding UI

This is the gateway to the entire platform, handled by Firebase Authentication on the backend.

**- Login/Logout Page:** Standard interface for authentication using username/password, handling MFA (Multi-Factor Authentication), and potentially social/enterprise logins (SAML).

**- Registration/Sign-up Page:** For new users to create accounts and for new tenants (companies) to register for the SaaS.

**- Password Reset & Account Management:** Standard self-service screens for managing user profiles and security settin

## 2. Administration UIs (SaaS & Tenant Level)

These UIs manage the multi-tenant SaaS environment.

**SaaS Provider Admin Portal (Super-Admin):** A dedicated UI for the platform administrators to manage all tenants, monitor global system health, view audit logs, and provision/de-provision client accounts.

**Tenant Admin Portal (Client-Level):** An interface for an organization's specific admin user to manage their team members, assign roles (e.g., Researcher, Lab Tech, Viewer), and manage their tenant-specific settings within our system.

## 3. Inventory and Compound Library Management UI

While the EDS uses the Compound Library Service for availability checks, there needs to be a dedicated UI to manage the library master data itself.

**- Material Entry/Edit Screen:** A complex form for inputting new chemical structures, cell line details, safety data sheets (SDS links), and setting reorder points.

**- Inventory Reconciliation:** A UI for lab technicians to perform physical stock checks, update quantities, manage expiry dates, and handle physical locations.

## 4. Billing and Usage Dashboard

This is crucial for the SaaS business model, tying into the `Billing/Metering Service`.

- Usage Metrics: Visualizations showing how many _in silico_ compute hours or _in vitro_ robot run-time hours a tenant has consumed in the current billing cycle.

- Invoices & Payment Management: A portal to view invoices, manage payment methods, and upgrade subscription tiers.
