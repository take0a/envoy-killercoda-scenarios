With the configuration based on EDS, when the services need to be scaled up a new endpoint can be added to the `eds.conf`{{}}. Envoy will then automatically include the changes.

## Task

Replace the configuration with the following to add a new endpoint to the cluster.

```json
{
  "version_info": "0",
  "resources": [
    {
      "@type": "type.googleapis.com/envoy.config.endpoint.v3.ClusterLoadAssignment",
      "cluster_name": "localservices",
      "endpoints": [
        {
          "lb_endpoints": [
            {
              "endpoint": {
                "address": {
                  "socket_address": {
                    "address": "172.30.1.2",
                    "port_value": 8009
                  }
                }
              }
            },
            {
              "endpoint": {
                "address": {
                  "socket_address": {
                    "address": "172.30.1.2",
                    "port_value": 8010
                  }
                }
              }
            }
          ]
        }
      ]
    }
  ]
}
```

Based on how Docker handles file inode tracking, sometimes the filesystem change isn't triggered and detected. Force the change with the command `cp assets/eds1.conf envoy/eds.conf`{{exec}}

Envoy should automatically reload the configuration and add the new node into the load balancing rotation `curl localhost`{{exec}}