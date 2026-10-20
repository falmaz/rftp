# **Robot Fleet Telemetry Platform**

A simulated fleet of 3-5 robots → telemetry streaming → storage and data models → dashboards and an ML service. This is exactly what robotics companies build for their fleets.

**Requirements**

1. **Simulation.** ROS 2 (Jazzy) + Gazebo, several TurtleBot3 robots with Nav2 running missions in a warehouse. If Gazebo is too slow, start with a Python simulator publishing the same message types.
2. **Telemetry into Kafka.** A ROS 2 node subscribes to `/odom`, `/battery_state`, `/diagnostics`, and `/scan` (downsampled) and writes to Kafka (Redpanda is lighter). Schemas in Protobuf or Avro. **Write this node in C++** to close your language gap.
3. **Raw recordings.** Record rosbags in MCAP format to MinIO (S3). A Dagster asset parses MCAP (`mcap` library) into Parquet and loads it into ClickHouse.
4. **Data models.** ClickHouse + dbt: bronze (raw), silver (cleaned, deduplicated, timestamp-aligned), gold (fleet metrics, missions, episodes). dbt data quality tests; handle out-of-order and late-arriving data.
5. **Episode segmentation.** Detect missions, stuck events, collisions, battery drops. This is the most "robotics" part.
6. **ML service.** A simple model (battery anomalies or stuck prediction), tracked in MLflow, wrapped in FastAPI.
7. **API and dashboards.** FastAPI: fleet status, robot history. Grafana: live fleet state map, pipeline health (Prometheus).
8. **Infra.** Start with docker compose. Then kind/k3s + Terraform + ArgoCD, CI on GitHub Actions (ruff, pyright, pytest, trivy).

**Stack**
ROS 2, Gazebo, C++ (rclcpp), Python, Kafka/Redpanda, MinIO, ClickHouse, dbt-clickhouse, Dagster, MLflow, FastAPI, Grafana, Prometheus, Docker/k8s.

**Phases**
- **MVP (2-3 weeks):** simulator → Kafka → ClickHouse → Grafana.
- **Phase 2:** MCAP pipeline, dbt layers, episode segmentation.
- **Phase 3:** ML service, k8s, CI.
- **Phase 4:** load testing and performance numbers.

**Definition of done**
- Starts with one command (`docker compose up`).
- README has an architecture diagram, a GIF or video demo, and measured numbers: messages/sec, end-to-end latency (p95), data volume.
- At least one incident write-up: a robot got stuck, and the data shows why.

**For the CV later:** "Built fleet telemetry platform for N simulated ROS 2 robots: X msg/s, p95 latency Y s, episode detection, anomaly model serving."