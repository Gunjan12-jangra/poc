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

