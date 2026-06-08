
# Basic load balancing provided by Service object

Question: Kuberenetes service object also provide load balancing, right? but that would be default round robin, right?

Yes, you are 100% correct on both points. A standard Kubernetes Service object (specifically of type ClusterIP or NodePort) does provide layer-4 load balancing, and it defaults to a round-robin distribution method [1, 2].
However, the way Kubernetes executes this load balancing is fundamentally different from a tool like NGINX or an AWS ALB.
------------------------------
## 1. How the Service Object Load Balances (Under the Hood)
A Kubernetes Service is not a running software container or a physical proxy. Instead, it is a set of networking rules written directly into the Linux kernel of your worker nodes [1, 3].

[ Ingress Controller Pod ] 
          │
          ▼
   ( Hits Service IP )
          │
  [ kube-proxy / iptables ] ──► (Random/Round-Robin Choice)
          │
          ├──► Pod A (10.244.0.5)
          └──► Pod B (10.244.1.9)


   1. When you create a Service, a component running on every node called kube-proxy notices it [4].
   2. kube-proxy modifies the host node's internal Linux kernel networking tables (using iptables or IPVS) [1].
   3. When traffic hits that Service, the Linux kernel instantly intercepts the packet and rewrites its destination address to target one of the backend application pods [1, 3].

## 2. Why it is strictly Round-Robin (or Random)
Because this load balancing happens instantly at the Linux packet level, it is incredibly fast and consumes almost zero CPU [3]. However, because it is so low-level, it is completely "blind" to your application's health or current load [3].

* The Selection Mechanism: If you are using standard iptables (the default mode for most clusters), it actually uses a probabilistic random selection that mathematically evens out to a round-robin distribution over time [1, 2].
* The Limitation: The Service object cannot track if Pod A is currently drowning in 90% CPU usage while Pod B is completely idle [3]. It will still blindly send a 50/50 split of traffic to both pods [1].

------------------------------
## The Perfect Team: How Ingress and Services Coordinate
This explains exactly why you need both an Ingress Controller (like NGINX or AWS ALB) and a Kubernetes Service working together in a cluster:

[ Internet Traffic ]
        │
        ▼
[ Ingress Controller / Proxy ]  ◄─── Handles LAYER 7 (URLs, SSL, smart routing)
        │
        ▼
[ Kubernetes Service Object ]   ◄─── Handles LAYER 4 (Packet forwarding, stable IP)
        │
        ▼
   [ App Pods ]


* The Ingress Controller (Layer 7): Inspects the HTTP traffic [5]. It handles things like cookie-based session stickiness (ensuring a user stays on the same pod), custom routing, and smart health checks.
* The Service (Layer 4): Provides a single, permanent IP address for those pods [6]. The Ingress Controller points to this Service IP so it doesn't have to constantly track individual pod IP addresses as they crash and recreate [6].

Would you like to see how to configure Session Stickiness (Sticky Sessions) if you need to bypass the default round-robin behavior for a specific application?

