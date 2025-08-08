# Setup MetalLB

## Prometheus integration

To have MetalLB integrate with Prometheus, that needs to be setup first.

The kube-prometheus-stack bundles the Prometheus Operator, monitors/rules, Grafana dashboards, and AlertManager needed to monitor a Kubernetes cluster. But there are customizations necessary to tailor the Helm installation for K3s.

https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack

Now we need to configure some things for our own setup. I am using a single node in this development cluster which makes things easier for me. Make sure to adjust to your own situation.

First, I want to collect metrics for the whole cluster so I create a k3s config file and add the following. Note: this is k3s specific:
```bash
$ echo "etcd-expose-metrics: true" | sudo tee /etc/config.yaml > /dev/null
```

And restart the k3s service when ready.

## Install MetalLB

To install MetalLB from Helm, you simply need to run the following command helm install ... with:

metallb: the name to give to the deployment.
bitnami/metallb: the name of the chart in the bitnami repo. (More on adding the bitnami repo later.)
namespace kube-system: the namespace in which we want to deploy MetalLB.

It is best practice to use a custom values file for any configuration. This way, it is easy to update the deployment.
```yaml
// filename: metallb-values.yaml
configInline:
  address-pools:
   - name: default
     protocol: layer2
     addresses:
     - 192.168.0.240-192.168.240.250
```

Or, you could do the same via Command-line, by passing in the arguments via `--set` followed by the YAML object to want to set.
For the above example:
```bash
  --set configInline.address-pools[0].name=default \
  --set configInline.address-pools[0].protocol=layer2 \
  --set configInline.address-pools[0].addresses[0]=192.168.0.240-192.168.0.250
```

In both cases I configures MetalLB in Layer 2 mode (see documentation for more details). The IPs range 192.168.0.240-192.168.0.250 is used to constitute a pool of virtual IP addresses.

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install metallb bitnami/metallb -f metallb-values.yaml --namespace kube-system 
```
After a few seconds, you should observe the MetalLB components deployed under kube-system namespace.

```
$ kubectl get pods -n kube-system -l app.kubernetes.io/name=metallb -o wide

NAMESPACE     NAME                                      READY   STATUS    RESTARTS   AGE     IP             NODE           NOMINATED NODE   READINESS GATES
kube-system   metallb-speaker-s7cvp                     1/1     Running   0          2m47s   192.168.0.22   kube-master    <none>           <none>
kube-system   metallb-speaker-jx64v                     1/1     Running   0          2m47s   192.168.0.23   kube-worker1   <none>           <none>
kube-system   metallb-controller-6fb88ff94b-4g256       1/1     Running   0          2m47s   10.42.1.7      kube-worker1   <none>           <none>
kube-system   metallb-speaker-k5kbh                     1/1     Running   0          2m47s   192.168.0.24   kube-worker2   <none>           <none>
```

All done. Now every time a new Kubenertes service of type LoadBalancer is deployed, MetalLB will assign an IP from the pool to access the application.


