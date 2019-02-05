chef-nsq
========
Installs and configures [NSQ](http://bitly.github.io/nsq).

Requirements
------------

### Chef
Chef version 0.10.10+ and Ohai 0.6.12+ are required.

### Platform
* Ubuntu 12.10
* Ubuntu 14.04
* Ubuntu 18.04, _kinda_, support has been added to allow the `nsqd` and `nsqlookupd` roles to work via systemd.

**Notes**: This cookbook has been tested on the listed platforms only. It may work on other platforms with modification.

Attributes
----------
Each of the attributes come with a default setting. Read the files in the `attributes/` directory for a full description of what each setting does along with its default value.

Recipes
-------
### default
Includes the `chef-nsq` recipe to install the base NSQ packages.

Data Bags
---------
None.

Roles
-----
There are 3 primary roles used for setting up NSQ:

#### nsqadmin - This role is to add nsqadmin to a host.

```ruby
name 'nsqadmin'
description 'nsqadmin'
run_list(
    'recipe[nsq::nsqadmin]'
)

default_attributes(
    nsq: {
        version: '0.2.24',
        go_version: 'go1.1.2'
    }
)
```

#### nsqd - This role is add an nsqd to a host.

```ruby
name 'nsqd'
description 'nsqd'
run_list(
    'recipe[nsq::nsqd]'
)

default_attributes(
  nsq: {
    version: '0.2.24',
    go_version: 'go1.1.2'
  }
)
```

#### nsqlookupd - This role adds an nsqlookupd instance to a host.

```ruby
name 'nsqlookupd'
description 'nsqlookupd'
run_list(
    'recipe[nsq::nsqlookupd]'
)

default_attributes(
    nsq: {
        version: '0.2.24',
        go_version: 'go1.1.2'
    }
)
```

Definitions
-----------
None

Libraries
---------
None

Resources/Providers
-------------------
None

Usage
-----

### Setup
In the environment.rb file relevant to your environment, it is useful to specify the static hostnames of your lookup nodes.  You can put these addresses in DNS to make replacement easier.

```ruby
nsq: {
  nsqd: {
    lookupd_tcp_address: (1..3).map{|i| "lookup-00#{i}.nsq.example.com:4160" },
    lookupd_http_address: (1..3).map{|i| "lookup-00#{i}.nsq.example.com:4161" },
    statsd_interval: '60s'
  }
}
```

Once you have setup the lookup servers, you can optionally setup _nsqadmin_.  _nsqadmin_ can easily be deployed to any host with a statsd graphite host optional. These attributes can also be set in your environment.

```ruby
nsq: {
  nsqadmin: {
    statsd_interval: '60s',
    graphite_url: 'http://graphite-001.example.com',
    proxy_graphite: true,
    use_statsd_prefixes: false
  }
}
```

After the lookup servers are up and running, you can setup _nsqd_.  To add _nsqd_ to any host, just add the role listed above to any other host role.  This will install and setup _nsqd_ with the default attributes to listen on localhost.

Testing
-------

    $ kitchen test

Authors
-------
- Author:: Matt Reiferson <snakes@gmail.com>
- Author:: Eric Lubow <elubow@simplereach.com>
- Author:: John Dewey <john@dewey.ws>
- Author:: Jon Nappi <moogar0880@gmail.com>
