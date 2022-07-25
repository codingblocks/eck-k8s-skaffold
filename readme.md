# About Me

I'm Joe Zack, software engineer who works with streaming analytics using a lot of Kafka, Elasticsearch, and Kubernetes. I have a podcast with a couple friends where we talk about all things Software Development, if you like this presentation, then you'll probably like Coding Blocks!

# What's this all about?

In this presentation we are going to build a local development environment for the Elastic Stack that will be easy to work with in Kubernetes.

- Why you should consider running Elastic Cloud on Kubernetes (ECK)
- See what you can do with it

The code for this up on Github, there is a link in the description. Of course, I tried to make it as easy as possible to get started. I tried to be really careful when I did this, because sometimes presentations or videos or blogs will boil something down to a really simple prototype. As soon as you try to make any changes, the whole thing falls apart because the prototype was designed to be demonstratable, not for production.

I want this demonstration to be different. I want to give you something you can take and make your own. In fact we'll be creating and evolving several different envronments throughout this talk.

# What is ECK?

Elastic Cloud on Kubernetes is a essentially a plugin for Kubernetes that greatly simplifies the orchestration of Elastic bits. Allgedly it's what Elastic uses as the engine under the covers for Elastic Cloud, minus the snazzy UI.

# Why Run Elastic Cloud on Kubernetes?

- Benefits of Kubernetes
  - Software Defined Architecture
  - Portable
  - Self-Documenting
- Experimentation

# Why not?

- Elastic Cloud
- Complexity
- Performance

# Setup

- Start Docker
- Settings > Kubernetes
- Settings > Resources
- k9s -n all

# About k9s

- Open-Source- CLI tool for interacting w/ Kubernetes
- Resource oriented like Kubernetes
  - See all the :abilities with :alias
  - Filter w/ /
  
- Supports most of the kubectl commands
  - AutoComplete
  - Easy filtering
  - Log Viewer
- Polls resources
  - Great for presentations!

# Important types of resources

- Pods
  - Smallest Unit that Kubernetes cares about
  - Hosts one or more containers
  - Can be associated with PersistentVolumes
- StatefulSets
  - Define one or more pods
  - Designed to maintain state via PersistentVolumes
- Services
  - Abstraction for DNS, Load Balancers, external resources etc
  - Associates name/IP with one or more pods

## Elastic Cloud on Kubernetes

- Set of Custom Resource Definitions that extend the kubernetes API
- Adds definitions for new types of resources: Elasticsearch, Kibana etc
- [kubectl create -f https://download.elastic.co/downloads/eck/2.3.0/crds.yaml](https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-deploy-eck.html)
- Now Kubernetes speaks our language!
  - :elasticsearch

## Install the Elastic Operator

- `kubectl apply -f https://download.elastic.co/downloads/eck/2.3.0/operator.yaml`
- Creates an operator in :namespace elastic-system
- An operator is like a person on your team that is responsible for managing your Kubernetes cluster
- It will take your Elasticsearch and Kibana resource definitions and translate it to Kubernetes resources

## Install Elasticsearch

- `kubectl apply -f k8s/3_cluster/elasticsearch.yaml `
- `kubectl apply -f k8s/3_cluster/kibana.yaml`
- :elasticsearch, :statefulset, :pods, :persistentvolume, :service

## But how do we see it?

- Clusters are closed to the outside world by default
- We need to open up a port
- Oh...https?
- Oh...authentication?
- :secrets

# Examples

- Adding nodes
- Delete nodes
 - What happens to replicas?
- Rolling upgrade
    - Downgrade not supported!
- Different types of services
  - Kibana
- Setting up beats
- Multiple clusters
- Loading data

# Where do we go from here?

- [Presentation Materials](https://github.com/codingblocks/eck-k8s-skaffold/)
- Helm Templates
- Certificates
- Custom applications

## Tools Used

- [Kubernetes](https://kubernetes.io/)
- [Elastic Cloud on Kubernetes](https://www.elastic.co/elastic-cloud-kubernetes)
  - [ECK Quickstart](https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-quickstart.html)
- Optional Recommendations
  - [Docker Desktop](https://www.docker.com/products/docker-desktop/)
  - [Helm](https://helm.sh/)
  - [K9s](https://k9scli.io/)
  - [Visual Studio Code](https://code.visualstudio.com/) (w/ [REST Client plugin](https://marketplace.visualstudio.com/items?itemName=humao.rest-client))
