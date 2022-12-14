The provided datasets are real time datasets of the iNeuron technical consultant team. You have to perform HIVE analysis on this given dataset.

Download Dataset 1 - https://drive.google.com/file/d/1WrG-9qv6atP-W3P_-gYln1hHyFKRKMHP/view

Download Dataset 2 - https://drive.google.com/file/d/1-JIPCZ34dyN6k9CqJa-Y8yxIGq6vTVXU/view

Note: both files are csv files.

### 1. Create a schema based on the given dataset.

#SCHEMA FOR AgentLogingReport.csv

hive> USE projects;    
    > CREATE TABLE agentlogingreport    
    > (    
    > sl_no int,   
    > agent_name string,   
    > date string,   
    > login_time string,   
    > logout_time string,   
    > duration string   
    > )  
    > ROW FORMAT DELIMITED   
    > FIELDS TERMINATED BY ','   
    > TBLPROPERTIES ("skip.header.line.count"="1");   

#SCHEMA FOR AgentPerformance.csv

hive> CREATE TABLE agentperformance   
    > (   
    > sl_no int,   
    > date string,   
    > agent_name string,   
    > total_chats int,
    > avg_response_time string,   
    > avg_resolution_time string,   
    > avg_rating float,   
    > total_feedback int   
    > )   
    > ROW FORMAT DELIMITED   
    > FIELDS TERMINATED BY ','   
    > TBLPROPERTIES ("skip.header.line.count"="1");   

![Q1](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Q1.JPG?raw=true)

### 2. Dump the data inside HDFS in the given schema location.

#FOR TABLE agentlogingreport

hive> LOAD DATA LOCAL INPATH 'file:///tmp/agentlogingreport.csv' INTO TABLE agentlogingreport;

hive> INSERT OVERWRITE TABLE agentlogingreport   
    > SELECT sl_no, agent_name,   
    > from_unixtime(unix_timestamp(date,'dd-MMM-yy'),'yyyy-MM-dd'),   
    > from_unixtime(unix_timestamp(concat(date,login_time),'dd-MMM-yyHH:mm:ss'),'yyyy-MM-dd HH:mm:ss'),   
    > from_unixtime(unix_timestamp(concat(date,logout_time),'dd-MMM-yyHH:mm:ss'),'yyyy-MM-dd HH:mm:ss'),   
    > from_unixtime(unix_timestamp(concat(date,duration),'dd-MMM-yyHH:mm:ss'),'yyyy-MM-dd HH:mm:ss')   
    > FROM agentlogingreport;

![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Q2_alr.JPG?raw=true)

#FOR TABLE agentperformance

hive> LOAD DATA LOCAL INPATH 'file:///tmp/agentperformance.csv' INTO TABLE agentperformance;

hive> INSERT OVERWRITE TABLE agentperformance   
    > SELECT sl_no,   
    > from_unixtime(unix_timestamp(date,'M/dd/yyyy'),'yyyy-MM-dd'),   
    > agent_name,   
    > total_chats,   
    > from_unixtime(unix_timestamp(concat(date,avg_response_time),'M/dd/yyyyHH:mm:ss'),'yyyy-MM-dd HH:mm:ss'),   
    > from_unixtime(unix_timestamp(concat(date,avg_resolution_time),'M/dd/yyyyHH:mm:ss'),'yyyy-MM-dd HH:mm:ss'),   
    > avg_rating,   
    > total_feedback   
    > FROM agentperformance;   

![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Q2_ap.JPG?raw=true)

### 3. List of all agents names.

hive> SELECT DISTINCT agent_name    
    > FROM agentperformance;

![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Q3.JPG?raw=true)

### 4. Find out average rating of each agents.

hive> SELECT agent_name, ROUND(AVG(avg_rating),2) AS avg_rating    
    > FROM agentperformance    
    > GROUP BY agent_name;

![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Q4.JPG?raw=true)

### 5. Total working days for each agents.

hive> SELECT agent_name,   
    > COUNT(DISTINCT date) AS total_working_days    
    > FROM agentlogingreport    
    > GROUP BY agent_name; 

![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Q5.JPG?raw=true)

### 6. Total query that each agent have taken.

hive> SELECT agent_name, SUM(total_chats) AS total_query   
    > FROM agentperformance   
    > GROUP BY agent_name;  

![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Q6.JPG?raw=true)

### 7. Total Feedback that each agent have received.

hive> SELECT agent_name, SUM(total_feedback) AS total_feedbacks   
    > FROM agentperformance    
    > GROUP BY agent_name;  

![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Q7.JPG?raw=true)

### 8. Agent names who have average rating between 3.5 to 4.

hive> SELECT agent_name, ROUND(AVG(avg_rating),2)   
    > FROM agentperformance    
    > WHERE avg_rating BETWEEN 3.5 AND 4   
    > GROUP BY agent_name;   

![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Q8.JPG?raw=true)

### 9. Agent names who have rating less than 3.5

hive> SELECT agent_name, ROUND(AVG(avg_rating),2)     
    > FROM agentperformance    
    > GROUP BY agent_name    
    > HAVING AVG(avg_rating) < 3.5;   

![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Q9.JPG?raw=truee)

### 10. Agent names who have rating more than 4.5

hive> SELECT agent_name, ROUND(AVG(avg_rating),2)   
    > FROM agentperformance    
    > WHERE avg_rating > 4.5    
    > GROUP BY agent_name;   

![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Q10.JPG?raw=true)

### 11. How many feedbacks agents have received more than 4.5 average

hive> SELECT agent_name, COUNT(avg_rating)    
    > FROM agentperformance      
    > WHERE avg_rating > 4.5    
    > GROUP BY agent_name;

![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Q11.JPG?raw=true)

### 12. Average weekly response time (in minutes) for each agent.

hive> WITH weekresponse AS(    
    > SELECT agent_name, WEEKOFYEAR(date) AS week,   
    > (HOUR(avg_response_time)*3600 + MINUTE(avg_response_time)*60 + SECOND(avg_response_time))/60 AS response2     
    > FROM agentperformance)     
    > SELECT agent_name, week, ROUND(AVG(response2),2) as avg_response_in_minutes    
    > FROM weekresponse     
    > GROUP BY agent_name, week;

![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Q12.JPG?raw=true)

### 13. Average weekly resolution time (in minutes) for each agent.

hive> WITH weeklyresolution AS(   
    > SELECT agent_name, WEEKOFYEAR(date) AS week,   
    > (HOUR(avg_resolution_time)*3600 + MINUTE(avg_resolution_time)*60 + SECOND(avg_resolution_time))/60 AS resolution_time    
    > FROM agentperformance)   
    > SELECT agent_name, week, ROUND(AVG(resolution_time),2) AS avg_resolution_in_minutes   
    > FROM weeklyresolution     
    > GROUP BY agent_name, week;  

![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Q13.JPG?raw=true)

### 14. Find the number of chat on which they have received feedbacks.

hive> SELECT agent_name, SUM(total_chats), total_feedback    
    > FROM agentperformance    
    > WHERE total_feedback!=0    
    > GROUP BY agent_name;  

![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Q14.JPG?raw=true)

### 15. Total contribution hour for each agent on weekly basis

hive> WITH weeklycontribution AS(   
    > SELECT agent_name, WEEKOFYEAR(date) AS WEEK,      
    > (HOUR(duration)*3600 + MINUTE(duration)*60 + SECOND(duration))/3600 AS hours    
    > FROM agentlogingreport)    
    > SELECT agent_name, week, ROUND(SUM(hours),2) AS contribution_hours    
    > FROM weeklycontribution    
    > GROUP BY agent_name, week;   

![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Q15.JPG?raw=true)

### 16. Perform inner join, left join and right join based on the agent column and after joining the table, export that data into your local system.

#### INNER JOIN

hive> SELECT alr.sl_no, alr.agent_name, alr.date, alr.duration, ap.total_chats, ap.avg_rating, ap.total_feedback   
    > FROM agentlogingreport alr   
    > JOIN    
    > agentperformance ap   
    > ON alr.agent_name = ap.agent_name    
    > LIMIT 30;
    
![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Q16%20INNER.JPG?raw=true)

#### Exporting the inner join data as a CSV file into the local system.

[cloudera@quickstart ~]$ hive -e 'SELECT alr.sl_no, alr.agent_name, alr.date, alr.duration, ap.total_chats, ap.avg_rating, ap.total_feedback 
FROM projects.agentlogingreport alr JOIN projects.agentperformance ap ON alr.agent_name = ap.agent_name' >/home/cloudera/projects/inner_join.csv;

![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Q16%20INNER%20Export.JPG?raw=true)

#### LEFT JOIN

hive> SELECT alr.sl_no, alr.agent_name, alr.date, alr.duration, ap.total_chats, ap.avg_rating, ap.total_feedback    
    > FROM agentlogingreport alr   
    > LEFT JOIN   
    > agentperformance ap   
    > ON alr.agent_name = ap.agent_name   
    > LIMIT 30;

![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Q16%20LEFT%20JOIN.JPG?raw=true)

#### Exporting the left join data as a CSV file into the local system.

[cloudera@quickstart ~]$ hive -e 'SELECT alr.sl_no, alr.agent_name, alr.date, alr.duration, ap.total_chats, ap.avg_rating, ap.total_feedback 
FROM projects.agentlogingreport alr LEFT JOIN projects.agentperformance ap ON alr.agent_name = ap.agent_name' >/home/cloudera/projects/left_join.csv;

![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Q16%20LEFT%20JOIN%20Export.JPG?raw=true)

#### RIGHT JOIN

SELECT alr.sl_no, alr.agent_name, alr.date, alr.duration, ap.total_chats, ap.avg_rating, ap.total_feedback    
FROM agentlogingreport alr   
RIGHT JOIN   
agentperformance ap    
ON alr.agent_name = ap.agent_name    
LIMIT 30;

![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Q16%20RIGHT%20JOIN.JPG?raw=true)

#### Exporting the right join data as a CSV file into the local system.

[cloudera@quickstart ~]$ hive -e 'SELECT alr.sl_no, alr.agent_name, alr.date, alr.duration, ap.total_chats, ap.avg_rating, ap.total_feedback 
FROM projects.agentlogingreport alr RIGHT JOIN projects.agentperformance ap ON alr.agent_name = ap.agent_name' >/home/cloudera/projects/right_join.csv

![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Q16%20RIGHT%20JOIN%20Export.JPG?raw=true)

### 17. Perform partitioning on top of the agent column and then on top of that perform bucketing for each partitioning.

hive> CREATE TABLE alr_partition_bucket   
    > (   
    > sl_no int,    
    > date string,   
    > login_time string,   
    > logout_time string,   
    > duration string   
    > )PARTITIONED BY(agent_name string)   
    > CLUSTERED BY (date)   
    > SORTED BY (date) INTO 4 BUCKETS   
    > ROW FORMAT DELIMITED   
    > FIELDS TERMINATED BY ',';

hive> SET hive.exec.dynamic.partition = true;   
hive> SET hive.exec.dynamic.partition.mode = nonstrict;

hive> INSERT INTO TABLE alr_partition_bucket PARTITION(agent_name)    
    > SELECT sl_no,date,login_time,logout_time,duration,agent_name FROM agentlogingreport;

![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Partiioned_bucketing_table.JPG?raw=true)
![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/Partiioned_bucketing_table2.JPG?raw=true)

FOR THE TABLE agentperformance,

hive> CREATE TABLE ap_partition_bucket   
    > (   
    > sl_no int,   
    > date string,   
    > total_chats int,   
    > avg_response_time string,    
    > avg_resolution_time string,   
    > avg_rating float,   
    > total_feedback int   
    > )PARTITIONED BY (agent_name string)   
    > CLUSTERED BY (date)   
    > SORTED BY(date) INTO 8 BUCKETS    
    > ROW FORMAT DELIMITED   
    > FIELDS TERMINATED BY ',';

hive> INSERT INTO TABLE ap_partition_bucket PARTITION(agent_name)   
    > SELECT sl_no, date, total_chats, avg_response_time, avg_resolution_time, avg_rating, total_feedback, agent_name FROM agentperformance;
    
![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/ap_partiioned_bucketing_table.JPG?raw=true)
![Q2](https://github.com/saheen619/Hive-Mini_Project_1/blob/main/Screenshots/ap_partiioned_bucketing_table2.JPG?raw=true)

