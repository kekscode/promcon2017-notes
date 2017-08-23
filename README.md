# PromCon 2017 in munich

## About prometheus monitoring

This is the second conference covering prometheus and the first one i attended to.

Prometheus is still a very young project which has been open sourced in 2012 by SoundCloud. It is based
on the works at Google and what they call "borgmon".

Prometheus focuses on reliability and simplicity. A basic deployment consists of only one self-contained binary
and a config file. It combines some interesting features which makes it especially attractive for scalable monitoring
of lots of (micro)services in a very dynamic environment:

* Multi-dimensional data (e. g. "no tree-based data model") which consists of a metric name, a value and
multiple key/value pairs for sorting, grouping, filterings, etc. Think of "tags" for a metric's datapoint.
It looks like: `http_requests_total{cache="upload",site="mywebserver"}.`
* A flexible query language ([PromQL][9]) which is specifically designed to support the prometheus data model. For example
this concise query: `topk(3, sum(http_requests_total{status~="^5"}) by (cache))` would return the top 3 caches with HTTP 5xx errors by regex matching the cache status.
* Pull and push for data ingestions (though pull aka "scraping" is the prefered method by design)
* Supports white- and blackbox monitoring
* Static and dynamic target discovery
* Exceptional good graphing and dashboard support
* Can be deployed on one or multiple nodes (for availability)
* Growing ecosystem of complementary software following the same design principles

Some more PromQL examples by prometheus core contributor Julius Volz:

* [Tutorial Part 1][jvtutor1]
* [Tutorial Part 2][jvtutor2]

### Links

* [Project website][5]
* [Github project][6]
* [@PrometheusIO][3] on Twitter
* Tweets from conference attendees: [#promcon2017][4]

### Overview

An overview about its history, features and architecture can be
found on the first page [of the prometheus documentation][8].

## About PromCon

From the [PromCon website][1]:

*PromCon 2017 will be the second conference around the Prometheus monitoring system.
It will take place from August 17 - 18 at Google Munich and will be a single-track
event with space for 200 attendants. All talks will be recorded and published after
the event to create maximum exposure.*

*PromCon aims to connect Prometheus users and developers from around the world in
order to exchange knowledge, best practices, and experience gained around using Prometheus.
We also want to collaborate to build a community and grow professional connections around
systems and service monitoring.*

## Schedule

The schedule can be found [here][2] and all abstracts from the various talks will be linked in the notes.

## The conference in general

The conference was held at Google in munich and round about 200 attendees from all sorts of IT-focused companies were at the venue. Naturally in 2017's world of microservices swimming in vast amounts of data, most of the attendees work for companies with large software stacks consisting of lots of network services in a heterogenous environment.

[Matt Bostock][mattb] from [Cloudflare showed their prometheus deployment][cloudflare] showed some impressive numbers. They monitor their global network with 185 local prometheus servers and 4 top level federators.

[Sneha Inguva][sneha] from digitalocean monitors 15 kubernetes clusteres in 12 data centers with 300+ production applications in running. They use 2 promethei and 1 alertmanager per cluster and create 1.5 million different timeseries and 99218 samples per second (550k per second on the line). Digitalocean monitors for cluster hardware usage, network and state (actual state vs. expected). They *also* use the prometheus metrics for capacity planning and autoscaling of services, loadbalancers and worker nodes, too.

On the small scale side of things, [Alexander Schwartz][ahus] showed us how he does application load testing by pushing metrics from JMeter and Gatling load testing tools into prometheus to leverage its qualities as a base for insights and nice dashboards.

Carl Bergquist from Grafanalabs showed the ongoing support from the Grafana dashboard project which integrates prometheus out-of-the-box as a datasource. The new integration features [typeahead, search suggestions and more][graflabs].

These are some examples i picked randomly to show that prometheus proves as a swiss army knife for all things around gathering metrics and making use of the data. The general idea is always to continuously gather *lots* of metrics and model your monitoring use-cases on top of this data.

The following are my quick-and-dirty notes while attending the talks.

## Welcome: 1 year passed...

...and the community is growing:

* 360 -> 650 contributors
* 50 -> 200+ third party integrations

Some famous users: CERN, NYT, DB, google, redhat, digitalocean,...

## Talks

**https://promcon.io/2017-munich/talks/monitoring-cloudflares-planet-scale-edge-network-with-prometheus**

* @mattbostock
* cdn
* 185 prometheus servers
* 4 top level servers (federating)
* 15 days data retention
* more interesting data on the slides
* using mtail for logging to metrics
* every prom node monitored all other nodes
* interesting tools mentioned
* lesson learned: standardise metric labels early
* alertmanager quite unreliable at their site (older versions, newer versions improved)

**https://promcon.io/2017-munich/talks/start-your-engines-white-box-monitoring-for-load-tests**

* Prometheus as TSDB for load testing
* use the same metrics in test and prod
* jmeter, gatling, 
* USE and RED method
  * utilization, saturation, errors for cpu, disk, ...
  * i. e. for endpoints: request rate, error rate, duration (distribution)

**https://promcon.io/2017-munich/talks/best-practices-and-beastly-pitfalls/**

* Talks about good and bad practices
* Lots of examples and explanations
* Some details and rules of thumbs
* Important: Metrics and their labels should not overlap
* Every unique set of labels creates a new series
  * dont i. e. labels never should contain ids or io addresses or whatever can "explode" in terms of the sum of time series generated
* labels should be quite static
* sometimes it is a good idea to add another metric with a label which can be JOINed at query time
* Rob Ewaschuk "My Philosophy on Alerting"
* dont alert on timeseries only, ALSO alert always if stuff is up or down in general!!
* rate() von counter werten kommen mit counter resets klar. sums nicht. 

**https://promcon.io/2017-munich/talks/prometheus-as-a-internal-service**

* promgen web ui to generate and maintain prometheus configurations
* Does also query shards for federation (proxy function)
* Should be checked out

**https://promcon.io/2017-munich/talks/grafana-and-prometheus**

* Grafana + Prometheus
* Update-Tool zu neuen Features rund um Grafana
* Grafana PromQL has now syntax highlighting, function docs etc. - sau cool
* checkout diagram plugin
* Development is rapid
* 92000 Grafana installs have 16000 prometheus datasources enabled

**https://promcon.io/2017-munich/talks/why-we-love-prometheus-even-though-i-hate-it**

* vpn provider
* trying to like prometheus but hit every problem they could
* nice talk to watch if you think all things are great in prom world
* https://www.anchorfree.com
* Several thousand nodes
* 200 countries

**https://promcon.io/2017-munich/talks/analyze-prometheus-metrics-like-a-data-scientist**

* pushing the boundaries from the perspective of datascience
* some examples like prediction of latency in http requests

**https://promcon.io/2017-munich/talks/alertmanager-and-high-availability**

* coreos origin
* crreating ha alertmanager 
* they use gossip (like consul, cassandra) for HA between alertmanagers
* internally they use mesh from weaveworks
* ha is now directly in alertmanager 

**https://promcon.io/2017-munich/talks/play-with-prometheus**

* dev driven (no testing, canary deployment)
* all cloud
* all containers and aws

**https://promcon.io/2017-munich/talks/the-uninstrumentable-getting-apache-spark-and-prometheus-to-play-nicely**

* FUNNY talk. two peole + british humor
* Apache Spark + PySpark
* multiple short lived workers push to the push gateway
* quite big project

**https://promcon.io/2017-munich/talks/social-aspects-of-change**

* social challenges in big environments for monitoring
* all about change
* Remove toil (mühevolle arbeit)

**https://promcon.io/2017-munich/talks/cortex-prometheus-as-a-service-one-year-on**

missed that one.

**https://promcon.io/2017-munich/talks/prometheus-everything-observing-kubernetes-in-the-cloud**

missed that one, too.

**https://promcon.io/2017-munich/talks/integrating-prometheus-and-influxdb**

* Influx-DB has fields
* generic query language
  * Sql sortof; will be replaced by IFQL in the future
  * a [directed A graph (DAG)](https://en.m.wikipedia.org/wiki/Directed_acyclic_graph) will be used to support multiple query languages by the engine
* "Process" kapacitor product
* More general use case compared to the TSDB prom already includes
* Influx and Prometheus share code and (want) to work together

**https://promcon.io/2017-munich/talks/a-worked-example-of-monitoring-a-queue-based-application**

* to be honest: not so intersting talk about just using prometheus
* OTOH: Good real life examples for queue monitoring code, promql queries and grafana dashboards
* Good examples for usage at QuoScient

**https://promcon.io/2017-munich/talks/storing-16-bytes-at-scale**

* prometheus: 8 byte timestamp + 8 byte data per TSDB entry
* Compression! Unix timestamps can be written short by using offsets and even these can be compressed
* In the end: 16 bytes per sample => 1.37 bytes per sample on Prometheus 2 by implementing all compression
* 7TB => ~0.8TB
* Churn is a problem in timeseries. This will be solved and the slides explain how in a graphical way. Also there is a blog post from [Fabian](https://twitter.com/fabxc)
* Lots of more improvements in terms of hardware usage and performance + efficiency

Great blogpost: https://fabxc.org/blog/2017-04-10-writing-a-tsdb/

**https://promcon.io/2017-munich/talks/improving-user-and-developer-experience-of-the-alertmanager-ui**

* Old alert manager ui could be improved or rewritten
  * Grouping, Filtering, Create/Preview alerts
* New UI uses elm. It is s functional language which compiles to javascript. Reasons: Maintainable and usable.  
* statically typed, pure functions, great compiler, no runtime exceptions, no front end regressions, etc.
* routing tree is missing (will be back soon)
* Talks about how to work with elm
* 183 commits => merge replaces old ui with new ui

**https://promcon.io/2017-munich/talks/using-tsdb-as-a-library**

* FUNNY talk. Nice guy. 
* PromFlux - prometheus with PUSH
* Simple
* Showed go code which uses prom tsdb as a library for pushing ts data

**https://promcon.io/2017-munich/talks/staleness-in-prometheus-2-0**

* Staleness is an old problem since 2014 which leads into some - at least - inconvenient behaviour(s).
  * Brian gives some examples in his slides
* A lot of technical insight on the changes

**https://promcon.io/2017-munich/talks/lightning-talks-day2**

* Many nice little pet projects and use cases

## Video Recordings

* PromCon 2017 (not yet released)
* [PromCon 2016][7]

[1]: https://promcon.io/2017-munich/
[2]: https://promcon.io/2017-munich/schedule/
[3]: https://twitter.com/PrometheusIO
[4]: https://twitter.com/search?q=%23PromCon2017
[5]: https://prometheus.io/
[6]: https://github.com/prometheus
[7]: https://www.youtube.com/watch?v=-JkxB0CiMjU&list=PLoz-W_CUquUlCq-Q0hy53TolAhaED9vmU
[8]: https://prometheus.io/docs/introduction/overview/
[9]: https://prometheus.io/docs/querying/basics/
[graflabs]: https://grafana.com/blog/2017/08/18/timeshiftgrafanabuzz-1w-issue-9/
[sneha]: https://twitter.com/snehainguva/status/899647928746205184
[cloudflare]: https://drive.google.com/file/d/0BzRE_fwreoDQcTF2WUtHRXp0ZzA/view
[mattb]: https://twitter.com/mattbostock
[ahus]: https://twitter.com/ahus1de/status/898093290553303042
[jvtutor1]: https://www.digitalocean.com/community/tutorials/how-to-query-prometheus-on-ubuntu-14-04-part-1
[jvtutor2]: https://www.digitalocean.com/community/tutorials/how-to-query-prometheus-on-ubuntu-14-04-part-2
