---
layout: post
title: SQL and Jupyter Notebooks
---

When you have data stored in a relational database, being able carry out SQL queries inside Jupyter notebooks comes in handy. For example, you can use Jupyter to import data from a relational database and use Python libraries to carry out data manipulation, create visualisations and build machine learning models. At other times, this can save you the trouble of downloading and installing a separate IDE for a particular relational DBMS.

There are several ways to query a SQL database with Jupyter notebooks. Two popular approaches are: 

### 1. Python SQL Libraries
Python SQL libraries exist for all popular DBMS such as SQLite (`sqlite3`), MySQL (`pymysql`), PostgreSQL (`psycopg2`), etc.  
You can use these Python libraries to *connect* with the database, *create* tables, *insert* records, *query* data, *delete* and *update* tables. [This blog post](https://realpython.com/python-sql-libraries/) gives a very good introduction to carrying out these tasks with Python. 

However, the output of your SQL queries will be a plain list of tuples. You can convert the query results into a Pandas dataframe through the Pandas `read_sql_query` function as shown [here](https://www.dataquest.io/blog/python-pandas-databases/). This allows you to carry out data manipulation using Pandas built-in functions, visualize data using the `matplotlib` library, and build machine learning models using the `scikit-learn` or `tensorflow` libraries. 


### 2. Jupyter Magic Functions

Using `ipython-sql` magic functions in Jupyter notebooks has two main benefits. First, code syntax for carrying out SQL queries using the `ipython-sql` magic functions is much more straightforward concise. Second, it returns the query results as a well-formatted table with headers. Moreover, the query results can be easily converted into Pandas dataframe. Here is a simple example:

```python
studentNames = %sql SELECT name FROM students
studentNames_df = studentNames.DataFrame()
```

You can install the `ipython-sql` library by running the following code in your jupyter notebook: 
```python
!pip install ipython-sql
```
[Here](https://github.com/bilalmkhan/Practice-SQL-with-SQLite-and-Jupyter-Notebook) is a comprehensive tutorial on how to use the `ipython-sql` magic functions in Jupyter.








