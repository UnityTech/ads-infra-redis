## Redis+Kontrol pod

### Overview

This project is a [**Docker**](https://www.docker.com) image packaging
[**Redis 3.0.7**](http://www.redis.io/) together with
[**Kontrol**](https://github.com/UnityTech/ads-infra-kontrol). It is meant
to be included in a [**Kubernetes**](https://github.com/GoogleCloudPlatform/kubernetes)
pod.

This container can be included in a pod to provide a local cache and mounted against
a persistent volume as well.

### Lifecycle

Redis will be started in stand-alone mode. The initial state will render the various
configuration files including the [**telegraf**](https://github.com/influxdata/telegraf)
one.

*Additional work down the road will enable periodic snapshots to S3 and the formation
of a redis cluster across multiple containers.*

### Configuring redis

A default *proxy,cfg* configuration file is provided with the image. This can however be
overriden via the *redis.unity3d.com/config* annotation. This payload is then used
vertabim (e.g make sure it is complete and valid). For instance:

```
annotations:
  redis.unity3d.com/config: |
      daemonize no
      port 6379
      dir /data
```

Please note redis **must** be started in foreground mode (e.g *daemonize* disabled). The
data directory should be set to */data*. If not, make sure the directory exists as redis
won't create it and fail starting if it does not. The port can be set to whatever (but
update the YAML manifest in that case).

### Building the image

Pick a distro and build from the top-level directory. For instance:

```
$ docker build -f alpine-3.5/Dockerfile .
```

### Manifest

Simply use this pod in a deployment and assign it to an array with external access using a
node-port service to clamp onto the desired port. For instance:

```
apiVersion: v1
kind: Pod
metadata:
  name: redis
  labels:
    app: redis
    role: store
    tier: cache
  annotations:
    kontrol.unity3d.com/opentsdb: kairosdb.us-east-1.applifier.info
    redis.unity3d.com/config: |
      daemonize no
      port 6379
      dir /data
spec:
  containers:
    - image: registry2.applifier.info:5005/ads-infra-redis-alpine-3.5
      name: cache
      imagePullPolicy: Always
      ports:
      - containerPort: 6379
        protocol: TCP
```

### Support

Contact olivierp@unity3d.com for more information about this project.