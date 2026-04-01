# Akka Resilience Demo

A visual dashboard for demonstrating Akka's distributed systems capabilities across two key scenarios: cluster-based agent orchestration and multi-region high availability with disaster recovery.

## Overview

This application connects to Akka services to visualize how distributed agent workloads behave under real-world conditions, including node failures, region outages, and automatic recovery.

### Akka Clustering

Demonstrates how Akka clusters distribute agent workloads and memory across nodes. Key features:

- **Agent Team Management** -- Launch agent teams that are distributed across a 3-node cluster
- **Node Failure Simulation** -- Take nodes offline and observe agent migration and recovery in real time
- **Transaction Log** -- Live feed of read/write operations showing which nodes handle each request
- **Cluster Health Monitoring** -- Summary metrics including active teams, token usage, read/write latency, and node status

### Akka HA/DR

Demonstrates Akka's active-active multi-region replication for high availability and disaster recovery. Key features:

- **Multi-Region Deployment** -- Agent teams operate across two regions (us-east-1 and eu-west-1)
- **Active-Active Replication** -- Writes to either region are automatically replicated to the other using reliable gRPC
- **Region Outage Simulation** -- Toggle regions unavailable to observe seamless failover with no data loss
- **Conflict Reconciliation** -- When a downed region reactivates, Akka uses vector spaces to reconcile conflicts automatically
- **Replication Metrics** -- Live replication latency and cumulative data transfer between regions
- **Replication Log** -- Tracks write origins and replication targets across regions

## Getting Started

Open `resilience.html` in your browser:

```
open resilience.html
```

Use the **Akka Clustering** and **Akka HA/DR** tabs to switch between demos. Each tab has its own control panel for launching agent teams and simulating infrastructure events.

## Usage

1. Click **Launch New Agent Team** to start a team
2. Observe the live transaction log and metrics as agents process workloads
3. Use the control panel to simulate failures:
   - **Clustering tab**: Toggle nodes on/off to trigger agent migration
   - **HA/DR tab**: Toggle regions available/unavailable to observe failover and replication behavior
4. Launch multiple teams to see how the system scales

## Learn More

- [Akka Documentation](https://doc.akka.io)
- [Akka Console](https://console.akka.io)
