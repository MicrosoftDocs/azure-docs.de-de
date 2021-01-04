---
title: Überwachen und Anzeigen von ML-Ausführungsprotokollen und -metriken
titleSuffix: Azure Machine Learning
description: Verwenden Sie Jupyter-Widgets und Azure Machine Learning Studio, um Ihre ML-Experimente zu überwachen und Ausführungsmetriken anzuzeigen.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 845160d92100a27c32f16eddcd1f36e9e8624e80
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360597"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>Überwachen und Anzeigen von ML-Ausführungsprotokollen und -metriken

In diesem Artikel erfahren Sie, wie Sie Azure Machine Learning-Ausführungen überwachen und die zugehörigen Protokolle anzeigen. Bevor Sie Protokolle anzeigen können, müssen Sie sie zuerst aktivieren. Weitere Informationen hierzu finden Sie unter [Aktivieren der Protokollierung in Azure ML-Trainingsausführungen](how-to-track-experiments.md).

Protokolle helfen bei der Diagnose von Fehlern und Warnungen und beim Nachverfolgen von Leistungsmetriken wie Parametern und Modellgenauigkeit. In diesem Artikel erfahren Sie, wie Sie Protokolle über die folgenden Methoden anzeigen:

> [!div class="checklist"]
> * Überwachen von Ausführungen in Studio
> * Überwachen von Ausführungen mit dem Jupyter Notebook-Widget
> * Überwachen von Ausführungen des automatisierten maschinellen Lernens
> * Anzeigen von Ausgabeprotokollen nach Abschluss
> * Anzeigen von Ausgabeprotokollen in Studio

Allgemeine Informationen zum Verwalten von Experimenten finden Sie unter [Starten, Überwachen und Abbrechen von Trainingsausführungen](how-to-manage-runs.md).

## <a name="monitor-runs-in-the-studio"></a>Überwachen von Ausführungen in Studio

Führen Sie die folgenden Schritte aus, um die Ausführungen für ein bestimmtes Computeziel in Ihrem Browser zu überwachen:

1. Wählen Sie in [Azure Machine Learning Studio](https://ml.azure.com/) Ihren Arbeitsbereich aus, und wählen Sie dann links auf der Seite __Compute__ aus.

1. Wählen Sie __Trainingscluster__ aus, um eine Liste der für das Training verwendeten Computeziele anzuzeigen. Wählen Sie dann den Cluster aus.

    ![Auswählen des Trainingsclusters](./media/how-to-track-experiments/select-training-compute.png)

1. Wählen Sie __Ausführungen__ aus. Die Liste der Ausführungen wird angezeigt, die diesen Cluster verwenden. Zum Anzeigen von Details für eine bestimmte Ausführung verwenden Sie den Link in der Spalte __Ausführung__. Zum Anzeigen von Details für ein bestimmtes Experiment verwenden Sie den Link in der Spalte __Experiment__.

    ![Auswählen von Ausführungen für Trainingscluster](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Da Trainingscomputeziele freigegebene Ressourcen sind, können zu jedem Zeitpunkt mehrere Ausführungen in die Warteschlange eingereiht oder aktiv sein.
    > 
    > Eine Ausführung kann untergeordnete Ausführungen enthalten, sodass ein Trainingsauftrag zu mehreren Einträgen führen kann.

Sobald eine Ausführung abgeschlossen ist, wird sie nicht mehr auf dieser Seite angezeigt. Informationen zu abgeschlossenen Ausführungen finden Sie in Studio im Abschnitt __Experimente__. Wählen Sie dazu das Experiment und die Ausführung aus. Weitere Informationen finden Sie im Abschnitt [Anzeigen von Metriken für abgeschlossene Ausführungen](#view-the-experiment-in-the-web-portal).

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>Überwachen von Ausführungen mit dem Jupyter Notebook-Widget

Wenn Sie Ausführungen mithilfe der **ScriptRunConfig**-Methode übermitteln, können Sie den Fortschritt der Ausführung mit dem [Jupyter-Widget](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py) anzeigen. Ebenso wie die Übermittlung der Ausführung ist das Widget asynchron und stellt alle 10 bis 15 Sekunden Liveupdates bereit, bis der Auftrag abgeschlossen ist.

Zeigen Sie das Jupyter-Widget an, während Sie darauf warten, dass die Ausführung abgeschlossen wird.
    
```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

![Screenshot des Jupyter-Notebook-Widgets](./media/how-to-track-experiments/run-details-widget.png)

Sie können auch einen Link zur gleichen Anzeige in Ihrem Arbeitsbereich abrufen.

```python
print(run.get_portal_url())
```

## <a name="monitor-automated-machine-learning-runs"></a>Überwachen von Ausführungen des automatisierten maschinellen Lernens

Um bei Ausführungen des automatisierten maschinellen Lernens auf die Diagramme aus einer vorherigen Ausführung zuzugreifen, ersetzen Sie `<<experiment_name>>` durch den Namen des entsprechenden Experiments:

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![Jupyter Notebook-Widget für automatisiertes Machine Learning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)

## <a name="show-output-upon-completion"></a>Anzeigen der Ausgabe nach Abschluss

Bei der Verwendung von **ScriptRunConfig** können Sie ```run.wait_for_completion(show_output = True)``` einsetzen,um anzuzeigen, wenn das Modelltraining abgeschlossen ist. Mithilfe des ```show_output```-Flags erhalten Sie eine ausführlichen Ausgabe. Weitere Informationen finden Sie im Abschnitt „ScriptRunConfig“ unter [Aktivieren der Protokollierung](how-to-track-experiments.md#scriptrun-logs).

<a id="queryrunmetrics"></a>
## <a name="query-run-metrics"></a>Metriken der Abfrageausführung

Mit ```run.get_metrics()``` können Sie die Metriken eines trainierten Modells anzeigen. Beispielsweise können Sie im obigen Beispiel das beste Modell ermitteln, indem Sie nach dem Modell mit dem niedrigsten mittleren quadratischen Fehlerwert (Mean Square Error, MSE) suchen.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-run-records-in-the-studio"></a>Anzeigen von Ausführungsaufzeichnungen in Studio

Sie können abgeschlossene Ausführungsaufzeichnungen, einschließlich protokollierter Metriken, in [Azure Machine Learning Studio](https://ml.azure.com) durchsuchen.

Navigieren Sie zur Registerkarte **Experimente**. Um alle Ihre Ausführungen in Ihrem Arbeitsbereich experimentübergreifend anzuzeigen, wählen Sie die Registerkarte **Alle Ausführungen** aus. Sie können einen Drilldown für die Ausführungen für bestimmte Experimente ausführen, indem Sie den Experimentfilter auf der oberen Menüleiste anwenden. 

Wählen Sie für die Ansicht einzelner Experimente die Registerkarte **Alle Experimente** aus. Auf dem Dashboard für die Experimentausführung können Sie nachverfolgte Metriken und Protokolle für jede Ausführung sehen. 

Führen Sie einen Drilldown für eine bestimmte Ausführung aus, um deren Ausgaben oder Protokolle anzuzeigen, oder laden Sie die Momentaufnahme des Experiments herunter, um den Experimentordner für andere freigeben zu können.

Sie können auch die Tabelle der Ausführungsliste bearbeiten, um mehrere Ausführungen auszuwählen und den letzten, den minimalen oder den maximalen protokollierten Wert für Ihre Ausführungen anzuzeigen. Passen Sie Ihre Diagramme an, um die protokollierten Metrikwerte und Aggregate über mehrere Ausführungen zu vergleichen.

![Ausführungsdetails im Azure Machine Learning-Studio](media/how-to-track-experiments/experimentation-tab.gif)

### <a name="format-charts-in-the-studio"></a>Formatieren von Diagrammen in Studio

Verwenden Sie die folgenden Methoden in den Protokollierungs-APIs, um die Visualisierung Ihrer Metriken durch Studio anzupassen.

|Protokollierter Wert|Beispielcode| Formatieren im Portal|
|----|----|----|
|Protokollieren eines Arrays mit numerischen Werten| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Liniendiagramm mit nur einer Variablen|
|Protokollieren eines einzelnen numerischen Werts mit mehrfacher Verwendung desselben Metriknamens (z.B. in einer for-Schleife)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Liniendiagramm mit nur einer Variablen|
|Wiederholtes Protokollieren einer Zeile mit zwei numerischen Spalten|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Liniendiagramm mit zwei Variablen|
|Protokollieren einer Tabelle mit zwei numerischen Spalten|`run.log_table(name='Sine Wave', value=sines)`|Liniendiagramm mit zwei Variablen|


## <a name="next-steps"></a>Nächste Schritte

Probieren Sie diese nächsten Schritte aus, um zu erfahren, wie Sie Azure Machine Learning verwenden können:

* Erfahren Sie, wie Sie [Experimente nachverfolgen und Protokolle im Azure Machine Learning-Designer aktivieren](how-to-track-designer-experiments.md).

* Ein Beispiel für die Registrierung des besten Modells und dessen Bereitstellung finden Sie im Tutorial [Trainieren eines Bildklassifizierungsmodells mit Azure Machine Learning](tutorial-train-models-with-aml.md).