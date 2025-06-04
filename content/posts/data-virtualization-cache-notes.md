+++
author = "Coty Sutherland"
categories = ["Software Things"]
date = 2014-02-20T03:38:00Z
description = ""
draft = false
slug = "data-virtualization-cache-notes"
title = "Data Virtualization Cache Notes"

+++


When to use caching:

* Query performance
* Load optimization – to minimize load on the underlying system.
* Consistent reporting
* Source availability
* Complex transformations
* Security – to allow users to see aggregate data and still keep them from the ability to access detailed data.

---

Where to keep the cache:

* Memory – gives you the fastest access, purged when the server restarts(?), more limited that storage in most envs
* Database – works well for large data sets, creates and manages tables with indexes
* File(s) – slower than memory, persistent across restarts, works well for small data sets

---

Refreshing the cache:

* Construction refresh – refresh when its built, only once
* Manual refresh – admin has to initiate a refresh
* Scheduled refresh
* Query-driven refresh – refreshed when queried if the cache has expired
* Event-driven refresh – an event can initiate a refresh. an event could be something over an ESB, a file drop, API call, etc.

How to refresh the cache:

* Incremental refresh – refresh only adds new data to the cache
* Live refresh – basically CDC/instant refreshing
* Full refresh – invalidate the cache and cache the data again

Online vs. Offline refreshing:

* Offline refreshing – the table cannot be accessed during the refresh period
* Online refreshing – queries CAN be executed against a table during a refresh. this data is obviously not complete though depending on the virtualization server’s implementation.

---

Cache replication is useful when:

* the number of queries/users accessing the table is high
* geographically dispersed consumers can have data cached physically closer to them for faster access
* cache replication makes the table more available. even if one cache is down, you still have another cache to query against.



