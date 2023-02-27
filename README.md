# ECK components
## Install & configure



Sample instructions to provision Filebeat, ElasticSearch and Kibana resources (as centralized logging solution for pre-installed sample app in sample namespace) on Kubernetes. 


[![besk](https://phoenixnap.com/kb/wp-content/uploads/2021/04/use-beats-to-import-data-directly-into-elasticsearch.png)](https://phoenixnap.com/kb/elk-stack)*reference:[phoenixnap.com](https://phoenixnap.com/kb/elk-stack)*



To install and configure elastic system we use [elastic](https://helm.elastic.co/) helm repository.  The code is available in GitHub [elastic/cloud-on-k8s](https://github.com/elastic/cloud-on-k8s) repository.


To get started, use following commands to download the packages & inspect the Helm charts:

```
helm repo add elastic https://helm.elastic.co
helm repo update
helm search repo elastic
helm pull elastic/eck-operator
helm pull elastic/eck-elasticsearch
helm pull elastic/eck-kibana
helm pull elastic/eck-beats
```


[Elastic Cloud on Kubernetes (ECK)](https://www.elastic.co/guide/en/cloud-on-k8s/current/index.html) simplifies the operational tasks (like deployment, management, orchestration) of Elastic stack components (such as Beats, Elasticsearch, Kibana) on Kubernetes, using [Operator pattern](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/).


The stack componentes will be delpoyed in `elastic-system` namespace.

```
kubectl create namespace elastic-system
```

Apply Kubernetes rbac authorization objects and install Helm charts.

```
helm upgrade --install eck-operator eck-operator-2.6.1.tgz --values eck-operator-values.yaml --namespace elastic-system

kubectl apply -f eck-elasticsearch-rbac.yaml -n elastic-system
helm upgrade --install eck-elasticsearch eck-elasticsearch-0.2.0.tgz --values eck-elasticsearch-values.yaml --namespace elastic-system

helm upgrade --install eck-kibana eck-kibana-0.2.0.tgz --values eck-kibana-values.yaml --namespace elastic-system

kubectl apply -f eck-beats-rbac.yaml -n elastic-system
helm upgrade --install eck-beats eck-beats-0.1.0.tgz --values eck-beats-values.yaml --namespace elastic-system
```

Access Kibana using `kubectl port-forward`.

```
kubectl port-forward service/eck-kibana-kb-http 5601 -n elastic-system

https://localhost:5601/
username: elastic
password is located in eck-elasticsearch-es-elastic-user Kubernetes secret.
kubectl get secret eck-elasticsearch-es-elastic-user -o=jsonpath='{.data.elastic}' -n elastic-system | base64 --decode; echo
```