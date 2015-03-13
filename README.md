# Hive

###Sample data

```
arun:inputlocal arun$ pwd
/Users/arun/apache-hive-1.1.0-bin/inputlocal
arun:inputlocal arun$ cat sample.txt 
1950	0	1
1950	22	1
1950	-11	1
1949	111	1
1949	78	1

arun:inputlocal arun$ 
```
### Create table
```
CREATE TABLE records (year STRING, temperature INT, quality INT)
ROW FORMAT DELIMITED
  FIELDS TERMINATED BY '\t';
```

### show tables
```
  hive> show tables;
OK
dummy
records
Time taken: 0.102 seconds, Fetched: 2 row(s)
```

### Load local data in to Table
```
hive> LOAD DATA LOCAL INPATH '/Users/arun/apache-hive-1.1.0-bin/inputlocal/sample.txt'
    > OVERWRITE INTO TABLE records;
Loading data to table default.records
Table default.records stats: [numFiles=1, numRows=0, totalSize=52, rawDataSize=0]
OK
Time taken: 3.166 seconds
```
### Show the table details

```
hive> select * from records;
OK
1950	0	1
1950	22	1
1950	-11	1
1949	111	1
1949	78	1
Time taken: 0.181 seconds, Fetched: 5 row(s)

```

The data is loaded in to HDFS
```
arun:inputlocal arun$ hadoop fs -ls /user/hive/warehouse
Warning: $HADOOP_HOME is deprecated.

Found 2 items
drwxr-xr-x   - arun supergroup          0 2015-03-13 12:09 /user/hive/warehouse/dummy
drwxr-xr-x   - arun supergroup          0 2015-03-13 12:27 /user/hive/warehouse/records

```

###Query the data

```
SELECT year, MAX(temperature)
    > FROM records
    > WHERE temperature != 9999 AND quality IN (0, 1, 4, 5, 9)
    > GROUP BY year;
```

####error
ERROR XSDB6: Another instance of Derby may have already booted the database /Users/arun/apache-hive-1.1.0-bin/metastore_db.
####reason
By default Hive uses Derby to store metadata of the tables. Derby is a single instance database. So you might be running another instance in your machine. check the processes running in your machine
```
ps - ef| grep hive
```

get the process Id for the hive process and kill it
```
kill -9 {PID}
```

#### error
Logging initialized using configuration in jar:file:/Users/arun/apache-hive-1.1.0-bin/lib/hive-common-1.1.0.jar!/hive-log4j.properties
Exception in thread "main" java.lang.RuntimeException: java.net.ConnectException: Call to localhost/127.0.0.1:10001 failed on connection exception: java.net.ConnectException: Connection refused
#### reason
Possible reason :Hadoop services are not running
#### solution
Start hadoop services (namenode, SNN, Datanode, Task and Job tracker) and then use hive
Note: you have to wait while the namenode comes out from safemode to run hive shell






