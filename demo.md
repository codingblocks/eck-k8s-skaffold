# Setup

* Start Docker
* Settings > Kubernetes
* Settings > Resources
* k9s -n all

## About k9s

* Open-Source* CLI tool for interacting w/ Kubernetes
* Resource oriented like Kubernetes
  * :alias
* Supports most of the kubectl commands
  * AutoComplete
  * Easy filtering
  * Log Viewer
* Polls resources
  * Great for presentations!

## Important types of resources

* Pods
  * Smallest Unit that Kubernetes cares about
  * Hosts one or more containers
  * Can be associated with PersistentVolumes
* StatefulSets
  * Define one or more pods
  * Designed to maintain state via PersistentVolumes
* Services
  * Abstraction for DNS, Load Balancers, external resources etc
  * Associates name/IP with one or more pods

## Elastic Cloud on Kubernetes

* Set of Custom Resource Definitions that extend the kubernetes API
* Adds definitions for new types of resources: Elasticsearch, Kibana etc
* [kubectl create -f https://download.elastic.co/downloads/eck/2.3.0/crds.yaml](https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-deploy-eck.html)
* Now Kubernetes speaks our language!
  * :elasticsearch

## Install the Elastic Operator

* `kubectl apply -f https://download.elastic.co/downloads/eck/2.3.0/operator.yaml`
* Creates an operator in :namespace elastic-system
* An operator is like a person on your team that is responsible for managing your Kubernetes cluster
* It will take your Elasticsearch and Kibana resource definitions and translate it to Kubernetes resources

## Install Elasticsearch

* `kubectl apply -f k8s/3_cluster/elasticsearch.yaml `
* `kubectl apply -f k8s/3_cluster/kibana.yaml`
* :elasticsearch, :statefulset, :pods, :persistentvolume, :service

## But how do we see it?

* Clusters are closed to the outside world by default
* We need to open up a port
* Oh...https?
* Oh...authentication?
* :secrets

# Examples
- Adding nodes
- Delete nodes
 - What happens to replicas?
- Rolling upgrade
    - Downgrade not supported!
- Different types of services
  - Kibana
  - Logsearch
- Setting up beats
- Multiple clusters
- Loading data

# Where do we go from here?

* [Documentation](https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-overview.html)
* [Settings](https://www.elastic.co/guide/en/elasticsearch/reference/current/settings.html)
* Helm Templates
* Certificates
* Custom applications