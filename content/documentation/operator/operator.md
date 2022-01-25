# foo bar 

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

The Kubernetes CLI, see [Installing the Operator](#installing-the-operator-using-the-cli)

For complete configuration references to use when creating CR instances based on the main broker and address CRDs, see:

Broker Custom Resource configuration reference

Address Custom Resource configuration reference

## Installing the Operator using the CLI

The procedures in this section show how to use the Kubernetes command-line interface (CLI) to install and deploy the latest 
version of the Operator for ArtemisCloud in a given Kubernetes project. In subsequent procedures, you use this Operator 
to deploy some broker instances.

### Getting the Operator code

This procedure shows how to access and prepare the code you need to install the latest version of the Operator for ArtemisCloud .

Procedure
Download the latest version of the Operator from https://github.com/artemiscloud/activemq-artemis-operator/archive/v0.18.1.zip

When the download has completed, move the archive to your chosen installation directory. The following example moves the archive to a directory called ~/broker/operator.

$ mkdir ~/broker/operator
$ mv activemq-artemis-operator-0.18.1.zip ~/broker/operator
In your chosen installation directory, extract the contents of the archive. For example:

$ cd ~/broker/operator
$ unzip activemq-artemis-operator-0.18.1.zip
Switch to the directory that was created when you extracted the archive. For example:

$ cd activemq-artemis-operator
Specify the project in which you want to install the Operator. You can create a new project or switch to an existing one.

Create a new namespace:

$ kubectl create namespace  <project-name>
Or, switch to an existing namespace:

$ kubectl config set-context $(kubectl config current-context) --namespace= <project-name>
Specify a service account to use with the Operator.

In the deploy directory of the Operator archive that you extracted, open the service_account.yaml file.

Ensure that the kind element is set to ServiceAccount.

In the metadata section, assign a custom name to the service account, or use the default name. The default name is activemq-artemis-operator.

Create the service account in your project.

$ kubectl create -f deploy/service_account.yaml
Specify a role name for the Operator.

Open the role.yaml file. This file specifies the resources that the Operator can use and modify.

Ensure that the kind element is set to Role.

In the metadata section, assign a custom name to the role, or use the default name. The default name is activemq-artemis-operator.

Create the role in your project.

$ kubectl create -f deploy/role.yaml
Specify a role binding for the Operator. The role binding binds the previously-created service account to the Operator role, based on the names you specified.

Open the role_binding.yaml file. Ensure that the name values for ServiceAccount and Role match those specified in the service_account.yaml and role.yaml files. For example:

metadata:
    name: activemq-artemis-operator
subjects:
    kind: ServiceAccount
    name: activemq-artemis-operator
roleRef:
    kind: Role
    name: activemq-artemis-operator
Create the role binding in your project.

$ kubectl create -f deploy/role_binding.yaml
In the procedure that follows, you deploy the Operator in your project.

## Deploying the Operator using the CLI

The procedure in this section shows how to use the Kubernetes command-line interface (CLI) to deploy the latest version of the Operator for ArtemisCloud in your Kubernetes project.

Prerequisites
You must have already prepared your Kubernetes project for the Operator deployment. See Getting the Operator code.

If you intend to deploy brokers with persistent storage and do not have container-native storage in your Kubernetes cluster, you need to manually provision Persistent Volumes (PVs) and ensure that they are available to be claimed by the Operator. For example, if you want to create a cluster of two brokers with persistent storage (that is, by setting persistenceEnabled=true in your Custom Resource), you need to have two PVs available. By default, each broker instance requires storage of 2 GiB.

If you specify persistenceEnabled=false in your Custom Resource, the deployed brokers uses ephemeral storage. Ephemeral storage means that that every time you restart the broker Pods, any existing data is lost.

Procedure
Specify the project in which you want to install the Operator. You can create a new project or switch to an existing one.

Create a new namespace:

$ kubectl create namespace  <project-name>
Or, switch to an existing namespace:

$ kubectl config set-context $(kubectl config current-context) --namespace= <project-name>
Switch to the directory that was created when you previously extracted the Operator installation archive. For example:

$ cd ~/broker/operator/amq-broker-operator--ocp-install-examples
Deploy the CRDs that are included with the Operator. You must install the CRDs in your Kubernetes cluster before deploying and starting the Operator.

Deploy the main broker CRD.

$ kubectl create -f deploy/crds/broker_activemqartemis_crd.yaml
Deploy the address CRD.

$ kubectl create -f deploy/crds/broker_activemqartemisaddress_crd.yaml
Deploy the scaledown controller CRD.

$ kubectl create -f deploy/crds/broker_activemqartemisscaledown_crd.yaml
In the deploy directory of the Operator archive that you downloaded and extracted, open the operator.yaml file. Ensure that the value of the spec.containers.image property is set to the latest Operator image for ActiveMQ Artemis , as shown below.

spec:
    template:
        spec:
            containers:
                image: quay.io/artemiscloud/activemq-artemis-operator:0.18.1
Deploy the Operator.

$ kubectl create -f deploy/operator.yaml
In your Kubernetes project, the Operator starts in a new Pod.

In the Kubernetes web console, the information on the Events tab of the Operator Pod confirms that Kubernetes has deployed the Operator image that you specified, has assigned a new container to a node in your Kubernetes cluster, and has started the new container.

In addition, if you click the Logs tab within the Pod, the output should include lines resembling the following:

...
{"level":"info","ts":1553619035.8302743,"logger":"kubebuilder.controller","msg":"Starting Controller","controller":"activemqartemisaddress-controller"}
{"level":"info","ts":1553619035.830541,"logger":"kubebuilder.controller","msg":"Starting Controller","controller":"activemqartemis-controller"}
{"level":"info","ts":1553619035.9306898,"logger":"kubebuilder.controller","msg":"Starting workers","controller":"activemqartemisaddress-controller","worker count":1}
{"level":"info","ts":1553619035.9311671,"logger":"kubebuilder.controller","msg":"Starting workers","controller":"activemqartemis-controller","worker count":1}
The preceding output confirms that the newly-deployed Operator is communicating with Kubernetes, that the controllers for the broker and addressing are running, and that these controllers have started some workers.

It is recommended that you deploy only a single instance of the ActiveMQ Artemis Operator in a given Kubernetes project. Setting the replicas element of your Operator deployment to a value greater than 1, or deploying the Operator more than once in the same project is not recommended.