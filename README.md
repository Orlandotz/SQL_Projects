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
### What are the top 10 biggest industries after the second quarter of 2020, what is the ranking and what percentage of the United States they represent?

#### Query

```SQL
with cterealgdp as (
SELECT 
	year, 
	Quarter,
	area, 
	Detaillevel,
	Description, 
	RealGDPValue as Rgdpvalue,
	rank() OVER(partition by Area, year, Quarter ORDER by	RealGDPValue DESC) as Industry_rank
from QuarterlyRealGDPUSA

)
select 

	t1. Description, 
	t1.Rgdpvalue,
	rank() OVER(partition by t1.Area, t1.year, t1.Quarter ORDER by	t1.Rgdpvalue DESC) as Industry_rank,
	round(Cast(t1.Rgdpvalue as real)/Cast(t2.Rgdpvalue as real)*100,2) || '%' as GDP_Percentage

 from cterealgdp t1

left join cterealgdp t2 on t2.area = t1.area AND t2.year = t1.year and t2.Quarter = t1.Quarter

where 

	t1.Detaillevel = '1.2' 
	and  t1.Rgdpvalue <> "(D)"
	and t2.Description =  'All industry total' 
	and t1.area = 'United States'
	and (t1.year = 2020 and t1.Quarter = 3)

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

```SQL
WITH quarterlyGDP as (
select 
	Area,
	year,
	Quarter,
	Detaillevel,
	Description,
	RealGDPValue,
	row_number()over(partition by Area, Description order by year, Quarter) as rn
	 from QuarterlyRealGDPUSA
 )
 select DISTINCT

	t1.Description,
	t1.RealGDPValue,
	rank() OVER(partition by t1.Area, t1.year, t1.Quarter ORDER by	t1.RealGDPValue DESC) as Industry_rank,
	
	round(coalesce((cast(t1.RealGDPValue as real) - cast(t2.RealGDPValue as real))*1.0/t2.RealGDPValue,0),4) as QuarterlyGrowth
	
 from quarterlyGDP t1
 left join quarterlyGDP t2 on  t1. area = t2.area and t1.Description = t2.Description and t1.rn = t2.rn + 1 
 
	WHERE t1.RealGDPValue <> "(D)" and t1.Area = 'United States' and (t1.year = 2020 and t1.Quarter = 3) and 
	t1.Detaillevel = '1.2' 
	
	order by  QuarterlyGrowth DESC 
	
	limit 10;
 
```
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
#### Reult
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

#### Query
```SQL
WITH quarterlyGDP as (
select 
	Area,
	year,
	Quarter,
	Description,
	RealGDPValue,
	rank() OVER(PARTITION by year, Quarter order by RealGDPValue DESC) as State_rank,
	row_number()over(partition by Area, Description order by year, Quarter) as rn
 from QuarterlyRealGDPUSA
 where Description =  'All industry total'  and area <> 'United States'
 
 )
 select DISTINCT
	t1.Area,
	t1.RealGDPValue,
	t1.State_rank,
	round(coalesce((cast(t1.RealGDPValue as real) - cast(t2.RealGDPValue as real))*1.0/t2.RealGDPValue,0),4) as QuarterlyGrowth
 from quarterlyGDP t1

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
	count(
	case WHEN round(coalesce((cast(t1.RealGDPValue as real) - cast(t2.RealGDPValue as real))*1.0/t2.RealGDPValue,0),8) < 0 then 1 END
	) No_Of_Industry_decrease,
	count(
	case WHEN round(coalesce((cast(t1.RealGDPValue as real) - cast(t2.RealGDPValue as real))*1.0/t2.RealGDPValue,0),8) > 0 then 1 END
	) No_Of_Industry_Increase

 from quarterlyGDP t1
 left join quarterlyGDP t2 on  t1. area = t2.area and t1.Description = t2.Description and t1.rn = t2.rn + 1 
WHERE t1.RealGDPValue <> "(D)" and t1.Area = 'United States'  and (t1.year <> 2018 or t1.quarter <> 1)
 GROUP by t1.year, t1.Quarter 
  order by t1.year, t1.Quarter 
 ;

```
#### Result

| Year | Quarter | No. of Industries Decreased | No. of Industries Increased |
|------|---------|-----------------------------|-----------------------------|
| 2018 | 2       | 5                           | 17                          |
| 2018 | 3       | 5                           | 17                          |
| 2018 | 4       | 8                           | 14                          |
| 2019 | 1       | 12                          | 10                          |
| 2019 | 2       | 2                           | 20                          |
| 2019 | 3       | 2                           | 20                          |
| 2019 | 4       | 6                           | 16                          |
| 2020 | 1       | 14                          | 8                           |
| 2020 | 2       | 18                          | 4                           |
| 2020 | 3       | 1                           | 21                          |
| 2020 | 4       | 8                           | 14                          |
| 2021 | 1       | 7                           | 15                          |
| 2021 | 2       | 4                           | 18                          |
| 2021 | 3       | 9                           | 13                          |
| 2021 | 4       | 4                           | 18                          |
| 2022 | 1       | 11                          | 11                          |
| 2022 | 2       | 10                          | 12                          |
| 2022 | 3       | 6                           | 16                          |
| 2022 | 4       | 4                           | 18                          |
| 2023 | 1       | 8                           | 14                          |
| 2023 | 2       | 9                           | 13                          |
| 2023 | 3       | 8                           | 14                          |
| 2023 | 4       | 4                           | 18                          |

### How many states have decreaced or increase in percentage from the first quarter of 2018 to the fouth quarter of 2023?

#### Query

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

	t1.year,
	t1.Quarter,
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
WHERE t1.RealGDPValue <> "(D)" and (t1.year <> 2018 or t1.Quarter <> 1)
group by t1.year,  t1.Quarter 
order by t1.year,  t1.Quarter ;
```

#### Result

| Year | Quarter | No. of States Decreased | No. of States GDP Increased |
|------|---------|-------------------------|-----------------------------|
| 2018 | 2       | 10                      | 42                          |
| 2018 | 3       | 12                      | 40                          |
| 2018 | 4       | 20                      | 31                          |
| 2019 | 1       | 16                      | 36                          |
| 2019 | 2       | 6                       | 44                          |
| 2019 | 3       | 0                       | 52                          |
| 2019 | 4       | 9                       | 43                          |
| 2020 | 1       | 48                      | 4                           |
| 2020 | 2       | 52                      | 0                           |
| 2020 | 3       | 0                       | 52                          |
| 2020 | 4       | 6                       | 45                          |
| 2021 | 1       | 10                      | 42                          |
| 2021 | 2       | 4                       | 48                          |
| 2021 | 3       | 13                      | 39                          |
| 2021 | 4       | 2                       | 50                          |
| 2022 | 1       | 32                      | 20                          |
| 2022 | 2       | 24                      | 28                          |
| 2022 | 3       | 8                       | 44                          |
| 2022 | 4       | 10                      | 42                          |
| 2023 | 1       | 12                      | 40                          |
| 2023 | 2       | 6                       | 46                          |
| 2023 | 3       | 0                       | 52                          |
| 2023 | 4       | 0                       | 52                          |
