With EDS in place, it's possible to move to scale up the upstream clusters. If we wanted to be able to dynamically add new domains and clusters, the Cluster Discovery Service (CDS) API needs to be implemented. In the following steps, we are configuring the Cluster Discovery Service (CDS) and The Listener Discovery Service (LDS).

You need to create a file to put the configuration for the clusters: `cds.conf`{{}}.

```json
{
  "version_info": "0",
  "resources": [
    {
      "@type": "type.googleapis.com/envoy.config.cluster.v3.Cluster",
      "name": "targetCluster",
      "connect_timeout": "0.25s",
      "lb_policy": "ROUND_ROBIN",
      "type": "EDS",
      "eds_cluster_config": {
        "service_name": "localservices",
        "eds_config": {
          "path": "/etc/envoy/eds.conf"
        }
      }
    }
  ]
}
```

And also, you need to create a file to put the configuration for the listeners: `lds.conf`{{}}.

```json
{
  "version_info": "0",
  "resources": [
    {
      "@type": "type.googleapis.com/envoy.config.listener.v3.Listener",
      "name": "listener_0",
      "address": {
        "socket_address": {
          "address": "0.0.0.0",
          "port_value": 10000
        }
      },
      "filter_chains": [
        {
          "filters": [
            {
              "name": "envoy.filters.network.http_connection_manager",
              "typed_config": {
                "@type": "type.googleapis.com/envoy.extensions.filters.network.http_connection_manager.v3.HttpConnectionManager",
                "stat_prefix": "ingress_http",
                "codec_type": "AUTO",
                "route_config": {
                  "name": "local_route",
                  "virtual_hosts": [
                    {
                      "name": "local_service",
                      "domains": [
                        "*"
                      ],
                      "routes": [
                        {
                          "match": {
                            "prefix": "/"
                          },
                          "route": {
                            "cluster": "targetCluster"
                          }
                        }
                      ]
                    }
                  ]
                },
                "http_filters": [
                  {
                    "name": "envoy.filters.http.router",
                    "typed_config": {
                      "@type": "type.googleapis.com/envoy.extensions.filters.http.router.v3.Router"
                    }
                  }
                ]
              }
            }
          ]
        }
      ]
    }
  ]
}
```

The content of files `cds.conf`{{}} and `lds.conf`{{}}  is a JSON definition of with the same information defined within our static configuration.

With the externalized the configuration of clusters and listeners, you need to modify your Envoy's configuration to make reference to these files. This can be accomplish changing all the `static_resources`{{}} for `dynamic_resources`{{}}.

Open the Envoy configuration file `envoy1.yaml`, and add the following configuration:

```yaml
dynamic_resources:
  cds_config:
    path: "/etc/envoy/cds.conf"
  lds_config:
    path: "/etc/envoy/lds.conf"
```{{copy}}

After that, launch the container with the following command:

```
docker run --name=proxy-eds-cds-lds-filebased -d \
    -p 9902:9901 \
    -p 81:10000 \
    -v /root/envoy/:/etc/envoy \
    -v /root/envoy/envoy1.yaml:/etc/envoy/envoy.yaml \
    envoyproxy/envoy:v1.31-latest
```{{execute}}

**Note:** to avoid port conflicts, we exposed the ports with offset 1.

Execute the following command:
`curl localhost:81`{{exec}}

