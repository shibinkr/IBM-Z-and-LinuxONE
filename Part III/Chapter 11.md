# Chapter 11: Linux on IBM Z

Linux on IBM Z (LinuxONE) brings the flexibility and open-source ecosystem of Linux to **enterprise-grade mainframes**, combining scalability, reliability, and security.


## 11.1 Supported Distributions

IBM Z officially supports multiple Linux distributions, optimized for the mainframe architecture:

1. **Red Hat Enterprise Linux (RHEL) for IBM Z**
   - Enterprise-grade support and long-term maintenance.
   - Certified for mission-critical workloads.

2. **SUSE Linux Enterprise Server (SLES) for IBM Z**
   - Optimized for high performance and scalability.
   - Integration with IBM hardware management tools.

3. **Ubuntu for IBM Z**
   - Popular for development and cloud-native workloads.
   - Regular updates and community support.

4. **Other Linux Distributions**
   - Debian, CentOS, and openSUSE are also available with varying levels of support.


## 11.2 Kernel Adaptations

Linux kernels for IBM Z include special adaptations to leverage mainframe capabilities:

1. **64-bit z/Architecture Support**
   - Native 64-bit kernel to utilize full CPU registers and address space.
   
2. **SMT and Multi-Core Optimizations**
   - Optimized for **Simultaneous Multithreading (SMT)** and multi-core processors.

3. **High-Throughput I/O**
   - Enhanced I/O subsystems for **FICON, zHPF, and OSA** adapters.

4. **Memory and Cache Management**
   - Support for **RAIM-protected memory**, large caches, and NUMA architectures.

5. **Crypto and Security Features**
   - Kernel supports **hardware-assisted encryption** via Crypto Express cards.

## 11.3 Device Drivers for IBM Z

Linux on IBM Z requires specialized drivers for mainframe hardware:

1. **Channel I/O (z/VM, FICON, zHPF) Drivers**
   - Enable fast access to storage and networking subsystems.

2. **Network Adapters (OSA-Express)**
   - High-speed networking for large-scale enterprise workloads.

3. **Crypto Express Drivers**
   - Interface with hardware encryption for secure and high-performance cryptography.

4. **Virtualization Drivers**
   - Support for z/VM virtual machines, logical partitions (LPARs), and hardware-assisted virtualization.

5. **Monitoring and Management Drivers**
   - Integrate with **IBM OMEGAMON, RMF, and z/OS cross-platform tools** for performance monitoring.

---

## 11.4 Advantages of Linux on IBM Z

- **Scalability:** Handles thousands of virtual machines or containers.  
- **Reliability:** Leverages mainframe HA features like RAIM memory and Parallel Sysplex.  
- **Security:** Pervasive encryption and hardware security features.  
- **Enterprise Integration:** Works with IBM middleware, databases, and management tools.  
- **Open Source Ecosystem:** Access to a wide range of applications and developer tools.

---

## 11.5 Summary

- Linux on IBM Z brings the **flexibility of Linux** to enterprise mainframes.  
- Official support includes **RHEL, SLES, Ubuntu**, and other distributions.  
- Kernel adaptations and specialized device drivers allow Linux to **fully leverage IBM Z hardware**.  
- Organizations benefit from **high performance, reliability, security, and scalability** for mission-critical workloads.
