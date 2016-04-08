---
title: AWS AMI
layout: default
prev_section: apache-solr
next_section: virtualbox-demo
category: Miscellaneous
permalink: v1_0_0-docs/aws-demo/
---
## Requirements
* Instance type: m4.2xlarge, m3.2xlarge or similar
* Disk: at least 50g

## AMI

## Creating EC instance from AMI

See the [Amazon EC2 Documentation](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/LaunchingAndUsingInstances.html)

## Services
* **GraphDB™ Workbench**: `http://<ec_instance_ip_address>:8080/graphdb`
* **Publishing TAG UI**: `http://<ec_instance_ip_address>:9101/tag-ui`
* **Publishing NOW UI**: `http://<ec_instance_ip_address>:9102/now-ui`
* **Publishing Extractor API**: `http://<ec_instance_ip_address>:9091/extractor`
* **Publishing Processor API**: `http://<ec_instance_ip_address>:9098/processor`
* **Publishing Content API**: `http://<ec_instance_ip_address>:9097/content-api`
* **Publishing Feeder API**: `http://<ec_instance_ip_address>:9096/feeder`
* **Publishing Related Reads API**: `http://<ec_instance_ip_address>:9099/api`
* **Publishing Concept API**: `http://<ec_instance_ip_address>:9092/concept-api`
* **Publishing Search API**: `http://<ec_instance_ip_address>:9095/search-api`
* **Apache Solr™**: `http://<ec_instance_ip_address>:9090/solr`

## Access
+ **Service User**:
    + Username: `tomcat`
    + Key: `ssh private key(selected on EC instance creation)`
+ **Root User**:
    + Username: `root`
    + Password: `ontotext`


#### SSH

```
ssh -i <path_to_private_key> tomcat@<ec_instance_public_ip_address>
```

## Usage
See the [Publishing Demo HowTo]({{ site.baseurl }}/v1_0_0-docs/publishing-demo-how-to/) section.