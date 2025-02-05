---
title: "Services"
keywords: 'KubeSphere, Kubernetes, services, workloads'
description: 'Create a KubeSphere Service'
linkTitle: "Services"
weight: 10240
---

A Service is an abstract way to expose an application running on a set of Pods as a network service. Namely, a Service groups endpoints of these Pods into a single resource, which can be accessed through different ways.

With Kubernetes, you don't need to modify your application to use an unfamiliar service discovery mechanism. Kubernetes gives Pods their IP addresses and a single DNS name for a set of Pods, and can load-balance across them.

For more information, see the [official documentation of Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/).

## Access Types

- **Virtual IP**: It is based on the unique IP generated by the cluster. A service can be accessed through this IP inside the cluster. This type is suitable for most services. Alternatively, a service can also be accessed through a NodePort and LoadBalancer outside the cluster.

- **Headless**: The cluster does not generate an IP address for the service, and the service is directly accessed through the backend Pod IP of the service within the cluster. This type is suitable for backend heterogeneous services, such as services that need to distinguish between master and slave.

{{< notice tip>}}

In KubeSphere, stateful and stateless Services are created with a virtual IP by default. If you want to create a Headless Service, use **YAML** to configure it directly.

{{</ notice>}}

## Prerequisites

You need to create a workspace, a project and an account (`project-regular`). The account must be invited to the project with the role of `operator`. For more information, see [Create Workspaces, Projects, Accounts and Roles](../../../quick-start/create-workspace-and-project).

## Service Types

As shown in the image below, KubeSphere provides three basic methods to create a Service: **Stateless Service**, **Stateful Service**, and **External Service**. Besides, you can also customize a Service through **Specify Workloads** and **Edit by YAML** under **Custom Creation**.

![create_service_type](/images/docs/project-user-guide/workloads/create_service_type.png)

- **Stateless Service**

  Stateless Services are the most commonly used Services in container services. They define the Pod template to control the Pod status, including rolling updates and rollbacks. A **Deployment** workload is also created when you create a stateless Service. For more information about stateless Services, see [Deployments](../../application-workloads/deployments).

- **Stateful Service**

  Stateful Services are used to manage stateful applications, ensuring ordered and graceful deployment and scaling. They also provide stable persistent storage and network identifiers. A **StatefulSet** workload is also created when you create a stateful Service. For more information about stateful Services, see [StatefulSets](../../application-workloads/statefulsets).

- **External Service**

  Different from stateless and stateful Services, an ExternalName Service maps a Service to a DNS name instead of a selector. You need to specify these Services in the **ExternalName** field, indicated by `externalName` in the YAML file.

- **Specify Workloads**

  Create a Service with existing Pods.

- **Edit by YAML**

  Create a Service directly with YAML. You can upload and download YAML configuration files to and from the console.

  {{< notice tip>}}

The value of `annotations:kubesphere.io/serviceType` keywords can be defined as: `statelessservice`, `statefulservice`, `externalservice` and `None`.

  {{</ notice>}}

## Create a Stateless Service

### Step 1: Open the dashboard

1. Go to **Services** under **Application Workloads** of a project and click **Create**.

    ![services_lists](/images/docs/project-user-guide/workloads/services_lists.jpg)

2. Click **Stateless Service**.

    ![stateless_form](/images/docs/project-user-guide/workloads/stateless_form.jpg)

    {{< notice note >}}

The steps of creating a stateful Service and a stateless Service are basically the same. This example only goes through the process of creating a stateless Service for demonstration purpose.

{{</ notice >}} 

### Step 2: Input basic information

1. In the dialog that appears, you can see the field **Version** prepopulated with `v1`. You need to define a name for the Service, such as `demo-service`. When you finish, click **Next** to continue.

    ![stateless_form_1](/images/docs/project-user-guide/workloads/stateless_form_1.jpg)

    - **Name**: The name of the Service and Deployment, which is also the unique identifier.
    - **Alias**: The alias name of the Service, making resources easier to identify.
    - **Version**: It can only contain lowercase letters and numbers. The maximum length of characters is set to 16.

    {{< notice tip >}}

The value of **Name** is used in both configurations, one for Deployment and the other for Service. You can see the manifest file of the Deployment and the Service by enabling **Edit Mode** in the top right corner. Below is an example file for your reference.

    {{</ notice>}}
    
    ``` yaml
    kind: Deployment
    metadata:
      labels:
        version: v1
        app: xxx
      name: xxx-v1
    spec:
      selector:
        matchLabels:
          version: v1
          app: xxx
      template:
        metadata:
          labels:
            version: v1
            app: xxx
    ---
    kind: Service
    metadata:
      labels:
        version: v1
        app: xxx
      name: xxx
    spec:
        metadata:
          labels:
            version: v1
            app: xxx
    ```

### Step 3: Set an image

To add a container image for the Service, see [Set an image](../deployments/#step-3-set-an-image) for details.

![stateless_form_2.png](/images/docs/project-user-guide/workloads/stateless_form_2.jpg)

{{< notice tip >}}

For more information about explanations of dashboard properties, see [Container Image Settings](../container-image-settings/) directly. 

{{</ notice >}}

### Step 4: Mount volumes

To mount a volume for the Service, see [Mount Volumes](../deployments/#step-4-mount-volumes) for details.

![stateless_form_3](/images/docs/project-user-guide/workloads/stateless_form_3.jpg)

### Step 5: Configure advanced settings

You can set a policy for node scheduling and add metadata which is the same as explained in [Deployments](../deployments/#step-5-configure-advanced-settings). For a Service, you can see two additional options available, **Internet Access** and **Enable Sticky Session**.

![stateless_form_4](/images/docs/project-user-guide/workloads/stateless_form_4.jpg)

- Internet Access

  You can expose a Service externally through two methods, NodePort and LoadBalancer.

  - **NodePort**: A Service is exposed on each node's IP address at a static port.

  - **LoadBalancer**: Clients send requests to the IP address of a load balancer.

  {{< notice note >}}

This value is specified by `.spec.type`. If you select **LoadBalancer**, you need to add annotations for it at the same time.

  {{</ notice >}}

- Enable Sticky Session

  You may want to route all traffic sent from a single client session to the same instance of an app which runs across multiple replicas. This makes better use of caches as it reduces latency. This behavior of load balancing is called Sticky Sessions.

  You can set the maximum session sticky time in this field, specified by `.spec.sessionAffinityConfig.clientIP.timeoutSeconds` in the manifest file, which defaults to 10800.

## Check Service Details

### Detail page

1. After a Service is created, you can click the three dots on the right to further edit it, such as its metadata (excluding **Name**), YAML, port, and Internet access.

    ![stateless_finish](/images/docs/project-user-guide/workloads/services_lists_finish.jpg)

    - **Edit**: View and edit the basic information.
    - **Edit YAML**: View, upload, download, or update the YAML file.
    - **Edit Service**: View the access type and set selectors and ports.
    - **Edit Internet Access**: Edit the service Internet access method.
    - **Delete**: When you delete a Service, associated resources will be displayed. If you check them, they will be deleted together with the Service.

2. Click the name of the Service and you can go to its detail page.

    ![stateless_finish](/images/docs/project-user-guide/workloads/services_detail.png)

    - Click **More** to expand the drop-down menu which is the same as the one in the Service list.
    - The Pod list provides detailed information of the Pod (status, node, Pod IP and resource usage).
    - You can view the container information by clicking a Pod item.
    - Click the container log icon to view output logs of the container.
    - You can view the Pod detail page by clicking the Pod name.

### Resource status

1. Click the **Resource Status** tab to view information about the Service ports and Pods.

   ![services](/images/docs/project-user-guide/workloads/services_resource_status.png)

2. In the **Pods** area, click <img src="/images/docs/project-user-guide/workloads/services_refresh_pods.png" width="20px" /> to refresh the Pod information, and click <img src="/images/docs/project-user-guide/workloads/services_display_containers.png" width="20px" />/<img src="/images/docs/project-user-guide/workloads/services_hide_containers.png" width="20px" /> to display/hide the containers in each Pod.

   ![services](/images/docs/project-user-guide/workloads/services_pods.png)

### Metadata

Click the **Metadata** tab to view the labels and annotations of the Service.

![services](/images/docs/project-user-guide/workloads/services_metadata.png)

### Events

Click the **Events** tab to view the events of the Service.

![services](/images/docs/project-user-guide/workloads/services_events.png)