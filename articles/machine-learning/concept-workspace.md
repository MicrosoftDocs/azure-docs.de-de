---
title: Was ist ein Arbeitsbereich?
titleSuffix: Azure Machine Learning
description: Der Arbeitsbereich ist die Ressource der obersten Ebene für Azure Machine Learning. Der Verlauf aller Trainingsausführungen wird gespeichert, z. B. Protokolle, Metriken, Ausgabe und eine Momentaufnahme Ihrer Skripts.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/22/2020
ms.openlocfilehash: 7acb555ddde20d6512119af85a8db055bcd9eeed
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701652"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Was ist ein Azure Machine Learning-Arbeitsbereich?

Der Arbeitsbereich ist die Ressource der obersten Ebene für Azure Machine Learning und ein zentraler Ort für die Arbeit mit allen Artefakten, die Sie während der Nutzung von Azure Machine Learning erstellen.  Im Arbeitsbereich wird der Verlauf aller Trainingsläufe gespeichert, einschließlich Protokollen, Metriken, Ausgabe und einer Momentaufnahme Ihrer Skripts. Anhand dieser Informationen ermitteln Sie, welcher Trainingslauf das beste Modell ergibt.  

Sobald Sie ein fertiges Modell haben, registrieren Sie es im Arbeitsbereich. Anschließend verwenden Sie das registrierte Modell und die Bewertungsskripts, um das Modell in Azure Container Instances, in Azure Kubernetes Service oder in einem FPGA (Field Programmable Gate Array) als REST-basierten HTTP-Endpunkt bereitzustellen. Sie können das Modell auch als Modul auf einem Azure IoT Edge-Gerät bereitstellen.

## <a name="taxonomy"></a>Taxonomie 

Das folgende Diagramm enthält eine Taxonomie des Arbeitsbereichs:

[![Taxonomie des Arbeitsbereichs](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

In dem Diagramm sind die folgenden Komponenten eines Arbeitsbereichs dargestellt:

+ Ein Arbeitsbereich kann [Azure Machine Learning-Compute-Instanzen](concept-compute-instance.md) enthalten. Diese sind Cloudressourcen, die mit der Python-Umgebung konfiguriert sind, die zum Ausführen von Azure Machine Learning erforderlich ist.

+ [Benutzerrollen](how-to-assign-roles.md) ermöglichen Ihnen, Ihren Arbeitsbereich für andere Benutzer, Teams oder Projekte freizugeben.
+ [Computeziele](concept-azure-machine-learning-architecture.md#compute-targets) werden verwendet, um Ihre Experimente auszuführen.
+ Wenn Sie den Arbeitsbereich erstellen, werden auch [zugeordnete Ressourcen](#resources) für Sie erstellt.
+ [Experimente](concept-azure-machine-learning-architecture.md#experiments) sind Trainingsläufe (Trainingsausführungen), mit denen Sie Ihre Modelle erstellen.  
+ [Pipelines](concept-azure-machine-learning-architecture.md#ml-pipelines) sind wiederverwendbare Workflows zum Trainieren und erneuten Trainieren Ihres Modells.
+ [Datasets](concept-azure-machine-learning-architecture.md#datasets-and-datastores) vereinfachen die Verwaltung der Daten, die Sie zum Modelltraining und zur die Pipelineerstellung verwenden.
+ Nachdem Sie über ein Modell verfügen, das Sie bereitstellen möchten, können Sie ein registriertes Modell erstellen.
+ Verwenden Sie das registrierte Modell und ein Bewertungsskript, um einen [Bereitstellungsendpunkt](concept-azure-machine-learning-architecture.md#endpoints) zu erstellen.

## <a name="tools-for-workspace-interaction"></a>Tools zum Arbeiten mit Ihrem Arbeitsbereich

Sie können auf folgende Arten mit Ihrem Arbeitsbereich arbeiten:

> [!IMPORTANT]
> Die unten markierten Tools (Vorschau) sind aktuell als öffentliche Vorschau verfügbar.
> Die Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+ Im Web:
    + [Azure Machine Learning-Studio](https://ml.azure.com) 
    + [Azure Machine Learning-Designer](concept-designer.md) 
+ In jeder Python-Umgebung mit dem [Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)
+ In jeder R-Umgebung mit dem [Azure Machine Learning SDK für R (Vorschau)](https://azure.github.io/azureml-sdk-for-r/reference/index.html)
+ Über die Befehlszeile mit der [CLI-Erweiterung](./reference-azure-machine-learning-cli.md) für Azure Machine Learning
+ [Azure Machine Learning-VS Code-Erweiterung](how-to-manage-resources-vscode.md#workspaces)


## <a name="machine-learning-with-a-workspace"></a>Maschinelles Lernen mit einem Arbeitsbereich

Für Aufgaben für maschinelles Lernen werden Artefakte aus Ihrem Arbeitsbereich gelesen oder in diesen geschrieben.

+ Ausführen eines Experiments, um ein Modell zu trainieren: Die Ergebnisse der Experimentausführung werden in den Arbeitsbereich geschrieben.
+ Verwenden von automatisiertem ML, um ein Modell zu trainieren: Die Trainingsergebnisse werden in den Arbeitsbereich geschrieben.
+ Registrieren eines Modells im Arbeitsbereich.
+ Bereitstellen eines Modells: Das registrierte Modell wird verwendet, um eine Bereitstellung zu erstellen.
+ Erstellen und Ausführen von wiederverwendbaren Workflows.
+ Anzeigen von Machine Learning-Artefakten, z. B. Experimente, Pipelines, Modelle, Bereitstellungen.
+ Nachverfolgen und Überwachen von Modellen.

## <a name="workspace-management"></a>Arbeitsbereichsverwaltung

Sie können auch die folgenden Arbeitsbereichsverwaltungsaufgaben ausführen:

| Arbeitsbereichsverwaltungsaufgabe   | Portal              | Studio | Python SDK / R SDK       | Befehlszeilenschnittstelle (CLI)        | VS-Code
|---------------------------|---------|---------|------------|------------|------------|
| Erstellen eines Arbeitsbereichs        | **&check;**     | | **&check;** | **&check;** | **&check;** |
| Verwalten des Arbeitsbereichszugriffs    | **&check;**   || |  **&check;**    ||
| Erstellen und Verwalten von Computeressourcen    | **&check;**   | **&check;** | **&check;** |  **&check;**   ||
| Erstellen einer Notebook-VM |   | **&check;** | |     ||

> [!WARNING]
> Das Verschieben des Azure Machine Learning-Arbeitsbereichs in ein anderes Abonnement oder das Verschieben des besitzenden Abonnements in einen neuen Mandanten wird nicht unterstützt. Andernfalls können Fehler auftreten.

## <a name="create-a-workspace"></a><a name='create-workspace'></a> Erstellen eines Arbeitsbereichs

Es gibt mehrere Möglichkeiten zum Erstellen eines Arbeitsbereichs:  

* Im [Azure-Portal](how-to-manage-workspace.md?tabs=azure-portal#create-a-workspace) steht eine Point-and-Click-Benutzeroberfläche zur Verfügung, über die Sie die einzelnen Schritte ausführen können.
* Verwenden Sie das [Azure Machine Learning SDK für Python](how-to-manage-workspace.md?tabs=python#create-a-workspace), um ohne weitere Vorbereitung einen Arbeitsbereich auf der Grundlage von Python-Skripts oder Jupyter Notebooks zu erstellen.
* Nutzen Sie eine [Azure Resource Manager-Vorlage](how-to-create-workspace-template.md) oder die [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md), wenn Sie die Erstellung automatisieren oder mit Unternehmenssicherheitsstandards anpassen müssen.
* Verwenden Sie die [VS Code-Erweiterung](how-to-manage-resources-vscode.md#create-a-workspace), wenn Sie mit Visual Studio Code arbeiten.

> [!NOTE]
> Für den Namen des Arbeitsbereichs wird die Groß-/Kleinschreibung nicht beachtet.

## <a name="associated-resources"></a><a name="resources"></a> Zugeordnete Ressourcen

Wenn Sie einen neuen Arbeitsbereich erstellen, werden darin automatisch mehrere Azure-Ressourcen erstellt, die vom Arbeitsbereich verwendet werden:

+ [Azure Storage-Konto](https://azure.microsoft.com/services/storage/): Wird als Standarddatenspeicher für den Arbeitsbereich verwendet.  Jupyter-Notebooks, die mit Ihren Azure Machine Learning-Compute-Instanzen verwendet werden, werden ebenfalls hier gespeichert. 
  
  > [!IMPORTANT]
  > Beim Speicherkonto handelt es sich standardmäßig um ein Konto vom Typ „Universell V1“. Nach der Erstellung des Arbeitsbereichs können Sie ein [Upgrade auf „Universell V2“ durchführen](../storage/common/storage-account-upgrade.md). Aktivieren Sie keinen hierarchischen Namespace für das Speicherkonto, nachdem Sie ein Upgrade auf Universell v2 ausgeführt haben.

  Wenn Sie ein vorhandenes Azure Storage-Konto verwenden möchten, darf es sich nicht um ein Premium-Konto (Premium_LRS oder Premium_GRS) handeln. Es darf auch keinen hierarchischen Namespace aufweisen (mit Azure Data Lake Storage Gen2 verwendet). Weder Storage Premium noch hierarchische Namespaces werden mit dem _Standardspeicherkonto_ des Arbeitsbereichs unterstützt. Sie können Storage Premium noch hierarchische Namespaces mit _nicht standardmäßigen_ Speicherkonten verwenden.
  
+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): Registriert die Docker-Container, die Sie während des Trainings und bei der Modellbereitstellung verwenden. Um Kosten zu minimieren, wird ACR **verzögert geladen**, bis Bereitstellungsimages erstellt sind.

+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): Speichert Überwachungsinformationen zu Ihren Modellen.

+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Speichert Geheimnisse, die von Computezielen verwendet werden, sowie andere vertrauliche Informationen, die vom Arbeitsbereich benötigt werden.

> [!NOTE]
> Sie können stattdessen vorhandene Azure-Ressourceninstanzen verwenden, wenn Sie den Arbeitsbereich mit dem [Python SDK](how-to-manage-workspace.md?tabs=python#create-a-workspace), dem [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/create_workspace.html) oder der Azure Machine Learning-CLI [unter Verwendung einer ARM-Vorlage](how-to-create-workspace-template.md) erstellen.

<a name="wheres-enterprise"></a>

## <a name="what-happened-to-enterprise-edition"></a>Was ist mit der Enterprise Edition passiert?

Ab September 2020 sind alle Funktionen, die in den Enterprise Edition-Arbeitsbereichen verfügbar waren, auch in den Basic Edition-Arbeitsbereichen verfügbar. Neue Enterprise-Arbeitsbereiche können nicht mehr erstellt werden.  Alle SDK-, CLI- oder Azure Resource Manager-Aufrufe, die den Parameter `sku` verwenden, funktionieren weiterhin, es wird jedoch ein Basic-Arbeitsbereich bereitgestellt.

Ab dem 21. Dezember werden alle Enterprise Edition-Arbeitsbereiche automatisch auf die Basic Edition festgelegt, die über die gleichen Funktionen verfügt. Während dieses Vorgangs tritt keine Downtime auf. Am 1. Januar 2021 wird die Enterprise Edition offiziell eingestellt. 

In beiden Editionen sind Kunden für die Kosten der verbrauchten Azure-Ressourcen verantwortlich und müssen keine zusätzlichen Gebühren für Azure Machine Learning bezahlen. Ausführlichere Informationen finden Sie auf der [Preisseite für Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten mit Azure Machine Learning finden Sie unter:

+ [Azure Machine Learning – Übersicht](overview-what-is-azure-ml.md)
+ [Erstellen und Verwalten eines Arbeitsbereichs](how-to-manage-workspace.md)
+ [Tutorial: Erste Schritte mit Azure Machine Learning in Ihrer Entwicklungsumgebung](tutorial-1st-experiment-sdk-setup-local.md)
+ [Tutorial: Erste Schritte beim Erstellen Ihres ersten ML-Experiments in einer Compute-Instanz](tutorial-1st-experiment-sdk-setup.md)
+ [Tutorial: Erste Schritte mit Azure Machine Learning mit dem R SDK](tutorial-1st-r-experiment.md)
+ [Tutorial: Erstellen Ihres ersten Klassifizierungsmodells mit automatisiertem maschinellen Lernen](tutorial-first-experiment-automated-ml.md) 
+ [Tutorial: Prognostizieren von Automobilpreisen mit dem Designer](tutorial-designer-automobile-price-train-score.md)
