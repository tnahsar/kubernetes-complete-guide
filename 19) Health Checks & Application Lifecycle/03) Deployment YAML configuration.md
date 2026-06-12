## Kubernetes Probes: Liveness vs. Readiness
Kubernetes uses probes to monitor container health and manage traffic. They ensure high availability by automatically fixing dead applications and directing traffic only to healthy instances.
------------------------------
## 📑 Core Definitions## Liveness Probe

* Purpose: Determines if a container is still running.
* Action: If it fails, Kubernetes kills the container and restarts it according to its restart policy.
* Use case: Fixing deadlocks, stuck processes, or heavy memory leaks.

## Readiness Probe

* Purpose: Determines if a container is ready to accept network traffic.
* Action: If it fails, Kubernetes removes the Pod's IP address from endpoints matching the Service.
* Use case: Waiting for large cache initialization, database migrations, or slow framework startup times.

------------------------------
## 📑 Quick Comparison

| Feature | Liveness Probe | Readiness Probe |
|---|---|---|
| Primary Goal | Catch and fix stuck applications. | Prevent routing traffic to unready apps. |
| Failure Action | Kills and restarts the container. | Disconnects the Pod from the Service. |
| Pod Status | Changes to CrashLoopBackOff if failing continuously. | Stays Running but becomes 0/1 Ready. |

------------------------------
## 📑 Probe Mechanisms
You can configure both probe types using one of three network and system checks:

* HTTP GET: Kubernetes sends an HTTP request to a specific path and port. A status code between 200 and 399 indicates success.
* TCP Socket: Kubernetes attempts to open a TCP connection to a specified port. A successful connection indicates health.
* Exec Command: Kubernetes runs a specific command inside the container. An exit code of 0 indicates success.

------------------------------
## 📑 YAML Configuration Example
Below is a standard deployment configuration demonstrating both probes using HTTP checks:

apiVersion: apps/v1kind: Deploymentmetadata:
  name: app-health-demospec:
  replicas: 3
  selector:
    matchLabels:
      app: web-server
  template:
    metadata:
      labels:
        app: web-server
    spec:
      containers:
      - name: web-container
        image: nginx:alpine
        ports:
        - containerPort: 8080
        
        # Liveness Probe configuration
        livenessProbe:
          httpGet:
            path: /healthz
            port: 8080
          initialDelaySeconds: 15
          periodSeconds: 20
          timeoutSeconds: 2
          failureThreshold: 3

        # Readiness Probe configuration
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 10
          timeoutSeconds: 2
          failureThreshold: 2

------------------------------
## 📑 Parameter Reference

* initialDelaySeconds: Time to wait after the container starts before running the first probe.
* periodSeconds: Frequency of the check.
* timeoutSeconds: Duration to wait for a response before the probe is considered a failure.
* failureThreshold: Number of consecutive failures before giving up and taking action.
* successThreshold: Number of consecutive successes to mark the probe healthy after a failure.

------------------------------
If you'd like, I can help you with the next step:

* Show you advanced configurations like Startup Probes for extremely slow-booting apps.
* Provide concrete code snippets for health endpoints in Node.js, Python, or Go.
* Troubleshoot a specific error you are seeing with your current probes.


