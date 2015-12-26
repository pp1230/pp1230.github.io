---
layout: post
title: Cassandra Lucene Index
description: Stratio’s Cassandra Lucene Index, derived from Stratio Cassandra, is a plugin for Apache Cassandra that extends its index functionality to provide near real time search such as ElasticSearch or Solr, including full text search capabilities and free multivariable, geospatial and bitemporal search. It is achieved through an Apache Lucene based implementation of Cassandra secondary indexes, where each node of the cluster indexes its own data. Stratio’s Cassandra indexes are one of the core modules on which Stratio’s BigData platform is based.
keywords: java,spring data,jpa
---


##Stratio’s Cassandra Lucene Index

<p>Stratio’s Cassandra Lucene Index, derived from <a href="https://github.com/Stratio/stratio-cassandra">Stratio Cassandra</a>, is
a plugin for <a href="http://cassandra.apache.org/">Apache Cassandra</a> that extends its index functionality to provide near
real time search such as ElasticSearch or Solr, including <a href="http://en.wikipedia.org/wiki/Full_text_search">full text search</a>
capabilities and free multivariable, geospatial and bitemporal search. It is achieved through an <a href="http://lucene.apache.org/">Apache Lucene</a>
based implementation of Cassandra secondary indexes, where each node of the cluster indexes its own data. Stratio’s
Cassandra indexes are one of the core modules on which <a href="http://www.stratio.com/">Stratio’s BigData platform</a> is based.</p>
<p>Index <a href="http://en.wikipedia.org/wiki/Relevance_(information_retrieval)">relevance searches</a> allows you to retrieve the
<em>n</em> more relevant results satisfying a search. The coordinator node sends the search to each node in the cluster, each node
returns its <em>n</em> best results and then the coordinator combines these partial results and gives you the <em>n</em> best of them,
avoiding full scan. You can also base the sorting in a combination of fields.</p>
<p>Index filtered searches are a powerful help when analyzing the data stored in Cassandra with <a href="http://es.wikipedia.org/wiki/MapReduce">MapReduce</a>
frameworks as <a href="http://hadoop.apache.org/">Apache Hadoop</a> or, even better, <a href="http://spark.apache.org/">Apache Spark</a>.
Adding Lucene filters in the jobs input can dramatically reduce the amount of data to be processed, avoiding full scan.</p>
<p>Any cell in the tables can be indexed, including those in the primary key as well as collections. Wide rows are also
supported. You can scan token/key ranges, apply additional CQL3 clauses and page on the filtered results.</p>
<p>This project is not intended to replace Apache Cassandra denormalized
tables, inverted indexes, and/or secondary indexes. It is just a tool
to perform some kind of queries which are really hard to be addressed
using Apache Cassandra out of the box features.</p>
<p>More detailed information is available at <a href="/pp1230/cassandra-lucene-index/blob/branch-2.2.4/doc/src/site/sphinx/documentation.rst">Stratio’s Cassandra Lucene Index documentation</a>.</p>
<a name="user-content-features"></a>


###Features

Stratio’s Cassandra Lucene Index and its integration with Lucene search technology provides:

* Full text search
* Geospatial search
* Bitemporal search
* Boolean (and, or, not) search
* Near real-time search
* Relevance scoring and sorting
* General top-k queries
* Custom analyzers
* CQL complex types (list, set, map, tuple and UDT)
* CQL user defined functions (UDF)
* Third-party CQL-based drivers compatibility
* Spark compatibility
* Hadoop compatibility
* Smart Chinese Analyzer
* Not yet supported:

Thrift API
* Legacy compact storage option
* Indexing counter columns
* Columns with TTL
* Static columns

###Requirements

* Cassandra (identified by the three first numbers of the plugin version)
* Java >= 1.7 (OpenJDK and Sun have been tested)
* Maven >= 3.0
###Build and install

Stratio’s Cassandra Lucene Index is distributed as a plugin for Apache Cassandra. Thus, you just need to build a JAR containing the plugin and add it to the Cassandra’s classpath:

Build the plugin with Maven: 
{%highlight c%}
$ mvn clean package
{%endhighlight%}
Copy the generated JAR to the lib folder of your compatible Cassandra installation:
{%highlight c%}
$ cp plugin/target/cassandra-lucene-index-plugin-*.jar <CASSANDRA_HOME>/lib/
{%endhighlight%}
Start/restart Cassandra as usual

Alternatively, patching can also be done with this Maven profile, specifying the path of your Cassandra installation,
this task also delete previous plugin's JAR versions in CASSANDRA_HOME/lib/ directory:
{%highlight c%}
mvn clean package -Ppatch -Dcassandra_home=<CASSANDRA_HOME>
{%endhighlight%}
If you don’t have an installed version of Cassandra, there is also an alternative profile to let Maven download and patch the proper version of Apache Cassandra:
{%highlight c%}
$ mvn clean package -Pdownload_and_patch -Dcassandra_home=<CASSANDRA_HOME>
{%endhighlight%}
Now you can run Cassandra and do some tests using the Cassandra Query Language:
{%highlight c%}
$ <CASSANDRA_HOME>/bin/cassandra -f
$ <CASSANDRA_HOME>/bin/cqlsh
{%endhighlight%}
The Lucene’s index files will be stored in the same directories where the Cassandra’s will be. The default data directory is /var/lib/cassandra/data, and each index is placed next to the SSTables of its indexed column family.

For more details about Apache Cassandra please see its documentation.

###Example:

####1.Creat Table

{%highlight sql linenos%}
USE demo;
CREATE TABLE test2 (
    id INT PRIMARY KEY,
    body TEXT,
    lucene TEXT
);
{%endhighlight%}

####2.Create Index
{%highlight sql linenos%}
USE demo;
CREATE CUSTOM INDEX test2_index ON test2 (lucene)
USING 'com.stratio.cassandra.lucene.Index'
WITH OPTIONS = {
    'refresh_seconds' : '1',
    'schema' : '{
        fields : {
            id    : {type : "integer"},
            body  : {type : "text", analyzer : "chinese"}
        }
    }'
};
{%endhighlight%}

####3.Insert record

{%highlight sql linenos%}
use demo;
insert into test2(id, body) values(1 , 'i come from china');
insert into test2 (id, body) values(2 , '我来自中国，住在南昌。');
insert into test2 (id, body) values(3 , '我来自中东，这里有丰富的石油。');
insert into test2 (id, body) values(4 , '今天有一个好天气，可以去爬山。');
{%endhighlight%}

####4.Query

* Query1:
{%highlight sql linenos%}
use demo;
SELECT * FROM test2 WHERE lucene='{
    query  : {type:"phrase", field:"body", value:"天有", slop:1}
}' limit 100;
{%endhighlight%}

* Query2:
{%highlight sql linenos%}
use demo;
SELECT * FROM test2 WHERE lucene='{
    query  : {type:"phrase", field:"body", value:"今天", slop:1}
}' limit 100;
{%endhighlight%}

####5.Result

* Result1:
Null
* Result2:
今天有一个好天气，可以去爬山。


