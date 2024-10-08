The final example uses Envoy to proxy traffic to different Python services based on the requested URL path.

## Configuration

The configuration of the application is defined as a Docker Compose file. We use a Docker Compose file because we want to run several containers simultaneously - one for the proxy and one for each of the individual services.

You can view the file by clicking `examples/front-proxy/docker-compose.yaml`{{}}.

## Application

The service is a Python web application it also uses Envoy within the container to forward traffic to the Python application. It’s not necessary to have Envoy in front of the application.

`examples/shared/python/service.py`{{}}

## Envoy Frontend Proxy

The Envoy proxy configuration is defined in: `examples/front-proxy/envoy.yaml`{{}}

As described in the first step, the configuration starts by defining a set of *static_resources*. The routes match based on the URL of the request.

```yaml
routes:
    - match:
        prefix: "/service/1"
    route:
        cluster: service1
    - match:
        prefix: "/service/2"
    route:
        cluster: service2
```

The cluster configuration forwards traffic to endpoints called _service1_ and _service2_. These are DNS entries provided by the Docker Network, configured with Docker Compose.

## Deploy

Start the example using the Docker Compose command below:

`docker-compose -f ~/envoy/examples/front-proxy/docker-compose.yaml up -d`{{execute}}

## Admin View

Once started, you can view the admin interface at [URL]({{TRAFFIC_HOST1_8001}}).

You can view the routing configuration as JSON at [URL]({{TRAFFIC_HOST1_8001}}/config_dump).

Additional information, such as the clusters available and their metrics is available at [URL]({{TRAFFIC_HOST1_8001}}/clusters).

You can discover more metrics from the server at [URL]({{TRAFFIC_HOST1_8001}}/stats). 

## Application Routing

With Envoy listening on port 8000, you can make requests. Based on the URL used, different services will respond according to the configuration.

`curl localhost:8001/service/1`{{execute}}

`curl localhost:8001/service/2`{{execute}}

You should see the metrics changing. What information is being shown and how can this be helpful in production?
