---
date: 2025-01-08
weight: "1"
---
# CS6650 Building Scalable Distributed Systems

**Instructor**: [Aditya Mishra.Links to an external site.](https://ask-prof-a.github.io/)

Text Book: [Foundations of Scalable Systems](https://www.oreilly.com/library/view/foundations-of-scalable/9781098106058/preface01.html#what_you_will_learn)

Foundations of Scalable Systems: Designing Distributed Architectures

- 65% Homework: Java
- 6% Labs
- 25% two exams
	- 10% take home (**Feb 26**)
	- 15% (**Apr 23**)
- 4% attendance

TA: yang.song@northeastern.edu
- Wed 10-11:30pm
- Friday 1:00-2:30

# Introduction
## What is Scalability?
Definition: The ability of system to increase or decrease capacity in response to changing demands, while satisfying **SLA** for latency and availability.
- without breaking SLAs for performance and reliability

### SLA
SLA (Service Level Agreement): provider-customer contract that specifies measurable service targets (e.g., uptime %, response time).
### Prefixes - SI (Decimal) and Binary
![[week1-fig1.png]]
### LAN vs Wan
- LAN (Local Area Network)
	A **LAN** is a network that connects devices **within a small, limited geographic area**.
- WANs(Wide Area Network)
	Connects smaller networks, like LANs, over large geographical areas (cities, countries, world) to allow devices in different locations to communicate.

Perfect linear scaling is impossible in practice due to bottlenecks.
### Scaling Different Attributes of Software
- The number of **simultaneous** requests a system can process
- The **amount of data** a system can effectively process and manage
- Maintain a stable, **consistent response time** as the request load grows
- And degrade gracefully (slow down with warnings, don’t crash)
### Scaling-Up vs Scaling-Out
Scaling-up: vertical
 - making a single machine more powerful: more CPU, RAM, faster disk.
Scaling-Out: horizon (cloud solution)
- adding more machines to handle the load

### Modification cost
Scalability is a function: (effort * cost) + (deployment * cost)
- Optimize server code
- Database schema and code changes
- Rewrite server code using fast framework or language

## Virtual Machines and Containers
Main different: 
- **VM:** virtualizes a whole machine and runs a **full guest OS**, including its **own kernel**.
- **Container:** does **not** include a full OS/kernel; it runs as **isolated processes** and **shares the host kernel**.

### Virtual Machines
Virtual Machines: run multiple processes simultaneously inside an operating system (OS)
**Virtualization**: run more than one OS on a single physical hardware.

#### Hypervisor (Virtual Machine Monitor): 
- The *software or layer* that creates and manages virtual machines, allowing them to share the underlying physical hardware. (it acts as a middle layer that manages the interaction between the two.)
	- VM must have hypervisor
	- Containers: don’t need a hypervisor. Standard containers (Docker, containerd) run on the host OS using the **same kernel**.
```
+--------------------+  +--------------------+
|        VM 1        |  |        VM 2        |
|  Guest OS + kernel |  |  Guest OS + kernel |
+--------------------+  +--------------------+
|      Hypervisor (VMM layer)               |
+-------------------------------------------+
|            Physical Hardware              |
+-------------------------------------------+

```
### Containers
A **container** is a way to package and run an application **with everything it needs** (code, libraries, dependencies, config) in an **isolated environment**, while **sharing** the host OS kernel.
- A container packages an application and its dependencies (user space) so it can run as isolated processes while sharing the host OS kernel.

```
+---------------------------+---------------------------+
|       Container A         |       Container B         |
|  App A + libs + runtime   |  App B + libs + runtime   |
|  isolated processes       |  isolated processes       |
| (namespaces + cgroups)    | (namespaces + cgroups)    |
+---------------------------+---------------------------+
|          Host OS (shared Linux kernel)                |
+------------------------------------------------------+
|                      Hardware                         |
+------------------------------------------------------+
```

OS / Kernel
- **VM:** each VM has a full guest OS + its own kernel
- **Container:** no separate kernel; containers **share** the **host kernel** (only user-space differs)

**Kernel:** the core part of an **operating system**. (e.g., CPU scheduling, Memory management, Device/I/O, System calls)
### Hypervisor(Virtual Machine Monitor) types:
Type-1: runs directly on top of bare h/w
- VMware ESXi, Microsoft Hyper-V, Citrix Hypervisor (XenServer
Type-2: runs on the OS
- Oracle VM VirtualBox, VMware Workstation/Fusion

## VM pros and cons
Pros
	• Can share hardware between separate users
	• High Security
	• Easy to create and destroy
	• VM description can be stored
Cons
	• Additional complexity
	• Very slight reduction in performance, perhaps 2%
	• Occasional sneaky security problems (Specter/Meltdown)