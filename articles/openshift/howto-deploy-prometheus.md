---
title: Deploy a standalone Prometheus in an Azure Red Hat Openshift cluster | Microsoft Docs
description: Here's how to create a Prometheus instance on an Azure Red Hat OpenShift cluster to monitor your application's metrics.
author: Makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus, aro, openshift, metrics, red hat
---

# Deploy a standalone Prometheus in an Azure Red Hat Openshift cluster

This how-to will describe how to configure a standalone Prometheus with service discovery.

The desired setup is as follows:

- one project which contains the prometheus and alertmanager called prometheus-project
- two projects which contain the applications to monitor called app-project1 and app-project2

We will prepare some configuration files locally, a new folder is recommended for that.
We put the config files into the cluster as secrets in case there are some secret tokens added to the configuration.

## Step 1: Log in
Log in with your oc tool by going to the URL based on properties.publicHostname of the cluster:

https://openshift.random-id.region.azmosa.io

and logging in,
then clicking your username in top right and selecting "Copy Login Command",
and pasting it into the terminal you will use

You can verify if you're logged in to the correct cluster with the `oc whoami -c` command.

## Step 2: Prepare the projects

Create projects
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> You can either use the `-n` or `--namespace` parameter or select an active project with the `oc project` command

## Step 3: Prepare Prometheus config
Create a file called prometheus.yml with the following content:
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
create a secret named "prom" with the configuration
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

This is a basic Prometheus config file.
It sets the intervals and configures auto discovery in 3 projects (prometheus-project, app-project1, app-project2).
The auto discovered endpoints will be scraped over HTTP without authentication.
For more details on scraping endpoints please see https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config


## Step 4: Prepare Alertmanager config
Create a file called alertmanager.yml with the following content:
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
create a secret named "prom-alerts" with the configuration
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

This is the Alert Manager configuration file.

> [!NOTE]
> You can verify the two previous steps with `oc get secret -n prometheus-project`

## Step 5: Start the Prometheus and Alertmanager
Download the [prometheus-standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) from the [openshift/origin repository](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus)
and apply it in the prometheus-project
```
oc process -f prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
This is an OpenShift Template which will create a prometheus instance with an oauth-proxy in front of it
and an alertmanager instance, also secured with oauth-proxy.

> [!NOTE]
> You can verify if the "prom" StatefulSet has equal *DESIRED* and *CURRENT* number replicas with the `oc get statefulset -n prometheus-project` command. You can also check all resources in the project with `oc get all -n prometheus-project`.

## Step 6: Add permissions to perform Service Discovery
Create prometheus-sdrole.yaml with the following content:
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus Service Discovery Role
    description: |
      A role and rolebinding adding permissions required to perform Service Discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
Apply the template to all projects where you would like to allow service discovery:
```
oc process -f prometheus-sdrole.yaml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yaml | oc apply -f - -n app-project2
```
If you'd like to also gather metrics from prometheus remember to add the permissions in prometheus-project.

> [!NOTE]
> You can verify if the Role and RoleBinding were created correctly with the `oc get role` and `oc get rolebinding` commands respectively

# Optional: Deploy example application
Everything is working, but there are no metrics sources. Please go to the Prometheus URL which can be found with
```
oc get route prom -n prometheus-project
```
Remember to prefix the hostname with https://.

The **Status > Service Discovery** page will show 0/0 active targets.

To deploy an example application which exposes basic python metrics under the /metrics endpoint run the following commands.
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
The new applications will appear as valid targets on the Service Discovery page within 30s after deployment.

# Recommended reading

The prometheus client library, which simplifies preparing prometheus metrics, is ready for different programming languages:

 - Java https://github.com/prometheus/client_java
 - Python https://github.com/prometheus/client_python
 - Go https://github.com/prometheus/client_golang
 - Ruby https://github.com/prometheus/client_ruby
