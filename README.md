# Table of Content #
1. [What is observability](#what-is-observability)
2. [What are Pillers of observability](#What-are-Pillers-of-observability)
    - [logging](#logging)
    - [Traces](#traces)
    - [Metrics](#metrics)
3. [What is prometheus](#what-is-prometheus)
    - [What prometheus can do?](#what-prometheus-can-do?)
    - [What kind of metrics prometheus can monitor?](#What-kind-of-metrics-prometheus-can-monitor)
    - [What prometheus does not monitor?](#what-prometheus-does-not-monitor?)
    - [Architecture of prometheus](#architecture-of-prometheus)
    - [Pull base vs push base model](#pull-base-vs-push-base-model)
    - [How to install prometheus](#how-to-install-prometheus)
4. [What are exporetes?](#what-are-exporters)
5. [What is node exporter](#what-is-node-exporter)
    - [How to install Node exporter](#how-to-install-node-exporeter)
6. [What are Metrics](#what-are-metrics)
    - [Attributes in Metrics](#attributes-in-metrics)
    - [Different Metrics Types](#different-metics-types)
        - [Counter Metrics](#counter-metrics)
        - [Gauge Metrics](#gauge-metrics)
        - [Histogram Metrics](#histogram-metrics)
        - [Summary Metrics](#summary-metrics)
7. [What is Promtool](#what-is-promtool)
8. [What is PromQL](#what-is-PromQL)
    - [Outline for PromQL](#outline-for-promQL)
    - [Data Types](#data-types)
        - [String](#string)
        - [Scaller](#scaller)
        - [Instant Vector](#instant-vector)
        - [Range Vector](#range-vector)
    - [Label Matcher](#Label-Matcher)
    - [@ Modifier](#Modifier)
    - [Operators](#Operators)
        - [Arithmetic Operators](#Arithmetic-Operators)
        - [Comparison Operators](#Comparison-Operators)
        - [Bool Operator](#Bool-Operator)
        - [Logical Operators](#Logical-Operators)
        - [Precedence of Operations](#Precedence-of-Operations)


## What is observability ##

## Pillers of Observability ##
There are three main pillers to observe the things

1. Logging
2. Traces
3. Metrics

### Logging ###

These are the record of event that have occurred.

Logs are comprised of
- Timestamp: When event occured
- Meesage: Information about the event

### Traces ###

Allow you to follow the operations as they traverses through various systems and services. Like, trace how a request reached to destination and back from that. So we can follow an individual request and see it flow thorugh our system hop by hop

e.g flow of an request

gateway <--> auth <--> user <--> redis

Traces comprises of
- trace-id: that can be used to identify a request as it traverses the system
- span: individual event forming a trace are called span
    - Each span tracks following 
        - start time
        - duration
        - parent-id

### What are Metrics ###

Metrics provide the information about the state of the system using numerical values
It contains the information
- What is the CPU load
- What is the number of open files
- What is the HTTP response time
- What are the number of errors

Metrics contains the four pieces of information 

1. metric_name
2. Value of metrics: Most recent or current value of the metrics ( Like current CPU load, Current RAM usage etc)
3. Timestamp of the metric: When did we actually collect this value
4. Dimensions: Additional information about the metrics

e.g
metric_name{dimentions} value timestamp
node_filesystem_avail_bytes{fstype="vfat",mountpoint="/home/"} 5000 4:30PM 12/1/2024

## What is prometheus ##

Prometheus is a monitoring solution that is responsible for collecting and aggregating metrics

### What prometheus can do ? ###

- Collect the metrics on http endpoint.

    Prometheus Collect the metrics by sending an http request
- Store metrics in time seriese databases

    Promethus store the metrics in time seriese databases which can be queired using promethus' built-in query language PromQl
- Dashboards

    Prometheus provide the dashboard functionality
- Alerts

    Prometheus provide the alerting system

### What kind of metrics prometheus monitor ###
It can monitor

- CPU 
- Memory
- Service uptime
- Application specific data
    - Number of exceptions
    - Latency
    - Pending Request

Note: Metrics are measured in numberical values. Like what are the pending requests. What is the latency, what is the cpu.

### What promethus dose not monitor ?###

It dose not monitor
- Logs
- Traces
- Events

### Architecture of Prometheus ###

1. Main Component

    Three parts of prometheus' main component
    1. Data Retrival worker

        This worker is responsible to scrap the metric data from the target. It pulls the metrics from the exporter which are running on target server
    2. Time Seriese database

        Data retrived from the remote target is stored in TSBD

    3. HTTP Server

        It accepts the query from the user / input section and retrive the results from TSBD

2. Exporters

    These are miniprocessors which collect the data from the current server and serve to the prometheus server. Different exporter are
    - Node exporeter
    - Apache
    - MySQL
    - Windows

3. Pushgateway

    A shortlive job runs and send the data to prometheus

4. Service Discovery
    
    Whenever we configure the target, we have to add target server in prometheus configuration file which is hactic. Service discovery automatically add the target servers instead we add the hard code values

5. Alert Manager

    Prometheus dose not send alert, it just triger the alert. To send email or SMS we need additional entity which can handle the Email, SMS and all that
    Prometheus push the alert to an alertmanager and alertmanager send that alert via email, sms

Following is the architectural diagram

![alt text](https://github.com/MunawarRaza/prometheus/blob/master/assests/architecture_diagram.png)

### Pull base vs Push Base Model ###

Pull Base:

In pull base model, prometheus sends a pull request to the target and on target, exporeter send the data to prometheus.
- Prometheus
- Zabix
- Nagios

In pull Base, we know our target system is down or up

Push Base:

In push base model, agent on target machines blindly sends the data to preconfigured IP. 
- Logstash
- Graphite

In Push Base, we don't know our main components where agent sends the data is live or not

### How to Install Prometheus ###

```
# Create User and block its login to the console
sudo useradd --no-create-home --shell /bin/false prometheus

# Create Config folder
sudo mkdir /etc/prometheus

# Create data folder
sudo mkdir /var/lib/prometheus

# Give Permissions
sudo chown -R prometheus:prometheus /etc/prometheus
sudo chown -R prometheus:prometheus /var/lib/prometheus

# Download the binary and extract it
# Go to browser https://prometheus.io/download/

# Copy the link and download in /home/user/
wget https://github.com/prometheus/prometheus/releases/download/v2.53.1/prometheus-2.53.1.linux-amd64.tar.gz 

tar xvf prometheus-2.53.1.linux-amd64.tar.gz 
cd prometheus-2.53.1.linux-amd64

# Copy executeable files
sudo cp prometheus /usr/local/bin
sudo cp promtool /usr/local/bin

# Assign Permissions to copied executeable files
sudo chown prometheus:prometheus /usr/local/bin/prometheus
sudo chown prometheus:prometheus /usr/local/bin/promtool

# Copy directories in config folder
sudo cp -r consoles /etc/prometheus
sudo cp -r console_libraries /etc/prometheus
sudo cp -r prometheus.yml /etc/prometheus

# Assign permissions to folders and files
sudo chown -R prometheus:prometheus /etc/prometheus/consoles
sudo chown -R prometheus:prometheus /etc/prometheus/console_libraries
sudo chown prometheus:prometheus /etc/prometheus/prometheus.yml

# Start the prometheus with command
sudo -u prometheus /usr/local/bin/prometheus \
    --config.file /etc/prometheus/prometheus.yml \
    --storage.tsdb.path /var/lib/prometheus/ \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries

# Create Systemd service to start prometheus in background

# Create service file in systemd
sudo touch /etc/systemd/system/prometheus.service

# Past following content in above created file and save the file

[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
    --config.file /etc/prometheus/prometheus.yml \
    --storage.tsdb.path /var/lib/prometheus/ \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries
[Install]
WantedBy=multi-user.target

# Reload the daemon
systemctl daemon-reload

# start the prometheus with systemd service
systemctl start prometheus
```
## What are exporters ? ##

These are miniprocessors which collect the data from the current server and serve to the prometheus server.
There are multiple exporter
Different exporter are
- Node exporeter
- Apache
- MySQL
- Windows

### How to Install Node exporeter ###

```
# Go to browser https://prometheus.io/download/

# Copy the link and download 
wget https://github.com/prometheus/node_exporter/releases/download/v1.8.2/node_exporter-1.8.2.linux-amd64.tar.gz

# Extract the binary and go inside the folder
tar xvf node_exporter-1.8.2.linux-amd64.tar.gz
cd node_exporter-1.8.2.linux-amd64

# Start the service. With below command service will be started in forground. If console is closed, this process will be shutdown
./node_exporter

# Check if the node exporter is working
curl http://localhost:9100/metrics

# Create systemd service #

# create user node_exporter
sudo useradd --no-create-home --shell /bin/false node_exporter

# Copy the executeable file
sudo cp -r node_exporter /usr/local/bin

# Assign the permissions to the executable file
sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter

# Create a service file in systemd

sudo touch /etc/systemd/system/node_exporter.service

# Past following content in above created file and save the file

[Unit]
Description=Node_Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target


# Reload the daemon
sudo systemctl daemon-reload

# Start the service
sudo systemctl start node_exporter

# Check if the node exporter is working
curl http://localhost:9100/metrics
```


## What are Metrics ##

### Attributes in metrics ###
Every metrics have TYPE and HELP attribute

Help Attribute: It is the description what the metric is

Type Attribute: Specify what type of metric ( Counter, Gauge, Histogram, Summary)

e.g

Help node_disk_discard_time_seconds_total: This is the total number of seconds spent by the all discards
Type node_disk_discard_time_seconds_total counter

### Different Metics Types ###

There are four main types of metric

- Counter
- Gauge
- Histogram
- Summary

#### Counter Metrics ####

- How many time did X happen

    e.g What are the total number of requests. This number is only increase not decrease.
- Number only can increase

e.g total number of requests, total number of exception etc

#### Gauge Metrics ####

- What is the current value of X. 

    e.g What is the current value of CPU Utilization. This can be increase or decrease at any time
- Number can go up or down

e.g Current CPU Utilization, Available System Memory, Number of concurrent Reuqests

#### Histogram Metrics ####

- How Long or how big something is
- Groups observations into configureable bucket sizes.

e.g you want to know the states of anything in different time zone.

You want to know
- How many total number of requests in <0.5s
- How many total number of requests in <1s
- How many total number of requests in <5s

so here <0.5s, <1s, <5s are the buckets.

Another example of size

- How many reuqest of size <800Mb
- How many reuqest of size <1000Mb
- How many reuqest of size <1500Mb

Following is the diagram for this 

![alt text](https://github.com/MunawarRaza/prometheus/blob/master/assests/histogram_metrics_example.png)

#### Summary Metrics ####

- Similar to historgram (track how long or how big something is)
- How many observations fell below X
- It gives value in percent

e.g
- 20% of all requests took less then .3s
- 50% of all requests took less than 0.8s
- 80% of all requests took less than 1s

Following is the example

![alt text](https://github.com/MunawarRaza/prometheus/blob/master/assests/summary_metrics_example.png)

### What is timeseries ###
When we "hit node_cpu_seconds_total" We receives the data against all CPUs and therir states (idl,nice,user,system). The combination of metric_name and unique label returned in the response of above query is called 1 time series. Since we got four results then there are four timeseries. We can see the timestamp which is same for each result. We got the result at single point in time. 

![alt text](https://github.com/MunawarRaza/prometheus/blob/master/assests/instant_vector_data_type_example.png)

## What is Promtool ##

Promtol is utility tool shipped with prometheus that can be used for:
- Check and validate configuration
    - Validate prometheus.yml
    - Validate Rules files
- Validate metric passed to it are correctly formatted

command: promtool check config /etc/prometheus/prometheus.yml

## What is PromQL ##
Its prometheus query language. Main way to query the metrics whitin prometheus from grafana or prometheus dashboard

### Outline for PromQL ###
- Data types
- Expressing data structure
- Selectors and modifiers
- Operations and Functions
- Vector Matching
- Aggregation
- Subqueries
- Histogram / Summary

#### Data Types ####
- String --> A simple string value (not in used)

    e.g "This is string"
- Scalar --> A simple numeric floting point value

    e.g 54.743 or 13.5
- Instant Vector --> Set of time series containing a single sample for each time series, all sharing the same timestamp

    e.g 
- Range Vector --> Set of time series containing a range of data points over time for each time serie

##### Instant Vector #####
If we got the value against each time series at same timestamp.

When we "hit node_cpu_seconds_total" We receives the data against all CPUs and therir states (idl,nice,user,system). The combination of metric_name and unique label returned in the response of above query is called 1 time series. Since we got four results then there are four timeseries. We can see the timestamp which is same for each result. We got the result at single point in time. 

![alt text](https://github.com/MunawarRaza/prometheus/blob/master/assests/instant_vector_data_type_example.png)

In URDU:

Hm query kerty hain k current cpu utilization ki value btao to ye ak value return ker dy ga. Hum keh sakty hain k hmen subha 10:50 minute jo cpu utilization thi wo btao to hm query man unix timestamp pass ker den gy. Hum kehty hain k 10:50 minute sy 10 minute pehly ki cpu utilization kia thi to is case man exact unix timestamp bhi pass ker sakty hain ya phr 10:50 minute ka Unix timestamp pass kren and offset 10m use ker len. Ye sari values jo return hon gi wo single value ho gi her query and timeseries ky against. Is liye ye instant vector ha

##### Range Vector #####

When we want to get the data from a specific time like we query give me the data of last three minutes. It will return all the data which was scrapped  against each timeseries in last three minutes and for each result timestamp would be different. It could be scrapped 1 time, 10 times etc.
e.g

Following query means, get the total cpu seconds in last three minutes.
node_cpu_seconds_total[3m]

let's spouse Above query will return following data as shown in picture. We got tow metrics with unique labels therefor there are two timeseries. Since it is giving us different time for each value of each timeseries, this is called Range vector


![alt text](https://github.com/MunawarRaza/prometheus/blob/master/assests/range_vector_data_type_example.png)

In URDU:
Hum range define kerty hain k last 5 minutes ka data hmen provide kren. Is case man prometheus ny 5 minute man ho sakta ha 5 dafa data collect kia hoa kisi bhi timeserise k liye. Is liye jb hmen data return ho ga to timeseries same ho gi but timestamp different ho ga r values bhi different ho sakti hain. 
e.g hm kehty hain k subha 10:40 sy 10:50 ky dermian cpu utilization kia thi. to hm 10:50 ki unix timestamp pass kren gy sath man [10m] range define kren gy. Phr her timeserise ky against hmen multiple values milen gi jinka timestamp differnt ho ga. Uper di gai picture man dkha ja sakta ha.

### Label Matcher ###
- = (equal)

    node_cpu_seconds_total{cpu="0",mode!="user"}[3m]
- != (not equal)

    node_cpu_seconds_total{cpu="0",mode!="user"}[3m]
- =~ (regex)

    node_filesystem_avail_bytes{cpu="0",device=~"/dev/sda.*"}
- !~ (negative regex)

    node_filesystem_avail_bytes{cpu="0",device!~"/dev/sda.*"}

### Modifire ###
We can get the data in past in multple ways

1. If we want to get the value of past in time. e.g get the cpu utilization 5hours ago, or 10days ago we use offset modifire before the time

    e.g

    - node_memory_Active_bytes{instance="server1"} offset 5h
    - node_memory_Active_bytes{instance="server1"} offset 1h30m

2. To go back to a specific point in time use the @ modifire

    - node_memory_Active_bytes{instance="server1"} @1663265188

        Note: here @1663265188 is the unix timestamp. We want to get the data at this unix timestamp

3. Combine "offset" modifire and "@" modifire
    
    - node_memory_Active_bytes{instance="server1"} @1663265188 offset 5m

        Note1: Above means first go to timestamp provide in unix form then go 5 minutes back before that time
    
        Note2: order dose not matter for offset and @ modifires. We can write in either ways.

    In following example both queries are equal

    - node_memory_Active_bytes{instance="server1"} @1663265188 offset 5m
    - node_memory_Active_bytes{instance="server1"} offset 5m @1663265188

4. offset and @ modifires both can can be used with range vector

    Let's suppose we want to go at a specific time with unix timetamp then we go 10 minutes back. From there we want to get the values of different time range for 2 minutes. Like we can say get 2 minutes data range of 10 minutes before September 15, 2024 10:00:4 PM GMT


    - node_memory_Active_bytes{instance="server1"}[2m] @1663265188 offset 10m

    node_cpu_seconds_total{cpu="0",mode="user"}[2m] @1722970584.93 offset 2m
    go to 1722970584.93 then go back 2 minutes and then give me the range of last 2 minutes

    let's break it

    If we convert @1722970584.93 into humen readable time it gives us `Tue Aug  6 11:56:24 PM PKT 2024`. Now due to `offset` modifire it will go to `Aug  6 11:54:24 PM PKT 2024` and due to range --> `[2m]` will return last 3 results before `11:54:24 PM` which are value at `11:54:24 PM, 11:53:24 PM, 11:52:24 P`. There is a difference of 1 minute in each timestamp becacuse i have configured prometheus to collect the data after 1 minute whcih is "scrape_interval: 60s"


### Operators ###

#### Arithmetic Operators ####

```
+
- 
*
/
%
^
node_filesystem_avail_bytes / 1024
```
#### Comparison Operators ####
```
==
!==
>
<
<=
>=

e.g
node_network_receive_bytes_total >=200
```
#### Bool Operator ####

bool operators are used to return true(1) or false(0)
e.g
- node_filesystem_avail_bytes > bool 2000000000
- bool operators are mostly used to generate alerts

#### Logical Operators ####
```
AND
OR
UNLESS
```

#### Precedence of Operations ####
When an promql expression has multiple binary operators they follow an order of precedence, from highest to lowest

```
1. ^
2. *, /, %, atan2
3. +, -
4. ==, !=, <=, >=, >
5. and, unless
6 or

Note: Operators on the same precedence level are left-associative. 
For Example, 2 * 3 % 2 is equilent to (2 * 3) % 2.
However ^ is right associateve, so 2 ^ 3 ^2 is equilent to 2^(3^2)
```


