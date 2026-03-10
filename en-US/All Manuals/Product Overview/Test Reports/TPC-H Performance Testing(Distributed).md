This section will introduce how to conduct TPC-H testing on the YashanDB ISC distributed database.

## Introduction to TPC-H

TPC-H (Transaction Processing Performance Council - H) is a benchmark set created by the Transaction Processing Performance Council of the United States, designed to simulate decision support applications. This commercial benchmark can comprehensively evaluate the overall business computation capabilities of systems, demanding higher requirements from vendors, while also possessing general commercial applicability. It is widely used in banking credit analysis, credit card analysis, telecommunications operations analysis, tax analysis, and tobacco industry decision analysis.

The TPC-H benchmark is developed from TPC-D (a standard established by TPC in 1994 for testing decision support systems). TPC-H implements a data warehouse in 3NF, which includes 8 basic relations, with the primary evaluation metric being the response time of each query, i.e., the time taken from submitting the query to returning the results.

## Environmental Requirements

- JDK: Required for JDBC connections to the database; OpenJDK version 1.8.0_412 or above is recommended. Please refer to the [OpenJDK official website](https://openjdk.org/install/) for installation instructions.
- make: Required for compiling the TPC-H data generation tool; GUN Make version 3.82 or above is recommended. Please refer to the [GUN Make official website](https://www.gnu.org/software/make/#download) for installation instructions.
- gcc: Required for compiling the TPC-H data generation tool; GCC version 10.2.1 or above is recommended. Please refer to the [GCC official website](https://gcc.gnu.org/) for installation instructions.
- YashanDB JDBC driver: Required for JDBC connections to the database; version 1.6.1 or above is recommended. Please refer to the [YashanDB JDBC Driver Installation](../../开发手册/JDBC驱动/安装JDBC驱动) for installation instructions.
- Python: Required for executing TPC-H test scripts; Python version 3.6.8 or above is recommended. Please refer to the [Python official website](https://www.python.org/downloads/) for installation instructions.
- JPype1: A dependency library for executing TPC-H test scripts; version 1.3.0 or above is recommended. Please refer to the [PyPI official website](https://pypi.org/project/JPype1/) for installation instructions.
- TPC-H Tool: A tool for generating TPC-H test related data; version 3.0.0 is recommended. Please refer to the [TPC-H official website](https://www.tpc.org/tpc_documents_current_versions/download_programs/tools-download-request5.asp?bm_type=TPC-H&bm_vers=3.0.0&mode=CURRENT-ONLY) for installation instructions.
- YashanDB database: The database for TPC-H tests; version 23.2.8.100 or subsequent patch versions are recommended. Please refer to [ISC Distributed Cluster Deployment](../../安装和升级/安装部署/YashanDB服务端安装（命令行）/存算一体分布式集群部署) for installation instructions. The TPC-H test parameter recommendations feature is temporarily only supported in version 23.2.

## Distributed Database Deployment

Four servers with identical hardware specifications need to be prepared, with recommended specifications of at least 16C32G; otherwise, it will impact the performance testing results of TPC-H SF100. For performance testing of TPC-H SF1000, server hardware specifications should be at least 32C256G. Additionally, the cluster deployment file must be manually adjusted to set the USERS_DATASPACE_SCALE_OUT_FACTOR value for the CN nodes; it is recommended to set this value to double the number of CPU cores and not exceeding 128.

The server requirements and planning for distributed nodes/node groups are as shown in the following table.

|Server Name |Server IP |Node/Node Group |Memory |CPU |Disk |
|----|----|----|----|----|----|
| Server 1    | 192.168.1.1  | MN: 1-1 (Master), CN: 2-1 (Master) | 64G    | 16 cores | HDD  |
| Server 2    | 192.168.1.2  | DN: 3-1 (Master)       | 64G    | 16 cores | SSD  |
| Server 3    | 192.168.1.3  | DN: 4-1 (Master)       | 64G    | 16 cores | SSD  |
| Server 4    | 192.168.1.4  | DN: 5-1 (Master)       | 64G    | 16 cores | SSD  |

If the server hardware specifications are inconsistent, the [degree of parallelism](../../参考手册/配置参数.html#degreeofparallel) for CN nodes must be manually set to fully utilize the CPU resources of DN nodes, and it is recommended to set this to the number of CPU cores on the DN node's server.

## Running TPC-H Tests

### Database Parameter Optimization

YashanDB supports parameter recommendations for TPC-H testing under ISC Distributed Cluster Deployment, which can be configured using the [Advanced Parameter Recommendations](../../开发手册/PL参考手册/内置高级包/DBMS_PARAM).

To conduct a baseline test for TPC-H SF100, you can execute the following command to apply the optimized parameters. After successful execution, the database must be restarted for the configuration to take effect.

```sql
EXEC DBMS_PARAM.OPTIMIZE(True, 'LSC', scene=>'TPCH', scale_factor=>100);
SELECT DBMS_PARAM.SHOW_RECOMMEND() FROM dual;
EXEC DBMS_PARAM.APPLY_RECOMMEND();
```

> **Note**:
>
> - The recommended TPC-H parameter is only applicable during the query phase of TPC-H tests. It is advised to execute this command after completing data import and statistics collection preparation steps.
> - Parameter recommendations are only optimal for TPC-H testing performance under sufficient hardware specifications. For instance, in the case of TPC-H SF100, the DN nodes require at least specifications of 16C32G.

### Installing TPC-H Tool

1. [Download](https://www.tpc.org/tpc_documents_current_versions/download_programs/tools-download-request5.asp?bm_type=TPC-H&bm_vers=3.0.0&mode=CURRENT-ONLY) the TPC-H Tool.
2. Unzip the file and navigate to the specified path.
    ```shell
    $ unzip TPC-H_Tools_v3.0.0.zip
    $ cd TPC-H_Tools_v3.0.0/dbgen
    ```
3. Add YashanDB compilation options to the tpcd.h header file.
    ```shell
    $ vi tpcd.h
    #ifdef YASHANDB
    #define GEN_QUERY_PLAN ""
    #define START_TRAN ""
    #define END_TRAN "COMMIT"
    #define SET_OUTPUT ""
    #define SET_ROWCOUNT "limit %d;\n"
    #define SET_DBASE ""
    #endif
    ```
4. Modify the CC, DATABASE, MACHINE, WORKLOAD, and other parameters in the compile template file to generate a Makefile.
    ```shell
    $ cp makefile.suite Makefile
    $ vi Makefile
    CC = gcc
    DATABASE = YASHANDB
    MACHINE = LINUX
    WORKLOAD = TPCH
    ```
5. Compile the Makefile.
    ```shell
    $ make
    ```

### Generating Data

You can specify parameters to generate any data scale of TPC-H specifications according to needs, such as 1G, 10G, 100G, 1T, etc. This document takes 100G as an example.

```shell
$ ./dbgen -s 100
$ mkdir -p /data/tpch/SF100
$ mv *.tbl /data/tpch/SF100
```

### Creating Test User and Test Tables

1. Create a test user and connect to the database using this user.
    ```sql
    CREATE USER regress IDENTIFIED BY regress;
    GRANT dba TO regress;
    conn regress/regress;
    ```

2. Create test tables.
    ```sql
    DROP TABLE IF EXISTS region;
    CREATE TABLE region (
    R_REGIONKEY       INTEGER         NOT NULL,
    R_NAME            CHAR(25)        NOT NULL,
    R_COMMENT         VARCHAR(152)
    ) ORGANIZATION LSC
    ORDER BY (R_REGIONKEY);
    
    DROP TABLE IF EXISTS nation;
    CREATE TABLE nation (
    N_NATIONKEY       INTEGER         NOT NULL,
    N_NAME            CHAR(25)        NOT NULL,
    N_REGIONKEY       INTEGER         NOT NULL,
    N_COMMENT         VARCHAR(152)
    ) ORGANIZATION LSC
    ORDER BY (N_NATIONKEY);
    
    DROP TABLE IF EXISTS supplier;
    CREATE TABLE supplier (
    S_SUPPKEY         INTEGER         NOT NULL,
    S_NAME            CHAR(25)        NOT NULL,
    S_ADDRESS         VARCHAR(40)     NOT NULL,
    S_NATIONKEY       INTEGER         NOT NULL,
    S_PHONE           CHAR(15)        NOT NULL,
    S_ACCTBAL         DECIMAL(15,2)   NOT NULL,
    S_COMMENT         VARCHAR(101)    NOT NULL
    ) ORGANIZATION LSC
    ORDER BY (S_SUPPKEY)
    PARTITION BY HASH(S_SUPPKEY) PARTITIONS AUTO;
    
    DROP TABLE IF EXISTS part;
    CREATE TABLE part (
    P_PARTKEY         INTEGER         NOT NULL,
    P_NAME            VARCHAR(55)     NOT NULL,
    P_MFGR            CHAR(25)        NOT NULL ENCODING DICTIONARY(RLE),
    P_BRAND           CHAR(10)        NOT NULL ENCODING DICTIONARY(RLE),
    P_TYPE            VARCHAR(25)     NOT NULL ENCODING DICTIONARY(RLE),
    P_SIZE            INTEGER         NOT NULL,
    P_CONTAINER       CHAR(10)        NOT NULL ENCODING DICTIONARY(RLE),
    P_RETAILPRICE     DECIMAL(15,2)   NOT NULL,
    P_COMMENT         VARCHAR(23)     NOT NULL
    ) ORGANIZATION LSC
    ORDER BY (P_PARTKEY)
    PARTITION BY HASH(P_PARTKEY) PARTITIONS AUTO;
    
    DROP TABLE IF EXISTS partsupp;
    CREATE TABLE partsupp (
    PS_PARTKEY        INTEGER         NOT NULL,
    PS_SUPPKEY        INTEGER         NOT NULL,
    PS_AVAILQTY       INTEGER         NOT NULL,
    PS_SUPPLYCOST     DECIMAL(15,2)   NOT NULL,
    PS_COMMENT        VARCHAR(199)    NOT NULL
    ) ORGANIZATION LSC
    ORDER BY (PS_SUPPKEY, PS_PARTKEY)
    PARTITION BY HASH(PS_PARTKEY) PARTITIONS AUTO;
    
    DROP TABLE IF EXISTS customer;
    CREATE TABLE customer (
    C_CUSTKEY         INTEGER         NOT NULL,
    C_NAME            VARCHAR(25)     NOT NULL,
    C_ADDRESS         VARCHAR(40)     NOT NULL,
    C_NATIONKEY       INTEGER         NOT NULL,
    C_PHONE           CHAR(15)        NOT NULL,
    C_ACCTBAL         DECIMAL(15,2)   NOT NULL,
    C_MKTSEGMENT      CHAR(10)        NOT NULL ENCODING DICTIONARY(RLE),
    C_COMMENT         VARCHAR(117)    NOT NULL
    ) ORGANIZATION LSC
    ORDER BY (C_CUSTKEY)
    PARTITION BY HASH(C_CUSTKEY) PARTITIONS AUTO;
    
    DROP TABLE IF EXISTS orders;
    CREATE TABLE orders (
    O_ORDERKEY        INTEGER          NOT NULL,
    O_CUSTKEY         INTEGER         NOT NULL,
    O_ORDERSTATUS     CHAR(1)         NOT NULL,
    O_TOTALPRICE      DECIMAL(15,2)   NOT NULL,
    O_ORDERDATE       DATE            NOT NULL,
    O_ORDERPRIORITY   CHAR(15)        NOT NULL ENCODING DICTIONARY(RLE),
    O_CLERK           CHAR(15)        NOT NULL ENCODING DICTIONARY(RLE),
    O_SHIPPRIORITY    INTEGER         NOT NULL,
    O_COMMENT         VARCHAR(79)     NOT NULL
    ) ORGANIZATION LSC
    ORDER BY(O_ORDERDATE, O_ORDERKEY)
    PARTITION BY HASH(O_ORDERKEY) PARTITIONS AUTO;
    
    DROP TABLE IF EXISTS lineitem;
    CREATE TABLE lineitem (
    L_ORDERKEY       INTEGER          NOT NULL,
    L_PARTKEY         INTEGER         NOT NULL,
    L_SUPPKEY         INTEGER         NOT NULL,
    L_LINENUMBER      INTEGER         NOT NULL,
    L_QUANTITY        DECIMAL(15,2)   NOT NULL,
    L_EXTENDEDPRICE   DECIMAL(15,2)   NOT NULL,
    L_DISCOUNT        DECIMAL(15,2)   NOT NULL,
    L_TAX             DECIMAL(15,2)   NOT NULL,
    L_RETURNFLAG      CHAR(1)         NOT NULL,
    L_LINESTATUS      CHAR(1)         NOT NULL,
    L_SHIPDATE        DATE            NOT NULL,
    L_COMMITDATE      DATE            NOT NULL,
    L_RECEIPTDATE     DATE            NOT NULL,
    L_SHIPINSTRUCT    CHAR(25)        NOT NULL ENCODING DICTIONARY(RLE),
    L_SHIPMODE        CHAR(10)        NOT NULL ENCODING DICTIONARY(RLE),
    L_COMMENT         VARCHAR(44)     NOT NULL
    ) ORGANIZATION LSC
    ORDER BY(L_SHIPDATE, L_ORDERKEY)
    PARTITION BY HASH(L_ORDERKEY) PARTITIONS AUTO;
    ```

### Prepare Query Statements

The TPC-H test consists of 22 query statements, which need to be saved as a .yml file in the same path as the execution script for the script's parsing, with the filename as tpch.yml for example.
```yaml
queries:
  Q1: |
    -- Q1: Pricing Summary Report Query
    select
        l_returnflag,
        l_linestatus,
        sum(l_quantity) as sum_qty,
        sum(l_extendedprice) as sum_base_price,
        sum(l_extendedprice * (1 - l_discount)) as sum_disc_price,
        sum(l_extendedprice * (1 - l_discount) * (1 + l_tax)) as sum_charge,
        avg(l_quantity) as avg_qty,
        avg(l_extendedprice) as avg_price,
        avg(l_discount) as avg_disc,
        count(*) as count_order
    from
        lineitem
    where
        l_shipdate <= date '1998-12-01' - interval '90' day
    group by
        l_returnflag,
        l_linestatus
    order by
        l_returnflag,
        l_linestatus
  Q2: |
    -- Q2: Minimum Cost Supplier Query
    select
        s_acctbal,
        s_name,
        n_name,
        p_partkey,
        p_mfgr,
        s_address,
        s_phone,
        s_comment
    from
        part,
        supplier,
        partsupp,
        nation,
        region
    where
        p_partkey = ps_partkey
        and s_suppkey = ps_suppkey
        and p_size = 15
        and p_type like '%BRASS'
        and s_nationkey = n_nationkey
        and n_regionkey = r_regionkey
        and r_name = 'EUROPE'
        and ps_supplycost = (
            select
                min(ps_supplycost)
            from
                partsupp,
                supplier,
                nation,
                region
            where
                p_partkey = ps_partkey
                and s_suppkey = ps_suppkey
                and s_nationkey = n_nationkey
                and n_regionkey = r_regionkey
                and r_name = 'EUROPE'
            )
    order by
        s_acctbal desc,
        n_name,
        s_name,
        p_partkey
    limit 100
  Q3: |
    -- Q3: Shipping Priority Query
    select
        l_orderkey,
        sum(l_extendedprice*(1-l_discount)) as revenue,
        o_orderdate,
        o_shippriority
    from
        customer,
        orders,
        lineitem
    where
        c_mktsegment = 'BUILDING'
        and c_custkey = o_custkey
        and l_orderkey = o_orderkey
        and o_orderdate < date '1995-03-15'
        and l_shipdate > date '1995-03-15'
    group by
        l_orderkey,
        o_orderdate,
        o_shippriority
    order by
        revenue desc,
        o_orderdate
    limit 10
  Q4: |
    -- Q4: Order Priority Checking Query
    select
        o_orderpriority,
        count(*) as order_count
    from
        orders
    where
        o_orderdate >= date '1993-07-01'
        and o_orderdate < date '1993-07-01' + interval '3' month
        and exists (
            select
                *
            from
                lineitem
            where
                l_orderkey = o_orderkey
                and l_commitdate < l_receiptdate
        )
    group by
        o_orderpriority
    order by
        o_orderpriority
  Q5: |
    -- Q5: Local Supplier Volume Query
    select
        n_name,
        sum(l_extendedprice * (1 - l_discount)) as revenue
    from
        customer,
        orders,
        lineitem,
        supplier,
        nation,
        region
    where
        c_custkey = o_custkey
        and l_orderkey = o_orderkey
        and l_suppkey = s_suppkey
        and c_nationkey = s_nationkey
        and s_nationkey = n_nationkey
        and n_regionkey = r_regionkey
        and r_name = 'ASIA'
        and o_orderdate >= date '1994-01-01'
        and o_orderdate < date '1994-01-01' + interval '1' year
    group by
        n_name
    order by
        revenue desc
  Q6: |
    -- Q6: Forecasting Revenue Change Query
    select
        sum(l_extendedprice*l_discount) as revenue
    from
        lineitem
    where
        l_shipdate >= date '1994-01-01'
        and l_shipdate < date '1994-01-01' + interval '1' year
        and l_discount between  0.06 - 0.01 and  0.06 + 0.01
        and l_quantity < 24
  Q7: |
    -- Q7: Volume Shipping Query
    select
        supp_nation,
        cust_nation,
        l_year,
        sum(volume) as revenue
    from (
        select
            n1.n_name as supp_nation,
            n2.n_name as cust_nation,
            extract(year from l_shipdate) as l_year,
            l_extendedprice * (1 - l_discount) as volume
        from
            supplier,
            lineitem,
            orders,
            customer,
            nation n1,
            nation n2
        where
            s_suppkey = l_suppkey
            and o_orderkey = l_orderkey
            and c_custkey = o_custkey
            and s_nationkey = n1.n_nationkey
            and c_nationkey = n2.n_nationkey
            and (
                (n1.n_name = 'FRANCE' and n2.n_name = 'GERMANY')
                or (n1.n_name = 'GERMANY' and n2.n_name = 'FRANCE')
            )
            and l_shipdate between date '1995-01-01' and date '1996-12-31'
        ) as shipping
    group by
        supp_nation,
        cust_nation,
        l_year
    order by
        supp_nation,
        cust_nation,
        l_year
  Q8: |
    -- Q8: National Market Share Query
    select
        o_year,
        sum(case
            when nation = 'BRAZIL'
            then volume
            else 0
        end) / sum(volume) as mkt_share
    from (
        select
            extract(year from o_orderdate) as o_year,
            l_extendedprice * (1-l_discount) as volume,
            n2.n_name as nation
        from
            part,
            supplier,
            lineitem,
            orders,
            customer,
            nation n1,
            nation n2,
            region
        where
            p_partkey = l_partkey
            and s_suppkey = l_suppkey
            and l_orderkey = o_orderkey
            and o_custkey = c_custkey
            and c_nationkey = n1.n_nationkey
            and n1.n_regionkey = r_regionkey
            and r_name = 'AMERICA'
            and s_nationkey = n2.n_nationkey
            and o_orderdate between date '1995-01-01' and date '1996-12-31'
            and p_type = 'ECONOMY ANODIZED STEEL'
        ) as all_nations
    group by
        o_year
    order by
        o_year
  Q9: |
    -- Q9: Product Type Profit Measure Query
    select
        nation,
        o_year,
        sum(amount) as sum_profit
    from (
        select
            n_name as nation,
            extract(year from o_orderdate) as o_year,
            l_extendedprice * (1 - l_discount) - ps_supplycost * l_quantity as amount
        from
            part,
            supplier,
            lineitem,
            partsupp,
            orders,
            nation
        where
            s_suppkey = l_suppkey
            and ps_suppkey = l_suppkey
            and ps_partkey = l_partkey
            and p_partkey = l_partkey
            and o_orderkey = l_orderkey
            and s_nationkey = n_nationkey
            and p_name like '%green%'
        ) as profit
    group by
        nation,
        o_year
    order by
        nation,
        o_year desc
  Q10: |
    -- Q10: Returned Item Reporting Query
    select
        c_custkey,
        c_name,
        sum(l_extendedprice * (1 - l_discount)) as revenue,
        c_acctbal,
        n_name,
        c_address,
        c_phone,
        c_comment
    from
        customer,
        orders,
        lineitem,
        nation
    where
        c_custkey = o_custkey
        and l_orderkey = o_orderkey
        and o_orderdate >= date '1993-10-01'
        and o_orderdate < date '1993-10-01' + interval '3' month
        and l_returnflag = 'R'
        and c_nationkey = n_nationkey
    group by
        c_custkey,
        c_name,
        c_acctbal,
        c_phone,
        n_name,
        c_address,
        c_comment
    order by
        revenue desc
    limit 20
  # In Q11, {fraction:.10f} needs to be replaced according to SF, replace value is 0.0001/SF, details refer to the TPC-H official documentation
  Q11: |
    -- Q11: Important Stock Identification Query
    select
        ps_partkey,
        sum(ps_supplycost * ps_availqty) as value
    from
        partsupp,
        supplier,
        nation
    where
        ps_suppkey = s_suppkey
        and s_nationkey = n_nationkey
        and n_name = 'GERMANY'
    group by
        ps_partkey having
            sum(ps_supplycost * ps_availqty) > (
                select
                    sum(ps_supplycost * ps_availqty) * {fraction:.10f}
                from
                    partsupp,
                    supplier,
                    nation
                where
                    ps_suppkey = s_suppkey
                    and s_nationkey = n_nationkey
                    and n_name = 'GERMANY'
            )
    order by
        value desc
  Q12: |
    -- Q12: Shipping Modes and Order Priority Query
    select
        l_shipmode,
        sum(case
            when o_orderpriority = '1-URGENT'
                or o_orderpriority = '2-HIGH'
            then 1
            else 0
        end) as high_line_count,
        sum(case
            when o_orderpriority <> '1-URGENT'
                and o_orderpriority <> '2-HIGH'
            then 1
            else 0
        end) as low_line_count
    from
        orders,
        lineitem
    where
        o_orderkey = l_orderkey
        and l_shipmode in ('MAIL', 'SHIP')
        and l_commitdate < l_receiptdate
        and l_shipdate < l_commitdate
        and l_receiptdate >= date '1994-01-01'
        and l_receiptdate < date '1994-01-01' + interval '1' year
    group by
        l_shipmode
    order by
        l_shipmode
  # Q13 differs from native TPC-H SQL, please refer to this document
  Q13: |
    -- Q13: Customer Distribution Query
    select
        c_count,
        count(*) as custdist
    from (
        select
            c_custkey,
            count(o_orderkey) as c_count
        from
            customer left outer join orders on
                c_custkey = o_custkey
                and o_comment not like '%special%requests%'
        group by
            c_custkey
        )as c_orders
    group by
        c_count
    order by
        custdist desc,
        c_count desc
  Q14: |
    -- Q14: Promotion Effect Query
    select
        100.00 * sum(case
            when p_type like 'PROMO%'
            then l_extendedprice*(1-l_discount)
            else 0
        end) / sum(l_extendedprice * (1 - l_discount)) as promo_revenue
    from
        lineitem,
        part
    where
        l_partkey = p_partkey
        and l_shipdate >= date '1995-09-01'
        and l_shipdate < date '1995-09-01' + interval '1' month
  Q15: |
    -- Q15: Top Supplier Query
    select
        s_suppkey,
        s_name,
        s_address,
        s_phone,
        total_revenue
    from
        supplier,
        revenue0
    where
        s_suppkey = supplier_no
        and total_revenue = (
            select
                max(total_revenue)
            from
                revenue0
        )
    order by
        s_suppkey
  Q16: |
    -- Q16: Parts/Supplier Relationship Query
    select
        p_brand,
        p_type,
        p_size,
        count(distinct ps_suppkey) as supplier_cnt
    from
        partsupp,
        part
    where
        p_partkey = ps_partkey
        and p_brand <> 'Brand#45'
        and p_type not like 'MEDIUM POLISHED%'
        and p_size in (49, 14, 23, 45, 19, 3, 36, 9)
        and ps_suppkey not in (
            select
                s_suppkey
            from
                supplier
            where
                s_comment like '%Customer%Complaints%'
        )
    group by
        p_brand,
        p_type,
        p_size
    order by
        supplier_cnt desc,
        p_brand,
        p_type,
        p_size
  Q17: |
    -- Q17: Small-Quantity-Order Revenue Query
    select
        sum(l_extendedprice) / 7.0 as avg_yearly
    from
        lineitem,
        part
    where
        p_partkey = l_partkey
        and p_brand = 'Brand#23'
        and p_container = 'MED BOX'
        and l_quantity < (
            select
                0.2 * avg(l_quantity)
            from
                lineitem
            where
                l_partkey = p_partkey
        )
  Q18: |
    -- Q18: Large Volume Customer Query
    select
        c_name,
        c_custkey,
        o_orderkey,
        o_orderdate,
        o_totalprice,
        sum(l_quantity)
    from
        customer,
        orders,
        lineitem
    where
        o_orderkey in (
            select
                l_orderkey
            from
                lineitem
            group by
                l_orderkey having
                    sum(l_quantity) > 300
        )
        and c_custkey = o_custkey
        and o_orderkey = l_orderkey
    group by
        c_name,
        c_custkey,
        o_orderkey,
        o_orderdate,
        o_totalprice
    order by
        o_totalprice desc,
        o_orderdate
    limit 100
  Q19: |
    -- Q19: Discounted Revenue Query
    select
        sum(l_extendedprice * (1 - l_discount)) as revenue
    from
        lineitem,
        part
    where
        (
            p_partkey = l_partkey
            and p_brand = 'Brand#12'
            and p_container in ('SM CASE', 'SM BOX', 'SM PACK', 'SM PKG')
            and l_quantity >= 1 and l_quantity <= 1 + 10
            and p_size between 1 and 5
            and l_shipmode in ('AIR', 'AIR REG')
            and l_shipinstruct = 'DELIVER IN PERSON'
        )
        or
        (
            p_partkey = l_partkey
            and p_brand = 'Brand#23'
            and p_container in ('MED BAG', 'MED BOX', 'MED PKG', 'MED PACK')
            and l_quantity >= 10 and l_quantity <= 10 + 10
            and p_size between 1 and 10
            and l_shipmode in ('AIR', 'AIR REG')
            and l_shipinstruct = 'DELIVER IN PERSON'
        )
        or
        (
            p_partkey = l_partkey
            and p_brand = 'Brand#34'
            and p_container in ('LG CASE', 'LG BOX', 'LG PACK', 'LG PKG')
            and l_quantity >= 20 and l_quantity <= 20 + 10
            and p_size between 1 and 15
            and l_shipmode in ('AIR', 'AIR REG')
            and l_shipinstruct = 'DELIVER IN PERSON'
        )
  Q20: |
    -- Q20: Potential Part Promotion Query
    select
        s_name,
        s_address
    from
        supplier,
        nation
    where
        s_suppkey in (
            select
                ps_suppkey
            from
                partsupp
            where
                ps_partkey in (
                    select
                        p_partkey
                    from
                        part
                    where
                        p_name like 'forest%'
                )
            and ps_availqty > (
                select
                    0.5 * sum(l_quantity)
                from
                    lineitem
                where
                    l_partkey = ps_partkey
                    and l_suppkey = ps_suppkey
                    and l_shipdate >= date '1994-01-01'
                    and l_shipdate < date '1994-01-01' + interval '1' year
            )
        )
        and s_nationkey = n_nationkey
        and n_name = 'CANADA'
    order by
        s_name
  Q21: |
    -- Q21: Suppliers Who Kept Orders Waiting Query
    select
        s_name,
        count(*) as numwait
    from
        supplier,
        lineitem l1,
        orders,
        nation
    where
        s_suppkey = l1.l_suppkey
        and o_orderkey = l1.l_orderkey
        and o_orderstatus = 'F'
        and l1.l_receiptdate > l1.l_commitdate
        and exists (
            select
                *
            from
                lineitem l2
            where
                l2.l_orderkey = l1.l_orderkey
                and l2.l_suppkey <> l1.l_suppkey
        )
        and not exists (
            select
                *
            from
                lineitem l3
            where
                l3.l_orderkey = l1.l_orderkey
                and l3.l_suppkey <> l1.l_suppkey
                and l3.l_receiptdate > l3.l_commitdate
        )
        and s_nationkey = n_nationkey
        and n_name = 'SAUDI ARABIA'
    group by
        s_name
    order by
        numwait desc,
        s_name
    limit 100
  Q22: |
    -- Q22: Global Sales Opportunity Query
    select
        cntrycode,
        count(*) as numcust,
        sum(c_acctbal) as totacctbal
    from (
        select
            substring(c_phone from 1 for 2) as cntrycode,
            c_acctbal
        from
            customer
        where
            substring(c_phone from 1 for 2) in
                ('13','31','23','29','30','18','17')
            and c_acctbal > (
                select
                    avg(c_acctbal)
                from
                    customer
                where
                    c_acctbal > 0.00
                    and substring (c_phone from 1 for 2) in
                        ('13','31','23','29','30','18','17')
            )
            and not exists (
                select
                    *
                from
                    orders
                where
                    o_custkey = c_custkey
            )
        ) custsale
    group by
        cntrycode
    order by
        cntrycode
```

### Import Data

Navigate to the database installation directory and execute the following command to import data.

> **Note**:
>
> It is recommended to adjust parameters DATA_BUFFER_SIZE=30G, VM_BUFFER_SIZE=20G, SCOL_DATA_BUFFER_SIZE=1G during the data import stage, and adjust the parameters as the volume of test data grows.

```shell
$ vi load_data.sh
#! /bin/bash

YASDB_HOME='./'  # Replace with the actual installed database path
CN_NODE_IP='127.0.0.1'  # Replace with the actual CN node IP address
DATA_PATH='/data/tpch/SF100'  # Replace with the actual TPC-H data storage path

$YASDB_HOME/bin/yasldr regress/regress@$CN_NODE_IP:1688 control_text="'load data options(DEGREE_OF_PARALLELISM=16,TRIM=NOTRIM,ENABLE_BULK=FALSE,DECODER_THREAD_TIMES=7) infile '$DATA_PATH/region.tbl' without embedded fields terminated by '|' append into table region(R_REGIONKEY, R_NAME, R_COMMENT)'" senders=9
$YASDB_HOME/bin/yasldr regress/regress@$CN_NODE_IP:1688 control_text="'load data options(DEGREE_OF_PARALLELISM=16,TRIM=NOTRIM,ENABLE_BULK=FALSE,DECODER_THREAD_TIMES=7) infile '$DATA_PATH/nation.tbl' without embedded fields terminated by '|' append into table nation(N_NATIONKEY, N_NAME, N_REGIONKEY, N_COMMENT)'" senders=9
$YASDB_HOME/bin/yasldr regress/regress@$CN_NODE_IP:1688 control_text="'load data options(DEGREE_OF_PARALLELISM=16,TRIM=NOTRIM,ENABLE_BULK=FALSE,DECODER_THREAD_TIMES=7) infile '$DATA_PATH/supplier.tbl' without embedded fields terminated by '|' append into table supplier(S_SUPPKEY, S_NAME, S_ADDRESS, S_NATIONKEY, S_PHONE, S_ACCTBAL, S_COMMENT)'" senders=9
$YASDB_HOME/bin/yasldr regress/regress@$CN_NODE_IP:1688 control_text="'load data options(DEGREE_OF_PARALLELISM=16,TRIM=NOTRIM,ENABLE_BULK=FALSE,DECODER_THREAD_TIMES=7) infile '$DATA_PATH/part.tbl' without embedded fields terminated by '|' append into table part(P_PARTKEY, P_NAME, P_MFGR, P_BRAND, P_TYPE, P_SIZE, P_CONTAINER, P_RETAILPRICE, P_COMMENT)'" senders=9
$YASDB_HOME/bin/yasldr regress/regress@$CN_NODE_IP:1688 control_text="'load data options(DEGREE_OF_PARALLELISM=16,TRIM=NOTRIM,ENABLE_BULK=FALSE,DECODER_THREAD_TIMES=7) infile '$DATA_PATH/partsupp.tbl' without embedded fields terminated by '|' append into table partsupp(PS_PARTKEY, PS_SUPPKEY, PS_AVAILQTY, PS_SUPPLYCOST, PS_COMMENT)'" senders=9
$YASDB_HOME/bin/yasldr regress/regress@$CN_NODE_IP:1688 control_text="'load data options(DEGREE_OF_PARALLELISM=16,TRIM=NOTRIM,ENABLE_BULK=FALSE,DECODER_THREAD_TIMES=7) infile '$DATA_PATH/customer.tbl' without embedded fields terminated by '|' append into table customer(C_CUSTKEY, C_NAME, C_ADDRESS, C_NATIONKEY, C_PHONE, C_ACCTBAL, C_MKTSEGMENT, C_COMMENT)'" senders=9
$YASDB_HOME/bin/yasldr regress/regress@$CN_NODE_IP:1688 control_text="'load data options(DEGREE_OF_PARALLELISM=16,TRIM=NOTRIM,ENABLE_BULK=FALSE,DECODER_THREAD_TIMES=7) infile '$DATA_PATH/orders.tbl' without embedded fields terminated by '|' append into table orders(O_ORDERKEY, O_CUSTKEY, O_ORDERSTATUS, O_TOTALPRICE, O_ORDERDATE, O_ORDERPRIORITY, O_CLERK, O_SHIPPRIORITY, O_COMMENT)'" senders=9
$YASDB_HOME/bin/yasldr regress/regress@$CN_NODE_IP:1688 control_text="'load data options(DEGREE_OF_PARALLELISM=16,TRIM=NOTRIM,ENABLE_BULK=FALSE,DECODER_THREAD_TIMES=7) infile '$DATA_PATH/lineitem.tbl' without embedded fields terminated by '|' append into table lineitem(L_ORDERKEY, L_PARTKEY, L_SUPPKEY, L_LINENUMBER, L_QUANTITY, L_EXTENDEDPRICE, L_DISCOUNT, L_TAX, L_RETURNFLAG, L_LINESTATUS, L_SHIPDATE, L_COMMITDATE, L_RECEIPTDATE, L_SHIPINSTRUCT, L_SHIPMODE, L_COMMENT)'" senders=9

$ chmod +x load_data.sh
$ ./load_data.sh
```

### Creating Views Required for Q15

```sql
CREATE VIEW revenue0 (supplier_no, total_revenue) AS
    SELECT
        l_suppkey,
        SUM(l_extendedprice * (1 - l_discount))
    FROM
        lineitem
    WHERE
        l_shipdate >= DATE '1996-01-01'
        AND l_shipdate < DATE '1996-01-01' + INTERVAL '3' month
    GROUP BY
        l_suppkey;
```

### Converting Hot Data to Cold Data

```sql
ALTER SYSTEM SET _enable_alter_slice=true scope=memory;
ALTER SYSTEM SET DATA_TRANSFORMER_ENABLED=true scope=memory;
 
ALTER TABLE region ALTER SLICE ALL STABLE;
ALTER TABLE nation ALTER SLICE ALL STABLE;
ALTER TABLE supplier ALTER SLICE ALL STABLE;
ALTER TABLE part ALTER SLICE ALL STABLE;
ALTER TABLE partsupp ALTER SLICE ALL STABLE;
ALTER TABLE customer ALTER SLICE ALL STABLE;
ALTER TABLE orders ALTER SLICE ALL STABLE;
ALTER TABLE lineitem ALTER SLICE ALL STABLE;
 
ALTER TABLE region ALTER SLICE ALL COMPACT;
ALTER TABLE nation ALTER SLICE ALL COMPACT;
ALTER TABLE supplier ALTER SLICE ALL COMPACT;
ALTER TABLE part ALTER SLICE ALL COMPACT;
ALTER TABLE partsupp ALTER SLICE ALL COMPACT;
ALTER TABLE customer ALTER SLICE ALL COMPACT;
ALTER TABLE orders ALTER SLICE ALL COMPACT;
ALTER TABLE lineitem ALTER SLICE ALL COMPACT;
 
ALTER TABLE region ALTER SLICE ALL CLEAN;
ALTER TABLE nation ALTER SLICE ALL CLEAN;
ALTER TABLE supplier ALTER SLICE ALL CLEAN;
ALTER TABLE part ALTER SLICE ALL CLEAN;
ALTER TABLE partsupp ALTER SLICE ALL CLEAN;
ALTER TABLE customer ALTER SLICE ALL CLEAN;
ALTER TABLE orders ALTER SLICE ALL CLEAN;
ALTER TABLE lineitem ALTER SLICE ALL CLEAN;
```

### Collecting Statistics

Execute the following statement to start collecting statistics.

> **Note**:
>
> It is recommended to adjust parameters DATA_BUFFER_SIZE=30G, VM_BUFFER_SIZE=20G, SCOL_DATA_BUFFER_SIZE=1G, and COLUMNAR_VM_BUFFER_SIZE=10G during the statistics collection phase, and adjust the parameters as the volume of test data grows.

```sql
ANALYZE SCHEMA regress ESTIMATE_PERCENT 1 PARALLEL_DEGREE 16 METHOD_OPTION 'FOR ALL COLUMNS SIZE AUTO' GRANULARITY 'GLOBAL';
```

### Executing Queries

```shell
$ vi tpch.py
#! /usr/bin/python

import time
import jpype
import yaml

jpype.addClassPath('./lib/yasdb-jdbc-{version_number}.jar') # Replace the path with the real JDBC driver path
jpype.startJVM(jpype.getDefaultJVMPath())
url = 'jdbc:yasdb://127.0.0.1:1688/test'  # Replace 127.0.0.1 with the actual CN node IP address
conn = jpype.java.sql.DriverManager.getConnection(url, 'regress', 'regress')
with open('./tpch.yml', "r") as f:
    queries = yaml.safe_load(f)

totalCost = 0
print("----------TPC-H Benchmark Begin----------")
for name, sql in queries["queries"].items():
    if name == "Q11":
        sql = sql.format(fraction=0.0001 / 100)  # Adjust 100 according to the actual test data scale
    status = 'SUCCESS'
    start = time.time()
    try:
        prep_stmt = conn.prepareStatement(sql)
        prep_stmt.execute()
    except Exception:
        status = 'ERROR'
    finally:
        elapsed = time.time() - start
    print(f"{name:>3} exec status: {status:>7}，elapsed: {elapsed:>7.3f}s")
    totalCost += elapsed
print("----------TPC-H Benchmark END({:.3f}s)------------".format(totalCost))
jpype.shutdownJVM()

$ python tpch.py
```

Wait for the test to complete and check the test results.