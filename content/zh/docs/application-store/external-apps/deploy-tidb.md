---
title: "在 KubeSphere 中部署 TiDB Operator 和 TiDB 集群"
keywords: 'KubeSphere, Kubernetes, TiDB, TiDB Operator, TiDB 集群'
description: '如何在 KubeSphere 中部署 TiDB Operator 和 TiDB 集群'
linkTitle: "部署 TiDB Operator 和 TiDB 集群"
weight: 14320
---

[TiDB](https://en.pingcap.com/) 是一个支持混合事务和分析处理 (HTAP) 工作负载的云原生、开源 NewSQL 数据库，具有水平扩缩性、强一致性以及高可用性。

本教程演示了如何在 KubeSphere 上部署 TiDB Operator 和 TiDB 集群。

## **准备工作**

- 您需要启用 [OpenPitrix 系统](../../../pluggable-components/app-store/)。
- 您需要为本教程创建一个企业空间、一个项目和两个帐户（`ws-admin` 和 `project-regular`）。帐户 `ws-admin` 必须在企业空间中被赋予 `workspace-admin` 角色，帐户 `project-regular` 必须被邀请至项目中赋予 `operator` 角色。若还未创建好，请参考[创建企业空间、项目、帐户和角色](../../../quick-start/create-workspace-and-project/)。

## **动手实验**

### **步骤** **1**：安装 **TiDB Operator CRD**

1. 以 `admin` 身份登录 KubeSphere 的 Web 控制台，使用右下角**工具箱**中的 **Kubectl** 执行以下命令来安装 TiDB Operator CRD：

   ```bash
   kubectl apply -f https://raw.githubusercontent.com/pingcap/tidb-operator/v1.1.6/manifests/crd.yaml
   ```

2. 预期输出如下所示：

   ```bash
   customresourcedefinition.apiextensions.k8s.io/tidbclusters.pingcap.com created
   customresourcedefinition.apiextensions.k8s.io/backups.pingcap.com created
   customresourcedefinition.apiextensions.k8s.io/restores.pingcap.com created
   customresourcedefinition.apiextensions.k8s.io/backupschedules.pingcap.com created
   customresourcedefinition.apiextensions.k8s.io/tidbmonitors.pingcap.com created
   customresourcedefinition.apiextensions.k8s.io/tidbinitializers.pingcap.com created
   customresourcedefinition.apiextensions.k8s.io/tidbclusterautoscalers.pingcap.com created
   ```

### **步骤** **2**：添加应用仓库

1. 登出 KubeSphere，再以 `ws-admin` 身份登录。在企业空间中，访问**应用管理**下的**应用仓库**，然后点击**添加仓库**。

   ![add-repo](/images/docs/zh-cn/appstore/external-apps/deploy-tidb-operator-and-cluster/add-repo.png)

2. 在出现的对话框中，输入 `pingcap` 作为应用仓库名称，输入 `https://charts.pingcap.org` 作为 PingCAP Helm 仓库的 URL。点击**验证**以验证 URL，如果可用，您将会在 URL 旁边看到一个绿色的对号。点击**确定**以继续。

   ![add-pingcap-repo](/images/docs/zh-cn/appstore/external-apps/deploy-tidb-operator-and-cluster/add-pingcap-repo.png)

3. 将仓库成功导入到 KubeSphere 之后，它将显示在列表中。

   ![added-pingcap-repo](/images/docs/zh-cn/appstore/external-apps/deploy-tidb-operator-and-cluster/added-pingcap-repo.png)

### **步骤** **3**：部署 **TiDB Operator**

1. 登出 KubeSphere，再以 `project-regular` 身份登录。在您的项目中，访问**应用负载**下的**应用**，点击**部署新应用**。

   ![deploy-app](/images/docs/zh-cn/appstore/external-apps/deploy-tidb-operator-and-cluster/deploy-app.png)

2. 在出现的对话框中，选择**来自应用模板**。

   ![from-app-templates](/images/docs/zh-cn/appstore/external-apps/deploy-tidb-operator-and-cluster/from-app-templates.png)

3. 从下拉菜单中选择 `pingcap`，然后点击 **tidb-operator**。

   ![click-tidb-operator](/images/docs/zh-cn/appstore/external-apps/deploy-tidb-operator-and-cluster/click-tidb-operator.png)

   {{< notice note >}}

   本教程仅演示如何部署 TiDB Operator 和 TiDB 集群。您也可以按需部署其他工具。

   {{</ notice >}}

4. 在**配置文件**选项卡，您可以直接从控制台查看配置，也可以通过点击右上角的图标以下载默认 `values.yaml` 文件。在**版本**下，从下拉菜单中选择一个版本号，点击**部署**。

   ![select-version](/images/docs/zh-cn/appstore/external-apps/deploy-tidb-operator-and-cluster/select-version.png)

5. 在**基本信息**页面，确认应用名称、应用版本以及部署位置。点击**下一步**以继续。

   ![basic-info](/images/docs/zh-cn/appstore/external-apps/deploy-tidb-operator-and-cluster/basic-info.png)

6. 在**应用配置**页面，您可以编辑 `values.yaml` 文件，也可以直接点击**部署**使用默认配置。

   ![check-config-file](/images/docs/zh-cn/appstore/external-apps/deploy-tidb-operator-and-cluster/check-config-file.png)

7. 等待 TiDB Operator 正常运行。

   ![tidb-operator-running](/images/docs/zh-cn/appstore/external-apps/deploy-tidb-operator-and-cluster/tidb-operator-running.png)

8. 访问**工作负载**，可以看到为 TiDB Operator 创建的两个部署。

   ![tidb-deployment](/images/docs/zh-cn/appstore/external-apps/deploy-tidb-operator-and-cluster/tidb-deployment.png)

### **步骤** **4：部署** **TiDB** **集群**

部署 TiDB 集群的过程与部署 TiDB Operator 的过程相似。

1. 访问**应用负载**下的**应用**，再次点击**部署新应用**，然后选择**来自应用模板**。

   ![deploy-app-again](/images/docs/zh-cn/appstore/external-apps/deploy-tidb-operator-and-cluster/deploy-app-again.png)

   ![from-app-templates-2](/images/docs/zh-cn/appstore/external-apps/deploy-tidb-operator-and-cluster/from-app-templates-2.png)

2. 在 PingCAP 仓库中，点击 **tidb-cluster**。

   ![click-tidb-cluster](/images/docs/zh-cn/appstore/external-apps/deploy-tidb-operator-and-cluster/click-tidb-cluster.png)

3. 在**配置文件**选项卡，可以查看配置和下载 `values.yaml` 文件。点击**部署**以继续。

   ![download-yaml-file](/images/docs/zh-cn/appstore/external-apps/deploy-tidb-operator-and-cluster/download-yaml-file.png)

4. 在**基本信息**页面，确认应用名称、应用版本和部署位置。点击**下一步**以继续。

   ![tidb-cluster-info](/images/docs/zh-cn/appstore/external-apps/deploy-tidb-operator-and-cluster/tidb-cluster-info.png)

5. 一些 TiDB 组件需要[持久卷](../../../cluster-administration/persistent-volume-and-storage-class/)。您可以运行以下命令查看存储类型。

   ```
   / # kubectl get sc
   NAME                       PROVISIONER     RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
   csi-high-capacity-legacy   csi-qingcloud   Delete          Immediate           true                   71m
   csi-high-perf              csi-qingcloud   Delete          Immediate           true                   71m
   csi-ssd-enterprise         csi-qingcloud   Delete          Immediate           true                   71m
   csi-standard (default)     csi-qingcloud   Delete          Immediate           true                   71m
   csi-super-high-perf        csi-qingcloud   Delete          Immediate           true                   71m
   ```

6. 在**应用配置**页面，将字段 `storageClassName` 的默认值从 `local-storage` 更改为您的存储类型名称。例如，您可以根据以上输出将其更改为 `csi-qingcloud`。

   ![tidb-cluster-config](/images/docs/zh-cn/appstore/external-apps/deploy-tidb-operator-and-cluster/tidb-cluster-config.png)

   {{< notice note >}}

   此处仅更改字段 `storageClassName` 以提供外部持久化存储。若想在单个节点上部署每个 TiDB 组件（例如 [TiKV](https://docs.pingcap.com/tidb/dev/tidb-architecture#tikv-server) 和 [Placement Driver](https://docs.pingcap.com/tidb/dev/tidb-architecture#placement-driver-pd-server)），请指定 `nodeAffinity` 字段。

   {{</ notice >}} 

7. 点击**部署**，然后就可以在列表中看到如下所示的两个应用：

   ![tidb-cluster-app-running](/images/docs/zh-cn/appstore/external-apps/deploy-tidb-operator-and-cluster/tidb-cluster-app-running.png)

## 步骤 5：查看 TiDB 集群状态

1. 访问**应用负载**下的**工作负载**，确认所有的 TiDB 集群部署都在正常运行。

   ![tidb-cluster-deployments-running](/images/docs/appstore/external-apps/deploy-tidb-operator-and-cluster/tidb-cluster-deployments-running.PNG)

2. 切换到**有状态副本集**选项卡，可以看到 TiDB、TiKV 和 PD 均正常运行。

   ![tidb-statefulsets](/images/docs/appstore/external-apps/deploy-tidb-operator-and-cluster/tidb-statefulsets.PNG)

   {{< notice note >}}

   TiKV 和 TiDB 会自动创建，可能要过一段时间才能在列表中显示。

   {{</ notice >}}

3. 点击单个有状态副本集以访问其详情页。在**监控**选项卡下，可以看到一段时间内以折线图显示的指标。

   TiDB 指标：

   ![tidb-metrics](/images/docs/appstore/external-apps/deploy-tidb-operator-and-cluster/tidb-metrics.PNG)

   TiKV 指标：

   ![tikv-metrics](/images/docs/appstore/external-apps/deploy-tidb-operator-and-cluster/tikv-metrics.PNG)

   PD 指标：

   ![pd-metrics](/images/docs/appstore/external-apps/deploy-tidb-operator-and-cluster/pd-metrics.PNG)

4. 在**应用负载**下的**容器组**中，可以看到 TiDB 集群包含两个 TiDB Pod、三个 TiKV Pod 和三个 PD Pod。

   ![tidb-pod-list](/images/docs/appstore/external-apps/deploy-tidb-operator-and-cluster/tidb-pod-list.PNG)

5. 在**存储管理**下的**存储卷**中，可以看到 TiKV 和 PD 都在使用持久卷。

   ![tidb-storage-usage](/images/docs/appstore/external-apps/deploy-tidb-operator-and-cluster/tidb-storage-usage.PNG)

6. 同时，也会监控存储卷的使用情况。点击一个存储卷以访问其详情页。以 TiKV 为例：

   ![tikv-volume-status](/images/docs/appstore/external-apps/deploy-tidb-operator-and-cluster/tikv-volume-status.PNG)

7. 在项目的**概览**页面，可以看到当前项目的资源使用情况列表。

   ![tidb-project-resource-usage](/images/docs/appstore/external-apps/deploy-tidb-operator-and-cluster/tidb-project-resource-usage.PNG)

### 步骤 6: 访问 TiDB 集群

1. 访问**应用负载**下的**服务**，可以看到所有服务的详细信息。由于服务类型默认设置为 `NodePort`，因此您可以通过集群外部的 Node IP 地址进行访问。

   ![tidb-service](/images/docs/appstore/external-apps/deploy-tidb-operator-and-cluster/tidb-service.PNG)

3. TiDB 集成了 Prometheus 和 Grafana 以监控数据库集群的性能。例如，您可以通过 `{$ NodeIP}：{Nodeport}` 访问 Grafana 以查看指标。

   ![tidb-service-grafana](/images/docs/appstore/external-apps/deploy-tidb-operator-and-cluster/tidb-service-grafana.PNG)

   ![tidb-grafana](/images/docs/appstore/external-apps/deploy-tidb-operator-and-cluster/tidb-grafana.PNG)

   {{< notice note >}}

   根据 Kubernetes 集群部署位置的不同，您可能需要在安全组中打开端口，并配置相关的端口转发规则。

   {{</ notice >}}

