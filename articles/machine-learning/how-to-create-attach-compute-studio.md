---
title: Erstellen von Computeressourcen für Training und Bereitstellung (Studio)
titleSuffix: Azure Machine Learning
description: Verwenden von Studio zum Erstellen von Computeressourcen (Computeziele) für Training und Bereitstellung für maschinelles Lernen
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1
ms.openlocfilehash: ab7a74166e85f2ba9fd73e7323cf9cd200cf32e4
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031027"
---
# <a name="create-compute-targets-for-model-training-and-deployment-in-azure-machine-learning-studio"></a>Erstellen von Computezielen für Modelltraining und -bereitstellung in Azure Machine Learning Studio

In diesem Artikel erfahren Sie, wie Sie Computeziele in Azure Machine Learning Studio erstellen und verwalten.  Sie können Computeziele auch mit folgenden Komponenten erstellen und verwalten:

* Azure Machine Learning SDK- oder CLI-Erweiterung für Azure Machine Learning
  * [Compute-Instanz](how-to-create-manage-compute-instance.md)
  * [Computecluster](how-to-create-attach-compute-cluster.md)
  * [Azure Kubernetes Service-Cluster](how-to-create-attach-kubernetes.md)
  * [Weitere Berechnungsressourcen](how-to-attach-compute-targets.md)
* [VS Code-Erweiterung](how-to-manage-resources-vscode.md#compute-clusters) für Azure Machine Learning


## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) noch heute aus.
* Ein [Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md).

## <a name="whats-a-compute-target"></a>Was ist ein Computeziel? 

Mit Azure Machine Learning können Sie Ihr Modell für eine Vielzahl von Ressourcen oder Umgebungen trainieren, die zusammen als [__Computeziele__](concept-azure-machine-learning-architecture.md#compute-targets) bezeichnet werden. Ein Computeziel kann ein lokaler Computer oder eine Cloudressource sein, wie beispielsweise Azure Machine Learning Compute, Azure HDInsight oder ein virtueller Remotecomputer.  Sie können auch Computeziele für die Modellimplementierung erstellen, wie in [Bereitstellen von Modellen mit dem Azure Machine Learning-Dienst](how-to-deploy-and-where.md) beschrieben.

## <a name="view-compute-targets"></a><a id="portal-view"></a>Anzeigen von Computezielen

Führen Sie die folgenden Schritte aus, um alle Computeziele für Ihren Arbeitsbereich anzuzeigen:

1. Navigieren Sie zu [Azure Machine Learning Studio](https://ml.azure.com).
 
1. Wählen Sie unter __Verwalten__ die Option __Compute__ aus.

1. Wählen Sie oben Registerkarten aus, um die einzelnen Computeziele anzuzeigen.

    :::image type="content" source="media/how-to-create-attach-studio/view-compute-targets.png" alt-text="Anzeigen der Liste mit Computezielen":::

## <a name="create-compute-target"></a><a id="portal-create"></a>Erstellen eines Computeziels

Führen Sie die vorherigen Schritte zum Anzeigen der Liste der Computeziele aus. Führen Sie dann die folgenden Schritte aus, um ein Computeziel zu erstellen:

1. Wählen Sie oben die Registerkarte aus, die dem von Ihnen erstellten Computetyp entspricht.

1. Wenn Sie keine Computeziele besitzen, wählen Sie in der Mitte der Seite **Erstellen** aus.
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="Erstellen eines Computeziels":::

1. Wenn eine Liste der Computeressourcen angezeigt wird, wählen Sie oberhalb der Liste **+ Neu** aus.

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="Auswählen von „Neu“":::


1. Füllen Sie das Formular für Ihren Computetyp aus:

  * [Compute-Instanz](#compute-instance)
  * [Computecluster](#amlcompute)
  * [Rückschlusscluster](#inference-clusters)
  * [Angefügte Computeressourcen](#attached-compute)

1. Klicken Sie auf __Erstellen__.

1. Sie können den Status des Erstellungsvorgangs anzeigen, indem Sie das Computeziel in der Liste auswählen:

    :::image type="content" source="media/how-to-create-attach-studio/view-list.png" alt-text="Anzeigen des Computestatus in einer Liste":::


### <a name="compute-instance"></a>Compute-Instanz

Führen Sie die [oben beschriebenen Schritte](#portal-create) aus, um die Compute-Instanz zu erstellen.  Füllen Sie das Formular anschließend wie folgt aus:

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="Erstellen einer neuen Compute-Instanz":::


|Feld  |BESCHREIBUNG  |
|---------|---------|
|Computename     |  <li>Der Name ist erforderlich und muss zwischen 3 und 24 Zeichen lang sein.</li><li>Gültige Zeichen sind Groß- und Kleinbuchstaben, Ziffern und das Zeichen **-** .</li><li>Der Name muss mit einem Buchstaben beginnen.</li><li>Der Name muss auf allen vorhandenen Compute-Instanzen innerhalb einer Azure-Region eindeutig sein. Sie erhalten eine Warnung, wenn der von Ihnen gewählte Name nicht eindeutig ist.</li><li>Wenn ein **-** -Zeichen verwendet wird, muss darauf im Namen mindestens ein Buchstabe folgen.</li>     |
|Typ des virtuellen Computers |  Wählen Sie CPU oder GPU aus. Dieser Typ kann nach der Erstellung nicht mehr geändert werden.     |
|Größe des virtuellen Computers     |  Die Größe der unterstützten virtuellen Computer kann in Ihrer Region eingeschränkt sein. Überprüfen Sie die [Verfügbarkeitsliste](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).     |
|Aktivieren/Deaktivieren des SSH-Zugriffs     |   Der SSH-Zugriff ist standardmäßig deaktiviert.  Der SSH-Zugriff kann nach der Erstellung nicht mehr geändert werden. Stellen Sie sicher, dass Sie den Zugriff aktivieren, wenn Sie das interaktive Debuggen mit [VS Code Remote](how-to-set-up-vs-code-remote.md) planen.   |
|Erweiterte Einstellungen     |  Optional. Konfigurieren Sie ein virtuelles Netzwerk. Geben Sie **Ressourcengruppe**, **Virtuelles Netzwerk** und **Subnetz** an, um die Compute-Instanz innerhalb von Azure Virtual Network (VNET) zu erstellen. Weitere Informationen finden Sie unter diesen [Netzwerkanforderungen](./how-to-secure-training-vnet.md) für VNET.  |

### <a name="compute-clusters"></a><a name="amlcompute"></a> Computecluster

Erstellen Sie einen Computecluster mit einem oder mehreren Knoten für Ihre Workloads in den Bereichen Training, Batchrückschlüsse und vertiefendes Lernen. Führen Sie die [oben beschriebenen Schritte](#portal-create) aus, um den Computecluster zu erstellen.  Füllen Sie das Formular anschließend wie folgt aus:


|Feld  |BESCHREIBUNG  |
|---------|---------|
|Computename     |  <li>Der Name ist erforderlich und muss zwischen 3 und 24 Zeichen lang sein.</li><li>Gültige Zeichen sind Groß- und Kleinbuchstaben, Ziffern und das Zeichen **-** .</li><li>Der Name muss mit einem Buchstaben beginnen.</li><li>Der Name muss auf allen vorhandenen Compute-Instanzen innerhalb einer Azure-Region eindeutig sein. Sie erhalten eine Warnung, wenn der von Ihnen gewählte Name nicht eindeutig ist.</li><li>Wenn ein **-** -Zeichen verwendet wird, muss darauf im Namen mindestens ein Buchstabe folgen.</li>     |
|Typ des virtuellen Computers |  Wählen Sie CPU oder GPU aus. Dieser Typ kann nach der Erstellung nicht mehr geändert werden.     |
|VM-Priorität | Wählen Sie **Dediziert** oder **Mit niedriger Priorität** aus.  Virtuelle Computer mit niedriger Priorität sind kostengünstiger, bieten jedoch keine garantierten Computeknoten. Ihr Auftrag wird unter Umständen vorzeitig entfernt.
|Größe des virtuellen Computers     |  Die Größe der unterstützten virtuellen Computer kann in Ihrer Region eingeschränkt sein. Überprüfen Sie die [Verfügbarkeitsliste](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).     |
|Mindestanzahl von Knoten | Mindestanzahl von Knoten, die Sie bereitstellen möchten. Wenn Sie eine dedizierte Anzahl von Knoten verwenden möchten, legen Sie diese Anzahl hier fest. Sparen Sie Geld, indem Sie die Mindestanzahl auf 0 festlegen, sodass Sie für keine Knoten bezahlen, wenn sich der Cluster im Leerlauf befindet. |
|Maximale Knotenanzahl | Maximale Anzahl von Knoten, die Sie bereitstellen möchten. Beim Übermitteln eines Auftrags wird die Computeressource automatisch auf den Höchstwert dieser Knotenanzahl skaliert. |
|Erweiterte Einstellungen     |  Optional. Konfigurieren Sie ein virtuelles Netzwerk. Geben Sie **Ressourcengruppe**, **Virtuelles Netzwerk** und **Subnetz** an, um die Compute-Instanz innerhalb von Azure Virtual Network (VNET) zu erstellen. Weitere Informationen finden Sie unter diesen [Netzwerkanforderungen](./how-to-secure-training-vnet.md) für VNET.   Fügen Sie außerdem [verwaltete Identitäten](#managed-identity) an, um Zugriff auf Ressourcen zu gewähren.     |

#### <a name="set-up-managed-identity"></a><a name="managed-identity"></a> Einrichten einer verwalteten Identität

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

Schalten Sie beim Erstellen von Clustern oder beim Bearbeiten von Computeclusterdetails unter **Erweiterte Einstellungen** die Option **Verwaltete Identität zuweisen** um, und geben Sie eine systemseitig oder eine benutzerseitig zugewiesene Identität an.

#### <a name="managed-identity-usage"></a>Nutzung von verwalteten Identitäten

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

### <a name="inference-clusters"></a>Rückschlusscluster

> [!IMPORTANT]
> Für die Verwendung des Azure Kubernetes Service mit Azure Machine Learning gibt es mehrere Konfigurationsoptionen. In bestimmten Szenarien wie Netzwerken ist eine zusätzliche Einrichtung und Konfiguration erforderlich. Weitere Informationen zum Verwenden von AKS mit Azure Machine Learning finden Sie unter [Erstellen und Anfügen eines Azure Kubernetes Service-Clusters](how-to-create-attach-kubernetes.md).

Sie können einen AKS-Cluster (Azure Kubernetes Service) für umfangreiche Rückschlüsse erstellen oder anfügen. Führen Sie die [oben beschriebenen Schritte](#portal-create) aus, um den AKS-Cluster zu erstellen.  Füllen Sie das Formular anschließend wie folgt aus:


|Feld  |BESCHREIBUNG  |
|---------|---------|
|Computename     |  <li>Der Name ist erforderlich. Der Name muss zwischen 2 und 16 Zeichen lang sein. </li><li>Gültige Zeichen sind Groß- und Kleinbuchstaben, Ziffern und das Zeichen **-** .</li><li>Der Name muss mit einem Buchstaben beginnen.</li><li>Der Name muss auf allen vorhandenen Compute-Instanzen innerhalb einer Azure-Region eindeutig sein. Sie erhalten eine Warnung, wenn der von Ihnen gewählte Name nicht eindeutig ist.</li><li>Wenn ein **-** -Zeichen verwendet wird, muss darauf im Namen mindestens ein Buchstabe folgen.</li>     |
|Kubernetes-Dienst | Wählen Sie **Neu erstellen** aus, und füllen Sie das restliche Formular aus.  Oder wählen Sie **Vorhandene verwenden** und dann einen vorhandenen AKS-Cluster aus Ihrem Abonnement aus.
|Region |  Wählen Sie die Region aus, an dem der Cluster erstellt wird. |
|Größe des virtuellen Computers     |  Die Größe der unterstützten virtuellen Computer kann in Ihrer Region eingeschränkt sein. Überprüfen Sie die [Verfügbarkeitsliste](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).     |
|Clusterzweck  | Wählen Sie **Produktion** oder **Dev-Test** aus. |
|Anzahl von Knoten | Die Anzahl von Knoten multipliziert mit der Anzahl von VM-Kernen (vCPUs) muss mindestens 12 ergeben. |
| Netzwerkkonfiguration | Wählen Sie **Erweitert** aus, um die Computeressource in einem vorhandenen virtuellen Netzwerk zu erstellen. Weitere Informationen zu AKS in einem virtuellen Netzwerk finden Sie unter [Netzwerkisolation während Training und Rückschluss mit privaten virtuellen Netzwerken](./how-to-secure-inferencing-vnet.md). |
| Enable SSL configuration (SSL-Konfiguration aktivieren) | Hiermit konfigurieren Sie das SSL-Zertifikat für das Computing. |

### <a name="attached-compute"></a>Angefügte Computeressourcen

Um Computeziele zu verwenden, die außerhalb des Azure Machine Learning-Arbeitsbereichs erstellt wurde, müssen Sie sie anfügen. Durch das Anfügen eines Computeziels wird es Ihrem Arbeitsbereich zur Verfügung gestellt.  Verwenden Sie **Angefügte Computeressourcen**, um ein Computeziel für **Training** anzufügen.  Verwenden Sie **Rückschlusscluster**, um einen AKS-Cluster für **Rückschlüsse** anzufügen.

Führen Sie die [oben beschriebenen Schritte](#portal-create) aus, um eine Computeressource anzufügen.  Füllen Sie das Formular anschließend wie folgt aus:

1. Geben Sie einen Namen für das Computeziel ein. 
1. Wählen Sie den Computetyp aus, den Sie anfügen möchten. Nicht alle Computetypen können in Azure Machine Learning Studio angefügt werden. Derzeit können folgende Computetypen für das Training angefügt werden:
    * Eine Remote-VM
    * Azure Databricks (zur Verwendung in Machine Learning-Pipelines)
    * Azure Data Lake Analytics (zur Verwendung in Machine Learning-Pipelines)
    * Azure HDInsight

1. Füllen Sie das Formular aus, und geben Sie Werte für die Pflichteigenschaften an.

    > [!NOTE]
    > Microsoft empfiehlt die Verwendung von SSH-Schlüsseln, die sicherer als Kennwörter sind. Kennwörter sind anfällig für Brute-Force-Angriffe. SSH-Schlüssel basieren auf kryptografische Signaturen. Informationen zum Erstellen von SSH-Schlüsseln für die Verwendung mit Azure Virtual Machines finden Sie in den folgenden Dokumenten:
    >
    > * [Erstellen und Verwenden eines SSH-Schlüsselpaars (öffentlich und privat) für virtuelle Linux-Computer in Azure](../virtual-machines/linux/mac-create-ssh-keys.md)
    > * [Verwenden von SSH-Schlüsseln mit Windows in Azure](../virtual-machines/linux/ssh-from-windows.md)

1. Wählen Sie __Anfügen__ aus. 


## <a name="next-steps"></a>Nächste Schritte

Nachdem ein Ziel erstellt und an Ihren Arbeitsbereich angefügt wurde, verwenden Sie es in Ihrer [Laufzeitkonfiguration](how-to-set-up-training-targets.md) mit einem `ComputeTarget`-Objekt:

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

* Verwenden Sie die Computeressource zum [Übermitteln einer Trainingsausführung](how-to-set-up-training-targets.md).
* [Tutorial: Trainieren eines Modells](tutorial-train-models-with-aml.md) verwendet ein verwaltetes Computeziel zum Trainieren eines Modells.
* Erfahren Sie, wie [Hyperparameter optimiert werden](how-to-tune-hyperparameters.md), um bessere Modelle zu erstellen.
* Erfahren Sie nach der Erstellung eines trainierten Modells, [wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md).
* [Verwenden von Azure Machine Learning mit virtuellen Azure-Netzwerken](./how-to-network-security-overview.md)