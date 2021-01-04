---
title: Was ist Azure Machine Learning?
description: Azure Machine Learning ist eine integrierte Data Science-Lösung für Data Scientists und MLops zum Modellieren und Bereitstellen von ML-Anwendungen in der Cloud.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 11/04/2019
ms.custom: devx-track-python
ms.openlocfilehash: 85080c018749f9aab716eff0a4527363da987007
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620476"
---
# <a name="what-is-azure-machine-learning"></a>Was ist Azure Machine Learning?

In diesem Artikel wird Azure Machine Learning beschrieben. Hierbei handelt es sich um eine cloudbasierte Umgebung, die Sie zum Trainieren, Bereitstellen, Automatisieren, Verwalten und Nachverfolgen von ML-Modellen verwenden können. 

Azure Machine Learning kann für alle Arten von maschinellem Lernen verwendet werden – von klassischem Machine Learning bis zu Deep Learning und für überwachtes und nicht überwachtes Lernen. Unabhängig davon, ob Sie das Schreiben von Python- oder R-Code mit dem SDK oder die Nutzung von Optionen ohne bzw. mit nur wenig Code in [Studio](#build-ml-models-in-the-studio) bevorzugen, können Sie in einem Azure Machine Learning-Arbeitsbereich Machine Learning- und Deep Learning-Modelle erstellen, trainieren und nachverfolgen. 

Beginnen Sie auf Ihrem lokalen Computer mit dem Training, und führen Sie dann eine Aufskalierung auf die Cloud durch. 

Der Dienst kann auch zusammen mit beliebten Open-Source-Tools für Deep Learning und vertiefendes Lernen (etwa PyTorch, TensorFlow, scikit-learn und Ray RLlib) genutzt werden. 

> [!VIDEO https://channel9.msdn.com/Events/Connect/Microsoft-Connect--2018/D240/player]

> [!Tip]
> **Kostenlose Testversion!**  Wenn Sie kein Azure-Abonnement besitzen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) noch heute aus. Sie erhalten dann eine Gutschrift, die Sie für Azure-Dienste einlösen können. Wenn das Guthaben aufgebraucht ist, können Sie das Konto behalten und weiterhin [kostenlose Azure-Dienste](https://azure.microsoft.com/free/) nutzen. Ihre Kreditkarte wird nur dann belastet, wenn Sie Ihre Einstellungen explizit ändern und mit der Berechnung von Gebühren einverstanden sind.


## <a name="what-is-machine-learning"></a>Was ist Machine Learning?

Machine Learning ist ein Data Science-Verfahren, mit dem Computer aus vorhandenen Daten lernen können, um zukünftiges Verhalten, Ergebnisse und Trends vorherzusagen. Durch den Einsatz von maschinellem Lernen können Computer lernen, ohne konkret programmiert worden zu sein.

Dank solcher Vorhersagen oder Prognosen aus Machine Learning können Apps und Geräte „intelligenter“ werden. Wenn Sie beispielsweise online einkaufen, trägt maschinelles Lernen dazu bei, dass Ihnen anhand der gekauften Produkte weitere Produkte empfohlen werden, die Ihnen gefallen könnten. Ein weiteres Beispiel ist die Nutzung Ihrer Kreditkarte. Dabei wird mithilfe von maschinellem Lernen die Transaktion mit einer Transaktionsdatenbank verglichen, wodurch Betrugsfälle erkannt werden können. Auch wenn ein automatischer Staubsauger ein Zimmer saugt, wird mit maschinellem Lernen entschieden, ob die Arbeit erledigt ist.

## <a name="machine-learning-tools-to-fit-each-task"></a>Passende Machine Learning-Tools für alle Aufgaben 

Azure Machine Learning verfügt über alle Tools, die Entwickler und Data Scientists für ihre Machine Learning-Workflows benötigen, z. B.:
+ [Azure Machine Learning-Designer](tutorial-designer-automobile-price-train-score.md): Fügen Sie Module per Drag & Drop ein, um Ihre Experimente zu erstellen, und stellen Sie anschließend Pipelines bereit.

+ Jupyter-Notebooks: Verwenden Sie unsere [Beispielnotebooks](https://github.com/Azure/MachineLearningNotebooks), oder erstellen Sie Ihre eigenen Notebooks, um unsere <a href="/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK für Python</a>-Beispiele für Ihre Machine Learning-Zwecke zu nutzen. 

+ R-Skripts oder Notebooks, in denen Sie das <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">SDK für R</a> zum Schreiben Ihres eigenen Codes verwenden (oder die R-Module im Designer).

+ + Der [Many Models Solution Accelerator](https://aka.ms/many-models) (Preview) (Projektmappenbeschleuniger für viele Modelle (Vorschau)) baut auf Azure Machine Learning auf und ermöglicht Ihnen Training, Betrieb und Verwaltung von hunderten oder sogar tausenden von Machine Learning-Modellen.

+ [Erweiterung für maschinelles Lernen für Visual Studio Code-Benutzer](tutorial-setup-vscode-extension.md)

+ [Machine Learning-CLI](reference-azure-machine-learning-cli.md)

+ Open-Source-Frameworks, z. B. PyTorch, TensorFlow, scikit-learn und viele mehr

+ [Vertiefendes Lernen](how-to-use-reinforcement-learning.md) mit Ray RLlib

Sie können sogar [MLflow zum Nachverfolgen von Metriken und Bereitstellen von Modellen](how-to-use-mlflow.md) oder Kubeflow zum [Erstellen von End-to-End-Workflowpipelines](https://www.kubeflow.org/docs/azure/) verwenden.

## <a name="build-ml-models-in-python-or-r"></a>Erstellen von ML-Modellen in Python oder R

Beginnen Sie auf Ihrem lokalen Computer mit dem Azure Machine Learning <a href="/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Python SDK</a> oder dem <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">R SDK</a> mit dem Training. Anschließend können Sie in die Cloud aufskalieren. 

Dank zahlreicher verfügbarer [Compute-Ziele](how-to-create-attach-compute-studio.md) wie Azure Machine Learning Compute und [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) sowie [Diensten für die erweiterte Hyperparameteroptimierung](how-to-tune-hyperparameters.md) können Sie mithilfe der Cloud schneller bessere Modelle erstellen.

Mit dem SDK können Sie auch [Modelltraining und -optimierung](tutorial-auto-train-models.md) automatisieren.

## <a name="build-ml-models-in-the-studio"></a>Erstellen von ML-Modellen in Studio

[Azure Machine Learning Studio](https://studio.azureml.net) ist ein Webportal in Azure Machine Learning, das Optionen mit wenig Code und ohne Code für das Training und die Bereitstellung von Modellen sowie für die Ressourcenverwaltung bereitstellt. Die Integration von Studio in das Azure Machine Learning SDK ermöglicht eine nahtlose Benutzererfahrung. Weitere Informationen finden Sie unter [Was ist Azure Machine Learning Studio?](overview-what-is-machine-learning-studio.md).

+ **Azure Machine Learning-Designer**

  Verwenden Sie den [Designer](concept-designer.md) zum Trainieren und Bereitstellen von Machine Learning-Modellen, ohne Code schreiben zu müssen. Informationen zu den ersten Schritten finden Sie im [Tutorial zum Designer](tutorial-designer-automobile-price-train-score.md). 

  ![Animiertes GIF der Drag & Drop-Schnittstelle des Azure Machine Learning-Designers](media/concept-designer/designer-drag-and-drop.gif)

+ **Nachverfolgen von Experimenten**

  Erfahren Sie, wie Sie [Data Science-Experimenten in Studio nachverfolgen und visualisieren](tutorial-first-experiment-automated-ml.md). 

    ![Ausführungsdetails in Azure Machine Learning Studio](media/how-to-track-experiments/experimentation-tab.gif)


+ **Und vieles mehr...**

  Besuchen Sie Azure Machine Learning Studio auf [ml.azure.com](https://studio.azureml.net). 


## <a name="mlops-deploy--lifecycle-management"></a>MLOps: Bereitstellung und Lebenszyklusverwaltung
Wenn Sie das richtige Modell haben, können Sie es ganz einfach in einem Webdienst, auf einem IoT-Gerät oder in Power BI verwenden. Weitere Informationen finden Sie im Artikel [Bereitstellen: wie und wo](how-to-deploy-and-where.md).

Anschließend können Sie Ihre bereitgestellten Modelle verwalten, indem Sie das [Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py), [Azure Machine Learning Studio](https://ml.azure.com) oder die [Machine Learning-CLI](reference-azure-machine-learning-cli.md) verwenden.

Diese Modelle können genutzt werden und Prognosen [in Echtzeit](how-to-consume-web-service.md) oder [asynchron](./tutorial-pipeline-batch-scoring-classification.md) (für große Datenmengen) zurückgeben.

Und dank fortschrittlicher[Machine Learning-Pipelines](concept-ml-pipelines.md) können Sie bei jedem einzelnen Schritt – von der Datenaufbereitung über Training und Bewertung von Modellen bis hin zur Bereitstellung – zusammenarbeiten. Pipelines ermöglichen Folgendes:

* Automatisieren des End-to-End-Prozesses für maschinelles Lernen in der Cloud
* Wiederverwenden von Komponenten und Wiederholen von Schritten nur bei Bedarf
* Verwenden verschiedener Computeressourcen in jedem Schritt
* Ausführen von Batchbewertungsaufgaben

Falls Sie Skripts zum Automatisieren Ihres Machine Learning-Workflows nutzen möchten, helfen Ihnen die Befehlszeilentools der [Machine Learning-CLI](reference-azure-machine-learning-cli.md) weiter. Hiermit können häufige Aufgaben erledigt werden, z. B. das Übermitteln einer Trainingsausführung oder das Bereitstellen eines Modells.

Informationen zu den ersten Schritten mit Azure Machine Learning finden Sie [hier](#next-steps).

## <a name="integration-with-other-services"></a>Integration in andere Dienste

Azure Machine Learning funktioniert mit anderen Diensten der Azure-Plattform und lässt sich auch in Open-Source-Tools wie Git und MLFlow integrieren.

+ Computeziele wie __Azure Kubernetes Service__, __Azure Container Instances__, __Azure Databricks__, __Azure Data Lake Analytics__ und __Azure HDInsight__. Weitere Informationen zu Computezielen finden Sie unter [Was sind Computeziele in Azure Machine Learning?](concept-compute-target.md).
+ __Azure Event Grid__. Weitere Informationen finden Sie unter [Nutzen von Azure Machine Learning-Ereignissen (Vorschauversion)](./how-to-use-event-grid.md).
+ __Azure Monitor__: Weitere Informationen finden Sie unter [Überwachen von Azure Machine Learning](monitor-azure-machine-learning.md).
+ Datenspeicher wie __Azure Storage-Konten__, __Azure Data Lake Storage__, __Azure SQL-Datenbank__, __Azure Database for PostgreSQL__ und __Azure Open Datasets__. Weitere Informationen finden Sie unter [Zugreifen auf Daten in Azure Storage-Diensten](how-to-access-data.md) und [Erstellen von Datasets mit Azure Open Datasets](how-to-create-register-datasets.md).
+ __Virtuelle Azure-Netzwerke:__ Weitere Informationen finden Sie unter [Übersicht zu Isolation und Datenschutz bei virtuellen Netzwerken](how-to-network-security-overview.md).
+ __Azure Pipelines__. Weitere Informationen finden Sie unter [Trainieren und Bereitstellen von Machine Learning-Modellen](/azure/devops/pipelines/targets/azure-machine-learning).
+ __Git-Repository-Protokolle:__ Weitere Informationen finden Sie unter [Git-Integration für Azure Machine Learning](concept-train-model-git-integration.md).
+ __MLFlow:__ Weitere Informationen finden Sie unter [Nachverfolgen von Metriken und Bereitstellen von Modellen mit MLflow und Azure Machine Learning (Vorschauversion)](how-to-use-mlflow.md). 
+ __Kubeflow:__ Weitere Informationen finden Sie unter [Erstellen von End-to-End-Workflowpipelines](https://www.kubeflow.org/docs/azure/).

### <a name="secure-communications"></a>Sichere Kommunikation

Das Azure Storage-Konto, die Computeziele und andere Ressourcen können innerhalb eines virtuellen Netzwerks gefahrlos zum Trainieren von Modellen sowie für Rückschlüsse verwendet werden. Weitere Informationen finden Sie unter [Übersicht zu Isolation und Datenschutz bei virtuellen Netzwerken](how-to-network-security-overview.md).

## <a name="next-steps"></a>Nächste Schritte

- Erstellen Sie Ihr erstes Experiment mit Ihrer bevorzugten Methode:
- + [Erste Schritte in Ihrer eigenen Entwicklungsumgebung](tutorial-1st-experiment-sdk-setup-local.md)
  + [Verwenden von Jupyter-Notebooks auf einer Compute-Instanz zum Trainieren und Bereitstellen von ML-Modellen](tutorial-1st-experiment-sdk-setup.md)
  + [Verwenden von R Markdown zum Trainieren und Bereitstellen von ML-Modellen](tutorial-1st-r-experiment.md) 
  + [Verwenden von automatisiertem Machine Learning zum Trainieren und Bereitstellen von ML-Modellen](tutorial-first-experiment-automated-ml.md) 
  + [Verwenden der Drag & Drop-Funktionen des Designers zum Trainieren und Bereitstellen](tutorial-designer-automobile-price-train-score.md) 
  + [Verwenden der Machine Learning-CLI zum Trainieren und Bereitstellen eines Modells](tutorial-train-deploy-model-cli.md)

- Erfahren Sie mehr über [Machine Learning-Pipelines](concept-ml-pipelines.md) zum Erstellen, Optimieren und Verwalten Ihrer Machine Learning-Szenarien.

- Lesen Sie den ausführlichen Artikel [Funktionsweise von Azure Machine Learning: Architektur und Konzepte](concept-azure-machine-learning-architecture.md).
