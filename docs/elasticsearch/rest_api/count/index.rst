Count
=====

The count API allows to easily execute a query and get the number of matches for that query. It can be executed across one or more indices and across one or more types. The query can either be provided using a simple query string as a parameter, or using the :doc:`Query DSL <./../query_dsl/index>` defined within the request body. Here is an example:


.. code-block:: js

    $ curl -XGET 'http://localhost:9200/twitter/tweet/_count?q=user:kimchy'
    
    $ curl -XGET 'http://localhost:9200/twitter/tweet/_count' -d '
    {
        "term" : { "user" : "kimchy" }
    }'


Both examples above end up doing the same thing, which is count the number of tweets from the twitter index for a certain user. The result of the commands is:


.. code-block:: js


    {
        "count" : 1,
        "_shards" : {
            "total" : 5,
            "successful" : 5,
            "failed" : 0
        }
    }


Multiple Indices and Types
--------------------------

The count API can be applied to multiple types within an index, and across multiple indices. For example, we can count all documents across all types that match a **user** field with value **kimchy**:


.. code-block:: js

    $ curl -XGET 'http://localhost:9200/_count?q=user:kimchy'


We can also count within specific types:


.. code-block:: js

    $ curl -XGET 'http://localhost:9200/twitter/tweet,user/_count?q=user:kimchy'


We can also count all tweets with a certain tag across several indices (for example, when each user has his own index):


.. code-block:: js

    $ curl -XGET 'http://localhost:9200/kimchy,elasticsearch/_count?q=tag:wow'


Or even count across all indices:


.. code-block:: js

    $ curl -XGET 'http://localhost:9200/_count?q=tag:wow'


Request Parameters
------------------

When executing count using the query parameter **q**, the query passed is a query string using Lucene query parser. There are additional parameters that can be passed:


========================  ==================================================================================
 Name                      Description                                                                      
========================  ==================================================================================
 **df**                    The default field to use when no field prefix is defined within the query.       
 **analyzer**              The analyzer name to be used when analyzing the query string.                    
 **default_operator**      The default operator to be used, can be **AND** or **OR**. Defaults to **OR**.   
========================  ==================================================================================

Request Body
------------

The count can use the :doc:`Query DSL <./../query_dsl/index>` within its body in order to express the query that should be executed. The body content can also be passed as a REST parameter named **source**.


Note
    Both HTTP GET and HTTP POST can be used to execute count with body. Since not all clients support GET with body, POST is allowed as well.


Distributed
-----------

The count operation is broadcast across all shards. For each shard id group, a replica is chosen and executed against it. This means that replicas increase the scalability of count.


Routing
-------

The routing value (a comma separated list of the routing values) can be specified to control which shards the count request will be executed on.


