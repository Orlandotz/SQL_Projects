# Economic analysis with SQL

## Project overview
Analyze the United Stated quarterly GDP data to determine how the economy was recovering after COVID-19 crisis. 

## Data source
https://www.bea.gov. Interactive data to download the industry quarterly real GDP for the United States and each of the states, including District of Columbia. 

## Data description

<p> The number of entries analyzed were 33,696  the available data was 33,448 entries and undisclosed data was 248 entries. 52 states were analyzed and 22 industries. At state level I only included All industrial total to analyze GDP grow and what was the economy ranking. The GDP by industry was analyzed at national level. 
The next table represents the number of undisclosed data group by state and industry from 2018 and 2023: </p>




| Area                | Description                                       | Undisclosed Data |
|---------------------|---------------------------------------------------|------------------|
| Alaska              | Agriculture, forestry, fishing and hunting        | 4                |
| Alaska              | Durable goods manufacturing                       | 4                |
| Alaska              | Manufacturing                                     | 4                |
| Connecticut         | Agriculture, forestry, fishing and hunting        | 12               |
| Connecticut         | Mining, quarrying, and oil and gas extraction     | 12               |
| Delaware            | Agriculture, forestry, fishing and hunting        | 8                |
| Delaware            | Mining, quarrying, and oil and gas extraction     | 8                |
| District of Columbia| Agriculture, forestry, fishing and hunting        | 12               |
| District of Columbia| Durable goods manufacturing                       | 16               |
| District of Columbia| Manufacturing                                     | 16               |
| District of Columbia| Nondurable goods manufacturing                    | 8                |
| District of Columbia| Utilities                                         | 4                |
| Hawaii              | Agriculture, forestry, fishing and hunting        | 4                |
| Hawaii              | Durable goods manufacturing                       | 4                |
| Hawaii              | Manufacturing                                     | 4                |
| Indiana             | Agriculture, forestry, fishing and hunting        | 12               |
| Indiana             | Mining, quarrying, and oil and gas extraction     | 12               |
| Maine               | Mining, quarrying, and oil and gas extraction     | 8                |
| Maine               | Utilities                                         | 8                |
| Rhode Island        | Agriculture, forestry, fishing and hunting        | 12               |
| Rhode Island        | Mining, quarrying, and oil and gas extraction     | 12               |
| Wisconsin           | Agriculture, forestry, fishing and hunting        | 12               |
| Wisconsin           | Mining, quarrying, and oil and gas extraction     | 12               |
| Wyoming             | Agriculture, forestry, fishing and hunting        | 4                |
| Wyoming             | Durable goods manufacturing                       | 8                |
| Wyoming             | Manufacturing                                     | 8                |
| Wyoming             | Nondurable goods manufacturing                    | 8                |
| Wyoming             | Utilities                                         | 12               |



## Tools used
1. SQLite for data cleaning, preparation and analysis.
2. Tableau to create a Dashboard:
   ### https://public.tableau.com/views/Quarterlyeconomyanalysis/Dashboard1?:language=en-US&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link

## Exploratory data analysis
1. What are the top 10 industries in the United States and how they growth after the second quarter of 2020?
2. What are the top 10 States that show more recovery after the second quarter of 2020?
3. How many industries and states show a negative percentage during the first and second quarter of 2020? 

## Code
### Preparing the data


```SQL
## Define different levels of the industries with the creation of a new column DetailLevel, to query the data easier.
alter table RealGDP 
add COLUMN DetailLevel  TEXT;

##Identify the higher and lower levels of the industries by calculating the difference length of the description with empty spaces and the trimmed description.
update RealGDP
set DetailLevel =  length(description) - length(trim( Description));
## Assign different levels to the previous results
update RealGDP
set DetailLevel =
	case 
		when DetailLevel = 0 then replace(detaillevel,0,1)
		when DetailLevel = 2 then replace(detaillevel,2,1.1)
		when DetailLevel = 6 then replace(detaillevel,6,1.2)
		when DetailLevel = 4 then replace(detaillevel,4,1.2)
		when DetailLevel = 10 then replace(detaillevel,10,1.3)
	end;
## Finally, I trimmed the description
update RealGDP
set Description = trim(description)
```


From the main table RealGDP, I created a table for each quarter that contains the values description, detailedlevel and the GDP Value, 24 tables were created.  

```SQL
-- 2018 Q1
CREATE TABLE GDP_2018_Q1 AS
SELECT Description, DetailLevel, "2018:Q1"
FROM RealGDP;

-- 2018 Q2
CREATE TABLE GDP_2018_Q2 AS
SELECT Description, DetailLevel, "2018:Q2"
FROM RealGDP;

-- 2018 Q3
CREATE TABLE GDP_2018_Q3 AS
SELECT Description, DetailLevel, "2018:Q3"
FROM RealGDP;

-- 2018 Q4
CREATE TABLE GDP_2018_Q4 AS
SELECT Description, DetailLevel, "2018:Q4"
FROM RealGDP;

-- 2019 Q1
CREATE TABLE GDP_2019_Q1 AS
SELECT Description, DetailLevel, "2019:Q1"
FROM RealGDP;

-- 2019 Q2
CREATE TABLE GDP_2019_Q2 AS
SELECT Description, DetailLevel, "2019:Q2"
FROM RealGDP;

-- 2019 Q3
CREATE TABLE GDP_2019_Q3 AS
SELECT Description, DetailLevel, "2019:Q3"
FROM RealGDP;

-- 2019 Q4
CREATE TABLE GDP_2019_Q4 AS
SELECT Description, DetailLevel, "2019:Q4"
FROM RealGDP;

-- 2020 Q1
CREATE TABLE GDP_2020_Q1 AS
SELECT Description, DetailLevel, "2020:Q1"
FROM RealGDP;

-- 2020 Q2
CREATE TABLE GDP_2020_Q2 AS
SELECT Description, DetailLevel, "2020:Q2"
FROM RealGDP;

-- 2020 Q3
CREATE TABLE GDP_2020_Q3 AS
SELECT Description, DetailLevel, "2020:Q3"
FROM RealGDP;

-- 2020 Q4
CREATE TABLE GDP_2020_Q4 AS
SELECT Description, DetailLevel, "2020:Q4"
FROM RealGDP;

-- 2021 Q1
CREATE TABLE GDP_2021_Q1 AS
SELECT Description, DetailLevel, "2021:Q1"
FROM RealGDP;

-- 2021 Q2
CREATE TABLE GDP_2021_Q2 AS
SELECT Description, DetailLevel, "2021:Q2"
FROM RealGDP;

-- 2021 Q3
CREATE TABLE GDP_2021_Q3 AS
SELECT Description, DetailLevel, "2021:Q3"
FROM RealGDP;

-- 2021 Q4
CREATE TABLE GDP_2021_Q4 AS
SELECT Description, DetailLevel, "2021:Q4"
FROM RealGDP;

-- 2022 Q1
CREATE TABLE GDP_2022_Q1 AS
SELECT Description, DetailLevel, "2022:Q1"
FROM RealGDP;

-- 2022 Q2
CREATE TABLE GDP_2022_Q2 AS
SELECT Description, DetailLevel, "2022:Q2"
FROM RealGDP;

-- 2022 Q3
CREATE TABLE GDP_2022_Q3 AS
SELECT Description, DetailLevel, "2022:Q3"
FROM RealGDP;

-- 2022 Q4
CREATE TABLE GDP_2022_Q4 AS
SELECT Description, DetailLevel, "2022:Q4"
FROM RealGDP;

-- 2023 Q1
CREATE TABLE GDP_2023_Q1 AS
SELECT Description, DetailLevel, "2023:Q1"
FROM RealGDP;

-- 2023 Q2
CREATE TABLE GDP_2023_Q2 AS
SELECT Description, DetailLevel, "2023:Q2"
FROM RealGDP;

-- 2023 Q3
CREATE TABLE GDP_2023_Q3 AS
SELECT Description, DetailLevel, "2023:Q3"
FROM RealGDP;

-- 2023 Q4
CREATE TABLE GDP_2023_Q4 AS
SELECT Description, DetailLevel, "2023:Q4"
FROM RealGDP;

```
Table modifications 

```SQL
## Add a column for year 
alter table GDP_2018_Q1
ADD COLUMN year INT;

## Add a column for quarter
alter table GDP_2018_Q1
ADD COLUMN Quarter  INT;

## Remane the value columns to RealGDPValue
alter table  GDP_2018_Q1
RENAME COLUMN "2018:Q1" to RealGDPValue;

## Set the year vaslues from 2018 to 2023
UPDATE GDP_2018_Q1
set year = 2018;

## Set the quarter column from 1 to 4 for each year.
UPDATE GDP_2018_Q1
set Quater = 1

```

Consolidated tables

```SQL
CREATE TABLE QuarterlyRealGDPUSA AS
SELECT * FROM GDP_2018_Q1
UNION ALL
SELECT * FROM GDP_2018_Q2
UNION ALL
SELECT * FROM GDP_2018_Q3
UNION ALL
SELECT * FROM GDP_2018_Q4
UNION ALL
SELECT * FROM GDP_2019_Q1
UNION ALL
SELECT * FROM GDP_2019_Q2
UNION ALL
SELECT * FROM GDP_2019_Q3
UNION ALL
SELECT * FROM GDP_2019_Q4
UNION ALL
SELECT * FROM GDP_2020_Q1
UNION ALL
SELECT * FROM GDP_2020_Q2
UNION ALL
SELECT * FROM GDP_2020_Q3
UNION ALL
SELECT * FROM GDP_2020_Q4
UNION ALL
SELECT * FROM GDP_2021_Q1
UNION ALL
SELECT * FROM GDP_2021_Q2
UNION ALL
SELECT * FROM GDP_2021_Q3
UNION ALL
SELECT * FROM GDP_2021_Q4
UNION ALL
SELECT * FROM GDP_2022_Q1
UNION ALL
SELECT * FROM GDP_2022_Q2
UNION ALL
SELECT * FROM GDP_2022_Q3
UNION ALL
SELECT * FROM GDP_2022_Q4
UNION ALL
SELECT * FROM GDP_2023_Q1
UNION ALL
SELECT * FROM GDP_2023_Q2
UNION ALL
SELECT * FROM GDP_2023_Q3
UNION ALL
SELECT * FROM GDP_2023_Q4;


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
### What are the top 10 biggest industries after the second quarter of 2020, what is the ranking and what percentage of the United States they represent?

#### Query

```SQL

## Create a CTE to calculate the GDP percentage 
with cterealgdp as (
SELECT 
	year, 
	Quarter,
	area, 
	Detaillevel,
	Description, 
	RealGDPValue as Rgdpvalue,
	
from QuarterlyRealGDPUSA

)
select 

	t1. Description, 
	t1.Rgdpvalue,
	## Rank the industries by GDP value in descending order and partition them by area, year and quarter
	rank() OVER(partition by t1.Area, t1.year, t1.Quarter ORDER by	t1.Rgdpvalue DESC) as Industry_rank,

	## Calculate the industry GDP percentage of the total industry
	round(Cast(t1.Rgdpvalue as real)/Cast(t2.Rgdpvalue as real)*100,2) || '%' as GDP_Percentage

 from cterealgdp t1

left join cterealgdp t2 on t2.area = t1.area AND t2.year = t1.year and t2.Quarter = t1.Quarter

where 
## Filter to get only the numerical values for industries t1.Rgdpvalue <> "(D)" and divide the values by the Industry total t2.Description =  'All industry total' for United States in the 3rd quarter of 2020. 
	t1.Detaillevel = '1.2' 
	and  t1.Rgdpvalue <> "(D)"
	and t2.Description =  'All industry total' 
	and t1.area = 'United States'
	and (t1.year = 2020 and t1.Quarter = 3)
## order and limit the results to 10 values. 
order by t1.year, t1.Area, t1.Quarter,  t1.Rgdpvalue DESC
limit 10
;
```

#### Result 

| Description                                        | Real GDP Value | Industry Rank | GDP Percentage |
|----------------------------------------------------|----------------|---------------|----------------|
| Real estate and rental and leasing                 | 2,697,552      | 1             | 13.15%         |
| Manufacturing                                      | 2,177,609      | 2             | 10.62%         |
| Professional, scientific, and technical services   | 1,666,622      | 3             | 8.13%          |
| State and local                                    | 1,647,900      | 4             | 8.03%          |
| Health care and social assistance                  | 1,582,170      | 5             | 7.71%          |
| Finance and insurance                              | 1,545,591      | 6             | 7.54%          |
| Retail trade                                       | 1,284,802      | 7             | 6.26%          |
| Information                                        | 1,248,395      | 8             | 6.09%          |
| Wholesale trade                                    | 1,232,366      | 9             | 6.01%          |
| Construction                                       | 864,452        | 10            | 4.21%          |


### What are the top 10 industries that show more growth after the second quarter of 2020 and what is their ranking in the economy?

#### Query
```SQL

## Create a CTE to calculate the growth percent.
WITH quarterlyGDP as (
select 
	Area,
	year,
	Quarter,
	Detaillevel,
	Description,
	RealGDPValue,
	## Assign a row number to each quarter ans year partition by area.
	row_number()over(partition by Area, Description order by year, Quarter) as rn
from QuarterlyRealGDPUSA
 )
 select DISTINCT

	t1.Description,
	t1.RealGDPValue,
	rank() OVER(partition by t1.Area, t1.year, t1.Quarter ORDER by	t1.RealGDPValue DESC) as Industry_rank,
	## Calculate the growth percent
	round(coalesce((cast(t1.RealGDPValue as real) - cast(t2.RealGDPValue as real))*1.0/t2.RealGDPValue,0),4) as QuarterlyGrowth
	
 from quarterlyGDP t1
## Self join the table with the columns area, description and the value for row number to calculate the growth percent
 left join quarterlyGDP t2 on  t1. area = t2.area and t1.Description = t2.Description and t1.rn = t2.rn + 1 
 
	WHERE t1.RealGDPValue <> "(D)" and t1.Area = 'United States' and (t1.year = 2020 and t1.Quarter = 3) and 
	t1.Detaillevel = '1.2' 
	
	order by  QuarterlyGrowth DESC 
	
	limit 10;
 
```

#### Result

| Description                                                                  | Real GDP Value | Industry Rank | Quarterly Growth (%) |
|------------------------------------------------------------------------------|----------------|---------------|----------------------|
| Accommodation and food services                                              | 500,537        | 13            | 45.99%               |
| Arts, entertainment, and recreation                                          | 143,915        | 22            | 43.85%               |
| Other services (except government and government enterprises)                | 417,887        | 16            | 16.98%               |
| Health care and social assistance                                            | 1,582,170      | 5             | 16.39%               |
| Agriculture, forestry, fishing and hunting                                   | 178,785        | 21            | 13.74%               |
| Transportation and warehousing                                               | 604,314        | 12            | 12.50%               |
| Manufacturing                                                                | 2,177,609      | 2             | 11.53%               |
| Retail trade                                                                 | 1,284,802      | 7             | 10.82%               |
| Administrative and support and waste management and remediation services     | 617,160        | 11            | 10.47%               |
| Wholesale trade                                                              | 1,232,366      | 9             | 9.80%                |


### What are the top 10 economies in the United States and is their GDP percentage of the total US GDP? 

#### Query
```SQL
## Create a CTE to calculate the percentage that represent each state of the total United States Economy. Filter the values to get only the all industry total and only numeric values. 
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
	t1.Area,
	t1.RealGDPValue,
	## Caulculate the percent of GDP that each of the states represent.
	round(cast(t1.RealGDPValue as real)/Cast(t2.RealGDPValue as real),6) As GDPState_Percentage,
        ## Assign a rank to the states depending on the values. 
	rank() over (PARTITION by t1. Quarter, t1.year order by t1.RealGDPValue DESC) as State_Rank
 
from State_GDP_Percentage t1

## Self join the table with the columns year and quarter. 
left join State_GDP_Percentage t2 on t2.year = t1.year and t2.Quarter = t1.Quarter
	## Filter to get values different the United States t1.Area <> 'United States', and to get the total GDP filter t2.Area = 'United States' 
	Where t1.Area <> 'United States' and t2.Area = 'United States' and (t1.year = 2020 and t1.Quarter = 3)
	order by State_Rank ASC
	limit 10

```
#### Result

| Area        | Real GDP Value | GDPState Percentage | State Rank |
|-------------|----------------|---------------------|------------|
| California  | 2,962,454.8    | 14.44%              | 1          |
| Texas       | 1,795,027.9    | 8.75%               | 2          |
| New York    | 1,659,831.8    | 8.09%               | 3          |
| Florida     | 1,089,771.4    | 5.31%               | 4          |
| Illinois    | 818,584.2      | 3.99%               | 5          |
| Pennsylvania| 744,977.7      | 3.63%               | 6          |
| Ohio        | 665,995.6      | 3.25%               | 7          |
| Georgia     | 611,130.1      | 2.98%               | 8          |
| Washington  | 605,303.3      | 2.95%               | 9          |
| New Jersey  | 603,345.8      | 2.94%               | 10         |


### What are the top 10 States that show more growth after the second quarter of 2020?

#### Query
```SQL
## Create a CTE to calculate the growth perncent by state. 
WITH quarterlyGDP as (
select 
	Area,
	year,
	Quarter,
	Description,
	RealGDPValue,
	rank() OVER(PARTITION by year, Quarter order by RealGDPValue DESC) as State_rank,
	## Assign a ror number to area and description, and order them by year and quarter.
	row_number()over(partition by Area, Description order by year, Quarter) as rn
 from QuarterlyRealGDPUSA
 where Description =  'All industry total'  and area <> 'United States'
 
 )
 select DISTINCT
	t1.Area,
	t1.RealGDPValue,
	t1.State_rank,
	## Calculate the quarterly growth percent for eche state. 
	round(coalesce((cast(t1.RealGDPValue as real) - cast(t2.RealGDPValue as real))*1.0/t2.RealGDPValue,0),4) as QuarterlyGrowth
 from quarterlyGDP t1
## Self join the table with the columns area and row number. 
 left join quarterlyGDP t2 on  t1. area = t2.area and t1.rn = t2.rn + 1 

 WHERE t1.RealGDPValue <> "(D)" AND (t1.year = 2020 and t1.Quarter = 3)
 
 order by   QuarterlyGrowth DESC
 limit 10

```
#### Result
| State          | Real GDP Value | State Rank | Quarterly Growth (%) |
|----------------|----------------|------------|----------------------|
| Michigan       | 516,457.6      | 14         | 13.13%               |
| Nevada         | 167,431.3      | 33         | 11.94%               |
| Tennessee      | 373,789.1      | 17         | 11.36%               |
| Indiana        | 367,182.3      | 19         | 10.80%               |
| Idaho          | 85,032.1       | 39         | 10.17%               |
| Vermont        | 32,802.9       | 51         | 10.12%               |
| Kentucky       | 210,998.0      | 28         | 10.03%               |
| New Hampshire  | 84,648.7       | 40         | 9.84%                |
| Rhode Island   | 59,008.3       | 45         | 9.34%                |
| Montana        | 51,371.4       | 48         | 9.25%                |


### How many industries have decreaced or increase in percentage from the first quarter of 2018 to the fouth quarter of 2023?

#### Query

```SQL
## Create a CTE to determine how many industries increse or decrease each quarter. 
WITH quarterlyGDP as (
select 
	Area,
	year,
	Quarter,
	Description,
	RealGDPValue,
	row_number()over(partition by Area, Description order by year, Quarter) as rn
 from QuarterlyRealGDPUSA
 WHERE Detaillevel = 1.2
 )
 select DISTINCT

	t1.year,
	t1.Quarter,
	## With a case statement assign 1 if the State increase or decrease its value and the calculate the number with a count function.
	count(
	case WHEN round(coalesce((cast(t1.RealGDPValue as real) - cast(t2.RealGDPValue as real))*1.0/t2.RealGDPValue,0),8) < 0 then 1 END
	) No_Of_Industry_decrease,
	count(
	case WHEN round(coalesce((cast(t1.RealGDPValue as real) - cast(t2.RealGDPValue as real))*1.0/t2.RealGDPValue,0),8) > 0 then 1 END
	) No_Of_Industry_Increase

 from quarterlyGDP t1
 left join quarterlyGDP t2 on  t1. area = t2.area and t1.Description = t2.Description and t1.rn = t2.rn + 1 
WHERE t1.RealGDPValue <> "(D)" and t1.Area = 'United States'  and (t1.year <> 2018 or t1.quarter <> 1) and year = 2020 
 GROUP by t1.year, t1.Quarter 
  order by t1.year, t1.Quarter 
 ;

```
#### Result

| Year | Quarter | No. of Industries Decreased | No. of Industries Increased |
|------|---------|-----------------------------|-----------------------------|
| 2020 | 1       | 14                          | 8                           |
| 2020 | 2       | 18                          | 4                           |
| 2020 | 3       | 1                           | 21                          |
| 2020 | 4       | 8                           | 14                          |


### How many states have decreaced or increase in percentage from the first quarter of 2018 to the fouth quarter of 2023?

#### Query

```SQL
## Create a CTE to determine how many states increse or decrease each quarter. 
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

	t1.year,
	t1.Quarter,

	## With a case statement assign 1 if the State increase or decrease its value and the calculate the number with a count function.
	count(
	case WHEN round(coalesce((cast(t1.RealGDPValue as real) - cast(t2.RealGDPValue as real))*1.0/t2.RealGDPValue,0),4) <0 THEN 1
	END
	) No_States_decrease,
	count(
	case WHEN round(coalesce((cast(t1.RealGDPValue as real) - cast(t2.RealGDPValue as real))*1.0/t2.RealGDPValue,0),4) >0 THEN 1
	END
	)No_States_GDP_Increase


 from quarterlyGDP t1
 left join quarterlyGDP t2 on  t1. area = t2.area and t1.rn = t2.rn + 1 
WHERE t1.RealGDPValue <> "(D)" and (t1.year <> 2018 or t1.Quarter <> 1) and t1.year = 2020

## Group the count result by year and quarter. 
group by t1.year,  t1.Quarter 
order by t1.year,  t1.Quarter ;
```

#### Result

| Year | Quarter | No. of States Decreased | No. of States GDP Increased |
|------|---------|-------------------------|-----------------------------|
| 2020 | 1       | 48                      | 4                           |
| 2020 | 2       | 52                      | 0                           |
| 2020 | 3       | 0                       | 52                          |
| 2020 | 4       | 6                       | 45                          |

# Conclusions 

1. The industry with the highest GDP during the third quarter of 2020 was Real estate and rental and leasing, however it only grew 3.39% in comparison with Accommodation and food services that grew 46% after the second quarter of 2020.
2. California had the highest GDP in the the third quarter representing 14% of the United States total GDP, but it only grew 8% in comparison to Michigan that grew 13% during the same period.
3. The second quarter of 2020 was the worst period for the economy, all the states show a reduction in the GDP and only 4 industries show a positive result.

| Description        | Real GDP Value | Industry Rank | Quarterly Growth |
|-----------------------|----------------|---------------|------------------|
| Finance and insurance | 1,530,918      | 5             | 2.43%            |
| Utilities             | 330,582        | 17            | 1.19%            |
| Military              | 322,587.5      | 18            | 0.96%            |
| Federal civilian      | 476,702.3      | 13            | 0.85%            |
