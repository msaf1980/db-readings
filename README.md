# Readings in Databases

A list of papers essential to understanding databases and building new data systems. The list is curated and maintained by Reynold Xin ([@rxin](http://twitter.com/rxin)). If you think a paper should be part of this list, please submit a pull request. It might take a while since I need to go over the paper.

If you are reading this and taking the effort to understand these papers, we would love to talk to you about opportunities at [Databricks](https://databricks.com/company/careers).

## <a name='TOC'>Table of Contents</a>

  1. [Basics and Algorithms](#basic-and-algo)
  2. [Essentials of Relational Databases](#essentials)
  3. [Classic System Design](#system-design)
  4. [Columnar Databases](#column)
  5. [Data-Parallel Computation](#data-parallel)
  6. [Concurrency Control](#cc)
  7. [Snapshot Isolation](#si)
  8. [Consensus and Consistency](#consensus)
  9. [Trends (Cloud Computing, Warehouse-scale Computing, New Hardware, Machine Learning)](#trends)
  10. [Miscellaneous](#misc)
  11. [External Reading Lists](#external)


## <a name='basic-and-algo'> Basics and Algorithms
* [The Five-Minute Rule Ten Years Later, and Other Computer Storage Rules of Thumb](papers/5_min_rule_sigmod.pdf) (1997): This paper (and the original one proposed 10 years earlier) illustrates a quantitative formula to calculate whether a data page should be cached in memory or not. It is a delight to read Jim Gray approach to an array of related problems, e.g. how big should a page size be.

* [AlphaSort: A Cache-Sensitive Parallel External Sort](papers/alphasortsigmod.pdf) (1995): Sorting is one of the most essential algorithms in databases, as it is used to do joins, aggregations, and sorts. In algorithms 101 class, CS students are asked to reason about big O complexity and ignore the constant factor. In practice, however, the change in constant from L2 cache can be as big as two or three orders of magnitude. This is a good paper to learn about all the tricks fast sorting implementations use.

* [Patience is a Virtue: Revisiting Merge and Sort on Modern Processors](papers/patsort-sigmod14.pdf) (2014): Sorting revisited. Actually also a good survey on sorting algorithms used in practice and their trade-offs.


## <a name='essentials'> Essentials of Relational Databases

* [Architecture of a Database System](papers/fntdb07-architecture.pdf) (2007): Joe Hellerstein's great overview of relational database systems. This essay walks readers through all components essential to relational database systems.

* [A Relational Model of Data for Large Shared Data Banks](papers/codd.pdf) (1970): Codd's argument for data independence (from 1970), a fundamental concept in relational databases. Despite the current NoSQL trend, I believe ideas from this paper are becoming increasingly important in massively parallel data systems.

* [The Transaction Concept: Virtues and Limitations](http://research.microsoft.com/en-us/um/people/gray/papers/theTransactionConcept.pdf) (1981): Jim Gray's paper about what is a transaction and the properties of atomicity, durability and consistency (does not discuss isolation), along with implementation techniques.

* [ARIES: A Transaction Recovery Method Supporting Fine-Granularity Locking and Partial Rollbacks Using Write-Ahead Logging](papers/aries.pdf) (1992): The first algorithm that actually works: it supports concurrent execution of transactions without losing data even in the presence of failures. This paper is very hard to read because it mixes a lot of low level details in the explanation of the high level algorithm. Perhaps try understand ARIES (log recovery) by reading a database textbook before attempting to read this paper.

* [B-tree and UB-tree](http://www.scholarpedia.org/article/B-tree_and_UB-tree) (2008): A good explanation of B-Tree, B+-Tree and UB-Trees by the Inventor of the B-Tree, Dr Rudolf Bayer.

* [The Ubiquitous B-Tree](https://wwwold.cs.umd.edu/class/fall2002/cmsc818s/Readings/b-tree.pdf) (1979): Reviews B-trees and discusses several major variations of the B-tree, including B*-tree and B+-tree.

* [Efficient Locking for Concurrent Operations on B-Trees](papers/btree.pdf) (1981) and The [R*-tree: An Efficient and Robust Access Method for Points and Rectangles](papers/rstar-tree.pdf) (1990): B-Tree is a core data structure in databases (not just relational). It is optimized and has a low read amplification factor for random lookups of on-disk data. R-tree is an extension of B-tree to support lookups of multi-dimensional data, e.g. geodata.

* [B-trees, Shadowing and Clones](http://liw.fi/larch/ohad-btrees-shadowing-clones.pdf) (2007): Describes a set of B-tree algorithms that respects shadowing and implements cloning whilst providing good concurrency. Takes a top-down approach to the B-tree and provides benchmark results.

* [Stratified B-trees and versioning dictionaries](http://arxiv.org/abs/1103.4282v2) [(video)](https://vimeo.com/26180574) (2011) Improves on semi-external memory versioned B-trees, including CoW B-tree. May have patent encumberance issues http://www.google.com/patents/WO2012110813A1?cl=en.

* [bLSM: A General Purpose Log Structured Merge Tree](http://www.eecs.harvard.edu/~margo/cs165/papers/gp-lsm.pdf) (2012) Improves on the LSM tree with Bloom Filters for index performance and introduces a novel “Spring and Gear” merge scheduler which reduces write pauses.

* [The Bw-Tree: A B-tree for New Hardware Platforms](http://research.microsoft.com/pubs/178758/bw-tree-icde2013-final.pdf) (2013) Provides a latch-free approach to B-trees by exploiting a mapping table of virtualized page addresses and a log structured store.

* [A practical scalable distributed B-tree](http://www.hpl.hp.com/techreports/2007/HPL-2007-193.pdf) (2007): Combines optimistic concurrency control, lazy replication of inner nodes and eager replication of node versions, to achieve a performant distributed B-tree.

* [The Bw-Tree: A B-tree for New Hardware Platforms](papers/bwtree.pdf) (2013) A lock-free version b-tree from Mircosoft, but optimized for modern hardware. It utilizes "delta" to store the modification instead of replacing the whole page/block. The "delta" can be the updating on the tuple or the changing of tree-structure (collapse or split). The "delta" is CPU cache friendly and can easily support the multi-version. 

* [Improved Query Performance with Variant Indexes](papers/variant-index.pdf) (1997): Analytical databases and OLTP databases require different trade-offs. These are reflected in the choices of indexing data structures. This paper talks about a number of index data structures more suitable for analytical databases.

* [On Optimistic Methods for Concurrency Control](papers/occ.pdf) (1981): There are two ways to support concurrency. The first is the pessimistic way, i.e. to lock shared data preemptively. This paper explains an alternatively to locking called Optimistic Concurrency Control. Optimistic approaches assume conflicts are rare and executes transactions without acquiring locks. Before committing the transactions, the database system checks for conflicts and aborts/restarts transactions if conflicts arise.

* [Access Path Selection in a Relational Database Management System](papers/systemr-optimizer.pdf) (1979): The basics of query optimization. SQL is declarative, i.e. you specify using a query language what data you want, not how you want it. There are usually multiple ways (query plans) of executing a query. The database system examines multiple plans and decides on an optimal one (best-effort). This process is called query optimization. The traditional way of doing query optimization is to have a cost-model for different access methods and query plans. This paper explains the cost-model and a dynamic programming algorithm to pick the best plan.

* [Eddies: Continuously Adaptive Query Processing](papers/eddies.pdf) (2000): Traditional query optimization (and the cost model used) is static. There are two problems with the traditional model. First, it is hard to build the cost model absent of data statistics. Second, query execution environment might change in long running queries and a static approach cannot capture the change. Analogous to fluid dynamics, this paper proposes a set of techniques that optimize query execution dynamically. I don't think ideas in Eddies have made their way into commercial systems yet, but the paper is very refreshing to read and might become more important now.

* [How Good Are Query Optimizers, Really?](http://www.vldb.org/pvldb/vol9/p204-leis.pdf): It explores how cardinality estimation, cost model and enumeration space can influence query optimizers. And it introduces a real dataset with real queries which have many correlations, making it much more suitable for doing experiments for query optimizer than some existing datasets such as TPC-H whose data is independent and uniformly distributed.

## <a name='system-design'> Classic System Design

* [A History and Evaluation of System R](papers/systemr.pdf) (1981): There were System R from IBM and Ingres from Berkeley, two systems that showed relational database was feasible. This paper describes System R. It is impressive and scary to note that the internals of relational database systems in 2012 look a lot like System R in 1981.

* [The Google File System](papers/gfs.pdf) (2003) and [Bigtable: A Distributed Storage System for Structured Data](papers/bigtable.pdf) (2006): Two core components of Google's data infrastructure. GFS is an append-only distributed file system for large sequential reads (data-intensive applications). BigTable is high-performance distributed data store that builds on GFS. One way to think about it is that GFS is optimized for high throughput, and BigTable explains how to build a low-latency data store on top of GFS. Some of these might have been replaced by newer proprietary technologies internal to Google, but the ideas stand.

* [Chord: A Scalable Peer-to-peer Lookup Service for Internet Applications](papers/chord.pdf) (2001) and [Dynamo: Amazon’s Highly Available Key-value Store](papers/dynamo.pdf) (2007): Chord was born in the days when distributed hash tables was a hot research. It does one thing, and does it really well: how to look up the location of a key in a completely distributed setting (peer-to-peer) using consistent hashing. The Dynamo paper explains how to build a distributed key-value store using Chord. Note some design decisions change from Chord to Dynamo, e.g. finger table O(logN) vs O(N), because in Dynamo's case, Amazon has more control over nodes in a data center, while Chord assumes peer-to-peer nodes in wide area networks.

* [Windows Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](http://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf) (2011) Windows Azure Storage (WAS) is a scalable cloud storage system that has been in production since November 2008. It is used inside Microsoft for applications such as social networking search, serving video, music and game content, managing medical records, and more. The paper describes Windows Azure Storage architecture.

## <a name='column'> Columnar Databases

Columnar storage and column-oriented query engine are critical to analytical workloads, e.g. OLAP. It's been almost 15 years since it first came out (the MonetDB paper in 1999), and almost every commercial warehouse database has a columnar engine by now.

* [C-Store: A Column-oriented DBMS](papers/cstore.pdf) (2005) and [The Vertica Analytic Database: C-Store 7 Years Later](papers/vertica-7-years.pdf) (2012): C-Store is an influential, academic system done by the folks in New England. Vertica is the commercial incarnation of C-Store.

* [Column-Stores vs. Row-Stores: How Different Are They Really?](papers/column-vs-row.pdf) (2012): Discusses the importance of both the columnar storage and the query engine.

* [Dremel: Interactive Analysis of Web-Scale Datasets](papers/dremel.pdf) (2010): A jaw-dropping paper when Google published it. Dremel is a massively parallel analytical database used at Google for ad-hoc queries. The system runs on thousands of nodes to process terabytes of data in seconds. It applies columnar storage to complex, nested data structures. The paper talks a lot about the nested data structure support, and is a bit light on the details of the query execution. Note that a number of open source projects are claiming they are building "Dremel". The Dremel system achieves low-latency through massive parallelism and columnar storage, so the model doesn't necessarily make sense outside Google since very few companies in the world can afford thousands of nodes for ad-hoc queries.


## <a name='data-parallel'> Data-Parallel Computation

* [MapReduce: Simplified Data Processing on Large Clusters](papers/mapreduce.pdf) (2004): MapReduce is both a programming model (borrowed from an old concept in functional programming) and a system at Google for distributed data-intensive computation. The programming model is so simple yet expressive enough to capture a wide range of programming needs. The system, coupled with the model, is fault-tolerant and scalable. It is probably fair to say that half of the academia are now working on problems heavily influenced by MapReduce.

* [Resilient Distributed Datasets: A Fault-Tolerant Abstraction for In-Memory Cluster Computing](papers/spark.pdf) (2012): This is the research paper behind the Spark cluster computing project at Berkeley. Spark exposes a distributed memory abstraction called RDD, which is an immutable collection of records distributed across a cluster's memory. RDDs can be transformed using MapReduce style computations. The RDD abstraction can be orders of magnitude more efficient for workloads that exhibit strong temporal locality, e.g. query processing and iterative machine learning. Spark is an example of why it is important to separate the MapReduce programming model from its execution engine.

* [Shark: SQL and Rich Analytics at Scale](papers/shark.pdf) (2013): Describes the Shark system, which is the SQL engine built on top of Spark. More importantly, the paper discusses why previous SQL on Hadoop/MapReduce query engines were slow.

* [Readings in Database Systems(The Red Book)](http://www.redbook.io): A compact and informative book by Peter Bailis, Joseph Hellerstein and Michael Stonebraker about the history and state of the art researches and developments an the field of data management systems.


## <a name="cc"> Concurrency Control

* [Concurrency Control in Distributed Database Systems](https://www.cs.berkeley.edu/~brewer/cs262/concurrency-distributed-databases.pdf) (1981): Reviews 48 principal methods of Concurrency Control in the know literature and consoldates them to present * Multi-version Concurrency Control* (MVCC).

* [High-Performance Concurrency Control Mechanisms for Main-Memory Databases](http://vldb.org/pvldb/vol5/p298_per-akelarson_vldb2012.pdf) (2012): Presents efficient concurrency control mechanisms for main-memory databases using multiversioning.

* [Rethinking Serializable Multiversion Concurrency Control](http://www.vldb.org/pvldb/vol8/p1190-faleiro.pdf) (2015): Presents a new concurrency control protocol for main-memory multi-versioned database systems - BOHM.

## <a name='si'> Snapshot Isolation

* [A Critique of ANSI SQL Isolation Levels](http://research.microsoft.com/pubs/69541/tr-95-51.pdf) (1995): Defines isolation levels in terms of phenomena, and shows that these and the ANSI SQL definitions fail to characterize several popular isolation levels. It also defines an important multiversion isolation type: *Snapshot Isolation (SI)*.

* [A Read-Only Transaction Anomaly Under Snapshot Isolation](http://www.sigmod.org/publications/sigmod-record/0409/2.ROAnomONeil.pdf) (2004): Disproves the assumption that under Snapshot Isolation, read-only transactions always execute serializably provided the concurrent update transactions are serializable.

* [Serializable Isolation for Snapshot Databases (SSI)](https://courses.cs.washington.edu/courses/cse444/08au/544M/READING-LIST/fekete-sigmod2008.pdf) (2008) and ([revised 2009 (ESSI)](http://dl.acm.org/citation.cfm?doid=1620585.1620587)): Describes a concurrency control algorithm that detects and prevents Snapshot Isolation anomalies at run-time, thus providing serializable isolation. Both papers are included for comparison, yet the second paper is more comprehensive and includes protection against additional phenomena and could be regarded as *Enhanced Serializable Snapshot Isolation (ESSI)*.

* [Precisely Serializable Snapshot Isolation (PSSI)](http://www.cs.umb.edu/~eoneil/PSSI_ICDE11_Numbered.pdf) (2011): Defines an algorithm for precisely detecting Snapshot Isolation anomalies, resulting in less false-positive aborts than ESSI. Discuesses implementation of the algorithm in MySQL's InnoDB.

* [Serializable Isolation in PostgreSQL](http://drkp.net/papers/ssi-vldb12.pdf) (2012):
Discusses the trade-offs between SSI, ESSI and PSSI and the approach to implementation of SSI in PostgresSQL.


## <a name='consensus'> Consensus and Consistency

* [Paxos Made Simple](papers/paxos.pdf) (2001): Paxos is a fault-tolerant distributed consensus protocol. It forms the basis of a wide variety of distributed systems. The idea is simple, but notoriously difficult to understand (perhaps due to the way the original Paxos paper was written).

* [The Raft Consensus Algorithm](papers/raft.pdf) (2014) : Raft is a consensus algorithm designed as an alternative to Paxos. It was meant to be more understandable than Paxos by means of separation of logic, but it is also formally proven safe and offers some new features.[1] Raft offers a generic way to distribute a state machine across a cluster of computing systems, ensuring that each node in the cluster agrees upon the same series of state transitions.

* [Pirogue, a lighter dynamic version of the Raft distributed consensus algorithm](http://ipccc.org/ipccc2015/Proceedings/data/60894-ieee-ipccc-1.2778729/t005-1.2779313/f005-1.2779314/session05-03-1.2779321/session05-03-1.2779322.html) (2015): Introduces a dynamic-linear voting algorithm and reduces the number of nodes required in a Raft cluster.

* [CAP Twelve Years Later: How the "Rules" Have Changed](papers/cap.pdf) (2012): The CAP theorem, proposed by Eric Brewer, asserts that any net­worked shared-data system can have only two of three desirable properties: Consistency, Availability, and Partition-Tolerance. A number of NoSQL stores reference CAP to justify their decision to sacrifice consistency. This is Eric Brewer's writeup on CAP in retrospective, explaining "'2 of 3' formulation was always misleading because it tended to oversimplify the tensions among properties."


## <a name='trends'> Trends (Cloud Computing, Warehouse-scale Computing, New Hardware, Machine Learning)

* [A View of Cloud Computing](papers/cloud-computing.pdf) (2010): This is THE paper on Cloud Computing. This paper discusses the economics and obstacles of cloud computing (referring to the elasticity of resources, not the consumer-facing "cloud") from a technical perspective. The obstacles presented in this paper will impact design decisions for systems running in the cloud.

* [The Datacenter as a Computer: An Introduction to the Design of Warehouse-Scale Machines](papers/data-center-computer.pdf): Google's Luiz André Barroso and Urs Hölzle explains the basics of data center hardware and software for warehouse-scale computing. There is an [accompanying video](http://dl.acm.org/citation.cfm?id=2019527&bnc=1). The video talks about the importance of cutting long-tail latency in massively parallel systems. The other key idea is the disaggregation of resources. Technologies such as GFS/HDFS already disaggregate disks because of high network bandwidth, but yet to see the same trend applying to DRAMs because that'd require low-latency networking.

* [SageDB: A Learned Database System](papers/sageDB.pdf) (2019): SageDB is an emerging database system jointly proposed by Google and MIT and Brown University researchers. It presents a radical new approach to build database systems, by using using machine learning models combined with program synthesis to generate system components.

* [CryptDB: Protecting Confidentiality with Encrypted Query Processing](http://people.csail.mit.edu/nickolai/papers/raluca-cryptdb.pdf) (2011) Details a database system that wrapped column fields in several layers of encryption, depending on the datatype. As the system receives queries that demand more computation (say, comparisons), the columns affected are decrypted to the maximum level that still permits the query. This system was used against five historical and live databases at MIT.

* [Orthogonal Security With Cipherbase](http://research.microsoft.com/apps/pubs/default.aspx?id=179425) (2013) Describes a Microsoft Research system to permit queries over encrypted data by joining a typical sql server on untrusted hardware with an FPGA. The trusted hardware is capable of decrypting the fields and performing sensitive query computations using onboard keys.


## <a name='misc'> Miscellaneous

* [Reflections on Trusting Trust](papers/trusting-trust.pdf) (1984): Ken Thompson's Turing Award acceptance speech in 1984, describing black box backdoor issues and pointing out trust is not absolute.

* [What Goes Around Comes Around](papers/goes-around.pdf): Michael Stonebraker and Joseph M. Hellerstein provide a summary of 35 years of data model proposals, grouped into 9 different eras. The paper discusses the proposals of each era, and show that there are only a few basic data modeling ideas, and most have been around a long time. Later proposals inevitably bear a strong resemblance to certain earlier proposals.


## <a name='external'> External Reading Lists

A number of schools have their own reading lists for graduate students in databases.

* Berkeley [PhD prelim exam reading list](http://www.eecs.berkeley.edu/GradAffairs/CS/Prelims/db.html) and [CS286 grad database class reading list](http://www.cs286.net/home/reading-list)
* [Brown CSCI 2270 Advanced Topics in Database Management](http://www.cs.brown.edu/courses/cs227/papers.html)
* [Stanford PhD qualifying exam](http://infolab.stanford.edu/db_pages/infoqual.html)
* MIT: [Database Systems 6.830 year 2014](http://db.csail.mit.edu/6.830/sched.html) and [year 2010](http://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-830-database-systems-fall-2010/readings/)
* [Wisconsin Database Qualifying Exam Reading List (2014)](https://www.cs.wisc.edu/sites/default/files/pictures/Database%20systems%20qual_Summer%202014.pdf)
* [CMU 15-721 Database Systems Reading List (Spring 2016)](http://15721.courses.cs.cmu.edu/spring2016/schedule.html)
* [TUM HyPer List of all publications](http://hyper-db.de/index.html#publications)
