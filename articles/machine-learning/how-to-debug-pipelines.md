---
title: Debuggen und Troubleshooting bei ML-Pipelines
titleSuffix: Azure Machine Learning
description: Debuggen Sie Ihre Azure Machine Learning-Pipelines in Python. Lernen Sie häufige Fallstricke kennen, und erhalten Sie Tipps, die Ihnen helfen, Ihre Skripts vor und während der Remoteausführung zu debuggen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 10/22/2020
ms.topic: conceptual
ms.custom: troubleshooting, devx-track-python, contperf-fy21q2
ms.openlocfilehash: a150a0745911a70fc71db6b9c05fe6610cd960bf
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033254"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Debuggen und Problembehandlung für Machine Learning-Pipelines

In diesem Artikel erfahren Sie, wie Sie das Debuggen und Troubleshooting für [Pipelines für maschinelles Lernen](concept-ml-pipelines.md) im [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) und im [Azure Machine Learning-Designer](./concept-designer.md) durchführen. 

## <a name="troubleshooting-tips"></a>Tipps zur Problembehandlung

Die folgende Tabelle enthält häufige Probleme bei der Pipelinentwicklung mit möglichen Lösungen.

| Problem | Mögliche Lösung |
|--|--|
| Daten können nicht in das Verzeichnis `PipelineData` übergeben werden | Stellen Sie sicher, dass Sie im Skript ein Verzeichnis erstellt haben, das dem entspricht, wo Ihre Pipeline die Schrittausgabedaten erwartet. In den meisten Fällen definiert ein Eingabeargument das Ausgabeverzeichnis, und dann erstellen Sie das Verzeichnis explizit. Verwenden Sie `os.makedirs(args.output_dir, exist_ok=True)`, um das Ausgabeverzeichnis zu erstellen. Im [Tutorial](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) finden Sie ein Beispiel für ein Bewertungsskript, das dieses Entwurfsmuster zeigt. |
| Fehler bei Abhängigkeiten | Wenn in Ihrer Remotepipeline Abhängigkeitsfehler angezeigt werden, die beim lokalen Testen nicht aufgetreten sind, vergewissern Sie sich, dass die Abhängigkeiten und Versionen Ihrer Remoteumgebung mit denen in Ihrer Testumgebung übereinstimmen. (Siehe [Erstellen, Zwischenspeichern und Wiederverwenden von Umgebungen](./concept-environments.md#environment-building-caching-and-reuse))|
| Mehrdeutige Fehler bei Computezielen | Versuchen Sie, Computeziele zu löschen und neu zu erstellen. Die Neuerstellung von Computezielen erfolgt schnell und kann vorübergehende Probleme beheben. |
| Pipeline verwendet Schritte nicht wieder | Die Wiederverwendung von Schritten ist standardmäßig aktiviert, aber stellen Sie sicher, dass Sie sie in einem Pipelineschritt nicht deaktiviert haben. Wenn die Wiederverwendung deaktiviert ist, wird der Parameter `allow_reuse` im Schritt auf `False` festgelegt. |
| Pipeline wird unnötigerweise erneut ausgeführt | Um sicherzustellen, dass Schritte nur dann erneut ausgeführt werden, wenn die zugrunde liegenden Daten oder Skripts geändert werden, entkoppeln Sie Ihre Quellcodeverzeichnisse für die einzelnen Schritte. Wenn Sie dasselbe Quellverzeichnis für mehrere Schritte verwenden, kann es zu unnötigen Wiederholungen kommen. Verwenden Sie den Parameter `source_directory` in einem Pipelineschrittobjekt, um auf Ihr isoliertes Verzeichnis für diesen Schritt zu verweisen, und stellen Sie sicher, dass Sie nicht denselben `source_directory`-Pfad für mehrere Schritte verwenden. |
| Verlangsamter Schritt über Trainingsepochen oder andere Schleifenverhalten | Versuchen Sie, alle Dateischreibvorgänge, einschließlich der Protokollierung, von `as_mount()` zu `as_upload()` zu wechseln. Im Modus für die **Einbindung** wird ein remotes virtualisiertes Dateisystem verwendet, und die gesamte Datei wird jedes Mal dann hochgeladen, wenn er angefügt wird. |

## <a name="troubleshooting-parallelrunstep"></a>Problembehandlung für `ParallelRunStep` 

Das Skript für `ParallelRunStep` *muss zwei Funktionen enthalten*:
- `init()`: Verwenden Sie diese Funktion für alle aufwendigen oder allgemeinen Vorbereitungsmaßnahmen für den späteren Rückschluss. Ein Beispiel wäre etwa das Laden des Modells in ein globales Objekt. Diese Funktion wird nur einmal zu Beginn des Prozesses aufgerufen.
-  `run(mini_batch)`: Diese Funktion wird für jede Instanz vom Typ `mini_batch` ausgeführt.
    -  `mini_batch`: `ParallelRunStep` ruft die Run-Methode auf und übergibt entweder eine Liste oder einen Pandas-Datenrahmen (`DataFrame`) als Argument an die Methode. Jeder Eintrag in „mini_batch“ entspricht einem Dateipfad, wenn die Eingabe ein `FileDataset` ist, bzw. einem Pandas-Datenrahmen (`DataFrame`), wenn die Eingabe ein `TabularDataset` ist.
    -  `response`: Die Run-Methode sollte einen Pandas-Datenrahmen (`DataFrame`) oder ein Array zurückgeben. Bei Verwendung von „append_row output_action“ werden diese zurückgegebenen Elemente am Ende der allgemeinen Ausgabedatei hinzugefügt. Bei Verwendung von „summary_only“ wird der Inhalt der Elemente ignoriert. Bei allen Ausgabeaktionen geben die zurückgegebenen Ausgabeelemente jeweils eine erfolgreiche Ausführung für ein Eingabeelement aus dem Eingabeminibatch an. Stellen Sie sicher, dass das Ausführungsergebnis genügend Daten für eine Zuordnung zwischen der Eingabe und der Ausgabe des Ausführungsergebnisses enthält. Die Ausführungsausgabe wird in die Ausgabedatei geschrieben. Da hierbei nicht unbedingt die Reihenfolge eingehalten wird, müssen Sie einen Schlüssel in der Ausgabe verwenden, um sie der Eingabe zuzuordnen.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

Wenn das Verzeichnis mit Ihrem Rückschlussskript noch andere Dateien oder Ordner enthält, können Sie einen Verweis darauf erstellen, indem Sie das aktuelle Arbeitsverzeichnis ermitteln.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

### <a name="parameters-for-parallelrunconfig"></a>Parameter für ParallelRunConfig

`ParallelRunConfig` ist die Hauptkonfiguration für die `ParallelRunStep`-Instanz innerhalb der Azure Machine Learning-Pipeline. Sie wird verwendet, um Ihr Skript zu umschließen und die erforderlichen Parameter, einschließlich der folgenden Einträge, zu konfigurieren:
- `entry_script`: Ein Benutzerskript als lokaler Dateipfad, das parallel auf mehreren Knoten ausgeführt wird. Ist `source_directory` vorhanden, verwenden Sie einen relativen Pfad. Verwenden Sie andernfalls einen beliebigen Pfad, auf den auf dem Computer zugegriffen werden kann.
- `mini_batch_size`: Die Größe des Minibatchs, der an einen einzelnen Aufruf von `run()` übergeben wird (optional; Standardwerte sind `10` Dateien für `FileDataset` und `1MB` für `TabularDataset`).
    - Bei `FileDataset` handelt es sich um die Anzahl von Dateien (Mindestwert: `1`). Mehrere Dateien können zu einem Minibatch zusammengefasst werden.
    - Bei `TabularDataset` handelt es sich um die Datengröße. Beispielwerte: `1024`, `1024KB`, `10MB`, `1GB`. Empfohlener Wert: `1MB`. Der Minibatch auf der Grundlage von `TabularDataset` geht nie über Dateigrenzen hinaus. Ein Beispiel: Angenommen, Sie verfügen über unterschiedlich große CSV-Dateien. Die kleinste Datei hat eine Größe von 100 KB, die größte Datei ist 10 MB groß. Wenn Sie nun `mini_batch_size = 1MB` festlegen, werden Dateien mit einer Größe von weniger als 1 MB als einzelner Minibatch behandelt. Dateien mit einer Größe von mehr als 1 MB werden dagegen in mehrere Minibatches aufgeteilt.
- `error_threshold`: Die Anzahl von Datensatzfehlern für `TabularDataset` bzw. Dateifehlern für `FileDataset`, die während der Verarbeitung ignoriert werden sollen. Übersteigt die Fehleranzahl für die gesamte Eingabe diesen Wert, wird der Auftrag abgebrochen. Der Fehlerschwellenwert gilt für die gesamte Eingabe und nicht für den einzelnen Minibatch, der an die Methode `run()` gesendet wird. Zulässiger Bereich: `[-1, int.max]`. Der Teil `-1` gibt an, dass bei der Verarbeitung alle Fehler ignoriert werden sollen.
- `output_action`: Gibt an, wie die Ausgabe strukturiert werden soll:
    - `summary_only`: Das Benutzerskript speichert die Ausgabe. `ParallelRunStep` verwendet die Ausgabe nur zur Berechnung des Fehlerschwellenwerts.
    - `append_row`: Für alle Eingaben wird lediglich eine einzelne Datei im Ausgabeordner erstellt, in der alle Ausgaben angefügt werden (getrennt durch eine Zeile).
- `append_row_file_name`: Zum Anpassen des Namens der Ausgabedatei für append_row output_action (optional; der Standardwert ist `parallel_run_step.txt`).
- `source_directory`: Pfade zu Ordnern mit allen Dateien, die am Computeziel ausgeführt werden sollen (optional).
- `compute_target`: Nur `AmlCompute` wird unterstützt.
- `node_count`: Die Anzahl von Computeknoten, die zum Ausführen des Benutzerskripts verwendet werden sollen.
- `process_count_per_node`: Die Anzahl von Prozessen pro Knoten. Die bewährte Methode besteht im Festlegen auf die Anzahl der GPUs oder CPUs pro Knoten (optional; der Standardwert ist `1`).
- `environment`: Die Python-Umgebungsdefinition. Sie kann für die Verwendung einer vorhandenen Python-Umgebung oder für die Einrichtung einer temporären Umgebung konfiguriert werden. Die Definition kann auch zum Festlegen der erforderlichen Anwendungsabhängigkeiten verwendet werden (optional).
- `logging_level`: Die Ausführlichkeit des Protokolls. Mögliche Werte (mit zunehmender Ausführlichkeit): `WARNING`, `INFO`, `DEBUG`. (optional; Standardwert: `INFO`)
- `run_invocation_timeout`: Das Timeout für den Aufruf der Methode `run()` in Sekunden. (optional; Standardwert `60`)
- `run_max_try`: Maximale Anzahl der Versuche von `run()` für einen Minibatch. Ein `run()` ist fehlgeschlagen, wenn eine Ausnahme ausgelöst wird oder beim Erreichen von `run_invocation_timeout` nichts zurückgegeben wird (optional; der Standardwert ist `3`). 

Sie können `mini_batch_size`, `node_count`, `process_count_per_node`, `logging_level`, `run_invocation_timeout` und `run_max_try` als `PipelineParameter` festlegen, sodass Sie die Parameterwerte beim erneuten Senden einer Pipelineausführung optimieren können. In diesem Beispiel verwenden Sie `PipelineParameter` für `mini_batch_size` und `Process_count_per_node`, und Sie ändern diese Werte, wenn Sie später erneut eine Ausführung senden. 

### <a name="parameters-for-creating-the-parallelrunstep"></a>Parameter zum Erstellen von ParallelRunStep

Erstellen Sie ParallelRunStep mithilfe von Skript, Umgebungskonfiguration und Parametern. Geben Sie das Computeziel an, das Sie bereits als Ausführungsziel für Ihr Rückschlussskript an Ihren Arbeitsbereich angefügt haben. Verwenden Sie `ParallelRunStep`, um den Batchrückschluss-Pipelineschritt mit folgenden Parametern zu erstellen:
- `name`: Der Name des Schritts. Benennungseinschränkungen: eindeutig, 3–32 Zeichen und regulärer Ausdruck ^\[a-z\]([-a-z0-9]*[a-z0-9])?$.
- `parallel_run_config`: Ein Objekt vom Typ `ParallelRunConfig`, wie zuvor definiert.
- `inputs`: Mindestens ein Azure Machine Learning-Dataset mit einem einzelnen Typ, das für die Parallelverarbeitung partitioniert werden soll.
- `side_inputs`: Verweisdaten oder Datasets, die als Seiteneingabe verwendet werden und für die keine Partitionierung erforderlich ist.
- `output`: Ein Objekt vom Typ `PipelineData`, das dem Ausgabeverzeichnis entspricht.
- `arguments`: Eine Liste mit Argumenten, die an das Benutzerskript übergeben werden. Verwenden Sie „unknown_args“, um sie in Ihrem Einstiegsskript zu nutzen (optional).
- `allow_reuse`: Angabe, ob bei dem Schritt vorherige Ergebnisse wiederverwendet werden sollen, wenn er mit den gleichen Einstellungen/Eingaben ausgeführt wird. Ist der Parameter auf `False` festgelegt, wird für diesen Schritt bei der Pipelineausführung immer eine neue Ausführung generiert. (optional; Standardwert `True`)

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```

## <a name="debugging-techniques"></a>Debuggingverfahren

Es gibt drei Haupttechniken zum Debuggen von Pipelines: 

* Debuggen der einzelnen Pipelineschritte auf dem lokalen Computer
* Verwenden von Protokollierung und Application Insights zum Isolieren und Diagnostizieren der Problemursache
* Anfügen eines Remotedebuggers an eine in Azure ausgeführte Pipeline

### <a name="debug-scripts-locally"></a>Lokales Testen von Skripts

Einer der häufigsten Fehler in einer Pipeline besteht darin, dass das Domänenskript nicht wie beabsichtigt ausgeführt wird oder Laufzeitfehler im Remotecomputekontext enthält, die schwierig zu debuggen sind.

Pipelines selbst können nicht lokal ausgeführt werden. Wenn Sie jedoch die Skripts isoliert auf dem lokalen Computer ausführen, können Sie schneller debuggen, da Sie nicht auf den Compute- und Umgebungserstellungsprozess warten müssen. Hierfür ist ein gewisser Entwicklungsaufwand erforderlich:

* Wenn sich Ihre Daten in einem Clouddatenspeicher befinden, müssen Sie Daten herunterladen und Ihrem Skript zur Verfügung stellen. Die Verwendung einer kleinen Stichprobe Ihrer Daten ist eine gute Möglichkeit, die Laufzeit zu verkürzen und schnell Feedback zum Skriptverhalten zu erhalten.
* Wenn Sie versuchen, einen Pipelinezwischenschritt zu simulieren, müssen Sie die Objekttypen, die das jeweilige Skript vom vorherigen Schritt erwartet, möglicherweise manuell erstellen.
* Sie müssen auch Ihre eigene Umgebung definieren und die in Ihrer Remotecomputeumgebung definierten Abhängigkeiten replizieren.

Nachdem Sie ein Skript für die Ausführung in Ihrer lokalen Umgebung eingerichtet haben, gestalten sich Debugaufgaben wie die Folgenden viel einfacher:

* Anfügen einer benutzerdefinierten Debugkonfiguration
* Anhalten der Ausführung und Überprüfen des Objektzustands
* Abfangen von Typfehlern oder logischen Fehlern, die bis zur Laufzeit nicht verfügbar gemacht werden

> [!TIP] 
> Wenn Sie sichergestellt haben, dass ihr Skript erwartungsgemäß ausgeführt wird, empfiehlt es sich, das Skript nun in einer Einzelschritt-Pipeline auszuführen, anstatt zu versuchen, das Skript in einer Pipeline mit mehreren Schritten auszuführen.

## <a name="configure-write-to-and-review-pipeline-logs"></a>Konfigurieren und Überprüfen von Pipelineprotokollen sowie Schreiben in diese

Das lokale Testen von Skripts ist eine hervorragende Möglichkeit, wichtige Codefragmente und eine komplexe Logik zu debuggen, bevor Sie mit dem Erstellen einer Pipeline beginnen. Aber irgendwann werden Sie Skripts wahrscheinlich während der eigentlichen Pipelineausführung selbst debuggen müssen, insbesondere bei der Diagnose von Verhaltensweisen, die während der Interaktion zwischen Pipelineschritten auftreten. Wir empfehlen die großzügige Verwendung von `print()`-Anweisungen in Ihren Schrittskripts, damit Sie den Objektzustand und die erwarteten Werte während der Remoteausführung sehen können, ähnlich wie beim Debuggen von JavaScript-Code.

### <a name="logging-options-and-behavior"></a>Protokollierungsoptionen und -verhalten

In der nachstehenden Tabelle finden Sie Informationen zu verschiedenen Debugoptionen für Pipelines. Es handelt sich nicht um eine vollständige Liste, da neben den hier gezeigten Optionen für Azure Machine Learning, Python und OpenCensus auch andere vorhanden sind.

| Bibliothek                    | type   | Beispiel                                                          | Destination                                  | Ressourcen                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Metrik | `run.log(name, val)`                                             | Benutzeroberfläche des Azure Machine Learning-Portals             | [Nachverfolgen von Experimenten](how-to-track-experiments.md)<br>[Klasse „azureml.core.Run“](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py)                                                                                                                                                 |
| Python-Druck/-Protokollierung    | Log    | `print(val)`<br>`logging.info(message)`                          | Treiberprotokolle, Azure Machine Learning-Designer | [Nachverfolgen von Experimenten](how-to-track-experiments.md)<br><br>[Python-Protokollierung](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Log    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights – Ablaufverfolgung                | [Debuggen von Pipelines in Application Insights](./how-to-log-pipelines-application-insights.md)<br><br>[OpenCensus Azure Monitor Exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Cookbook zur Python-Protokollierung](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Beispiel für Protokollierungsoptionen

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning-Designer

Die **70_driver_log**-Dateien für die im Designer erstellten Pipelines finden Sie entweder auf der Seite zur Dokumenterstellung oder auf der Detailseite zur Pipelineausführung.

### <a name="enable-logging-for-real-time-endpoints"></a>Aktivieren der Protokollierung für Echtzeitendpunkte

Für die Problembehandlung und das Debuggen von Echtzeitendpunkten im Designer müssen Sie die Application Insights-Protokollierung mithilfe des SDK aktivieren. Mithilfe der Protokollierung können Sie Probleme bei der Modellimplementierung und -verwendung behandeln und debuggen. Weitere Informationen finden Sie unter [Protokollierung für bereitgestellte Modelle](./how-to-enable-app-insights.md). 

### <a name="get-logs-from-the-authoring-page"></a>Abrufen von Protokollen auf der Seite zur Dokumenterstellung

Wenn Sie eine Pipelineausführung übermitteln und auf der Seite zur Dokumenterstellung bleiben, finden Sie nach der Ausführung der einzelnen Module die jeweils generierten Protokolldateien.

1. Wählen Sie ein Modul aus, dessen Ausführung im Dokumenterstellungsbereich beendet wurde.
1. Wechseln Sie im rechten Bereich des Moduls zur Registerkarte **Ausgaben und Protokolle**.
1. Erweitern Sie den Bereich auf der rechten Seite, und wählen Sie die Datei **70_driver_log.txt** aus, um sie im Browser anzuzeigen. Sie können Protokolle auch lokal herunterladen.

    ![Erweiterter Ausgabebereich im Designer](./media/how-to-debug-pipelines/designer-logs.png)?view=azure-ml-py&preserve-view=true)?view=azure-ml-py&preserve-view=true)

### <a name="get-logs-from-pipeline-runs"></a>Abrufen von Protokollen von Pipelineausführungen

Sie finden die Protokolldateien bestimmter Ausführungen auch auf der Detailseite für Pipelineausführungen, entweder im Abschnitt **Pipelines** oder im Abschnitt **Experimente** des Studios.

1. Wählen Sie eine im Designer erstellte Pipelineausführung aus.

    ![Seite für Pipelineausführungen](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Wählen Sie im Vorschaubereich ein Modul aus.
1. Wechseln Sie im rechten Bereich des Moduls zur Registerkarte **Ausgaben und Protokolle**.
1. Erweitern Sie den Bereich auf der rechten Seite, um die Datei **70_driver_log.txt** im Browser anzuzeigen, oder wählen Sie die Datei aus, um die Protokolle lokal herunterzuladen.

> [!IMPORTANT]
> Wenn Sie eine Pipeline über die Detailseite für Pipelineausführungen aktualisieren möchten, müssen Sie die Pipelineausführung in einen neuen Pipelineentwurf **klonen**. Pipelineausführungen sind Momentaufnahmen von Pipelines. Sie ähneln Protokolldateien und können nicht geändert werden. 

## <a name="application-insights"></a>Application Insights
Weitere Informationen zur Verwendung der OpenCensus-Python-Bibliothek in diesem Zusammenhang finden Sie in diesem Handbuch: [Debugging und Problembehandlung für Pipelines des maschinellen Lernens in Application Insights](./how-to-log-pipelines-application-insights.md)

## <a name="interactive-debugging-with-visual-studio-code"></a>Interaktives Debuggen mit Visual Studio Code

In einigen Fällen muss der in Ihrer ML-Pipeline verwendete Python-Code ggf. interaktiv debugged werden. Mit Visual Studio Code (VS Code) und debugpy können Sie am den Code anfügen, während er in der Trainingsumgebung ausgeführt wird. Weitere Informationen finden Sie im [Leitfaden zum interaktiven Debuggen in VS Code](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines).

## <a name="next-steps"></a>Nächste Schritte

* Ein vollständiges Tutorial zur Verwendung von `ParallelRunStep` finden Sie unter [Tutorial: Erstellen einer Azure Machine Learning-Pipeline für die Batchbewertung](tutorial-pipeline-batch-scoring-classification.md).

* Ein umfassendes Beispiel, das automatisiertes maschinelles Lernen in ML-Pipelines veranschaulicht, finden Sie unter [Verwenden von automatisiertem ML in einer Azure Machine Learning-Pipeline in Python](how-to-use-automlstep-in-pipelines.md).

* Hilfe zu den Paketen [azureml-pipelines-core](/python/api/azureml-pipeline-core/?preserve-view=true&view=azure-ml-py) und [azureml-pipelines-steps](/python/api/azureml-pipeline-steps/?preserve-view=true&view=azure-ml-py) finden Sie in der SDK-Referenz.

* Weitere Informationen finden Sie in der Liste [Designer-Ausnahmen und-Fehlercodes](algorithm-module-reference/designer-error-codes.md).