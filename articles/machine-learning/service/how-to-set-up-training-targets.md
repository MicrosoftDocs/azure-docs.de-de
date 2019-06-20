---
title: Erstellen und Verwenden von Computezielen für das Modelltraining
titleSuffix: Azure Machine Learning service
description: Konfigurieren Sie die Trainingsumgebungen (Computeziele) für das Machine Learning-Modelltraining. Sie können problemlos zwischen Trainingsumgebungen wechseln. Beginnen Sie das Training lokal. Wenn ein horizontales Hochskalieren erforderlich ist, wechseln Sie zu einem cloudbasierten Computeziel.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/07/2019
ms.custom: seodec18
ms.openlocfilehash: 054aaf6f607bba216f979665a0b0672ec253ba7f
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475985"
---
# <a name="set-up-compute-targets-for-model-training"></a>Einrichten von Computezielen für das Modelltraining 

Mit Azure Machine Learning Service können Sie Ihr Modell für eine Vielzahl von Ressourcen oder Umgebungen trainieren, die zusammen als [__Computeziele__](concept-azure-machine-learning-architecture.md#compute-target) bezeichnet werden. Ein Computeziel kann ein lokaler Computer oder eine Cloudressource sein, wie beispielsweise Azure Machine Learning Compute, Azure HDInsight oder ein virtueller Remotecomputer.  Sie können auch Computeziele für die Modellimplementierung erstellen, wie in [Bereitstellen von Modellen mit dem Azure Machine Learning-Dienst](how-to-deploy-and-where.md) beschrieben.

Sie können Computeziele mit dem Azure Machine Learning SDK, im Azure-Portal, über die Azure-Befehlszeilenschnittstelle (Azure CLI) oder die Azure Machine Learning- VS Code-Erweiterung erstellen und verwalten. Wenn Sie Computeziele haben, die über einen anderen Dienst (z. B. einen HDInsight-Cluster) erstellt wurden, können Sie diese an Ihren Azure Machine Learning Service-Arbeitsbereich anfügen, um sie verwenden zu können.
 
In diesem Artikel erfahren Sie, wie Sie verschiedene Computeziele für das Modelltraining verwenden.  Die Schritte für alle Computeziele führen Sie den gleichen Workflow:
1. __Erstellen__ Sie ein Computeziel, wenn noch keines vorhanden ist.
2. __Fügen__ Sie das Computeziel zu Ihrem Arbeitsbereich hinzu.
3. __Konfigurieren__ Sie das Computeziel, sodass es die von Ihren Skript benötigte Python-Umgebung und Paketabhängigkeiten enthält.


>[!NOTE]
> Der Code in diesem Artikel wurde mit Version 1.0.39 des Azure Machine Learning SDK getestet.

## <a name="compute-targets-for-training"></a>Computeziele für das Training

Azure Machine Learning Service bietet unterschiedliche Unterstützung für die verschiedenen Computeziele. Ein typischer Modellentwicklungslebenszyklus beginnt mit der Entwicklung/Experimenten mit einer kleinen Menge von Daten. In dieser Phase empfehlen wir die Verwendung einer lokalen Umgebung. Verwenden Sie beispielsweise Ihren lokalen Computer oder eine cloudbasierte VM. Wenn Sie Ihr Training zur Verwendung größerer Datasets zentral hochskalieren oder sich für ein verteiltes Training entscheiden, empfehlen wir, mit Azure Machine Learning Compute einen Cluster mit einem einzelnen oder mehreren Knoten zu erstellen, der bei jeder Übermittlung einer Ausführung automatisch skaliert wird. Sie können auch Ihre eigene Computeressource anfügen. Die Unterstützung für verschiedene Szenarien variiert jedoch wie unten beschrieben:


|Training &nbsp;Ziele| GPU-Unterstützung |Automatisiertes maschinelles Lernen | ML-Pipelines | Grafische Benutzeroberfläche
|----|:----:|:----:|:----:|:----:|
|[Lokaler Computer](#local)| Vielleicht | Ja | &nbsp; | &nbsp; |
|[Azure Machine Learning Compute](#amlcompute)| Ja | Ja & <br/>Hyperparameteroptimierung&nbsp; | Ja | Ja |
|[Remote-VM](#vm) |Ja | Ja & <br/>Hyperparameteroptimierung | Ja | &nbsp; |
|[Azure&nbsp;Databricks](how-to-create-your-first-pipeline.md#databricks)| &nbsp; | Ja | Ja | &nbsp; |
|[Azure Data Lake Analytics](how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | Ja | &nbsp; |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | Ja | &nbsp; |
|[Azure Batch](#azbatch)| &nbsp; | &nbsp; | Ja | &nbsp; |

**Alle Computeziele können für mehrere Trainingsaufträge wiederverwendet werden**. Beispielsweise können Sie einen virtuellen Remotecomputer, den Sie an Ihren Arbeitsbereich anfügen, für mehrere Aufträge wiederverwenden.

> [!NOTE]
> Azure Machine Learning Compute kann als dauerhafte Ressource oder dynamisch beim Anfordern einer Ausführung erstellt werden. Bei der ausführungsbasierten Erstellung wird das Computeziel nach Abschluss des Trainingsauftrags entfernt, sodass Sie auf diese Weise erstellte Computeziele nicht wiederverwenden können.

## <a name="whats-a-run-configuration"></a>Was ist eine Laufzeitkonfiguration?

Beim Training ist es üblich, auf dem lokalen Computer zu starten und dieses Trainingsskript später auf einem anderen Computeziel auszuführen. Mit Azure Machine Learning Service können Sie Ihr Skript auf unterschiedlichen Computezielen ausführen, ohne das Skript zu ändern. 

Sie müssen lediglich die Umgebung für jedes einzelne Computeziel mit einer **Laufzeitkonfiguration** definieren.  Wenn Sie Ihr Trainingsexperiment auf einem anderen Computeziel ausführen möchten, geben Sie die Laufzeitkonfiguration für diese Computeressource an. 

Mehr über das [Übermitteln von Experimenten](#submit) finden Sie am Ende dieses Artikels.

### <a name="manage-environment-and-dependencies"></a>Verwalten der Umgebung und von Abhängigkeiten

Wenn Sie eine Laufzeitkonfiguration erstellen, müssen Sie entscheiden, wie Sie die Umgebung und die Abhängigkeiten des Computeziels verwalten. 

#### <a name="system-managed-environment"></a>Vom System verwaltete Umgebung

Verwenden Sie eine vom System verwaltete Umgebung, wenn Sie möchten, dass [Conda](https://conda.io/docs/) die Python-Umgebung und die Skriptabhängigkeiten für Sie verwaltet. Eine vom System verwaltete Umgebung wird standardmäßig angenommen und ist die häufigste Wahl. Dies ist hilfreich für Remotecomputeziele, insbesondere, wenn Sie dieses Ziel nicht konfigurieren können. 

Alles, was Sie tun müssen, ist, jede Paketabhängigkeit mit der [CondaDependency-Klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) anzugeben. Dann erstellt Conda eine Datei namens **conda_dependencies.yml** im Verzeichnis **aml_config** in Ihrem Arbeitsbereich mit Ihrer Liste der Paketabhängigkeiten und richtet Ihre Python-Umgebung ein, wenn Sie Ihr Trainingsexperiment übermitteln. 

Die erstmalige Einrichtung einer neuen Umgebung kann einige Minuten dauern, je nach der Größe der erforderlichen Abhängigkeiten. Solange die Liste der Pakete unverändert bleibt, erfolgt die Einrichtung nur einmal.
  
Der folgende Code zeigt ein Beispiel für eine vom System verwaltete Umgebung, die „SciKit-learn“ erfordert:
    
[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_system_managed)]

#### <a name="user-managed-environment"></a>Vom Benutzer verwaltete Umgebung

Bei benutzerverwalteten Umgebungen sind Sie für die Einrichtung Ihrer Umgebung und die Installation aller Pakete, die Ihr Trainingsskript auf dem Computeziel benötigt, selbst verantwortlich. Wenn Ihre Trainingsumgebung bereits konfiguriert ist (z.B. auf Ihrem lokalen Computer), können Sie den Einrichtungsschritt überspringen, indem Sie `user_managed_dependencies` auf TRUE festlegen. Conda wird Ihre Umgebung nicht überprüfen oder Installationen für Sie vornehmen.

Der folgende Code ist ein Beispiel für die Trainingsausführungen für eine vom Benutzer verwaltete Umgebung:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_user_managed)]
  
## <a name="set-up-compute-targets-with-python"></a>Einrichten von Computezielen mit Python

Konfigurieren Sie die Computeziele anhand der folgenden Abschnitte:

* [Lokaler Computer](#local)
* [Azure Machine Learning Compute](#amlcompute)
* [Virtuelle Remotecomputer](#vm)
* [Azure HDInsight](#hdinsight)


### <a id="local"></a>Lokaler Computer

1. **Erstellen und Anfügen**: Es ist nicht erforderlich, ein Computeziel zu erstellen oder anzufügen, um Ihren lokalen Computer als Trainingsumgebung zu verwenden.  

1. **Konfigurieren**:  Wenn Sie Ihren lokalen Computer als Computeziel verwenden, wird der Trainingscode in Ihrer [Entwicklungsumgebung](how-to-configure-environment.md) ausgeführt.  Wenn diese Umgebung bereits über die von Ihnen benötigten Python-Pakete verfügt, verwenden Sie die vom Benutzer verwaltete Umgebung.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Nachdem Sie nun die Computeressource angefügt und Ihre Ausführung konfiguriert haben, besteht der nächste Schritt im [Übermitteln der Trainingsläufe](#submit).

### <a id="amlcompute"></a>Azure Machine Learning Compute

Azure Machine Learning Compute ist eine verwaltete Compute-Infrastruktur, die dem Benutzer das einfache Erstellen von Computezielen mit einem oder mehreren Knoten ermöglicht. Das Computeziel wird in Ihrer Arbeitsbereichsregion als Ressource erstellt, die für andere Benutzer im Arbeitsbereich freigegeben werden kann. Es wird automatisch zentral hochskaliert, wenn ein Auftrag übermittelt wird, und kann in einem virtuellen Azure-Netzwerk platziert werden kann. Das Computeziel wird in einer Containerumgebung ausgeführt und packt die Abhängigkeiten Ihres Modells in einem [Docker-Container](https://www.docker.com/why-docker).

Sie können Azure Machine Learning Compute verwenden, um den Trainingsprozess auf einen Cluster von CPU- oder GPU-Computeknoten in der Cloud zu verteilen. Weitere Informationen zu den VM-Größen mit GPUs finden Sie unter [Für GPU optimierte VM-Größen](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Bei Azure Machine Learning Compute gelten Standardgrenzwerte, beispielsweise für die Anzahl von Kernen, die zugeordnet werden können. Weitere Informationen finden Sie unter [Verwalten und Anfordern von Kontingenten für Azure-Ressourcen](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).


Sie können eine Azure Machine Learning Compute-Umgebung bedarfsgesteuert beim Planen einer Ausführung oder als persistente Ressource erstellen.

#### <a name="run-based-creation"></a>Ausführungsbasierte Erstellung

Sie können eine Azure Machine Learning Compute-Umgebung als Computeziel zur Laufzeit erstellen. Das Computeziel wird automatisch für Ihre Ausführung erstellt. Das Computeziel wird nach Abschluss der Ausführung automatisch gelöscht. 

> [!NOTE]
> Zum Festlegen einer maximalen Anzahl der zu verwendenden Knoten würden Sie normalerweise `node_count` auf die jeweilige Anzahl von Knoten festlegen. Derzeit (4.4.2019) gibt es einen Fehler, der diese Funktionsweise verhindert. Verwenden Sie die `amlcompute._cluster_max_node_count`-Eigenschaft der Ausführungskonfiguration, um dieses Problem zu umgehen. Beispiel: `run_config.amlcompute._cluster_max_node_count = 5`.

> [!IMPORTANT]
> Die ausführungsbasierte Erstellung von Azure Machine Learning Compute ist zurzeit als Vorschauversion verfügbar. Verwenden Sie die ausführungsbasierte Erstellung nicht, wenn Sie die Hyperparameteroptimierung oder automatisiertes maschinelles Lernen nutzen. Um Hyperparameteroptimierung oder automatisches maschinelles Lernen zu verwenden, erstellen Sie stattdessen ein [persistentes Computeziel](#persistent).

1.  **Erstellen, Anfügen und Konfigurieren**: Die ausführungsbasierte Erstellung führt alle notwendigen Schritte aus, um das Computeziel mit der Laufzeitkonfiguration zu erstellen, anzufügen und zu konfigurieren.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Nachdem Sie nun die Computeressource angefügt und Ihre Ausführung konfiguriert haben, besteht der nächste Schritt im [Übermitteln der Trainingsläufe](#submit).

#### <a id="persistent"></a>Persistente Computeressource

Eine persistente Azure Machine Learning Compute-Ressource kann für mehrere Aufträge wiederverwendet werden. Sie kann für andere Benutzer im Arbeitsbereich freigegeben werden und bleibt zwischen Aufträgen erhalten.

1. **Erstellen und Anfügen**: Um eine persistente Azure Machine Learning Compute-Ressource in Python zu erstellen, geben Sie die Eigenschaften **vm_size** und **max_nodes** an. Azure Machine Learning verwendet dann für die restlichen Eigenschaften intelligente Standardwerte. Die Computeressource skaliert automatisch auf Null herunter, wenn sie nicht benötigt wird.   Dedizierte VMs werden erstellt, um Ihre Aufträge bei Bedarf auszuführen.
    
    * **vm_size**: Die VM-Familie der von Azure Machine Learning Compute erstellten Knoten.
    * **max_nodes**: Die maximale Anzahl von Knoten, auf die das Computeziel bei der Ausführung eines Auftrags in Azure Machine Learning Compute automatisch hochskaliert wird.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Sie können beim Erstellen von Azure Machine Learning Compute auch mehrere erweiterte Eigenschaften konfigurieren. Mithilfe der Eigenschaften können Sie einen persistenten Cluster mit einer festen Größe oder in einem vorhandenen virtuellen Azure-Netzwerk in Ihrem Abonnement erstellen.  Weitere Informationen finden Sie in der [AmlCompute-Klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ).
    
   Oder Sie können eine persistente Azure Machine Learning Compute-Ressource [ im Azure Portal](#portal-create) erstellen und anfügen.

1. **Konfigurieren**: Erstellen Sie eine Ausführungskonfiguration für das persistente Computeziel.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Nachdem Sie nun die Computeressource angefügt und Ihre Ausführung konfiguriert haben, besteht der nächste Schritt im [Übermitteln der Trainingsläufe](#submit).


### <a id="vm"></a>Virtuelle Remotecomputer

Azure Machine Learning bietet Ihnen auch die Möglichkeit, eine eigene Computeressource zu verwenden und diese an Ihren Arbeitsbereich anzufügen. Eine Remote-VM, auf die über Azure Machine Learning Service zugegriffen werden kann, ist ein solcher Ressourcentyp. Die Ressource kann entweder eine Azure-VM, ein Remoteserver in Ihrer Organisation oder lokal sein. Durch Angabe der IP-Adresse und Anmeldeinformationen (Benutzername und Kennwort oder SSH-Schlüssel) können Sie jede VM, auf die zugegriffen werden kann, für Remoteausführungen verwenden.

Sie können eine systemseitig erstellte Conda-Umgebung, eine bereits vorhandene Python-Umgebung oder einen Docker-Container verwenden. Zur Ausführung auf einem Docker-Container muss die Docker-Engine auf der VM ausgeführt werden. Diese Funktion ist besonders nützlich, wenn Sie eine cloudbasierte Entwicklungs-/Experimentierumgebung nutzen möchten, die Ihnen mehr Flexibilität bietet als Ihr lokaler Computer.

Verwenden Sie in diesem Szenario die Data Science Virtual Machine (DSVM) als Azure-VM. Diese VM ist eine vorkonfigurierte Data Science- und KI-Entwicklungsumgebung in Azure. Die VM bietet eine zusammengestellte Auswahl an Tools und Frameworks für die Entwicklung des maschinellen Lernens über den gesamten Lebenszyklus. Weitere Informationen zum Verwenden der DSVM mit Azure Machine Learning finden Sie unter [Konfigurieren einer Entwicklungsumgebung](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

1. **Erstellen**: Erstellen Sie eine DSVM, bevor Sie si zum Trainieren Ihres Modells verwenden. Informationen zum Erstellen dieser Ressource finden Sie in [Bereitstellen der Data Science Virtual Machine für Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Azure Machine Learning unterstützt nur virtuelle Computer, auf denen Ubuntu ausgeführt wird. Wenn Sie eine VM erstellen oder eine vorhandene VM auswählen, müssen Sie eine VM auswählen, die Ubuntu verwendet.

1. **Anfügen**: Um einen vorhandenen virtuellen Computer als Computeziel anzufügen, müssen Sie den vollqualifizierten Domänennamen (FQDN), Benutzernamen und das Kennwort für die VM bereitstellen. Ersetzen Sie in diesem Beispiel \<fqdn> durch den öffentlichen FQDN der VM oder die öffentliche IP-Adresse. Ersetzen Sie im folgenden Befehl \<username> und \<password> durch den SSH-Benutzernamen und das Kennwort für die VM.

   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   attach_config = RemoteCompute.attach_configuration(address = "<fqdn>",
                                                    ssh_port=22,
                                                    username='<username>',
                                                    password="<password>")

   # If you authenticate with SSH keys instead, use this code:
   #                                                  ssh_port=22,
   #                                                  username='<username>',
   #                                                  password=None,
   #                                                  private_key_file="<path-to-file>",
   #                                                  private_key_passphrase="<passphrase>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Oder Sie können die DSVM [über das Azure-Portal](#portal-reuse) an Ihren Arbeitsbereich anfügen.

1. **Konfigurieren**: Erstellen Sie eine Ausführungskonfiguration für das DSVM-Computeziel. Zum Erstellen und Konfigurieren der Trainingsumgebung in der DSVM werden Docker und Conda verwendet.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Nachdem Sie nun die Computeressource angefügt und Ihre Ausführung konfiguriert haben, besteht der nächste Schritt im [Übermitteln der Trainingsläufe](#submit).

### <a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight ist eine beliebte Plattform für Big Data-Analysen. Die Plattform stellt Apache Spark bereit, das zum Training Ihres Modells verwendet werden kann.

1. **Erstellen**:  Erstellen Sie den HDInsight-Cluster, bevor Sie ihn zum Training Ihres Modells verwenden. Informationen zum Erstellen von Spark in einem HDInsight-Cluster finden Sie unter [Erstellen eines Spark-Clusters in HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    Beim Erstellen des Clusters müssen Sie einen SSH-Benutzernamen und ein Kennwort angeben. Notieren Sie diese Werte, da Sie sie benötigen, wenn Sie HDInsight als Computeziel verwenden.
    
    Nach Erstellen des Clusters stellen Sie eine Verbindung mit dem Hostnamen \<clustername>-ssh.azurehdinsight.net her, wobei \<clustername> der Name ist, den Sie für den Cluster angegeben haben. 

1. **Anfügen**: Um einen HDInsight-Cluster als Computeziel anzufügen, müssen Sie den Hostnamen, Benutzernamen und das Kennwort für den HDInsight-Cluster bereitstellen. Im folgenden Beispiel wird das SDK zum Anfügen eines Clusters zu Ihrem Arbeitsbereich verwendet. Ersetzen Sie in diesem Beispiel \<clustername> durch den Namen Ihres Clusters. Ersetzen Sie \<username> und \<password> durch den SSH-Benutzernamen und das Kennwort für den Cluster.

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase
    attach_config = HDInsightCompute.attach_configuration(address='<clustername>-ssh.azureinsight.net', 
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

   Oder Sie fügen den HDInsight-Cluster [über das Azure-Portal](#portal-reuse) an Ihren Arbeitsbereich an.

1. **Konfigurieren**: Erstellen Sie eine Ausführungskonfiguration für das HDI-Computeziel. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Nachdem Sie nun die Computeressource angefügt und Ihre Ausführung konfiguriert haben, besteht der nächste Schritt im [Übermitteln der Trainingsläufe](#submit).


### <a id="azbatch"></a>Azure Batch 

Azure Batch wird verwendet, um umfangreiche, auf Parallelverarbeitung ausgelegte HPC-Anwendungen effizient in der Cloud auszuführen. AzureBatchStep kann in einer Azure Machine Learning-Pipeline verwendet werden, um Aufträge an einen Azure Batch Pool von Computern zu übermitteln.

Um Azure Batch als Computeziel anzufügen, müssen Sie das Azure Machine Learning SDK verwenden und die folgenden Informationen angeben:

-   **Azure Batch-Computename**: Ein Computeanzeigename, der innerhalb des Arbeitsbereichs verwendet wird.
-   **Azure Batch-Kontoname**: Der Name des Azure Batch-Kontos.
-   **Ressourcengruppe**: Die Ressourcengruppe, die das Azure Batch-Konto enthält.

Der folgende Code veranschaulicht, wie Azure Batch als Computeziel angefügt wird:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

batch_compute_name = 'mybatchcompute' # Name to associate with new compute in workspace

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>" # Name of the Batch account
batch_resource_group = "<batch_resource_group>" # Name of the resource group which contains this account

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

## <a name="set-up-compute-in-the-azure-portal"></a>Einrichten von Computezielen im Azure-Portal

Sie können im Azure-Portal auf Computeziele zugreifen, die Ihrem Arbeitsbereich zugeordnet sind.  Das Portal bietet Ihnen folgende Möglichkeiten:

* [Anzeigen von Computezielen](#portal-view), die an Ihren Arbeitsbereich angefügt sind
* [Erstellen eines Computeziels](#portal-create) in Ihrem Arbeitsbereich
* [Anfügen eines Computeziels](#portal-reuse), das außerhalb des Arbeitsbereichs erstellt wurde

Nachdem ein Ziel erstellt und an Ihren Arbeitsbereich angefügt wurde, verwenden Sie es in Ihrer Laufzeitkonfiguration mit einem `ComputeTarget`-Objekt: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>Anzeigen von Computezielen


Um die Compute-Ziele für Ihren Arbeitsbereich anzuzeigen, führen Sie die folgenden Schritte aus:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com), und öffnen Sie Ihren Arbeitsbereich. 
1. Wählen Sie unter __Anwendungen__ den Eintrag __Compute__.

    ![Registerkarte „Computer“ anzeigen](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a id="portal-create"></a>Erstellen eines Computeziels

Führen Sie die vorherigen Schritte zum Anzeigen der Liste der Computeziele aus. Führen Sie dann die folgenden Schritte aus, um ein Computeziel zu erstellen: 

1. Klicken Sie auf das Pluszeichen (+), um ein Computeziel hinzuzufügen.

    ![Hinzufügen eines Computeziels](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Geben Sie einen Namen für das Computeziel ein. 

1. Wählen Sie **Machine Learning Compute** als Computetyp für __Training__ aus. 

    >[!NOTE]
    >Azure Machine Learning Compute ist die einzige verwaltete Computeressource, die Sie im Azure-Portal erstellen können.  Alle anderen Computeressourcen können erst nach dem Erstellen angefügt werden.

1. Füllen Sie das Formular aus. Geben Sie für die Einrichtung des Computeziels Werte für die Pflichteigenschaften ein, insbesondere die **VM-Familie** und die **maximale Anzahl von Knoten**.  

    ![Ausfüllen des Formulars](./media/how-to-set-up-training-targets/add-compute-form.png) 

1. Klicken Sie auf __Erstellen__.


1. Sie können den Status des Erstellungsvorgangs anzeigen, indem Sie das Computeziel in der Liste auswählen:

    ![Auswählen eines Computeziels zum Anzeigen des Status des Erstellungsvorgangs](./media/how-to-set-up-training-targets/View_list.png)

1. Daraufhin werden die Details für das Computeziel angezeigt: 

    ![Anzeigen der Details des Computeziels](./media/how-to-set-up-training-targets/compute-target-details.png) 



### <a id="portal-reuse"></a>Anfügen von Computezielen

Um Computeziele zu verwenden, die außerhalb des Azure Machine Learning Service-Arbeitsbereichs erstellt wurde, müssen Sie sie anfügen. Durch das Anfügen eines Computeziels wird es Ihrem Arbeitsbereich zur Verfügung gestellt.

Führen Sie die zuvor beschriebenen Schritte aus, um die Liste der Computeziele anzuzeigen. Führen Sie dann die folgenden Schritte aus, um ein Computeziel anzufügen: 

1. Klicken Sie auf das Pluszeichen (+), um ein Computeziel hinzuzufügen. 
1. Geben Sie einen Namen für das Computeziel ein. 
1. Wählen Sie den Computetyp aus, den Sie für __Training__ anfügen möchten:

    > [!IMPORTANT]
    > Nicht alle Computetypen können im Azure-Portal angefügt werden. Derzeit können folgende Computetypen für das Training angefügt werden:
    >
    > * Eine Remote-VM
    > * Azure Databricks (zur Verwendung in Machine Learning-Pipelines)
    > * Azure Data Lake Analytics (zur Verwendung in Machine Learning-Pipelines)
    > * Azure HDInsight

1. Füllen Sie das Formular aus, und geben Sie Werte für die Pflichteigenschaften an.

    > [!NOTE]
    > Microsoft empfiehlt die Verwendung von SSH-Schlüsseln, die sicherer als Kennwörter sind. Kennwörter sind anfällig für Brute-Force-Angriffe. SSH-Schlüssel basieren auf kryptografische Signaturen. Informationen zum Erstellen von SSH-Schlüsseln für die Verwendung mit Azure Virtual Machines finden Sie in den folgenden Dokumenten:
    >
    > * [Erstellen und Verwenden eines SSH-Schlüsselpaars (öffentlich und privat) für virtuelle Linux-Computer in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Verwenden von SSH-Schlüsseln mit Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Wählen Sie __Anfügen__ aus. 
1. Sie können den Status des Anfügevorgangs anzeigen, indem Sie das Computeziel in der Liste auswählen.

## <a name="set-up-compute-with-the-cli"></a>Einrichten von Computeressourcen mit der CLI

Sie können auf die mit Ihrem Arbeitsbereich verknüpften Computeziele mit der [CLI-Erweiterung](reference-azure-machine-learning-cli.md) für Azure Machine Learning Service zugreifen.  Mit der CLI haben Sie folgende Möglichkeiten:

* Erstellen eines verwalteten Computeziels
* Aktualisieren eines verwalteten Computeziels
* Anfügen eines nicht verwalteten Computeziels

Weitere Informationen finden Sie unter [Ressourcenverwaltung](reference-azure-machine-learning-cli.md#resource-management).

## <a name="set-up-compute-with-vs-code"></a>Einrichten von Computeressourcen mit VS Code

Sie können auf die mit Ihrem Arbeitsbereich verknüpften Computeziele mit der [VS Code-Erweiterung](how-to-vscode-tools.md#create-and-manage-compute-targets) für Azure Machine Learning Service zugreifen, sie erstellen und verwalten.

## <a id="submit"></a>Übermitteln einer Trainingsausführung

Nachdem Sie eine Laufzeitkonfiguration erstellt haben, verwenden Sie sie zum Ausführen Ihres Experiments.  Das Codemuster für die Übermittlung einer Trainingsausführung ist für alle Arten von Computezielen gleich:

1. Erstellen eines Experiments zum Ausführen
1. Übermitteln Sie die Ausführung.
1. Warten Sie, bis die Ausführung abgeschlossen ist.

> [!IMPORTANT]
> Wenn Sie eine Trainingsausführung übermitteln, wird eine Momentaufnahme des Verzeichnisses, das Ihre Trainingsskripts enthält, erstellt und an das Computeziel gesendet. Sie wird auch als Teil des Experiments in Ihrem Arbeitsbereich gespeichert. Wenn Sie Dateien ändern und die Ausführung erneut übermitteln, werden nur die geänderten Dateien hochgeladen.
>
> Wenn Sie verhindern möchten, dass Dateien von der Momentaufnahme erfasst werden, müssen Sie eine [GITIGNORE](https://git-scm.com/docs/gitignore)- oder `.amlignore`-Datei im Verzeichnis erstellen und diesem die Dateien hinzufügen. Für die `.amlignore`-Datei werden die gleiche Syntax und die gleichen Muster wie für die [GITIGNORE](https://git-scm.com/docs/gitignore)-Datei verwendet. Wenn beide Dateien vorhanden sind, hat die `.amlignore`-Datei Vorrang.
> 
> Weitere Informationen finden Sie unter [Momentaufnahmen](concept-azure-machine-learning-architecture.md#snapshot).

### <a name="create-an-experiment"></a>Erstellen eines Experiments

Erstellen Sie zunächst ein Experiment in Ihrem Arbeitsbereich.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Übermitteln des Experiments

Übermitteln Sie das Experiment mit einem `ScriptRunConfig`-Objekt.  Dieses Objekt enthält Folgendes:

* **source_directory**: Das Quellverzeichnis mit Ihrem Trainingsskript
* **script**: Bestimmen des Trainingsskripts
* **run_config**: Die Laufzeitkonfiguration, die wiederum definiert, wo das Training erfolgt.

Verwenden Sie beispielsweise die Konfiguration [lokales Ziel](#local):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Wechseln Sie mit demselben Experiment zur Ausführung zu einem anderen Computeziel, indem Sie eine andere Laufzeitkonfiguration verwenden, z.B. [amlcompute-Ziel](#amlcompute):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

Alternative:

* Übermitteln Sie das Experiments mit einem `Estimator`-Objekt, wie in [Trainieren von ML-Modellen mit Kalkulatoren](how-to-train-ml-models.md) erläutert.
* Übermitteln Sie das Experiment [mithilfe der CLI-Erweiterung](reference-azure-machine-learning-cli.md#experiments).
* Übermitteln Sie ein Experiment über die [VS Code-Erweiterung](how-to-vscode-tools.md#train-and-tune-models).

## <a name="github-tracking-and-integration"></a>GitHub-Nachverfolgung und -Integration

Wenn Sie eine Trainingsausführung starten, bei der das Quellverzeichnis ein lokales Git-Repository ist, werden Informationen über das Repository im Ausführungsverlauf gespeichert. Zum Beispiel wird die aktuelle Commit-ID für das Repository als Teil des Verlaufs protokolliert.

## <a name="notebook-examples"></a>Notebook-Beispiele

Beispiele für das Training mit verschiedenen Computeziele finden Sie in diesen Notebooks:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nächste Schritte

* [Tutorial: Trainieren eines Modells](tutorial-train-models-with-aml.md) verwendet ein verwaltetes Computeziel zum Trainieren eines Modells.
* Erfahren Sie, wie [Hyperparameter optimiert werden](how-to-tune-hyperparameters.md), um bessere Modelle zu erstellen.
* Erfahren Sie nach der Erstellung eines trainierten Modells, [wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md).
* Zeigen Sie die SDK-Referenz der [RunConfiguration-Klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) an.
* [Verwenden von Azure Machine Learning Service mit Azure Virtual Networks](how-to-enable-virtual-network.md)
