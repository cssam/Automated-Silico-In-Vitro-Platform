# Mobile App

The mobile application for the automated in silico-in vitro platform is designed to be a lightweight companion to the comprehensive Web Portal. It shifts the focus from complex design and deep analysis to **remote monitoring, critical alerts, and simple interventions**. Built using a cross-platform framework (like Flutter or React Native) and leveraging the **GCP Firebase** ecosystem, it provides immediacy and accessibility essential for busy researchers and lab managers.

## Core Objectives of the Mobile App

**- Awareness:** Provide instant push notifications for critical system events.

**- Accessibility:** Offer at-a-glance status checks of lab operations from any location.

**- Simplicity:** Enable basic actions like approving or canceling experimental runs.

## Technical Foundation (GCP/Firebase Stack)

**- Authentication:** Uses Firebase Authentication SDK for secure login, receiving the JWT with the tenant_id claim.

**- Notifications:** Leverages Firebase Cloud Messaging (FCM) for reliable push notifications, triggered by backend microservices.

**- Backend Communication:** Communicates with the same secure API Gateway and backend microservices (e.g., EMS, LAO) as the web portal, ensuring consistent data access.

## Ideal UI Layout & Key Features

The mobile app should use a tab-based navigation structure, optimized for quick actions:

### Tab 1: Dashboard (Status at a Glance)

This screen mirrors the most essential features of the Web Portal's Status Dashboard but optimized for a vertical layout.

- **Layout:** A simple list view of active experiments/jobs.

- **Features:**

  - Prioritized List: Shows the most urgent or critical jobs at the top (e.g., failed jobs, completed jobs requiring review).

  - Simplified Status Cards: Each list item shows only the Experiment ID, current status (color-coded badge), progress percentage, and estimated time remaining.

  - Pull-to-Refresh: Standard mobile interaction to manually sync data if needed.

### Tab 2: Notifications/Alerts History

A dedicated inbox for all notifications received via FCM.

- **Layout:** A time-stamped list of all push notifications.
- **Features:**
  - **Filterable:** Filter by type (Error, Complete, Warning).
  - **Deep Linking:** Tapping a notification opens the relevant details screen within the app or even the web portal, providing context.
  - **Example Alerts:**
    - "✅ Job ET-045 Complete! Data ready for analysis."
    - "❌ CRITICAL ERROR: Liquid Handler A reported failure."
    - "🔔 Low Inventory Alert: Compound X below reorder point."

### Tab 3: Actions/Approvals

A screen for simple, actionable items that require researcher input to continue the workflow. This interacts with the **Experiment Management Service API.**

- **Layout:** A list of pending approvals.

- **Features:**
  - **Approval List:** (e.g., "Approve proposed in silico parameters for TS-002?").
  - **[Approve] / [Reject] Buttons:** Send lightweight API calls back to the backend microservices to authorize the next stage of the experiment.
  - This prevents bottlenecks in the DBTL cycle if a researcher is away from their desk.

### Tab 4: Profile & Settings

Standard account management tab handled by the Firebase Auth SDK.

- **Features:** Manage MFA settings, change password, tenant selection (if a user belongs to multiple organizations), and logout.

The mobile app focuses purely on monitoring and intervention, keeping the complex configuration and analysis within the web portal interface, ensuring an efficient and highly utilized application across the user base.

[Mobile Status Dashboard](./UI-mobile-status-dashboard.html)
