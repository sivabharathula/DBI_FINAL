# DBI_FINAL_SIVA_GOWTHAM
Instructions

This folder has two files:

    query.execute -- you should be able to execute the queries specified in this file (7 queries)
    query.plan -- you should be able to generate a query plan for queries listed in this file (10 queries)

Preparing for the demo:

    Make sure you have loaded all the tpch tables into your system
    Set up the Statistics module so that it can give meaningful plans
    Go over the final project description carefully to avoid surprises!

During the demo:

    Please arrive at the CSE114 lab atleast 10 minutes before the demo
    All your team members must be present for the demo
    You will be required to run a few queries and generate a few plans
    The queries that we give out are for testing purposes only..You should be prepared to run/generate plans for arbitrary queries
    Be prepared to answer questions about the implementation as well as your specific role in it.

Good luck!


##Load


CREATE TABLE customer (c_custkey INTEGER, c_name STRING, c_address STRING, c_nationkey INTEGER, c_phone STRING, c_acctbal DOUBLE, c_mktsegment STRING, c_comment STRING) AS HEAP

INSERT customer.tbl INTO customer

DROP TABLE customer

CREATE TABLE orders (o_orderkey INTEGER, o_custkey INTEGER, o_orderstatus STRING, o_totalprice DOUBLE, o_orderdate STRING,
o_orderpriority STRING, o_clerk STRING, o_shippriority  INTEGER, o_comment STRING) AS HEAP

INSERT orders.tbl INTO orders

DROP TABLE orders

CREATE TABLE region (r_regionkey INTEGER, r_name STRING, r_comment STRING) AS HEAP

INSERT region.tbl INTO region

DROP TABLE region

CREATE TABLE lineitem (l_orderkey INTEGER, l_partkey INTEGER, l_suppkey INTEGER, l_linenumber INTEGER, l_quantity DOUBLE, l_extendedprice DOUBLE, l_discount DOUBLE, l_tax DOUBLE, l_returnflag STRING, l_linestatus STRING, l_shipdate STRING, l_commitdate STRING, l_receiptdate STRING, l_shipinstruct STRING, l_shipmode STRING, l_comment STRING) AS HEAP

INSERT lineitem.tbl INTO lineitem

DROP TABLE lineitem


CREATE TABLE partsupp (ps_partkey INTEGER,ps_suppkey INTEGER,ps_availqty INTEGER ,ps_supplycost DOUBLE , ps_comment STRING) AS HEAP


INSERT partsupp.tbl INTO partsupp

DROP TABLE partsupp

CREATE TABLE part (p_partkey INTEGER,p_name STRING,p_mfgr STRING,p_brand STRING,p_type STRING,p_size INTEGER,p_container STRING,p_retailprice DOUBLE,p_comment STRING) AS HEAP

INSERT part.tbl INTO part

DROP TABLE part

CREATE TABLE supplier (s_suppkey INTEGER,s_name STRING,s_address STRING,s_nationkey INTEGER,s_phone STRING,s_acctbal DOUBLE,s_comment STRING) AS HEAP

INSERT supplier.tbl INTO supplier

DROP TABLE supplier

CREATE TABLE nation (n_nationkey INTEGER,n_name STRING,n_regionkey INTEGER,n_comment STRING) AS HEAP

INSERT nation.tbl INTO nation

DROP TABLE nation


## SET OUTPUT

SET OUTPUT STDOUT

SET OUTPUT mytempfile

SET OUTPUT NONE




##Execute
(Q1)

SELECT SUM (ps.ps_supplycost)
FROM part AS p, supplier AS s, partsupp AS ps
WHERE (p.p_partkey = ps.ps_partkey) AND
      (s.s_suppkey = ps.ps_suppkey) AND
      (s.s_acctbal > 2500.0)

ANSWER: 274251601.970003 
(Q2)

SELECT SUM (c.c_acctbal)
FROM customer AS c, orders AS o
WHERE (c.c_custkey = o.o_custkey) AND
      (o.o_totalprice < 10000.0)

ANSWER: 133122759.670001 
(Q3)

SELECT l.l_orderkey, l.l_partkey, l.l_suppkey
FROM lineitem AS l
WHERE (l.l_returnflag = 'R') AND
      (l.l_discount < 0.04 OR l.l_shipmode = 'MAIL') AND
      (l.l_orderkey > 5000) AND (l.l_orderkey < 6000)

ANSWER: 96 rows in set 
(Q4)

SELECT ps.ps_partkey, ps.ps_suppkey, ps.ps_availqty
FROM partsupp AS ps
WHERE (ps.ps_partkey < 100) AND (ps.ps_suppkey < 50)

ANSWER: 48 rows 
(Q5)

SELECT SUM (l.l_discount)
FROM customer AS c, orders AS o, lineitem AS l
WHERE (c.c_custkey = o.o_custkey) AND
      (o.o_orderkey = l.l_orderkey) AND
      (c.c_name = 'Customer#000070919') AND
      (l.l_quantity > 30.0) AND (l.l_discount < 0.03)

ANSWER: 0.03 
(Q6)

SELECT DISTINCT s.s_name
FROM supplier AS s, part AS p, partsupp AS ps
WHERE (s.s_suppkey = ps.ps_suppkey) AND
      (p.p_partkey = ps.ps_partkey) AND
      (p.p_mfgr = 'Manufacturer#4') AND
      (ps.ps_supplycost < 350.0)

ANSWER: 9964 rows 
(Q7)


SELECT SUM (l.l_extendedprice * (1 - l.l_discount)), l.l_orderkey, o.o_orderdate, o.o_shippriority
FROM customer AS c, orders AS o, lineitem AS l
WHERE (c.c_mktsegment = 'BUILDING') AND
      (c.c_custkey = o.o_custkey) AND (l.l_orderkey = o.o_orderkey) AND
          (l_orderkey < 100 OR o_orderkey < 100)
GROUP BY l_orderkey, o_orderdate, o_shippriority

ANSWER: 7 rows


##Plans
(Q1)

SELECT SUM (ps.ps_supplycost), s.s_suppkey
FROM part AS p, supplier AS s, partsupp AS ps
WHERE (p.p_partkey = ps.ps_partkey) AND
      (s.s_suppkey = ps.ps_suppkey) AND (s.s_acctbal > 2500.0)
GROUP BY s.s_suppkey

ANSWER: 6848 rows
(Q2)

SELECT SUM (c.c_acctbal), c.c_name
FROM customer AS c, orders AS o
WHERE (c.c_custkey = o.o_custkey) AND (o.o_totalprice < 10000.0)
GROUP BY c.c_name

ANSWER: 25359 rows
(Q3)

SELECT l.l_orderkey, l.l_partkey, l.l_suppkey
FROM lineitem AS l
WHERE (l.l_returnflag = 'R') AND
      (l.l_discount < 0.04 OR l.l_shipmode = 'MAIL')

ANSWER: 671392 rows
(Q4)

SELECT DISTINCT c1.c_name, c1.c_address, c1.c_acctbal
FROM customer AS c1, customer AS c2
WHERE (c1.c_nationkey = c2.c_nationkey) AND
      (c1.c_name ='Customer#000070919')

ANSWER: Customer#000070919|b3rPDRarGuz|4309.34
(Q5)

SELECT SUM(l.l_discount)
FROM customer AS c, orders AS o, lineitem AS l
WHERE (c.c_custkey = o.o_custkey) AND
      (o.o_orderkey = l.l_orderkey) AND
      (c.c_name = 'Customer#000070919') AND
      (l.l_quantity > 30.0) AND (l.l_discount < 0.03)

ANSWER: 0.03
(Q6)

SELECT l.l_orderkey
FROM lineitem AS l
WHERE (l.l_quantity > 30.0)

ANSWER: 2402187 rows


(Q7)

SELECT DISTINCT c.c_name
FROM lineitem AS l, orders AS o, customer AS c, nation AS n, region AS r
WHERE (l.l_orderkey = o.o_orderkey) AND
      (o.o_custkey = c.c_custkey) AND
      (c.c_nationkey = n.n_nationkey) AND
      (n.n_regionkey = r.r_regionkey)

ANSWER: 99996 rows
(Q8)

SELECT l.l_discount
FROM lineitem AS l, orders AS o, customer AS c, nation AS n, region AS r
WHERE (l.l_orderkey = o.o_orderkey) AND
      (o.o_custkey = c.c_custkey) AND
      (c.c_nationkey = n.n_nationkey) AND
      (n.n_regionkey = r.r_regionkey) AND
      (r.r_regionkey = 1) AND (o.o_orderkey < 10000)

ANSWER: 2132 rows
(Q9)

SELECT SUM (l.l_discount)
FROM customer AS c, orders AS o, lineitem AS l
WHERE (c.c_custkey = o.o_custkey) AND (o.o_orderkey = l.l_orderkey) AND
      (c.c_name = 'Customer#000070919') AND (l.l_quantity > 30.0) AND
      (l.l_discount < 0.03)

ANSWER: 0.03
(Q10)

SELECT SUM (l.l_extendedprice * l.l_discount)
FROM lineitem AS l
WHERE (l.l_discount<0.07) AND (l.l_quantity < 24.0)

ANSWER: 947887616.680417
                           
