# POC Documentation – End-to-End Observability Stack

---

## Objective

The objective of this Proof of Concept (POC) was to build a complete observability stack capable of collecting, storing, and visualizing application Metrics, Logs, and Traces using open-source technologies.

The stack includes:

- VictoriaMetrics for Metrics
- Loki for Logs
- Tempo for Distributed Traces
- OpenTelemetry Collector
- OpenTelemetry Java Agent
- Fluent Bit
- Grafana

---

## Architecture

<img width="800" height="500" alt="image" src="https://github.com/user-attachments/assets/3cf3d5c3-f7d3-423e-8bf4-28e6c6b45dd1" />

---

## Components Used

| Component |	Purpose |
| ------------------- | ------------------------- |
| Spring Boot Application |	Sample E-commerce application |
| Node Exporter |	Collects Linux system metrics |
| VictoriaMetrics | Time-series database for metrics |
| Fluent Bit | Log collection and forwarding |
| Loki | Log storage backend |
| OpenTelemetry Java Agent |	Automatic application instrumentation |
| OpenTelemetry Collector |	Receives, processes, and exports telemetry |
| Tempo | Distributed trace storage |
| Grafana |	Visualization for Metrics, Logs, and Traces |

---

## Port Configuration

| Service | Port |
|---------|------|
| Spring Boot Application | 8080 |
| Grafana | 3000 |
| VictoriaMetrics | 8428 |
| Loki | 3100 |
| Tempo gRPC | 9096 |
| OpenTelemetry Collector OTLP gRPC | 4317 |
| OpenTelemetry Collector OTLP HTTP | 4318 |


---

## Metrics
## Components

- Spring Boot Application
- Node Exporter
- VictoriaMetrics
- Grafana

### Flow

<img width="800" height="300" alt="image" src="https://github.com/user-attachments/assets/6aecd702-27f4-4886-a73e-b4f344440c4c" />


### Metrics Collected

- CPU Usage
- Memory Usage
- Disk Usage
- Filesystem Usage
- Network Statistics
- System Load

---

## Logs

### Components

- Spring Boot Application
- Fluent Bit
- Loki
- Grafana

### Flow

<img width="800" height="400" alt="image" src="https://github.com/user-attachments/assets/cdf9e1e5-e885-4a5d-987f-6d24ed346a36" />

### Logs Collected

- Application Logs
- Spring Boot Logs
- Startup Logs
- Runtime Logs
- Java Exceptions

---

## Traces

## Components

- Spring Boot Application
- OpenTelemetry Java Agent
- OpenTelemetry Collector
- Tempo
- Grafana

### Flow

<img width="800" height="400" alt="image" src="https://github.com/user-attachments/assets/5dd47947-7e00-4109-978e-728c8caadb31" />

### OpenTelemetry Collector

The OpenTelemetry Collector receives telemetry data from applications, processes it, and forwards it to the configured backend services.

#### 1. Receivers
- OTLP gRPC – Receives telemetry over gRPC.
- OTLP HTTP – Receives telemetry over HTTP.

#### 2. Processor
- Batch Processor – Groups telemetry data into batches for efficient processing and transmission.

#### 3. Exporters
- OTLP Exporter (Tempo) – Sends traces to Grafana Tempo.
- Debug Exporter – Prints telemetry data to the Collector logs for debugging and verification.

---

### OpenTelemetry Java Agent

The application was instrumented using the OpenTelemetry Java Agent without modifying the application source code.

The Java Agent automatically generated:

- HTTP Request Traces
- Database Call Traces
- Service Information
- Distributed Trace Context

---

## Tempo Configuration

Tempo was configured with:

- Local Storage Backend
- WAL Storage
- OTLP Receiver
- HTTP API
- gRPC API
- Trace Retention

### tempo.yaml

```bash
server:
  http_listen_port: 3200
  grpc_listen_port: 9096

distributor:
  receivers:
    otlp:
      protocols:
        grpc:
          endpoint: 0.0.0.0:4319
        http:
          endpoint: 0.0.0.0:4320

storage:
  trace:
    backend: local
    local:
      path: /opt/observability/tempo/data
    wal:
      path: /opt/observability/tempo/wal

compactor:
  compaction:
    block_retention: 24h
```
---

## Fluent Bit Configuration

Configured Fluent Bit to:

- Monitor application log files
- Read logs continuously
- Parse log entries
- Buffer logs
- Retry failed transmissions
- Forward logs to Loki

### fluent-bit.conf

```bash
[SERVICE]
    # Flush
    # =====
    # set an interval of seconds before to flush records to a destination
    flush        1

    # Daemon
    # ======
    # instruct Fluent Bit to run in foreground or background mode.
    daemon       Off

    # Log_Level
    # =========
    # Set the verbosity level of the service, values can be:
    #
    # - error
    # - warning
    # - info
    # - debug
    # - trace
    #
    # by default 'info' is set, that means it includes 'error' and 'warning'.
    log_level    info

    # Parsers File
    # ============
    # specify an optional 'Parsers' configuration file
    parsers_file parsers.conf

    # Plugins File
    # ============
    # specify an optional 'Plugins' configuration file to load external plugins.
    plugins_file plugins.conf

    # HTTP Server
    # ===========
    # Enable/Disable the built-in HTTP Server for metrics
    http_server  Off
    http_listen  0.0.0.0
    http_port    2020

    # Storage
    # =======
    # Fluent Bit can use memory and filesystem buffering based mechanisms
    #
    # - https://docs.fluentbit.io/manual/administration/buffering-and-storage
    #
    # storage metrics
    # ---------------
    # publish storage pipeline metrics in '/api/v1/storage'. The metrics are
    # exported only if the 'http_server' option is enabled.
    #
    storage.metrics on

    # storage.path
    # ------------
    # absolute file system path to store filesystem data buffers (chunks).
    #
    # storage.path /tmp/storage

    # storage.sync
    # ------------
    # configure the synchronization mode used to store the data into the
    # filesystem. It can take the values normal or full.
    #
    # storage.sync normal

    # storage.checksum
    # ----------------
    # enable the data integrity check when writing and reading data from the
    # filesystem. The storage layer uses the CRC32 algorithm.
    #
    # storage.checksum off

    # storage.backlog.mem_limit
    # -------------------------
    # if storage.path is set, Fluent Bit will look for data chunks that were
    # not delivered and are still in the storage layer, these are called
    # backlog data. This option configure a hint of maximum value of memory
    # to use when processing these records.
[INPUT]
    Name              tail
    Path              /var/log/ecommerce/application.log
    Tag               ecommerce
    Read_from_Head    true
    Refresh_Interval  5

[OUTPUT]
    Name              loki
    Match             ecommerce
    Host              localhost
    Port              3100
    Labels            job=ecommerce
```

---

## VictoriaMetrics Configuration

Configured VictoriaMetrics to:

- Store Prometheus-compatible metrics
- Receive scraped metrics
- Retain historical metric data
- Serve Grafana dashboards

### prometheus.yml

```bash
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: "node_exporter"
    metrics_path: /metrics
    static_configs:
      - targets:
          - "65.2.48.73:9100"
```

---

## Grafana Configuration

Configured the following Data Sources:

| Data Source | Purpose |
|-------------|---------|
| VictoriaMetrics | Metrics |
| Loki | Logs |
| Tempo | Traces |

Grafana provides a unified interface for observing system metrics, application logs, and distributed traces.

<img width="1500" height="576" alt="Screenshot 2026-08-01 175504" src="https://github.com/user-attachments/assets/5a53f609-0136-4dfa-92f2-4be1b88e7e7b" />

<img width="1500" height="700" alt="Screenshot 2026-08-01 175523" src="https://github.com/user-attachments/assets/7bfac88d-d2c5-442c-9874-33d550f550da" />

---                                           

## Outcome

The observability stack was successfully implemented.

The POC demonstrates:

- Centralized metrics collection using VictoriaMetrics.
- Centralized log aggregation using Fluent Bit and Loki.
- Distributed tracing using OpenTelemetry and Tempo.
- Automatic application instrumentation using the OpenTelemetry Java Agent without requiring code changes.
- Centralized visualization through Grafana for metrics, logs, and traces.

---
