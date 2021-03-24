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

## Enable elasticsearch security (add at the end of elasticsearch.yml)
```
# --------------------------------- Security ----------------------------------
xpack.security.enabled: true

$ vi config/elasticsearch.yml
```

## Enable elasticsearch api key authentication (add at the end of elasticsearch.yml)
```
xpack.security.authc.api_key.enabled: true

$ vi config/elasticsearch.yml
```

## Setup default xpack users account password (NOTE: Make sure to take note the passwords of all the account that you change)
* When you have a new installation of xpack and enable the authentication, xpack will have a default user accounts. You need to update the password of all the default users of elasticsearch xpack.
* Reference: https://www.elastic.co/guide/en/elasticsearch/reference/current/built-in-users.html#set-built-in-user-passwords
* Open terminal and browse to your elasticsearch xpack folder
```
$ cd elasticsearch-7.9.3
$ bin/elasticsearch
```
* open new terminal window, follow the instructions when prompt to enter the user's password:
```
$ bin/elasticsearch-setup-passwords interactive
```

## Enable api key authentication (add at the end of elasticsearch.yml)
* Reference: https://www.elastic.co/guide/en/elasticsearch/reference/current/security-settings.html#api-key-service-settings
```
xpack.security.authc.api_key.enabled: true

$ vi config/elasticsearch.yml
```

## Create api key (NOTE: make sure to use the user account you set up on above steps (e.g <user>/<password> elastic/elasticsearch))
* (IMPORTANT: Take note the details of the generated api key as you will use the details in your appliacation to connect to the elasticsearch)
* Reference: https://www.elastic.co/guide/en/elasticsearch/reference/current/security-api-create-api-key.html
* NOTE: below request we don't add expiration of the api key, if you want to add expiration of the api key you can specify using below field in the request:
```
"expiration": "1d"
```
* Execute below command to generate an api key (name: csmsf-api-key = you can change this value based on your preference)
```
curl --user elastic:elasticsearch -X POST "localhost:9200/_security/api_key?pretty" -H 'Content-Type: application/json' -d'
{
  "name": "csmsf-api-key",
  "role_descriptors": { 
    "role-a": {
      "cluster": ["all"],
      "index": [
        {
          "names": ["index-a*"],
          "privileges": ["read"]
        }
      ]
    },
    "role-b": {
      "cluster": ["all"],
      "index": [
        {
          "names": ["index-b*"],
          "privileges": ["all"]
        }
      ]
    }
  }
}
'
```
  



