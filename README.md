# Economic analysis with SQL

## Project overview
Analyze the United Stated quarterly GDP data to determine what industries and states are the strongest from 2028 to 2022. 

## Data source
https://www.bea.gov. Interactive data to download the industry quarterly real GDP for the United States and each of the states, including District of Columbia. 


## Tools Used
1. SQLite for data cleaning, preparation and analysis.
2. Tableau to create a Dashboard.

## Exploratory data analysis
1. What are the top 5 industries in the United States and how they growth?
2. What are States that show more recovery after COVID or first quarter of 2020.
3. Describe the quarterly ecomomic changes: what economies increase or decrease and what industries increase or decrease. 

## Code
### Preparing the data
 Alter the table to add the column DetailLevel that specify the GDP breakdown and different levels. 
 
```SQL
alter table RealGDP 
add COLUMN DetailLevel  TEXT
```
<p>
As the industry names were indented I had to count the number of spaces, and based on the values, with a case statement I assigned a value to determine the different GDP livels. 
 - If it has no indentation 0 spaces assign 1 for the value All industry total
 - If it has 2 assign 1.1 for Private industries and Government and government enterprises
 - If it has 4 or 6 assign 1.2 to the industries that belong to Private industries and Government and government enterprises
 - If it has 10 assign 1.3 to a lower level industries: Durable goods manufacturing and Nondurable goods manufacturing that belong to manufacturing
</p>

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
Triming the Description
```SQL
update RealGDP
set Description = trim(description)
```

From the main table I created a table for each quarter that contains the values description, detailedlevel and the GDP Value 

```SQL
create table GDP_2018_Q1 as
select Description, DetailLevel, "2018:Q1"
from RealGDP
```
The next steps were to add a column for year and quarter, assign a value for year and quarter, and remane the column for the GDP value 

```SQL
alter table GDP_2018_Q1
ADD COLUMN year  INT;

alter table GDP_2018_Q1
ADD COLUMN Quarter  INT;

alter table  GDP_2018_Q1
RENAME COLUMN "2018:Q1" to RealGDPValue;

UPDATE GDP_2018_Q2
set year = 2018;

UPDATE GDP_2018_Q2
set Quater = 1

```

Consolidated table 

```SQL
create table QuarterlyRealGDPUSA as
SELECT * FROM GDP_2020_Q2
union all
SELECT * FROM GDP_2020_Q3
union all
SELECT * FROM GDP_2020_Q4

```

### Result of the SQL table:

| Area    | Year | Quarter | Description                                    | Detail Level | Real GDP Value |
|---------|------|---------|------------------------------------------------|--------------|----------------|
| Alabama | 2018 | 1       | All industry total                             | 1            | 220305.3       |
| Alabama | 2018 | 1       | Private industries                             | 1.1          | 184529.1       |
| Alabama | 2018 | 1       | Agriculture, forestry, fishing and hunting     | 1.2          | 2621.8         |
| Alabama | 2018 | 1       | Mining, quarrying, and oil and gas extraction  | 1.2          | 1906.8         |
| Alabama | 2018 | 1       | Utilities                                      | 1.2          | 6035.5         |
| Alabama | 2018 | 1       | Construction                                   | 1.2          | 8977.8         |


## Data analysis 
### What are the top 10 biggest industries after the second quarter of 2020?
```SQL

with cterealgdp as (
SELECT 
	year, 
	Quarter,
	area, 
	Description, 
	RealGDPValue as Rgdpvalue
from QuarterlyRealGDPUSA
)
select 
	t1. Description, 
	round(Cast(t1.Rgdpvalue as real)/Cast(t2.Rgdpvalue as real),4) as GDP_Percentage
 from cterealgdp t1

left join cterealgdp t2 on t2.area = t1.area AND t2.year = t1.year and t2.Quarter = t1.Quarter

where 
 	t1.Description <> 'All industry total' 
and  	t1.Description <> 'Private industries' 
and  	t1.Description <> 'Durable goods manufacturing' 
and  	t1.Description  <> 'Nondurable goods manufacturing' 
and  	t1.Description <> 'Government and government enterprises'
and  	t1.Rgdpvalue <> "(D)"
and 	t2.Description =  'All industry total' 
and 	t1.area = 'United States'
and 	(t1.year = 2020 and t1.Quarter = 3)

order by t1.year, t1.Area, t1.Quarter,  t1.Rgdpvalue DESC
limit 10
```

### Result 

| Description                                        | GDP Percentage |
|----------------------------------------------------|----------------|
| Real estate and rental and leasing                 | 13.15%         |
| Manufacturing                                      | 10.62%         |
| Professional, scientific, and technical services   | 8.13%          |
| State and local                                    | 8.03%          |
| Health care and social assistance                  | 7.71%          |
| Finance and insurance                              | 7.54%          |
| Retail trade                                       | 6.26%          |
| Information                                        | 6.09%          |
| Wholesale trade                                    | 6.01%          |
| Construction                                       | 4.21%          |
### What are the top 10 industries that show mor growth after the second quarter of 2020?

```SQL
WITH quarterlyGDP as (
select 
	Area,
	year,
	Quarter,
	Description,
	RealGDPValue,
	row_number()over(partition by Area, Description order by year, Quarter) as rn
	 from QuarterlyRealGDPUSA
 
 )
 select DISTINCT

	t1.Description,
	round(coalesce((cast(t1.RealGDPValue as real) - cast(t2.RealGDPValue as real))*1.0/t2.RealGDPValue,0),2) as QuarterlyGrowth
	
 from quarterlyGDP t1
 left join quarterlyGDP t2 on  t1. area = t2.area and t1.Description = t2.Description and t1.rn = t2.rn + 1 
 
	WHERE t1.RealGDPValue <> "(D)" and t1.Area = 'United States' and (t1.year = 2020 and t1.Quarter = 3)
	
	order by  QuarterlyGrowth DESC 
	
	limit 10;
 
```
| Description                                                                  | Quarterly Growth (%) |
|------------------------------------------------------------------------------|-----------------------|
| Accommodation and food services                                              | 46.00%                |
| Arts, entertainment, and recreation                                          | 44.00%                |
| Other services (except government and government enterprises)                | 17.00%                |
| Health care and social assistance                                            | 16.00%                |
| Agriculture, forestry, fishing and hunting                                   | 14.00%                |
| Durable goods manufacturing                                                  | 14.00%                |
| Manufacturing                                                                | 12.00%                |
| Transportation and warehousing                                               | 12.00%                |
| Retail trade                                                                 | 11.00%                |
| Administrative and support and waste management and remediation services     | 10.00%                |


### What are the top 10 economies in the United States and is their GDP percentage of the total US GDP? 

```SQL
With State_GDP_Percentage as (
select 
	year, 
	Quarter, 
	Area, 
	Description,
	RealGDPValue 
	from QuarterlyRealGDPUSA
	where Description =  'All industry total'  and RealGDPValue <> '(D)'
)
Select 
	t1.year,
	t1.Quarter,
	t1.Area,
	t1.RealGDPValue,
	round(cast(t1.RealGDPValue as real)/Cast(t2.RealGDPValue as real),6) As GDPState_Percentage,
	rank() over (PARTITION by t1. Quarter, t1.year order by t1.RealGDPValue DESC) as State_Rank
 
from State_GDP_Percentage t1

left join State_GDP_Percentage t2 on t2.year = t1.year and t2.Quarter = t1.Quarter

	Where t1.Area <> 'United States'   and t2.Area = 'United States' and (t1.year = 2020 and t1.Quarter = 3)
	order by State_Rank ASC
	limit 10

```

| Year | Quarter | Area        | Real GDP Value | GDPState Percentage | State Rank |
|------|---------|-------------|----------------|---------------------|------------|
| 2020 | 3       | California  | 2,962,454.8    | 14.44%              | 1          |
| 2020 | 3       | Texas       | 1,795,027.9    | 8.75%               | 2          |
| 2020 | 3       | New York    | 1,659,831.8    | 8.09%               | 3          |
| 2020 | 3       | Florida     | 1,089,771.4    | 5.31%               | 4          |
| 2020 | 3       | Illinois    | 818,584.2      | 3.99%               | 5          |
| 2020 | 3       | Pennsylvania| 744,977.7      | 3.63%               | 6          |
| 2020 | 3       | Ohio        | 665,995.6      | 3.25%               | 7          |
| 2020 | 3       | Georgia     | 611,130.1      | 2.98%               | 8          |
| 2020 | 3       | Washington  | 605,303.3      | 2.95%               | 9          |
| 2020 | 3       | New Jersey  | 603,345.8      | 2.94%               | 10         |

### What are the top 10 States that show more growth after the second quarter of 2020?

```SQL
WITH quarterlyGDP as (
select 
	Area,
	year,
	Quarter,
	Description,
	RealGDPValue,
	row_number()over(partition by Area, Description order by year, Quarter) as rn
 from QuarterlyRealGDPUSA
 where Description =  'All industry total' 
 )
 select DISTINCT
	t1.Area,
	t1.RealGDPValue,
	round(coalesce((cast(t1.RealGDPValue as real) - cast(t2.RealGDPValue as real))*1.0/t2.RealGDPValue,0),4) as QuarterlyGrowth
 from quarterlyGDP t1

 left join quarterlyGDP t2 on  t1. area = t2.area and t1.rn = t2.rn + 1 

 WHERE t1.RealGDPValue <> "(D)" AND (t1.year = 2020 and t1.Quarter = 3)
 
 order by   QuarterlyGrowth DESC
 limit 10;
```

| Area         | Real GDP Value | Quarterly Growth (%) |
|--------------|----------------|----------------------|
| Michigan     | 516,457.6      | 13.13%               |
| Nevada       | 167,431.3      | 11.94%               |
| Tennessee    | 373,789.1      | 11.36%               |
| Indiana      | 367,182.3      | 10.80%               |
| Idaho        | 85,032.1       | 10.17%               |
| Vermont      | 32,802.9       | 10.12%               |
| Kentucky     | 210,998.0      | 10.03%               |
| New Hampshire| 84,648.7       | 9.84%                |
| Rhode Island | 59,008.3       | 9.34%                |
| Montana      | 51,371.4       | 9.25%                |
