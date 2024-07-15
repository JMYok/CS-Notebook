# 什么是filebeat
> Filebeat is a lightweight shipper for forwarding and centralizing log data. Installed as an agent on your servers, Filebeat monitors the log files or locations that you specify, collects log events, and forwards them either to Elasticsearch or Logstash for indexing.
  
作为代理安装在服务器上，用于转发和集中日志数据的工具。例如转发到ES中存储，便于索引
# 如何工作的？
> Here’s how Filebeat works: When you start Filebeat, it starts one or more inputs that look in the locations you’ve specified for log data. For each log that Filebeat locates, Filebeat starts a harvester. Each harvester reads a single log for new content and sends the new log data to libbeat, which aggregates the events and sends the aggregated data to the output that you’ve configured for Filebeat.

通过harvest读取日志，存储在一个集中日志的地方，然后根据配置将这些日志发送到对应的输出。
![](https://pic.imgdb.cn/item/6695202cd9c307b7e98c1405.png)

# 参考
- https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-overview.html