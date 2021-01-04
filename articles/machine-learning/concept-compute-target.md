---
title: Was sind Computziele?
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie eine Computeressource oder Umgebung für das Training oder die Bereitstellung Ihres Modells mit Azure Machine Learning bestimmen können.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/29/2020
ms.openlocfilehash: 1d3a7be429f730dd4dbd71bade1e3e51ea73c10f
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188648"
---
# <a name="what-are-compute-targets-in-azure-machine-learning"></a>Was sind Computeziele in Azure Machine Learning?

Ein *Computeziel* ist eine festgelegte Computeressource oder -Umgebung, in der Ihr Trainingsskript ausgeführt oder Ihre Dienstbereitstellung gehostet wird. Hierbei kann es sich um Ihren lokalen Computer oder eine cloudbasierte Computeressource handeln. Mithilfe von Computezielen können Sie Ihre Compute-Umgebung später problemlos ändern, ohne den Code anpassen zu müssen.

In einem typischen Modellentwicklungslebenszyklus gehen Sie unter Umständen wie folgt vor:

1. Sie entwickeln und experimentieren zunächst mit einer kleinen Datenmenge. In dieser Phase verwenden Sie Ihre lokale Umgebung, z. B. einen lokalen Computer oder einen cloudbasierten virtuellen Computer (VM), als Ihr Computeziel.
1. Skalieren Sie Ihre Umgebung für größere Datenmengen hoch, oder führen Sie ein verteiltes Training mithilfe eines dieser [Trainingscomputeziele](#train) durch.
1. Wenn Ihr Modell bereit ist, stellen Sie es in einer Webhostingumgebung oder auf einem IoT-Gerät mit einem [dieser Bereitstellungscomputeziele](#deploy) bereit.

Die Computeressourcen, die Sie für Ihre Computeziele verwenden, werden an einen [Arbeitsbereich](concept-workspace.md) angefügt. Andere Computeressourcen als der lokale Computer werden von Benutzern des Arbeitsbereichs gemeinsam genutzt.

## <a name="training-compute-targets"></a><a name="train"></a> Trainieren von Computezielen

Azure Machine Learning bietet unterschiedliche Unterstützung für verschiedene Computeziele. Ein typischer Lebenszyklus der Modellentwicklung beginnt mit der Entwicklung oder dem Experimentieren mit einer kleinen Menge von Daten. In dieser Phase verwenden Sie eine lokale Umgebung wie Ihren lokalen Computer oder eine cloudbasierte VM. Wenn Sie Ihr Training zur Verwendung größerer Datasets hochskalieren oder sich für ein verteiltes Training entscheiden, verwenden Sie Azure Machine Learning Compute, um einen Cluster mit einem einzelnen oder mehreren Knoten zu erstellen, der bei jeder Übermittlung einer Ausführung automatisch skaliert wird. Sie können auch Ihre eigene Computeressource anfügen. Die Unterstützung für verschiedene Szenarien kann jedoch variieren.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Erfahren Sie mehr darüber, wie Sie [eine Trainingsausführung an ein Computeziel übermitteln](how-to-set-up-training-targets.md).

## <a name="compute-targets-for-inference"></a><a name="deploy"></a> Computeziele für Rückschlüsse

Die folgenden Computeressourcen können zum Hosten Ihrer Modellimplementierung verwendet werden.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Beim Durchführen von Rückschlüssen erstellt Azure Machine Learning einen Docker-Container, der das Modell und zugehörige Ressourcen hostet, die für die Verwendung erforderlich sind. Dieser Container wird dann in einem der folgenden Bereitstellungsszenarien verwendet:

* Als *Webdienst*, der für Echtzeitrückschlüsse verwendet wird. Webdienstbereitstellungen verwenden eines der folgenden Computeziele:

    * [Lokaler Computer](how-to-attach-compute-targets.md#local)
    * [Azure Machine Learning-Computeinstanz](how-to-create-manage-compute-instance.md)
    * [Azure Container Instances](how-to-attach-compute-targets.md#aci)
    * [Azure Kubernetes Service](how-to-create-attach-kubernetes.md)
    * Azure Functions (Vorschau). Bei der Bereitstellung in Azure Functions wird nur Azure Machine Learning zum Erstellen des Docker-Containers vorausgesetzt. Von dort aus wird es mithilfe von Azure Functions bereitgestellt. Weitere Informationen finden Sie unter [Bereitstellen eines Machine Learning-Modells in Azure Functions (Vorschauversion)](how-to-deploy-functions.md).

* Als Endpunkt für _Batchrückschlüsse_, der zur regelmäßigen Verarbeitung von Datenbatches verwendet wird. Batchrückschlüsse verwenden [Azure Machine Learning-Computecluster](how-to-create-attach-compute-cluster.md).

* Auf ein _IoT-Gerät_ (Vorschau). Bei der Bereitstellung auf einem IoT-Gerät wird nur Azure Machine Learning zum Erstellen des Docker-Containers vorausgesetzt. Anschließend erfolgt die Bereitstellung über Azure IoT Edge. Weitere Informationen finden Sie unter [Deploy as an IoT Edge module (preview)](../iot-edge/tutorial-deploy-machine-learning.md) (Bereitstellen als IoT Edge-Modul (Vorschau)).

Erfahren Sie, [wo und wie Sie Ihr Modell auf einem Computeziel bereitstellen](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning-Computeressource (verwaltet)

Eine verwaltete Computeressource wird von Azure Machine Learning erstellt und verwaltet. Diese Computeressource ist für Machine Learning-Workloads optimiert. Azure Machine Learning-Computecluster und -[Compute-Instanzen](concept-compute-instance.md) sind die einzigen verwalteten Computeressourcen.

Sie können Azure Machine Learning-Compute-Instanzen oder -Computecluster erstellen mithilfe von:

* [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md)
* Das Python SDK und die CLI:
    * [Compute-Instanz](how-to-create-manage-compute-instance.md)
    * [Computecluster](how-to-create-attach-compute-cluster.md)
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (Vorschau)
* Azure Resource Manager-Vorlage Eine Beispielvorlage finden Sie unter [Erstellen eines Azure Machine Learning-Computeclusters](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute).
* Eine [Machine-Learning-Erweiterung für die Azure CLI](reference-azure-machine-learning-cli.md#resource-management)

Nach der Erstellung sind diese Computeressourcen im Gegensatz zu anderen Arten von Computezielen automatisch Teil Ihres Arbeitsbereichs.


|Funktion  |Computecluster  |Compute-Instanz  |
|---------|---------|---------|
|Cluster mit einem oder mehreren Knoten     |    **&check;**       |         |
|Automatische Skalierung bei jedem Übermitteln einer Ausführung     |     **&check;**      |         |
|Automatische Clusterverwaltung und Auftragsplanung     |   **&check;**        |     **&check;**      |
|Unterstützt CPU- und GPU-Ressourcen     |  **&check;**         |    **&check;**       |


> [!NOTE]
> Wenn sich ein *Computecluster* im Leerlauf befindet, wird er automatisch auf 0 Knoten skaliert. Es entstehen also keine Kosten, wenn der Cluster nicht verwendet wird. Eine *Compute-Instanz* ist immer aktiviert und wird nicht automatisch skaliert. Sie sollten [Compute-Instanzen anhalten](how-to-create-manage-compute-instance.md#manage), wenn Sie sie nicht verwenden, um unnötige Kosten zu vermeiden.

### <a name="supported-vm-series-and-sizes"></a>Unterstützte VM-Serien und -Größen

Wenn Sie eine Knotengröße für eine verwaltete Computeressource in Azure Machine Learning auswählen, können Sie unter den in Azure verfügbaren VM-Größen auswählen. Azure bietet eine Reihe von Größen für Linux und Windows für verschiedene Workloads. Weitere Informationen finden Sie unter [VM-Typen und -Größen](../virtual-machines/sizes.md).

Bei der Auswahl einer VM-Größe gelten einige Ausnahmen und Einschränkungen:

* Einige VM-Serien werden in Azure Machine Learning nicht unterstützt.
* Einige VM-Serien sind eingeschränkt. Um eine eingeschränkte Serie zu verwenden, wenden Sie sich an den Support, und fordern Sie eine Kontingenterhöhung für die Serie an. Informationen zur Kontaktaufnahme mit dem Support finden Sie unter [Azure-Supportoptionen](https://azure.microsoft.com/support/options/).

In der folgenden Tabelle finden Sie weitere Informationen zu unterstützten Serien und Einschränkungen.

| **Unterstützte VM-Serie**  | **Einschränkungen** |
|------------|------------|
| D | Keine. |
| Dv2 | Keine. |  
| Dv3 | Keine.|
| DSv2 | Keine. | 
| DSv3 | Keine.|
| FSv2 | Keine. | 
| HBv2 | Genehmigung erforderlich. |  
| HCS | Genehmigung erforderlich. |  
| M | Genehmigung erforderlich. |
| NC | Keine. |    
| NCsv2 | Genehmigung erforderlich. |
| NCsv3 | Genehmigung erforderlich. |  
| NDs | Genehmigung erforderlich. |
| NDv2 | Genehmigung erforderlich. |
| SH | Keine. |
| NVv3 | Genehmigung erforderlich. | 


Obwohl Azure Machine Learning diese VM-Serien unterstützt, sind sie möglicherweise nicht in allen Azure-Regionen verfügbar. Informationen zum Überprüfen, ob VM-Serien verfügbar sind, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

> [!NOTE]
> Azure Machine Learning unterstützt nicht alle VM-Größen, die von Azure Compute unterstützt werden. Um alle verfügbaren VM-Größen aufzulisten, verwenden Sie eine der folgenden Methoden:
> * [REST-API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable/2020-08-01/examples/ListVMSizesResult.json)
> * [Python SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py.md#supported-vmsizes-workspace--location-none-)
>

### <a name="compute-isolation"></a>Computeisolation

Azure Machine Learning Compute bietet VM-Größen, die für einen bestimmten Hardwaretyp isoliert und für einen einzelnen Kunden bestimmt sind. Die Größen isolierter VMs eignen sich am besten für Workloads, die ein hohes Maß an Isolation von den Workloads anderer Kunden erfordern, beispielsweise um Compliance zu erzielen und gesetzliche Anforderungen zu erfüllen. Durch die Verwendung isolierter Größen wird sichergestellt, dass Ihre VM als einzige in der jeweiligen Serverinstanz ausgeführt wird.

Die aktuellen Angebote für isolierte VMs umfassen Folgendes:

* Standard_M128ms
* Standard_F72s_v2
* Standard_NC24s_v3
* Standard_NC24rs_v3*

*RDMA-fähig

Weitere Informationen zur Isolation finden Sie unter [Isolation in der öffentlichen Azure-Cloud](../security/fundamentals/isolation-choices.md).

## <a name="unmanaged-compute"></a>Nicht verwaltete Computeressourcen

Ein nicht verwaltetes Computeziel wird *nicht* von Azure Machine Learning verwaltet. Sie erstellen diese Art von Computeziel außerhalb von Azure Machine Learning und fügen es anschließend an Ihren Arbeitsbereich an. Für nicht verwaltete Computeressourcen sind möglicherweise weitere Schritte erforderlich, um die Leistung von Machine Learning-Workloads beizubehalten oder zu verbessern.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel werden folgende Themen erläutert:
* [Einrichten und Verwenden von Computezielen für das Modelltraining](how-to-set-up-training-targets.md)
* [Bereitstellen Ihres Modells auf einem Computeziel](how-to-deploy-and-where.md)
