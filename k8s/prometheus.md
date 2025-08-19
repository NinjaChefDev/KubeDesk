# Prometheus and Grafana

On my homelab cluster I would like to have some monitoring. For this I use Prometheus and Grafana.

## Installation

My preference when installing is to use the Helm Chart. This makes updating a whole lot easier. 
The settings used are tweaked for my small homelab:
- StorageClass: k3s uses local-path as the default.
- Resources: my cluster is not very powerful (yet) so resources are tweaked to not overload the cluster.
- Helm repo: I am using the Prometheus Community Helm chart and not the Bitnami. I like the out-of-the-box dashboards and (I read somewhere) the repo is compatible with the full Prometheus Operator, making an upgrade path easier.
- I have MetalLB setup, so I can have Grafana setup with a Loadbalancer IP. MetalLB will assign an IP address from the pool, I can then assign a local DNS name to it. You can also use the k3s default Traefik for this setup. In that case, use the following for the Grafana settings in your `values.yaml`:

```yaml
grafana:
  enabled: true

  service:
    type: ClusterIP

  ingress:
    enabled: true
    ingressClassName: traefik
    annotations:
      traefik.ingress.kubernetes.io/router.entrypoints: web
    hosts:
      - grafana.homelab.local # our your own DNS name
    # no tls block, HTTP only
```

So here we go:
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

helm upgrade --install kube-prom-stack prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --create-namespace \
  -f prometheus-values.yaml
```

You can find my [prometheus-values.yaml](./prometheus-values.yaml) in this folder.

Output should look like this:
```bash
Release "kube-prom-stack" does not exist. Installing it now.
NAME: kube-prom-stack
LAST DEPLOYED: Tue Aug 19 21:58:13 2025
NAMESPACE: monitoring
STATUS: deployed
REVISION: 1
NOTES:
kube-prometheus-stack has been installed. Check its status by running:
  kubectl --namespace monitoring get pods -l "release=kube-prom-stack"

Get Grafana 'admin' user password by running:

  kubectl --namespace monitoring get secrets kube-prom-stack-grafana -o jsonpath="{.data.admin-password}" | base64 -d ; echo

Access Grafana local instance:

  export POD_NAME=$(kubectl --namespace monitoring get pod -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=kube-prom-stack" -oname)
  kubectl --namespace monitoring port-forward $POD_NAME 3000

Visit https://github.com/prometheus-operator/kube-prometheus for instructions on how to create & configure Alertmanager and Prometheus instances using the Operator.
```

Verify the pods and the storage (pvc's) in the monitoring namespace are up:
```bash
kubectl get pods,pvc -n monitoring
```

Fetch the configured IP address for the Grafana service:
```bash
kubectl get svc -n monitoring kube-prom-stack-grafana
```
and now setup your external DNS to point to this IP, and connect to your fresh Prometheus and Grafana Stack :rocket:
