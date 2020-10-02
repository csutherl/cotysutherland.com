+++
author = "Coty Sutherland"
categories = ["Software Things"]
date = 2014-02-19T19:38:00Z
description = ""
draft = false
slug = "jboss-data-virtualization-6-implementation-notes"
title = "JBoss Data Virtualization 6 Implementation Notes"

+++


### Purpose

I am writing this post to document some notes about my experience while developing a virtual database on JBoss Data Virtualization 6. This post is a collection of notes that expand on my experience documented in [this](https://cotysutherland.com/2014/02/19/data-virtualization-jboss/) post. These are some things broken apart into sections that I thought were noteworthy.

### My Environment

* Dell T7400
* JBoss Data Virtualization 6 Beta (Teiid Engine 8.4.1)
* 16G of RAM allocated to heap
* JBoss Developer Studio 7 with Teiid Designer tools<

## Notes

### Caching

* General notes on Data Virtualization server caching are [here](https://cotysutherland.com/2014/02/19/data-virtualization-cache-notes/)
* Flat files are pretty slow and should typically be cached to work with. You can do this by making the view a materialized view. In the view table editor settings, set Materialized to True, leave the Materialized Table setting blank. Also add a cache TTL to the view’s transformation SQL. An example of this is to add the following line to the top of your SQL transformation “/*+ cache(ttl:3600000) */”. Doing this increased the performance of a ‘select count(*) from $FILE_SRC’ query from 950 seconds to just 0.09 seconds after caching.
* Applying the same technique to the complete joined view, we go from 400 seconds to less than 1 second.
* According to the doc [here](https://developer.jboss.org/wiki/AHowToGuideForMaterializationcachingViewsInTeiid), Teiid only supports two options for refreshing.

1. Query driven refresh, which does not block requests.
2. Manual refresh, which does block requests.
3. You can use the web UI for this, or you can call a function that will case a refresh. Calling the function to refresh can be scheduled.

* External vs. Internal Caching from here:
* Reasons to use External caching instead of Internal:
* The cached data needs to be fully durable. Internal materialization does not survive a cluster restart.
* Full control is needed of loading and refresh. Internal materialization does offer several system supported methods for refreshing, but does not give full access to the materialized table.
* Control is needed over the materialized table definition. Internal materialization does support [Secondary Indexes](https://docs.jboss.org/author/display/teiid84final/Materialized+Views#MaterializedViews-SecondaryIndexes), but they cannot be directly controlled. Constraints or other database features cannot be added to internal materialization tables.
* The data volume is large. Internal materialization (and temp tables in general) have memory overhead for each page. A rough guideline is that there can be 100 million rows in all materialized tables across all VDBs for every gigabyte of heap.

### Translators

* To create a data source on top of Excel is not quite as simple as creating a flat file (CSV) or JDBC source. The steps to create an Excel source (which I haven’t tested yet) are located here: [teiid: Expose Excel Data as OData feed using Teiid](http://teiid.blogspot.com/2013/06/expose-excel-data-as-odata-feed-using.html)
* I have so far been unable to get the Salesforce translator working properly. I can create a source that will successfully fetch metadata from SFDC, but when I try and put a view on top of it or anything else I get exceptions from JBoss. Once such exception is pasted below:

```
Caused by: org.jboss.jca.deployers.common.DeployException: IJ020060: Unable to inject: org.teiid.resource.adapter.salesforce.SalesForceManagedConnectionFactory property: url value: <a href="https://test.salesforce.com/services/Soap/u/29.0">https://test.salesforce.com/services/Soap/u/29.0</a>
```

### Optimizer

* The following steps are taken by Teiid when a query is executed (from here):

1. Parsing – validate syntax and convert to internal form
2. Resolving – link all identifiers to metadata and functions to the function library
3. Validating – validate SQL semantics based on metadata references and type signatures
4. Rewriting – rewrite SQL to simplify expressions and criteria
5. Logical plan optimization – the rewritten canonical SQL is converted into a logical plan for in-depth optimization. The Teiid optimizer is predominantly rule-based. Based upon the query structure and hints a certain rule set will be applied. These rules may trigger in turn trigger the execution of more rules. Within several rules, Teiid also takes advantage of costing information. The logical plan optimization steps can be seen by using the [OPTION DEBUG](http://docs.jboss.org/teiid/7.2.0.Final/reference/en-US/html/sql_support.html#option_clause) clause and are described in the [query planner](http://docs.jboss.org/teiid/7.2.0.Final/reference/en-US/html/federated_planning.html#query_planner) section.
6. Processing plan conversion – the logic plan is converted into an executable form where the nodes are representative of basic processing operations. The final processing plan is displayed as the [query plan](http://docs.jboss.org/teiid/7.2.0.Final/reference/en-US/html/federated_planning.html#query_plan).

* The most important consideration for a federated query planner is minimizing data transfer.
* Once a query plan has been obtained you will most commonly be looking for:
* Source pushdown — what parts of the query that got pushed to each source
* Join ordering
* Join algorithm used – merge or nested loop.
* Presence of federated optimizations, such as dependent joins.
* Join criteria type mismatches.

### Querying

* Looks like this version of Teiid might have a problem with *= outer joins.

```
Caused by: org.teiid.api.exception.query.QueryParserException: TEIID31100 Parsing error: Encountered "account_view.account.ACCOUNT_ID(+[*])[*]= case_view.cases.ACCOUNT_ID" at line 6, column 88.
```

### Things left To Do

* Build and explore the Excel translator
* Check into Big Data translator
* Learn about the Teiid Optimizer

Other guides for Teiid 8.4 can be located here: [Home – Teiid 8.4 – Project Documentation Editor](https://docs.jboss.org/author/display/teiid84final/Home)

