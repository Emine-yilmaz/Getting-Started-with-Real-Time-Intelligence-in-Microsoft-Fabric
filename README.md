# Getting Started with Real-Time Intelligence in Microsoft Fabric

This guide walks you through the steps to create a real-time analytical solution using Microsoft Fabric's Real-Time Intelligence capabilities. You'll learn how to ingest, analyze, and visualize a live stream of stock market data.

**Note:** You need a Microsoft Fabric tenant to complete this exercise.

## Prerequisites

* A Microsoft Fabric tenant and access to it.
* Your Microsoft Fabric credentials.

## Steps

### 1. Create a Workspace

Before working with data in Fabric, you need a workspace with the Fabric capacity enabled.

1.  Navigate to the [Microsoft Fabric home page](https://app.fabric.microsoft.com/home?experience=fabric) in a web browser and sign in with your Fabric credentials.
2.  In the left-hand menu bar, select **Workspaces** (the icon looks like 🗇).
3.  Create a **New workspace**.
4.  Give your workspace a name of your choice.
5.  Select a licensing mode that includes Fabric capacity (Trial, Premium, or Fabric).
6.  Once created, your new workspace should be empty.

### 2. Create an Eventstream

Next, you'll ingest real-time data from a streaming source using the Fabric Real-Time Hub.

1.  In the left-hand menu bar, select the **Real-Time hub**.
2.  In the Real-Time Hub, under the **Connect to** section, choose **Data sources**.
3.  Find the **Stock market** sample data source and click **Connect**.
4.  In the **Connect** wizard:
    * Name the source: `stock`
    * Edit the default eventstream name to: `stock-data` (The associated stream will be automatically named `stock-data-stream`).
5.  Click **Next** and wait for the source and eventstream to be created.
6.  Select **Open eventstream**. You should see the `stock` source and the `stock-data-stream` on the design canvas.

### 3. Create an Eventhouse

Now, you'll create an eventhouse to store the captured real-time data in a table.

1.  On the left-hand menu bar, select **Create**.
2.  In the **New** page, under the **Real-Time Inteligence** section, select **Eventhouse**.
3.  Give your eventhouse a unique name.
4.  Close any initial tips or prompts until you see your empty eventhouse.
5.  In the left pane, note the KQL database with the same name as your eventhouse.
6.  Select the database and observe the associated **queryset** containing sample KQL queries.
7.  In the main page of your KQL database, click **Get data**.
8.  For the data source, select **Eventstream** > **Existing eventstream**.
9.  In the **Select or create a destination table** pane, create a **New table** named `stock`.
10. In the **Configure the data source** pane:
    * Select your workspace.
    * Select the `stock-data` eventstream.
    * Name the connection `stock-table`.
11. Complete the steps to inspect the data and finish the configuration using the **Next** button.
12. Close the configuration window. You should now see the `stock` table in your eventhouse.
13. To verify the connection, navigate to the **Real-Time hub** in the left-hand menu and go to the **My data streams** page.
14. In the **...** menu for the `stock-data-stream`, select **Open eventstream**. You should now see the eventhouse as a destination.
    * **Tip:** Select the destination on the canvas and click **Refresh** if the data preview isn't immediately visible.

### 4. Query the Captured Data

With data flowing into your `stock` table, you can now query it.

1.  In the left-hand menu, select your eventhouse database.
2.  Select the **queryset** for your database.
3.  Modify the first example query in the query pane to:
    ```kql
    stock
    | take 100
    ```
4.  Select the query code and **Run** it to view the first 100 rows of data.
5.  Modify the query again to retrieve the average price for each stock symbol in the last 5 minutes:
    ```kql
    stock
    | where ["time"] > ago(5m)
    | summarize avgPrice = avg(todecimal(bidPrice)) by symbol
    | project symbol, avgPrice
    ```
6.  Highlight the modified query and **Run** it to see the results.
7.  Wait a few seconds and run the query again to observe the changing average prices as new data arrives.

### 5. Create a Real-Time Dashboard

Visualize the real-time data using a dashboard.

1.  In the query editor, select the KQL query you used to retrieve the average stock prices for the last five minutes.
2.  On the toolbar, click **Pin to dashboard**.
3.  Choose **New dashboard** and configure the following:
    * **Dashboard name:** `Stock Dashboard`
    * **Tile name:** `Average Prices`
4.  Click **Create and open**. Your new dashboard should appear.
5.  At the top of the dashboard, switch from **Viewing** mode to **Editing** mode.
6.  Select the **Edit** (pencil) icon for the **Average Prices** tile.
7.  In the **Visual formatting** pane, change the **Visual** from **Table** to **Column chart**.
8.  At the top of the dashboard, click **Apply changes** to view your modified real-time visualization.

### 6. Create an Alert

Use Activator to trigger actions based on real-time events. Let's create an alert for significant price increases.

1.  In your **Stock Dashboard**, on the toolbar, select **Set alert**.
2.  In the **Set alert** pane, configure the following settings:
    * **Run query every:** `5 minutes`
    * **Check:** `On each event grouped by`
    * **Grouping field:** `symbol`
    * **When:** `avgPrice`
    * **Condition:** `Increases by`
    * **Value:** `100`
    * **Action:** `Send me an email`
    * **Save location:**
        * **Workspace:** `Your workspace`
        * **Item:** `Create a new item`
        * **New item name:** A unique name of your choice (e.g., `PriceIncreaseAlert`)
3.  Click **Create** and wait for the alert to be saved. Close the confirmation pane.
4.  In the left-hand menu, navigate to your workspace.
5.  You should see the items you've created, including the activator for your alert.
6.  Open the activator. Under the `avgPrice` node, select the unique identifier for your alert.
7.  Go to the **History** tab. If the average stock price increases by more than 100, you'll receive an email, and the alert will be recorded here.

### 7. Clean Up Resources

To avoid unnecessary costs, it's good practice to clean up the resources you created.

1.  Navigate to your workspace in Microsoft Fabric.
2.  For each item created (Eventstream, Eventhouse, Dashboard, Alert/Activator):
    * Hover over the item.
    * Click the **...** (ellipsis) menu.
    * Select **Delete**.
    * Confirm the deletion.
3.  Finally, you can also delete the workspace itself if you no longer need it.

Congratulations! You've successfully created a basic real-time intelligence solution in Microsoft Fabric, ingesting, analyzing, and visualizing real-time stock market data, and even setting up an alert. This exercise provides a foundation for building more complex real-time analytical solutions.
