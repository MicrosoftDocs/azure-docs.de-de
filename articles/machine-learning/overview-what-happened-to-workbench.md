---
title: Was ist mit Workbench passiert?
titleSuffix: Azure Machine Learning
description: Azure Machine Learning ist eine integrierte Data Science-Lösung zum Modellieren und Bereitstellen von ML-Anwendungen in der Cloud. Das Workbench-Feature wurde eingestellt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 03/05/2020
ms.openlocfilehash: fb35cce46a8448b744e8f7b3d26f2ca6a166f831
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541214"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>Was ist mit der Azure Machine Learning Workbench geschehen?

Die Azure Machine Learning Workbench-Anwendung und einige andere frühe Features sind veraltet und wurden im Release von **September 2018** ersetzt, um den Weg für eine verbesserte [Architektur](concept-azure-machine-learning-architecture.md) frei zu machen.

De Version enthält viele wichtige Updates, die durch Feedback von Kunden veranlasst wurden, um Ihr Benutzererlebnis zu verbessern. Die Kernfunktionen von der Experimentausführung bis hin zur Modellimplementierung hat sich nicht geändert. Jetzt können Sie jedoch das stabile <a href="/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Python SDK</a>, das R SDK und die [Azure-Befehlszeilenschnittstelle](reference-azure-machine-learning-cli.md) für Ihre Machine Learning-Aufgaben und -Pipelines verwenden.

Die meisten der in der früheren Version von Azure Machine Learning erstellten Artefakte sind in Ihrem eigenen lokalen Speicher oder in Cloudspeicher gespeichert. Diese Artefakte werden nie verloren gehen.

In diesem Artikel erfahren Sie, was sich geändert hat und welche Auswirkungen das auf Ihre vorhandenen Arbeit mit Azure Machine Learning Workbench und dessen APIs hat.

>[!Warning]
>Dieser Artikel ist nicht für Benutzer von Azure Machine Learning Studio bestimmt. Er richtet sich an Kunden von Azure Machine Learning, die die Workbench-Anwendung (Vorschau) installiert haben und/oder über Experimentier- und Modellverwaltungskonten (Vorschau) verfügen.


## <a name="what-changed"></a>Was hat sich geändert?

Die neueste Version von Azure Machine Learning umfasst folgende Funktionen:
+ Ein [vereinfachtes Azure-Ressourcenmodell](concept-azure-machine-learning-architecture.md).
+ Eine [neue Portal-Benutzeroberfläche](how-to-track-experiments.md) zum Verwalten Ihrer Experimente und Compute-Ziele.
+ Ein neues, umfangreicheres Python-<a href="/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK</a>.
+ Die neue, erweiterte [Azure CLI-Erweiterung](reference-azure-machine-learning-cli.md) für Machine Learning.

Die [Architektur](concept-azure-machine-learning-architecture.md) wurde im Hinblick auf leichte Bedienbarkeit umgestaltet. Statt mehrerer Azure-Ressourcen und -Konten benötigen Sie nur einen [Azure Machine Learning-Arbeitsbereich](concept-workspace.md). Arbeitsbereiche können schnell im [Azure-Portal](how-to-manage-workspace.md) erstellt werden. Durch die Verwendung eines Arbeitsbereichs können mehrere Benutzer Computeziele für Training und Bereitstellung, Modellexperimente, Docker-Images, bereitgestellte Modelle usw. speichern.

Zwar gibt es in der aktuellen Version neue, verbesserte CLI- und SDK-Clients, die eigentliche Workbench-Desktopanwendung wurde aber eingestellt. Experimente können im [Arbeitsbereichdashboard von Azure Machine Learning Studio](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal) verwaltet werden. Verwenden Sie das Dashboard, um Ihren Experimentverlauf abzurufen, die an Ihren Arbeitsbereich angefügten Computeziele zu verwalten, Ihre Modelle und Docker-Images zu verwalten und sogar Webdienste bereitzustellen.

<a name="timeline"></a>

## <a name="support-timeline"></a>Supportzeitachse

Am 9. Januar 2019 wurden der Support für Machine Learning Workbench-, Azure Machine Learning-Experimentieren- und Modellverwaltung-Konten sowie das zugehörige SDK und die CLI eingestellt.

Alle aktuellen Funktionen sind bei Verwendung dieses <a href="/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK</a>, der [CLI](reference-azure-machine-learning-cli.md) und des [Portals](how-to-manage-workspace.md) verfügbar.

## <a name="what-about-run-histories"></a>Wie sieht es mit Ausführungsverläufen aus?

Der Zugriff auf ältere Ausführungsverläufe ist nicht mehr möglich, Sie können Ihre Ausführungen in der aktuellen Version jedoch weiterhin anzeigen.

Ausführungsverläufe heißen jetzt **Experimente**. Sie können die Experimente Ihres Modells sammeln und sie mithilfe des SDK oder der Befehlszeilenschnittstelle oder unter Verwendung von Azure Machine Learning Studio untersuchen.

Das Arbeitsbereich-Dashboard im Portal wird nur in den Browsern Microsoft Edge, Chrome und Firefox unterstützt:

[![Online-Portal](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

Beginnen Sie, Ihre Modelle mithilfe des neuen CLIs und SDKs zu trainieren und ihre Ausführungsverläufe mit diesen nachzuverfolgen. Wie das geht, können Sie mit dem [Tutorial: Trainieren von Modellen mit Azure Machine Learning](tutorial-train-models-with-aml.md) lernen.

## <a name="will-projects-persist"></a>Bleiben Projekte dauerhaft erhalten?

Sie verlieren weder Code noch Arbeit. In der älteren Version stellen Projekte Cloudentitäten mit einem lokalen Verzeichnis dar. In der aktuellen Version fügen Sie lokale Verzeichnisse mithilfe einer lokalen Konfigurationsdatei an den Azure Machine Learning-Arbeitsbereich an. Hier finden Sie ein [Diagramm der aktuellen Architektur](concept-azure-machine-learning-architecture.md).

Ein großer Teil des Projektinhalts befindet sich bereits auf Ihrem lokalen Computer. Daher müssen Sie lediglich eine Konfigurationsdatei im betreffenden Verzeichnis erstellen und in Ihrem Code auf sie verweisen, um ihn mit Ihrem Arbeitsbereich zu verbinden. Um das lokale Verzeichnis, das Ihre Dateien und Skripts enthält, weiterhin zu verwenden, geben Sie den Namen des Verzeichnisses im Python-Befehl ['experiment.submit'](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py) ein. Alternativ hierzu können Sie auch den CLI-Befehl `az ml project attach` verwenden.  Beispiel:
```python
run = exp.submit(source_directory=script_folder,
                 script='train.py', run_config=run_config_system_managed)
```

[Erstellen Sie einen Arbeitsbereich](how-to-manage-workspace.md), um loszulegen.

## <a name="what-about-my-registered-models-and-images"></a>Was geschieht mit meinen registrierten Modellen und Images?

Die Modelle, die Sie in Ihrer alten Modellregistrierung registriert haben, müssen zu Ihrem neuen Arbeitsbereich migriert werden, wenn Sie sie weiterhin verwenden möchten. Um Ihre Modelle zu migrieren, laden Sie die Modelle herunter, und registrieren Sie sie erneut in Ihrem neuen Arbeitsbereich.

Die Images, die Sie in Ihrer alten Imageregistrierung erstellt haben, können nicht direkt in den neuen Arbeitsbereich migriert werden. In den meisten Fällen kann das Modell bereitgestellt werden, ohne ein Image zu erstellen. Bei Bedarf können Sie ein Image für das Modell im neuen Arbeitsbereich erstellen. Weitere Informationen finden Sie unter [Verwalten, Bereitstellen und Überwachen von Modellen mit Azure Machine Learning Services](concept-model-management-and-deployment.md).

## <a name="what-about-deployed-web-services"></a>Was ist mit bereitgestellten Webdiensten?

Da die alte CLI jetzt nicht mehr unterstützt wird, können Sie weder Modelle erneut bereitstellen noch die Webdienste verwalten, die Sie ursprünglich mit Ihrem Modellverwaltung-Konto bereitgestellt haben. Diese Webdienste funktionieren jedoch weiterhin, so lange der Azure Container Service (ACS) noch unterstützt wird.

In der aktuellen Version werden Modelle als Webdienste in Azure Container Instances (ACI) oder Azure Kubernetes Service-Clustern (AKS) bereitgestellt. Darüber hinaus sind Bereitstellungen in FPGAs und an Azure IoT Edge möglich.

Weitere Informationen finden Sie in diesen Artikeln:
+ [Bereitstellen von Modellen mit dem Azure Machine Learning-Dienst](how-to-deploy-and-where.md)
+ [Tutorial: Bereitstellen von Modellen mit Azure Machine Learning](tutorial-deploy-models-with-aml.md)

## <a name="next-steps"></a>Nächste Schritte

Informationen zur [aktuellen Architektur für Azure Machine Learning](concept-azure-machine-learning-architecture.md).

Eine Übersicht über den Dienst finden Sie unter [Was ist Azure Machine Learning?](overview-what-is-azure-ml.md).

Erstellen Sie Ihr erstes Experiment mit Ihrer bevorzugten Methode:

  + [Verwenden einer eigenen Umgebung](tutorial-1st-experiment-sdk-setup-local.md)
  + [Verwenden von Python-Notebooks](tutorial-1st-experiment-sdk-setup.md)
  + [Verwenden von R Markdown](tutorial-1st-r-experiment.md) 
  + [Verwenden von automatisiertem maschinellen Lernen](tutorial-designer-automobile-price-train-score.md) 
  + [Verwenden der Drag & Drop-Funktionen des Designers](tutorial-first-experiment-automated-ml.md) 
  + [Verwenden der ML-Erweiterung für die CLI](tutorial-train-deploy-model-cli.md)
