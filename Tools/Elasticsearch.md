Elasticsearch
========
Abstract
---------
Elasticsearch is a distributed, RESTful search and analytics engine, and makes up the heart of the SIEM used in SEC455. It is the main storage and search component of the Elastic Stack.

Where to Acquire
---------
Elasticsearch can be downloaded from https://www.elastic.co/products/elasticsearch. It is open source but also has a commercial support offering.

Documentation
---------
The documentation for the latest version of Elasticsearch can be found at https://www.elastic.co/guide/en/elasticsearch/reference/master/index.html.

Troubleshooting
---------
If you have a node that either won't start up, or is failing after a period of time, the reason should be present in the log files. Depending on the point at which it failed, there are a couple options for checking the reason.

1. The systemd journal.
2. The cluster logs in /var/log/elasticsearch/
3. The trace logs, which must be enabled.

To tail the elasticsearch service logs written to the systemd journal, use the command below. It can be helpful to leave this command running in a terminal window while trying to diagnose the problem.

```bash
sudo journalctl -u elasticsearch.service -f
```

To view the entire systemd journal for all services, which may help in case the issue is being caused with another service, this command can be used.

```bash
sudo journalctl -xe
```

Often times, if the node is not able to start, the reasons will not be present in the systemd journal. In this case you should check the main elasticsearch log files, written by default to /var/log/elasticsearch/[clustername].log. This folder contains multiple logs files: the normal cluster log (sec455.log), the deprecation log (sec455_deprecation.log), and the slowlog (sec455_index_search_slowlog.log). FOr troublshooting, you are interested in the log that matches the cluster name, in the case of the VM, sec455.log.

```bash
root@sec455:/# ls /var/log/elasticsearch/
sec455-2018-01-15-1.log.gz  sec455-2018-01-16-1.log.gz  sec455_deprecation.log  sec455_index_indexing_slowlog.log  sec455_index_search_slowlog.log  sec455.log
```

In the case of the class VM, the following command will show the elasticsearch log file, as well as use tail to continuously monitor it. Be aware that by default, **you will need to use root access** to read the elasticsearch log files. It can be helpful to leave this command running in a terminal window while issuing the "sudo service elasticsearch restart" command to find what errors are generated when the node is started. Be aware that logs can be very verbose so you may want to filter for lines that contain WARN or FATAL and ignore INFO line.

```bash
sudo cat /var/log/elasticsearch/sec455.log
sudo tail -f /var/log/elasticsearch/sec455.log
```

Logs from previous days are gzipped to save space. These can still be viewed without uncompressing them by using the "zcat" command such as in the example below. It may be helpful to filter out the lines that contain INFO using the output pipped to a grep command eliminating lines that contain the "INFO" tag, however be aware that since the log entries can span multiple lines, this may not perfectly filter it out.

```bash
sudo zcat /var/log/elasticsearch/sec455-2018-01-15-1.log.gz
sudo zcat /var/log/elasticsearch/sec455-2018-01-15-1.log.gz | grep -v INFO
```

If you need to go even deeper on debugging, you can turn up log4j2 settings to "trace" level by inserting the following line into the elasticsearch.yml file and restarting the elasticsearch process. Be sure to turn this back off after it is no longer needed, since it will cause log files to be extremely verbose.

```bash
logger.org.elasticsearch.transport: trace
```