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

### Metrics ###

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

![alt text](https://github.com/MunawarRaza/prometheus/blob/master/assests/image.png)

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