# About Coding Blocks
I'm Joe Zack, software engineer who works with streaming analytics using a lot of Kafka, Elasticsearch, and Kubernetes. I have a podcast with a couple friends where we talk about all things Software Development, if you like this presentation, then you'll probably like Coding Blocks!

# Title

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
- Experimentation (SimCity)

# Why not?

- Elastic Cloud
- Complexity
- Performance

On to demo.md