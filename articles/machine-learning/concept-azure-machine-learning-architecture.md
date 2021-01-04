---
title: Architektur und wichtige Konzepte
titleSuffix: Azure Machine Learning
description: Dieser Artikel enthält eine allgemeine Beschreibung der Grundlagen in Bezug auf die Architektur, Begriffe und Konzepte von Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: a36481b2496060cb12bd755f56680915ec1074bb
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540203"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>So funktioniert Azure Machine Learning: Architektur und Konzepte

Enthält Informationen zu Architektur und Konzepten von [Azure Machine Learning](overview-what-is-azure-ml.md).  In diesem Artikel erhalten Sie ein allgemeines Verständnis der Komponenten und deren Zusammenarbeit, um das Entwickeln, Bereitstellen und Warten von Machine Learning-Modellen zu unterstützen.

## <a name="workspace"></a><a name="workspace"></a> Arbeitsbereich

Ein [Arbeitsbereich für maschinelles Lernen](concept-workspace.md) ist die Ressource der obersten Ebene für Azure Machine Learning.

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="Diagramm: Azure Machine Learning-Architektur eines Arbeitsbereichs und seiner Komponenten":::

Der Arbeitsbereich ist der zentrale Ort für:

* Verwalten von Ressourcen, die Sie für Training und Bereitstellung von Modellen wie [Berechnungen](#compute-instance) verwenden.
* Speichern von Ressourcen, die Sie erstellen, wenn Sie Azure Machine Learning verwenden, einschließlich:
  * [Umgebungen](#environments)
  * [Experimente](#experiments)
  * [Pipelines](#ml-pipelines)
  * [Datasets](#datasets-and-datastores)
  * [Modelle](#models)
  * [Endpunkte](#endpoints)

Ein Arbeitsbereich enthält andere Azure-Ressourcen, die er verwendet:

+ [Azure Container Registry (ACR)](https://azure.microsoft.com/services/container-registry/): Registriert die Docker-Container, die Sie während des Trainings und bei der Modellbereitstellung verwenden. Um Kosten zu minimieren, wird ACR nur erstellt, wenn Bereitstellungsimages erstellt werden.
+ [Azure Storage-Konto](https://azure.microsoft.com/services/storage/): Wird als Standarddatenspeicher für den Arbeitsbereich verwendet.  Jupyter-Notebooks, die mit Ihren Azure Machine Learning-Compute-Instanzen verwendet werden, werden ebenfalls hier gespeichert.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): Speichert Überwachungsinformationen zu Ihren Modellen.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Speichert Geheimnisse, die von Computezielen verwendet werden, sowie andere vertrauliche Informationen, die vom Arbeitsbereich benötigt werden.

Sie können einen Arbeitsbereich mit anderen Benutzern teilen.

### <a name="create-workspace"></a>Arbeitsbereich erstellen

Das folgende Diagramm zeigt den Workflow des Erstellens des Arbeitsbereichs.

* Sie melden sich von einem der unterstützten Azure Machine Learning-Clients (Azure CLI, Python, SDK, Azure-Portal) aus bei Azure AD an und fordern das entsprechende Azure Resource Manager-Token an.
* Sie rufen Azure Resource Manager auf, um den Arbeitsbereich zu erstellen. 
* Der Azure Resource Manager fordert den Azure Machine Learning-Ressourcenanbieter auf, den Arbeitsbereich bereitzustellen.
* Wenn Sie keine vorhandenen Ressourcen angeben, werden zusätzliche erforderliche Ressourcen in Ihrem Abonnement erstellt.

Sie können bei Bedarf auch andere Computeziele bereitstellen, die an einen Arbeitsbereich angefügt sind (wie Azure Kubernetes Service oder VMs).

[![Erstellen eines Arbeitsbereichs-Workflows](media/concept-azure-machine-learning-architecture/create-workspace.png)](media/concept-azure-machine-learning-architecture/create-workspace.png#lightbox)

## <a name="computes"></a>Berechnungen

<a name="compute-targets"></a> Ein [Computeziel](concept-compute-target.md) ist ein Computer oder eine Computergruppe, wo Ihr Trainingsskript ausgeführt oder Ihre Dienstbereitstellung gehostet wird. Sie können Ihren lokalen Computer oder eine Remotecomputeressource als Computeziel verwenden.  Mit Computezielen können Sie das Training auf dem lokalen Computer starten und dann horizontal auf die Cloud hochskalieren, ohne Ihr Trainingsskript zu ändern.

Azure Machine Learning führt zwei vollständig verwaltete, cloudbasierte virtuelle Computer (VM) ein, die für Machine Learning-Aufgaben konfiguriert sind:

* <a name="compute-instance"></a> **Compute-Instanz**: Eine Compute-Instanz ist eine VM, die mehrere für maschinelles Lernen installierte Tools und Umgebungen umfasst. Eine Compute-Instanz wird primär für Ihre Entwicklungsarbeitsstation verwendet.  Sie können ohne Setup mit der Ausführung von Beispiel-Notebooks beginnen. Eine Compute-Instanz kann auch als Computeziel für Trainings- und Rückschlussaufträge verwendet werden.

* **Computecluster**: Computecluster sind Cluster von VMs, in denen mehrere Knoten skaliert werden können. Computecluster eignen sich besser für Computeziele für große Aufträge und Produktion.  Beim Übermitteln eines Auftrags wird der Cluster automatisch hochskaliert.  Verwenden Sie ihn als Trainingscomputeziel oder zur Dev/Test-Bereitstellung.

Weitere Informationen zum Trainieren von Computezielen finden Sie unter [Trainieren von Computezielen](concept-compute-target.md#train).  Weitere Informationen zur Bereitstellung von Computezielen finden Sie unter [Bereitstellungsziele](concept-compute-target.md#deploy).

## <a name="datasets-and-datastores"></a>Datasets und Datenspeicher

[**Azure Machine Learning-Datasets**](concept-data.md#datasets) erleichtern Ihnen den Zugriff auf Ihre Daten und die Arbeit damit. Durch Erstellen eines Datasets erstellen Sie einen Verweis auf den Speicherort der Datenquelle sowie eine Kopie der zugehörigen Metadaten. Da die Daten an ihrem bisherigen Speicherort verbleiben, entstehen Ihnen keine zusätzlichen Speicherkosten und Sie riskieren nicht die Integrität Ihrer Datenquellen.

Weitere Informationen finden Sie unter [Erstellen und Registrieren von Azure Machine Learning Datasets](how-to-create-register-datasets.md).  Weitere Beispiele für die Verwendung von Datasets finden Sie in den [Beispielnotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial).

Datasets verwenden [Datenspeicher](concept-data.md#datastores), um eine sichere Verbindung mit ihren Azure-Speicherdiensten herzustellen. Datenspeicher speichern Verbindungsinformationen ohne Ihre Anmeldeinformationen für die Authentifizierung und die Integrität Ihrer ursprünglichen Datenquelle zu gefährden. Darin werden Verbindungsinformationen wie Ihre Abonnement-ID und die Tokenautorisierung in Ihrer mit dem Arbeitsbereich verknüpften Key Vault-Instanz gespeichert, damit Sie sicher auf Ihren Speicher zuzugreifen können, ohne diese Informationen in Ihren Skripts hartcodieren zu müssen.

## <a name="environments"></a>Umgebungen

[Arbeitsbereich](#workspace) > **Umgebungen**

Eine [Umgebung](concept-environments.md) ist die Kapselung der Umgebung, in der das Training oder die Bewertung Ihres Machine Learning-Modells stattfindet. Die Umgebung gibt die Python-Pakete, Umgebungsvariablen und Softwareeinstellungen bezüglich Ihrer Trainings- und Bewertungsskripts an.  

Codebeispiele finden Sie im Abschnitt „Verwalten von Umgebungen“ unter [Verwenden von Umgebungen](how-to-use-environments.md#manage-environments).

## <a name="experiments"></a>Experimente

[Arbeitsbereich](#workspace) > **Experimente**

Ein Experiment ist eine Gruppierung vieler Ausführungen aus einem bestimmten Skript. Es gehört immer zu einem Arbeitsbereich. Beim Übermitteln einer Ausführung geben Sie einen Experimentnamen an. Die Informationen für die Ausführung werden unter diesem Experiment gespeichert. Wenn der Name nicht vorhanden ist, wenn Sie ein Experiment übermitteln, wird automatisch ein neues Experiment erstellt.
  
Ein Beispiel zum Verwenden eines Experiments finden Sie im [Tutorial: Trainieren Ihres ersten Modells](tutorial-1st-experiment-sdk-train.md).

### <a name="runs"></a>Ausführungen

[Arbeitsbereich](#workspace) > [Experimente](#experiments) > **Ausführen**

Eine Ausführung ist eine einzelne Ausführung eines Trainingsskripts. Ein Experiment enthält in der Regel mehrere Durchläufe.

Azure Machine Learning zeichnet alle Ausführungen auf und speichert die folgenden Informationen im Experiment:

* Metadaten zur Ausführung (Zeitstempel, Dauer usw.)
* Metriken, die von Ihrem Skript protokolliert werden
* Ausgabedateien, die automatisch vom Experiment gesammelt oder explizit von Ihnen hochgeladen werden
* Eine Momentaufnahme des Verzeichnisses, in dem Ihre Skripts enthalten sind, vor der Ausführung

Eine Ausführung wird ausgelöst, wenn Sie ein Skript zum Trainieren eines Modells übermitteln. Eine Ausführung kann über null oder mehr untergeordnete Elemente verfügen. Die Ausführung der obersten Ebene weist also unter Umständen zwei untergeordnete Ausführungen auf, die beide jeweils selbst eine untergeordnete Ausführung aufweisen können.

### <a name="run-configurations"></a>Laufzeitkonfigurationen

[Arbeitsbereich](#workspace) > [Experimente](#experiments) > [Ausführen](#runs) > **Ausführen der Konfiguration**

Eine Ausführungskonfiguration definiert, wie ein Skript in einem bestimmten Computeziel ausgeführt werden sollte. Verwenden Sie die Konfiguration, um das Skript, das Computeziel und die Azure ML-Umgebung für die Ausführung, alle verteilten auftragsspezifischen Konfigurationen und einige zusätzliche Eigenschaften anzugeben. Weitere Informationen zum vollständigen Satz konfigurierbarer Optionen für Ausführungen finden Sie unter [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py).

Eine Laufzeitkonfiguration kann in einer Datei in dem Verzeichnis, in dem Ihr Trainingsskript enthalten ist, beständig gespeichert werden.   Alternativ kann sie als Objekt im Arbeitsspeicher erstellt und zum Übermitteln einer Ausführung verwendet werden.

Beispiele für Ausführungskonfigurationen finden Sie unter [Konfigurieren einer Trainingsausführung](how-to-set-up-training-targets.md).

### <a name="snapshots"></a>Momentaufnahmen

[Arbeitsbereich](#workspace) > [Experimente](#experiments) > [Ausführen](#runs) > **Momentaufnahme**

Beim Übermitteln einer Ausführung komprimiert Azure Machine Learning das Verzeichnis, in dem das Skript als ZIP-Datei enthalten ist, und sendet es an das Computeziel. Die ZIP-Datei wird dann extrahiert, und das Skript wird ausgeführt. Azure Machine Learning speichert die ZIP-Datei im Rahmen der Ausführungsaufzeichnung zudem als Momentaufnahme. Alle Benutzer mit Zugriff auf den Arbeitsbereich können eine Ausführungsaufzeichnung durchsuchen und die Momentaufnahme herunterladen.

Das folgende Diagramm zeigt den Workflow der Codemomentaufnahme.

[![Workflow der Codemomentaufnahme](media/concept-azure-machine-learning-architecture/code-snapshot.png)](media/concept-azure-machine-learning-architecture/code-snapshot.png#lightbox)

### <a name="logging"></a>Protokollierung

Standardmäßige Ausführungsmetriken werden von Azure Machine Learning automatisch protokolliert. Sie können jedoch auch das [Python SDK verwenden, um beliebige Metriken zu protokollieren](how-to-track-experiments.md).

Es gibt mehrere Möglichkeiten zum Anzeigen ihrer Protokolle: Überwachen des Ausführungsstatus in Echtzeit oder Anzeigen der Ergebnisse nach Abschluss. Weitere Informationen finden Sie unter [Überwachen und Anzeigen von ML-Ausführungsprotokollen](how-to-monitor-view-training-logs.md).


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Git-Nachverfolgung und -Integration

Wenn Sie eine Trainingsausführung starten, bei der das Quellverzeichnis ein lokales Git-Repository ist, werden Informationen über das Repository im Ausführungsverlauf gespeichert. Dies funktioniert für Ausführungen, die über eine Skriptausführungskonfiguration oder eine ML-Pipeline übermittelt werden. Dies funktioniert auch für Ausführungen, die aus dem SDK oder der Machine Learning-CLI übermittelt wurden.

Weitere Informationen finden Sie unter [Git-Integration für Azure Machine Learning](concept-train-model-git-integration.md).

### <a name="training-workflow"></a>Trainingsworkflow

Wenn Sie ein Experiment ausführen, um ein Modell zu trainieren, werden die folgenden Schritte ausgeführt. Diese werden im folgenden Diagramm zum Trainingsworkflow veranschaulicht:

* Azure Machine Learning wird mit der Momentaufnahmen-ID für die im vorherigen Abschnitt gespeicherte Codemomentaufnahme aufgerufen.
* Azure Machine Learning erstellt eine Ausführungs-ID (optional) und ein Azure Machine Learning-Token, das später von Computezielen wie Machine Learning Compute/VMs für die Kommunikation mit Machine Learning verwendet wird.
* Sie können entweder ein verwaltetes Computeziel (wie Machine Learning Compute) oder ein nicht verwaltetes Computeziel (wie VMs) auswählen, um Trainingsaufträge auszuführen. Im Folgenden finden Sie die Datenflüsse für beide Szenarien:
   * VMs/HDInsight, Zugriff erfolgt über SSH-Anmeldeinformationen in einem Schlüsseltresor im Microsoft-Abonnement. Azure Machine Learning führt verwalteten Code auf dem Computeziel aus, der die folgenden Aktionen ausführt:

   1. Vorbereiten der Umgebung (Docker ist eine Option für VMs und lokale Computer. In den folgenden Schritten für Machine Learning Compute erfahren Sie, wie die Ausführung eines Experiments in Docker-Containern funktioniert.)
   1. Herunterladen des Codes
   1. Einrichten von Umgebungsvariablen und Konfigurationen
   1. Ausführen von Benutzerskripts (die im vorherigen Abschnitt genannte Codemomentaufnahme)

   * Machine Learning Compute, Zugriff erfolgt über eine vom Arbeitsbereich verwaltete Identität.
Da Machine Learning Compute ein verwaltetes Computeziel ist (d. h. es wird von Microsoft verwaltet), wird es unter Ihrem Microsoft-Abonnement ausgeführt.

   1. Starten der Docker-Remotekonstruktion (sofern erforderlich)
   1. Schreiben des Verwaltungscodes in die Azure Files-Freigabe des Benutzers
   1. Starten des Containers mit einem ersten Befehl. Dies ist der Verwaltungscode, der im vorherigen Schritt beschrieben wurde.

* Nachdem die Ausführung abgeschlossen ist, können Sie Ausführungen und Metriken abfragen. Im folgenden Flussdiagramm wird dieser Schritt ausgeführt, wenn das Trainingscomputeziel die Ausführungsmetriken aus dem Speicher in der Cosmos DB-Datenbank an Azure Machine Learning zurückschreibt. Clients können Azure Machine Learning aufrufen. Machine Learning pullt wiederum die Metriken aus der Cosmos DB-Datenbank und gibt sie an den Client zurück.

[![Trainingsworkflow](media/concept-azure-machine-learning-architecture/training-and-metrics.png)](media/concept-azure-machine-learning-architecture/training-and-metrics.png#lightbox)

## <a name="models"></a>Modelle

Im einfachsten Fall ist ein Modell ein Codeabschnitt, für den eine Eingabe verwendet wird, um eine Ausgabe zu erzeugen. Die Erstellung eines Machine Learning-Modells umfasst die Auswahl eines Algorithmus, die Bereitstellung der zugehörigen Daten und die [Optimierung von Hyperparametern](how-to-tune-hyperparameters.md). Das Training ist ein iterativer Prozess, bei dem ein trainiertes Modell mit den während des Trainingsprozesses erlernten Informationen erzeugt wird.

Sie können ein Modell verwenden, das außerhalb von Azure Machine Learning trainiert wurde. Oder Sie können ein Modell trainieren, indem Sie eine [Ausführung](#runs) eines [Experiments](#experiments) an ein [Computeziel ](#compute-targets) in Azure Machine Learning übermitteln. Sobald Sie ein fertiges Modell haben, [registrieren](#register-model) Sie es im Arbeitsbereich.

Azure Machine Learning ist „framework-agnostisch“. Wenn Sie ein Modell erstellen, können Sie alle gängigen Frameworks für maschinelles Lernen verwenden, wie Scikit-learn, XGBoost, PyTorch, TensorFlow und Chainer.

Ein Beispiel zum Trainieren eines Modells mittels Scikit-learn finden Sie im [Tutorial: Trainieren eines Bildklassifizierungsmodells mit Azure Machine Learning](tutorial-train-models-with-aml.md).


### <a name="model-registry"></a><a name="register-model"></a> Modellregistrierung

[Arbeitsbereich](#workspace) > **Modelle**

Mit der **Modellregistrierung** können Sie alle Modelle in Ihrem Azure Machine Learning-Arbeitsbereich verfolgen.

Die Modelle werden anhand des Namens und der Version identifiziert. Jedes Mal, wenn Sie ein Modell mit dem gleichen Namen wie ein bereits vorhandenes Modell registrieren, geht die Registrierung davon aus, dass es sich um eine neue Version handelt. Die Version wird inkrementiert, und das neue Modell wird unter demselben Namen registriert.

Bei der Registrierung des Modells können Sie zusätzliche Metadatentags bereitstellen und diese Tags dann beim Suchen nach Modellen verwenden.

> [!TIP]
> Ein registriertes Modell ist ein logischer Container für eine oder mehrere Dateien, aus denen Ihr Modell besteht. Wenn Sie beispielsweise ein Modell verwenden, das in mehreren Dateien gespeichert ist, können Sie diese als einzelnes Modell in Ihrem Azure Machine Learning-Arbeitsbereich registrieren. Nach der Registrierung können Sie das registrierte Modell dann herunterladen oder bereitstellen und alle Dateien empfangen, die registriert wurden.

Sie können kein registriertes Modell löschen, das von einer aktiven Bereitstellung verwendet wird.

Ein Beispiel für das Registrieren eines Modells finden Sie unter [Trainieren eines Bildklassifizierungsmodells mit Azure Machine Learning Service](tutorial-train-models-with-aml.md).

## <a name="deployment"></a>Bereitstellung

Sie stellen ein [registriertes Modell](#register-model) als Dienstendpunkt bereit. Sie benötigen folgende Komponenten:

* **Umgebung**. Diese Umgebung kapselt die Abhängigkeiten, die zur Ausführung Ihres Modells für Rückschlüsse erforderlich sind.
* **Bewertungscode**. Dieses Skript akzeptiert Anforderungen, bewertet die Anforderungen über Verwenden des Modells und gibt die Ergebnisse zurück.
* **Rückschlusskonfiguration**. Die Rückschlusskonfiguration gibt die Umgebung, das Eingabeskript und andere Komponenten an, die zur Ausführung des Modells als Dienst benötigt werden.

Weitere Informationen zu diesen Komponenten finden Sie unter [Bereitstellen von Modellen mit Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="endpoints"></a>Endpunkte

[Arbeitsbereich](#workspace) > **Endpunkte**

Ein Endpunkt ist eine Instanziierung Ihres Modells in einem Webdienst, der in der Cloud gehostet werden kann, oder in einem IoT-Modul für Bereitstellungen von integrierten Diensten.

#### <a name="web-service-endpoint"></a>Webdienst-Endpunkt

Wenn Sie ein Modell als Webdienst bereitstellen, kann der Endpunkt auf Azure Container Instances, Azure Kubernetes Service oder FPGAs bereitgestellt werden. Sie erstellen den Dienst aus Ihrem Modell, dem Skript und zugeordneten Dateien. Diese werden in ein Basiscontainerimage eingefügt, das die Ausführungsumgebung für das Modell enthält. Das Image verfügt über einen HTTP-Endpunkt mit Lastenausgleich, der die an den Webdienst gesendeten Bewertungsanforderungen empfängt.

Sie können Application Insights-Telemetrie oder Modelltelemetrie aktivieren, um den Webdienst zu überwachen. Die Telemetriedaten sind nur für Sie zugänglich.  Sie werden in Ihren Application Insights- und Speicherkontoinstanzen gespeichert. Wenn Sie die automatische Skalierung aktiviert haben, führt Azure für Ihre Bereitstellung automatisch eine Skalierung durch.

Das folgende Diagramm zeigt den Workflow zum Ziehen von Rückschlüssen für ein Modell, das als Webdienstendpunkt bereitgestellt wird:

Es folgen die Details:

* Der Benutzer registriert ein Modell, indem er einen Client wie das Azure Machine Learning-SDK verwendet.
* Der Benutzer erstellt das Image mit einem Modell, einer Bewertungsdatei und anderen Modellabhängigkeiten.
* Das Docker-Image wird erstellt und in Azure Container Registry gespeichert.
* Der Webdienst wird auf dem Computeziel (Container Instances/AKS) mithilfe des Images bereitgestellt, das im vorherigen Schritt erstellt wurde.
* Details der Bewertung werden in Application Insights gespeichert (im Abonnement des Benutzers enthalten).
* Telemetriedaten werden ebenfalls per Push an das Microsoft/Azure-Abonnement übertragen.

[![Rückschlussworkflow](media/concept-azure-machine-learning-architecture/inferencing.png)](media/concept-azure-machine-learning-architecture/inferencing.png#lightbox)


Ein Beispiel für die Bereitstellung eines Modells als Webdienst finden Sie unter [Bereitstellen eines Bildklassifizierungsmodells in Azure Container Instances](tutorial-deploy-models-with-aml.md).

#### <a name="real-time-endpoints"></a>Echtzeitendpunkte

Wenn Sie ein trainiertes Modell im Designer bereitstellen, können Sie das [Modell als Echtzeitendpunkt bereitstellen](tutorial-designer-automobile-price-deploy.md). Ein Echtzeitendpunkt empfängt in der Regel eine einzelne Anforderung über den REST-Endpunkt und gibt eine Vorhersage in Echtzeit zurück. Dies steht im Gegensatz zur Batchverarbeitung, bei der mehrere Werte gleichzeitig verarbeitet werden und die Ergebnisse nach Abschluss in einem Datenspeicher gespeichert werden.

#### <a name="pipeline-endpoints"></a>Pipelineendpunkte

Mithilfe von Pipelineendpunkten können Sie Ihre [ML-Pipelines](#ml-pipelines) programmgesteuert über einen REST-Endpunkt abrufen. Mit Pipelineendpunkten lassen sich Pipelineworkflows automatisieren.

Ein Pipelineendpunkt ist eine Sammlung veröffentlichter Pipelines. Diese logische Anordnung ermöglicht Ihnen die Verwaltung und den Aufruf mehrerer Pipelines mithilfe desselben Endpunkts. Jede veröffentlichte Pipeline in einem Pipelineendpunkt verfügt über eine Versionsangabe. Sie können eine Standardpipeline für den Endpunkt auswählen oder im REST-Befehl eine Version angeben.
 

#### <a name="iot-module-endpoints"></a>IoT-Modulendpunkte

Ein bereitgestellter IoT-Modulendpunkt ist ein Docker-Container, der Ihr Modell und das zugeordnete Skript oder die Anwendung sowie alle zusätzlichen Abhängigkeiten enthält. Sie stellen diese Module mit Azure IoT Edge auf Edge-Geräten bereit.

Wenn Sie die Überwachung aktiviert haben, erfasst Azure Telemetriedaten aus dem Modell im Azure IoT Edge-Modul. Die Telemetriedaten sind nur für Sie zugänglich und werden in Ihrer Speicherkontoinstanz gespeichert.

Azure IoT Edge stellt sicher, dass Ihr Modul ausgeführt wird, und überwacht das Gerät, auf dem es gehostet wird. 
## <a name="automation"></a>Automation

### <a name="azure-machine-learning-cli"></a>Azure Machine Learning-CLI 

Die [Azure Machine Learning-CLI](reference-azure-machine-learning-cli.md) ist eine Erweiterung der Azure CLI, eine plattformübergreifende Befehlszeilenschnittstelle für die Azure-Plattform. Diese Erweiterung bietet Befehle zum Automatisieren Ihrer Machine Learning-Aktivitäten.

### <a name="ml-pipelines"></a>ML-Pipelines

[Machine Learning-Pipelines](concept-ml-pipelines.md) werden zum Erstellen und Verwalten von Workflows verwendet, die Machine Learning-Phasen zusammenfügen. Eine Pipeline kann beispielsweise eine Datenaufbereitungs-, eine Modelltrainings-, eine Modellimplementierungs- und eine Rückschluss-/Bewertungsphase enthalten. Jede Phase kann mehrere Schritte umfassen, von denen wiederum jeder Schritt auf verschiedenen Computezielen unbeaufsichtigt ausgeführt werden kann. 

Pipelineschritte sind wiederverwendbar und können ohne erneute Ausführung der vorherigen Schritte ausgeführt werden, wenn sich die Ausgabe dieser Schritte nicht geändert hat. Beispielsweise können Sie ein Modell erneut trainieren, ohne teure Schritte zur Datenvorbereitung erneut auszuführen, wenn sich die Daten nicht geändert haben. Pipelines ermöglichen Data Scientists auch, bei der Arbeit an separaten Bereichen eines Machine Learning-Workflows zusammenzuarbeiten.

## <a name="monitoring-and-logging"></a>Überwachung und Protokollierung

Azure Machine Learning verfügt über die folgenden Funktionen für die Überwachung und Protokollierung:

* __Data Scientists__ (Wissenschaftliche Fachkräfte für Daten) können ihre Experimente und Protokollinformationen über unsere Trainingsausführungen überwachen. Weitere Informationen finden Sie in den folgenden Artikeln:
   * [Starten, Überwachen und Abbrechen von Trainingsausführungen in Python](how-to-manage-runs.md)
   * [Protokollieren von Metriken für Trainingsausführungen](how-to-track-experiments.md)
   * [Nachverfolgen von Experimenten mit MLflow](how-to-use-mlflow.md)
   * [Visualisieren von Experimentausführungen und -metriken mit TensorBoard und Azure Machine Learning](how-to-monitor-tensorboard.md)
* __Administratoren__ können ihre Informationen zum Arbeitsbereich und zu den relevanten Azure-Ressourcen und Ereignissen, z. B. Erstellen und Löschen von Ressourcen, mit Azure Monitor überwachen. Weitere Informationen finden Sie unter [Überwachen von Azure Machine Learning](monitor-azure-machine-learning.md).
* Für den __DevOps__- bzw. __MLOps__-Bereich können generierte Informationen von Modellen, die als Webdienste oder IoT Edge-Module bereitgestellt werden, überwacht werden. Dies ermöglicht das Identifizieren von Problemen mit den Bereitstellungen und das Sammeln der an den Dienst übermittelten Daten. Weitere Informationen finden Sie unter [Sammeln von Modelldaten](how-to-enable-data-collection.md) und [Überwachen mit Application Insights](how-to-enable-app-insights.md).

## <a name="interacting-with-your-workspace"></a>Interaktion mit Ihrem Arbeitsbereich

### <a name="studio"></a>Studio

[Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) stellt eine Webansicht aller Artefakte in Ihrem Arbeitsbereich bereit.  Sie können Ergebnisse und Details zu ihren Datasets, Experimenten, Pipelines, Modellen und Endpunkten anzeigen.  Sie können auch Computeressourcen und Datenspeicher in Studio verwalten.

In Studio greifen Sie auch auf die interaktiven Tools zu, die Teil von Azure Machine Learning sind:

+ [Azure Machine Learning-Designer](concept-designer.md) zur Ausführung der Workflowschritte ohne Programmierung
+ Weboberfläche für [automatisiertes maschinelles Lernen](concept-automated-ml.md)
+ [Azure Machine Learning-Notebooks](how-to-run-jupyter-notebooks.md) zum Schreiben und Ausführen Ihres eigenen Code in integrierten Jupyter Notebook-Servern
+ [Datenbezeichnungsprojekte](how-to-create-labeling-projects.md) zum Erstellen, Verwalten und Überwachen von Projekten zur Bezeichnung Ihrer Daten

### <a name="programming-tools"></a>Programmiertools

> [!IMPORTANT]
> Die unten markierten Tools (Vorschau) sind aktuell als öffentliche Vorschau verfügbar.
> Die Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+  Interagieren Sie mit dem Dienst in einer beliebigen Python-Umgebung, indem Sie das [Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) verwenden.
+ Interagieren Sie mit dem Dienst in einer beliebigen R-Umgebung, indem Sie das [Azure Machine Learning SDK für R](https://azure.github.io/azureml-sdk-for-r/reference/index.html) (Vorschauversion) verwenden.
+ Verwenden Sie den [Azure Machine Learning-Designer](concept-designer.md), um die Workflowschritte auszuführen – ganz ohne Programmierung. 
+ Verwenden Sie die [Azure Machine Learning-CLI](./reference-azure-machine-learning-cli.md) zur Automatisierung.
+ Der [Many Models Solution Accelerator](https://aka.ms/many-models) (Preview) (Projektmappenbeschleuniger für viele Modelle (Vorschau)) baut auf Azure Machine Learning auf und ermöglicht Ihnen Training, Betrieb und Verwaltung von hunderten oder sogar tausenden von Machine Learning-Modellen.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten mit Azure Machine Learning finden Sie unter:

* [Was ist Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md)
* [Tutorial (Teil 1): Trainieren eines Modells](tutorial-train-models-with-aml.md)