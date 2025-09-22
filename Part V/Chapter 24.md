# Chapter 24: Communication Between LPARs

## 24.1 Introduction

Logical Partitions (LPARs) are independent virtual machines running on a single IBM mainframe system. While LPARs operate independently, many workloads require **high-speed, low-latency communication** between them. This chapter explores the mechanisms that enable inter-LPAR communication, focusing on **HiperSockets**, shared memory techniques, and other networking methods within the z/Architecture environment.



## 24.2 Overview of LPAR Communication

LPARs communicate using:

1. **HiperSockets** – Memory-only, high-speed TCP/IP communication.
2. **Channel-to-Channel (CTC) Adapters** – Emulated or physical I/O channels for data exchange.
3. **Shared Coupling Facility (CF) Structures** – For workload coordination and message passing.
4. **Traditional Networking** – Using standard Ethernet, OSA-Express, or other network adapters.

Among these, **HiperSockets** provides the fastest and most secure method because data never leaves the mainframe’s memory subsystem.



## 24.3 HiperSockets Architecture

### 24.3.1 Key Components

| Component | Description |
|-----------|-------------|
| **LPARs** | Independent virtual machines that need to communicate. |
| **Virtual NICs** | HiperSockets virtual network adapters configured per LPAR. |
| **PR/SM Hypervisor** | Manages memory sharing, isolation, and synchronization between LPARs. |
| **Shared Memory Buffers** | Memory areas used to transfer TCP/IP packets between LPARs. |

### 24.3.2 How HiperSockets Works

1. **Application Layer**: Applications send or receive data via **standard TCP/IP sockets**.  
2. **TCP/IP Stack**: TCP segments and IP packets are prepared for transport.  
3. **HiperSockets Driver**: Acts like a network adapter but uses **memory queues** instead of physical cabling.  
4. **Shared Memory Transfer**: Packets are written to a **send queue** in shared memory, visible to the destination LPAR.  
5. **Memory Synchronization**: PR/SM ensures cache coherency and access control.  
6. **Receiving LPAR Driver**: Reads packets from the **receive queue** and passes them to the TCP/IP stack.  
7. **Delivery to Application**: TCP/IP stack reassembles data and delivers it via socket API.


## 24.4 Internal Data Flow Example

Consider LPAR A sending data to LPAR B:

```mermaid
flowchart LR
    subgraph LPAR_A["LPAR A"]
        A1[Application] --> A2[TCP/IP Stack]
        A2 --> A3[HiperSockets Driver]
        A3 -->|Writes to| SM_Send[Shared Memory Send Queue]
    end

    subgraph Hypervisor["PR/SM Hypervisor"]
        SM_Send -->|Memory Synchronization| SM_Receive[Shared Memory Receive Queue]
    end

    subgraph LPAR_B["LPAR B"]
        SM_Receive --> B3[HiperSockets Driver]
        B3 --> B2[TCP/IP Stack]
        B2 --> B1[Application]
    end

    SM_Receive -->|Interrupt/Notification| B3

```
### Explanation

- LPAR A writes data to a **shared memory send queue** via its HiperSockets driver.
- Hypervisor **synchronizes memory**, making the data visible to LPAR B.
- LPAR B driver is **notified via an interrupt** and reads the data.
- Data flows through **TCP/IP stack** to the application.

This architecture enables **sub-microsecond latency** and high throughput, ideal for workload-intensive applications such as **databases, transaction processing, and high-speed file transfers**.



## 24.5 Advanced Features

### 24.5.1 Bidirectional Communication
```mermaid
flowchart LR
    subgraph LPAR_A["LPAR A"]
        A_App[Application] --> A_TCP[TCP/IP Stack]
        A_TCP --> A_HS[HiperSockets Driver]
    end

    subgraph LPAR_B["LPAR B"]
        B_App[Application] --> B_TCP[TCP/IP Stack]
        B_TCP --> B_HS[HiperSockets Driver]
    end

    subgraph Hypervisor["PR/SM Hypervisor"]
        A_HS -->|Send Queue| SM_AB[Shared Memory AB Queue] --> B_HS
        B_HS -->|Send Queue| SM_BA[Shared Memory BA Queue] --> A_HS
    end

```


- Each LPAR can have multiple **virtual NICs**.
- **Send and receive queues** are separate per NIC, supporting **simultaneous bidirectional traffic**.

---
### Data Flow Between 4 LPARs

```mermaid
flowchart LR
    %% LPAR A
    subgraph LPAR_A["LPAR A"]
        A_App[Application] --> A_TCP[TCP/IP Stack]
        A_TCP --> A_HS[HiperSockets Driver]
    end

    %% LPAR B
    subgraph LPAR_B["LPAR B"]
        B_App[Application] --> B_TCP[TCP/IP Stack]
        B_TCP --> B_HS[HiperSockets Driver]
    end

    %% LPAR C
    subgraph LPAR_C["LPAR C"]
        C_App[Application] --> C_TCP[TCP/IP Stack]
        C_TCP --> C_HS[HiperSockets Driver]
    end

    %% LPAR D
    subgraph LPAR_D["LPAR D"]
        D_App[Application] --> D_TCP[TCP/IP Stack]
        D_TCP --> D_HS[HiperSockets Driver]
    end

    %% Hypervisor and Shared Memory
    subgraph Hypervisor["PR/SM Hypervisor & Shared Memory"]
        A_HS -->|Send Queue AB| SM_AB[Shared Memory Queue AB] --> B_HS
        B_HS -->|Send Queue BA| SM_BA[Shared Memory Queue BA] --> A_HS

        A_HS -->|Send Queue AC| SM_AC[Shared Memory Queue AC] --> C_HS
        C_HS -->|Send Queue CA| SM_CA[Shared Memory Queue CA] --> A_HS

        B_HS -->|Send Queue BD| SM_BD[Shared Memory Queue BD] --> D_HS
        D_HS -->|Send Queue DB| SM_DB[Shared Memory Queue DB] --> B_HS

        C_HS -->|Send Queue CD| SM_CD[Shared Memory Queue CD] --> D_HS
        D_HS -->|Send Queue DC| SM_DC[Shared Memory Queue DC] --> C_HS
    end
```
### Explanation

- Each pair of LPARs has **dedicated shared memory queues** for bidirectional communication.  
- **HiperSockets drivers** handle sending and receiving data through these queues.  
- **Interrupts or notifications** signal when data is available for reading.  
- Applications interact **transparently via TCP/IP**, with no need for physical network devices.
---
### 24.5.2 Security

- Data **never leaves the mainframe**, reducing exposure to external threats.
- Virtual NICs can be **isolated per LPAR**, preventing unauthorized access.
---
### 24.5.3 Scalability

- HiperSockets scales with the **number of virtual NICs** and **mainframe memory bandwidth**.
- Multiple LPARs can communicate through the **same HiperSockets network** without additional physical infrastructure.


## 24.6 Comparison with Other Communication Methods

| Method             | Latency   | Throughput | Use Case                               |
|-------------------|-----------|------------|----------------------------------------|
| **HiperSockets**    | Ultra-low | Very high  | In-memory TCP/IP, high-speed apps      |
| **CTC Adapters**    | Medium    | Medium     | Legacy systems, SNA applications       |
| **Coupling Facility** | Low       | High       | DB2, workload coordination             |
| **Ethernet / OSA**  | Higher    | Medium     | External network communication         |

## 24.7 Best Practices

- **Use HiperSockets** wherever possible for internal LPAR communication.  
- **Configure dedicated virtual NICs** per workload to avoid congestion.  
- **Monitor shared memory usage** to prevent queue overflow.  
- **Leverage interrupts instead of polling** for efficient CPU usage.  
- **Ensure security isolation** between unrelated LPARs.  

---

## 24.8 Summary

LPAR-to-LPAR communication is a critical component of mainframe architecture. **HiperSockets** provides:

- **Memory-only, high-speed data transfer**  
- **Standard TCP/IP compatibility**  
- **Ultra-low latency and high throughput**  
- **Secure in-system communication**  
