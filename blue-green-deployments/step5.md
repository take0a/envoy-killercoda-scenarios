## Restart Proxy

As we have defined this example using the static API, the proxy needs to be restarted to detect the changes.

`docker rm -f proxy1; docker run -d --name proxy1 -p 80:8080 -v /root/:/etc/envoy envoyproxy/envoy:v1.31-latest`{{execute}}

## Testing

To test the weighted changes, make a series of requests to the newly defined service. You should see two different responses, with most coming from v2 and a smaller number of requests from v3.

`for i in {1..10}; do curl -s http://localhost/service/3; done`{{execute}}

By utilising the Metrics and Tracing capabilities in Envoy it's possible to verify that all users are having a positive experience. You can learn more about how to do this in the [Metrics and Tracing Interactive Scenario](https://killercoda.com/envoyproxy-scenarios/scenario/implementing-metrics-tracing).

## Enable Dynamic Configuration

As discussed in the scenario on [File Based Dynamic Routing Configuration](https://killercoda.com/envoyproxy-scenarios/scenario/file-based-dynamic-routing-configuration) and [API Based Dynamic Endpoint Discovery Configuration](https://killercoda.com/envoyproxy-scenarios/scenario/api-based-dynamic-routing-configuration), Envoy has a number of API extensions to allow for dynamic configuration. This would allow for traffic to be shifted. By implementing the route discovery service (RDS) API, the changes could be changed and automatically detected and picked up by Envoy. This would remove the need for restarting the Docker Container.
