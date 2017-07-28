---
layout: "guides"
page_title: "Managing Kubernetes with Terraform - Guides"
sidebar_current: "guides-managing-kubernetes-with-terraform"
description: |-
  TODO
---

# Managing Kubernetes with Terraform

## Kubernetes

[Kubernetes](https://kubernetes.io/) is an open-source workload scheduler
with focus on containerized applications.

There are generally 2 steps involved in scheduling your first container
on a K8s cluster. You need the K8S cluster with all its components
running _somewhere_ and then schedule the K8S resources, like Pods,
Replication Controllers, Services etc.

This guide focuses mainly on the latter part and expects you to have
a properly configured & running Kubernetes cluster.

## Why Terraform?

While you could use `kubectl` to manage all K8S resources described
in YAML files, orchestration with Terraform presents a few benefits.

 - HCL - if 
 - drift detection
 - full lifecycle management with synchronous feedback

## Provider Setup

The easiest way to configure the provider is by creating/generating a config
in a default location (`~/.kube/config`). That allows you to leave the
provider block completely empty.

```hcl
provider "kubernetes" {}
```

If you wish to configure the provider statically you can do so

```hcl
provider "kubernetes" {
  host     = "https://104.196.242.174"
  username = "ClusterMaster"
  password = "MindTheGap"

  client_certificate     = "${file("~/.kube/client-cert.pem")}"
  client_key             = "${file("~/.kube/client-key.pem")}"
  cluster_ca_certificate = "${file("~/.kube/cluster-ca-cert.pem")}"
}
```

## Scheduling a Simple Application

```hcl
resource "kubernetes_pod" "nginx" {
  metadata {
    name = "nginx-example"
    labels {
      App = "nginx"
    }
  }

  spec {
    container {
      image = "nginx:${var.nginx_version}"
      name  = "example"

      port {
        container_port = 80
      }
    }
  }
}
```

```hcl
resource "kubernetes_service" "nginx" {
  metadata {
    name = "nginx-example"
  }
  spec {
    selector {
      App = "${kubernetes_pod.nginx.metadata.0.labels.App}"
    }
    port {
      port = 80
      target_port = 80
    }

    type = "LoadBalancer"
  }
}
```

## Scheduling a Robust and Scalable Application

RCs and HPA

```hcl

```

### Updating Application Configuration

```hcl

```

## Bonus: Managing Quotas and Limits

TODO

## Conclusion

TODO
