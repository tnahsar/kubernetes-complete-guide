There are exactly three types of probes in Kubernetes. While they can execute different underlying network mechanisms, they are categorized strictly by their behavioral purpose in the container lifecycle. [1] 
------------------------------
## 📑 1. The Three Types of Probes (By Purpose) [2] 

* 
* Startup Probe: Verifies if the application within the container has successfully started up. All other probes (Liveness and Readiness) are completely disabled until the startup probe passes. It is ideal for legacy or slow-booting applications that take a long time to warm up. If it fails, the container is restarted. [1, 3, 4] 
* Liveness Probe: Determines if the container is still running smoothly during its daily operation. It catches deadlocks or frozen processes where the application is technically "running" but cannot progress. If it fails, Kubernetes kills the container and triggers an automatic restart. [1, 5, 6] 
* Readiness Probe: Determines if the container is prepared to accept network traffic from a Kubernetes Service. If it fails, the Pod’s IP address is removed from the load balancer endpoints so no users hit a broken container. It never restarts the container. [3, 5, 7, 8] 
* 

------------------------------
## 📑 2. The Four Check Mechanisms (How they execute)
Do not confuse the 3 probe types with the four different mechanisms used to run them. Any of the three probes above can be configured to use one of these actions: [1, 5, 9] 

   1. httpGet: Sends an HTTP GET request to the Pod IP. Success is any status code between 200 and 399.
   2. tcpSocket: Checks if a specific TCP port on the container is open. If it connects successfully, it passes.
   3. exec: Runs a specified CLI command inside the target container. It passes only if the command exits with a status code of 0.
   4. grpc: Performed natively via the gRPC Health Checking Protocol. It passes if the response status returned is SERVING. [1, 10] 

------------------------------
Would you like to see a YAML layout demonstrating how to chain a Startup Probe and a Liveness Probe together for a slow-starting application?

[1] [https://kubernetes.io](https://kubernetes.io/docs/concepts/workloads/pods/probes/)
[2] [https://medium.com](https://medium.com/@ayoubajdour20/health-checks-the-self-healing-mechanisms-that-keep-applications-bulletproof-3ebb63ad330f)
[3] [https://www.cloudthat.com](https://www.cloudthat.com/resources/blog/kubernetes-probe-configuration-for-different-scenarios)
[4] [https://www.youtube.com](https://www.youtube.com/watch?v=x2e6pIBLKzw&t=64)
[5] [https://kubeops.net](https://kubeops.net/blog/kubernetes-probes)
[6] [https://www.linkedin.com](https://www.linkedin.com/pulse/understanding-kubernetes-probes-liveness-readiness-startup-feyz-sari-5xmpe)
[7] [https://kubernetes.io](https://kubernetes.io/docs/concepts/workloads/pods/probes/)
[8] [https://medium.com](https://medium.com/@jrkessl/readiness-vs-liveness-probes-what-is-the-difference-and-startup-probes-215560f043e4)
[9] [https://devoriales.com](https://devoriales.com/mastering-kubernetes-health-checks-probes-for-application-resilience-part-1-out-of-3)
[10] [https://kubernetes.io](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)
