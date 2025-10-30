# Table of Content #
1. [What is loki](#what-is-loki)
2. [What are Pillers of observability](#What-are-Pillers-of-observability)

## What is loki ##
Loki is a horizontally-scalable, highly-available, multi-tenant log aggregation system inspired by Prometheus. Loki differs from Prometheus by focusing on logs instead of metrics, and collecting logs via push, instead of pull.

Loki is designed to be very cost effective and highly scalable. Unlike other logging systems, Loki does not index the contents of the logs, but only indexes metadata about your logs as a set of labels for each log stream.

A log stream is a set of logs which share the same labels. Labels help Loki to find a log stream within your data store

Log data is then compressed and stored in chunks in an object store such as Amazon Simple Storage Service (S3) or Google Cloud Storage (GCS), or even, for development or proof of concept, on the filesystem

## Loki Logging Stack ##
A typical Loki-based logging stack consists of 3 components:
1. Agent
2. Loki
3. Grafana

### Agent ###
An agent or client, for example Grafana Alloy, or Promtail. The agent scrapes logs, turns the logs into streams by adding labels, and pushes the streams to Loki through an HTTP API

### Loki ###
The main server, responsible for ingesting and storing logs and processing queries. It can be deployed in three different configurations.

### Grafana ###
Grafana is for querying and displaying log data. You can also query logs from the command line, using LogCLI or using the Loki API directly.

## Loki Feature ##

- Scalability
- Multi-tenancy
- Third-party integrations
- Efficient storage
- Alerting
- Grafana integration

## Architecture ##

Grafana Loki has a microservices-based architecture and is designed to run as a horizontally scalable, distributed system. The system has multiple components that can run separately and in parallel. The Grafana Loki design compiles the code for all components into a single binary or Docker image

## Storage ##
Loki stores all data in a single object storage backend, such as Amazon Simple Storage Service (S3), Google Cloud Storage (GCS), Azure Blob Storage, among others. This mode uses an adapter called index shipper (or short shipper) to store index (TSDB or BoltDB) files the same way we store chunk files in object storage.

## Data Format ##
Grafana Loki has two main file types: index and chunks.
- The index is a table of contents of where to find logs for a specific set of labels.
- The chunk is a container for log entries for a specific set of labels.

Loki index the labels attached to logs. 
If tow log lines have same lables attached, they are indexed and message in those lines are divided into chuncks which are compressed.
e.g
{component="Print", location="fc216", level="error"}"Printing is not supported by this printer"
Let's break above line:
- Here `{component="Print", location="fc216", level="error"}` is the combination of labels key/values. These key/values are hashed to form streamID which is `3bdfd339`.
- Here `"Printing is not supported by this printer"` is the log entry which is added to a chunck.

If new log entry comes with same labels key/value pairs are added in same hashed id which is created above i.e `3bdfd339` and log message will be added to chunck.

e.g

{component="Print", location="fc216", level="error"}"Out of paper"

{component="Print", location="fc216", level="error"}"Too much papers

these log meesages, which are `Out of paper, Too much papers, Printing is not supported by this printer`, are compressed and store in chuncks.
