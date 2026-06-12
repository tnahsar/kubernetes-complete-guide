## ⚙️ How Probes Work in Production
In a production cluster, probes operate as an automated backend loop managed completely by the infrastructure components.
## 1. Who Checks These Probes?
The Kubelet is the component that executes these probes. [1] 

* 
* It is a node-level agent that runs locally on every single worker node in your cluster.
* The Kubelet directly initiates the HTTP GET, TCP Socket, or Exec Command checks against the container runtime inside its node.
* The control plane (the master node) is not involved in sending probe requests, preventing network bottlenecks. [2, 3, 4] 
* 

## 2. At What Duration Do They Check?
Probes run indefinitely based on specific intervals defined in your configuration. The exact timeline is controlled by three parameters: [5] 

* 
* Check Frequency: Driven by periodSeconds (default is 10 seconds). The Kubelet fires a check exactly at this interval. [6, 7, 8] 
* Timeout Limit: Controlled by timeoutSeconds (default is 1 second). If the container takes longer than this to respond, the individual check counts as a failure. [9, 10, 11, 12] 
* Failure Window: Determined by failureThreshold (default is 3 attempts). A container is only declared "Unready" or "Dead" after failing consecutively for this exact count. [13, 14, 15] 
* 

## 3. Where Do We Describe These Probes?
Probes are defined within the manifest file of your workload inside the Pod Specification (spec.containers). [16, 17, 18] 
You will typically write them in:

* 
* Deployment YAMLs: The most common location for microservices and web servers.
* StatefulSet YAMLs: Used for databases or stateful applications.
* DaemonSet YAMLs: Used for logging or monitoring agents running on every node. [19, 20, 21] 
* 

------------------------------
## 🔄 The Lifecycle in Production (Step-by-Step)## Step 1: Pod Scheduling [22] 
You apply your YAML. The Pod lands on a worker node, and its containers spin up.
## Step 2: The Initial Delay
The Kubelet reads initialDelaySeconds and intentionally does nothing for that duration, allowing your application code to run its internal bootstrap processes. [23] 
## Step 3: Regular Polling Loop
The Kubelet begins pinging your configured endpoint (e.g., /ready) exactly every periodSeconds. [24] 
## Step 4: The Routing / Restart Decision

* 
* Readiness Fails: The Kubelet immediately notifies the node's local routing agent (kube-proxy). kube-proxy strips the Pod's private IP out of the network Endpoints list. External traffic stops hitting this Pod, routing instead to sibling replicas. [25, 26, 27, 28] 
* Liveness Fails: The Kubelet ignores the network completely. It talks to the local container runtime (like containerd or Docker), terminates the process, and initiates a brand new container instance. [29] 
* 

------------------------------
If you are setting this up for your live system, let me know:

* 
* What programming language or framework your application uses.
* If your application depends on a database that might cause slow startups. [30, 31] 
* 

I can help you design the optimal endpoint logic for your code!

[1] [https://www.vcluster.com](https://www.vcluster.com/blog/kubernetes-liveness-probes-examples-and-common-pitfalls)
[2] [https://spacelift.io](https://spacelift.io/blog/kubernetes-liveness-probe)
[3] [https://medium.com](https://medium.com/@ranbir11/a-complete-beginners-guide-to-kubernetes-architecture-pods-deployments-ingress-storage-0615d751743e)
[4] [https://resolve.ai](https://resolve.ai/glossary/how-to-debug-kubernetes-probe-issues)
[5] [https://blog.colinbreck.com](https://blog.colinbreck.com/kubernetes-liveness-and-readiness-probes-how-to-avoid-shooting-yourself-in-the-foot/)
[6] [https://blog.nashtechglobal.com](https://blog.nashtechglobal.com/kubernetes-probes-liveness-readiness-and-startup/)
[7] [https://www.fairwinds.com](https://www.fairwinds.com/blog/a-guide-to-understanding-kubernetes-liveness-probes-best-practices)
[8] [https://docs.okd.io](https://docs.okd.io/latest/applications/application-health.html)
[9] [https://www.fairwinds.com](https://www.fairwinds.com/blog/a-guide-to-understanding-kubernetes-liveness-probes-best-practices)
[10] [https://www.squadcast.com](https://www.squadcast.com/blog/kubernetes-health-check-using-probes)
[11] [https://docs.redhat.com](https://docs.redhat.com/en/documentation/openshift_container_platform/3.11/html/developer_guide/dev-guide-application-health)
[12] [https://dl.acm.org](https://dl.acm.org/doi/fullHtml/10.1145/3551902.3551961)
[13] [https://thenewstack.io](https://thenewstack.io/kubernetes-probes-and-why-they-matter-for-autoscaling/)
[14] [https://blog.devops.dev](https://blog.devops.dev/a-detailed-guide-to-probes-in-kubernetes-272692477b9f)
[15] [https://hackernoon.com](https://hackernoon.com/kubernetes-liveness-readiness-and-startup-probes-keys-to-container-health-and-resilience)
[16] [https://levelup.gitconnected.com](https://levelup.gitconnected.com/kubernetes-from-zero-to-hero-5-3ffde6339e62)
[17] [https://medium.com](https://medium.com/@emafzal/kubernetes-health-checks-simplifying-readiness-and-liveness-probes-13d6eda09345)
[18] [https://seifrajhi.github.io](https://seifrajhi.github.io/blog/kubernetes-probes/)
[19] [https://www.alibabacloud.com](https://www.alibabacloud.com/blog/getting-started-with-kubernetes-%7C-stateful-application-orchestration-with-statefulsets_596312)
[20] [https://www.examtopics.com](https://www.examtopics.com/discussions/linux-foundation/view/148291-exam-kcna-topic-1-question-56-discussion/)
[21] [https://www.acte.in](https://www.acte.in/kubernetes-interview-questions-and-answers)
[22] [https://praneethreddybilakanti.medium.com](https://praneethreddybilakanti.medium.com/understanding-pod-phases-and-lifecycle-in-kubernetes-bac81ed16daa)
[23] [https://www.fairwinds.com](https://www.fairwinds.com/blog/a-guide-to-understanding-kubernetes-liveness-probes-best-practices)
[24] [https://ashadali.medium.com](https://ashadali.medium.com/understanding-readiness-and-liveness-probes-in-kubernetes-with-examples-2f31aaf6e693)
[25] [https://medium.com](https://medium.com/@pranavsb699/kubernetes-actual-working-flow-e24aff05620d)
[26] [https://kubeops.net](https://kubeops.net/blog/kubernetes-probes)
[27] [https://dzone.com](https://dzone.com/articles/fault-tolerant-microservices-with-observability)
[28] [https://distantjob.com](https://distantjob.com/blog/kubernetes-readiness-vs-liveness-probe/)
[29] [https://www.theserverside.com](https://www.theserverside.com/blog/Coffee-Talk-Java-News-Stories-and-Opinions/KCNA-Study-Guide-and-Certification-Questions)
[30] [https://dev.to](https://dev.to/sagarmaheshwary/kubernetes-liveness-vs-readiness-probes-what-they-actually-mean-3b3l)
[31] [https://oneuptime.com](https://oneuptime.com/blog/post/2026-03-19-rancher-liveness-readiness/view)
