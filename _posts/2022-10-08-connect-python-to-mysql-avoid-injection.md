---
title: Connecting Python to MySQL and tipps to avoid SQL injection attacks
categories: [python, mysql, database]
tags: [python, databases, mysql, injection attack, security]
---

To quick-connect Python program with a MySQL, one needs to consider following:

* MySQL driver needs to be installed,
* connection object needs to be created,
* cursor object needs to be created,
* the query should be executed.

Of course, Python can connect to most databases, yet it seems that the most-preffered database is MySQL, for the reason of being powerful, free and open source, just like Python is.

## Steps to follow

MySQL driver connects MySQL database/server and Python program. It is installed either during MySQL install process, or separately by using PIP:

```
python -m pip install mysql-connector-python 
```

In any case, to check whether the MySQL is installed or not, following is typed into Python prompt (no error after executing means it is working properly, otherwise re-run the installation):

```python
import mysql.connector  
```

In order to link the database and the Python program, the mysql.connectir provides the <B>connect()</B> method:

```
Connection_object= mysql.connector.connect(host = <hostname>, user = <username>, passwd = <password>)    
```
* <b>Hostname</b>: localhost or IP address
* <b>Username</b>: name of the user that can access the database
* <b>Password</b>: password that has been associated with the particular user
* <b>Database</b>: name of the database to connect to

Purpose of the connection object is to provide multiple working environments using same connection to the database:

```python
import mysql.connector  
  
db = mysql.connector.connect(host="localhost", user="root", passwd="admin123", database="testdatabase")
```

The <B>cursor()</B> function creates the cursor object which then further uses methods like <b>execute</b> and <b>rollback</b>. Inside those methods the SQL queries are run:

```python
cursor=db.cursor()
cursor.execute("CREATE TABLE Food (name VARCHAR(50), price int UNSIGNED, foodID PRIMARY KEY AUTO_INCREMENT)")
cursor.execute("DESCRIBE Food)
```
Display the data by looping through the cursor object:

```python
for x in cursor:
    print(x)
```
This is pretty straightforward and the syntax is similar for other databases. However when writing SQL code one should be aware of SQL injection attacks which are very common as they use SQL code to manipulate databases in an unwanted way. Usually, attacker has the access to the program's code or part of it, but it can't access the database directly, so it is trying to gain unwaranted access through the program's (in this case Python's code).

## Preventing SQL injection attacks

Although this is a broad topic, the aim of this article is to show how to connect to a database from Python and give a few important tips that can prevent SQL attacks. Sanitization is a common termn meaning typing such a code that prevents itself from being exploited. For instance, using <b>execute()</b> method with <b>%s</b> in place of each variable while passing the value by the list or tuple as the second parameter of <b>execute()</b>:

```python
cursor=db.cursor()
max_price=[10]
cursor.execute(""" SELECT flour FROM Food WHERE price <%s """, (max_price))
```
This would be insecure:

```python
cursor=db.cursor()
cursor.execute("SELECT flour FROM Food WHERE price <10 ")
```
Because the variable is encoded separately in a list or tuple, it can't be changed by manipulating SQL code, and the characters are excaped properly, using triple quotes (""") as well as the comma character instead a %. Of course, there are a lot of other ways to prevent SQL injections but maybe the best ones are done when writing the code.
