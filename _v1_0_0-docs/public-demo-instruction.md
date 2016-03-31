---
title: Public Demo
layout: default
prev_section: apache-solr
next_section: configuration-setups
category: Miscellaneous
permalink: v1_0_0-docs/public-demo/
---
## Service management
* Service definitions:
`/etc/init/tomcat-<service-id>.conf`
* Control service via:
`service tomcat-<service-id> <stop/start/restart/status>`
* Tomcat installation:
`/opt/tomcat`
* Tomcat instances:
`/opt/tomcat/instances`


## GraphDB
* Licence location: `/opt/tomcat/instances/graphdb-workbench/GRAPHDB_ENTERPRISE.license`
* Data dir location: `/var/lib/graphdb/graphdb-workbench`


## Logs
* Location: `/var/log/upstart/tomcat-<service-id>.log`