---
title: "Overview"
level: 0
---

## Overview of the ArtemisCloud Operator Custom Resource Definitions

In general, a Custom Resource Definition (CRD) is a schema of configuration items that you can modify for a custom Kubernetes 
object deployed with an Operator. By creating a corresponding Custom Resource (CR) instance, you can specify values for 
configuration items in the CRD. If you are an Operator developer, what you expose through a CRD essentially becomes the 
API for how a deployed object is configured and used. You can directly access the CRD through regular HTTP curl commands, 
because the CRD gets exposed automatically through Kubernetes.

***Main broker CRD***

You deploy a CR instance based on this CRD to create and configure a broker deployment

The **broker_activemqartemis_crd** file in the **crds** directory of the Operator installation archive (Kubernetes CLI installation method)

***Address CRD***

You deploy a CR instance based on this CRD to create addresses and queues for a broker deployment.

The **broker_activemqartemisaddress**_crd file in the **crds** directory of the Operator installation archive (Kubernetes CLI installation method)

***Scaledown CRD***

The Operator automatically creates a CR instance based on this CRD when instantiating a scaledown controller for message migration.

The **broker_activemqartemisscaledown_crd** file in the **crds** directory of the Operator installation archive (Kubernetes CLI installation method)

### Additional resources

To learn how to install the ActiveMQ Artemis Operator (and all included CRDs) using:

The Kubernetes CLI, see [Installing the Operator](#operator_installing)

For complete configuration references to use when creating CR instances based on the main broker and address CRDs, see:

Broker Custom Resource configuration reference

Address Custom Resource configuration reference