# Elasticsearch XPack Basic License Authentication Setup

This document will provide you guide on how to setup authentication on elasticseach xpack basic license

## Download elasticsearch and extract
```
$ cd srv
$ wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.9.3-darwin-x86_64.tar.gz
$ tar xzvf elasticsearch-7.9.3-darwin-x86_64.tar.gz
$ cd elasticsearch-7.9.3
```

## Enable painless regular expression parsing (add at the end of elasticsearch.yml)
```
# Add painless script regular expression parsing
script.painless.regex.enabled: true

$ vi config/elasticsearch.yml
```

## Configure elasticsearch to use a single node locally
```
# Configure elasticsearch as a single node
discovery.type: single-node

$ vi config/elasticsearch.yml
```

## Set a unique cluster name for your machine in config/elasticsearch.yml
* To avoid automatically joining other nodes in the network. Then set that cluster name in your application.yml in tomcat
```
cluster.name: elasticsearch

$ vi config/elasticsearch.yml
```

##



