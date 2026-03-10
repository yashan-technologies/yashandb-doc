To meet the normal installation and operation requirements of YashanDB, it is recommended to log in as root user or a user with sudo privileges to **all servers** and perform configuration adjustments according to the items listed in this document.

## Check Server Name (YAC/Distributed Cluster Deployment)

When deploying a YAC or distributed cluster, server names within the same cluster cannot be the same.

```shell
## View the hostname of each server
# hostnamectl

## If the names are the same, corresponding adjustments need to be made
# hostnamectl set-hostname host0001
```

## Enable SSH Service

Check whether the SSH service on all servers is active:

```shell
# systemctl status sshd.service
```

If the output does not contain `active (running)`, use the following command to enable the SSH service:

```shell
# systemctl start sshd.service
```

> **Note**: 
>
> This installation manual assumes the default SSH port number is 22. If the actual planning is for a different port number, manual specification will be required during the installation process.

## Disable Swap Space

Swap space is an important supplement to physical memory, serving critical functions such as memory page swapping and emergency memory reclamation during database operation, directly affecting database stability and performance.

If the database is used to run latency-sensitive, high-stability, and I/O-intensive critical business applications, it is recommended to increase physical memory configuration and not allocate a separate swap partition. If a swap partition has already been allocated, the following command can be executed to disable system swap.

```shell
## Check if currently enabled
# swapon -s

## Disable swap
# echo "vm.swappiness = 0">> /etc/sysctl.conf
# sysctl -p
# swapoff -a

## If swap needs to be re-enabled subsequently, execute the following command
# swapon -a
```

If physical memory configuration is relatively tight, insufficient memory may trigger the OOM Killer mechanism. Completely disabling swap partitions might increase OOM occurrences. Consider lowering swap partition priority and treat this as a performance tuning observation item. For example, first set vm.swappiness to 1 to observe system behavior before deciding whether to completely disable swap, and periodically reassess memory configuration strategies based on business growth. For in-depth understanding of swappiness configuration details, please refer to the [Linux Kernel Documentation](https://www.kernel.org/doc/html/latest/admin-guide/sysctl/vm.html#swappiness).

```shell
## View current configuration
# sysctl -a | grep swappiness

## Modify configuration, set swappiness to 0
# echo "vm.swappiness = 0">> /etc/sysctl.conf
# sysctl -p
```

## Adjust Automatically Assigned Local Port Range

Use `cat /proc/sys/net/ipv4/ip_local_port_range` to check the available local port range. The lower limit is recommended to be greater than 30000 to reduce port conflicts.

```bash
## View current configuration
cat /proc/sys/net/ipv4/ip_local_port_range

## Modify configuration
# sysctl -w net.ipv4.ip_local_port_range='32768 60999' 
# echo "net.ipv4.ip_local_port_range=32768 60999" >> /etc/sysctl.conf
```

This operation is also effective for IPv6.

## Adjust Process VMA Limit

The Virtual Memory Area (VMA) of a process is a important data structure in the Linux kernel for managing process address spaces. In database environments, large numbers of shared memory segments, SPA memory allocations, and concurrent connections cause processes to create numerous VMA regions. When the number of VMAs exceeds the system's default limit, it may cause memory allocation failures, affecting normal database operation.  

When there are many concurrent users or complex SQL operations are executed, a single process may need to manage thousands of VMA regions. The Linux system's default limit of 65,536 VMAs is often insufficient in high-load database environments, potentially causing "Cannot allocate memory" or "ENOMEM" errors. It is recommended to increase the VMA upper limit, with a recommendation of 2,000,000 or higher.  

```bash
## View current configuration
# sysctl -a|grep vm.max_map_count

## Modify configuration
# sysctl -w vm.max_map_count=2000000
# echo "vm.max_map_count=2000000" >> /etc/sysctl.conf 
```

## Adjust Resource Limits

Adjust some resource limit values (use `ulimit -a` to check all resource limit values) to recommended or higher values. Detailed information is as follows, where `yashan` is an example of the planned database installation user (specific usernames should be based on actual planning).

|Resource Item |Recommended Value |OS Commands |Resource Item Description |
| ---------------- | ---------- |------------------------------- | -----------------|
| open files           | 1048576           | ulimit -n 1048576<br>vi /etc/security/limits.conf add:<br>`yashan soft nofile 1048576`<br>`yashan hard nofile 1048576`         | Number of files opened by the process   |
| max user processes   | 1048576           | ulimit -u 1048576<br>vi /etc/security/limits.conf add:<br/>`yashan soft nproc 1048576`<br/>`yashan hard nproc 1048576`         | Maximum number of user processes         |
| max memory size      | unlimited         | ulimit -m unlimited<br>vi /etc/security/limits.conf add:<br/>`yashan soft rss unlimited`<br/>`yashan hard rss unlimited` | Maximum memory usage by a process       |
| stack size           | 8192              | ulimit -s 8192<br>vi /etc/security/limits.conf add:<br/>`yashan soft stack 8192`<br/>`yashan hard stack 8192`            | Maximum stack space for the process     |

##  Adjust Network Connection Parameter

The TCP listen backlog queue is an important buffering mechanism in the OS's network subsystem, used to temporarily store TCP connection requests that have completed the three-way handshake but have not yet been accepted by the application. In high-concurrency database environments, proper configuration of the listen queue length directly affects the system's connection handling capability and shock resistance, and it is recommended to set this value to 32768 or higher.

```bash
## View Current Configuration 
# cat /proc/sys/net/core/somaxconn

## Adjust Parameter Value  
# sudo sysctl -w net.core.somaxconn=32768
```

##  Adjust Shared Memory Parameters

Shared memory is an important inter-process communication mechanism in Linux systems, serving critical data sharing and transmission functions in database environments. By properly configuring these three core parameters—shmmax, shmall, and shmmni—the database instance can be provided with sufficient shared memory resources to ensure stable and efficient operation.  

Use the `sysctl` command to view the current configuration of each parameter, and make corresponding adjustments based on actual conditions and recommended values. The numeric values in the commands are only examples. Replace them with actual values as needed.  

|Configuration Item |Recommended Value |OS Commands |Description |
| ---------------- | ---------- |------------------------------- | -----------------|
| shmmax | 50%-90% of physical memory, and greater than the database SGA | vi /etc/sysctl.conf add: `kernel.shmmax = 24696061952`, requires reboot to take effect | Maximum size of a single shared memory segment (in bytes) |
| ‌shmall | Calculated as shmmax / PAGE_SIZE (typically 4KB) | vi /etc/sysctl.conf add: `kernel.shmall = 3774873`, requires reboot to take effect | Total number of shared memory pages in the system |
| shmmni | Typically default 4096, can be increased for high concurrency scenarios | vi /etc/sysctl.conf add: `kernel.shmmni = 4096`, requires reboot to take effect | Total number of shared memory segments allowed in the system |

## Configure HugePages Memory

YashanDB utilizes HugePages memory to improve memory access efficiency. Standard HugePages memory is pre-allocated at system startup, while Transparent HugePages are dynamically allocated at runtime, which may cause runtime errors, leading to the termination of YashanDB.

It is recommended to configure the OS to enable HugePages memory and disable the Transparent HugePages option during installation. After YashanDB service installation, if you need to use HugePages memory, please modify the database configuration parameter [USE_LARGE_PAGES](../../../Reference Manual/Configuration Parameters.html#use_large_pages) to TRUE or ONLY.

For a deeper understanding of HugePages memory details, please refer to the [Linux Kernel Documentation](https://www.kernel.org/doc/html/latest/admin-guide/mm/hugetlbpage.html).

### Enable HugePages Memory

1. Execute the command below to check the current HugePages memory configuration (Echo information is subject to actual conditions):
   
   ```bash
   # grep -i huge /proc/meminfo
   AnonHugePages:         0 kB
   HugePages_Total:       0
   HugePages_Free:        0
   HugePages_Rsvd:        0
   HugePages_Surp:        0
   Hugepagesize:       2048 kB
   ```

   - AnonHugePages: Total memory currently used by Transparent HugePages, refer to this document to disable.
   - HugePages_Total: Total number of pages in huge page pool.
   - HugePages_Free: Number of free pages in huge page pool.
   - HugePages_Rsvd: Number of pages reserved by the system. This indicates that the application has requested the corresponding number of huge pages, but since the application has not yet accessed these pages, the system has not actually allocated these pages from physical memory. The application can still request `HugePages_Free - HugePages_Rsvd` huge pages.
   - HugePages_Surp: When `nr_overcommit_hugepages` is enabled, the system allows swapping out some huge pages, allowing applications to request pages that exceed the capacity of the huge page pool. If applications frequently access huge pages, it may negatively impact performance.
   - Hugepagesize: Size of huge pages, which varies across platforms; x86 platforms support 2M and 1G, typically 2M.

   If `HugePages_Total = 0`, `HugePages_Free = 0`, or `HugePages_Free - HugePages_Rsvd < YashanDB requirement`, then the system configuration needs to be modified to enable huge pages memory or increase the capacity of the huge page memory pool.

2. Estimate HugePages memory requirement, starting with estimating YashanDB's huge page memory requirement:

   |Configuration Parameter |Default Value |
   | ---- | ---- |
   |DATA_BUFFER_SIZE  |32M|
   |REDO_BUFFER_SIZE  |8M|
   |VM_BUFFER_SIZE    |16M|
   |SHARE_POOL_SIZE   |64M|
   |LARGE_POOL_SIZE   |8M|
   |DBWR_BUFFER_SIZE  |4M|
   |WORK_AREA_POOL_SIZE  |16M|
   |AUDIT_QUEUE_SIZE  |16M|

   Calculate the total memory required by YashanDB as UserSize, which is the sum of all the above configuration items (unconfigured items are calculated based on default values). If planning to use MMS tablespace, also estimate the total memory required for the MMS tablespace as MMSSize.

   - YashanDB needs at least huge page memory space: LargePageSize = (UserSize + MMSSize + 64M) * 1.2. 

   - The number of huge pages needed by YashanDB: HugePages = Ceil(LargePageSize / Hugepagesize).

   > **Caution**:
   > 
   > Allocating all memory to HugePage management may cause the system to fail to start or other abnormalities. **Please use with caution**.
   > 
   > During configuration, ensure the system's memory capacity is sufficient to support the huge pages configuration values.

3. Enable HugePages memory and change the related configurations based on the above estimates:
   
   This document uses a huge page size of 2M and requests 16 huge pages (i.e., planning for 32M HugePages memory) as an example; production environment configurations should be based on actual conditions.
   
   Edit the `/etc/sysctl.conf` file:
   
   ```bash
   # vi /etc/sysctl.conf
   ```
   
   Add the following configuration:
   ```text
   vm.nr_hugepages=16
   vm.nr_overcommit_hugepages=0
   ```

4. Reboot the system for the changes to take effect.
   ```bash
   # reboot
   ```

### Disable Transparent HugePages

1. Some operating systems enable the Transparent HugePages option by default. You can execute the following command to confirm:

   ```shell
   ## 1. Find the virtual file system path related to Transparent HugePages
   # find / -name *transparent_hugepage

   ## 2. Use the path to execute the following commands, common commands are as follows:
   # cat /sys/kernel/mm/transparent_hugepage/enabled
   ## or
   # cat /sys/kernel/mm/redhat_transparent_hugepage/enabled
   ```

   - If it displays `[always] madvise never` or `always [madvise] never`, it indicates Transparent HugePages is enabled.

   - If it displays `always madvise [never]`, it indicates Transparent HugePages is disabled.

2. Execute `vi /etc/default/grub`, add or modify the parameter `transparent_hugepage=never` in `GRUB_CMDLINE_LINUX`.
   
   ```text
      GRUB_TIMEOUT=5
      GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
      GRUB_DEFAULT=saved
      GRUB_DISABLE_SUBMENU=true
      GRUB_TERMINAL_OUTPUT="console"
      GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet transparent_hugepage=never"
      GRUB_DISABLE_RECOVERY="true"
   ```

3. Check the current system's boot type with the following command:
   ```bash
   # [ -d /sys/firmware/efi ] && echo UEFI || echo BIOS
   ```

   The paths for the boot files for the two boot types are:
   - BIOS: `/boot/grub2/grub.cfg`

   - UEFI: `/boot/efi/EFI/<distro_name>/grub.cfg`, where `distro_name` is the system distribution version name, such as `ubuntu`, `fedora`, `debian`, etc.

4. Execute the `grub2-mkconfig` command to reconfigure `grub.cfg`.

   ```bash
   ## BIOS boot
   # grub2-mkconfig -o /boot/grub2/grub.cfg
   ## UEFI boot
   # grub2-mkconfig -o /boot/efi/EFI/<distro_name>/grub.cfg
   ```

5. Reboot the operating system to make the configuration permanent.
   ```bash
   # reboot
   ```

6. Verify that Transparent HugePages is disabled:
   ```bash
   ## Red Hat Enterprise Linux kernel
   # cat /sys/kernel/mm/redhat_transparent_hugepage/enabled

   ## Other kernels
   # cat /sys/kernel/mm/transparent_hugepage/enabled
   ```

   It should display `always madvise [never]`.

## Enable Core Dumps (Optional)

When building a testing or experimental environment, it is recommended to enable the generation of core dumps (use `ulimit -c` to check the current status) for easier issue localization.

In a production environment, **it is not recommended** to enable this switch to avoid affecting system efficiency.

```bash 
# ulimit -c unlimited 
# vi /etc/security/limits.conf

## Add the following information and save, where yashan is the planned database installation user example, specific usernames should be based on actual planning

yashan soft core unlimited
yashan hard core unlimited
```

## Clean Shared Memory

This step is only applicable to the YAC Deployment and  Distributed Cluster Deployment. Other scenarios should be skipped.

Shared memory needs to be cleared on all servers.

```shell
# ipcrm -a
```
