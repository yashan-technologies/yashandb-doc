## Check Server Name (YAC/Distributed Cluster Deployment)

When deploying a YAC or distributed cluster, server names within the same cluster cannot be the same.

```shell
## View the hostname of each server
# hostnamectl

## If the names are the same, corresponding adjustments need to be made
# hostnamectl set-hostname host0001
```

## Disable Swap Partition

Use `sysctl -a | grep swappiness` to check the current status. It is recommended to set it to 0 to reduce memory swapping and avoid impacting system performance.

```bash
# sysctl -w vm.swappiness=0
# echo "vm.swappiness=0">> /etc/sysctl.conf
```

## Adjust Automatically Assigned Local Port Range

Use `cat /proc/sys/net/ipv4/ip_local_port_range` to check the available local port range. The lower limit is recommended to be greater than 30000 to reduce port conflicts.

```bash
# sysctl -w net.ipv4.ip_local_port_range='32768 60999' 
# echo "net.ipv4.ip_local_port_range=32768 60999" >> /etc/sysctl.conf
```

This operation is also effective for IPv6.

## Adjust Process VMA Limit

Use `sysctl -a|grep vm.max_map_count` to check the current high value, which is recommended to be greater than 2000000 to increase the VMA size that the database process can have.

```bash
# sysctl -w vm.max_map_count=2000000
# echo "vm.max_map_count=2000000" >> /etc/sysctl.conf 
```

## Adjust Resource Limits

Adjust some resource limit values (use `ulimit -a` to check all resource limit values) to recommended or higher values. Detailed information is as follows, where `yashan` is an example of the planned database installation user (specific usernames should be based on actual planning).

|Resource Item |Recommended Value |COMMAND |Resource Item Description |
| ---------------- | ---------- |------------------------------- | -----------------|
| open files           | 1048576           | ulimit -n 1048576<br>vi /etc/security/limits.conf add:<br>`yashan soft nofile 1048576`<br>`yashan hard nofile 1048576`         | Number of files opened by the process   |
| max user processes   | 1048576           | ulimit -u 1048576<br>vi /etc/security/limits.conf add:<br/>`yashan soft nproc 1048576`<br/>`yashan hard nproc 1048576`         | Maximum number of user processes         |
| max memory size      | unlimited         | ulimit -m unlimited<br>vi /etc/security/limits.conf add:<br/>`yashan soft rss unlimited`<br/>`yashan hard rss unlimited` | Maximum memory usage by a process       |
| stack size           | 8192              | ulimit -s 8192<br>vi /etc/security/limits.conf add:<br/>`yashan soft stack 8192`<br/>`yashan hard stack 8192`            | Maximum stack space for the process     |

## Configure HugePages Memory

YashanDB utilizes HugePages memory to improve memory access efficiency. Standard HugePages memory is pre-allocated at system startup, while Transparent HugePages are dynamically allocated at runtime, which may cause runtime errors, leading to the termination of YashanDB.

It is recommended to configure the operating system to enable HugePages memory and disable the Transparent HugePages option during installation. After YashanDB service installation, if you need to use HugePages memory, please modify the database configuration parameter [USE_LARGE_PAGES](../../../Reference Manual/Configuration Parameters.html#use_large_pages) to TRUE or ONLY.

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
