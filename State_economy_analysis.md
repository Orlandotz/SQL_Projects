# Data cleaning
## Create employment view
```SQL
  CREATE VIEW Employement_View as
    SELECT 
        CASE WHEN "2008" IS NOT NULL THEN 2008 END AS Year,
        GeoName AS Area, 
        Description, 
        "2008" AS No_of_employees 
    FROM Employment
    WHERE "2008" IS NOT NULL
    
    UNION ALL 
    
    SELECT 
        CASE WHEN "2009" IS NOT NULL THEN 2009 END AS Year,
        GeoName AS Area, 
        Description, 
        "2009" AS No_of_employees 
    FROM Employment
    WHERE "2009" IS NOT NULL
    
    UNION ALL 
    
    SELECT 
        CASE WHEN "2010" IS NOT NULL THEN 2010 END AS Year,
        GeoName AS Area, 
        Description, 
        "2010" AS No_of_employees 
    FROM Employment
    WHERE "2010" IS NOT NULL
    
    UNION ALL 
    
    SELECT 
        CASE WHEN "2011" IS NOT NULL THEN 2011 END AS Year,
        GeoName AS Area, 
        Description, 
        "2011" AS No_of_employees 
    FROM Employment
    WHERE "2011" IS NOT NULL
    
    UNION ALL 
    
    SELECT 
        CASE WHEN "2012" IS NOT NULL THEN 2012 END AS Year,
        GeoName AS Area, 
        Description, 
        "2012" AS No_of_employees 
    FROM Employment
    WHERE "2012" IS NOT NULL
    
    UNION ALL 
    
    SELECT 
        CASE WHEN "2013" IS NOT NULL THEN 2013 END AS Year,
        GeoName AS Area, 
        Description, 
        "2013" AS No_of_employees 
    FROM Employment
    WHERE "2013" IS NOT NULL
    
    UNION ALL 
    
    SELECT 
        CASE WHEN "2014" IS NOT NULL THEN 2014 END AS Year,
        GeoName AS Area, 
        Description, 
        "2014" AS No_of_employees 
    FROM Employment
    WHERE "2014" IS NOT NULL
    
    UNION ALL 
    
    SELECT 
        CASE WHEN "2015" IS NOT NULL THEN 2015 END AS Year,
        GeoName AS Area, 
        Description, 
        "2015" AS No_of_employees 
    FROM Employment
    WHERE "2015" IS NOT NULL
    
    UNION ALL 
    
    SELECT 
        CASE WHEN "2016" IS NOT NULL THEN 2016 END AS Year,
        GeoName AS Area, 
        Description, 
        "2016" AS No_of_employees 
    FROM Employment
    WHERE "2016" IS NOT NULL
    
    UNION ALL 
    
    SELECT 
        CASE WHEN "2017" IS NOT NULL THEN 2017 END AS Year,
        GeoName AS Area, 
        Description, 
        "2017" AS No_of_employees 
    FROM Employment
    WHERE "2017" IS NOT NULL
    
    UNION ALL 
    
    SELECT 
        CASE WHEN "2018" IS NOT NULL THEN 2018 END AS Year,
        GeoName AS Area, 
        Description, 
        "2018" AS No_of_employees 
    FROM Employment
    WHERE "2018" IS NOT NULL
    
    UNION ALL 
    
    SELECT 
        CASE WHEN "2019" IS NOT NULL THEN 2019 END AS Year,
        GeoName AS Area, 
        Description, 
        "2019" AS No_of_employees 
    FROM Employment
    WHERE "2019" IS NOT NULL
    
    UNION ALL 
    
    SELECT 
        CASE WHEN "2020" IS NOT NULL THEN 2020 END AS Year,
        GeoName AS Area, 
        Description, 
        "2020" AS No_of_employees 
    FROM Employment
    WHERE "2020" IS NOT NULL
    
    UNION ALL 
    
    SELECT 
        CASE WHEN "2021" IS NOT NULL THEN 2021 END AS Year,
        GeoName AS Area, 
        Description, 
        "2021" AS No_of_employees 
    FROM Employment
    WHERE "2021" IS NOT NULL
    
    UNION ALL 
    
    SELECT 
        CASE WHEN "2022" IS NOT NULL THEN 2022 END AS Year,
        GeoName AS Area, 
        Description, 
        "2022" AS No_of_employees 
    FROM Employment
    WHERE "2022" IS NOT NULL;
```
## Create Expenses per capita view 

```SQL
CREATE VIEW EPC AS
  SELECT 
    CASE WHEN "2008" IS NOT NULL THEN 2008 END AS Year,
    Geoname AS Area,
    Description,
    "2008" AS Expenses
FROM Expenses_per_capital

UNION ALL

SELECT 
    CASE WHEN "2009" IS NOT NULL THEN 2009 END AS Year,
    Geoname AS Area,
    Description,
    "2009" AS Expenses
FROM Expenses_per_capital

UNION ALL

SELECT 
    CASE WHEN "2010" IS NOT NULL THEN 2010 END AS Year,
    Geoname AS Area,
    Description,
    "2010" AS Expenses
FROM Expenses_per_capital

UNION ALL

SELECT 
    CASE WHEN "2011" IS NOT NULL THEN 2011 END AS Year,
    Geoname AS Area,
    Description,
    "2011" AS Expenses
FROM Expenses_per_capital

UNION ALL

SELECT 
    CASE WHEN "2012" IS NOT NULL THEN 2012 END AS Year,
    Geoname AS Area,
    Description,
    "2012" AS Expenses
FROM Expenses_per_capital

UNION ALL

SELECT 
    CASE WHEN "2013" IS NOT NULL THEN 2013 END AS Year,
    Geoname AS Area,
    Description,
    "2013" AS Expenses
FROM Expenses_per_capital

UNION ALL

SELECT 
    CASE WHEN "2014" IS NOT NULL THEN 2014 END AS Year,
    Geoname AS Area,
    Description,
    "2014" AS Expenses
FROM Expenses_per_capital

UNION ALL

SELECT 
    CASE WHEN "2015" IS NOT NULL THEN 2015 END AS Year,
    Geoname AS Area,
    Description,
    "2015" AS Expenses
FROM Expenses_per_capital

UNION ALL

SELECT 
    CASE WHEN "2016" IS NOT NULL THEN 2016 END AS Year,
    Geoname AS Area,
    Description,
    "2016" AS Expenses
FROM Expenses_per_capital

UNION ALL

SELECT 
    CASE WHEN "2017" IS NOT NULL THEN 2017 END AS Year,
    Geoname AS Area,
    Description,
    "2017" AS Expenses
FROM Expenses_per_capital

UNION ALL

SELECT 
    CASE WHEN "2018" IS NOT NULL THEN 2018 END AS Year,
    Geoname AS Area,
    Description,
    "2018" AS Expenses
FROM Expenses_per_capital

UNION ALL

SELECT 
    CASE WHEN "2019" IS NOT NULL THEN 2019 END AS Year,
    Geoname AS Area,
    Description,
    "2019" AS Expenses
FROM Expenses_per_capital

UNION ALL

SELECT 
    CASE WHEN "2020" IS NOT NULL THEN 2020 END AS Year,
    Geoname AS Area,
    Description,
    "2020" AS Expenses
FROM Expenses_per_capital

UNION ALL

SELECT 
    CASE WHEN "2021" IS NOT NULL THEN 2021 END AS Year,
    Geoname AS Area,
    Description,
    "2021" AS Expenses
FROM Expenses_per_capital

UNION ALL

SELECT 
    CASE WHEN "2022" IS NOT NULL THEN 2022 END AS Year,
    Geoname AS Area,
    Description,
    "2022" AS Expenses
FROM Expenses_per_capital;

```


