With the  Outlier Detection in place, Envoy will remove a host based on the responses from real requests.

## Testing

Start two nodes, both start in a healthy state.
`docker run -d katacoda/docker-http-server:healthy; docker run -d katacoda/docker-http-server:healthy;`{{execute}}

Start Envoy with the command:

```
docker run -d --name proxy2 -p 81:8080 \
    -v /root/:/etc/envoy \
    -v /root/envoy1.yaml:/etc/envoy/envoy.yaml \
    envoyproxy/envoy:v1.31-latest
```{{execute}}

In a separate terminal window, launch a loop that will send requests. This will allow you to identify the changes in status.

`while true; do curl localhost:81; sleep .5; done`{{execute}}

## Mark Node Unhealthy

To make the node unhealthy, call the endpoint `curl 172.18.0.5/unhealthy`{{execute}}

This will cause all future requests to return a 500 error message `curl 172.18.0.5 -i`{{execute}}.

During this time, after the 3rd request with code `5xx`, Envoy will eject the node. You can see in the second terminal this behavior.

## Mark Node Healthy

You can mark the node healthy again, and see how Envoy will send traffic to that node again.
`curl 172.18.0.5/healthy`{{execute}}.

