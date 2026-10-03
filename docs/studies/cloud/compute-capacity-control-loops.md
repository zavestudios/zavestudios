---
title: "Compute Capacity Control Loops"
weight: 20
---

An Auto Scaling group, an EKS managed node group, and a Karpenter NodePool can
all result in another worker joining a cluster. They are not three names for
the same thing. Each accepts a different input, owns a different part of the
lifecycle, and acts through a different authority boundary.

The useful question is not *which one scales nodes?* It is:

> When a pod cannot schedule, which control loop is authorized to create what?

## The Names Do Not Describe The Same Layer

| Construct | Authority | What it manages |
|---|---|---|
| EC2 Auto Scaling group | AWS infrastructure | A desired number of EC2 instances |
| EKS managed node group | EKS service | Kubernetes-aware lifecycle for EC2 workers, backed by an Auto Scaling group |
| Cluster Autoscaler | Kubernetes controller, acting through AWS Auto Scaling APIs | The desired count of node groups it has been configured to consider |
| Karpenter NodePool | Kubernetes controller | Constraints and disruption policy for nodes created in response to pod demand |
| EKS Auto Mode NodePool | EKS-managed Karpenter model | The same declarative constraints, with `NodeClass` served by EKS itself rather than by an installed Karpenter |

An Auto Scaling group knows about instances, health checks, and desired
capacity. It does not know that a pod is pending. A managed node group adds an
EKS lifecycle around that capacity: node registration, updates, draining, and
repair. Every managed-node-group node still belongs to an Auto Scaling group
in the AWS account.

Those rows are not peers. An Auto Scaling group holds capacity; Cluster
Autoscaler and Karpenter are the loops that turn pending pods into a request for
it. Cluster Autoscaler is the one row with nothing an operator writes down: no
custom resource, only process flags and Auto Scaling group tags it discovers
itself. That is largely why it goes missing from comparisons like this one.

It is also the narrower of the two loops. Cluster Autoscaler chooses among
groups that already exist and raises the desired count of one of them. It never
decides what to launch, because the group's launch template already did. On the
way back down it does not lower a desired count — it terminates specific
instances through the Auto Scaling group.

A Karpenter NodePool is not another wrapper around that group. It defines the
range of capacity Karpenter is allowed to create — architectures, zones,
instance families, capacity type (spot or on-demand), taints, limits, and
disruption policy. Karpenter combines those constraints with unsatisfied pod
requirements, creates a NodeClaim, and asks the cloud provider for a fitting
instance. The shape of the machine is decided per demand rather than fixed in
advance.

The two can collide. Pointing Cluster Autoscaler at a managed node group's Auto
Scaling group leaves two controllers writing the same object, because EKS
reconciles that group as well.

## Two Paths From Demand To A Node

The boxes below show authority, not deployment topology, and they deliberately
mix levels: controllers, the resources they act on, and the AWS APIs they call.
The paths are alternatives — Karpenter does not pass through the managed node
group's Auto Scaling group.

```mermaid
flowchart LR
  operator@{ shape: person, label: "Platform operator" }

  subgraph cluster["Kubernetes cluster"]
    pod["<b>Pending pod</b><br/><i>Declares resource and placement needs</i>"]
    ca["<b>Cluster Autoscaler</b><br/><i>Chooses an existing node group</i>"]
    pool["<b>Karpenter NodePool</b><br/><i>Bounds acceptable capacity</i>"]
    karp["<b>Karpenter controller</b><br/><i>Selects capacity for pod demand</i>"]
    claim["<b>NodeClaim</b><br/><i>One concrete capacity request</i>"]
    nodeA["<b>Kubernetes Node</b>"]
    nodeB["<b>Kubernetes Node</b>"]
  end

  subgraph aws["AWS account"]
    mng["<b>EKS managed node group</b><br/><i>Owns worker lifecycle</i>"]
    asg["<b>EC2 Auto Scaling group</b><br/><i>Maintains desired instance count</i>"]
    fleet["<b>EC2 CreateFleet</b><br/><i>Launches fitting capacity directly</i>"]
    ec2A["<b>EC2 instance</b>"]
    ec2B["<b>EC2 instance</b>"]
  end

  operator -- "declares group" --> mng
  mng -- "creates and manages" --> asg
  pod -- "unschedulable" --> ca
  ca -- "raises desired count" --> asg
  asg -- "launches" --> ec2A
  ec2A -- "registers as" --> nodeA

  operator -- "declares constraints" --> pool
  pool -- "constrains" --> karp
  pod -- "unschedulable" --> karp
  karp -- "creates" --> claim
  claim -- "requests" --> fleet
  fleet -- "launches" --> ec2B
  ec2B -- "registers as" --> nodeB

  classDef person   fill:#ffffff,stroke:#052e56,stroke-width:2px,color:#000000
  classDef control  fill:#f4f8fc,stroke:#1168bd,stroke-width:2px,color:#000000
  classDef resource fill:#ffffff,stroke:#6b6b6b,stroke-width:1px,color:#000000
  class operator person
  class ca,pool,karp,mng,asg,fleet control
  class pod,claim,nodeA,nodeB,ec2A,ec2B resource
```

## Scaling Is A Chain Of Decisions

The word *autoscaling* conceals several independent loops.

1. A workload scaler such as the Horizontal Pod Autoscaler raises a replica
   count, and the workload controller creates the pods.
2. The Kubernetes scheduler may find that no existing node can satisfy them.
3. A node autoscaler interprets those pending pods as demand for capacity.
4. An infrastructure mechanism launches an instance.
5. The instance registers as a node, after which the scheduler can place the
   pod.

An Auto Scaling group can replace an unhealthy instance or maintain a desired
count without understanding any of those pod-level signals. Likewise, creating
a managed node group does not by itself make pending pods change its desired
size. Cluster Autoscaler supplies that translation. Karpenter replaces both
the fixed-group selection and the separate desired-count update with
per-demand capacity selection.

This is also why *node pool* must be qualified. In Karpenter it is a Kubernetes
custom resource describing allowable capacity. In other managed Kubernetes
products, the same phrase may name the provider's managed collection of
workers, which is closer to an EKS node group. The same words describe
different authority on each platform.

## What EKS Auto Mode Changes

EKS Auto Mode keeps NodePool and NodeClass as the declarative surface while
moving more implementation behind the EKS service boundary. The NodeClass is not
Karpenter's, though: Auto Mode serves `eks.amazonaws.com/v1`, where a
self-installed Karpenter serves `EC2NodeClass` in `karpenter.k8s.aws`. AWS
manages compute scaling, node replacement and upgrades, networking, load
balancing, and storage components that would otherwise be installed or operated
separately.

That is not merely convenience. It changes where evidence can be collected and
where an operator can intervene. A Karpenter deployment exposes its controller
and cloud integration as components the platform operates. Auto Mode exposes a
smaller contract and makes the provider responsible for more of the result.

## The On-Premises Contrast

ZaveStudios currently has none of these managed compute abstractions. Terraform
declares libvirt virtual machines, and k3s registers them as Kubernetes Nodes.
Changing machine count is an infrastructure change; an unschedulable pod does
not cause the hypervisor to create a VM.

That makes a short-lived EKS exercise useful. It demonstrates something the
permanent platform structurally cannot: a provider API deciding, on its own
authority, whether Kubernetes demand becomes a new machine. The exercise should
be torn down afterward rather than becoming a second environment to fund and
secure.

## Exercise Before Conclusions

The AWS path has not been run yet. The failure modes below are hypotheses to
test, not operational findings.

1. Create an Auto Scaling group outside Kubernetes, terminate an instance, and
   observe desired-capacity reconciliation.
2. Create an EKS managed node group and identify the generated Auto Scaling
   group. Observe a managed update and the node drain boundary.
3. Make a pod unschedulable and prove that a managed node group does not respond
   to pod demand until Cluster Autoscaler is present. Find the tags it
   discovered the group by, and watch what happens when it and EKS both write
   that group.
4. Repeat with Karpenter. Trace the pending pod through NodePool selection,
   NodeClaim creation, instance launch, and node registration.
5. Constrain the NodePool so that no capacity satisfies the pod. Determine
   which status, event, and controller evidence explains why it remains
   pending.
6. Repeat through EKS Auto Mode and record what an operator can no longer see.

The comparison is complete only when it can explain who noticed demand, who
selected capacity, who created the machine, and who is responsible when that
machine cannot become a ready node.

## References

- [Amazon EKS managed node groups](https://docs.aws.amazon.com/eks/latest/userguide/managed-node-groups.html)
- [Karpenter NodePools](https://karpenter.sh/docs/concepts/nodepools/)
- [Karpenter NodeClaims](https://karpenter.sh/docs/concepts/nodeclaims/)
- [Amazon EKS Auto Mode](https://docs.aws.amazon.com/eks/latest/userguide/automode.html)
- [Amazon EC2 Auto Scaling groups](https://docs.aws.amazon.com/autoscaling/ec2/userguide/auto-scaling-groups.html)
