# My kubernetes infrastructure for local cluster

I use kubernetes a lot. A I love it, so I've decided to
have all my infrastructure configs in one place.

This repo has minimal setup for:
* grafana
* sentry
* jaeger
* loki
* influx-db (for jmeter)
* opentelemetry-collector

All projects are interconnected, so grafana has predefined 
data sources for jaeger and loki, opentelemetry collector
sends collected information to loki and jaeger.

To create local cluster from scratch use this commands:
```bash
# Creates cluster and local registry for docker images.
k3d cluster create --config k3d-config.yml

kubectl create namespace tracing 
kubectl create namespace sentry 
kubectl create namespace grafana
kubectl apply -f sentry
kubectl apply -f tracing
kubectl apply -f grafana

# After applying this configuration create superuser for sentry.
kubectl exec -it -n sentry deployments/sentry --container sentry -- sentry createuser
```

It has hardcoded things, but I don't really care, since this configuration
is not intended to be used in production.

Also it uses deprecated sentry deployment method as described here: https://hub.docker.com/_/sentry, because it's easier to configure.

Available links after installation:
* http://grafana.localhost/
* http://sentry.localhost/
* http://jaeger.localhost/
* http://loki.localhost/

Also it has opened ports for some services:
* http://localhost:30086 - influx-db
* http://localhost:30017 - opentelemetry grpc endpoint