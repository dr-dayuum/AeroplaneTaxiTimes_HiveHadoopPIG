# Hive/SQL Script

"Apache Hive is a distributed, fault-tolerant data warehouse system that enables analytics at a massive scale. A data warehouse provides a central store of information that can easily be analyzed to make informed, data driven decisions. Hive allows users to read, write, and manage petabytes of data using SQL." [`(AWS)`](https://aws.amazon.com/big-data/what-is-hive/#:\~:text=Hive%20allows%20users%20to%20read,quickly%20on%20petabytes%20of%20data.)``

```
CREATE TABLE temp_finals(col_value STRING);

LOAD DATA INPATH '/user/maria_dev/wholeEnchilada.csv' OVERWRITE INTO TABLE temp_finals;

CREATE TABLE Taxis (UniqueCarrier STRING, Origin STRING, TaxiIn INT, TaxiOut INT);
INSERT OVERWRITE TABLE Taxis
SELECT
	REGEXP_EXTRACT(COL_VALUE,'^(?:([^,]*),?){9}', 1) UniqueCarrier,
	REGEXP_EXTRACT(COL_VALUE,'^(?:([^,]*),?){17}', 1) Origin,
	REGEXP_EXTRACT(COL_VALUE,'^(?:([^,]*),?){20}', 1) TaxiIn,
	REGEXP_EXTRACT(COL_VALUE,'^(?:([^,]*),?){21}', 1) TaxiOut
FROM temp_finals;

SELECT Table1.AirlineOrigin AS AirlineOrigin, MIN(Table1.TaxiVals) AS Min, MAX(Table1.TaxiVals) AS Max, AVG(Table1.TaxiVals) AS Average
FROM (SELECT 
	CONCAT(UniqueCarrier, '-', Origin) AS AirlineOrigin,
	(TaxiIn + TaxiOut) AS TaxiVals
	FROM Taxis
	WHERE TaxiIn NOT LIKE 'NA' OR TaxiOut NOT LIKE 'NA') AS Table1
GROUP BY Table1.AirlineOrigin
ORDER BY Table1.AirlineOrigin;
```

{% file src=".gitbook/assets/TaxiHiveResults.csv" %}

The above Hive script yields the same results as the Hadoop MapReduce. Major difference between Hadoop and Hive approach is that Hive was designed such that those with low programming experience could still leverage a distributed file system using SQL queries.
