Terms Facet
===========

Allow to specify field facets that return the N most frequent terms. For example:


.. code-block:: js


    {
        "query" : {
            "match_all" : {  }
        },
        "facets" : {
            "tag" : {
                "terms" : {
                    "field" : "tag",
                    "size" : 10
                }
            }
        }
    }


Note
    It is preferred to have the terms facet executed on a non analyzed field, or a field without a large number of terms it breaks to.


Ordering
--------

Allow to control the ordering of the terms facets, to be ordered by **count**, **term**, **reverse_count** or **reverse_term**. The default is **count**. Here is an example:


.. code-block:: js


    {
        "query" : {
            "match_all" : {  }
        },
        "facets" : {
            "tag" : {
                "terms" : {
                    "field" : "tag",
                    "size" : 10,
                    "order" : "term"
                }
            }
        }
    }


Excluding Terms
---------------

It is possible to specify a set of terms that should be excluded from the terms facet request result:


.. code-block:: js


    {
        "query" : {
            "match_all" : { }
        },
        "facets" : {
            "tag" : {
                "terms" : {
                    "field" : "tag",
                    "exclude" : ["term1", "term2"]
                }
            }
        }
    }


Regex Patterns
--------------

The terms API allows to define regex expression that will control which terms will be included in the faceted list, here is an example:


.. code-block:: js


    {
        "query" : {
            "match_all" : { }
        },
        "facets" : {
            "tag" : {
                "terms" : {
                    "field" : "tag",
                    "regex" : "_regex expression here_"
                    "regex_flags" : "DOTALL"
                }
            }
        }
    }


Term Scripts
------------

Allow to define a script for terms facet to process the actual term that will be used in the term facet collection, and also optionally control its inclusion or not.


The script can either return a boolean value, with **true** to include it in the facet collection, and **false** to exclude it from the facet collection.


Another option is for the script to return a **string** controlling the term that will be used to count against. The script execution will include the term variable which is the current field term used.


For example:


.. code-block:: js


    {
        "query" : {
            "match_all" : {  }
        },
        "facets" : {
            "tag" : {
                "terms" : {
                    "field" : "tag",
                    "size" : 10,
                    "script" : "term + 'aaa'"
                }
            }
        }
    }


And using the boolean feature:


.. code-block:: js


    {
        "query" : {
            "match_all" : {  }
        },
        "facets" : {
            "tag" : {
                "terms" : {
                    "field" : "tag",
                    "size" : 10,
                    "script" : "term == 'aaa' ? true : false"
                }
            }
        }
    }


Multi Fields
------------

The term facet can be executed against more than one field, returning the aggregation result across those fields. For example:


.. code-block:: js


    {
        "query" : {
            "match_all" : {  }
        },
        "facets" : {
            "tag" : {
                "terms" : {
                    "fields" : ["tag1", "tag2"],
                    "size" : 10
                }
            }
        }
    }


Script Field
------------

A script that provides the actual terms that will be processed for a given doc. A **script_field** (or **script** which will be used when no **field** or **fields** are provided) can be set to provide it.


As an example, a search request (that is quite "heavy") can be executed and use either **_source** itself or **_fields** (for stored fields) without needing to load the terms to memory (at the expense of much slower execution of the search, and causing more IO load):


.. code-block:: js


    {
        "query" : {
            "match_all" : {  }
        },
        "facets" : {
            "my_facet" : {
                "terms" : {
                    "script_field" : "_source.my_field",
                    "size" : 10
                },
            }
        }
    }


Or:


.. code-block:: js


    {
        "query" : {
            "match_all" : {  }
        },
        "facets" : {
            "my_facet" : {
                "terms" : {
                    "script_field" : "_fields['my_field']",
                    "size" : 10
                },
            }
        }
    }


Note also, that the above will use the whole field value as a single term.


_index
------

The term facet allows to specify a special field name called **_index**. This will return a facet count of hits per **_index** the search was executed on (relevant when a search request spans more than one index).


Memory Considerations
---------------------

Term facet causes the relevant field values to be loaded into memory. This means that per shard, there should be enough memory to contain them. It is advisable to explicitly set the fields to be **not_analyzed** or make sure the number of unique tokens a field can have is not large.

