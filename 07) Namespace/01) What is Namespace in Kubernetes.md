
# What is Namespace in Kubernetes

A Namespace is a virtual sub-cluster within a physical Kubernetes cluster. It allows you to logically divide and isolate your cluster resources among different projects, teams, or environments (like Development, Testing, and Production). [1, 2, 3, 4, 5] 
## Why Do We Use Namespaces?

* Resource Isolation: Prevent naming conflicts. Team A and Team B can both create a deployment named web-app without interfering with each other, as long as they use different namespaces. [6, 7] 
* Access Control (RBAC): Grant user permissions restricted to a specific namespace. For example, a developer can have full access to the development namespace but zero access to the production namespace. [8, 9, 10] 
* Resource Quotas: Set strict limits on the amount of CPU, memory, or storage a specific team or project can consume, preventing one application from accidentally starving others. [11, 12, 13, 14] 

------------------------------
## Default Namespaces
Every Kubernetes cluster comes with a few built-in namespaces out of the box: [15] 

* default: The landing place for any resource you create without explicitly assigning it a namespace.
* kube-system: Reserved for internal system components created by Kubernetes itself (like the API server, CoreDNS, and proxy controllers).
* kube-public: Accessible by all users (even unauthenticated ones). It is rarely used, typically reserved for cluster-wide public data like bootstrap configurations.
* kube-node-lease: Holds internal heartbeat records (Lease objects) for each node to help the cluster determine node availability efficiently. [16, 17, 18, 19, 20] 

------------------------------
## Key Behavioral Rules## 1. What is Isolate vs. What is Shared
Namespaces do not provide complete physical or network isolation by default. [21, 22, 23] 

* Namespace-scoped (Isolated): Pods, Deployments, Services, ConfigMaps, and Secrets live inside a specific namespace.
* Cluster-scoped (Shared): Storage classes (StorageClass), Persistent Volumes (PersistentVolume), Users, Roles, and the Nodes themselves exist globally across the entire cluster. [24, 25, 26, 27, 28] 

## 2. Network Communication
By default, Pods in different namespaces can still talk to each other over the network using their fully qualified domain name (FQDN). [29, 30, 31] 

* Format: <service-name>.<namespace-name>.svc.cluster.local
* To strictly block communication between namespaces, you must implement Network Policies. [32, 33] 

------------------------------
## Common Commands

* List all namespaces:

kubectl get namespaces

* Create a new namespace:

kubectl create namespace development

* Run a command in a specific namespace:

kubectl get pods --namespace=development

* Set a temporary default namespace for your current context:

kubectl config set-context --current --namespace=development

[34, 35, 36, 37, 38] 

To help apply this to your setup, let me know:

* Are you setting up namespaces to isolate different environments (like dev/test/prod) or different team projects?
* Would you like to see an example of how to write a YAML file with a specific resource quota or access policy for a namespace?


[1] [https://www.vcluster.com](https://www.vcluster.com/blog/kubernetes-namespace-the-what-why-and-how)
[2] [https://zesty.co](https://zesty.co/finops-glossary/kubernetes-namespaces/)
[3] [https://cast.ai](https://cast.ai/blog/kubernetes-namespace-how-to-use-it-to-organize-and-optimize-costs/)
[4] [https://www.plural.sh](https://www.plural.sh/blog/namespace-in-kubernetes/)
[5] [https://www.tigera.io](https://www.tigera.io/learn/guides/kubernetes-security/kubernetes-namespace/)
[6] [https://www.plural.sh](https://www.plural.sh/blog/namespace-in-kubernetes/)
[7] [https://www.alibabacloud.com](https://www.alibabacloud.com/help/en/acr/user-guide/manage-namespaces)
[8] [https://www.vmware.com](https://www.vmware.com/topics/kubernetes-namespace)
[9] [https://www.plural.sh](https://www.plural.sh/blog/kubernetes-namespaces-guide/)
[10] [https://spacelift.io](https://spacelift.io/blog/kubernetes-best-practices)
[11] [https://www.plural.sh](https://www.plural.sh/blog/namespace-in-kubernetes/)
[12] [https://www.tigera.io](https://www.tigera.io/learn/guides/kubernetes-security/kubernetes-namespace/)
[13] [https://www.scaler.com](https://www.scaler.com/topics/namespace-in-kubernetes/)
[14] [https://www.plural.sh](https://www.plural.sh/blog/kubernetes-create-namespace-tutorial/)
[15] [https://oneuptime.com](https://oneuptime.com/blog/post/2026-02-02-kubernetes-namespaces-guide/view)
[16] [https://www.plural.sh](https://www.plural.sh/blog/namespace-in-kubernetes/)
[17] [https://k21academy.com](https://k21academy.com/kubernetes/kubernetes-namespace/)
[18] [https://www.plural.sh](https://www.plural.sh/blog/kubernetes-namespace-guide/)
[19] [https://medium.com](https://medium.com/@Adekola_Olawale/part-11-kubernetes-101-2004c89b4bcf)
[20] [https://goteleport.com](https://goteleport.com/blog/what-is-kubectl/)
[21] [https://release.com](https://release.com/blog/kubernetes-namespaces-the-ultimate-guide)
[22] [https://www.vcluster.com](https://www.vcluster.com/blog/achieving-network-namespace-and-cluster-isolation-in-kubernetes-part-1)
[23] [https://www.securview.com](https://www.securview.com/ai-security-essentials/kubernetes-namespace-isolation)
[24] [https://dev.to](https://dev.to/favxlaw/namespaces-in-kubernetes-explained-understanding-isolation-and-sharing-5ki)
[25] [https://slickfinch.com](https://slickfinch.com/blog/how-to-change-namespace-in-kubernetes-a-guide/)
[26] [https://medium.com](https://medium.com/@emafzal/kubernetes-secrets-and-cross-namespace-access-a-guide-e52cc276231a)
[27] [https://learn.microsoft.com](https://learn.microsoft.com/en-us/azure/aks/concepts-storage)
[28] [https://metalbear.com](https://metalbear.com/blog/kubernetes-guide/)
[29] [https://kubex.ai](https://kubex.ai/kubernetes-autoscaling/kubernetes-namespace/)
[30] [https://oneuptime.com](https://oneuptime.com/blog/post/2026-02-02-kubernetes-namespaces-guide/view)
[31] [https://www.plural.sh](https://www.plural.sh/blog/can-pods-communicate-across-namespaces/)
[32] [https://kubernetes.io](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)
[33] [https://www.plural.sh](https://www.plural.sh/blog/kubectl-switch-namespace-guide/)
[34] [https://kubegrade.com](https://kubegrade.com/kubernetes-namespaces/)
[35] [https://www.warp.dev](https://www.warp.dev/terminus/kubectl-create-namespace)
[36] [https://www.ibm.com](https://www.ibm.com/docs/en/zoscp/1.1.0?topic=clusters-adding-namespaces-cluster)
[37] [https://community.sap.com](https://community.sap.com/t5/technology-blog-posts-by-sap/deploying-sap-s-4hana-containers-with-kubernetes/ba-p/13651471)
[38] [https://blog.devops.dev](https://blog.devops.dev/top-16-kubernetes-scripts-for-streamlined-cluster-management-24bc24c867e8)
