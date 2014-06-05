---
layout: post
title:  "You don't have to stretch for search: elasticsearch"
date:   2014-06-05 16:12:09
categories: frameworks
---

#tl;dr

Elasticsearch is a document oriented database with search built-in. This
post is a short introduction of its capabilites and gives tips for debugging queries.

#Elasticsearch - What it is

[Elasticsearch](http://www.elasticsearch.org) is a database, that's
built around [Apache Lucene](http://lucene.apache.org), the formidable
open source search library. It is also a document oriented database,
which can be compared to the popular [MongoDB](http://www.mongodb.org/).
And it comes with a complete REST API!

#Database features

The database offers several datatypes:

* string
* integer
* long
* float
* doubl
* boolean
* date
* geo\_point
* geo\_shape
* null
* array
* ip
* attachment
* object

You can define document's data types explicitly via [mappings](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/mapping.html). If you don't, elasticsearch will guess the mapping
from the provided data.

The database provides automatic, yet configurable, clustering and sharding. I haven't tried those features extensively. But my overall
impression of elasticsearch is pretty positive, so I guess, those features also work nicely.

A note on terminology: a "collection" (MongoDB) or "database" (RDBMS) is called **"index"** in elasticsearch. This can cause some confusion, as the term index has a different meaning traditionally.

#Search features

Now to the really cool part: **search**.

Lucene has a lot to offer, but the configuration can be a [PITA](http://en.wiktionary.org/wiki/pain_in_the_ass) sometimes.
Elasticsearch assists the user with sensible defaults and a consistent JSON based API.

Conceptually, there are a lot of **queries** and **filters**. The main difference is, that filters can be cached and are therefore preferred to queries performancewise.
Both, queries and filters, are defined as a JSON document, that you send to the server.
A simple [**match**](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/query-dsl-match-query.html) query looks like this:

{% highlight json %}
{
    "match" : {
        "company" : "crealytics"
    }
}
{% endhighlight %}


If you want to combine queries, this can easily be done with [bool query](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/query-dsl-bool-query.html).
Simple example:

{% highlight json %}
{
    "bool" : {
        "must" : {
            "term" : { "company" : "crealytics" }
        },
        "should" :
            {
                "term" : { "tag" : "wow" }
            },
        "minimum_should_match" : 1,
        "boost" : 1.0
    }
}
{% endhighlight %}

If you want to find out, which part of the word matched, you should use the [highlighting feature](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/search-request-highlighting.html).

And there's also the [percolator API](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/search-percolate.html), that stores queries in the database instead of documents.
That way you can easily detect, if a new estate entry in the database matches the house search of one of your users, for example.

There are lots more options, like the [suggestion feature](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/search-suggesters.html) or [facetted search](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/search-facets.html).
But I haven't tried those out, yet. So there's still land to conquer for you.

A good way to find out, how the queries and settings work, is to just play around with your elasticsearch instance.
There are several nice plugins to do that. So you don't even have to touch a single line of code or use CURL like a pro.

#Cool addons

A very useful plugin is **sense**, now included in the official **marvel** plugin:
[http://www.elasticsearch.org/guide/en/marvel/current/#\_sense](http://www.elasticsearch.org/guide/en/marvel/current/#\_sense)

Another plugin, that I found very practical, is **inquisitor**:
[https://github.com/polyfractal/elasticsearch-inquisitor](https://github.com/polyfractal/elasticsearch-inquisitor)

All known plugins can be found here:
[http://www.elasticsearch.org/guide/en/elasticsearch/reference/1.x/modules-plugins.html#known-plugins](http://www.elasticsearch.org/guide/en/elasticsearch/reference/1.x/modules-plugins.html#known-plugins)


#Debugging queries

There will be times when you ask yourself *"Why did this query match word X?"*.
Elasticsearch provides several useful tools to help answer this question.
The following approaches have worked very well for me:

##Is the token stored in the index the way you expect it to?

Take a look at the mappings and settings of the index. Are your customizations actually there? Elasticsearch creates default settings and mappings without error message
when you pass them wrong. You can take a look at both when calling the route
`http://localhost:9200/INDEXNAME/_mapping` and `http://localhost:9200/INDEXNAME/_settings`.

##What does the tokenizer do to the token?

You can check this by requesting the tokenization result from the API.

`curl -XGET 'localhost:9200/_analyze?analyzer=standard' -d 'crealytics is awesome'`

yields a different result than

`curl -XGET 'localhost:9200/_analyze?analyzer=keyword' -d 'crealytics is awesome'`

##Why exactly does this match?

If you have a strange looking search match, you can use the EXPLAIN API to have a closer look at the internals of elasticsearch.
This can be compared to the [EXPLAIN function of other databases like PostgreSQL](http://www.postgresql.org/docs/9.3/static/sql-explain.html).


Usage:
`curl -XGET 'localhost:9200/INDEX_NAME/TYPE_NAME/ID/_explain' -d 'YOUR_QUERY'`

Elasticsearch explains then, why **YOUR_QUERY** matched exactly **ID** document.


#Additional useful links

[Securing your elasticsearch cluster](https://www.found.no/foundation/elasticsearch-security/)

