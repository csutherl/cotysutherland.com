+++
author = "Coty Sutherland"
categories = ["Software Things"]
date = 2014-02-19T19:35:00Z
description = ""
draft = false
slug = "data-virtualization-with-jboss"
title = "Data Virtualization with JBoss"

+++


### Purpose

I am writing this post to document some of the issues that I encountered while working with JBoss Data Virtualization 6 (Beta). It has been a very interesting week working on this, but I feel that I have a good understanding of all of the pieces and parts in play here. I have developed a few virtual databases with various sources, so I will walk through one of those now to cover some of the high points.

### My Environment

* My laptop (Lenovo)
* JBoss Data Virtualization 6 Beta (Teiid Engine 8.4.1)
* JBoss Developer Studio 7 with Teiid Designer tools

### What is a Virtual Database?

A Virtual Database is comprised of one or more models created by a user. There are two types of models that you can create and source with a Virtual Database, Source models and View models. Source models are created directly on top of some data source that you want to model. For example, a Source model would be metadata describing a table in a database. A View model is very similar to a Source model but has one big difference; with a View model you can transform the data that you are displaying. You can also join Sources, or other Views, together and present them as a single View model. All of these models are just metadata that exist on the Teiid server so that it knows how to interpret queries that we give it. Moving on…

### What do I currently have working?

I am using JBDV to join some baseball stats that I currently have in various forms. If you’d like, you can get the CSVs from [here](http://seanlahman.com/files/database/lahman2012-csv.zip). The View that I created pulled data from three tables in the Virtual Database (VDB named testing). In order to get the data into the view, I wrote some ETL with Pentaho Data Integration (PDI) to pull two of the CSV files into Oracle (Salaries.csv) and MySQL (SchoolsPlayers.csv) and pull in Master.csv as a flat file. I had to create JDBC Source models for the Oracle and MySQL sources and a flat file Source model for the Master.csv file for this to work. After the Source models were created I created a View that joined all of those models together on player ID. Below is the SQL that was generated and updated by the Transform operation in JBDS.

```sql
SELECT
  OracleDS.SALARIES.PLAYERID,
  OracleDS.SALARIES.TEAMID,
  OracleDS.SALARIES.SALARY,
  MAX(OracleDS.SALARIES.SALARY) AS max_sal,
  RANK() OVER (ORDER BY OracleDS.SALARIES.SALARY) AS rank,
  MySQLDS.schoolsplayers.schoolID,
  Master.Master.nameLast
FROM
  OracleDS.SALARIES
  LEFT OUTER JOIN MySQLDS.schoolsplayers ON MySQLDS.schoolsplayers.playerID = OracleDS.SALARIES.PLAYERID
  LEFT OUTER JOIN Master.Master ON Master.Master.playerID = OracleDS.SALARIES.PLAYERID
GROUP BY OracleDS.SALARIES.PLAYERID, OracleDS.SALARIES.TEAMID, MySQLDS.schoolsplayers.schoolID, OracleDS.SALARIES.SALARY, Master.Master.nameLast
```

‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍‍As you can see, the query isn’t that complicated, but it is using some aggregation functions to better demonstrate how the push down functions work in Teiid. What I learned from this particular exercise was that the query only does selects from the sources and then does the aggregate functions within Teiid itself UNLESS the View is comprised of like sources. For example, if I wrote this same query and all of the sources were Oracle, then Teiid would push down the RANK function to the database instead of handling it at the server level.

