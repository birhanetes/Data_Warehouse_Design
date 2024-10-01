# Data_Warehouse_Design
Project objectives:
•	Design a data warehouse
•	Load data into the data warehouse
•	Create a materialized view
Scenario:
You are a data engineer hired by a solid waste management company. The company collects and recycles solid waste across major cities in the country of Brazil. The company operates hundreds of trucks of different types to collect and transport solid waste. The company would like to create a data warehouse so that it can create reports like:
•	Total waste collected per year per city
•	Total waste collected per month per city
•	Total waste collected per quarter per city
•	Total waste collected per year per truck type
•	Total waste collected per truck type per city
•	Total waste collected per truck type per station per city 
 
After the initial schema design, you were told that due to operational issues, data could not be collected in the format initially planned. This implies that the previous tables (MyDimDate, MyDimWaste, MyDimZone, MyFactTrips) in the Project database and their associated attributes are no longer applicable to the current design. The company has now provided data in CSV files with new tables DimTruck and DimStation as per the new design.
