本文将介绍适用于鲲鹏环境的TPC-C测试调优的关键步骤，包括数据库层面的调优和数据库运行环境的调优。

## 环境准备

- 硬件环境信息如下：
    - 服务器：2台鲲鹏920，128核CPU，4个NUMA节点

    - 网卡：鲲鹏配套网卡，10GE

    - 硬盘：SAS SSD（NVME SSD更佳）

- 软件环境信息如下：

    - 数据库：YashanDB v23.3及以上

    - TPC-C客户端：BenchMarkSQL5.0

## 调优操作

### 1. 操作系统调优

在NUMA架构中，如需从操作系统层面进行调优，可以通过关闭irqbalance实现对中断负载进行更细粒度的控制，避免不必要的资源浪费和延迟。

```bash
systemctl stop irqbalance
```

### 2. 网络配置调优

进行TPC-C压测时，为达到更佳效果通常推荐将客户端和服务端部署在不同服务器上，而这种测试场景中客户端和服务端的网络传输将是较大的瓶颈。此时，可以将服务端网卡队列中断处理绑定在网卡设备所在NUMA节点的CPU上，优化网络性能。

网络配置相关的常用命令如下：

- `ifconfig`用于查看网卡设备名。
- `ethtool -i %NETWORK_CARD_NAME%`用于查看网卡bus—info。
- `lspci -vvvs %NETWORK_CARD_NAME%bus-info`用于查看网卡所在numa node。
- `ethtool -l %NETWORK_CARD_NAME%`用于查看网卡中断队列信息。
- `ethtool -L %NETWORK_CARD_NAME% combined %INTERRUPT_QUEUES_NUMBER%`用于设置网卡中断队列数。

```bash
// 查看测试环境中网卡中断队列消息
$ ethtool -l enp1s0f0np0
Channel parameters for enp1s0f0np0:
Pre-set maximums:
RX:             0
TX:             0
Other:          0
Combined:       63
Current hardware settings:
RX:             0
TX:             0
Other:          0
Combined:       16
// 根据回显信息，当前环境中最大中断队列数为63，设置的中断队列数为16
```

测试环境存在4个NUMA node且每个NUMA node为32核CPU，网卡中断队列数设置为32最优，并将网卡中断队列绑定在NUMA node0的CPU上。脚本如下，sh irqbind.sh 网卡设备bus-info，将网卡中断队列绑定在0-31号CPU。
```bash
#!/bin/bash
# irqbind.sh $1为网卡设备buf-info信息
irq_list=(`cat /proc/interrupts | grep $1 | awk -F: '{print $1}'`)

cpunum=0
for irq in ${irq_list[@]}
do
echo $cpunum &gt; /proc/irq/$irq/smp_affinity_list
echo `cat /proc/irq/$irq/smp_affinity_list`
(( cpunum+=1 ))
(( cpunum %= 32))
done
```

### 3. 数据库调优

#### 3.1 数据库服务端绑核

为了提高网络模块处理能力，数据库服务端应尽可能不占用网卡绑定的CPU，可以将数据库进程绑定在其他NUMA node上。

数据库进程绑定NUMA node后，将只能使用对应的CPU和内存资源，请确保相应资源充足，否则性能可能反而会下降。

测试环境中按照TPC-C最佳实践，数据库进程占用CPU约5000%左右，可通过下述命令将数据库进程绑定在相应的NUMA node。

```bash
numactl --cpunodebind=1,2,3 --membind=1,2,3 yasdb open
```

#### 3.2 数据库配置参数调优（各类架构通用）

下面列举了TPC-C测试推荐的数据库配置参数，具体参数值可根据实际环境进一步调整。

```bash
DATA_BUFFER_SIZE = "200G"
_DATA_BUFFER_PARTS=8
VM_BUFFER_SIZE="50G"
VM_BUFFER_PARTS=8
REDO_BUFFER_SIZE="64M"
REDO_BUFFER_PARTS = 8
LARGE_POOL_SIZE="2G"
UNDO_RETENTION=10
UNDO_SHRINK_ENABLED="FALSE"
_SESSION_RESERVED_CURSORS=64
LOCK_POOL_SIZE="3G"
SHARE_POOL_SIZE="30G"
SQL_POOL_PARTS = 8
COMMIT_LOGGING = "BATCH"
DBWR_COUNT=8
DBWR_BUFFER_SIZE="16M"
CHECKPOINT_TIMEOUT=1000000000
CHECKPOINT_INTERVAL="200G"
```

#### 3.3 benchmark调优（各类架构通用）

结合数据特征，合理使用表分区。

```sql
CREATE TABLE bmsql_config
(
    cfg_name  VARCHAR2(30) PRIMARY key,
    cfg_value VARCHAR2(50)
) TABLESPACE users PCTFREE 50
PARTITION BY hash (cfg_name) partitions 64;

CREATE TABLE bmsql_warehouse
(
    w_id       INTEGER NOT null,
    w_ytd      NUMBER(12,2),
    w_tax      NUMBER(4,4),
    w_name     VARCHAR2(10),
    w_street_1 VARCHAR2(20),
    w_street_2 VARCHAR2(20),
    w_city     VARCHAR2(20),
    w_state    CHAR(2),
    w_zip      CHAR(9)
) TABLESPACE users PCTFREE 50
PARTITION BY RANGE (w_id) INTERVAL(1) (
PARTITION VALUES LESS than (1)
);

CREATE TABLE bmsql_district
(
    d_w_id      INTEGER NOT null,
    d_id        INTEGER NOT null,
    d_ytd       NUMBER(12,2),
    d_tax       NUMBER(4,4),
    d_next_o_id INTEGER,
    d_name      VARCHAR2(10),
    d_street_1  VARCHAR2(20),
    d_street_2  VARCHAR2(20),
    d_city      VARCHAR2(20),
    d_state     CHAR(2),
    d_zip       CHAR(9)
) TABLESPACE users PCTFREE 50
PARTITION BY RANGE (d_w_id) INTERVAL(1) (
PARTITION VALUES LESS than (1)
);

CREATE TABLE bmsql_customer
(
    c_w_id         INTEGER NOT null,
    c_d_id         INTEGER NOT null,
    c_id           INTEGER NOT null,
    c_discount     NUMBER(4,4),
    c_credit       CHAR(2),
    c_last         VARCHAR2(16),
    c_first        VARCHAR2(16),
    c_credit_lim   NUMBER(12,2),
    c_balance      NUMBER(12,2),
    c_ytd_payment  NUMBER(12,2),
    c_payment_cnt  INTEGER,
    c_delivery_cnt INTEGER,
    c_street_1     VARCHAR2(20),
    c_street_2     VARCHAR2(20),
    c_city         VARCHAR2(20),
    c_state        CHAR(2),
    c_zip          CHAR(9),
    c_phone        CHAR(16),
    c_since        TIMESTAMP,
    c_middle       CHAR(2),
    c_data         VARCHAR2(500)
) TABLESPACE users PCTFREE 50
PARTITION BY RANGE (c_w_id) INTERVAL(1) (
PARTITION VALUES LESS than (1)
);

CREATE TABLE bmsql_history
(
    hist_id  INTEGER,
    h_c_id   INTEGER,
    h_c_d_id INTEGER,
    h_c_w_id INTEGER,
    h_d_id   INTEGER,
    h_w_id   INTEGER,
    h_date   TIMESTAMP,
    h_amount NUMBER(6,2),
    h_data   VARCHAR2(24)
) TABLESPACE users PCTFREE 50
PARTITION BY RANGE (h_w_id) INTERVAL(1) (
PARTITION VALUES LESS than (1)
);

CREATE TABLE bmsql_new_order
(
    no_w_id INTEGER NOT null,
    no_d_id INTEGER NOT null,
    no_o_id INTEGER NOT null
) TABLESPACE users PCTFREE 50
PARTITION BY RANGE (no_w_id) INTERVAL(1) (
PARTITION VALUES LESS than (1)
);

CREATE TABLE bmsql_oorder
(
    o_w_id       INTEGER NOT null,
    o_d_id       INTEGER NOT null,
    o_id         INTEGER NOT null,
    o_c_id       INTEGER,
    o_carrier_id INTEGER,
    o_ol_cnt     INTEGER,
    o_all_local  INTEGER,
    o_entry_d    TIMESTAMP
) TABLESPACE users PCTFREE 50
PARTITION BY RANGE (o_w_id) INTERVAL(1) (
PARTITION VALUES LESS than (1)
);

CREATE TABLE bmsql_order_line
(
    ol_w_id        INTEGER NOT null,
    ol_d_id        INTEGER NOT null,
    ol_o_id        INTEGER NOT null,
    ol_number      INTEGER NOT null,
    ol_i_id        INTEGER NOT null,
    ol_delivery_d  TIMESTAMP,
    ol_amount      NUMBER(6,2),
    ol_supply_w_id INTEGER,
    ol_quantity    INTEGER,
    ol_dist_info   CHAR(24)
) TABLESPACE users PCTFREE 50
PARTITION BY RANGE (ol_w_id) INTERVAL(1) (
PARTITION VALUES LESS than (1)
);

CREATE TABLE bmsql_item
(
    i_id    INTEGER NOT null,
    i_name  VARCHAR2(24),
    i_price NUMBER(5,2),
    i_data  VARCHAR2(50),
    i_im_id INTEGER
) TABLESPACE users PCTFREE 50
PARTITION BY hash (i_id) partitions 64;

CREATE TABLE bmsql_stock
(
    s_w_id       INTEGER NOT null,
    s_i_id       INTEGER NOT null,
    s_quantity   INTEGER,
    s_ytd        INTEGER,
    s_order_cnt  INTEGER,
    s_remote_cnt INTEGER,
    s_data       VARCHAR2(50),
    s_dist_01    CHAR(24),
    s_dist_02    CHAR(24),
    s_dist_03    CHAR(24),
    s_dist_04    CHAR(24),
    s_dist_05    CHAR(24),
    s_dist_06    CHAR(24),
    s_dist_07    CHAR(24),
    s_dist_08    CHAR(24),
    s_dist_09    CHAR(24),
    s_dist_10    CHAR(24)
) TABLESPACE users PCTFREE 50
PARTITION BY RANGE (s_w_id) INTERVAL(1) (
PARTITION VALUES LESS than (1)
);

ALTER TABLE bmsql_warehouse ADD CONSTRAINT bmsql_warehouse_pkey
    PRIMARY key (w_id) USING INDEX LOCAL;

ALTER TABLE bmsql_district ADD CONSTRAINT bmsql_district_pkey
    PRIMARY key (d_w_id, d_id) USING INDEX LOCAL;

ALTER TABLE bmsql_customer ADD CONSTRAINT bmsql_customer_pkey
    PRIMARY key (c_w_id, c_d_id, c_id) USING INDEX LOCAL;

CREATE INDEX bmsql_customer_idx1
  ON  bmsql_customer (c_w_id, c_d_id, c_last, c_first) LOCAL TABLESPACE users PCTFREE 50;

ALTER TABLE bmsql_oorder ADD CONSTRAINT bmsql_oorder_pkey
    PRIMARY key (o_w_id, o_d_id, o_id) USING INDEX LOCAL;

ALTER TABLE bmsql_new_order ADD CONSTRAINT bmsql_new_order_pkey
    PRIMARY key (no_w_id, no_d_id, no_o_id) USING INDEX LOCAL;

ALTER TABLE bmsql_order_line ADD CONSTRAINT bmsql_order_line_pkey
    PRIMARY key (ol_w_id, ol_d_id, ol_o_id, ol_number) USING INDEX LOCAL;

CREATE UNIQUE INDEX bmsql_stock_idx1 ON bmsql_stock(s_w_id, s_i_id) LOCAL TABLESPACE users PCTFREE 50;

ALTER TABLE bmsql_stock ADD CONSTRAINT bmsql_stock_pkey
    PRIMARY key (s_w_id, s_i_id) USING INDEX LOCAL;

ALTER TABLE bmsql_item ADD CONSTRAINT bmsql_item_pkey
    PRIMARY key (i_id) USING INDEX LOCAL;
```

#### 3.4 数据库文件分盘存储（各类架构通用）

结合各类数据文件的读写频次等特点，合理规划其存储位置。例如，将数据库redo文件与其他文件存储在不同的SSD盘，尽可能保障redo文件的磁盘IO不被其他文件争抢。
