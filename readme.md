# Easy Local Development with Elastic Cloud on Kubernetes with Skaffold

Elastic Cloud on Kubernetes (ECK) provides everything you need to run the Elasticsearch and Kibana in Kubernetes. We will use ECK together with Skaffold, an open-source Kubernetes tool from Google, to create a simple, flexible, and modular environment that is easy to develop for.

By the end of this presentation, you will be creating your own Elastic Stack architectures for development, testing, experimentation, and fun. Yes, really!

## Requirements

- Skaffold
- Docker
- Kubernetes
- Helm

## About the Author:
Joe Zack is a Software Developer with a focus in streaming analytics and a newfound love of DevOps. He is a host of the Coding Blocks podcast, and he wants to #getgood at Python.

## Before Starting

- Reset Kuberentes!

## Presentation

### Title

In this presentation we are going to build a local development environment for the Elastic Stack that will be easy to work with in Kubernetes.

The code for this up on Github, there is a link in the description. Of course, I tried to make it as easy as possible to get started. I tried to be really careful when I did this, because sometimes presentations or videos or blogs will boil something down to a really simple prototype. As soon as you try to make any changes, the whole thing falls apart because the prototype was designed to be demonstratable, not for production.

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

### Part 1: Create the skaffold file

All kubernetes files need to have an apiVersion `skaffold/v2beta10` and a kind `Config`, and Skaffold follows this convention.

Next, we'll create a deploy section so we can install ECK.

I'm not going to spend much time talking about ECK, that's a whole talk on it's own. TL;DR version, ECK provides you with custom resource definitions that make working with Elastic and Kubernetes really easy.

#### Install the CRD

```
kubectl:
  manifests:
    - https://download.elastic.co/downloads/eck/1.3.0/all-in-one.yaml
```

I just told skaffold that there are some files that need to be deployed. Now we can run `skaffold deploy` and it will deploy ECK


#### Create Elasticsearch / Kibana

I grabbed a couple files from the ECK quickstart. These fields reference the custom resource definitions we installed earlier, and the operator knows what to do with them.

We can add these files to our list of manifests, and do another `skaffold deploy`

- Check the logs
- port-forward Kibana

#### What is our life like now?

We have a file, checked into source control, that devs can use to start up our services with a single command.

We can do better though, port-forwarding is awkward, and you have to deploy every time you make a change.

## Skaffold Dev

If we run `skaffold dev --port-forward` then skaffold watches our files for changes, and will automatically deploy them. Additionally, it will automatically set up any port-forwards that it can see.

There is a catch here, life isn't perfect. Skaffold doesn't know how to port-forward our services because they

```
portForward:
  - resourceType: Service
    resourceName: quickstart-kb-http
    port: 5601
    localPort: 5601
    namespace: default
```

What is dev live like? `git clone && skaffold dev --port-forward`

## Profiles

Ok, we have Elastic..but we aren't doing anything with it. If you're working on ingestion related tasks, you may want a blank slate.

Most of the time, you're probably wanting to load some data. Lets setup a profile that gives our developers the _option_ to start loading data.

```
  - name: filebeat
    patches:
      - op: add
        path: /deploy/kubectl/manifests
        value:
          - k8s/filebeat/*
```

Yay, data!

Now, it's not too hard to imagine setting up different clusters as profiles. For example, maybe you want to setup profiles for running different versions of Elasticsearch, or loading different sets of data.

```
  - name: prod
    patches:
      - op: replace
        path: /deploy/kubectl/manifests
        value:
          - https://download.elastic.co/downloads/eck/1.3.1/all-in-one.yaml
          - k8s/prod/*.yaml
```

Quick note: Helm is a really popular tool for templating Kubernetes files, so you can have variables, loops, conditionals, etc. I didn't want to get into Helm in this talk, because it adds a new complicated dimenions but I want to make sure you know that it's fully supported.

## Optional Helm file (Depending on Time)

```
mkdir charts
helm create charts/website
```

This creates a sample helm chart, which expands on Kubernetes by allowing templating, and variables, and tests, and dependencies and...a bunch of other stuff. We don't need to go into it, but if there are any Kubernetes power users out there I wanted you to see what that was like and it gives me the chance to show you how you can combine profiles in interesting ways.

You can also have profiles conditionally activate based on environmental settings.

## Publishing

So far we've only worked with Skaffold dev, but that's not the only verb. In fact, there are about a dozen, with several tailored for CI/CD operations.

`skaffold --help`

Let's setup a quick build, and then I'll show you how you what it is like to use skaffold in your CI/CD pipeline.

## Build

```
build:
  artifacts:
    - name: website
      context: chart/website
```

Now skaffold knows about a docker image, Docker isn't the only thing Skaffold knows how to build, but it's the default.

Any changes made in this directory will trigger a rebuild of the image. Additionally, now skaffold has some work to do when we do a `skaffold build`.

The result of a skaffold build is a tagged image. If we were running this in a CI/CD pipeline we would then retag this image and push it out to our container registry. It was tempting to demo all that, but I wanted to keep things a bit more focused so I'll leave it as an excercise for you. Hit me up on Twitter if you have any questions.

Speaking of CI/CD servers, we can also use `skaffold deploy`

## Deploy

As we've seen, skaffold knows how to build and deploy, and we've got our architecture defined in a single file. Why not go one step further, and use Skaffold on the build server?

`skaffold build` will take care of building and publishing to an image repository. I wanted to focus on Elastic in this talk, so I don't want to go deep here, but there are a number of configurations, environment variables, and command flags that you can use to configure image repositories. Skaffold build can build, and publish to these repositories.

Skaffold also has support for custom build scripts.

## TODO: Actually do this to google?

Once your artifacts are published, you can use configre a context on your deployment server and run `skaffold deploy` to actually dish out the kubernetes files. Anything you can do with skaffold (ie: profiles, helm, kubectl, kustomize, kapt, kaniko, etc) is available to you.

Also, if you're lazy like me you can do both of these in one step, with the command that we ran earlier `skaffold run`, which will build, tag, and publish all in one command. Let's do that real quick, I'm going to deploy to a new namespace to simulate deploying to a remote cluster.

`skaffold run -p filebeat --namespace sandiego`


## [What else can Skaffold do?](https://skaffold.dev/docs/)

There is a lot we didn't cover, for the sake of time, but hopefully I showed you how you can install Skaffold and create a skaffold.yaml file to serve as the bedrock for your development, build, and deployments to keep your dev and production environments in sync.

  - Image building
  - Testers
  - Deployers
  - Tag Policies
  - Push Strategies
  - Demo environments
  - Render
  - Health/Status checks
  - Debugging

## Conclusion

I love Elastic, Kubernetes and Skaffold. If your org has already decided to run Elastic Stack in Kubernetes, then I think Skaffold is a no-brainer. Even if you're org isn't using Kubernetes, I think it's a great choice, better than docker-compose for example, because of it's tie-ins with CI/CD.

- What now?
- Install skaffold, Docker, Create a skaffold.yaml file and go live your best lyfe!

## Resources

- [Elastic Cloud on Kubernetes Quickstart](https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-quickstart.html)
- [Get started with skaffold](https://skaffold.dev/docs/quickstart/)
