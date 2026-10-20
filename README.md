# Part 1: Switching into robotics

**Short version:** it's doable, but difficulty depends on the role. Robotics software (controls, perception, embedded) is hard to enter from your background. Robotics data and platform work (data/backend/infra for robots) is quite realistic.

**What already fits**
- Kafka, ClickHouse, dbt, Dagster, Kubernetes, and Terraform are needed for fleet telemetry, training data, and simulation.
- ML model wrappers (FastAPI, MLflow) are in demand in robot learning.
- Your vacuum systems degree is rare and valuable in semiconductor equipment: Applied Materials, Lam, KLA, Brooks/Azenta, the ASML ecosystem. They use vacuum wafer-handling robots, and few data engineers understand the hardware.

**What's missing**
- C++ (used almost everywhere in the robotics core).
- ROS 2, Gazebo or Isaac Sim, rosbag/MCAP formats.
- Basics of kinematics, sensors, and real-time systems. For data/platform roles, the basics are enough.

**Where to aim**
- Robotics data/platform/infra engineer: Waymo, Nuro, Zoox, Aurora, Skydio, Boston Dynamics, Amazon Robotics, Symbotic, Figure, Physical Intelligence.
- Cloud/fleet backend in warehouse and industrial robotics.
- Semiconductor equipment (data + vacuum physics).

**Countries (Russian citizen living in Georgia; the US is close to unrealistic)**
- **Germany:** Blue Card; Munich is a robotics hub (Neura, Agile Robots, Franka).
- **Canada:** clear skilled-worker programs (Express Entry, Global Talent Stream); hubs in Toronto and Waterloo.
- **Netherlands, Denmark** (Universal Robots, MiR), **Poland/Czechia** as easier EU entry points.
- **UAE:** active market and a relatively accessible option.

Rules for Russian citizens vary a lot by country and change often, so check current conditions on official sites.

**3-6 month plan**
1. Learn ROS 2 and C++ basics.
2. Build a pet project: simulated robot fleet → Kafka → ClickHouse → Grafana, with rosbag/MCAP ingestion.
3. Rewrite the CV for robotics: drop the "bank/social media" emphasis, and highlight telemetry pipelines and ML serving.

**CV fix:** in Education, write "Vacuum Systems Process Engineering" and KNRTU (formerly KHTI) instead of just "Engineering Specialty". For roles at the hardware/data intersection, this is your competitive edge.

---

# Part 2: Pet project

**Project: Robot Fleet Telemetry Platform**

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