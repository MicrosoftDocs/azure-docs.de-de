---
title: Einrichten von Computezielen für das Training und für Rückschlüsse
titleSuffix: Azure Machine Learning
description: So fügen Sie Computeressourcen (Computeziele) zu Ihrem Arbeitsbereich hinzu, um damit Machine Learning-Modelle zu trainieren und Rückschlüsse zu ziehen
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 10/02/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: c25f3965775c6518629c92ccc371855d9178e648
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033713"
---
# <a name="set-up-compute-targets-for-model-training-and-deployment"></a>Einrichten von Computezielen für das Training und die Bereitstellung von Modellen

Hier erfahren Sie, wie Sie Azure-Computeressourcen an Ihren Azure Machine Learning-Arbeitsbereich anfügen.  Anschließend können Sie diese Ressourcen als [Computeziele](concept-compute-target.md) für Rückschlüsse und das Training in Ihren Machine Learning-Tasks verwenden.

In diesem Artikel erfahren Sie, wie Sie Ihren Arbeitsbereich für die Verwendung dieser Computeressourcen einrichten:

* Ihr lokaler Computer
* Virtuelle Remotecomputer
* Azure HDInsight
* Azure Batch
* Azure Databricks
* Azure Data Lake Analytics
* Azure Container Instances

Informationen zur Verwendung von Computezielen, die von Azure Machine Learning verwaltet werden, finden Sie unter den folgenden Links:


* [Azure Machine Learning-Computeinstanz](how-to-create-manage-compute-instance.md)
* [Azure Machine Learning Compute-Cluster](how-to-create-attach-compute-cluster.md)
* [Azure Kubernetes Service-Cluster](how-to-create-attach-kubernetes.md)

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).

* Die [Azure CLI-Erweiterung für Machine Learning Service](reference-azure-machine-learning-cli.md), das [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) oder die [Visual Studio Code-Erweiterung für Azure Machine Learning](tutorial-setup-vscode-extension.md).

## <a name="limitations"></a>Einschränkungen

* **Erstellen Sie nicht mehrere gleichzeitige Verknüpfungen für die gleichen Compute-Ressourcen** in Ihrem Arbeitsbereich. Verknüpfen Sie beispielsweise einen Azure Kubernetes Service-Clusters nicht unter zwei verschiedenen Namen mit einem Arbeitsbereich. Jede neue Verknüpfung führt zu einem Fehler der vorherigen vorhandenen Verknüpfungen.

    Falls Sie ein Computeziel erneut verknüpfen möchten (etwa zum Ändern der TLS-Einstellung oder einer anderen Clusterkonfigurationseinstellung), müssen Sie zunächst die vorhandene Verknüpfung entfernen.

## <a name="whats-a-compute-target"></a>Was ist ein Computeziel?

Mit Azure Machine Learning können Sie Ihr Modell für eine Vielzahl von Ressourcen oder Umgebungen trainieren, die zusammen als [__Computeziele__](concept-azure-machine-learning-architecture.md#compute-targets) bezeichnet werden. Ein Computeziel kann ein lokaler Computer oder eine Cloudressource sein, wie beispielsweise Azure Machine Learning Compute, Azure HDInsight oder ein virtueller Remotecomputer.  Ebenso können Sie Computeziele für die Modellimplementierung erstellen. Dies wird unter [Bereitstellen von Modellen mit Azure Machine Learning](how-to-deploy-and-where.md) beschrieben.


## <a name="local-computer"></a><a id="local"></a>Lokaler Computer

Wenn Sie Ihren lokalen Computer für das **Training** verwenden, ist es nicht erforderlich, ein Computeziel zu erstellen.  [Übermitteln Sie einfach die Trainingsausführung](how-to-set-up-training-targets.md) von Ihrem lokalen Computer aus.

Wenn Sie Ihren lokalen Computer für **Rückschlüsse** verwenden, muss Docker installiert sein. Zum Ausführen der Bereitstellung definieren Sie den vom Webdienst verwendeten Port in [LocalWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.local.localwebservice?preserve-view=true&view=azure-ml-py#deploy-configuration-port-none-). Folgen Sie dann dem normalen Bereitstellungsprozess, wie in [Bereitstellen von Modellen mit Azure Machine Learning](how-to-deploy-and-where.md) beschrieben.

## <a name="remote-virtual-machines"></a><a id="vm"></a>Virtuelle Remotecomputer

Azure Machine Learning bietet Ihnen auch die Möglichkeit, eine eigene Computeressource zu verwenden und diese an Ihren Arbeitsbereich anzufügen. Eine Remote-VM, auf die über Azure Machine Learning zugegriffen werden kann, ist ein solcher Ressourcentyp. Die Ressource kann entweder eine Azure-VM, ein Remoteserver in Ihrer Organisation oder lokal sein. Durch Angabe der IP-Adresse und Anmeldeinformationen (Benutzername und Kennwort oder SSH-Schlüssel) können Sie jede VM, auf die zugegriffen werden kann, für Remoteausführungen verwenden.

Sie können eine systemseitig erstellte Conda-Umgebung, eine bereits vorhandene Python-Umgebung oder einen Docker-Container verwenden. Zur Ausführung auf einem Docker-Container muss die Docker-Engine auf der VM ausgeführt werden. Diese Funktion ist besonders nützlich, wenn Sie eine cloudbasierte Entwicklungs-/Experimentierumgebung nutzen möchten, die Ihnen mehr Flexibilität bietet als Ihr lokaler Computer.

Verwenden Sie in diesem Szenario Azure Data Science Virtual Machine (DSVM) als virtuellen Azure-Computer. Diese VM ist eine vorkonfigurierte Data Science- und KI-Entwicklungsumgebung in Azure. Die VM bietet eine zusammengestellte Auswahl an Tools und Frameworks für die Entwicklung des maschinellen Lernens über den gesamten Lebenszyklus. Weitere Informationen zum Verwenden der DSVM mit Azure Machine Learning finden Sie unter [Konfigurieren einer Entwicklungsumgebung](./how-to-configure-environment.md#dsvm).

1. **Erstellen**: Erstellen Sie eine DSVM, bevor Sie si zum Trainieren Ihres Modells verwenden. Informationen zum Erstellen dieser Ressource finden Sie in [Bereitstellen der Data Science Virtual Machine für Linux (Ubuntu)](./data-science-virtual-machine/dsvm-ubuntu-intro.md).

    > [!WARNING]
    > Von Azure Machine Learning werden nur virtuelle Computer unterstützt, auf denen **Ubuntu** ausgeführt wird. Wenn Sie eine VM erstellen oder eine vorhandene VM auswählen, müssen Sie eine VM auswählen, die Ubuntu verwendet.
    > 
    > Von Azure Machine Learning wird außerdem vorausgesetzt, dass der virtuelle Computer über eine __öffentliche IP-Adresse__ verfügt.

1. **Anfügen**: Wenn Sie einen vorhandenen virtuellen Computer als Computeziel anfügen möchten, müssen Sie die Ressourcen-ID, den Benutzernamen und das Kennwort für den virtuellen Computer angeben. Die Ressourcen-ID des virtuellen Computers kann unter Verwendung der Abonnement-ID, des Ressourcengruppennamens und des Namens des virtuellen Computers im folgenden Zeichenfolgenformat erstellt werden: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`.

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Sie können die DSVM auch [über Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#attached-compute) an Ihren Arbeitsbereich anfügen.

    > [!WARNING]
    > Erstellen Sie nicht mehrere gleichzeitige Verknüpfungen für die gleichen DSVM in Ihrem Arbeitsbereich. Jede neue Verknüpfung führt zu einem Fehler der vorherigen vorhandenen Verknüpfungen.

1. **Konfigurieren**: Erstellen Sie eine Ausführungskonfiguration für das DSVM-Computeziel. Zum Erstellen und Konfigurieren der Trainingsumgebung in der DSVM werden Docker und Conda verwendet.

   ```python
   from azureml.core import ScriptRunConfig
   from azureml.core.environment import Environment
   from azureml.core.conda_dependencies import CondaDependencies
   
   # Create environment
   myenv = Environment(name="myenv")
   
   # Specify the conda dependencies
   myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
   
   # If no base image is explicitly specified the default CPU image "azureml.core.runconfig.DEFAULT_CPU_IMAGE" will be used
   # To use GPU in DSVM, you should specify the default GPU base Docker image or another GPU-enabled image:
   # myenv.docker.enabled = True
   # myenv.docker.base_image = azureml.core.runconfig.DEFAULT_GPU_IMAGE
   
   # Configure the run configuration with the Linux DSVM as the compute target and the environment defined above
   src = ScriptRunConfig(source_directory=".", script="train.py", compute_target=compute, environment=myenv) 
   ```

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight ist eine beliebte Plattform für Big Data-Analysen. Die Plattform stellt Apache Spark bereit, das zum Training Ihres Modells verwendet werden kann.

1. **Erstellen**:  Erstellen Sie den HDInsight-Cluster, bevor Sie ihn zum Training Ihres Modells verwenden. Informationen zum Erstellen von Spark in einem HDInsight-Cluster finden Sie unter [Erstellen eines Spark-Clusters in HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md). 

    > [!WARNING]
    > Von Azure Machine Learning wird vorausgesetzt, dass der HDInsight-Cluster über eine __öffentliche IP-Adresse__ verfügt.

    Beim Erstellen des Clusters müssen Sie einen SSH-Benutzernamen und ein Kennwort angeben. Notieren Sie diese Werte, da Sie sie benötigen, wenn Sie HDInsight als Computeziel verwenden.
    
    Stellen Sie nach der Erstellung des Clusters eine Verbindung mit dem Hostnamen „\<clustername>-ssh.azurehdinsight.net“ her, wobei \<clustername> der Name ist, den Sie für den Cluster angegeben haben. 

1. **Anfügen**: Wenn Sie einen HDInsight-Cluster als Computeziel anfügen möchten, müssen Sie den die Ressourcen-ID, den Benutzernamen und das Kennwort für den HDInsight-Cluster angeben. Die Ressourcen-ID des HDInsight-Clusters kann unter Verwendung der Abonnement-ID, des Ressourcengruppennamens und des Namens des HDInsight-Clusters im folgenden Zeichenfolgenformat erstellt werden: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`.

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   Sie können den HDInsight-Cluster auch [über Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#attached-compute) an Ihren Arbeitsbereich anfügen.

    > [!WARNING]
    > Erstellen Sie nicht mehrere gleichzeitige Verknüpfungen für den gleichen HDInsight in Ihrem Arbeitsbereich. Jede neue Verknüpfung führt zu einem Fehler der vorherigen vorhandenen Verknüpfungen.

1. **Konfigurieren**: Erstellen Sie eine Ausführungskonfiguration für das HDI-Computeziel. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Nachdem Sie nun die Computeressource angefügt und Ihre Ausführung konfiguriert haben, besteht der nächste Schritt im [Übermitteln der Trainingsausführung](how-to-set-up-training-targets.md).

## <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch wird verwendet, um umfangreiche, auf Parallelverarbeitung ausgelegte HPC-Anwendungen effizient in der Cloud auszuführen. AzureBatchStep kann in einer Azure Machine Learning-Pipeline verwendet werden, um Aufträge an einen Azure Batch Pool von Computern zu übermitteln.

Um Azure Batch als Computeziel anzufügen, müssen Sie das Azure Machine Learning SDK verwenden und die folgenden Informationen angeben:

-    **Azure Batch-Computename**: Ein Computeanzeigename, der innerhalb des Arbeitsbereichs verwendet wird.
-    **Azure Batch-Kontoname**: Der Name des Azure Batch-Kontos.
-    **Ressourcengruppe**: Die Ressourcengruppe, die das Azure Batch-Konto enthält.

Der folgende Code veranschaulicht, wie Azure Batch als Computeziel angefügt wird:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

> [!WARNING]
> Erstellen Sie nicht mehrere gleichzeitige Verknüpfungen für den gleichen Azure Batch in Ihrem Arbeitsbereich. Jede neue Verknüpfung führt zu einem Fehler der vorherigen vorhandenen Verknüpfungen.

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks ist eine Apache Spark-basierte Umgebung in der Azure-Cloud. Sie kann mit einer Azure Machine Learning-Pipeline als Computeziel verwendet werden.

Erstellen Sie vor der Verwendung einen Azure Databricks-Arbeitsbereich. Informationen zum Erstellen einer Arbeitsbereichsressource finden Sie im Dokument [Ausführen eines Spark-Auftrags in Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal).

Geben Sie zum Anfügen von Azure Databricks als Computeziel die folgenden Informationen an:

* __Databricks-Computename__: Der Name, der dieser Computeressource zugewiesen werden soll.
* __Name des Databricks-Arbeitsbereichs__: Der Name des Azure Databricks-Arbeitsbereichs.
* __Databricks-Zugriffstoken__: Das zur Authentifizierung bei Azure Databricks verwendete Zugriffstoken. Informationen zum Generieren eines Zugriffstokens finden Sie im Dokument [Authentifizierung](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html).

Der folgende Code veranschaulicht, wie Sie Azure Databricks als Computeziel mit dem Azure Machine Learning SDK anfügen (__der Databricks-Arbeitsbereich muss im gleichen Abonnement wie der AML-Arbeitsbereich vorhanden sein__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Ein ausführlicheres Beispiel finden Sie in einem [Beispiel-Notebook](https://aka.ms/pl-databricks) auf GitHub.

> [!WARNING]
> Erstellen Sie nicht mehrere gleichzeitige Verknüpfungen für den gleichen Azure Databricks in Ihrem Arbeitsbereich. Jede neue Verknüpfung führt zu einem Fehler der vorherigen vorhandenen Verknüpfungen.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics ist eine umfangreiche Datenanalyseplattform in der Azure-Cloud. Sie kann mit einer Azure Machine Learning-Pipeline als Computeziel verwendet werden.

Erstellen Sie vor der Verwendung ein Azure Data Lake Analytics-Konto. Informationen zum Erstellen dieser Ressource finden Sie im Dokument [Erste Schritte mit Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Um Data Lake Analytics als Computeziel anzufügen, müssen Sie das Azure Machine Learning SDK verwenden und die folgenden Informationen angeben:

* __Computename__: Der Name, der dieser Computeressource zugewiesen werden soll.
* __Ressourcengruppe__: Die Ressourcengruppe, die das Data Lake Analytics-Konto enthält.
* __Kontoname__: Der Name des Data Lake Analytics-Kontos.

Der folgende Code veranschaulicht, wie Data Lake Analytics als Computeziel angefügt wird:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Ein ausführlicheres Beispiel finden Sie in einem [Beispiel-Notebook](https://aka.ms/pl-adla) auf GitHub.

> [!WARNING]
> Erstellen Sie nicht mehrere gleichzeitige Verknüpfungen für das gleiche ADLA in Ihrem Arbeitsbereich. Jede neue Verknüpfung führt zu einem Fehler der vorherigen vorhandenen Verknüpfungen.

> [!TIP]
> Azure Machine Learning-Pipelines können nur ausgeführt werden, wenn die Daten im Standarddatenspeicher des Data Lake Analytics-Kontos gespeichert werden. Wenn die Daten, die Sie verwenden möchten, in einem nicht standardmäßigen Speicher gespeichert sind, können Sie die Daten mithilfe von [`DataTransferStep`](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?preserve-view=true&view=azure-ml-py) vor dem Trainieren kopieren.

## <a name="azure-container-instance"></a><a id="aci"></a>Azure Container Instances

Azure Container Instances (ACI) werden dynamisch erstellt, wenn Sie ein Modell bereitstellen. Es gibt keine andere Möglichkeit, ACI zu erstellen und an Ihren Arbeitsbereich anzufügen. Weitere Informationen finden Sie unter [Bereitstellen eines Modells in Azure Container Instances](how-to-deploy-azure-container-instance.md).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Azure Kubernetes Service (AKS) unterstützt bei Verwendung mit Azure Machine Learning eine Vielzahl von Konfigurationsoptionen. Weitere Informationen finden Sie unter [How to create and attach Azure Kubernetes Service](how-to-create-attach-kubernetes.md) (Erstellen und Anfügen des Azure Kubernetes Service).


## <a name="notebook-examples"></a>Notebook-Beispiele

Beispiele für das Training mit verschiedenen Computeziele finden Sie in diesen Notebooks:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie die Computeressource zum [Konfigurieren und Übermitteln einer Trainingsausführung](how-to-set-up-training-targets.md).
* [Tutorial: Trainieren eines Modells](tutorial-train-models-with-aml.md) verwendet ein verwaltetes Computeziel zum Trainieren eines Modells.
* Erfahren Sie, wie [Hyperparameter optimiert werden](how-to-tune-hyperparameters.md), um bessere Modelle zu erstellen.
* Erfahren Sie nach der Erstellung eines trainierten Modells, [wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md).
* [Verwenden von Azure Machine Learning mit virtuellen Azure-Netzwerken](./how-to-network-security-overview.md)