This is a brief description for my attempt to the data engineering test for SamKnows

**Understanding**

The scope of the test is to generate a report of data anomalies in the file data_engineering.csv provided as part of the exercise. It is asked to be done using either an SQL approach or a programming language approach. 

**Assumptions & approach**

1. In the interest of time, I have chosen an SQL approach.
2. fetch_time in the file is not present in the format definition. I've included the column also when loading.
3. the following data format validations have been used to check if data is in the right format
* dtime - check if the datetime is valid (load will fail if not datetime)
* device_identifier - check if not null (no point loading records without device identifier) 
* bytes_total - check if >= 0
* bytes_per_second - check if >= 0
* successes - check if 1 or 0 
* failures - check if 1 or 0 
* fetchtime - check if >= 0
4. I have installed and configured a mySQL database on my laptop to carry on with the exercise. 
5. Data folder has to be configured appropriately for data I/O from and to the mySQL db. This can be done my configuring the file my.cnf and adding in the data folder into the variable secure-file-priv
e.g. My file was in the folder /Volumes/Media/Learning/SamKnows
Open the file my.cnf for editing
> `sudo vim /etc/my.cnf`

Add the following variable into it. 
> `secure-file-priv=/Volumes/Media/Learning/SamKnows`

Set the right permissions
> `sudo chown -R mysql:mysql  /Volumes/Media/Learning/SamKnows/`


**Steps involved**

1. create a new database
> `create database samknows`
2. create table (table created with all character datatypes to enable validation)
> `create table ingest_table (`
`dtime varchar(255),`
`device_identifier varchar(255),`
`bytes_total varchar(255),`
`bytes_per_second varchar(255),`
`successes varchar(255),`
`failures varchar(255) ,`
`fetchtime varchar(255)`
`);`
3. load data
> `LOAD DATA INFILE '/Volumes/Media/Learning/SamKnows/data_engineering.csv' 
INTO TABLE ingest_table 
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;
`
4. Create validation table 
> `create table ingest_validation as 
Select 
	case when STR_TO_DATE(dtime, ‘%Y-%m-%d %H:%i:%s.%f’) is null then 0 else 1 end dtime_check,
	case when device_identifier is null then 0 else 1 end device_identifier_check,
	case when bytes_total >= 0 then 1 else 0 end bytes_total_check,
	case when bytes_per_second >= 0 then 1 else 0 end bytes_per_second_check,
	case when successes in (0,1) then 1 else 0 end successes_check,
	case when failures in (0,1) then 1 else 0 end failures_check,
	case when fetchtime >= 0 then 1 else 0 end fetchtime_check
From ingest_table;`
5. find number of failed records
> `select count(1) from ingest_validation where dtime_check = 0 or device_identifier_check = 0 or bytes_total_check = 0 or bytes_per_second_check = 0 or successes_check = 0 or failures_check = 0 or fetchtime_check = 0;`
6. find summary of failed records by column
> `select    colname, summary, count(1)  from (    select 'dtime_check' as colname, case when dtime_check = 0 then 'invalid' else 'valid' end as summary from ingest_validation union all   select 'device_identifier' as colname, case when device_identifier_check = 0 then 'invalid' else 'valid' end as summary from ingest_validation union all   select 'bytes_total' as colname, case when bytes_total_check = 0 then 'invalid' else 'valid' end as summary from ingest_validation union all   select 'bytes_per_second' as colname, case when bytes_per_second_check = 0 then 'invalid' else 'valid' end as summary from ingest_validation union all   select 'successes' as colname, case when successes_check = 0 then 'invalid' else 'valid' end as summary from ingest_validation union all   select 'failures_check' as colname, case when failures_check = 0 then 'invalid' else 'valid' end as summary from ingest_validation union all   select 'fetchtime_check' as colname, case when fetchtime_check = 0 then 'invalid' else 'valid' end as summary from ingest_validation  ) a  group by colname, summary;`


**Results Summary**
* Step 5 
> `+----------+  `  
> `| count(1) |  `  
> `+----------+  `  
> `|     1132 |  `  
> `+----------+ `   
* Step 6
> `+-------------------+---------+----------+`  
> `| colname           | summary | count(1) |`  
> `+-------------------+---------+----------+`  
> `| dtime_check       | valid   |   348767 |`  
> `| device_identifier | valid   |   348767 |`  
> `| bytes_total       | valid   |   348767 |`  
> `| bytes_per_second  | valid   |   347635 |`  
> `| bytes_per_second  | invalid |     1132 |`  
> `| successes         | valid   |   348767 |`  
> `| failures_check    | valid   |   348767 |`  
> `| fetchtime_check   | valid   |   348767 |`  
> `+-------------------+---------+----------+` 
