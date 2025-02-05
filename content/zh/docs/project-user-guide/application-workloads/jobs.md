---
title: 任务
keywords: "KubeSphere, Kubernetes, docker, 任务"
description: "创建 Kubernetes 任务"
linkTitle: "任务"

weight: 10250
---

任务会创建一个或者多个 Pod，并确保指定数量的 Pod 成功结束。随着 Pod 成功结束，任务跟踪记录成功结束的 Pod 数量。当达到指定的成功结束数量时，任务（即 Job）完成。删除任务的操作会清除其创建的全部 Pod。

在简单的使用场景中，您可以创建一个任务对象，以便可靠地运行一个 Pod 直到结束。当第一个 Pod 故障或者被删除（例如因为节点硬件故障或者节点重启）时，任务对象会启动一个新的 Pod。您也可以使用一个任务并行运行多个 Pod。

下面的示例演示了在 KubeSphere 中创建任务的具体步骤，该任务会计算 π 到小数点后 2000 位。 

## 准备工作

您需要创建一个企业空间、一个项目和一个帐户 (`project-regular`)，务必邀请该帐户到项目中并赋予 `operator` 角色。有关更多信息，请参见[创建企业空间、项目、帐户和角色](../../../quick-start/create-workspace-and-project/)。

## 创建任务

### 步骤 1：打开仪表板

以 `project-regular` 身份登录控制台。转到**应用负载**下的**任务**，点击**创建**。

![创建任务](/images/docs/zh-cn/project-user-guide/application-workloads/jobs/create-job.PNG)

### 步骤 2：输入基本信息

输入基本信息。请参考下图作为示例。

- **名称**：任务的名称，也是唯一标识符。
- **别名**：任务的别名，使资源易于识别。
- **描述信息**：任务的描述，简要介绍任务。

![输入基本信息](/images/docs/zh-cn/project-user-guide/application-workloads/jobs/job-create-basic-info.PNG)

### 步骤 3：任务设置（可选）

您可以参照下图在该步骤设置值，或点击**下一步**以使用默认值。有关每个字段的详细说明，请参考下表。

![任务设置](/images/docs/zh-cn/project-user-guide/application-workloads/jobs/job-create-job-settings.PNG)

| 名称                   | 定义                         | 描述信息                                                     |
| ---------------------- | ---------------------------- | ------------------------------------------------------------ |
| 最大重试次数           | `spec.backoffLimit`          | 指定将该任务视为失败之前的重试次数。默认值为 6。             |
| 完成数                 | `spec.completions`           | 指定该任务应该运行至成功结束的 Pod 的期望数量。如果设置为 nil，则意味着任何 Pod 成功结束即标志着所有 Pod 成功结束，并且允许并行数为任何正数值。如果设置为 1，则意味着并行数限制为 1，并且该 Pod 成功结束标志着任务成功完成。有关更多信息，请参见 [Jobs](https://kubernetes.io/zh/docs/concepts/workloads/controllers/job/)。 |
| 并行数                 | `spec.parallelism`           | 指定该任务在任何给定时间应该运行的最大期望 Pod 数量。当剩余工作小于最大并行数时 ((`.spec.completions - .status.successful`) < `.spec.parallelism`)，实际稳定运行的 Pod 数量会小于该值。有关更多信息，请参见 [Jobs](https://kubernetes.io/zh/docs/concepts/workloads/controllers/job/)。 |
| 退出超时时限(单位：秒) | `spec.activeDeadlineSeconds` | 指定该任务在系统尝试终止任务前处于运行状态的持续时间（相对于 stratTime），单位为秒；该值必须是正整数。 |

### 步骤 4：设置镜像

1. **重启策略**选择 **Never**。当任务未完成时，您只能将**重启策略**指定为 **Never** 或 **OnFailure**：

   - 如果将**重启策略**设置为 **Never**，当 Pod 发生故障时，任务将创建一个新的 Pod，并且故障的 Pod 不会消失。

   - 如果将**重启策略**设置为 **OnFailure**，当 Pod 发生故障时，任务会在内部重启容器，而不是创建新的 Pod。

    ![设置镜像](/images/docs/zh-cn/project-user-guide/application-workloads/jobs/job-container-settings.PNG)

2. 点击**添加容器镜像**，它将引导您进入**添加容器**页面。在镜像搜索栏中输入 `perl`，然后按**回车**键。

    ![添加镜像](/images/docs/zh-cn/project-user-guide/application-workloads/jobs/add-container-image-job.PNG)

3. 在该页面向下滚动到**启动命令**。在命令框中输入以下命令，计算 pi 到小数点后 2000 位并输出结果。点击右下角的 **√**，然后选择**下一步**继续。

    ```bash
    perl,-Mbignum=bpi,-wle,print bpi(2000)
    ```

    ![启动命令](/images/docs/zh-cn/project-user-guide/application-workloads/jobs/start-command-job.PNG)

    {{< notice note >}}有关设置镜像的更多信息，请参见[容器镜像设置](../../../project-user-guide/application-workloads/container-image-settings/)。{{</ notice >}}

### 步骤 5：检查任务清单（可选）

1. 在右上角启用**编辑模式**，显示任务的清单文件。您可以看到所有值都是根据先前步骤中指定的值而设置。

    ```yaml
    apiVersion: batch/v1
    kind: Job
    metadata:
      namespace: cc
      labels:
        app: job-test-1
      name: job-test-1
      annotations:
        kubesphere.io/alias-name: Test
        kubesphere.io/description: A job test
    spec:
      template:
        metadata:
          labels:
            app: job-test-1
          annotations:
            kubesphere.io/containerSecrets: null
        spec:
          containers:
            - name: container-xv4p2o
              imagePullPolicy: IfNotPresent
              image: perl
              command:
                - perl
                - '-Mbignum=bpi'
                - '-wle'
                - print bpi(2000)
          restartPolicy: Never
          serviceAccount: default
          initContainers: []
          volumes: []
          imagePullSecrets: null
      backoffLimit: 5
      parallelism: 2
      completions: 4
      activeDeadlineSeconds: 300
    ```

2. 您可以直接在清单文件中进行调整，然后点击**创建**，或者关闭**编辑模式**然后返回**创建任务**页面。

    {{< notice note >}}您可以跳过本教程的**挂载存储**和**高级设置**。有关更多信息，请参见[挂载存储卷](../../../project-user-guide/application-workloads/deployments/#步骤-4挂载存储卷)和[配置高级设置](../../../project-user-guide/application-workloads/deployments/#步骤-5配置高级设置)。{{</ notice >}}

### 步骤 6：检查结果

1. 在最后一步**高级设置**中，点击**创建**完成操作。如果创建成功，将添加新条目到任务列表中。

    ![任务列表](/images/docs/zh-cn/project-user-guide/application-workloads/jobs/job-list-new.PNG)

2. 点击此任务，然后转到**执行记录**选项卡，您可以在其中查看每个执行记录的信息。先前在步骤 3 中**完成数**设置为 `4`，因此有四个已结束的 Pod。

    ![执行记录](/images/docs/zh-cn/project-user-guide/application-workloads/jobs/execution-record.PNG)

    {{< notice tip >}}如果任务失败，您可以重新运行该任务，失败原因显示在**消息**下。{{</ notice >}}

3. 在**资源状态**中，您可以查看 Pod 状态。先前将**并行数**设置为 2，因此每次会创建两个 Pod。点击右侧的箭头，查看容器日志，如下所示，该日志显示了预期的计算结果。

    ![容器日志](/images/docs/zh-cn/project-user-guide/application-workloads/jobs/container-log.PNG)

    ![查看容器日志](/images/docs/zh-cn/project-user-guide/application-workloads/jobs/container-log-check.jpg)

    {{< notice tip >}}

- 在**资源状态**中，Pod 列表提供了 Pod 的详细信息（例如创建时间、节点、Pod IP 和监控数据）。
- 您可以点击 Pod 查看容器信息。
- 点击容器日志图标查看容器的输出日志。
- 您可以点击 Pod 名称查看 Pod 详情页面。

    {{</ notice >}}

## 查看任务详情

### 任务操作

在任务详情页面上，您可以在任务创建后对其进行管理。

- **编辑信息**：编辑基本信息，但`名称`无法编辑。
- **重新执行**：重新执行任务，Pod 将重启，并生成新的执行记录。
- **查看配置文件**：查看 YAML 格式的任务规格。
- **删除**：删除该任务并返回到任务列表页面。

![任务操作](/images/docs/zh-cn/project-user-guide/application-workloads/jobs/job-operation.PNG)

### 执行记录

1. 点击**执行记录**选项卡查看任务的执行记录。

   ![execution-records](/images/docs/zh-cn/project-user-guide/application-workloads/jobs/execution-records.png)

2. 点击 <img src="/images/docs/zh-cn/project-user-guide/application-workloads/jobs/refresh.png" width="20px" /> 刷新执行记录。

### 资源状态

1. 点击**资源状态**选项卡查看任务的容器组。

   ![resource-status](/images/docs/zh-cn/project-user-guide/application-workloads/jobs/resource-status.png)

2. 点击 <img src="/images/docs/zh-cn/project-user-guide/application-workloads/jobs/refresh.png" width="20px" /> 刷新 Pod 信息，点击 <img src="/images/docs/zh-cn/project-user-guide/application-workloads/jobs/display.png" width="20px" />/<img src="/images/docs/zh-cn/project-user-guide/application-workloads/jobs/hide.png" width="20px" /> 显示或隐藏每个 Pod 中的容器。

### 元数据

点击**元数据**选项卡查看任务的标签和注解。

![metadata](/images/docs/zh-cn/project-user-guide/application-workloads/jobs/metadata.png)

### 环境变量

点击**环境变量**选项卡查看任务的环境变量。

![env-variable](/images/docs/zh-cn/project-user-guide/application-workloads/jobs/env-variable.png)

### 事件

点击**事件**选项卡查看任务的事件。

![events](/images/docs/zh-cn/project-user-guide/application-workloads/jobs/events.png)
