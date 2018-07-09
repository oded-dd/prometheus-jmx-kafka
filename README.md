# Kafka JMX exporter configuration

A Kafka metric configuration file for JMX exporter (https://github.com/prometheus/jmx_exporter)

The collector configurably scrapes and exposes Kafka mBeans of the JMX target. It runs a Java Agent, exposing a HTTP server and serving metrics of the local JVM.

## Running
To run as a javaagent [download the jar](https://repo1.maven.org/maven2/io/prometheus/jmx/jmx_prometheus_javaagent/0.3.1/jmx_prometheus_javaagent-0.3.1.jar) and run:

```
java -javaagent:/usr/bin/jmx_prometheus_javaagent-0.3.0.jar=7071:/etc/kafka/kafka-jmx-metric.yaml
```

For systemd use `kafka.service`:

```
[Unit]
Description=Apache Kafka server (broker)
Documentation=http://kafka.apache.org/documentation.html
Requires=network.target remote-fs.target
After=network.target remote-fs.target zookeeper.service

[Service]
Type=simple
User=root
Group=root
LimitNOFILE=300000
LimitFSIZE=infinity
Restart=on-failure
Environment=JMX_PORT=9999
Environment="KAFKA_OPTS=-javaagent:/usr/bin/jmx_prometheus_javaagent-0.3.0.jar=7071:/etc/kafka/kafka-metrics.yml"
Environment="KAFKA_JMX_OPTS=-Dcom.sun.management.jmxremote=true -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false -Djava.rmi.server.hostname=prod-kafka-001 -Djava.net.preferIPv4Stack=true"
Environment="KAFKA_HEAP_OPTS=-Xmx5120m -Xms5120m"
Environment="KAFKA_LOG4J_OPTS=-Dlog4j.configuration=file:/etc/kafka/log4j.properties"
Environment="KAFKA_JVM_PERFORMANCE_OPTS=-XX:MetaspaceSize=96m -XX:+UseG1GC -XX:MaxGCPauseMillis=20 -XX:InitiatingHeapOccupancyPercent=35 -XX:G1HeapRegionSize=16M -XX:MinMetaspaceFreeRatio=50 -XX:MaxMetaspaceFreeRatio=80"
ExecStart=/usr/share/kafka/bin/kafka-server-start.sh /etc/kafka/server.properties
ExecStop=/usr/share/kafka/bin/kafka-server-stop.sh

[Install]
WantedBy=multi-user.target
```

## Installing

A config file loaded with `jmx_exporter` should be places under `/etc/kafka/kafka-metrics.yml`

## Configuration 

<!-- BEGINNING OF PRE-COMMIT DOCS HOOK -->

| Name | Description | Type |
|------|-------------|------|
| kafka_server_under_replicated_partitions | Number of under-replicated partitions | GAUGE |
| kafka_controller_offline_partitions_count | Number of partitions that do not have an active leader and are hence not writable or readable. Alert if value is greater than 0 | GAUGE |
| kafka_controller_active_controller_count | Number of active controllers in the cluster. Alert if the aggregated sum across all brokers in the cluster is anything other than 1 (there should be exactly one controller per cluster)| GAUGE |
| kafka_server_total_bytes_in_per_sec | Aggregate incoming byte rate| UNTYPED |
| kafka_server_total_bytes_in_per_sec_per_topic | Aggregate incoming byte rate per topic | UNTYPED |
| kafka_server_total_bytes_out_per_sec | Aggregate outgoing byte rate| UNTYPED |
| kafka_server_total_bytes_out_per_sec_per_topic | Aggregate outgoing byte rate per topic| UNTYPED |
| kafka_network_requests_per_sec | Produce request rate | UNTYPED |
| kafka_server_total_produce_requests_per_sec | Produce request rate | UNTYPED |
| kafka_server_total_produce_requests_per_sec_per_topic | Produce request rate per topic | UNTYPED |
| kafka_server_total_fetch_requests_per_sec | Fetch request rate | UNTYPED |
| kafka_server_total_fetch_requests_per_sec_per_topic | Fetch request rate per topic | UNTYPED |
| kafka_server_failed_produce_requests_per_sec  | Produce request rate for requests that failed | UNTYPED |
| kafka_server_failed_produce_requests_per_sec_per_topic  | Produce request rate for requests that failed per topic | UNTYPED |
| kafka_server_failed_fetch_requests_per_sec | Fetch request rate for requests that failed | UNTYPED |
| kafka_server_failed_fetch_requests_per_sec_per_topic | Fetch request rate for requests that failed per topic | UNTYPED |
| kafka_controller_leader_election_rate_time | Leader election rate and latency (rate in seconds, latency|time in ms) | UNTYPED |
| kafka_controller_unclean_leader_elections_per_sec | Unclean leader election rate | UNTYPED |
| kafka_server_partition_count | Number of partitions on this broker (This should be mostly even across all brokers) | GAUGE |
| kafka_server_leader_count | Number of leaders on this broker (This should be mostly even across all brokers) | GAUGE |
| kafka_server_max_lag_in_replica | Maximum lag in messages between the follower and leader replicas | GAUGE |
| kafka_server_request_handler_avg_idle_precent | Average fraction of time the request handler threads are idle. Values are between 0 (all resources are used) and 1 (all resources are available) | GAUGE |
| kafka_network_network_processor_avg_idle_precent | Average fraction of time the network processor threads are idle. Values are between 0 (all resources are used) and 1 (all resources are available) | GAUGE |
| kafka_network_request_queue_size | Size of the request queue. A congested request queue will not be able to process incoming or outgoing requests | GAUGE |
| kafka_network_total_time_ms | Total time in ms to serve the specified request (Produce, FetchConsumer, FetchFollower) | COUNTER |
| kafka_network_request_queue_time_ms | Time the request waits in the request queue (Produce, FetchConsumer, FetchFollower) | COUNTER |
| kafka_network_local_time_ms | Time the request is processed at the leader (Produce, FetchConsumer, FetchFollower) | COUNTER |
| kafka_network_remote_time_ms | Time the request waits for the follower (Produce, FetchConsumer, FetchFollower) | COUNTER |
| kafka_network_response_queue_time_ms | Time the request waits in the response queue (Produce, FetchConsumer, FetchFollower) | COUNTER |
| kafka_network_response_send_time_ms | Time to send the response (Produce, FetchConsumer, FetchFollower) | COUNTER |
| kafka_server_messages_in_per_sec | kafka_server_messages_in_per_sec | COUNTER |
| kafka_server_messages_in_per_sec_per_topic | Aggregate incoming message rate per_topic | COUNTER |
| kafka_log_log_flush_rate_time | Log flush rate and time (rate in seconds, latency time in ms) | UNTYPED |
| kafka_server_isr_shrinks_per_sec | If a broker goes down, ISR for some of the partitions will shrink. When that broker is up again, ISR will be expanded once the replicas are fully caught up. Other than that, the expected value for both ISR shrink rate and expansion rate is 0 | GAUGE |
| kafka_server_isr_expands_per_sec | When a broker is brought up after a failure, it starts catching up by reading from the leader. Once it is caught up, it gets added back to the ISR | GAUGE |
| kafka_server_purgatory_size | Number of requests waiting in the producer purgatory. This should be non-zero when acks=all is used on the producer | GAUGE |
| kafka_server_zookeeper_disconnects_per_sec | Zookeeper client is currently disconnected from the ensemble. The client lost its previous connection to a server and it is currently trying to reconnect. The session is not necessarily expired | UNTYPED |
| kafka_server_zookeeper_expires_per_sec | The ZooKeeper session has expired. When a session expires, we can have leader changes and even a new controller. Alert if value of such events across a Kafka cluster and if the overall number is high | UNTYPED |
| kafka_server_zookeeper_sync_connects_per_sec | ZooKeeper client is connected to the ensemble and ready to execute operations | UNTYPED |
| kafka_server_zookeeper_auth_failure_per_sec | An attempt to connect to the ensemble failed because the client has not provided correct credentials | UNTYPED |
| kafka_server_zookeeper_readonly_connects_per_sec | The server the client is connected to is currently LOOKING, which means that it is neither FOLLOWING nor LEADING. Consequently, the client can only read the ZooKeeper state, but not make any changes (create, delete, or set the data of znodes) | UNTYPED |
| kafka_server_zookeeper_sasl_auth_per_sec | Client has successfully authenticated | UNTYPED |
| kafka_server_zookeeper_expired_per_sec | The ZooKeeper session has expired. When a session expires, we can have leader changes and even a new controller. Alert if value of such events across a Kafka cluster and if the overall number is high | UNTYPED |

<!-- END OF PRE-COMMIT DOCS HOOK -->

## TODO

Add Grafana dashboard

## Authors

Module managed by [Oded David](https://github.com/oded-dd).

## License

Apache 2 Licensed. See LICENSE for full details.
