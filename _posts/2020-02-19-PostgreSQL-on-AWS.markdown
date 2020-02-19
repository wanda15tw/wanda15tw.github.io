---
layout: post
title:  "PostgreSQL on AWS and get started"
date:   2020-02-19 21:20:00 +0530
categories: DataScience SQL AWS
---


###### tags: `note`, `tutorial`, `SQL`

# PostgreSQL on AWS and get started

This is a note/tutorial about (1) setting up a Postgre DB on AWS, (2) connecting to the DB using  **pgAdmin**, and (3) interaction with the DB using **Python sqlalchemy**.

## Create a PostgreSQL on AWS
* After logging into the AWS console, look for or search for **RDS**, and in the **Databases** page select **Create database**.
* Choose **Standard Create** and **PostgreSQL** as shown below.
![](https://i.imgur.com/J4uBW1R.png)
* Select **Free tier** in template session if you will.
* Specify **DB instance identifier** (unique cross your RDS DB instances) and master user's credential as shown below.
![](https://i.imgur.com/AVUDJdR.png)

* You can keep most of settings default except for making sure that **Publicly accessible** is enabled so that client from any IP addresses can access. 
![](https://i.imgur.com/YQFuFPl.png)

* Wait until the instance is provisioned and up and running. Then click the DB identifier to enter its detailed page, and record **Endpoint & port**, which along with the master user's credentials are connection information for clients to access.
![](https://i.imgur.com/ylGWIVJ.png)




## PostgreSQL Client - pgAdmin
I recommend **pgAdmin** as a free, light-weight DB client to PostgreSQL. You can download it from its [website](https://www.pgadmin.org/download/)

* Right click **server** to **create server**
* Under **General**, specify DB name
* Under **Connection**, enter **Host name/address & Port** with **Endpoint & Port** from AWS and **master user's credentials** to **Username/Password**. As shown below.
![](https://i.imgur.com/k2C3LCY.png)

* Once connection is successful. You may test it by creating a table.
* Expand the browser to **schemas > public > Tables**, and right click to **create a table**
![](https://i.imgur.com/QWIN9KR.png)
* Once the table is created, you can right click the tablename to **View/Edit Data**.
![](https://i.imgur.com/5aLEZVY.png)


## Access PostgreSQL with Python
As most of my analytics work is in Python and PostgreSQL is intended to be a staging location for my data, especially scrapped from the internet.

Just as we do with any SQL client, we need connection information comprised with **Endpoint, Port, Username and Password** to empower our Python program as well.

```python=
import sqlalchemy as sq

engine = sq.create_engine('postgresql://'+username+':'+password+'@'+endpoint+':'+port+'/postgres')
engine.execute("SELECT * FROM demo").fetchall()
```

If I have a table to store in the DB and it is already in Pandas DataFrame format, I can simply use `to_sql` and `read_sql` method to store and load.
```python=
# export reviews to DB as 'reviews', replace if already exists
reviews.to_sql('reviews', engine, if_exists='replace') #append

# import queried results from DB to a DataFrame
sql = 'select * from reviews'
df = pd.read_sql(engine, sql)
```