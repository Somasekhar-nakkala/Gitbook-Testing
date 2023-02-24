# ISD-Argo 4.1.1 Quick Installation

## Introduction:

To experience ISD-Argo quickly, you can install it and deploy your applications. The instructions below are intended to get you started quickly and try out ISD-Argo functionality.

{% hint style="info" %}
**Note:** **The instructions below are not suitable for production or any other environment where security is a concern, for production-grade installation of ISD-Argo please refer to** [**ISD-Argo Standard Installation.**](https://docs.opsmx.com/opsmx-intelligent-software-delivery-isd-platform-argo/operator-manual/installation-and-configuration/isd-argo-standard-installation)****
{% endhint %}

The installation involves the following components:

* OEA(OpsMx Enterprise for Argo)-UI and associated components (Audit, Platform, Autopilot)
* Argo components (ArgoCD + Argo Rollouts)
* Additional supporting/common components (DB, Redis, OpenLDAP)

In Quick Installation mode, you can install ISD + Master OEA (Argo components: ArgoCD+Rollouts) and it will integrate ArgoCD with ISD by default with an agent.

{% hint style="info" %}
**Note:** ISD-Argo supports the following Ago Components:

* ArgoCD - v2.4.8&#x20;
* Argo Rollout - v1.2.0
* Argo Workflow: v3.3.8
* Argo Events: v1.7.1
{% endhint %}

## Environment requirements:

You should have access to a Kubernetes cluster (with 1 node, 4CPU with 16GB RAM) and kubectl set-up.

**Compute:**

* Minimum: 4CPU, 16 GB RAM, 1 node
* Prefered: 8CPU/32 GB RAM, 2 Nodes

## ISD-Argo Quick Installation Steps:

Follow the procedure below for Quick Installation of ISD for Argo.

