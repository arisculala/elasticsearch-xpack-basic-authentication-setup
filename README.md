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
* After you execute the command above take note on the generated api key
```
{
  "id" : "VcfhZHgBOrZ190hkLwxC",
  "name" : "csmsf-api-key",
  "api_key" : "oQcdC8LfR2Cczlz2YrZMFA"
}
```
* NOTE: The apiKey value credentials are the base64 encoding of the API key ID and the API key joined by a colon. You can use a tool https://www.base64encode.org/ to generate the base64 apiKey value. In the sample above the apiKey is `VmNmaFpIZ0JPcloxOTBoa0x3eEM6b1FjZEM4TGZSMkNjemx6MllyWk1GQQ==`

## Test the api key generated to connect to elasticsearch
* When you type below command you will get an error
```
$ curl localhost:9200

{"error":{"root_cause":[{"type":"security_exception","reason":"missing authentication credentials for REST request [/]","header":{"WWW-Authenticate":["Basic realm=\"security\" charset=\"UTF-8\"","ApiKey"]}}],"type":"security_exception","reason":"missing authentication credentials for REST request [/]","header":{"WWW-Authenticate":["Basic realm=\"security\" charset=\"UTF-8\"","ApiKey"]}},"status":401}
```
* Execute below command to get elasticsearch info using API key authentication
```
curl -H "Authorization: ApiKey VmNmaFpIZ0JPcloxOTBoa0x3eEM6b1FjZEM4TGZSMkNjemx6MllyWk1GQQ==" http://localhost:9200/_cluster/health

OR

curl -H "Authorization: ApiKey VmNmaFpIZ0JPcloxOTBoa0x3eEM6b1FjZEM4TGZSMkNjemx6MllyWk1GQQ==" http://localhost:9200/
```



