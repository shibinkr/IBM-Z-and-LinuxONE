# Chapter 19: Workload Partitioning and Multi-tenancy

Workload partitioning and multi-tenancy are key concepts in IBM Z and LinuxONE environments, enabling efficient utilization of resources, isolation of workloads, and secure multi-user operations. This chapter explores the mechanisms and best practices for workload management in mainframe and LinuxONE systems.


## 19.1 Workload Partitioning

Workload partitioning allows administrators to divide physical hardware into logical units, ensuring predictable performance, isolation, and security.

### 19.1.1 LPARs (Logical Partitions)

**Description:** LPARs divide a single physical mainframe into multiple isolated partitions, each running its own operating system instance.  

**Features:**
- Dedicated or shared CPUs, memory, and I/O channels.
- Hardware-enforced isolation.
- Independent OS and workloads per LPAR.

**Use Cases:**
- Running multiple Linux distributions or z/OS instances simultaneously.
- Isolating high-priority workloads from non-critical ones.
- Enabling multi-tenant environments.

---

### 19.1.2 z/VM Virtualization

**Description:** z/VM is a hypervisor that runs on top of an LPAR to host multiple virtual machines (VMs) for Linux or other workloads.

**Features:**
- Fine-grained CPU and memory allocation.
- Supports thousands of VMs per LPAR.
- Provides virtualized I/O, networking, and storage.

**Use Cases:**
- Consolidating workloads into fewer physical LPARs.
- Running test/dev, production, and sandbox environments concurrently.
- Enabling containerized workloads (Docker, Kubernetes) on LinuxONE VMs.



### 19.1.3 Containerized Partitioning

**Description:** Within LinuxONE VMs, containers (Docker/Kubernetes) provide lightweight workload partitioning.

**Features:**
- Shares the same OS kernel but isolates processes.
- Rapid provisioning and scaling.
- Integrates with Kubernetes orchestration for HA and load balancing.

**Use Cases:**
- Running cloud-native applications alongside traditional workloads.
- Implementing microservices architectures.
- Efficiently managing resource usage for multi-tenant applications.



## 19.2 Multi-Tenancy

Multi-tenancy allows multiple users, teams, or applications to securely share the same infrastructure without interference.

### 19.2.1 Security and Isolation

**Mechanisms:**
- Hardware-enforced isolation via LPARs.
- z/VM provides virtual machine boundaries.
- Containers provide process-level isolation.
- Role-based access control (RBAC) and SELinux/SElinux sVirt for process security.

**Benefits:**
- Protects tenant data and workloads.
- Prevents resource starvation across tenants.
- Enables regulated environments to enforce compliance.


---
### 19.2.2 Resource Management

**Mechanisms:**
- CPU capping and weight assignments in LPARs/z/VM.
- Memory allocation and dynamic memory management.
- Storage quotas using SAN/FICON/zHPF and Linux quotas.
- Kubernetes resource requests, limits, and namespaces.

**Benefits:**
- Ensures fair resource allocation among tenants.
- Prevents performance degradation.
- Supports predictable SLAs.


## 19.3 Hybrid Workload Strategies

IBM Z and LinuxONE support **hybrid workload partitioning**:
- Combine traditional mainframe workloads (z/OS, DB2, CICS) with Linux containerized applications.
- Utilize LPARs for critical workloads and z/VM for flexible VM/container deployments.
- Leverage Kubernetes for orchestrating microservices on LinuxONE while maintaining isolation from legacy workloads.


## 19.4 Best Practices

1. Use LPARs to separate critical from non-critical workloads.
2. Employ z/VM for large-scale VM consolidation.
3. Use containerization for dynamic, cloud-native workloads.
4. Implement RBAC, SELinux, and sVirt for tenant security.
5. Monitor resource utilization to enforce quotas and prevent contention.
6. Plan multi-tenancy with predictable SLAs for each tenant.

---

## 19.5 Summary

- IBM Z and LinuxONE provide layered workload partitioning: **LPAR → z/VM → Linux VM → Container**.
- Multi-tenancy is achievable without sacrificing security or performance.
- Combining virtualization and containerization enables consolidation, efficient resource usage, and flexible deployment of both legacy and cloud-native workloads.
