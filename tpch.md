## TPCH preformance testing

```shell
tpc test document read 

# 基于RapidsDB v4.3的TPCH测试步骤 #

### 启动RapidsDB4.2集群 ###
    [root@node1 current]./bootstrapper.sh -a start
	
## 数据准备 ##
### 生成tpch数据 ###
进入rapidsdb
    
    [root@node1 opt] cd /opt/rdp/current
    [root@node1 current] ./rapids-shell.sh
    
    rapids > CREATE CONNECTOR SF1 TYPE TPCHDB WITH SCALE_FACTOR='1';

建立TPCH 1GB 数据的连接器，连接器名为SF1

### 建立moxe连接器 ###
进入rapidsdb
    
    [root@node1 opt] cd /opt/rdp/current
    [root@node1 current] ./rapids-shell.sh
    
    rapids > CREATE CONNECTOR MOXE TYPE MOXE WITH PARTITIONS_PER_NODE='2', MEM_PER_NODE='10GB';
	
  代表在rapidsdb中建立moxe的连接器
  其中分片数为每节点数据分为2部分，且每个节点允许最大内存使用限度为10GB
  
### 建表 ###
TPCH测试中我们建立分布式行式存储表

    create table moxe.SUPPLIER (
       s_suppkey integer not null,
       s_name varchar(25) not null,
       s_address varchar(40) not null,
       s_nationkey integer not null,
       s_phone varchar(15) not null,
       s_acctbal decimal(15,2) not null,
       s_comment varchar(101) not null
    ) PARTITION (s_suppkey);
    
    create table moxe.PART (
       p_partkey integer not null,
       p_name varchar(55) not null,
       p_mfgr varchar(25) not null,
       p_brand varchar(10) not null,
       p_type varchar(25) null,
       p_size integer not null,
       p_container varchar(10),
       p_retailprice decimal(15,2) not null,
       p_comment varchar(23) not null
    ) PARTITION (p_partkey);
    
    create table moxe.PARTSUPP (
       ps_partkey integer not null,
       ps_suppkey integer not null,
       ps_availqty integer not null,
       ps_supplycost decimal(15,2) not null,
       ps_comment varchar(199) not null
    ) PARTITION (ps_partkey);
    
    create table moxe.CUSTOMER (
       c_custkey integer not null,
       c_name varchar(25) not null,
       c_address varchar(40) not null,
       c_nationkey integer not null,
       c_phone varchar(15) not null,
       c_acctbal decimal(15,2) not null,
       c_mktsegment varchar(10),
       c_comment varchar(117)
    ) PARTITION (c_custkey);
    
    create table moxe.ORDERS (
       o_orderkey integer not null,
       o_custkey integer not null,
       o_orderstatus varchar(1) not null,
       o_totalprice decimal(15,2) not null,
       o_orderdate date not null,
       o_orderpriority varchar(15) not null,
       o_clerk varchar(15) not null,
       o_shippriority integer not null,
       o_comment varchar(79) not null
    ) PARTITION (o_orderkey);
    
    create table moxe.LINEITEM (
       l_orderkey integer not null,
       l_partkey integer not null,
       l_suppkey integer not null,
       l_linenumber integer not null,
       l_quantity decimal not null,
       l_extendedprice decimal(15,2) not null,
       l_discount decimal(15,2) not null,
       l_tax decimal(15,2) not null,
       l_returnflag varchar(1) not null,
       l_linestatus varchar(1) not null,
       l_shipdate date not null,
       l_commitdate date not null,
       l_receiptdate date not null,
       l_shipinstruct varchar(25) not null,
       l_shipmode varchar(10) not null,
       l_comment varchar(44) not null
    ) PARTITION (l_orderkey);
    
    create table moxe.NATION (
       n_nationkey integer not null,
       n_name varchar(25) not null,
       n_regionkey integer not null,
       n_comment varchar(152)
    );
    
    create table moxe.REGION (
       r_regionkey integer not null,
       r_name varchar(25) not null,
       r_comment varchar(152)
    );
	
### 数据导入 ###
从TPCHDB往MOXE的表中加载数据

    insert into moxe.lineitem select * from sf1.lineitem;
    insert into moxe.orders select * from sf1.orders;
    insert into moxe.partsupp select * from sf1.partsupp;
    insert into moxe.part select * from sf1.part;
    insert into moxe.supplier select * from sf1.supplier;
    insert into moxe.customer select * from sf1.customer;
    insert into moxe.nation select * from sf1.nation;
    insert into moxe.region select * from sf1.region;

## 测试过程 ##
### 查询语句 ###
//Query #1:

    select l_returnflag, l_linestatus, sum(l_quantity) as sum_qty, sum(l_extendedprice) as sum_base_price,
      sum(l_extendedprice*(1-l_discount)) as sum_disc_price, sum(l_extendedprice*(1-l_discount)*(1+l_tax)) as sum_charge,
      avg(l_quantity) as avg_qty, avg(l_extendedprice) as avg_price, avg(l_discount) as avg_disc, count(*) as count_order
      from moxe.LINEITEM
     where l_shipdate <= '1998-09-02 00:00:00'
     group by l_returnflag, l_linestatus
     order by l_returnflag, l_linestatus;
    
//Query #2:
    
    select s_acctbal, s_name, n_name, p_partkey, p_mfgr, s_address, s_phone, s_comment
      from moxe.PART, moxe.SUPPLIER, moxe.PARTSUPP, moxe.NATION, moxe.REGION
    where p_partkey = ps_partkey
    and s_suppkey = ps_suppkey
    and p_size = 15
    and p_type like '%BRASS'
    and s_nationkey = n_nationkey
    and n_regionkey = r_regionkey
    and r_name = 'EUROPE'
    and ps_supplycost = (
    select min(ps_supplycost)
      from moxe.PARTSUPP, moxe.SUPPLIER, moxe.NATION, moxe.REGION
    where p_partkey = ps_partkey
    and s_suppkey = ps_suppkey
    and s_nationkey = n_nationkey
    and n_regionkey = r_regionkey
    and r_name = 'EUROPE'
     )
    order by s_acctbal desc, n_name, s_name, p_partkey limit 1;
    
//Query #3:
    
    select l_orderkey, sum(l_extendedprice*(1-l_discount)) as revenue, o_orderdate, o_shippriority
      from moxe.CUSTOMER, moxe.ORDERS, moxe.LINEITEM
    where c_mktsegment = 'BUILDING'
    and c_custkey = o_custkey
    and l_orderkey = o_orderkey
    and o_orderdate < '1995-03-15 00:00:00'
    and l_shipdate > '1995-03-15 00:00:00'
    group by l_orderkey, o_orderdate, o_shippriority
     order by revenue desc, o_orderdate limit 1;
    
//Query #4:
    
    select o_orderpriority, count(*) as order_count
      from moxe.ORDERS
    where o_orderdate >= '1993-07-01 00:00:00'
    and o_orderdate < '1993-10-01 00:00:00'
    and exists (  select *
      from moxe.LINEITEM
     where l_orderkey = o_orderkey
     and l_commitdate < l_receiptdate
       )
    group by o_orderpriority
     order by o_orderpriority;
    
//Query #5:
    
    select n_name, sum(l_extendedprice * (1 - l_discount)) as revenue
      from moxe.CUSTOMER, moxe.ORDERS, moxe.LINEITEM, moxe.SUPPLIER, moxe.NATION, moxe.REGION
    where c_custkey = o_custkey
    and l_orderkey = o_orderkey
    and l_suppkey = s_suppkey
    and c_nationkey = s_nationkey
    and s_nationkey = n_nationkey
    and n_regionkey = r_regionkey
    and r_name = 'ASIA'
    and o_orderdate >= '1994-01-01 00:00:00'
    and o_orderdate < '1995-01-01 00:00:00'
    group by n_name
     order by revenue desc;
    
//Query #6:
   
    select sum(l_extendedprice*l_discount) as revenue
      from moxe.LINEITEM
    where l_shipdate >= '1994-01-01 00:00:00'
    and l_shipdate <'1995-01-01 00:00:00'
    and l_discount between 0.06 - 0.01 and 0.06 + 0.01
    and l_quantity < 24;
    
//Query #7:
    
    select supp_nation, cust_nation, l_year, sum(volume) as revenue
      from ( select n1.n_name as supp_nation, n2.n_name as cust_nation, extract(year from l_shipdate) as l_year,
     l_extendedprice * (1 - l_discount) as volume
    from moxe.SUPPLIER, moxe.LINEITEM, moxe.ORDERS, moxe.CUSTOMER, moxe.NATION n1, moxe.NATION n2
    where s_suppkey = l_suppkey
      and o_orderkey = l_orderkey
      and c_custkey = o_custkey
      and s_nationkey = n1.n_nationkey
      and c_nationkey = n2.n_nationkey
      and ( (n1.n_name = 'FRANCE' and n2.n_name = 'GERMANY')
     or(n1.n_name = 'GERMANY' and n2.n_name = 'FRANCE')
      )
       and l_shipdate between '1995-01-01 00:00:00' and '1996-12-31 00:00:00'
       ) as shipping
    group by supp_nation, cust_nation, l_year
     order by supp_nation, cust_nation, l_year limit 1;
    
//Query #8:
    
    select o_year, sum(case when nation = 'BRAZIL' then volume else 0 end) / sum(volume) as mkt_share
      from ( select extract(year from o_orderdate) as o_year, l_extendedprice * (1-l_discount) as volume, n2.n_name as nation
    from moxe.PART, moxe.SUPPLIER, moxe.LINEITEM, moxe.ORDERS, moxe.CUSTOMER, moxe.NATION n1, moxe.NATION n2, moxe.REGION
    where p_partkey = l_partkey
      and s_suppkey = l_suppkey
      and l_orderkey = o_orderkey
      and o_custkey = c_custkey
      and c_nationkey = n1.n_nationkey
      and n1.n_regionkey = r_regionkey
      and r_name = 'AMERICA'
      and s_nationkey = n2.n_nationkey
      and o_orderdate between '1995-01-01 00:00:00' and '1996-12-31 00:00:00'
      and p_type = 'ECONOMY ANODIZED STEEL'
    ) as all_nations
    group by o_year
     order by o_year;
     
    
//Query #9:
    
    select nation, o_year, sum(amount) as sum_profit
      from ( select n_name as nation, extract(year from 
      o_orderdate) as o_year, l_extendedprice * (1 - l_discount) - ps_supplycost * l_quantity as amount
    from moxe.PART, moxe.SUPPLIER, moxe.LINEITEM, moxe.PARTSUPP, moxe.ORDERS, moxe.NATION
      where s_suppkey = l_suppkey
      and ps_suppkey = l_suppkey
      and ps_partkey = l_partkey
      and p_partkey = l_partkey
      and o_orderkey = l_orderkey
      and s_nationkey = n_nationkey
      and p_name like '%green%'
       ) as profit
    group by nation, o_year
     order by nation, o_year desc limit 10;
    
//Query #10:
    
    select c_custkey, c_name, sum(l_extendedprice * (1 - l_discount)) as revenue, c_acctbal, n_name, c_address, c_phone, c_comment
      from moxe.CUSTOMER, moxe.ORDERS, moxe.LINEITEM, moxe.NATION
    where c_custkey = o_custkey
    and l_orderkey = o_orderkey
    and o_orderdate >= '1993-10-01 00:00:00'
    and o_orderdate <  '1994-01-01 00:00:00'
    and l_returnflag = 'R'
    and c_nationkey = n_nationkey
    group by c_custkey, c_name, c_acctbal, c_phone, n_name, c_address, c_comment
     order by revenue desc limit 10;
    
//Query #11:
    
    select ps_partkey, sum(ps_supplycost * ps_availqty)
      from moxe.PARTSUPP, moxe.SUPPLIER, moxe.NATION
    where ps_suppkey = s_suppkey
    and s_nationkey = n_nationkey
    and n_name = 'GERMANY'
    group by ps_partkey having sum(ps_supplycost * ps_availqty) > (  select sum(ps_supplycost * ps_availqty) * 0.000001
     from moxe.PARTSUPP, moxe.SUPPLIER, moxe.NATION
       where ps_suppkey = s_suppkey
       and s_nationkey = n_nationkey
       and n_name = 'GERMANY'
    )
    order by ps_partkey, sum(ps_supplycost * ps_availqty) desc limit 10;
    
//Query #12:
    
    select l_shipmode, sum(case
       when
     o_orderpriority ='1-URGENT' or o_orderpriority ='2-HIGH' then 1
      else 0
    end) as high_line_count,
      sum(case
    when o_orderpriority <> '1-URGENT' and o_orderpriority <> '2-HIGH' then 1
    else 0
    end) as low_line_count
      from moxe.ORDERS, moxe.LINEITEM
    where o_orderkey = l_orderkey
    and l_shipmode in ('MAIL', 'SHIP')
    and l_commitdate < l_receiptdate
    and l_shipdate < l_commitdate
    and l_receiptdate >= '1994-01-01 00:00:00'
    and l_receiptdate < '1995-01-01 00:00:00'
    group by l_shipmode
     order by l_shipmode limit 10;
    
//Query #13:
    
    select c_count, count( * ) as custdist
      from ( select c_custkey, count(o_orderkey) c_count
       from moxe.CUSTOMER left outer join moxe.ORDERS
     on c_custkey = o_custkey
    and o_comment not like '%special%requests%' group by c_custkey )
    as c_orders
     group by c_count ;
    
//Query #14:
    
    select 100.00 * sum(case
      when p_type like 'PROMO%'
      then l_extendedprice*(1-l_discount)
      else 0
       end) / sum(l_extendedprice * (1 - l_discount)) as promo_revenue
      from moxe.LINEITEM, moxe.PART
    where l_partkey = p_partkey
    and l_shipdate >= '1995-09-01 00:00:00'
    and l_shipdate < '1995-10-01 00:00:00';
    
//Query #15:
    
    with revenue_q15 (supplier_no, total_revenue) as
       (select l_suppkey, sum(l_extendedprice * (1 - l_discount))
      from moxe.LINEITEM
     where l_shipdate >= '1996-01-01 00:00:00'
       and l_shipdate < '1996-04-01 00:00:00'
     group by l_suppkey)
    
    select s_suppkey, s_name, s_address, s_phone, total_revenue
      from moxe.SUPPLIER, revenue_q15
     where s_suppkey = supplier_no
       and total_revenue = ( select max(total_revenue)
       from revenue_q15 )
     order by s_suppkey;
    
//Query #16:
    
    select p_brand, p_type, p_size, count(ps_suppkey) as supplier_cnt
      from moxe.PARTSUPP, moxe.PART
    where p_partkey = ps_partkey
    and p_brand <> 'BRAND#15'
    and p_type not like 'ECONOMY%'
    and p_size in (49, 14, 23, 45, 19, 3, 36, 9)
    and ps_suppkey not in (  select s_suppkey
       from moxe.SUPPLIER
     where s_comment like '%moxe.CUSTOMER%Complaints%'
    )
    group by p_brand, p_type, p_size
     order by supplier_cnt desc, p_brand, p_type, p_size limit 10;
    
//Query #17:
    
    select sum(l_extendedprice) / 7.0 as avg_yearly
      from moxe.LINEITEM, moxe.PART
    where p_partkey = l_partkey
    and p_brand = 'Brand#23'
    and p_container = 'MED BOX'
    and l_quantity < ( select 0.2 * avg(l_quantity)
       from moxe.LINEITEM
     where l_partkey = p_partkey
       );
    
//Query #18:
    
    select c_name, c_custkey, o_orderkey, o_orderdate, o_totalprice, sum(l_quantity)
      from moxe.CUSTOMER, moxe.ORDERS, moxe.LINEITEM
    where o_orderkey in ( select l_orderkey
      from moxe.LINEITEM
    group by l_orderkey having sum(l_quantity) > 300
      )
    and c_custkey = o_custkey
    and o_orderkey = l_orderkey
     group by c_name, c_custkey, o_orderkey, o_orderdate, o_totalprice
     order by o_totalprice desc, o_orderdate limit 10;
    
//Query #19:
    
    select sum(l_extendedprice * (1 - l_discount) ) as revenue
    from moxe.LINEITEM, moxe.PART
    where ( p_partkey = l_partkey
    and p_brand = 'Brand#12'
    and p_container in ( 'SM CASE', 'SM BOX', 'SM PACK', 'SM PKG')
    and l_quantity >= 1 and l_quantity <= 1 + 10
    and p_size between 1 and 5
    and l_shipmode in ('AIR', 'AIR REG')
    and l_shipinstruct = 'DELIVER IN PERSON' )
    or
    ( p_partkey = l_partkey
    and p_brand = 'Brand#23'
    and p_container in ('MED BAG', 'MED BOX', 'MED PKG', 'MED PACK')
    and l_quantity >= 10 and l_quantity <= 10 + 10
    and p_size between 1 and 10
    and l_shipmode in ('AIR', 'AIR REG')
    and l_shipinstruct = 'DELIVER IN PERSON' )
    or
    ( p_partkey = l_partkey
    and p_brand = 'Brand#34'
    and p_container in ( 'LG CASE', 'LG BOX', 'LG PACK', 'LG PKG')
    and l_quantity >= 20 and l_quantity <= 20 + 10
    and p_size between 1 and 15
    and l_shipmode in ('AIR', 'AIR REG')
    and l_shipinstruct = 'DELIVER IN PERSON');
    
//Query #20:
    
    select s_name, s_address
      from moxe.SUPPLIER, moxe.NATION
    where s_suppkey in ( select ps_suppkey
    from moxe.PARTSUPP
    where ps_partkey in ( select p_partkey
      from moxe.PART
    where p_name like 'forest%'
       )
      and ps_availqty > ( select 0.5 * sum(l_quantity)
       from moxe.LINEITEM
     where l_partkey = ps_partkey
     and l_suppkey = ps_suppkey
     and l_shipdate >= '1994-01-01 00:00:00'
     and l_shipdate < '1995-01-01 00:00:00'
       )
    )
    and s_nationkey = n_nationkey
    and n_name = 'GERMANY'
     order by s_name limit 10;
    
//Query #21:
    
    select s_name, count(*) as numwait
      from moxe.SUPPLIER, moxe.LINEITEM l1, moxe.ORDERS, moxe.NATION
    where s_suppkey = l1.l_suppkey
    and o_orderkey = l1.l_orderkey
    and o_orderstatus = 'F'
    and l1.l_receiptdate > l1.l_commitdate
    and exists ( select *
       from moxe.LINEITEM l2
     where l2.l_orderkey = l1.l_orderkey
     and l2.l_suppkey <> l1.l_suppkey
       )
    and not exists ( select *
      from moxe.LINEITEM l3
    where l3.l_orderkey = l1.l_orderkey
    and l3.l_suppkey <> l1.l_suppkey
    and l3.l_receiptdate > l3.l_commitdate
     )
    and s_nationkey = n_nationkey
    and n_name = 'SAUDI ARABIA'
     group by s_name
      order by numwait desc, s_name limit 10;
    
//Query #22:
    
    select cntrycode, count(*) as numcust, sum(c_acctbal) as totacctbal
      from ( select substring(c_phone from 1 for 2) as cntrycode, c_acctbal
       from moxe.CUSTOMER
     where substring(c_phone from 1 for 2) in ('13','31','23','29','30','18','17')
     and c_acctbal > ( select avg(c_acctbal)
    from moxe.CUSTOMER
      where c_acctbal > 0.00
      and substring (c_phone from 1 for 2) in ('13','31','23','29','30','18','17')
    )
      and not exists ( select *
    from moxe.ORDERS
    where o_custkey = c_custkey
    )
       ) as custsale
    group by cntrycode
     order by cntrycode limit 10;
	 
### 文件准备 ###
将22条语句写入query.txt文件中，再建立一个result.txt文件，放入/opt/rdp/current目录下
### 脚本准备 ###
建立一个脚本文件

    [root@node1 current] vi test.sh
	
在文件里写入以下内容
    #! /bin/bash
    export RDP_USERNAME=rapids
    export RDP_PASSWORD=rapids
    time echo "run /opt/rdp/current/query.txt;" | ./rapids-shell.sh 2>&1 | tee result.txt

给脚本赋予权限

    [root@node1 current] chmod 777 test.sh
	
### 执行脚本 ###
    [root@node1 current] ./test.sh
	
结果将显示查询总耗时，并将详细查询结果录入result.txt文件中
```
