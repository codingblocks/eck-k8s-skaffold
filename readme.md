# Easy Local Development with Elastic Cloud on Kubernetes with Skaffold

Elastic Cloud on Kubernetes (ECK) provides everything you need to run the Elasticsearch and Kibana in Kubernetes. We will use ECK together with Skaffold, an open-source Kubernetes tool from Google, to create a simple, flexible, and modular environment that is easy to develop for.

By the end of this presentation, you will be creating your own Elastic Stack architectures for development, testing, experimentation, and fun. Yes, really!

# TODO Loading data

## Requirements

- Skaffold
- Docker
- Kubernetes
- Helm

## About the Author:
Joe Zack is a Software Developer with a focus in streaming analytics and a newfound love of DevOps. He is a host of the Coding Blocks podcast, and he wants to #getgood at Python.

## Presentation

### Title

In this presentation we are going to build a local development environment for the Elastic Stack that will be easy to work with in Kubernetes.

Everything you need to run this environment is up on Github, there is a link in the description. Of course, I tried to make it as easy as possible to get started. I tried to be really careful when I did this, because sometimes presentations or videos or blogs will boil something down to a really simple prototype. As soon as you try to make any changes, the whole thing falls apart because the prototype was designed to be demonstratable, not for production.

I want this demonstration to be different. I want to give you something you can take and make your own. In fact we'll be creating and evolving several different envronments throughout this talk.

### About Coding Blocks

I have a podcast with a couple friends where we talk about all things Software Development, if you like this presentation, then you'll probably like that too.

## The Problem

This talk is focused around the Elastic Stack, which is a set of services that work really well together. Organizations that use these tools in production environments generally end up running a LOT of these services.

This means that it can be difficult to have a local environment on your laptop that is similar to what you run in production. It takes effort to maintain multiple services.

Because of this you'll often see developers doing things like setting up shared development environments. Why should every developer go through the pain of setting up an Elasticsearch cluster and all the ancillary services, when you can have one developer set up an environment that everybody else can share.

## The Problem with the Solution

Except...sometimes developers need different configurations or versions, for instance if you're working on a future release that requires a new version of the product. Should your shared environment match production, or the latest state of development?

Maybe you need 2 clusters. And if you need to support an older release, maybe 3. And one for load testing, user acceptance testing, maybe staging. Suddenly we have half a dozen environments, and maintaining them is getting costly.

Inevitably somebody makes a change that breaks half the team. You know how that goes.

## Local Development

Local development solves these problems by giving developers isolated environments that can be versioned with application code to keep changes in sync. Need to work on an older release? No problem just checkout the tag or the branch! Need to make a breaking change? Same thing. You goof up and lose all your data? The damage is contained to your environment.

Local development is better in every way I can think of, except for 2.

1. Local environments are typically smaller than production, since the entire architecture needs to run on a single laptop
2. It puts the onus of maintaining complicated environments on the individual developers

But that's where the tooling comes in.

## Demo

I'm going to show you how you can use Skaffold, an open source tool from Google, to make it easy to develop, evolve, and publish your architecture to Kubernetes so you can focus on the work that makes your organization unique.


### Part 1: Install ECK

I'm not going to spend much time talking about ECK, that's a whole talk on it's own. TL;DR version, ECK provides you with custom resource definitions that make working with Elastic and Kubernetes really easy.

TODO: Download a back-up of ECK

#### Install the CRD

- kubectl apply -f https://download.elastic.co/downloads/eck/1.3.0/all-in-one.yaml
- view logs

If we check the logs we can see that this operator is managing our certificates and is just kinda hanging out waiting for something to do.

#### Create Elasticsearch / Kibana

I grabbed a couple files from the ECK quickstart. These fields reference the custom resource definitions we installed earlier, and the operator knows what to do with them.

- `kubectl apply -f k8s`
- Check the logs
- `kubectl get secret quickstart-es-elastic-user -o go-template='{{.data.elastic | base64decode}}`'
- port-forward Kibana

#### What is our life like now?

Well, we have some files defined that work for dev. This is about where ECK presentations normally end, but it's not enough. Port-forwarding is awkard and we need different configurations for different environments. Any changes that we need to make require going back to the command line to be applied. Yuck.

#### Enter skaffold

- choco install -y skaffold
- https://skaffold.dev/docs/references/yaml/


Skaffold is a tool for managing YOUR workflow, and it's surprisingly simple. We'll create a new file with information about our environments. The formal reference is a single web page, because Skaffold is simple but it's extremely expressive - meaning you can do a lot, with a ittle.

We've got a bit of boilerplate to start here, pretty much everything in k8s land needs an apiVersion and a kind. Next we tell skaffold that we have a folder of kubernetes files that we want to apply.

```
apiVersion: skaffold/v2beta10
kind: Config
deploy:
  kubectl:
    manifests:
      - k8s/*.yaml
```

We can run this with `skaffold dev` to start up our environment. One cool thing about skaffold is it watches the directories it knows about for changes. Let's add some nodes!

Skaffold doesn't just watch for yaml changes. It works for any files it knows about. If you have a python application server hooked up in your skaffold file, then it will reload that on any changes you make in the python.

More on that in a sec. I want to configure port-forwarding so that developers on my team can get to work with a single command.

```
portForward:
  - resourceType: Service
    resourceName: quickstart-kb-http
    port: 5601
    localPort: 5601
```

What is dev live like? `git clone && skaffold dev --port-forward`

## Profiles

Skaffold can do a lot of other things, but there are two other capabilities that I want to focus on.

- Profiles
- CI/CD

I mentioned the problem where a developer might need to work with a different version or configuration. So let's look at how we can handle that with Helm and Skaffold.

If you're not familiar with Helm, it's a really common tool for working with Kubernetes that gives you templating capabilities. Ie: loops and variables.

```
helm create helm/elasticstack
```

This creates a example chart, but we really only need a few things so I'm going to delete all the stuff we don't care about an dcopy in the k8s files from before.

Then I'm going to impliment variables for version and nodeCount. 


## Conclusion

I love Elastic, Kubernetes and Skaffold. If your org has already decided to run Elastic Stack in Kubernetes, then I think Skaffold is a no-brainer. It makes local development, maintenance, and deployment simple and it's got a low barrier to entry, requiring no refactoring.


TODO:
- Push to "production"
  - What if production isn't Kubernetes?
- Helm charts, variables
- Profiles

- [What else can Skaffold do?](https://skaffold.dev/docs/)
  - Image building
  - Testers
  - Deployers
  - Tag Policies
  - Push Strategies
  - Demo environments
  - Render
  - Health/Status checks
  - Debugging

- What now?
- Install skaffold, Docker, Create a skaffold.yaml file and go live your best lyfe!

## Resources

- [Elastic Cloud on Kubernetes Quickstart](https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-quickstart.html)
- [Get started with skaffold](https://skaffold.dev/docs/quickstart/)