Server checks the configuration file listing the various indicators checked by the YashanDB server.

***Example***: foryascheck.toml

```toml
[yascheck]
  [yascheck.hardware]
    [yascheck.hardware.cpu]
      ignore = false
      physical = "cat /proc/cpuinfo | grep 'physical id' | sort | uniq | wc -l"
      logical = "cat /proc/cpuinfo | grep processor | wc -l"
      cores_per_cpu = "cat /proc/cpuinfo | grep 'cpu cores' | uniq | awk -F : '{print $2}'"
    [yascheck.hardware.memory]
      ignore = false
    [yascheck.hardware.disk]
      ignore = false
    [yascheck.hardware.network]
      ignore = false
      dns = "grep nameserver /etc/resolv.conf| grep -v '#' | awk '{print $2}' | tr '\\n' ';' | sed 's/;$//'"
      gateway = "ip route | grep default | awk '{print $3}'"
      status = "ping 127.0.0.1 -c 3"
  [yascheck.system_params]
    [yascheck.system_params.base_info]
      ignore = false
      selinux = "/usr/sbin/sestatus | grep 'SELinux status: ' | awk '{print $3}'"
      io_schedule = "cat /sys/block/sda/queue/scheduler | awk -F [ '{print $2}' | awk -F ] '{print $1}'"
      en_lang = "echo $LANG"
    [yascheck.system_params.sysctl]
      sysctl = "sysctl -a | grep vm | grep 'swappiness\\|dirty_ratio\\|dirty_background_ratio\\|dirty_expire_centisecs\\|vfs_cache_pressure\\|min_free_kbytes\\|overcommit_memory\\|overcommit_ratio\\|max_map_count'"
      ignore = false
    [yascheck.system_params.users]
      ignore = false
      user_privilege = "\n        for user in $(cat /etc/passwd | awk -F: '{print $1}'); do\n            if [ $(id -u $user) -eq 0 ]; then\n                echo $user\n            fi\n        done"
      sudo_users = "grep -v \"^#\" /etc/sudoers | grep -v \"^Defaults\" | sed '/^$/d' | awk '{print $1}' | uniq | tr '\\n' ' '"
      need_login_users = "\n        for shell in $(grep -v \"/sbin/nologin\" /etc/shells); do\n            for username in $(grep \"$shell\" /etc/passwd | awk -F: '{print $1}'); do \n            cat /etc/passwd | grep -w \"$username\" | grep -w \"$shell\" | awk -F: '{print $1,$3,$4,$6,$7}'\n            done\n        done"
  [yascheck.service]
    [yascheck.service.sys_service]
      ignore = false
      enabled_service = "systemctl list-unit-files --type=service --state=enabled --no-pager | grep \"enabled\" | awk '{print $1}'"
      running_service = "systemctl list-units --type=service --state=running --no-pager | grep \".service\" | awk '{print $1}'"
    [yascheck.service.ssh_info]
      ignore = false
      enabled = "systemctl list-unit-files --type=service --state=enabled --no-pager | grep \"enabled\" | awk '{print $1}'| grep sshd"
      Status = "systemctl is-active sshd"
      config_path = "/etc/ssh/sshd_config"
      protocol = "cat /etc/ssh/sshd_config | grep Protocol | awk '{print $2}'"
      permit_root_login = "cat /etc/ssh/sshd_config | grep PermitRootLogin | awk 'NR==1'"
    [yascheck.service.firewalld]
      ignore = false
      status = "systemctl is-active firewalld"
      enabled = "systemctl list-unit-files --type=service --state=enabled --no-pager | grep \"enabled\" | awk '{print $1}'| grep firewalld"
      open_ports = "firewall-cmd --zone=public --list-ports"
    [yascheck.service.listening_info]
      ignore = false
      listening_service = "/usr/sbin/ss -tlun"
  [yascheck.process_consumption]
    ignore = false
    cpu_top10 = "ps aux | awk '{print $2, $1, $3, $4, $5, $6, $10, $11}' | sort -k3rn | head -n 10"
    mem_top10 = "ps aux | awk '{print $2, $1, $3, $4, $5, $6, $10, $11}' | sort -k4rn | head -n 10"
  [yascheck.network_io]
    ignore = true
    [yascheck.network_io.server]
      kill_server = false
      cmd = "iperf=/data/yashan/yasdb_home/yashandb/22.2.0.9/ext/bin/iperf;$iperf -s -p 9999 -D"
    [yascheck.network_io.client]
      cmd = "iperf=/data/yashan/yasdb_home/yashandb/22.2.0.9/ext/bin/iperf;$iperf -c 127.0.0.1 -p 9999 -i 1 -d -t 10"
  [yascheck.disk_io]
    ignore = true
    cmd = "fio=/data/yashan/yasdb_data/yashandb/22.2.0.9/ext/bin/fio;$fio --randrepeat=1 --ioengine=sync --direct=1 --gtod_reduce=1 --name=test --directory=./ --bs=4k --size=4G --readwrite=randrw --rwmixread=75 --runtime=10s --output-format=json"
```
