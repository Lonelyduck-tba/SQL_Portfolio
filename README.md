# Investigation-on-an-NESO-Dataset
In this project I aimed to investigate the historic electricity demand, interconnector, wind and solar outturn data for 2023. This dataset was sourced from the Nation Energy System Operator which is a licensed electricity system operator balancing supply and demand; residing within Great Britain.

**Obtaining the Dataset**

The first thing I had to do was sourcing the dataset, I used one provided by a Kaggle user (https://www.kaggle.com/datasets/albertovidalrod/electricity-consumption-uk-20092022/data) which was sourced from the NESO. They gathered information on the electricity demand in Great Britain from 2009-2024. The table is updated twice an hour, which means 48 entries per day. The dataset was formatted as a CSV and I used Pgadmin4 to import it into the Postgresql database. 

**Importing the Dataset**

Using PgAdmin4 I created a database named "energy_statistics" and created a table to import the CSV file. I learned through this that I had to name, order, and format the datatype of each column to match that of the original dataset. Apart from the SETTLEMENT_PERIOD column (date) all other columns were integers and I had no other problems importing the CSV. Although for larger tables with a more columns it might be better to use the inbuilt sql query conversion in Notepad ++.

**Writing the Query**

Inorder to write my query I had to know what I was trying investigate. Looking at  "EMBEDDED_SOLAR_GENERATION" and "EMBEDDED_WIND_GENERATION" I learned that they are an estimate of the GB solar and wind generation from pv panels and wind farms. Their effect is to suppress the electricity demand during periods of high sun and wind. Measured in MW. So I decided to investigate the seasonality of solar and wind generation using the "SETTLEMENT_PERIOD" column as my timeline and day as the smallest granularity. 

So my query would have to select the sum of the "EMBEDDED_SOLAR_GENERATION" and "EMBEDDED_WIND_GENERATION" and "SETTLEMENT_PERIOD", I also decided to select the sum of the "EMBEDDED_SOLAR_CAPACITY" and "EMBEDDED_WIND_CAPACITY" as would like to see how close generation was to capacity. Grouping this query by the "SETTLEMENT_DATE" meant that all 17520 entries would be cut down to only 365 entries as "SETTLEMENT_DATE" had 48 duplicate entries for one day and I really only wanted to find energy generation per-day. I also ordered it by date. 

**Looking Deeper**

I suspected that the solar generation would show as highly volatile if visualised and wanted to add a moving avereage to fully illustrate that point. In order for that to happen several things need to be put into place. First a window function that would calculate the average of 10 prior data points for every row. Window functions , as they are in the SELECT statement, use the non-aggregated dataset for its calculations and maintains the dataset's granularity, meaning that in order for the query to run I would have to add all the columns used in my window function in my GROUP BY statement because I would be trying to compare 17520 entries to 365 and thus create an error. This would create a moving average but would also push me back to square one with my 17520 entries. To remedy this I would create a CTE(Common Table Expression) to create an aggregated dataset to which the window function would be applied to. This resulted in both a moving average and 365 entries.

**Exporting into PowerBI***
To export the query into PowerBI I had to create a table from my query I couldn't connect properly before because I tried connecting to a sql server not a postgresql server.
Now I just need to create a table from my query so that it shows up when I import the data into powerbi

