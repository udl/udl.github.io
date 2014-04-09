---
layout: post
title:  "Disabling SSL verification for certain ruby client libraries"
date:   2014-04-09 13:27:09
categories: frameworks, elasticsearch
---

There are times, when you want to disable SSL verification.
For example, when you connect to a HTTPS server with an self-signed certificate.

(Yes, the proper way to deal with this would be importing the certificate.
But let's face it: developers are lazy sometimes.)

To disable verification for [HTTPClient](https://github.com/nahi/httpclient) (for example in the [neography](https://github.com/maxdemarzi/neography) gem):

```
neo_client = Neography::Rest.new
neo_client.connection.client.ssl_config.verify_mode=OpenSSL::SSL::VERIFY_NONE
```

Disabling SSL verification in [Faraday](https://github.com/lostisland/faraday) (for example used in the [elasticsearch](https://github.com/elasticsearch/elasticsearch-ruby) gem):

```
client = Elasticsearch::Client.new
client.transport.connections.first.connection.instance_variable_set :@ssl, { verify: false }
```

