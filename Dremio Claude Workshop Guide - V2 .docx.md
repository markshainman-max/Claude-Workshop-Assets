**Claude Workshop**

**Prerequisite** \- 

1. Attendees will have Claude code desktop version downloaded in their environment.  
2. Python 3.11+ is installed.     
   Can check python version by running  Mac \-  “python3 \--version” and in PC “python \--version”

Setup \-

1. In your terminal, run “pip install dremio-cli”   for windows for mac run “pip3 install dremio-cli”  
2. Open Claude Code   
3. Go to [https://www.dremio.com/get-started/](https://www.dremio.com/get-started/) and put in your e-mail address and then check your e-mail. You will see the link to get started with Dremio Cloud.   \- it takes a little under 3 minutes to set up.

Test

1. Go to Dremio Cloud and show a quick walkthrough of the features.  
2.  Now type in Claude Code “help me connect the Dremio Cloud account” \-  I will also show them where their PAT is located.   
3.  Claude should tell them to run the following in their terminal “ dremio profile create default \--type cloud \--base-url https://api.dremio.cloud \--project-id (project ID Here) \--auth-type pat \--token YOUR\_PAT\_HERE  
4.   
5. “Run a test query against Dremio to confirm my connection is working   \- it should run Select 1 as hello\_dremio  and return a result 

Main  \- create tables and views.

1. Create Folders  \-Tell Claude the following: “Create a space called StrategicMaterialsDB with Bronze, Silver, Gold folders.”  
2. Create Tables and Load Data \- Next tell Claude “I have three CSV files in my workshop folder — mineral\_sources.csv, geopolitical\_risk\_index.csv, and supplier\_audit\_findings.csv. Create Iceberg tables for each in the Bronze folder and load all the data.”  
3. Create Silver Views \- “Create a Silver view called ShipmentRisk that joins mineral sources to the risk index and calculates weighted risk exposure for each shipment”  
4. Create a view in StrategicMaterialsDB.silver called supplier\_audit\_mineral\_sources that combines data from the table SupplierAuditFindings and MineralSources  
5. Create Gold Views \-” Create a Gold view called VulnerabilityDashboard that shows each mineral type's total volume, average risk score, high-risk volume, and percentage dependence on high-risk countries”  
6. Use Dremio to create Wiki information for the ShipmentRsik, VulnerabilityDashboard and supplier\_audit\_mineral\_sources. Views. 

Ask Business Questions

"Whi."

"Which suppliers should we be most worried about and why?"

"If China restricted exports tomorrow, what percentage of our Neodymium supply would be at risk?

Lineage \-   
"Show me the lineage for the Gold VulnerabilityDashboard view,  what are all its upstream dependencies?

Reflections \-

1. Show Customer 360 data set \- Now lets look at the customer 360 data set that is the sample data set that comes pre-configured on Dremio Cloud.  \-  177Million orders and 4.8 million customers  
2. Join data without the reflection \- “ in the customer36 data set show me total orders and total revenue by customer joining the orders and custoemrs table and time it”  
3.  Create a Reflection  \-“Create an aggregation reflection on the Customer360 orders table to pre-materialize it.”  
4. Check if the reflection is built “Check the status of the reflection,  is it ready yet?"  
5. Run query with the reflection “Run the same membership tier query again and time it.”  
6. Ask \- “Why was the second query so much faster? What did Dremio do differently?”  
7. Explain Autonomous reflections \- 

   
