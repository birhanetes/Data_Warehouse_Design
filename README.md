# Data Warehouse Design
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
      
 #Solution:
      
      Design a Star Schema warehouse by:
      1. Identifying the columns for the various dimensions and fact tables in the schema. See Pictures atached from  1 - 4
      2. Creating schema for data warehouse. See Pictures atached from  5 - 8
      3. Loading data into the data warehouse. See Pictures atached from  9 - 12
      4. Writing aggregation queries and create materialized view. See Pictures atached from  13 - 16
      

 # Create a data warehouse for a solid waste management company
 
create database FinalProject;

use FinalProject;

        #Create a date dimension table 
        
        Create table MyDimDate (
        
		date_id int primary key,
  
		year int Not Null,
  
		month int Not Null,
  
		month_name varchar(25) Not Null,
  
		quarteer int,
  
		quarter_name Varchar(25) Not Null,
  
		day int Not Null,
  
		weekday int Not Null,
  
		weekday_name Varchar(25) Not Null );
  

#Create a truck dimension table 

        create table DimTruck (

		Truck_id int primary key,
  
		Truck_type varchar(50) Not Null);


#Create a station dimension table 

        create table DimStation (
        
		Station_id int primary key,
  
		City Varchar(255) Not Null);
  

#Create a fact table 

        create table MyFactTrips (
        
		Trip_Id int primary key,
  
                date_id int Not Null,
        
		station_id int Not Null,
  
		Truck_id int Not Null,
  
		waste_collected_in_tons Decimal (10,2) Not Null,
  
        FOREIGN KEY (date_id) REFERENCES MyDimDate(Date_id),
        
		FOREIGN KEY (Station_id) REFERENCES DimStation(Station_id),
  
		FOREIGN KEY (Truck_id) REFERENCES DimTruck(Truck_id) );
  

 
#Create a grouping sets query

            SELECT
            
                f.station_id,
                
                t.Truck_type,
                
                SUM(f.waste_collected_in_tons) AS TotalWaste
                
            FROM MyFactTrips f  
            INNER JOIN DimTruck t 
            ON f.Truck_id = t.Truck_id   
            GROUP BY 
            
        		GROUPING  sets(
          
        			(f.station_id, t.Truck_type),
           
        			(f.station_id),
           
        			(t.Truck_type),
           
        			() 
           
            )
        
        
            ORDER BY
            
                         f.station_id,
                         
                         t.Truck_type;
                         


#Create a rollup query    


            SELECT
          
                        d.year,
                        s.city,
                        s.station_id,
                        SUM(f.waste_collected_in_tons) AS TotalWaste
                FROM MyFactTrips f
                inner JOIN DimDate d 
                ON f.Dateid = d.Dateid
                inner JOIN DimStation s 
                ON f.station_id = s.station_id
                GROUP BY rollup (d.year, s.city, s.station_id)
                order by
                	 d.year desc, 
                         s.city,  
                         s.station_id;   
    

#Create a cube query


             select

        	           d.year,
         
                            s.city,
                            
                            s.station_id,
                            
                            avg(f.waste_collected_in_tons) as Average_waste
    
                FROM MyFactTrips f
                
                inner JOIN DimDate d 
                
                ON f.Dateid = d.Dateid
                
                inner JOIN DimStation s 
                
                ON f.station_id = s.station_id
                
                GROUP BY CUBE (d.Year, s.City,  s.station_id)
                
                ORDER BY
        
                                    d.Year DESC,
                                    
                                    s.City,
                                    
                                    s.station_id;
    
    
#Create a materialized view


        create materialized veiw  max_waste_status as 
  
            select
        		s.city,
          
                        f.station_id,
                        
                        t.Truck_type,
                
                        max(f.waste_collected_in_tons) as MaxWaste
                
                from MyFactTrips f
                
                inner JOIN DimStation s 
                
                ON f.station_id = s.station_id
                
                INNER JOIN DimTruck t 
                
                ON f.Truck_id = t.Truck_id
                
                group by 
        
                	s.city,
                 
                	f.station_id,
                 
                	t.Truck_type
         
                 with data;  

      
