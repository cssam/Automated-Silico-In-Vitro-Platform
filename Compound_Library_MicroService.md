# Compound Library MicroService

The **Compound Library Service (CLS)** is the master data management system and operational inventory tracking service for all physical and virtual materials used in research (compounds, cell lines, reagents, etc.). It is crucial for ensuring that the lab can actually execute the experiments designed by the EMS.

The CLS will run within the **GCP GKE/Cloud Run environment**, use **Cloud SQL** for structured inventory data, and interact with the **Cloud Storage** data lake for safety data sheets (SDS) and material specification sheets.

## Compound Library Service: Key Responsibilities

- **Master Data Management:** Stores canonical information about every chemical entity or biological material managed by the client.

- **Inventory & Location Management:** Tracks physical quantities, concentrations, and specific storage locations in real-time.

- **Availability Checks:** Provides real-time information on whether enough material is available for a proposed experiment.

- **Lifecycle Management:** Manages material expiry dates, reordering points, and disposal records.

## Technical Design Details

### 1. API Endpoints (REST & Event-Driven)

The CLS uses synchronous REST APIs for immediate lookups and inventory checks, and Pub/Sub for background updates related to lab operations.

![Compound Library MicroService API Endpoints](./Compound%20-Library%20-MicroService-api-endpoints.png)

### 2. Service Interactions

- **Receives from Experiment Management Service:** Requests for inventory validation before an experiment is approved.

- **Sends to Lab Automation Orchestrator:** Specific storage coordinates for material retrieval (e.g., Fridge_A/Shelf_B/Box_42/Well_H12).

- **Reads/Writes to Cloud SQL:** The primary interaction for all inventory data.

- **Writes to Cloud Pub/Sub:** Alerts the UI or procurement systems when stock is low.

### 3. Data Model (Cloud SQL)

The CLS data model is the most complex within the operational database, requiring detailed tracking of physical locations and logical substances. The tenant_id is critical here for multi-tenancy isolation.

- **Substances Table:** Stores chemical identifiers, IUPAC names, molecular formulas, safety info links, and unique identifiers.

- **Inventory_Batches Table:** Tracks specific physical batches of a substance:

  - `batch_id`
  - `substance_id` (Foreign Key to Substances)
  - `tenant_id`
  - `current_quantity`
  - `units` (mg, mL, vials)
  - `expiry_date`
  - `reorder_point`

- **Locations Table:** Hierarchical tracking of storage:

  - `location_id`
  - `parent_location_id`
  - `location_type` (Room, Fridge, Shelf, Box, Well Plate)

## 4. Workflow Logic (Internal to CLS)

The CLS manages transactional integrity for physical inventory:

1.  **Inventory Check Request:** EMS sends a list of required materials and quantities.

2.  **Inventory_Batch Query:** CLS queries available batches in Cloud SQL, prioritizing those closest to expiry or in the required quantity/concentration.

3.  **inventory.check.result** (REST Response): Returns boolean approval/denial and suggested locations.

4.  **Consume Event (inventory.consume API Call):** The Lab Orchestrator confirms material consumption post-experiment. The CLS immediately performs a database transaction (`UPDATE Inventory_Batches SET current_quantity = current_quantity - X`) to maintain accurate real-time inventory.

5.  **Alerting:** After consumption, it checks if `current_quantity <= reorder_point` and publishes an `inventory.low.stock` event if triggered.
