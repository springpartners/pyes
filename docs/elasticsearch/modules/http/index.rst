HTTP Module
===========

The http module allows to expose ElasticSearch :doc:`REST API </elasticsearch/rest_api/index>` over HTTP.


The http mechanism is completely asynchronous in nature, meaning that there is no blocking thread waiting for a response. The benefit of using asynchronous communication for HTTP is solving the `C10k problem <http://en.wikipedia.org/wiki/C10k_problem>`. 

When possible, consider using `HTTP keep alive <http://en.wikipedia.org/wiki/Keepalive#HTTP_Keepalive>` when connecting for better performance and try to get your favorite client not to do `HTTP chunking <http://en.wikipedia.org/wiki/Chunked_transfer_encoding>`. 

Settings
--------

The following are the settings the can be configured for HTTP:


=============================  ===========================================================
 Setting                        Description                                               
=============================  ===========================================================
**http.port**                  A bind port range. Defaults to **9200-9300**.              
**http.max_content_length**    The max content of an HTTP request. Defaults to **100mb**  
=============================  ===========================================================

It also shares the uses the common :doc:`network settings <.//node/network/index>`. 

Disable HTTP
------------

The http module can be completely disabled and not started by setting **http.enabled** to **false**. This make sense when creating non :doc:`data nodes <./../node/data_node/index>` which accept HTTP requests, and communicate with data nodes using the internal :doc:`transport <.//transport/index>`. 

