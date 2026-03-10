This article will introduce the key steps for tuning the TPC-C test applicable to the Kunpeng environment, including tuning at the database layer and the database operating environment.

## Environment Preparation

- Hardware environment information is as follows:
    - Server: 2 Kunpeng 920, 128-core CPU, 4 NUMA nodes

    - Network card: Kunpeng matching network card, 10GE

    - Hard disk: SAS SSD (NVME SSD preferred)

- Software environment information is as follows:

    - Database: YashanDB v23.3 and above

    - TPC-C client: BenchMarkSQL5.0

## Tuning Operations

### 1. Operating System Tuning

In a NUMA architecture, if tuning needs to be done at the operating system level, finer control over the interrupt load can be achieved by disabling irqbalance to avoid unnecessary resource waste and delays.

```bash
systemctl stop irqbalance
```

### 2. Network Configuration Tuning

For TPC-C stress testing, it is generally recommended to deploy the client and server on different servers to achieve better results. In this testing scenario, the network transmission between the client and the server will be a significant bottleneck. At this point, the server's network card queue interrupt handling can be bound to the CPU on the NUMA node where the network card is located to optimize network performance.

Common commands related to network configuration are as follows:

-  `ifconfig` is used to view the network card device name.
- `ethtool -i %NETWORK_CARD_NAME%` is used to view the network card bus-info.
- `lspci -vvvs %NETWORK_CARD_NAME%bus-info` is used to view the NUMA node where the network card is located.
- `ethtool -l %NETWORK_CARD_NAME%` is used to view the interrupt queue information of the network card.
- `ethtool -L %NETWORK_CARD_NAME% combined %INTERRUPT_QUEUES_NUMBER%` is used to set the number of interrupt queues for the network card.

```bash
// View the interrupt queue messages of the network card in the test environment
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
// According to the echo information, the maximum number of interrupt queues in the current environment is 63, and the set number of interrupt queues is 16
```

There are 4 NUMA nodes in the test environment, and each NUMA node has 32 cores. The optimal number of interrupt queues for the network card is set to 32, and the network card interrupt queues are bound to the CPU on NUMA node0. The script is as follows; sh irqbind.sh %NETWORK CARD%bus-info binds the network card interrupt queues to CPUs 0-31.
```bash
#!/bin/bash
# irqbind.sh $1 is the network card device buf-info
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

### 3. Database Tuning

#### 3.1 Database Server Core Binding

To improve the processing capability of the network module, the database server should avoid occupying the CPUs bound to the network card as much as possible; the database process can be bound to other NUMA nodes.

After binding the database process to a NUMA node, it will only be able to use the corresponding CPU and memory resources, so ensure that the corresponding resources are sufficient; otherwise, performance may decrease.

In the test environment, according to TPC-C best practices, the CPU usage of the database process is about 5000%, and the database process can be bound to the corresponding NUMA node using the following command.

```bash
numactl --cpunodebind=1,2,3 --membind=1,2,3 yasdb open
```

#### 3.2 Database Configuration Parameter Tuning (Applicable to Various Architectures)

The following lists the recommended database configuration parameters for TPC-C testing. Specific parameter values can be further adjusted according to the actual environment.

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

#### 3.3 Benchmark Tuning (Applicable to Various Architectures)

Combine the data characteristics to reasonably use table partitioning.

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

#### 3.4 Database File Partition Storage (Applicable to Various Architectures)

Based on the read and write frequency characteristics of various data files, rationally plan their storage locations. For example, store the database redo files separately from other files on different SSDs to ensure that the disk IO of the redo files is not contended by other files.