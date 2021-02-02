#The Pathologies of Bid Data Note 

## P1
1980 US Census database: No practical way to provide
drawback:
    too expensive to a crawl
	limited the kinds of questions that could be asked about the census data
    6.75 billion rows and 10 cols: all human on the planet
	
ex: "what is the median age by sex for each country? "

## P3
The author implement a simple algorithm and test:
	this algo was limited promarily by the speed at which data could be fetched from disks.
	However, it can fit in a $15K Dell server with 128GB RAM. Thus, the author would hesitate to call this "big data". The LHC at CERN produce 150,000 times as much raw data each year.

Then, he tried to use PostgreSQL running on relatively hefty hardware:
	the execution time increased rapidly as the number of rows grew past 1 million.
	applied billion rows -> more than 24 hours

Thus : PostgreSQL was not scaling gracefully to this big dataset.

Revealed the problem: a viable, if sub- optimal strategy given a few million rows, but a very poor one when facing a billion. 

## P4
PostgreSQL’s difficulty here was in analyzing the data, not in storing it.

SO~ the author maintains that there is no pathology here! The pathologies of big data are pri- marily those of analysis.

Traditional techniques such as RDBMS-based dimentional modeling and cube-based OLAP turn out to be either too slow or too limited to support asking the really interesting questions about warehosed data.

To understand how to avoid pathologies of data, we need to consider what exacly make it "big"?

## Section 2:Dealing with Big Data

	- Data = "Things given" in Latin 
	- Data tends to exhibit predictable characteristics
	- rule: the largest cardinalities of most datasets are much smaller than the total numbers of observations.

## P5
	Upper bound: 6.75 billion (world population)
	What makes most big data big is repeated observations over time and/ or space. 
	
	EX: 
	The Web log records mil- lions of visits a day to a handful of pages; 
	the cellphone database stores time and location every 15 seconds for each of a few million phones; 
	the retailer has thousands of stores, tens of thousands of products, and millions of customers but logs billions and billions of individual transactions in a year. 

	Most large dataset have inherenet "Temporal" or "Spatial" dimensions. or both.
	-> big data can cause performance problems, especially the databases are involved.

	The prevailing database model today, however, is the relational database, and this model explicitly ig- nores the ordering of rows in tables.
	This kind of model eschewing the idea of an inherent order on tables, will inevitably end up retrieving data in a non- sequential fashion once it grows large enough that it no longer fits in memory.

	As the total amount of data stored in the database grows, the problem only becomes more significant.

	"""To achieve acceptable performance for highly order-dependent queries on truly large data, one must be willing to con- sider abandoning the purely relational database model"""

	As dataset sizes grow, it becomes increasingly important to choose algorithms that exploit the ef- ficiency of sequential access as much as possible at all stages of processing. 

	seconds -> hours : far more painful

	The penalty for inefficient access pat- terns increases disproportionately as the limits of successive stages of hardware are exhausted

## P6

	Sequential access Faster than Random access! : Figure 3

	In a very real sense, all of the modern forms of storage improve only in degree, not in their essential nature, upon that most venerable and sequential of storage media: the tape.

	-Since RAN is a scarce resource, the vest solution when constructing a large database for analytical purpose is to build a *fully denormalized table*. Figure 4.

## P7
## Section 3: Hard Limit
    applications with hard limits on the size of data they can handle.
	
ZOI (zero-one-infinity) rule : a program should “allow none of foo, one of foo, or any number of foo.

hardware—chiefly memory and CPU limitations—is of- ten a major factor in software limits on dataset size.
Many apps read entire datasets into memory and work with them there: ex: R.
	
    - Memory-bound application naturally exhibit higher performance than disk-bound ones.
	- However, requring all data to fit in memory means that if u have dataset larger than ur installed RAM -> GG.


even 64-bit binary is available, 32-bit code still pervade: 
	ex: 64-bit versions of R (available for Linux and Mac) use signed 32-bit integers to rep- resent lengths, limiting data frames to at most 231–1, or about two billion rows. Even on a 64-bit system with suf- ficient RAM to hold the data, therefore, a 6.75-billion-row dataset such as the earlier world census example ends up being too big for R to handle.
## P8
## Section 4:Distributed Computing as a Strategy for Big Data

- Buying 8 'commodity' servers with 8 cores and 128GB RAM is more cost-effective than acquiring a single system with 64 processors and 1 TB RAM.
-> distributed computing is the most success- ful strategy known for analyzing very large datasets.
- Distributing analysis over multiple computers has significant performance costs:
    - both bandwidth (sequential access speed) and latency (thus, ran- dom access speed) are several orders of magnitude worse than RAM.
    - However, the highest-speed local network technologies have now surpassed most locally attached disk systems with respect to bandwidth, and network latency is naturally much lower than disk latency.(modern local 網路帶寬>local disk, latency < disk latency)
    - Performance cost of storing and retrieving data on other nodes in a network is comparable to the cost of using disk. 
    - Also, processing data has huge advantage as long as the analysis is amenable to parallel processing.
    - locality of reference via sequential access is also crucial to processes that rely on disk I/O. That is, does not require simultaneous processing of many disparate parts of the datasets(because communication between the different processing domain is expensive)
    - communication between nodes is required

##  P9
-  distributed analysis of big data comes with its own set of “gotchas.”(drawbacks)
    - Each node has different loads of work (nonuniform work distribution): (could illustrate more)
    - -> how the data is distributed across nodes?
        - Whether the rows are clustered by sensor or by time stamp makes a big difference in the degree of parallelism with which different queries will execute.
    - Another issue: Reliability

## Section 5: A Meta-Definition
- Answer to What is big data?
- **“data whose size forces us to look beyond the tried-and- true methods that are prevalent at that time.”**


