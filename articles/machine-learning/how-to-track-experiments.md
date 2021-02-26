---
title: Protokollieren von ML-Experimenten und -Metriken
titleSuffix: Azure Machine Learning
description: Aktivieren Sie die Protokollierung für Ihre ML-Trainingsausführungen, um Ausführungsmetriken in Echtzeit zu überwachen und Fehler und Warnungen zu diagnostizieren.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 9e5f64d9ef61a272da488ad70e690db4c07ddccc
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625076"
---
# <a name="enable-logging-in-ml-training-runs"></a>Aktivieren der Protokollierung in ML-Trainingsausführungen


Mit dem Python-SDK für Azure Machine Learning können Sie Echtzeitinformationen protokollieren, indem Sie sowohl das standardmäßige Python-Protokollierungspaket als auch SDK-spezifische Funktionen verwenden. Sie können lokal protokollieren und Protokolle an Ihren Arbeitsbereich im Portal senden.

Protokolle helfen bei der Diagnose von Fehlern und Warnungen und beim Nachverfolgen von Leistungsmetriken wie Parametern und Modellleistung. In diesem Artikel erfahren Sie, wie Sie die Protokollierung in den folgenden Szenarien aktivieren:

> [!div class="checklist"]
> * Interaktive Trainingssitzungen
> * Übermitteln von Trainingsaufträgen mithilfe von ScriptRunConfig
> * Native `logging`-Einstellungen für Python
> * Protokollierung von zusätzlichen Quellen


> [!TIP]
> In diesem Artikel erfahren Sie, wie Sie den Modelltrainingsprozess überwachen. Wenn Sie sich für die Überwachung der Nutzung und Ereignisse von Azure Machine Learning (z. B. Kontingente, abgeschlossene Trainingsausführungen oder abgeschlossene Modellimplementierungen) interessieren, helfen Ihnen die Informationen im Artikel [Überwachen von Azure Machine Learning](monitor-azure-machine-learning.md) weiter.

## <a name="data-types"></a>Datentypen

Sie können mehrere Datentypen protokollieren, einschließlich skalarer Werte, Listen, Tabellen, Images, Verzeichnisse und mehr. Weitere Informationen und Python-Codebeispiele für verschiedene Datentypen finden Sie auf der [Referenzseite für die Run-Klasse](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py).

## <a name="interactive-logging-session"></a>Interaktive Protokollierungssitzung

Interaktive Protokollierungssitzungen werden in der Regel in Notebook-Umgebungen verwendet. Die Methode [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truestart-logging--args----kwargs-) startet eine interaktive Protokollierungssitzung. Alle Metriken, die während der Sitzung protokolliert werden, werden der Ausführungsaufzeichnung im Experiment hinzugefügt. Die Methode [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecomplete--set-status-true-) beendet die Sitzungen und markiert die Ausführung als abgeschlossen.

## <a name="scriptrun-logs"></a>ScriptRun-Protokolle

In diesem Abschnitt erfahren Sie, wie Sie Protokollierungscode innerhalb von Ausführungen hinzufügen, die beim Konfigurieren mit ScriptRunConfig erstellt werden. Sie können die Klasse [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) verwenden, um Skripts und Umgebungen für wiederholbare Ausführungen zu kapseln. Sie können diese Option auch verwenden, um ein visuelles Jupyter Notebooks-Widget zur Überwachung anzuzeigen.

Dieses Beispiel führt einen Parameter-Sweep über Alphawerte durch und erfasst die Ergebnisse mit der Methode [run.log()](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truelog-name--value--description----).

1. Erstellen Sie ein Trainingsskript, das die Protokollierungslogik (`train.py`) enthält.

   [!code-python[](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Übermitteln Sie das ```train.py```-Skript, um in einer vom Benutzer verwalteten Umgebung ausgeführt zu werden. Der gesamte Skriptordner wird zum Training übermittelt.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)]


   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

    Der Parameter `show_output` aktiviert die ausführliche Protokollierung, mit der Sie Details aus dem Trainingsprozess sowie Informationen zu allen entfernten Ressourcen oder Computezielen anzeigen können. Verwenden Sie den folgenden Code, um die ausführliche Protokollierung zu aktivieren, wenn Sie das Experiment übermitteln.

```python
run = exp.submit(src, show_output=True)
```

Sie können denselben Parameter auch in der `wait_for_completion`-Funktion der resultierende Ausführung verwenden.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Native Python-Protokollierung

Einige Protokolle im SDK können einen Fehler enthalten, der Sie anweist, den Protokolliergrad auf DEBUG festzulegen. Um den Protokolliergrad festzulegen, fügen Sie Ihrem Skript den folgenden Code hinzu.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>Zusätzliche Protokollierungsquellen

Azure Machine Learning kann während des Trainings auch Informationen aus anderen Quellen protokollieren, etwa aus automatisierten Machine Learning-Ausführungen oder aus Docker-Containern, in denen der Auftrag ausgeführt wird. Diese Protokolle sind nicht dokumentiert, aber wenn Probleme auftreten und Sie sich an den Microsoft-Support wenden, können diese Protokolle möglicherweise bei der Problembehandlung verwendet werden.

Informationen zur Protokollierung von Metriken im Azure Machine Learning-Designer finden Sie unter [Protokollieren von Metriken im Designer](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>Beispielnotebooks

Die folgenden Notebooks verdeutlichen die Konzepte in diesem Artikel:
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie diese Artikel, um mehr über die Verwendung von Azure Machine Learning zu erfahren:

* Erfahren Sie, wie Sie [Metriken in Azure Machine Learning-Designer protokollieren](how-to-track-designer-experiments.md).

* Ein Beispiel für die Registrierung des besten Modells und dessen Bereitstellung finden Sie im Tutorial [Trainieren eines Bildklassifizierungsmodells mit Azure Machine Learning](tutorial-train-models-with-aml.md).
