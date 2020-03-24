---
title: " ELK and Filebeat setup and installation local Ubuntu 18.04"
date: "2020-03-18"
layout: single
tags:
- ELK
categories:
- Blog-post
---

This installation for Ubuntu Desktop. For Cloud Ubuntu Server, the method is a bit different. Go to the reference at the bottom of this post and check out those tutorial websites.

## Install software requirement

``apt install openjdk-8-jre apt-transport-https wget nginx``

## Configuring Java

Follow this website: https://www.hostinger.my/tutorials/install-java-ubuntu

```
apt-get update && apt-get upgrade
add-apt-repository ppa:linuxuprising/java
apt-get update
apt-get install oracle-java14-installer
```



## Installing and Configuring Elasticsearch

Download Elasticsearch followed by public signing key:

``wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -``

Add repository:

``echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.list``

Update the repo list and install the package:

``sudo apt-get update``

``sudo apt-get install elasticsearch -y``

Mdify *elasticsearch.yml* file:

``nano /etc/elasticsearch/elasticsearch.yml``

Uncomment *network.host* and *http.port*. Following configuration should be added:
```
network.host: localhost
http.port: 9200
```

Check java home

``echo $JAVA_HOME``

Edit and uncomment this part in ``/etc/default/elasticsearch`` to

```
JAVA_HOME=/usr/lib/jvm/java-14-oracle
```

Enable and start elastic search:

``sudo systemctl enable elasticsearch.service``

``sudo systemctl start elasticsearch.service``

``sudo systemctl status elasticsearch``

Check installation

``curl localhost:9200/?pretty``

## Installing and Configuring Kibana

Install:

``apt-get install kibana``

Modify Kibana settings:

``nano /etc/kibana/kibana.yml``

Uncomment following lines:
```
server.port: 5601
server.host: "localhost"
elasticsearch.url: "http://localhost:9200"
```

Enable it on boot and start Kibana service:

``sudo systemctl enable kibana.service``

``sudo systemctl start kibana.service``

``sudo systemctl status kibana.service``

Go to this url *localhost:5601* to view Kibana dashboard

## Installing and Configuring Logstash
Install Logstash:

``sudo apt-get install logstash``

To load logstash beat create the below config file and insert below lines:

``sudo nano /etc/logstash/conf.d/02-beats-input.conf``

```
input {

  beats {

    port => 5044

  }

}
```
Create the configuration file and insert below lines:

``sudo nano /etc/logstash/conf.d/30-elasticsearch-output.conf``

```
output {

  elasticsearch {

    hosts => ["localhost:9200"]

    manage_template => false

    index => "%{[@metadata][beat]}-%{[@metadata][version]}-%{+YYYY.MM.dd}"

  }

}
```

Let’s enable Logstash on boot and start the service:

``sudo systemctl enable logstash.service``

``sudo systemctl start logstash.service``

``sudo systemctl status logstash.service``


# Filebeat setup and installation for Client

---

## Install and Configure Filebeat

To install filebeat:

```
apt update
apt upgrade
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-7.x.list
apt-get install apt-transport-https
apt update
sudo apt-get install filebeat
```

Now lets make changes in below configuration file

``sudo nano /etc/filebeat/filebeat.yml``

In the configuration file go to Filebeat Section change false to true as shown below :
```
#=========================== Filebeat inputs =============================
# Change to true to enable this input configuration.
  enabled: true                            #  (change false to true)
  paths:
    - /var/log/syslog
```

In the configuration file go to Kibana Section as shown below:
```
#============================== Kibana =====================================
# uncomment the host and change "localhost to IP"
host: "<IP of server>:5601"
```

In the configuration file got ElasticSearch Section as shown below:
```
#-------------------------- Elasticsearch output ------------------------------
# uncommnet the hosts section
hosts: ["localhost:9200"]
```

Uncomment the Logstash output:
```
output.logstash:
# The Logstash hosts
hosts: ["localhost:5044"]
Start and enable Filebeat
```

To enable Filebeat modules, we need to edit the “filebeat.reference.yml” configuration file:

``nano /etc/filebeat/filebeat.reference.yml``

We will enable the syslog system module for Filebeat:

```
module: system
# Syslog
syslog:
enabled: true
```

We will start and enable Filebeat:

``systemctl start filebeat``

``systemctl enable filebeat``

``systemctl status filebeat``

---

You're good to go now! ❤️

Reference:
1. [https://www.fosslinux.com/6084/how-to-install-elk-stack-on-ubuntu-18-04.htm](https://www.fosslinux.com/6084/how-to-install-elk-stack-on-ubuntu-18-04.htm)
2. [https://www.linuxcloudvps.com/blog/how-to-install-elk-stack-on-ubuntu-18-04/](https://www.linuxcloudvps.com/blog/how-to-install-elk-stack-on-ubuntu-18-04/)
3. [https://www.fosstechnix.com/install-elk-stack-on-ubuntu/](https://www.fosstechnix.com/install-elk-stack-on-ubuntu/)
4. [https://linuxconfig.org/install-elk-on-ubuntu-18-04-bionic-beaver-linux](https://linuxconfig.org/install-elk-on-ubuntu-18-04-bionic-beaver-linux)
5. [https://www.lahilabs.com/2020/01/02/how-to-install-elk-siem-for-beginners-complete-guide/](https://www.lahilabs.com/2020/01/02/how-to-install-elk-siem-for-beginners-complete-guide/)
