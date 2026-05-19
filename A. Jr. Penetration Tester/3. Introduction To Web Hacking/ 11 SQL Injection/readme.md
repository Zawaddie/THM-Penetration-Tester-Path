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

In-Band SQL Injection is the easiest type to detect and exploit; In-Band just refers to the same method of communication being used to exploit the vulnerability and also receive the results, for example, discovering an SQL Injection vulnerability on a website page and then being able to extract data from the database to the same page.

 

Error-Based SQL Injection
This type of SQL Injection is the most useful for easily obtaining information about the database structure, as error messages from the database are printed directly to the browser screen. This can often be used to enumerate a whole database. 

 

Union-Based SQL Injection
This type of Injection utilises the SQL UNION operator alongside a SELECT statement to return additional results to the page. This method is the most common way of extracting large amounts of data via an SQL Injection vulnerability.

 

Practical:
Click the green "Start Machine" button to use the SQL Injection Example practice lab. Each level contains a mock browser and also SQL Query and Error boxes to assist in getting your queries/payload correct. You may need to refresh this web page once the machine starts if you get a 502 Bad Gateway error.


Level one of the practice lab contains a mock browser and website featuring a blog with different articles, which can be accessed by changing the id number in the query string.

 

The key to discovering error-based SQL Injection is to break the code's SQL query by trying certain characters until an error message is produced; these are most commonly single apostrophes ( ' ) or a quotation mark ( " ).

 

Try typing an apostrophe ( ' ) after the id=1 and press enter. And you'll see this returns an SQL error informing you of an error in your syntax. The fact that you've received this error message confirms the existence of an SQL Injection vulnerability. We can now exploit this vulnerability and use the error messages to learn more about the database structure. 

 

The first thing we need to do is return data to the browser without displaying an error message. Firstly, we'll try the UNION operator so we can receive an extra result if we choose it. Try setting the mock browsers id parameter to:

 

1 UNION SELECT 1

 

This statement should produce an error message informing you that the UNION SELECT statement has a different number of columns than the original SELECT query. So let's try again but add another column:

 

1 UNION SELECT 1,2

 

Same error again, so let's repeat by adding another column:

 

1 UNION SELECT 1,2,3

 

Success, the error message has gone, and the article is being displayed, but now we want to display our data instead of the article. The article is displayed because it takes the first returned result somewhere in the website's code and shows that. To get around that, we need the first query to produce no results. This can simply be done by changing the article ID from 1 to 0.

 

0 UNION SELECT 1,2,3

 

You'll now see the article is just made up of the result from the UNION select, returning the column values 1, 2, and 3. We can start using these returned values to retrieve more useful information. First, we'll get the database name that we have access to:

 

0 UNION SELECT 1,2,database()

 

You'll now see where the number 3 was previously displayed; it now shows the name of the database, which is sqli_one.

 

Our next query will gather a list of tables that are in this database.

 

0 UNION SELECT 1,2,group_concat(table_name) FROM information_schema.tables WHERE table_schema = 'sqli_one'

 

There are a couple of new things to learn in this query. Firstly, the method group_concat() gets the specified column (in our case, table_name) from multiple returned rows and puts it into one string separated by commas. The next thing is the information_schema database; every user of the database has access to this, and it contains information about all the databases and tables the user has access to. In this particular query, we're interested in listing all the tables in the sqli_one database, which is article and staff_users. 

 

As the first level aims to discover Martin's password, the staff_users table is what interests us. We can utilise the information_schema database again to find the structure of this table using the below query.

 

0 UNION SELECT 1,2,group_concat(column_name) FROM information_schema.columns WHERE table_name = 'staff_users'

 

This is similar to the previous SQL query. However, the information we want to retrieve has changed from table_name to column_name, the table we are querying in the information_schema database has changed from tables to columns, and we're searching for any rows where the table_name column has a value of staff_users.

 

The query results provide three columns for the staff_users table: id, password, and username. We can use the username and password columns for our following query to retrieve the user's information.

 

0 UNION SELECT 1,2,group_concat(username,':',password SEPARATOR '<br>') FROM staff_users

 

Again, we use the group_concat method to return all of the rows into one string and make it easier to read. We've also added ,':', to split the username and password from each other. Instead of being separated by a comma, we've chosen the HTML <br> tag that forces each result to be on a separate line to make for easier reading.

 

You should now have access to Martin's password to enter and move to the next level.
