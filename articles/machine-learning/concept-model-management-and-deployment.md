---
title: 'MLOps: ML-Modellverwaltung'
titleSuffix: Azure Machine Learning
description: 'Erfahren Sie mehr über die Modellverwaltung mit Azure Machine Learning (MLOps). Stellen Sie Modelle bereit, und verwalten und überwachen Sie diese, um sie kontinuierlich zu verbessern. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 64784d747e9f33961c2f5d2df95e0d5a83e01548
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324832"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: Verwaltung, Bereitstellung und Überwachung von Modellen mit Azure Machine Learning

In diesem Artikel erfahren Sie, wie Sie Azure Machine Learning verwenden, um den Lebenszyklus Ihrer Modelle zu verwalten. Azure Machine Learning verwendet einen Machine Learning Operations-Ansatz (MLOps). MLOps verbessert die Qualität und Konsistenz ihrer Machine Learning-Lösungen. 

## <a name="what-is-mlops"></a>Was ist MLOps?

Machine Learning-Vorgänge (MLOps) basieren auf [DevOps](https://azure.microsoft.com/overview/what-is-devops/)-Prinzipien und -Methoden, die die Effizienz von Workflows erhöhen. Beispiele sind Continuous Integration, Continuous Delivery und Continuous Deployment. MLOps wendet diese Prinzipien auf den Machine Learning-Prozess mit folgendem Ziel an:

* Schnelleres Experimentieren und Entwickeln von Modellen
* Schnellere Bereitstellung von Modellen in der Produktionsumgebung
* Qualitätssicherung

Azure Machine Learning bietet die folgenden MLOps-Features:

- **Erstellen reproduzierbarer ML-Pipelines**. Mit Machine Learning-Pipelines können Sie wiederholbare und wiederverwendbare Schritte für Ihre Datenaufbereitungs-, Trainings- und Bewertungsprozesse definieren.
- **Erstellen Sie wiederverwendbare Softwareumgebungen** für das Training und die Bereitstellung von Modellen.
- **Registrieren, Verpacken und Bereitstellen von Modellen von überall aus**. Sie können auch zugehörige Metadaten nachverfolgen, die für die Verwendung des Modells erforderlich sind.
- **Erfassung der Governancedaten für den End-to-End-ML-Lebenszyklus**. Die protokollierten Informationen können umfassen, wer die Modelle veröffentlicht, warum Änderungen vorgenommen wurden und wann die Modelle bereitgestellt oder in der Produktionsumgebung verwendet wurden.
- **Benachrichtigen und Warnen bei Ereignissen im ML-Lebenszyklus**. Beispielsweise die Durchführung von Experimenten, die Registrierung und Bereitstellung von Modellen sowie die Erkennung von Datendrift.
- **Überwachen von ML-Anwendungen auf betriebs- und ML-bezogene Probleme**. Vergleichen Sie Modelleingaben zwischen Trainings- und Inferenzphase, untersuchen Sie modellspezifische Metriken, und stellen Sie Überwachungsfunktionen und Warnungen für Ihre ML-Infrastruktur bereit.
- **Automatisieren des End-to-End-ML-Lebenszyklus mit Azure Machine Learning und Azure Pipelines**. Mithilfe von Pipelines können Sie Modelle regelmäßig aktualisieren, neue Modelle testen und fortlaufende Rollouts von neuen ML-Modellen zusätzlich zu Ihren anderen Anwendungen und Diensten durchführen.

## <a name="create-reproducible-ml-pipelines"></a>Erstellen reproduzierbarer ML-Pipelines

Verwenden Sie ML-Pipelines aus Azure Machine Learning, um alle Schritte zu verbinden, die am Trainingsprozess Ihres Modells beteiligt sind.

Eine ML-Pipeline kann Schritte aus der Datenaufbereitung über die Featureextraktion und die Optimierung von Hyperparametern bis hin zur Modellauswertung enthalten. Weitere Informationen finden Sie im Artikel zu [ML-Pipelines](concept-ml-pipelines.md).

Wenn Sie den [Designer](concept-designer.md) zum Erstellen Ihrer ML-Pipelines verwenden, können Sie jederzeit auf die **"..."** oben rechts auf der Designer-Seite klicken und dann **Klonen** auswählen. Durch das Klonen der Pipeline können Sie das Pipelinedesign durchlaufen, ohne Ihre alten Versionen zu verlieren.  

## <a name="create-reusable-software-environments"></a>Erstellen von wiederverwendbaren Softwareumgebungen

Azure Machine Learning-Umgebungen gestatten es Ihnen, die Softwareabhängigkeiten Ihrer Projekte in ihrem zeitlichen Ablauf nachzuverfolgen und zu reproduzieren. Mithilfe von Umgebungen können Sie sicherstellen, dass Builds ohne manuelle Softwarekonfigurationen reproduzierbar sind.

Die Umgebungen beschreiben die pip- und Conda-Abhängigkeiten für Ihre Projekte und können sowohl für das Training als auch für die Bereitstellung von Modellen verwendet werden. Weitere Informationen finden Sie unter [Was sind Azure Machine Learning-Umgebungen?](concept-environments.md).

## <a name="register-package-and-deploy-models-from-anywhere"></a>Registrieren, Verpacken und Bereitstellen von Modellen von überall aus

### <a name="register-and-track-ml-models"></a>Registrieren und Nachverfolgen von ML-Modellen

Die Modellregistrierung ermöglicht es Ihnen, Ihre Modelle in der Azure-Cloud in Ihrem Arbeitsbereich zu speichern und zu versionieren. Die Modellregistrierung erleichtert das Organisieren und Verwalten von trainierten Modellen.

> [!TIP]
> Ein registriertes Modell ist ein logischer Container für eine oder mehrere Dateien, aus denen Ihr Modell besteht. Wenn Sie beispielsweise ein Modell verwenden, das in mehreren Dateien gespeichert ist, können Sie diese als einzelnes Modell in Ihrem Azure Machine Learning-Arbeitsbereich registrieren. Nach der Registrierung können Sie das registrierte Modell dann herunterladen oder bereitstellen und alle Dateien empfangen, die registriert wurden.

Registrierte Modelle werden anhand des Namens und der Version identifiziert. Jedes Mal, wenn Sie ein Modell mit dem gleichen Namen wie ein bereits vorhandenes Modell registrieren, erhöht die Registrierung die Versionsnummer. Zusätzliche Metadatentags können während der Registrierung bereitgestellt werden. Diese Tags werden dann bei der Suche nach einem Modell verwendet. Azure Machine Learning unterstützt Modelle, die mit Python 3.5.2 oder höher geladen werden können.

> [!TIP]
> Sie können auch Modelle registrieren, die außerhalb von Azure Machine Learning trainiert wurden.

Sie können kein registriertes Modell löschen, das in einer aktiven Bereitstellung verwendet wird.
Weitere Informationen finden Sie im Abschnitt „Registrieren eines Modells“ von [Bereitstellen von Modellen](how-to-deploy-and-where.md#registermodel).

> [!IMPORTANT]
> Bei Verwendung der Option „Filtern nach `Tags`“ auf der Seite „Modelle“ von Azure Machine Learning Studio müssen Kunden `TagName=TagValue` (ohne Leerzeichen) anstelle von `TagName : TagValue` verwenden.

### <a name="profile-models"></a>Profilmodelle

Azure Machine Learning kann Ihnen helfen, die CPU- und Arbeitsspeicheranforderungen des Diensts zu verstehen, der bei der Bereitstellung Ihres Modells erstellt wird. Die Profilerstellung testet den Dienst, der Ihr Modell ausführt, und gibt Informationen wie CPU-Auslastung, Speicherauslastung und Antwortlatenz zurück. Sie bietet auch eine CPU- und Speicherempfehlung auf der Grundlage der Ressourcenauslastung.
Weitere Informationen finden Sie im Abschnitt „Profilerstellung“ von [Bereitstellen von Modellen](how-to-deploy-profile-model.md).

### <a name="package-and-debug-models"></a>Packen und Debuggen von Modellen

Vor dem Bereitstellen eines Modells in der Produktion wird es in ein Docker-Image gepackt. In den meisten Fällen erfolgt die Imageerstellung während der Bereitstellung automatisch im Hintergrund. Sie können das Image auch manuell angeben.

Wenn Probleme bei der Bereitstellung auftreten, können Sie ein Modell für Problembehandlung und Debugging in Ihrer lokalen Entwicklungsumgebung bereitstellen.

Weitere Informationen finden Sie unter [Bereitstellen von Modellen](how-to-deploy-and-where.md#registermodel) und [Problembehandlung von Bereitstellungen](how-to-troubleshoot-deployment.md).

### <a name="convert-and-optimize-models"></a>Konvertieren und Optimieren von Modellen

Die Konvertierung Ihres Modells zu [Open Neural Network Exchange](https://onnx.ai) (ONNX) kann die Leistung verbessern. Im Durchschnitt lässt sich durch eine Konvertierung zu ONNX eine 2-fache Leistungssteigerung erzielen.

Weitere Informationen zu ONNX mit Azure Machine Learning finden Sie im Artikel [Erstellen und Beschleunigen von ML-Modellen](concept-onnx.md).

### <a name="use-models"></a>Verwenden von Modellen

Trainierte Machine Learning-Modelle werden als Webdienste in der Cloud oder lokal bereitgestellt. Sie können Modelle auch auf Azure IoT Edge-Geräten bereitstellen. Bereitstellungen verwenden CPUs, GPUs oder Field Programmable Gate Arrays (FPGAs) für das Ziehen von Rückschlüssen. Sie können auch Modelle aus Power BI verwenden.

Wenn Sie ein Modell als Webdienst oder auf einem IoT Edge-Gerät verwenden, stellen Sie die folgenden Elemente bereit:

* Die Modelle, die zum Bewerten der Daten dienen, die an den Dienst bzw. das Gerät übermittelt werden.
* Ein Eingabeskript. Dieses Skript akzeptiert Anforderungen, verwendet die Modelle zum Bewerten der Daten und gibt eine Antwort zurück.
* Eine Azure Machine Learning-Umgebung, die die pip- und Conda-Abhängigkeiten beschreibt, die von dem/den Modell(en) und dem Eingabeskript benötigt werden.
* Zusätzliche Ressourcen wie Text, Daten usw., die von den Modellen und vom Eingabeskript benötigt werden.

Sie stellen auch die Konfiguration der Zielbereitstellungsplattform bereit. Beispielsweise den Typ der VM-Familie, den verfügbaren Arbeitsspeicher und die Anzahl von Kernen bei der Bereitstellung in Azure Kubernetes Service.

Wenn das Image erstellt wird, werden auch die Komponenten hinzugefügt, die von Azure Machine Learning benötigt werden. Beispielsweise die Ressourcen, die erforderlich sind, um den Webdienst auszuführen und mit IoT Edge zu interagiere.

#### <a name="batch-scoring"></a>Batchbewertung
Die Batchbewertung durch ML-Pipelines wird unterstützt. Weitere Informationen finden Sie im Artikel [Batchvorhersagen für Big Data](./tutorial-pipeline-batch-scoring-classification.md).

#### <a name="real-time-web-services"></a>Echtzeit-Webdienste

Sie können Ihre Modelle in **Webdiensten** mit den folgenden Computezielen verwenden:

* Azure Container Instances
* Azure Kubernetes Service
* Lokale Entwicklungsumgebung

Um das Modell als Webdienst bereitzustellen, müssen Sie folgende Elemente zur Verfügung stellen:

* Das Modell oder ein Ensemble von Modellen.
* Abhängigkeiten, die zur Nutzung des Modells erforderlich sind. Beispielsweise ein Skript, das Anforderungen akzeptiert und das Modell aufruft, Conda-Abhängigkeiten, usw.
* Eine Bereitstellungskonfiguration, die beschreibt, wie und wo das Modell bereitgestellt werden soll.

Weitere Informationen finden Sie unter [Bereitstellen von Modellen](how-to-deploy-and-where.md).

#### <a name="controlled-rollout"></a>Kontrollierter Rollout

Bei der Bereitstellung in Azure Kubernetes Service können Sie einen kontrollierten Rollout verwenden, um die folgenden Szenarien zu unterstützen:

* Erstellen mehrerer Versionen eines Endpunkts für eine Bereitstellung
* Durchführen von A/B-Tests über die Weiterleitung von Datenverkehr an verschiedene Versionen des Endpunkts
* Wechsel zwischen Endpunktversionen durch Aktualisierung des Prozentsatzes an Datenverkehr in der Endpunktkonfiguration

Weitere Informationen finden Sie unter [Kontrollierter Rollout von ML-Modellen](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview).

#### <a name="iot-edge-devices"></a>IoT Edge-Geräte

Sie können Modelle mit IoT-Geräten über **Azure IoT Edge-Module** verwenden. IoT Edge-Module werden auf einem Hardwaregerät bereitgestellt, wodurch Rückschlüsse bzw. Modellbewertungen auf dem Gerät ermöglicht werden.

Weitere Informationen finden Sie unter [Bereitstellen von Modellen](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analytics

Microsoft Power BI unterstützt die Verwendung von Machine Learning-Modellen für Datenanalysen. Weitere Informationen finden Sie unter [Integration von Azure Machine Learning in Power BI (Vorschau)](/power-bi/service-machine-learning-integration).

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>Sammeln der erforderlichen Governancedaten zur Erfassung des ML-Lebenszyklus End-to-End

Bei Azure ML können Sie den End-to-End-Überwachungspfad Ihrer gesamten ML-Ressourcen durch Verwendung von Metadaten nachverfolgen.

- Azure ML ist [mit Git integriert](how-to-set-up-training-targets.md#gitintegration), um nachverfolgen zu können, von welchem Repository, welcher Branch oder welchem Commit Ihr Code stammt.
- Mit [Azure ML-Datasets](how-to-create-register-datasets.md) können Sie Daten nachverfolgen, Profile erstellen und Versionen verwalten.
- Dank der [Interpretierbarkeit](how-to-machine-learning-interpretability.md) ist es möglich, Ihre Modelle zu erläutern, gesetzliche Bestimmungen einzuhalten und zu verstehen, wie Modelle bei bestimmten Eingaben zu einem Ergebnis kommen.
- Der Azure ML-Ausführungsverlauf speichert eine Momentaufnahme des Codes, der Daten und der Computevorgänge, mit denen ein Modell trainiert wurde.
- Die Azure ML-Modellregistrierung erfasst alle Metadaten, die Ihrem Modell zugeordnet sind (welches Experiment wurde für das Training verwendet, wo wird es bereitgestellt und ob die Bereitstellungen fehlerfrei sind).
- Durch die [Integration mit Azure](how-to-use-event-grid.md) können Sie auf Ereignisse im ML-Lebenszyklus reagieren. Hierzu zählen beispielsweise Ereignisse für Modellregistrierung, Bereitstellung, Datenabweichung und Training (Ausführung).

> [!TIP]
> Während einige Informationen zu Modellen und Datasets automatisch erfasst werden, können Sie mithilfe von __Tags__ zusätzliche Informationen hinzufügen. Wenn Sie in Ihrem Arbeitsbereich nach registrierten Modellen und Datasets suchen, können Sie Tags als Filter verwenden.
>
> Die Zuordnung eines Datasets zu einem registrierten Modell ist ein optionaler Schritt. Informationen zum Referenzieren eines Datasets beim Registrieren eines Modells finden Sie in der Referenz zur [Model](/python/api/azureml-core/azureml.core.model%28class%29?preserve-view=true&view=azure-ml-py)-Klasse.


## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>Benachrichtigen, Automatisieren und Warnen bei Ereignissen im ML-Lebenszyklus
Azure ML veröffentlicht wichtige Ereignisse in Azure EventGrid, das für Benachrichtigung und Automatisierung bei Ereignissen im ML-Lebenszyklus verwendet werden kann. Weitere Informationen finden Sie in [diesem Dokument](how-to-use-event-grid.md).


## <a name="monitor-for-operational--ml-issues"></a>Überwachen auf Betriebs- und ML-Probleme

Die Überwachung ermöglicht Ihnen zu verstehen, welche Daten an Ihr Modell gesendet werden und welche Vorhersagen es zurückgibt.

Diese Informationen helfen Ihnen zu verstehen, wie Ihr Modell genutzt wird. Die gesammelten Eingabedaten können auch zum Trainieren künftiger Versionen des Modells nützlich sein.

Weitere Informationen finden Sie unter [Gewusst wie: Aktivieren der Modelldatensammlung](how-to-enable-data-collection.md).

## <a name="retrain-your-model-on-new-data"></a>Erneutes Trainieren des Modells nach neuen Daten

Oftmals möchten Sie Ihr Modell validieren, aktualisieren oder sogar von Grund auf neu trainieren, wenn Sie neue Informationen erhalten. Manchmal ist das Empfangen neuer Daten ein erwarteter Teil der Domäne. Zu anderen Zeiten, wie in [Erkennen von Datenabweichungen (Vorschau) in Datasets](how-to-monitor-datasets.md) erläutert, kann die Modellleistung angesichts von Faktoren wie Änderungen an einem bestimmten Sensor, natürlichen Datenänderungen wie saisonalen Effekten oder Verschiebung von Features in ihrer Beziehung zu anderen Features abnehmen. 

Es gibt keine allgemeingültige Antwort auf die Frage „Woher weiß ich, ob ich erneut trainieren soll?“, aber die zuvor besprochenen Ereignis- und Überwachungstools von Azure Machine Learning sind gute Ausgangspunkte für die Automatisierung. Wenn Sie sich für ein erneutes Training entschieden haben, sollten Sie wie folgt vorgehen: 

- Vorverarbeiten Ihrer Daten durch einen wiederholbaren, automatisierten Prozess
- Trainieren Ihres neuen Modells
- Vergleichen der Ergebnisse des neuen Modells mit denen des alten Modells
- Verwenden von vordefinierten Kriterien, um zu entscheiden, ob das alte Modell ersetzt werden soll 

Ein Thema der oben genannten Schritte ist, dass das erneute Trainieren automatisch und nicht ad hoc erfolgen sollte. [Azure Machine Learning-Pipelines](concept-ml-pipelines.md) sind eine gute Antwort für die Erstellung von Workflows in Bezug auf Datenvorbereitung, Training, Überprüfung und Bereitstellung. Lesen Sie [Erneutes Trainieren von Modellen mit dem Azure Machine Learning-Designer](how-to-retrain-designer.md), um zu sehen, wie Pipelines und der Azure Machine Learning-Designer in ein Szenario für erneutes Trainieren passen. 

## <a name="automate-the-ml-lifecycle"></a>Automatisieren des ML-Lebenszyklus 

Sie können GitHub und Azure Pipelines verwenden, um einen Continuous Integration-Prozess zu erstellen, der ein Modell trainiert. In einem typischen Szenario, in dem ein Data Scientist eine Änderung in das Git-Repository für ein Projekt eincheckt, startet die Azure-Pipeline einen Trainingsdurchlauf. Die Ergebnisse dieses Durchlaufs können dann untersucht werden, um die Leistungsmerkmale des trainierten Modells zu ermitteln. Sie können auch eine Pipeline erstelle, die das Modell als Webdienst bereitstellt.

Die [Azure Machine Learning-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) erleichtert die Arbeit mit Azure Pipelines. Sie bietet folgende Verbesserungen für Azure Pipelines:

* Ermöglicht beim Definieren einer Dienstverbindung die Auswahl eines Arbeitsbereichs.
* Ermöglicht das Auslösen von Releasepipelines durch trainierte Modelle, die in einer Trainingspipeline erstellt wurden.

Weitere Informationen zur Verwendung von Azure Pipelines mit Azure Machine Learning finden Sie unter den folgenden Links:

* [Continuous Integration und Continuous Deployment von ML-Modellen mit Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning) 
* [Azure Machine Learning MLOps](https://aka.ms/mlops)-Repository
* [Azure Machine Learning MLOpsPython](https://github.com/Microsoft/MLOpspython)-Repository

Sie können Azure Data Factory auch verwenden, um eine Datenerfassungspipeline zu erstellen, die die Daten für die Verwendung im Training vorbereitet. Weitere Informationen finden Sie unter [Datenerfassungspipeline](how-to-cicd-data-ingestion.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Ressourcen:

+ [Bereitstellen von Modellen](how-to-deploy-and-where.md) mit Azure Machine Learning

+ [Tutorial: Bereitstellen eines Bildklassifizierungsmodells in Azure Container Instances](tutorial-deploy-models-with-aml.md)

+ [Repository mit End-to-End-Beispielen für MLOps](https://github.com/microsoft/MLOps)

+ [CI/CD von ML-Modellen mit Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)

+ Erstellen von Clients, die ein [bereitgestelltes Modell nutzen](how-to-consume-web-service.md)

+ [Bedarfsorientiertes Machine Learning](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Repository mit bewährten Methoden und Referenzarchitekturen für Azure KI](https://github.com/microsoft/AI)