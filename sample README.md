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

```text
Spring Boot Application
          │
          ▼
OpenTelemetry Java Agent
          │
          ▼
OpenTelemetry Collector
          │
          ▼
Tempo
          │
          ▼
Grafana
```

---
