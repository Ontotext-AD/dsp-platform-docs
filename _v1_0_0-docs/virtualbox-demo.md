---
title: VirtualBox
layout: default
prev_section: aws-demo
next_section: publishing-demo-how-to
category: Miscellaneous
permalink: v1_0_0-docs/virtualbox-demo/
---
## Requirements
* VirtualBox: version 4.3 and up
* Cpu: faster CPU = faster concept extraction
* Disk: at least 50g
* Ram: at least 28g

## Download
Ontotext public FTP: ftp.ontotext.com:/pub/dsp/pub-demo.tar.gz

## VirtualBox Import
1. Import VM image in VirtualBox by clicking “File” menu and then “Import Appliance”
<img src="{{ site.baseurl }}/img/virtualbox/import.png" alt="VirtualBox Import Appliance" style="float:none; margin:10px 0 10px 0" >
2. Select the pub-demo.ova image and click Next
<img src="{{ site.baseurl }}/img/virtualbox/import-select-file.png" alt="VirtualBox Select File" style="float:none; margin:10px 0 10px 0" >
3. On the next screen make sure you that “Reinitialize the MAC address of all network cards” is NOT selected
<img src="{{ site.baseurl }}/img/virtualbox/import-overview.png" alt="VirtualBox Import Overview" style="float:none; margin:10px 0 10px 0" >
4. When import process is done you should have new VM in the VMs list menu, right click on the newly created VM and select “Headless Start”
<img src="{{ site.baseurl }}/img/virtualbox/start.png" alt="VirtualBox VM Start" style="float:none; margin:10px 0 10px 0" >
5. Wait until the machine the state of the VM became “Running”, then you should be able to access the following services:
* [GraphDB™ Workbench](http://localhost:18080/graphdb)
* [Publishing TAG UI](http://localhost:19101/tag-ui)
* [Publishing NOW UI](http://localhost:19102/now-ui)
* [Publishing Extractor API](http://localhost:19091/extractor)
* [Publishing Processor API](http://localhost:19098/processor)
* [Publishing Content API](http://localhost:19097/content-api)
* [Publishing Feeder API](http://localhost:19096/feeder)
* [Publishing Related Reads API](http://localhost:19099/api)
* [Publishing Concept API](http://localhost:19092/concept-api)
* [Publishing Search API](http://localhost:19095/search-api)
* [Apache Solr™](http://localhost:19090/solr)

## Access
+ Port: 2222
+ **Service User**:
    + Username: `tomcat`
    + Password: `ontotext`
+ **Root User**:
    + Username: `root`
    + Password: `ontotext`
    + Note: `No root login`


#### SSH

```
ssh -p 2222 tomcat@localhost
```

## Usage
See the [Publishing Demo HowTo]({{ site.baseurl }}/v1_0_0-docs/publishing-demo-how-to/) section.