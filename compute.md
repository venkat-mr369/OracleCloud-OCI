Below is a comprehensive and well-structured presentation of the content from your PPT file, improved for readability, clarity, and formatting. All reference links and slide navigation cues have been removed, as requested. Headings, sections, and explanations are organized for maximum comprehension and professional presentation.

# Oracle Cloud Infrastructure (OCI) Compute and Storage

## Learning Objectives

- Understand OCI compute options
- Learn about autoscaling and OS management
- Explore VM migration

## Overview of OCI Compute Service

- **OCI Compute Service** allows you to provision and manage compute hosts (*instances*), forming the foundation for other OCI services.
- **Shapes** define the specific configuration (OCPUs, memory, and resources) for an instance.

## Compute Service Options

### 1. Bare Metal vs Virtual Machines

- **Bare Metal Instances:** Provide dedicated access to the physical server—ideal for performance-intensive and isolated workloads.
- **Virtual Machines (VMs):** Independent compute environments running on top of bare metal hardware; suitable for most standard applications.
- **Data Persistence:** Changes to local drives are lost after termination unless data is saved to attached volumes.

## Instance Images

- **Image:** A template specifying the OS and software for an instance; similar to a virtual hard drive.
- **Types of Images:**
  - **Platform Images:** Pre-built OS images (Oracle Linux, Ubuntu, CentOS, Microsoft Windows, Oracle Autonomous Linux).
  - **Oracle-Provided Images:** Enterprise-specific images and solutions.
  - **Custom Images:** Created from existing instances; include instance customizations and software.
  - **Partner Images:** Trusted third-party images from the Oracle marketplace.
  - **Bring Your Own Image (BYOI):** Import your own OS images if supported by hardware (max size: 400 GB).
- **Windows Custom Images:** Support generalized (cleaned of instance data) and specialized (point-in-time instance snapshots) images.

## Image Modes

- **Emulation Mode:** For VMs without paravirtualized driver support, created outside OCI from older environments.
- **Paravirtualized Mode:** For VMs with paravirtualized driver support, created outside OCI.
- **Native Mode:** For images exported from OCI.

## Import and Export Images

- Export the image to Object Storage in the current region, then copy and import into the destination region.
- Supported formats: **qcow2** and **VMDK**.
- The BYOI feature supports various OS versions and increases flexibility—observe all licensing requirements.

## Compute Shapes

### Fixed Shape

- Fixed OCPU and memory; available for both bare metal and VM instances.
- Processor options include AMD, Intel, and ARM.

### Flexible Shape

- **VM.Standard.E3.Flex** (and similar): Customize the number of OCPUs (up to 32), and memory (up to 64 GB per OCPU).
- Only available for virtual machines.
- Benefits: Align resources with workloads to optimize performance and costs.

## GPU Shapes

- Designed for **hardware-accelerated workloads** like AI training and high-performance computing.
- Powered by NVIDIA GPUs (A100, A10, V100, P100) with ultra-low latency networking (RDMA).
- Supports both VM and bare metal deployments.

## Instance Types

### Bare Metal

- Direct access to hardware; ideal for high-performance, non-virtualized, or compliance workloads.

### Virtual Machines

- Hypervisor-based virtualization; good for typical application hosting.

### Dedicated Hosts

- Entire server dedicated to a single tenant with multiple VMs.
- Meets compliance and host-based licensing needs.

## Autoscaling

- **Instance Pools:** Group of instances managed together to enable horizontal scaling.
- Build instance configurations and pools for quick scaling.
- **Autoscaling Policies:**
  - **Metric-based:** Scales based on CPU/memory usage.
  - **Schedule-based:** Scales based on predefined schedules.
- Scaling in prioritizes balance across availability and fault domains, then terminates the oldest instance.

## OS Management Services

- **Automated OS patch and package management** for Oracle Linux and Windows.
- Reduces manual work and errors and is available at no additional cost for OCI subscribers.
- Enables bulk management, scheduled updates, and CVE lookups across large fleets.

### Windows OS Management

- Supports Windows Server 2012 R2, 2016, 2019 (Standard and Datacenter).
- Manual agent installation required for pre-April 2020 images.
- Enables grouping, viewing, and scheduling updates.

## Preemptible Instances

- Use excess capacity at a lower price (50% cheaper).
- Not suitable for long-running or persistent workloads.
- Lifespan is not guaranteed; instances may be reclaimed with 30 seconds’ notice.
- Cannot be converted to on-demand instances or stopped/rebooted.

## Capacity Reservations

- Reserve compute capacity in advance for specific availability domains.
- Ensures availability for migrations, failovers, and workload spikes.
- Charges apply even if unused (at a reduced rate).

## Burstable Instances

- Designed for workloads with low baseline CPU usage but occasional spikes.
- Baseline and maximum OCPU are defined at launch.
- Useful for microservices, dev/test, CI/CD, static sites, and monitoring.
- Provisioned like standard instances using **VM.Standard.E3.Flex** with burstable mode enabled.

## Run Command

- Remotely configure and troubleshoot instances without SSH or open ports.
- Supported OS: Oracle Autonomous Linux, Oracle Linux, CentOS, Windows Server (not Ubuntu).
- Scripts up to 4 KB; larger files should use object storage. Output is limited for direct text, but extensive results can be stored in object storage.
- Useful for tasks like network configuration or troubleshooting SSH issues.

## Shielded Instances

- Protects against firmware-level threats like rootkits and bootkits.
- Uses **Secure Boot**, **Measured Boot**, and **Trusted Platform Module (TPM)**.
- Secure Boot ensures only signed bootloaders/OS can boot.
- Measured Boot records boot metrics to prevent unauthorized changes.
- TPM securely stores boot measurements.

## Dedicated Virtual Machine Hosts

- Run VMs on single-tenant, non-shared servers.
- Enables compliance and specialized licensing.
- VM types are limited to the host’s platform; some features requiring multiple hosts are unavailable on dedicated hosts.

