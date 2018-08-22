# ziggy

Ziggy is an Helm chart for deploying ziggy app. It's a special app created in order to demoing stuff on K8s:

* Health check 
* Readiness
* CPU intensive workloads (eg HPA)
* Memory leaked apps

## How it works

### Overview

There is two containers:

* spaceboy: a simple handler:
    * "/" -> return Pod hostname
    * "/healthz" -> return http 200 during 120s (by default), then http 503
    * "/ready" -> return http 503 during 30s (by deault), then http 200
* spaceoddity:
    * can simulate a CPU intensive app
    * or an app with a memroy leak (these to settings are exclisives)

### How to use it

There is a configMap with default options:

```
configmap:
  # args for spaceboy container, eg "-healthz 120", "-ready 60"
  spaceboy:
    key: spaceboy-args
    value: ["-healthz 300"]
  # args for spaceoddity container, eg "-cpu 300", "-mem"
  spaceoddity:
    key: spaceoddity-args
    value: ["-cpu 0"]
```

Just overwrite the options you need:

If you need to change readiness dealay to 60 seconds:

```
$ healm install ziggy --set configmap.spaceboy.value="-ready 60"
```

If you have to simulate a memory leak during 5 minutes:

```
$ healm install ziggy --set configmap.spaceoddity.value="-mem 300"
```

## See also

[spaceboy on Dockerhub](https://hub.docker.com/r/de13/spaceboy/)

[spaceoddity on Dockerhub](https://hub.docker.com/r/de13/spaceoddity/)