# Hadoop Script

Now that we have the mapper and reducer set up, we can go forth and use this on our dataset. In this case the dataset was named "wholeEnchilada.csv" and the output is stored in a directory called myOutput.

To call the mapper and the reducer is simple, all we need to do is enter a jar command and specify the mapper, reducer, and and dataset location and specify an output name.

```
hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar
	-file ./MyMapper.py -mapper 'MyMapper.py' 
	-file ./MyReducer.py -reducer 'MyReducer.py'
	-input directory/wholeEnchilada.csv
        -output directory/myOutput 

```

In this case the mapper and reducer were put into the Hadoop file system using a\
\-put command on a linux machine which made a directory for them. That is why we need to use "./MyMapper.py" and "./MyReducer.py" to specify the Hadoop directory to look in. If the mapper and reducer are stored in a different location, one should use the directory path to call them. For example:

```
hadoop jar /home/User/Documents/hadoop-streaming.jar \
        -mapper /home/User/Documents/mapper.py \
        -reducer /home/User/Documents/reducer.py
        -input /data.txt \
        -output /myOutput \
```



To check our output, we use the -cat command. All outputs in Hadoop are stored in the directory specified in the streaming command above. This directory usually has a log file and an output file, and the output file has the output stored in "part-00000." So, the command to look at the output is something like:

```
hadoop -cat /myOutput/output/part-00000
```

Once you are satisfied with the output, you can save it to your system using the -get command:

```
hadoop fs -get myOutput/p* myOutputStore
```

This command copies the output to the system's working directory in "myOutputStore."



For this assignment, there were more than 2400 lines of output. I will not post all of them but here are the top 20 ordered by name:

```
9E-ABE	0	220	28
9E-ALB	0	101	26
9E-ALO	0	83	24
9E-ATL	9	300	29
9E-ATW	0	191	27
9E-AUS	0	104	24
9E-AVL	0	168	26
9E-AVP	0	107	28
9E-AZO	0	152	26
9E-BDL	0	132	27
9E-BGM	0	157	25
9E-BGR	0	209	29
9E-BHM	0	161	26
9E-BIL	12	101	27
9E-BIS	0	93	21
9E-BJI	0	99	20
9E-BNA	0	158	27
9E-BOS	0	187	31
9E-BTR	0	62	18
9E-BTV	0	163	30
```

The output columns correspond to the order specified in the last print statement of the reducer. So, the output here is carrier-origin, minimum taxi time, maximum taxi time, and average taxi time.
