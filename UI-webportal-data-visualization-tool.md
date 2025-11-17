# UI Data Visualization Tools

The Data Visualization Tools is integrated into the main Web Portal (research board) UI.

While integrating third-party UI tools (like Tableau, Power BI, or even bespoke scientific visualization tools) is possible, keeping the visualization within the unified web application provides a seamless user experience, consistent branding, and simplified authentication/authorization management (single sign-on via Firebase Auth).

## Design Approach: Integrated Visualization

The Analytics Service is designed with REST API endpoints (`/analytics/dashboard, /analytics/query`) specifically to serve structured data to this integrated frontend.

### 1. Implementation Technology

We would leverage powerful JavaScript visualization libraries within our React/Vue/Angular frontend application:

- **Plotly.js or D3.js:** These libraries provide the flexibility to create the highly specialized, interactive plots common in scientific research (e.g., dose-response curves, heat maps, scatter plots, molecular structure viewers).

### 2. Location on the Research Board

The visualization tools would reside in a dedicated section of the Web Portal, likely accessible via a "Dashboard" or "Analysis" tab, separate from the "Experiment Design Studio" tab.

### 3. Key Visualization Features & Layout

The visualization layout should be highly interactive and support multi-tenant data access:

**- Interactive Filters Panel (Left Sidebar):** - Allows researchers to filter data based on experiment ID, date range, compound type, cell line, or specific assay type. These filters translate directly into parameters for the backend API calls to the Analytics Service/BigQuery.

**- Main Visualization Area (Center):**

- Dynamic Plotting: When a user selects specific data or an experiment, the main area renders the appropriate visualization.
- Examples of Visualizations:
  - Heat Maps: For quickly visualizing results across 96/384-well plates.
  - Dose-Response Curves: Standard plots showing biological response to varying compound concentrations.
  - Scatter Plots/Volcano Plots: For comparing different experimental outcomes or identifying outliers.
  - 3D Molecular Viewers: Integration with libraries like 3Dmol.js to visualize the output of in silico molecular docking simulations.
- Data Table View (Bottom Panel):
  - A corresponding interactive data table below the main chart, showing the raw numerical data points that generated the visualization. This allows for data export ([Export CSV] button).

## Data Flow for Visualization

1.  User Interaction: A user applies filters in the UI.
2.  API Call: The Web Frontend makes a secure REST API call to the Analytics Service endpoint (e.g., /analytics/query).
3.  Backend Processing: The Analytics Service runs the optimized query against BigQuery, enforcing the user's tenant_id.
4.  Data Return: BigQuery returns a clean, structured JSON dataset.
5.  Frontend Rendering: The JavaScript library (Plotly/D3) in the user's browser renders the interactive visualization.

By integrating this directly, we maintain a seamless experience where a user can design an experiment in one tab and analyze the results in the next, all within the same platform provided by the SaaS.

[Example for Data Visualization Tool](./UI-Data-Visualization-Tool.html)
