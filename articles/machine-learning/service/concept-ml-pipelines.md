---
title: 'Pipelines: Optimieren von Machine Learning-Workflows'
titleSuffix: Azure Machine Learning service
description: In diesem Artikel lernen Sie die Machine Learning-Pipelines, die Sie mit dem Azure Machine Learning SDK für Python erstellen können, und die Vorteile der Verwendung von Pipelines kennen. Machine Learning-Pipelines werden von Data Scientists verwendet, um Workflows für das maschinelle Lernen zu erstellen, zu optimieren und zu verwalten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: f49b384f6f943e8c6767a6133a835011bc1e6bac
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059330"
---
# <a name="build-reusable-ml-pipelines-in-azure-machine-learning-service"></a>Erstellen wiederverwendbarer Machine Learning-Pipelines im Azure Machine Learning Service

In diesem Artikel lernen Sie die Machine Learning-Pipelines, die Sie mit dem Azure Machine Learning SDK für Python erstellen können, und die Vorteile der Verwendung von Pipelines kennen.

## <a name="what-are-machine-learning-pipelines"></a>Einführung in Machine Learning-Pipelines

Mithilfe von Pipelines für maschinelles Lernen können Datenanalysten, Dateningenieure und IT-Experten an den Schritten für Folgendes zusammenarbeiten:
+ Datenaufbereitung, z.B. Normalisierung und Transformation
+ Modelltraining
+ Modellauswertung
+ Bereitstellung 

Das folgende Diagramm zeigt eine Beispielpipeline:

![Machine Learning-Pipelines im Azure Machine Learning Service](./media/concept-ml-pipelines/pipelines.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Welche Azure-Pipelinetechnologie sollte ich verwenden?

Die Azure-Cloud bietet mehrere andere Pipelines, die jeweils einem anderen Zweck dienen. Die folgende Tabelle listet die verschiedenen Pipelines und ihren Verwendungszweck auf:

| Pipeline | Funktionsbeschreibung | Kanonische Pipe |
| ---- | ---- | ---- |
| Azure Machine Learning-Pipelines | Definiert wiederverwendbare Machine Learning-Workflows, die als Vorlage für Ihre Machine Learning-Szenarien verwendet werden können. | Daten -> Modell |
| [Azure Data Factory-Pipelines](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Gruppieren Datenverschiebungs-, Transformations- und Steuerungsaktivitäten, die für das Ausführen einer Aufgabe erforderlich sind.  | Daten -> Daten |
| [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Continuous Integration und Delivery Ihrer Anwendung auf jeder Plattform und in jeder Cloud  | Code -> App/Dienst |

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Erstellen von Pipelines mit Azure Machine Learning

Mit dem [Azure Machine Learning SDK für Python](#the-python-sdk-for-pipelines) können Sie Machine Learning-Pipelines erstellen sowie einzelne Pipelineausführungen übermitteln und nachverfolgen.

Mit Pipelines können Sie Ihren Workflow durch Einfachheit, Geschwindigkeit, Portabilität und Wiederverwendung optimieren. Beim Erstellen von Pipelines mit Azure Machine Learning können Sie sich auf Ihre Fachkenntnisse (Machine Learning) und nicht auf die Infrastruktur konzentrieren.

Da verschiedene Schritte verwendet werden, können Sie nur die Schritte erneut ausführen, die Sie zum Optimieren und Testen Ihres Workflows benötigen. Ein Schritt ist eine Computeeinheit in der Pipeline. Wie im vorhergehenden Diagramm dargestellt, kann das Vorbereiten der Daten viele Schritte umfassen. Zu diesen Schritten zählen unter anderem Normalisierung, Transformation, Überprüfung und Featurebereitstellung. Datenquellen und Zwischendaten werden in der gesamten Pipeline wiederverwendet, um Rechenzeit und Ressourcen zu sparen. 

Nachdem die Pipeline entworfen wurde, wird ihr Trainingsprozess in der Regel weiter optimiert. Wenn Sie eine Pipeline erneut ausführen, springt die Ausführung zu den Schritten, die wiederholt werden müssen, z.B. zu einem aktualisierten Trainingsskript, und überspringt, was sich nicht geändert hat. Das gleiche gilt für unveränderte Skripts, die zum Ausführen des Schritts verwendet werden. 

Mit Azure Machine Learning können Sie verschiedene Toolkits und Frameworks wie PyTorch oder TensorFlow für jeden Pipelineschritt verwenden. Azure koordiniert zwischen den verschiedenen [Computezielen](concept-azure-machine-learning-architecture.md), die Sie verwenden, sodass Ihre Zwischendaten problemlos für die nachgelagerten Computeziele freigegeben werden können. 

Sie können [die Metriken für Ihre Pipelineexperimente direkt im Azure-Portal verfolgen](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments). 

## <a name="key-advantages"></a>Hauptvorteile

Die Übersicht enthält die wichtigsten Argumente, die für das Erstellen von Pipelines für die Workflows des maschinellen Lernens sprechen:

|Vorteil|BESCHREIBUNG|
|:-------:|-----------|
|**Unbeaufsichtigte&nbsp;Ausführung**|Planen Sie mehrere Schritte, die zuverlässig und unbeaufsichtigt parallel oder nacheinander ausgeführt werden. Da die Datenaufbereitung und das Modellieren Tage oder Wochen dauern können, können Sie sich nun auf andere Aufgaben konzentrieren, während die Pipeline ausgeführt wird. |
|**Gemischte und verschiedenen Computeressourcen**|Verwenden Sie mehrere Pipelines, die zuverlässig über heterogene und skalierbare Computeressourcen und Speicher koordiniert werden. Einzelne Pipelineschritte können auf verschiedenen Computezielen ausgeführt werden, z. B. HDInsight, Data Science VM mit GPU und Databricks. Dies ermöglicht eine effizientere Nutzung der verfügbaren Computeoptionen.|
|**Wiederverwendbarkeit**|Sie können Pipelines für bestimmte Szenarien wie erneutes Training und Batchbewertung als Vorlagen verwenden. Sie können von externen Systemen über einfache REST-Aufrufe ausgelöst werden.|
|**Nachverfolgung und Versionierung**|Statt Daten- und Ergebnispfade bei der Iteration manuell zu verfolgen, verwenden Sie das Pipelines SDK, um Ihre Datenquellen, Eingaben und Ausgaben explizit zu benennen und Versionen zu verwalten. Sie können Skripts und Daten auch separat verwalten, um die Produktivität zu steigern.|

## <a name="the-python-sdk-for-pipelines"></a>Python SDK für Pipelines

Verwenden Sie Python, um Ihre Machine Learning-Pipelines zu erstellen. Das Azure Machine Learning SDK bietet imperative Konstrukte zum Sequenzieren und Parallelisieren der Pipelineschritte, wenn keine Datenabhängigkeit besteht. Sie können mit ihm in Jupyter-Notebooks oder in einer anderen bevorzugten integrierten Entwicklungsumgebung (IDE) interagieren. 

Mit deklarativen Datenabhängigkeiten können Sie Ihre Aufgaben optimieren. Das SDK enthält ein Framework von vordefinierten Modulen für allgemeine Aufgaben wie Datenübertragung und Modellveröffentlichung. Sie können das Framework erweitern, um eigene Konventionen zu modellieren. Implementieren Sie dafür benutzerdefinierte Schritte, die über Pipelines hinweg wiederverwendbar sind. Sie können Computeziele und Speicherressourcen ebenfalls direkt über das SDK verwalten.

Sie können Pipelines als Vorlagen speichern und auf einem REST-Endpunkt bereitstellen, damit Sie Batchbewertungen und erneute Trainingsaufträge planen können.

In den [Referenzdokumenten für das Python SDK für Pipelines](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) und dem Notebook im nächsten Abschnitt erfahren Sie, wie Sie Ihre eigenen Pipelines erstellen.

## <a name="example-notebooks"></a>Beispielnotebooks
 
Die folgenden Notebooks veranschaulichen Pipelines mit Azure Machine Learning: [how-to-use azureml/machine-learning-pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines).
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [Ihre erste Pipeline erstellen](how-to-create-your-first-pipeline.md).
