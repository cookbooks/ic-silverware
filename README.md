# silverware chef cookbook

Cluster orchestration -- coordinates discovery, integration and decoupling of cookbooks

* Cookbook source:   [http://github.com/infochimps-cookbooks/silverware](http://github.com/infochimps-cookbooks/silverware)
* ClusterChef tools: [http://github.com/infochimps/cluster_chef](http://github.com/infochimps/cluster_chef)
* Homebase (shows cookbook in use): [http://github.com/infochimps-labs/cluster_chef-homebase](http://github.com/infochimps-labs/cluster_chef-homebase)

## Overview

Cookbooks repeatably express these and other aspects:
* "I launch these daemons: ..."
* "I haz a bukkit, itz naem '/var/log/lol'"
* "I have a dashboard at 'http://....:...'"
* ... and much more.

Wouldn't it be nice if announcing a log directory caused...
  - my log rotation system to start rotating my logs?
  - a 'disk free space' gauge to be added to the monitoring dashboard for that service?
  - flume (or whatever) began picking up my logs and archiving them to a predictable location?
  - in the case of standard apache logs, a listener to start counting the rate of requests, 200s, 404s and so forth?
Similarly, announcing ports should mean
  - the firewall and security groups configure themselves correspondingly
  - the monitor system starts regularly pinging the port for uptime and latency 
  - and pings the interfaces that it should *not* appear on to ensure the firewall is in place?

Cluster chef make those aspects standardized and predictable, and provides integration and discovery hooks. The key is to make integration *inevitable*: No more forgetting to rotate or monitor a service, or having a config change over here screw up a dependent system over there.

__________________________________________________________________________

(*below is a planning document and may not perfectly reflect reality*)

FIXME: **update for version_3 release**

Attributes are scoped by *cookbook* and then by *component*.
* If I declare `i_haz_a_service_itz('redis)`, it will look in `node[:redis]`.
* If I declare `i_haz_a_service_itz('hadoop-namenode')`, it will look in `node[:hadoop]` for cookbook-wide concerns and `node[:hadoop][:namenode]` for component-specific concerns.

* The cookbook scope is always named for its cookbook. Its attributes live in`node[:cookbook_name]`.
  - if everything in the cookbook shares a concern, it sits at cookbook level. So the hadoop log directory (shared by all its components) is at `(scratch_root)/hadoop/log`.
* If there is only one component, it can be implicitly named for its cookbook. In this case, it is omitted: the component attributes live in `node[:cookbook_name]` (which is the same as the component name).
* If there are multiple components, they will live in `node[:cookbook_name][:component_name]` (eg `[:hadoop][:namenode]` or `[:flume][:master]`. In file names, these become `(whatever)/cookbook_name/component_name/(whatever)`; in other cases they are joined as `cookbook_name-component_name`.

Allow nodes to discover the location for a given service at runtime, adapting when new services register.

### Discovery

Allow nodes to discover the location for a given service at runtime, adapting
when new services register.

#### Operations:

* register for a service. A timestamp records the last registry.
* discover all chef nodes that have registered for the given service.
* discover the most recent chef node for that service.
* get the 'public_ip' for a service -- the address that nodes in the larger
  world should use
* get the 'public_ip' for a service -- the address that nodes on the local
  subnet / private cloud should use

#### Implementation

Nodes register a service by calling `announce`, which sets a hash containing
'timestamp' (the time of registry) and other metadata passed in.

## Recipes 

* `default`                  - Base configuration for silverware

## Integration

Supports platforms: debian and ubuntu



## Attributes

* `[:silverware][:conf_dir]`            -  (default: "/etc/silverware")
* `[:silverware][:log_dir]`             -  (default: "/var/log/silverware")
* `[:silverware][:home_dir]`            -  (default: "/etc/silverware")
* `[:silverware][:user]`                -  (default: "root")
* `[:users][:root][:primary_group]`   -  (default: "root")

## License and Author

Author::                Philip (flip) Kromer - Infochimps, Inc (<coders@infochimps.com>)
Copyright::             2011, Philip (flip) Kromer - Infochimps, Inc

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

> readme generated by [cluster_chef](http://github.com/infochimps/cluster_chef)'s cookbook_munger
