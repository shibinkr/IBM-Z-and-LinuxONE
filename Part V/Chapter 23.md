# Chapter 23: IBM zCX – Enabling Linux Containers on z/OS

IBM z/OS Container Extensions (zCX) is a feature introduced in z/OS 2.4 that enables the deployment of Linux applications as Docker containers within a z/OS environment. This integration allows for the co-location of Linux-based workloads with traditional z/OS applications, facilitating modern application architectures while leveraging the strengths of the mainframe.

---

## 23.1 What is IBM zCX?

IBM zCX provides an execution environment within z/OS that allows Linux applications to run as Docker containers. Each zCX instance operates within its own z/OS address space, providing isolation and resource management. This setup eliminates the need for separate Linux partitions, streamlining operations and reducing overhead.

**Key Features:**

- **Co-location of Workloads:** Run Linux containers alongside z/OS applications, enabling seamless integration.
- **Operational Control:** Maintain z/OS qualities of service, including scalability, availability, and security.
- **Resource Efficiency:** Utilize zIIP processors for eligible workloads, optimizing resource usage and cost.

---

## 23.2 Architecture Overview

The zCX architecture integrates Docker-compatible Linux containers into the z/OS environment. Each containerized application runs within a dedicated zCX address space, which is managed through z/OS Management Facility (z/OSMF). This design ensures that Linux applications can interact with z/OS resources while benefiting from the mainframe's robust infrastructure.

**Components:**

- **zCX Address Space:** Hosts the Linux environment and manages containerized applications.
- **z/OSMF Workflows:** Facilitate the provisioning, management, and monitoring of zCX instances.
- **Docker Engine:** Runs within the zCX address space, managing container lifecycles.

### 23.2.1 Workflow Diagram


```mermaid
flowchart TD
    A["IBM Z / LinuxONE Physical Hardware"] --> B["LPAR: z/OS"]
    B --> C["zCX Address Space"]
    C --> D["z/VM Hypervisor"]
    D --> E["Linux VM(s) (Kubernetes / Standalone)"]
    E --> F["Docker Engine"]
    F --> G["Docker Containers / Pods"]

    C --> H["z/OS Services API Layer"]
    G --> H
    G --> I["Persistent Storage (DAS/SAN/FICON/zHPF)"]
    B --> I

    %% z/OSMF integration
    J["z/OSMF Management & Workflow"] --> C
    J --> F
    J --> E
```
#### Diagram Explanation

- **IBM Z / LinuxONE Physical Hardware:** CPU, memory, I/O channels, Crypto Express cards.

- **LPAR (Logical Partition):** Runs z/OS in an isolated partition.

- **zCX Address Space:** Hosts Linux VMs and Docker engine.

- **z/VM:** Provides virtualization for multiple Linux VMs.

- **Linux VMs:** Containers run inside these VMs, optionally orchestrated by Kubernetes.

- **Docker Engine:** Manages container lifecycle (deployment, scaling, networking).

- **Docker Containers / Pods:** Host Linux workloads or microservices.

- **z/OS Services API Layer:** Exposes mainframe services (DB2, MQ, etc.) to containers.

- **Persistent Storage:** Containers access DAS, SAN, FICON, or zHPF storage.

- **z/OSMF Management & Workflow:** Handles provisioning, lifecycle management, and monitoring for zCX, Linux VMs, and Docker containers.

This layout shows end-to-end integration from physical hardware, through z/OS and zCX, to Docker containers, while highlighting **z/OSMF’s role in managing the environment**.

---

### Multi-LPAR zCX with Kubernetes and z/OSMF

```mermaid
flowchart TD
    %% Physical hardware
    A["IBM Z / LinuxONE Physical Hardware: CPU, Memory, I/O Channels, Crypto Express"] --> B1["LPAR 1: z/OS"]
    A --> B2["LPAR 2: z/OS"]
    A --> B3["LPAR 3: z/OS"]

    %% zCX address spaces
    B1 --> C1["zCX Address Space (Linux)"]
    B2 --> C2["zCX Address Space (Linux)"]
    B3 --> C3["zCX Address Space (Linux)"]

    %% z/VM hypervisors
    C1 --> D1["z/VM Hypervisor"]
    C2 --> D2["z/VM Hypervisor"]
    C3 --> D3["z/VM Hypervisor"]

    %% Linux VMs running Kubernetes
    D1 --> E1["Linux VM 1 (K8s Node)"]
    D2 --> E2["Linux VM 2 (K8s Node)"]
    D3 --> E3["Linux VM 3 (K8s Node)"]

    %% Kubernetes control plane (multi-node)
    F["Kubernetes Control Plane"] --> E1
    F --> E2
    F --> E3

    %% Docker containers
    E1 --> G1["Docker Container / Pod"]
    E2 --> G2["Docker Container / Pod"]
    E3 --> G3["Docker Container / Pod"]

    %% Access to z/OS services and storage
    G1 --> H["z/OS Services: DB2, MQ, IMS, APIs"]
    G2 --> H
    G3 --> H
    G1 --> I["Persistent Storage: DAS / SAN / FICON / zHPF"]
    G2 --> I
    G3 --> I

    %% Management & monitoring
    J["z/OSMF: Provision, Monitor & Manage Containers / zCX"] --> C1
    J --> C2
    J --> C3
    J --> E1
    J --> E2
    J --> E3
    J --> G1
    J --> G2
    J --> G3

    %% Feedback loops
    H --> G1
    H --> G2
    H --> G3
    I --> G1
    I --> G2
    I --> G3
```
#### Explanation

- Multiple LPARs run isolated z/OS instances on the same physical IBM Z / LinuxONE hardware.

- Each LPAR hosts a **zCX address space**, running Linux for container workloads.

- **z/VM hypervisors** provide virtualization of Linux VMs, enabling multiple Kubernetes nodes per zCX.

- **Linux VMs** serve as Kubernetes worker nodes; the control plane can span multiple nodes for high availability (HA).

- **Docker containers (pods)** run business services or applications.

- Containers interact with **z/OS services** and persistent storage (DAS, SAN, FICON, zHPF).

- **z/OSMF** orchestrates provisioning, monitoring, and management across the entire stack.

- **High availability** is enabled through multi-node Kubernetes, multi-LPAR deployment, and redundant storage access.

This layout clearly shows multi-LPAR, multi-node orchestration, containerized workloads, and management with **z/OSMF**, making it closer to a real production IBM Z / LinuxONE zCX deployment.


## 23.3 Use Cases for zCX

IBM zCX supports various use cases that enhance the capabilities of z/OS environments:

- **IBM MQ Client Concentrator:** Deploying a lightweight client within a container to interact with z/OS-based MQ servers.
- **Service Management Unite:** Centralizing management interfaces for z/OS subsystems within a unified portal.
- **Application Modernization:** Running microservices, non-SQL databases, and analytics frameworks alongside traditional z/OS applications.
- **Open Source Integration:** Utilizing open-source tools and libraries within the z/OS ecosystem.

These use cases demonstrate how zCX can modernize mainframe operations by integrating contemporary application architectures.



## 23.4 Security and Compliance

zCX inherits the security features of z/OS, including:

- **RACF Integration:** Ensures secure access control for containerized applications.
- **Data Protection:** Leverages z/OS's data encryption and integrity mechanisms.
- **Compliance:** Meets regulatory requirements by maintaining data locality and auditability within the z/OS environment.

This robust security framework ensures that Linux containers running on zCX adhere to the same standards as traditional z/OS applications.



## 23.5 Performance and Resource Management

zCX is designed to optimize resource utilization:

- **zIIP Processor Utilization:** Eligible workloads can run on zIIP processors, reducing costs and improving performance.
- **Resource Allocation:** zCX instances are allocated CPU, memory, and storage resources through z/OSMF, ensuring efficient use of system resources.
- **Performance Monitoring:** Tools integrated with z/OS provide visibility into the performance of containerized applications.

These features enable efficient management of resources, ensuring that Linux containers operate effectively within the z/OS environment.



## 23.6 Getting Started with zCX

To begin using zCX:

1. **System Requirements:** Ensure your system meets the prerequisites, including z/OS 2.4 or higher and a supported IBM Z server.
2. **Installation:** Use z/OSMF to provision and configure zCX instances.
3. **Deployment:** Deploy Docker images within the zCX environment using standard Docker tools.
4. **Management:** Utilize z/OSMF workflows for ongoing management and monitoring of zCX instances.

IBM provides resources and support to assist with the setup and operation of zCX, ensuring a smooth integration process.



## 23.7 Summary

IBM zCX bridges the gap between traditional z/OS applications and modern Linux-based workloads. By enabling the deployment of Docker containers within the z/OS environment, zCX allows organizations to modernize their application architectures while maintaining the reliability and security of the mainframe.

Whether integrating open-source tools, modernizing legacy applications, or deploying microservices, zCX provides a flexible and efficient platform for running Linux containers on z/OS.



## 23.8 Further Reading and Resources

- [IBM zCX Documentation](https://www.ibm.com/docs/en/zos/2.4.0?topic=extensions-what-is-zos-container)
- [IBM zCX Redbooks](https://www.redbooks.ibm.com/abstracts/sg248471.html)
- [IBM zCX Support](https://www.ibm.com/support/z-content-solutions/container-extensions/)
