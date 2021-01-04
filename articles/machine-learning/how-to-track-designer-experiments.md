---
title: Protokollmetriken im Designer
titleSuffix: Azure Machine Learning
description: Überwachen Sie Ihre Azure Machine Learning-Designerexperimente. Aktivieren Sie die Protokollierung mithilfe des „Execute Python Script“-Moduls (Ausführen eines Python-Skripts), und zeigen Sie die protokollierten Ergebnisse in Studio an.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 11/25/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: c67415be38bc7b6fa48fd1046b51194c294df2f3
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030389"
---
# <a name="enable-logging-in-azure-machine-learning-designer-pipelines"></a>Aktivieren der Protokollierung in Azure Machine Learning-Designerpipelines


In diesem Artikel erfahren Sie, wie Sie Protokollierungscode zu Designerpipelines hinzufügen. Außerdem wird erläutert, wie Sie diese Protokolle mit dem Azure Machine Learning Studio-Webportal anzeigen.

Weitere Informationen zum Protokollieren von Metriken mithilfe der SDK-Erstellung finden Sie unter [Überwachen von Azure ML-Experimentausführungen und -metriken](how-to-track-experiments.md).

## <a name="enable-logging-with-execute-python-script"></a>Aktivieren der Protokollierung mit „Execute Python Script“ (Ausführen eines Python-Skripts)

Verwenden Sie das Modul __Execute Python Script__ (Ausführen eines Python-Skripts), um die Protokollierung in Designerpipelines zu aktivieren. Obwohl Sie mit diesem Workflow beliebige Werte protokollieren können, ist es besonders dann hilfreich, Metriken aus dem Modul __Evaluate Model__ (Bewerten eines Modells) zu protokollieren, wenn Sie die Modellleistung für mehrere Ausführungen überwachen möchten.

Im folgenden Beispiel wird gezeigt, wie Sie die mittlere quadratische Abweichung zweier trainierter Modelle mithilfe der Module „Evaluate Model“ (Bewerten eines Modells) und „Execute Python Script“ (Ausführen eines Python-Skripts) protokollieren.

1. Stellen Sie eine Verbindung vom Modul __Execute Python Script__ (Ausführen eines Python-Skripts) zur Ausgabe des Moduls __Evaluate Model__ (Bewerten eines Modells) her.

    ![Herstellen einer Verbindung vom Modul zum Ausführen des Python-Skripts zum Modul zum Evaluieren des Modells](./media/how-to-track-experiments/designer-logging-pipeline.png)

1. Fügen Sie den folgenden Code in den Code-Editor des Moduls __Execute Python Script__ (Ausführen eines Python-Skripts) ein, um die mittlere absolute Abweichung für Ihr trainiertes Modell zu protokollieren. Sie können ein ähnliches Muster verwenden, um einen beliebigen anderen Wert im Designer zu protokollieren:

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1=None, dataframe2=None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')
    
        from azureml.core import Run
    
        run = Run.get_context()
    
        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.
        parent_run = Run.get_context().parent
        
        # Log left output port result of Evaluate Model. This also works when evaluate only 1 model.
        parent_run.log(name='Mean_Absolute_Error (left port)', value=dataframe1['Mean_Absolute_Error'][0])
        # Log right output port result of Evaluate Model.
        parent_run.log(name='Mean_Absolute_Error (right port)', value=dataframe1['Mean_Absolute_Error'][1])

        return dataframe1,
    ```
    
In diesem Code wird das Python-SDK für Azure Machine Learning zum Protokollieren von Werten verwendet. Es verwendet „Run.get_context()“, um den Kontext des aktuellen Testlaufs abzurufen. Anschließend werden mit der Methode „run.parent.log()“ Werte in diesem Kontext protokolliert. Dabei wird `parent` verwendet, um Werte für die übergeordnete Pipeline zu protokollieren, anstatt das Modul auszuführen.

Weitere Informationen zur Verwendung des Python-SDK zum Protokollieren von Werten finden Sie unter [Überwachen von Azure ML-Experimentausführungen und -metriken](how-to-track-experiments.md).

## <a name="view-logs"></a>Anzeigen von Protokollen

Nachdem die Pipelineausführung abgeschlossen ist, wird der Fehler *Mean_Absolute_Error* auf der Seite „Experimente“ angezeigt.

1. Navigieren Sie zum Abschnitt **Experimente**.
1. Wählen Sie das Experiment aus.
1. Wählen Sie die Ausführung in Ihrem Experiment aus, die Sie anzeigen möchten.
1. Klicken Sie auf **Metriken**.

    ![Anzeigen von Ausführungsmetriken in Studio](./media/how-to-track-experiments/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie Protokolle im Designer verwenden. Informationen zu den nächsten Schritten finden Sie in den folgenden Artikeln:


* Informationen zum Beheben von Problemen mit Designerpipelines im Artikel [Debuggen und Problembehandlung für Machine Learning-Pipelines](how-to-debug-pipelines.md#azure-machine-learning-designer)
* Informationen zum Verwenden des Python-SDK zum Protokollieren von Metriken in der SDK-Erstellungsfunktion im Artikel [Überwachen von Azure ML-Experimentausführungen und -metriken](how-to-track-experiments.md)
