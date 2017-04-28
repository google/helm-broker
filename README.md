# Service Broker

This is an implementation of a service broker which runs in a Kubernetes
cluster and deploys Kubernetes native resources only using Helm as the reifier.

This is not an official Google product.

## Prerequisites

1) A [helm](https://github.com/k8s.io/helm) tiller running in the cluster.

2) [Glide](https://github.com/Masterminds/glide) installed and in your path.

## Building and deploying

To install the dependencies, build the binaries, run the tests, and build the Docker
images, run the following:

```
make init build test docker
```

To then push your images to a Google Cloud Project registry, run the following,
where `${PROJECT_NAME}` is the name of your GCP project:

```
PROJECT="${PROJECT_NAME}" make push
```

## Add a service class's Helm chart to the registry

```shell

curl -X POST -d @- "${REGISTRY_HOST}:8001/services" <<__EOF__
{
  "name": "nginx",
  "id": "4179E70A-4641-49D5-B395-A8ACB1419BCA",
  "description": "Helm chart for running nginx",
  "plans": [
    {
      "name": "nginx",
      "id": "696AD474-123F-474F-8FDB-C724C058CF03",
      "metadata": {
        "instanceType": "gs://helm-sb-test/nginx-0.0.1.tgz"
      }
    }
  ],
  "requires": [],
  "tags": [],
  "bindable":false
}
__EOF__

```


## Create an instance of it

```shell

curl -X PUT -d @- "${BROKER_HOST}:8000/v2/service_instances/912ae324-2fb6-41ba-bddd-6f0a7e5239fa" <<__EOF__
{
  "service_id": "4179E70A-4641-49D5-B395-A8ACB1419BCA",
  "plan_id":"696AD474-123F-474F-8FDB-C724C058CF03"
}
__EOF__

```
