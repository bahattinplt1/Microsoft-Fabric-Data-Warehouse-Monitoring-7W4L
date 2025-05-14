# Microsoft Fabric – Data Warehouse Monitoring

This project is a step-by-step lab for monitoring a data warehouse in **Microsoft Fabric** using **DMVs** and **Query Insights**. It follows the official Microsoft Learn module instructions and documents each stage of the lab, including setup, execution, and results.

> ⚠️ A Microsoft Fabric trial (or a licensed workspace with Fabric capacity) is required.

---

## 🚀 Lab Overview

### Step 1: Create a Workspace
- Navigate to [https://app.fabric.microsoft.com](https://app.fabric.microsoft.com).
- Sign in with your Microsoft account.
- In the left navigation menu, select **Workspaces**.
- Create a new workspace (e.g., `FabricMonitorLab`) with licensing mode set to **Trial**, **Premium**, or **Fabric**.
- Confirm the workspace opens as an empty environment.

---

### Step 2: Create a Sample Data Warehouse
- Select **Create** in the left menu.
- Choose **Sample warehouse** under the **Data Warehouse** section.
- Name it `sample-dw`.
- Wait for the sample data (Taxi Ride Analysis) to populate.

---

### Step 3: Explore Dynamic Management Views (DMVs)
1. Open a new SQL query tab from the `sample-dw` page.
2. Run the following DMV queries in sequence:
    - `SELECT * FROM sys.dm_exec_connections;`
    - `SELECT * FROM sys.dm_exec_sessions;`
    - `SELECT * FROM sys.dm_exec_requests;`
    - Join view:
      ```sql
      SELECT connections.connection_id,
             sessions.session_id, sessions.login_name, sessions.login_time,
             requests.command, requests.start_time, requests.total_elapsed_time
      FROM sys.dm_exec_connections AS connections
      INNER JOIN sys.dm_exec_sessions AS sessions
          ON connections.session_id = sessions.session_id
      INNER JOIN sys.dm_exec_requests AS requests
          ON requests.session_id = sessions.session_id
      WHERE requests.status = 'running'
          AND requests.database_id = DB_ID()
      ORDER BY requests.total_elapsed_time DESC;
      ```
3. In a new tab, run a long-running query:
    ```sql
    WHILE 1 = 1
        SELECT * FROM Trip;
    ```
4. Go back to the first tab and re-run the DMV join query to observe the running query.
5. Cancel the infinite query and confirm it is no longer running via DMVs.
6. Close all query tabs.

---

### Step 4: Explore Query Insights
1. Open a new SQL query tab.
2. Run the following queries sequentially:
    - `SELECT * FROM queryinsights.exec_requests_history;`
    - `SELECT * FROM queryinsights.frequently_run_queries;`
    - `SELECT * FROM queryinsights.long_running_queries;`

These views provide historical query execution data and performance insights.

---

## 📁 Screenshots

All screenshots for each step are located in the `screenshots/` folder.

---

## ✅ Conclusion

You have now successfully monitored a Microsoft Fabric data warehouse using dynamic management views and query insights. This setup is useful for real-time and historical performance diagnostics.

For more details, refer to the official Microsoft documentation:
- [Monitor connections, sessions, and requests using DMVs](https://learn.microsoft.com/fabric/data-warehouse/monitor-activity)
- [Query insights in Fabric data warehousing](https://learn.microsoft.com/fabric/data-warehouse/query-insights)
