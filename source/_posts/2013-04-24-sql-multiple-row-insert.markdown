---
layout: post
title: "SQL：一次性插入多个元组"
date: 2013-04-24 19:21
comments: true
categories: [SQL,DB] 
---
书本上没有，在 StackOverflow 找到了，做一下笔记    

**句式一：**                      
               
```sql
INSERT INTO 'tablename' ('column1', 'column2') VALUES         
('data1', 'data2'),             
('data3', 'data4'),                  
('data5', 'data6'),               
('data7', 'data8')
```                        
**句式二：**                    
            
```sql
INSERT INTO 'tablename'          
	    SELECT 'data1' AS 'column1', 'data2' AS 'column2'      
UNION SELECT 'data3', 'data4'      
UNION SELECT 'data5', 'data6'          
UNION SELECT 'data7', 'data8'
```        
##参考资料              
+ [Is it possible to insert multiple rows at a time in an SQLite database?](http://stackoverflow.com/questions/1609637/is-it-possible-to-insert-multiple-rows-at-a-time-in-an-sqlite-database)                     
                   
