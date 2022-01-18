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

The above Hive script yields the same results as the Hadoop MapReduce.



| airlineorigin | min | max | average  |
| ------------- | --- | --- | -------- |
| 9E-ABE        | 0   | 220 | 28.01667 |
| 9E-ALB        | 0   | 101 | 26.54437 |
| 9E-ALO        | 0   | 83  | 24.94542 |
| 9E-ATL        | 9   | 300 | 29.65468 |
| 9E-ATW        | 0   | 191 | 27.14186 |
| 9E-AUS        | 0   | 104 | 24.8308  |
| 9E-AVL        | 0   | 168 | 26.95926 |
| 9E-AVP        | 0   | 107 | 28.39558 |
| 9E-AZO        | 0   | 152 | 26.15218 |
| 9E-BDL        | 0   | 132 | 27.66311 |
| 9E-BGM        | 0   | 157 | 25.59056 |
| 9E-BGR        | 0   | 209 | 29.32854 |
| 9E-BHM        | 0   | 161 | 26.61771 |
| 9E-BIL        | 12  | 101 | 27.975   |
| 9E-BIS        | 0   | 93  | 21.57887 |
| 9E-BJI        | 0   | 99  | 20.0796  |
| 9E-BNA        | 0   | 158 | 27.59889 |
| 9E-BOS        | 0   | 187 | 31.72752 |
| 9E-BTR        | 0   | 62  | 18.91587 |

Major difference between Hadoop and Hive approach is that Hive was designed such that those with low programming experience could still leverage a distributed file system using SQL queries.
