An initial outline of the Envoy configuration required is available at `envoy.yaml`

The first changes required is to add a [Node](https://www.envoyproxy.io/docs/envoy/latest/api-v3/config/core/v3/base.proto#config-core-v3-node). This allows the Envoy node to be identified, potentially allowing for unique configurations to be applied. 

## Task

Prepend the following snippet to the top of the `envoy.yaml`{{}} file.

```yaml
node:
  id: id_1
  cluster: test
```{{copy}}

The API also has support for additional metadata, such as [locality](https://www.envoyproxy.io/docs/envoy/latest/api-v3/config/core/v3/base.proto.html#config-core-v3-locality) for providing region and zone-based information.