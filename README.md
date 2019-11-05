# JanusGraph Helm Chart

This is an opinionated [Helm](https://v3.helm.sh/) chart for deploying [JanusGraph](https://janusgraph.org/) on top of [Scylla](https://www.scylladb.com/).

This chart assumes that a Scylla cluster already exists.  It will deploy all other required components of a JanusGraph - Scylla - Elasticsearch graph data system.

This functionality is in beta and will definitely change.  We have no SLAs on its features.  As mentioned, this is an opinionated take on deployment - if it meets your needs, awesome!  Otherwise, hopefully it's a good starting point for your graph adventures.

This chart is designed for use with Helm 3 (aka "Tiller-less" Helm).  We think this makes using Helm with Kubernetes even easier!

## Requirements

* [Helm](https://v3.helm.sh/) >= 3.0.0
* Kubernetes - cluster requirements will vary based on Elasticsearch and JanusGraph replication and sizing

## Dependencies

This chart uses Elastic's [Elasticsearch and Kibana charts](https://github.com/elastic/helm-charts) as dependencies, to deploy and monitor an Elasticsearch cluster.


## Installing
This chart should work out of the box with the default values, __but you'll need to update `scylla.hostname` to match your existing cluster!__

```
git clone https://github.com/EnharmonicAI/janusgraph-helm.git && cd janusgraph-helm
vim janusgraph/values.yaml
echo "I just updated my values.yaml file with my Scylla cluster hostname(s)!"
```

Then update dependency charts and install the JanusGraph chart!
```
helm dep up janusgraph
helm install janusgraph --namespace jg janusgraph
```

## Configuration

| Parameter   | Description        | Default       |
| ----------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| `replicaCount` | The number of JanusGraph replica pods to deploy | `1` |
| `image.repository` | The repository from which to pull the JanusGraph image.  Either set to the string 'DockerHub' or use a private repository, such as gcr.io/${PROJECT_ID} | `DockerHub` |
| `name` | The image name. The image version tag will always be the appVersion value specified in `Chart.yaml` | `janusgraph/janusgraph` |
| `images.pullPolicy` | The image pull policy | `Always` |
| `service.port` | The port on which to expose the JanusGraph server | `8182` |
| `imagePullSecrets` | secrets to pull the JanusGraph image from the repository | `[]` |
| `scylla.hostname` | One or more hostnames/IPs in the Scylla cluster | `` |
| `scylla.keyspace` | The keyspace that stores the graph data for this deployment. This should be a clean keyspace that will only be used for JanusGraph data | `janusgraph` |
| `search.enabled` | Boolean for whether we are utilizing Elasticsearch for this deployment | `true` |
| `search.storageClass.name` | The name of a custom storage class to use | `elasticsearch-ssd` |
| `search.storageClass.provisioner` | The [provisioner](https://kubernetes.io/docs/concepts/storage/storage-classes/#provisioner) for the storage class | `kubernetes.io/gce-pd` |
| `search.storageClass.type` | Additional type parameter for custom storage class | `pd-ssd` |
| `elasticsearch.replicas` | Kubernetes replica count for the elasticsearch statefulset (i.e. how many pods) | `3` |
| `elasticsearch.minimumMasterNodes` | The value for discovery.zen.minimum_master_nodes | `2` |
| `elasticsearch.image` | Elasticsearch image | `docker.elastic.co/elasticsearch/elasticsearch` |
| `elasticsearch.imageTag` | Elasticsearch image tag | `6.6.0` |
| `elasticsearch.imagePullPolicy` | Elasticsearch image pull policy | `IfNotPresent` |
| `elasticsearch.volumeClaimTemplate.accessModes` | Access mode for the Elasticsearch volume claim template  | `[ "ReadWriteOnce" ]` |
| `elasticsearch.volumeClaimTemplate.storageClassName` | Name of the storage class to use. Switch to Custom Storage Class as needed.   | `elasticsearch-ssd` |
| `elasticsearch.volumeClaimTemplate.resources` | Resources for the volume claim  | `{ requests: { storage: 30Gi } }` |
| `podSecurityContext` | Additional pod security context as desired | `{}` |
| `securityContext` | [Security context](https://kubernetes.io/docs/concepts/policy/pod-security-policy/) | `{}` |
| `resources` | [Request resources](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/) | `{}` |
| `affinity` | [Affinity](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity) for pod assignment | `{}` |

## Notes
This Helm Chart is created and maintained by [Enharmonic, Inc](https://enharmonic.ai).
It is released under the Apache 2.0 license.
