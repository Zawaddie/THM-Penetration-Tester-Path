# SQL Injection
Learnt how to detect and exploit SQL Injection vulnerabilities

---

## Introduction

 SQLi, is an attack on a web application database server that causes malicious queries to be executed. 
 When a web application communicates with a database using input from a user that hasn't been properly validated, 
 there runs the potential of an attacker being able to steal, 
 delete or alter private and customer data and also attack the web application authentication methods to private or customer areas.

---

## What is a Database?

A database is a way of electronically storing collections of data in an organised manner. 
A database is controlled by a DBMS (Database Management System). 
DBMSs fall into two camps: 
1. Non-Relational
   - MongoDB,
   - Cassandra
   - ElasticSearch.
3. Relational databases:
   - MySQL,
   - Microsoft SQL Server,
   - Access,
   - PostgreSQL
   - SQLite
Within a DBMS, you can have multiple databases, each containing its own set of related data

<img width="973" height="449" alt="image" src="https://github.com/user-attachments/assets/2dbf3e19-48c8-42b6-9972-bcd87bff47b9" />

A database is made of tables and tables of columns and rows.


### Relational Vs Non-Relational Databases:

**A relational database**: stores information in tables, and often, the tables share information between them; they use columns to specify and define the data being stored and rows actually to store the data. 
The tables will often contain a column that has a unique ID (primary key), which will then be used in other tables to reference it and cause a relationship between the tables, hence the name relational database.

**Non-relational databases**: sometimes (NoSQL) are any sort of database that doesn't use tables, columns and rows to store the data. 
A specific database layout doesn't need to be constructed so each row of data can contain different information, giving more flexibility over a relational database. 

---

## What is SQL?

SQL (Structured Query Language) is a feature-rich language used for querying databases. 

These SQL queries are better referred to as statements.

---

## What is SQLi?

The point wherein a web application using SQL can turn into SQL Injection is when user-provided data gets included in the SQL query.

What it look like:
Take the following scenario where you've come across an online blog, and each blog entry has a unique ID number. 
The blog entries may be either set to public or private, depending on whether they're ready for public release. 

The URL for each blog entry may look something like this:

```
https://website.thm/blog?id=1
```

From the URL, the blog entry selected comes from the id parameter in the query string. 
The web application needs to retrieve the article from the database and may use an SQL statement that looks something like:

```
SELECT * from blog where id=1 and private=0 LIMIT 1;
```

Remember, SQL Injection is introduced when user input is introduced into the database query. 
In this instance, the id parameter from the query string is used directly in the SQL query.

Now. if article ID 2 is still locked as private, so it cannot be viewed on the website. We could now instead call the URL:
 
```
https://website.thm/blog?id=2;--
```

Which would then, in turn, produce the SQL statement:


```
SELECT * from blog where id=2;-- and private=0 LIMIT 1;
```

The semicolon in the URL signifies the end of the SQL statement, and the two dashes cause everything afterwards to be treated as a comment. 
By doing this, we are running the query:

```
SELECT * from blog where id=2;--
```

Which will return the article with an ID of 2 whether it is set to public or not.

This was just one example of an SQL Injection vulnerability of a type called In-Band SQL Injection; 

Three types in of SQLi: 
- In-Band
- Blind 
- Out-of-Band

---

## In-Band SQL Injection

It's the easiest type to detect and exploit.
In-Band just refers to the same method of communication being used to exploit the vulnerability and also receive the results, for example, discovering an SQL Injection vulnerability on a website page and then being able to extract data from the database to the same page.

 

### Error-Based SQL Injection

This type of SQL Injection is the most useful for easily obtaining information about the database structure, as error messages from the database are printed directly to the browser screen. This can often be used to enumerate a whole database. 

 

### Union-Based SQL Injection
This type of Injection utilises the SQL UNION operator alongside a SELECT statement to return additional results to the page. This method is the most common way of extracting large amounts of data via an SQL Injection vulnerability.

 

### Practical:

Level one of the practice lab contains a mock browser and website featuring a blog with different articles, which can be accessed by changing the id number in the query string.

The key to discovering error-based SQL Injection is to break the code's SQL query by trying certain characters until an error message is produced; these are most commonly single apostrophes ( ' ) or a quotation mark ( " ).

 
Typing an apostrophe ( ' ) after the id=1 and pressing enter, returns an SQL error informing us of an error in the syntax. 
The fact that we've received the error message confirms the existence of an SQL Injection vulnerability. 

<img width="668" height="400" alt="image" src="https://github.com/user-attachments/assets/8ec82f0e-18c6-455b-b319-a35c0021481e" />

We can now exploit this vulnerability and use the error messages to learn more about the database structure. 

 

The first thing we need to do is return data to the browser without displaying an error message: 
Firstly, we'll try the UNION operator so we can receive an extra result if we choose it by setting the mock browsers id parameter to:

```
1 UNION SELECT 1
```

<img width="666" height="380" alt="image" src="https://github.com/user-attachments/assets/81d5859e-2bc5-495d-9488-77a6b92711fe" />
 

This statement  produces an error message informing us that the UNION SELECT statement has a different number of columns than the original SELECT query. 

We thus try again but add another column:

 ```
1 UNION SELECT 1,2
```
 
<img width="645" height="351" alt="image" src="https://github.com/user-attachments/assets/399fb364-64d7-4bf7-8cd8-20fceea8cc80" />

Same error again, so let's repeat by adding another column:

```
1 UNION SELECT 1,2,3
```

 <img width="666" height="226" alt="image" src="https://github.com/user-attachments/assets/242a18ad-5567-4f7b-aa43-df82794feeb2" />



Success, the error message has gone, and the article is being displayed, but now we want to display our data instead of the article. 

The article is displayed because it takes the first returned result somewhere in the website's code and shows that. To get around that, we need the first query to produce no results. This can simply be done by changing the article ID from 1 to 0.
 
```
0 UNION SELECT 1,2,3
```

 <img width="663" height="197" alt="image" src="https://github.com/user-attachments/assets/5c5e27eb-0d5a-475e-a6ca-050b2afcdbea" />


We see the article is just made up of the result from the UNION select, returning the column values 1, 2, and 3. We can start using these returned values to retrieve more useful information. 

First, we'll get the database name that we have access to:

 ```
0 UNION SELECT 1,2,database()
```
 

We now see where the number 3 was previously displayed; it now shows the name of the database; sqli_one.

<img width="665" height="186" alt="image" src="https://github.com/user-attachments/assets/bf50797b-48f5-4627-9d99-ccfbb60a7f7a" />
 

Our next query will gather a list of tables that are in this database.

```
0 UNION SELECT 1,2,group_concat(table_name) FROM information_schema.tables WHERE table_schema = 'sqli_one'
```

 <img width="655" height="184" alt="image" src="https://github.com/user-attachments/assets/c50338c9-6e34-4886-93ea-8a15ae98dfb0" />


There are a couple of new things to learn in this query: 
- Firstly, the method ```group_concat()``` gets the specified column (in our case, table_name) from multiple returned rows and puts it into one string separated by commas.
- The next thing is the ```information_schema``` database; every user of the database has access to this, and it contains information about all the databases and tables the user has access to. In this particular query, we're interested in listing all the tables in the sqli_one database, which is article and staff_users. 

 

Level one of the practical aims to discover Martin's password, the staff_users table is what interests us. 
We thus  utilise the ```information_schema``` database again to find the structure of this table using the below query.

```
0 UNION SELECT 1,2,group_concat(column_name) FROM information_schema.columns WHERE table_name = 'staff_users'
```
 <img width="651" height="182" alt="image" src="https://github.com/user-attachments/assets/da5a8377-91ba-4d0f-87ae-eb3735f2d068" />


This is similar to the previous SQL query. However, the information we want to retrieve has changed from table_name to column_name, the table we are querying in the information_schema database has changed from tables to columns, and we're searching for any rows where the table_name column has a value of staff_users.

 
The query results provide three columns for the staff_users table: id, password, and username.
We can use the username and password columns for our following query to retrieve the user's information.

 ```
0 UNION SELECT 1,2,group_concat(username,':',password SEPARATOR '<br>') FROM staff_users
```

 <img width="656" height="179" alt="image" src="https://github.com/user-attachments/assets/c33e9424-1bf7-48c0-9bc7-3804cc5f6731" />


Again, we use the ```group_concat`` method to return all of the rows into one string and make it easier to read. We've also added ,':', to split the username and password from each other. 
Instead of being separated by a comma, we've chosen the HTML <br> tag that forces each result to be on a separate line to make for easier reading.


We now have Martin's password to enter and move to the next level: martin:pa$$word


---

## Blind SQLi

Unlike In-Band SQL injection, where we can see the results of our attack directly on the screen, blind SQLi is when we get little to no feedback to confirm whether our injected queries were, in fact, successful or not, this is because the error messages have been disabled, but the injection still works regardless. 

All we need is that little bit of feedback to successfully enumerate a whole database.



### Blind SQLi-Authentication Bypass

One of the most straightforward Blind SQL Injection techniques is when bypassing authentication methods such as login forms. In this instance, we aren't that interested in retrieving data from the database; We just want to get past the login. 


Login forms that are connected to a database of users are often developed in such a way that the web application isn't interested in the content of the username and password but more in whether the two make a matching pair in the users table. In basic terms, the web application is asking the database, "Do you have a user with the username bob and the password bob123?" the database replies with either yes or no (true/false) and, depending on that answer, dictates whether the web application lets you proceed or not. 

Thus, it's unnecessary to enumerate a valid username/password pair. We just need to create a database query that replies with a yes/true.



### Practical:

Level Two of the SQL Injection examples shows Blind SQLi-Authentication Bypass. 

We can see in the box labelled "SQL Query" that the query to the database is the following:

```
select * from users where username='%username%' and password='%password%' LIMIT 1;
```

<img width="653" height="361" alt="image" src="https://github.com/user-attachments/assets/1ff36cbc-d0ee-483f-880c-40754b11d8c0" />


N.B The %username% and %password% values are taken from the login form fields. The initial values in the SQL Query box will be blank as these fields are currently empty.



To make this into a query that always returns as true, we can enter the following into the password field:


```
' OR 1=1;--
```


Which turns the SQL query into the following:

```
select * from users where username='' and password='' OR 1=1;
```


Because ```1=1`` is a true statement and we've used an OR operator, this will always cause the query to return as true, which satisfies the web applications logic that the database found a valid username/password combination and that access should be allowed.

<img width="670" height="323" alt="image" src="https://github.com/user-attachments/assets/08de2636-3a4d-4221-9781-3cb58c52964c" />


---

### Bling SQLi -Boolean Based

Boolean-based SQL Injection refers to the response we receive from our injection attempts, which could be a true/false, yes/no, on/off, 1/0 or any response that can only have two outcomes. That outcome confirms that our SQL Injection payload was either successful or not. On the first inspection, you may feel like this limited response can't provide much information. Still, with just these two responses, it's possible to enumerate a whole database structure and contents.



Practical:

On level three of the SQL Injection Examples Machine, you're presented with a mock browser with the following URL:


```
https://website.thm/checkuser?username=admin
```

<img width="666" height="370" alt="image" src="https://github.com/user-attachments/assets/058ce54e-68dd-4f28-a4ed-326392ccb6d4" />

The browser body contains  {"taken":true}. This API endpoint replicates a common feature found on many signup forms, which checks whether a username has already been registered to prompt the user to choose a different username. Because the taken value is set to true, we can assume the username admin is already registered. We can confirm this by changing the username in the mock browser's address bar from admin to admin123, and upon pressing enter, you'll see the value taken has now changed to false.



The SQL query that is processed looks like the following:



```
select * from users where username = '%username%' LIMIT 1;
```


The only input we have control over is the username in the query string, and we'll have to use this to perform our SQL injection. Keeping the username as admin123, we can start appending to this to try and make the database confirm true things, changing the state of the taken field from false to true.



Like in previous levels, our first task is to establish the number of columns in the users' table, which we can achieve by using the UNION statement. Change the username value to the following:


```
admin123' UNION SELECT 1;-- 
```

<img width="668" height="348" alt="image" src="https://github.com/user-attachments/assets/89d5e091-b498-497c-bd3e-c84da6a8f37a" />

As the web application has responded with the value taken as false, we can confirm this is the incorrect value of columns. Keep on adding more columns until we have a taken value of true. You can confirm that the answer is three columns by setting the username to the below value:


```
admin123' UNION SELECT 1,2,3;-- 
```
<img width="660" height="339" alt="image" src="https://github.com/user-attachments/assets/0ad268cb-7679-4762-8f92-b7b56794d1a8" />


Now that our number of columns has been established, we can work on the enumeration of the database. Our first task is to discover the database name. We can do this by using the built-in database() method and then using the like operator to try and find results that will return a true status.

Try the below username value and see what happens:


```
admin123' UNION SELECT 1,2,3 where database() like '%';--
```


We get a true response because, in the like operator, we just have the value of %, which will match anything as it's the wildcard value. If we change the wildcard operator to a%, you'll see the response goes back to false, which confirms that the database name does not begin with the letter a. We can cycle through all the letters, numbers and characters such as - and _ until we discover a match. If you send the below as the username value, you'll receive a true response that confirms the database name begins with the letter s.


```
admin123' UNION SELECT 1,2,3 where database() like 's%';--
```


Now you move on to the next character of the database name until you find another true response, for example, 'sa%', 'sb%', 'sc%', etc. Keep on with this process until you discover all the characters of the database name, which is sqli_three.



We've established the database name, which we can now use to enumerate table names using a similar method by utilising the information_schema database. Try setting the username to the following value:


```
admin123' UNION SELECT 1,2,3 FROM information_schema.tables WHERE table_schema = 'sqli_three' and table_name like 'a%';--
```


This query looks for results in the information_schema database in the tables table where the database name matches sqli_three, and the table name begins with the letter a. As the above query results in a false response, we can confirm that there are no tables in the sqli_three database that begin with the letter a. Like previously, you'll need to cycle through letters, numbers and characters until you find a positive match.



You'll finally end up discovering a table in the sqli_three database named users, which you can confirm by running the following username payload:


```
admin123' UNION SELECT 1,2,3 FROM information_schema.tables WHERE table_schema = 'sqli_three' and table_name='users';--
```


Lastly, we now need to enumerate the column names in the users table so we can properly search it for login credentials. Again, we can use the information_schema database and the information we've already gained to query it for column names. Using the payload below, we search the columns table where the database is equal to sqli_three, the table name is users, and the column name begins with the letter a.

```
admin123' UNION SELECT 1,2,3 FROM information_schema.COLUMNS WHERE TABLE_SCHEMA='sqli_three' and TABLE_NAME='users' and COLUMN_NAME like 'a%';
```


Again,  you'll need to cycle through letters, numbers and characters until you find a match. As you're looking for multiple results, you'll have to add this to your payload each time you find a new column name to avoid discovering the same one. For example, once you've found the column named id, you'll append that to your original payload (as seen below).


```
admin123' UNION SELECT 1,2,3 FROM information_schema.COLUMNS WHERE TABLE_SCHEMA='sqli_three' and TABLE_NAME='users' and COLUMN_NAME like 'a%' and COLUMN_NAME !='id';
```


Repeating this process three times will enable you to discover the columns' id, username and password. Which now you can use to query the users table for login credentials. First, you'll need to discover a valid username, which you can use the payload below:


```
admin123' UNION SELECT 1,2,3 from users where username like 'a%
```


Once you've cycled through all the characters, you will confirm the existence of the username admin. Now you've got the username. You can concentrate on discovering the password. The payload below shows you how to find the password:


```
admin123' UNION SELECT 1,2,3 from users where username='admin' and password like 'a%
```


Cycling through all the characters, you'll discover the password is 3845.



You can now use the username and password you've enumerated through the blind SQL Injection vulnerability on the login form to access the next level.

---

### Blind SQLi-Time-Based



A time-based blind SQL injection is very similar to the above boolean-based one in that the same requests are sent, but there is no visual indicator of your queries being wrong or right this time. Instead, your indicator of a correct query is based on the time the query takes to complete. This time delay is introduced using built-in methods such as SLEEP(x) alongside the UNION statement. The SLEEP() method will only ever get executed upon a successful UNION SELECT statement. 



So, for example, when trying to establish the number of columns in a table, you would use the following query:


```
admin123' UNION SELECT SLEEP(5);--
```


If there was no pause in the response time, we know that the query was unsuccessful, so like on previous tasks, we add another column:


```
admin123' UNION SELECT SLEEP(5),2;--
```


This payload should have produced a 5-second delay, confirming the successful execution of the UNION statement and that there are two columns.



You can now repeat the enumeration process from the Boolean-based SQL injection, adding the SLEEP() method to the UNION SELECT statement.
If you're struggling to find the table name, the below query should help you on your way:

```
referrer=admin123' UNION SELECT SLEEP(5),2 where database() like 'u%';--
```


---

## Out-Of-Band SQLi

Out-of-band SQL Injection isn't as common as it either depends on specific features being enabled on the database server or the web application's business logic, which makes some kind of external network call based on the results from an SQL query.

An Out-Of-Band attack is classified by having two different communication channels, one to launch the attack and the other to gather the results. For example, the attack channel could be a web request, and the data gathering channel could be monitoring HTTP/DNS requests made to a service you control.

1) An attacker makes a request to a website vulnerable to SQL Injection with an injection payload.

2) The Website makes an SQL query to the database, which also passes the hacker's payload.

3) The payload contains a request which forces an HTTP request back to the hacker's machine containing data from the database.

<img width="378" height="367" alt="image" src="https://github.com/user-attachments/assets/a3c98d9f-7fba-4ee5-9c40-c4e3dba31fc9" />

---

## Remediation

As impactful as SQL Injection vulnerabilities are, developers do have a way to protect their web applications from them by following the advice below:



1. Prepared Statements (With Parameterized Queries):

In a prepared query, the first thing a developer writes is the SQL query, and then any user inputs are added as parameters afterwards. Writing prepared statements ensures the SQL code structure doesn't change and the database can distinguish between the query and the data. As a benefit, it also makes your code look much cleaner and easier to read.



2. Input Validation:

Input validation can go a long way to protecting what gets put into an SQL query. Employing an allow list can restrict input to only certain strings, or a string replacement method in the programming language can filter the characters you wish to allow or disallow. 



3. Escaping User Input:

Allowing user input containing characters such as ' " $ \ can cause SQL Queries to break or, even worse, as we've learnt, open them up for injection attacks. Escaping user input is the method of prepending a backslash (\) to these characters, which then causes them to be parsed just as a regular string and not a special character.


----
