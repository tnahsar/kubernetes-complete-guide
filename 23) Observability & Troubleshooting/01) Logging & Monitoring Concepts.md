# 📘 Chapter 30: Logging & Monitoring Concepts

Now we enter something **every production system must have**:

> Observability 👀

You cannot manage what you cannot see.

In Kubernetes:

* Pods crash
* Nodes fail
* CPU spikes
* Memory leaks happen

If you don’t monitor → you discover problems too late.

This chapter explains logging and monitoring in **simple language**.

---

# 🧠 First: What Is Observability?

Observability has 3 main parts:

1. 📄 Logs → What happened?
2. 📊 Metrics → How is system performing?
3. 🔍 Traces → Where is delay happening?

In this chapter, we focus mainly on:

* Logs
* Monitoring (metrics)

---

# 📄 1️⃣ Pod Logs (Basic Logging)

Every container writes logs to:

```
stdout (standard output)
stderr (error output)
```

Kubernetes automatically captures this.

---

## 🔹 View Pod Logs

```bash
kubectl logs pod-name
```

If multiple containers:

```bash
kubectl logs pod-name -c container-name
```

---

## 🧠 Important Concept

Logs live on the **node** where the Pod runs.

If Pod dies:

* Logs may disappear.

So for production:

> We need centralized logging.

---

# 🏢 2️⃣ Centralized Logging (Production Standard)

Instead of checking logs manually on each Pod:

We collect logs into one place.

Architecture:

```
Pod → Log Agent → Log Storage → Dashboard
```

Common tools:

* Log collector agents (DaemonSet)
* Log storage systems
* Visualization dashboards

---

## 🔥 Popular Logging Stack

### 1️⃣ Elasticsearch

Elasticsearch

Stores logs.

---

### 2️⃣ Logstash

Logstash

Processes logs.

---

### 3️⃣ Kibana

Kibana

Visualizes logs.

Together called:

> ELK Stack

---

Another modern stack:

* Fluentd / Fluent Bit
* Loki
* Grafana

---

# 📊 3️⃣ Monitoring (Metrics Collection)

Logs tell:

> What happened

Metrics tell:

> How system is behaving over time

Examples:

* CPU usage
* Memory usage
* Network traffic
* Pod restarts
* Request rate
* Error rate

---

# 🏆 Most Popular Monitoring Tool

## Prometheus

Prometheus

Designed for:

* Kubernetes
* Cloud-native systems

It:

* Scrapes metrics
* Stores time-series data
* Allows querying

---

# 📈 Visualization Tool

## Grafana

Grafana

* Connects to Prometheus
* Builds dashboards
* Shows charts & alerts

---

# 🧠 How Monitoring Works in Kubernetes

Step-by-step:

1. Pods expose metrics (via `/metrics`)
2. Prometheus scrapes metrics
3. Stores them
4. Grafana displays them
5. Alerts trigger if threshold crossed

---

# 🔥 Metrics Server (Simple Concept)

Remember from HPA chapter?

Horizontal Pod Autoscaler uses:

> Metrics Server

It:

* Collects CPU/memory metrics
* Feeds HPA

But it’s basic.

For full monitoring → use Prometheus.

---

# 🧩 What Should You Monitor?

In production, monitor:

### 🔹 Cluster Level

* Node CPU
* Node memory
* Disk pressure
* Node availability

### 🔹 Pod Level

* CPU usage
* Memory usage
* Restarts
* OOMKilled events

### 🔹 Application Level

* Request rate
* Error rate
* Response time

---

# 🚨 Why Monitoring Is Critical

Without monitoring:

* You don’t know when scaling needed
* You don’t know when memory leaking
* You don’t know when node overloaded
* You don’t detect failure early

Monitoring enables:

* Auto-scaling
* Alerting
* Capacity planning
* Incident response

---

# 🔔 Alerts (Very Important)

Monitoring is useless without alerts.

Examples:

* CPU > 80% for 5 minutes
* Pod restarting repeatedly
* Memory usage 90%
* Error rate spike

Prometheus + Alertmanager:

* Sends email
* Slack notifications
* PagerDuty alerts

---

# 🏭 Real Production Example

E-commerce site:

During sale:

* Traffic spikes
* CPU increases
* HPA scales Pods
* Prometheus detects high latency
* Alert triggers if threshold crossed

Everything works automatically.

---

# 🧠 Beginner vs Production Logging

| Beginner              | Production             |
| --------------------- | ---------------------- |
| kubectl logs          | Centralized logging    |
| Manual checking       | Dashboards             |
| No alerts             | Automated alerts       |
| Local troubleshooting | Observability platform |

---

# 🚨 Common Mistakes

❌ Not collecting logs centrally
❌ No alerts configured
❌ Monitoring only CPU
❌ Ignoring memory limits
❌ No dashboard review

---

# 📌 Chapter 30 Summary

Logging:

* Captures what happened
* Use centralized logging in production

Monitoring:

* Tracks system health
* Use Prometheus
* Visualize with Grafana
* Configure alerts

Observability is:

> The difference between reacting late and acting early.

---

Next:

# 📘 Chapter 31: Troubleshooting Kubernetes

(Where we learn how to debug like a real DevOps engineer 🔧)

Ready to continue?
