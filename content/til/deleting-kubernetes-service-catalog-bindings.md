---
title: Deleting Kubernetes Service Catalog bindings
date: 2021-02-23
summary: >
  Kubernetes' Service Catalog is less documented/blogged about.
  Learn how to delete stale instance bindings for Service Catalog instances
tags:
  - kubernetes
  - cli
---

# tl;dr

```bash
svcat unbind <instance-name> # this will remove all the bindings for this service catalog instance
svcat unbind --name <binding-name> # this will remove a specific binding
```

# Motivation

I recently used my company's [Kubernetes Service Catalog](https://kubernetes.io/docs/concepts/extend-kubernetes/service-catalog/) to spin up an [Elasticache](https://aws.amazon.com/elasticache/redis/) instance for an application. [When you create a ServiceInstance, you need to bind it to an application](https://kubernetes.io/docs/concepts/extend-kubernetes/service-catalog/#binding-to-a-managed-service) in order to get connection information for that instance.

From the Service Catalog documentation:

> After a new instance has been provisioned, a cluster operator must bind to the managed service to get the connection credentials and service account details necessary for the application to use the service. 

That said -- I messed up the original binding.

I tried to create a `ServiceInstance` but missed a parameter, resulting in two things:

- The service instance has an orphaned mitigation
- The service binding had an error

# What I did

We use the [`svcat` cli](https://svc-cat.io/docs/cli/) to interact with Service Catalog.

Once I found out what the issue was with the `ServiceInstace`, I patched up the `yaml` config and ran an apply. **This resulted in an error because the service instance + binding was in a bad state.**

In a panic, I renamed the `metadata.name` of the service instance and binding, and ran an apply again. This time, the apply worked.

Something didn't feel right. I should not be blocked on bad state to create a `ServiceInstance`/`ServiceBinding`.

Therefore, I found that I can first delete the bad binding by running the following command:

`svcat unbind --name <binding-name>`

Once the bad binding was removed, I was able to deprovision the bad instance by running:

`svcat deprovision <instance-name>`

# Closing thoughts

When I looked up the webpage for [`svcat`](https://svc-cat.io/docs/cli/#provision-a-service), I found the information I was looking for.  However, I didn't find a blog or any kind of article with the errors/keywords of issues that I was looking for. Thus, this post was born.

I hope that this is helpful.
