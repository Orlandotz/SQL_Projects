# Economic analysis with SQL

## Project overview
Analyze the United Stated quarterly GDP data to determine what industries and states are the strongest from 2028 to 2022. 

## Data source
https://www.bea.gov. Interactive data to download the industry quarterly real GDP for the United States and each of the states, including District of Columbia. 


## Tools ssed
1. SQLite for data cleaning, preparation and analysis.
2. Tableau to create a Dashboard.

## Exploratory data analysis
1. What are the top 5 industries in the United States and how they growth?
2. What are States that show more recovery after COVID or first quarter of 2020.
3. Describe the quarterly ecomomic changes: what economies increase or decrease and what industries increase or decrease. 

## Code
### Preparing the data
> Alter the table to add the column DetailLevel that specify the GDP breakdown and different levels. 
```SQL
alter table RealGDP 
add COLUMN DetailLevel  TEXT

```
As the industry names were indented I had to count the number of spaces, and based on the values assign a value for the level with a case statement. 
> - If it has no indentation 0 spaces assign 1 for the value All industry total
> - If it has 2 assign 1.1 for Private industries and Government and government enterprises
> - If it has 4 or 6 assign 1.2 to the industries that belong to Private industries and Government and government enterprises
> - If it has 10 assign 1.3 to a lower level industries: Durable goods manufacturing and Nondurable goods manufacturing that belong to manufacturing

```SQL
update RealGDP
set DetailLevel =  length(description) - length(trim( Description))
```
```SQL
update RealGDP
set DetailLevel =
	case 
		when DetailLevel = 0 then replace(detaillevel,0,1)
		when DetailLevel = 2 then replace(detaillevel,2,1.1)
		when DetailLevel = 6 then replace(detaillevel,6,1.2)
		when DetailLevel = 4 then replace(detaillevel,4,1.2)
		when DetailLevel = 10 then replace(detaillevel,10,1.3)
	end
```
> Triming the Description
```SQL
update RealGDP
set Description = trim(description)

```
