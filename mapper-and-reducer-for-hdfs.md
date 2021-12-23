# Mapper and Reducer for HDFS

MyMapper.py:

The mapper takes all the values from the inputs and splits them into named columns. Referring to target values is much easier by column name than by column number. This mapper code takes the unique carrier and the flight origin and joins them together to form a "key" and pairs it with TaxiIn (value1) and TaxiOut (value2.) This pair then becomes an input for the reducer.

```
#! /usr/bin/env python

import sys

# -- Airline Data
# Year, Month, DayofMonth, DayOfWeek, DepTime, CRSDepTime, ArrTime, CRSArrTime, UniqueCarrier, FlightNum,
# TailNum, ActualElapsedTime, CRSElapsedTime, AirTime, ArrDelay, DepDelay, Origin, Dest, Distance, TaxiIn,
# TaxiOut, Cancelled, CancellationCode, Diverted, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay

for line in sys.stdin:
    line = line.strip()
    unpacked = line.split(",")
    Year, Month, DayofMonth, DayOfWeek, DepTime, CRSDepTime, ArrTime, CRSArrTime, UniqueCarrier, FlightNum, TailNum, ActualElapsedTime, CRSElapsedTime, AirTime, ArrDelay, DepDelay, Origin, Dest, Distance, TaxiIn, TaxiOut, Cancelled, CancellationCode, Diverted, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay = line.split(",")
    value1 = TaxiIn
    value2 = TaxiOut
    key = "-".join([UniqueCarrier, Origin])
    print("\t".join([key, value1, value2]))
```

MyReducer.py:

The reducer takes in the ordered pairs of the mapper output and uses them to calculate the total taxi time (TaxiIn+TaxiOut) and finds the minimum, maximum, and the average of the total taxi time, in that order.

```
#! /usr/bin/env python

import sys

last_key = None
count = 0
sum = 0
check_min = 0
check_max = 0

# keys come grouped together
# so we need to keep track of state a little bit
# thus when the key changes , we need to reset
# our counter, and write out the count we've accumulated

for line in sys.stdin:
   line = line.strip()
   key, value1, value2 = line.split("\t")

   # we have to be able to deal with missing values
   if value1 =="NA" or value2 =="NA":
       continue

   value = int(value1)+int(value2)

   # if this is the first iteration
   if not last_key:
       last_key = key
       check_min = value
       check_max = value
       sum = value
       count = 1

    # if they're the same, log it
   elif key == last_key:
        check_min = min(check_min, value)
        check_max = max(check_max, value)
        sum = sum + value
        count = count + 1

   else: 
        result = [last_key, check_min, check_max, sum/count]
        print("\t".join(str(v) for v in result))
        last_key = key
        count = 1
        sum = value
        check_min = value
        check_max = value

# this is to catch the final value that we output
print("\t".join(str(v) for v in [last_key, check_min, check_max, sum/count]))
```

