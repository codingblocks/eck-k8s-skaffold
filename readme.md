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


Building a local development environment for the Elastic Stack that will be easy to work with.

Everything you need to run this environment is up on Github, there is a link in the description. Of course, I tried to make it as easy as possible to get started. I tried to be really careful when I did this, because sometimes presentations or videos or blogs will boil something down to it's simplest components to make a tidy prototype but that prototype is kind of an illusion. As soon as you try to make any changes, the whole thing falls apart because the prototype was designed to be demonstratable, not for real-life.

I want this demonstration to be the opposite of that. I want to give you something you can take and make your own. Now, we will be using on a couple big technologies and you don't need to know much about them, but if you are serious about taking this infrastructure to production then you're going to need to learn more about tools like Kubernetes, and Helm, and Skaffold. Because this demonstration is not a product, it's a good foundation.


### About Coding Blocks

I have a podcast with a couple friends where we talk about all things Software Development, if you like this presentation, then you'll probably like that too.

## The Problem

This talk is focused around the Elastic Stack, which is a set of services that work really well together. Organizations that use these tools in production environments generally end up running a LOT of these services. Frankly, if you can get away with a single node of Elasticsearch, then you can probably solve your problem easier another way. Much of the value of the Elastic STack comes from the scalability that you get.

This also means that it can be difficult to have a local environment on your laptop that is similar to what you run in production. Because of this you'll often see developers doing things like setting up shared development environments. We do this because it takes time and energy to set this things up and maintain them. Why should every developer go through the pain of setting up an Elasticsearch cluster and ancillary services, when you can have one do it and every body get the value? 

Shared development environments just make sense.

## The Problem with the Solution

Well, except...

Sometimes a developer is working on a fix for an older relase of the product. Any upgrades to the shared environemnt make it harder for them to work.

What if a developer on the team needs to change a version or a schema for a future release. Any changes for this will put the shared development environment out of sync with production

Well, I guess you really need a couple different environments. Anybody who's done this knows that it's complicated. Now instead of managing 1 cluster, you're managing several and there is a lot of overhead with that.

## Local Development

Local development solves these problems by giving developers isolated environments that can be versioned with application code to keep changes in sync. Need to work on an older release? No problem just checkout the tag or the branch! Need to make a breaking change that could result in some downtime? No problem, it doesn't affect anybody else. You mess up and brick the environment for a few hours? The other devs aren't chucking tomatoes at you because it doesn't affect them.

Local development is better in every way I can think of, except that it puts the onus of maintaining complicated environments on the individual developers...but that's where the tooling comes in.

## Demo

Today I'm going to show you how you can use Elastic Cloud on Kubernetes, and Skaffold - an open source tool from Google that makes it easy to develop and publish your architecture to Kubernetes so you can focus on getting work done.