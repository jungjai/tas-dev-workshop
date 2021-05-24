# Logging and metric system on TAS.

## Loggregator Architecture
[Architecture](https://docs.pivotal.io/application-service/2-10/loggregator/architecture.html)

### Tailing App Logs
Cloud Foundry allows developers and operators to stream logs or view recent logs.
- Use cf logs --help to determine how to view recent logs for your app
- Use cf logs command to stream logs for your app
- Access your app multiple times.
Note: You can exit streaming with CTRL-C or you can continue to stream and start a new terminal window.

```
$ cf logs spring-music
Connected, tailing logs for app spring-music in org example / space development as admin@example.com...

2016-06-14T15:16:12.70-0700 [RTR/4]      OUT www.example.com - [14/06/2016:22:16:12.582 +0000] "GET / HTTP/1.1" 200 0 103455 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36" 192.0.2.206:27743 x_forwarded_for:"203.0.113.222" x_forwarded_proto:"http" vcap_request_id:bd3e6ed1-5dd0-43ab-70ed-5d232b577b09 response_time:0.12050583 app_id:79bb58ab-3737-43be-ac70-39a2843b5177
2016-06-14T15:16:20.06-0700 [RTR/4]      OUT www.example.com - [14/06/2016:22:16:20.034 +0000] "GET /test/ HTTP/1.1" 200 0 6879 "http://www.example.com/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36" 192.0.2.206:2228 x_forwarded_for:"203.0.113.222" x_forwarded_proto:"http" vcap_request_id:a31f0b1d-3827-4b8f-57e3-6f42d189f025 response_time:0.033311281 app_id:79bb58aa-3747-43be-ac70-39a3843b5178
2016-06-14T15:16:22.44-0700 [RTR/4]      OUT www.example.com - [14/06/2016:22:17:22.415 +0000] "GET /test5/ HTTP/1.1" 200 0 5461 "http://www.example.com/test5" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36" 192.0.2.206:2228 x_forwarded_for:"203.0.113.322" x_forwarded_proto:"http" vcap_request_id:5d6855a2-4a79-4432-7927-de8215f5a2c7 response_time:0.029211609 app_id:79bb58aa-3737-43bb-ac70-39a2943b5178
```

### Filtering Logs
To view some subset of log output, run cf logs APP-NAME in conjunction with filtering commands of your choice. Replace APP-NAME with the name of your app. In the example below, grep -v RTR excludes all Gorouter logs
```
$ cf logs spring-music --recent | grep -v RTR
2016-06-14T14:10:05.36-0700 [API/0]      OUT Updated app with guid cdabc604-0b73-47e1-a7d5-24af2c63f723 ({"name"=>"spring-music", "instances"=>1, "memory"=>512, "environment_json"=>"PRIVATE DATA HIDDEN"})
2016-06-14T14:10:14.52-0700 [APP/0]      OUT - Gracefully stopping, waiting for requests to finish
2016-06-14T14:10:14.52-0700 [CELL/0]     OUT Exit status 0
2016-06-14T14:10:14.54-0700 [APP/0]      OUT === puma shutdown: 2016-06-14 21:10:14 +0000 ===
2016-06-14T14:10:14.54-0700 [APP/0]      OUT - Goodbye!
2016-06-14T14:10:14.56-0700 [CELL/0]     OUT Creating container
    ...
```

### Log Ordering

Diego uses a nanosecond-based timestamp that can be ingested properly by Splunk. For more information, see TIME_FORMAT and subseconds in the Splunk documentation

Java app developers may want to convert stack traces into a single log entity. To simplify log ordering for Java apps, use the multi-line Java message workaround to convert your multi-line stack traces into a single log entity. 

- you can force your app to reformat stack trace messages, replacing newline characters with a token. 
TODO: sample app for multi-line log using sl4j 

- Set your log parsing code to replace that token with newline characters again to display the logs properly in Kibana
For more information, see [Multi-line Java message workaround](https://github.com/cloudfoundry/loggregator-release/blob/develop/docs/java-multi-line-work-around.md) in the loggregator-release repository on GitHub.


## App Metric  & Metric Store
[Architecture](https://docs.pivotal.io/app-metrics/2-0/architecture.html)


### Tailing App Metrics
```
cf tail APP-NAME
```

### Querying via Prometheus-Compatible HTTP Endpoints (Metric Store API)

As a PAS developer, you can query metrics for applications you have access to. When querying with PromQL, you must specify the source_id label with the application guid as the label value. [official docs]( https://docs.pivotal.io/metric-store/1-5/using.html#using)

```
curl -H "Authorization: $(cf oauth-token)" -G "https://metric-store.SYSTEM-DOMAIN/api/v1/query" \
  --data-urlencode "query=cpu{source_id=\"$(cf app --guid your-app-name)\"}"
```
> metric: cpu, memory, http_total ...


```
$ curl -H "Authorization: $(cf oauth-token)" -G "https://metric-store.SYSTEM-DOMAIN/api/v1/query_range" \
    --data-urlencode 'query=http_latency{source_id="source_id_0"}' \
    --data-urlencode "start=$(date -d '24 hours ago' +%s)" \
    --data-urlencode "end=$(date +%s)" \
    --data-urlencode 'step=1h'
```
> 
- query is a Prometheus expression query string.
- start is a UNIX timestamp in seconds or RFC3339. (e.g. date -d '24 hours ago' +%s). Start time is inclusive. [start..end)
- end is a UNIX timestamp in seconds or RFC3339. (e.g. date +%s). End time is exclusive. [start..end)
- step is a query resolution step width in duration format or float number of seconds

*The End of this lab*

---

# Reference
- https://docs.cloudfoundry.org/devguide/deploy-apps/streaming-logs.html#overview

- App metric UI [official doc](https://docs.pivotal.io/app-metrics/2-0/architecture.html)
