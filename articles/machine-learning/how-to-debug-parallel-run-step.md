---
title: Behandeln von Problemen mit „ParallelRunStep“
titleSuffix: Azure Machine Learning
description: Hier finden Sie Tipps zur Problembehandlung im Zusammenhang mit Fehlern bei der Verwendung von „ParallelRunStep“ in Machine Learning-Pipelines.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: troubleshooting
ms.reviewer: larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 09/23/2020
ms.openlocfilehash: a0f813253520d76731a9b49a89b0bcace7c2ef34
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979163"
---
# <a name="troubleshooting-the-parallelrunstep"></a>Behandeln von Problemen mit „ParallelRunStep“

Dieser Artikel enthält eine Problembehandlungsanleitung für den Fall, dass bei der Verwendung der Klasse [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?preserve-view=true&view=azure-ml-py) aus dem [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) Fehler auftreten.

Allgemeine Tipps zur Problembehandlung für Pipelines finden Sie unter [Problembehandlung bei Machine Learning-Pipelines](how-to-debug-pipelines.md).

## <a name="testing-scripts-locally"></a>Lokales Testen von Skripts

 „ParallelRunStep“ wird als Schritt in ML-Pipelines ausgeführt. Als Erstes sollten Sie ggf. [Ihre Skripts lokal testen](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines).

##  <a name="script-requirements"></a>Skriptanforderungen

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

## <a name="debugging-scripts-from-remote-context"></a>Debuggen von Skripts aus Remotekontext

Der Übergang vom lokalen Debuggen eines Bewertungsskripts zum Debuggen eines Bewertungsskripts in einer echten Pipeline kann einen schwierigen Schritt darstellen. Wie Sie Ihre Protokolle im Portal finden, erfahren Sie im [Abschnitt zu Pipelines des maschinellen Lernens über das Debuggen von Skripts aus einem Remotekontext](how-to-debug-pipelines.md). Die Informationen in diesem Abschnitt gelten auch für ParallelRunStep.

Die Protokolldatei `70_driver_log.txt` enthält z. B. Informationen vom Controller, der den ParallelRunStep-Code startet.

Aufgrund der verteilten Struktur von ParallelRunStep-Aufträgen gibt es Protokolle aus mehreren verschiedenen Quellen. Es werden jedoch zwei konsolidierte Dateien erstellt, die allgemeine Informationen bieten:

- `~/logs/job_progress_overview.txt`: Diese Datei enthält allgemeine Informationen zur Anzahl der bisher erstellten Minibatches (auch als Aufgaben bezeichnet) und der Anzahl der bisher verarbeiteten Minibatches. Am Ende wird das Ergebnis des Auftrags aufgeführt. Wenn bei dem Auftrag ein Fehler aufgetreten ist, wird die Fehlermeldung angezeigt, und es wird beschrieben, wo mit der Problembehandlung begonnen werden sollte.

- `~/logs/sys/master_role.txt`: Diese Datei stellt die Prinzipalknotenansicht (auch als Orchestrator bezeichnet) des laufenden Auftrags bereit. Dies umfasst die Aufgabenerstellung, die Fortschrittsüberwachung und das Ergebnis der Ausführung.

Protokolle, die mit dem EntryScript-Hilfsprogramm aus dem Eingabeskript generiert wurden, sowie Druckanweisungen finden Sie in den folgenden Dateien:

- `~/logs/user/entry_script_log/<ip_address>/<process_name>.log.txt`: Diese Dateien sind die Protokolle, die von „entry_script“ mithilfe des EntryScript-Hilfsprogramms geschrieben wurden.

- `~/logs/user/stdout/<ip_address>/<process_name>.stdout.txt`: Bei diesen Dateien handelt es sich um die Protokolle von stdout (z. B. eine print-Anweisung) von entry_script.

- `~/logs/user/stderr/<ip_address>/<process_name>.stderr.txt`: Diese Dateien sind die Protokolle von stderr von entry_script.

Für ein präzises Verständnis von Fehlern in Ihrem Skript gibt es Folgendes:

- `~/logs/user/error.txt`: Diese Datei wird versuchen, die Fehler in Ihrem Skript zusammenzufassen.

Weitere Informationen zu Fehlern in Ihrem Skript finden Sie hier:

- `~/logs/user/error/`: Enthält vollständige Stapelüberwachungen von Ausnahmen, die beim Laden und Ausführen des Eingabeskripts ausgelöst werden.

Wenn Sie detailliert erfahren möchten, wie die einzelnen Knoten das Bewertungsskript ausgeführt haben, sehen Sie sich die jeweiligen Prozessprotokolle für jeden Knoten an. Die Prozessprotokolle befinden sich im Ordner `sys/node`, gruppiert nach Workerknoten:

- `~/logs/sys/node/<ip_address>/<process_name>.txt`: Diese Datei enthält ausführliche Informationen zu den einzelnen Minibatches, die von einem Worker abgerufen oder abgeschlossen werden. Für jeden Minibatch umfasst diese Datei Folgendes:

    - Die IP-Adresse und die PID des Workerprozesses. 
    - Die Gesamtzahl der Elemente, die Anzahl der erfolgreich verarbeiteten Elemente und die Anzahl der nicht erfolgreichen Elemente.
    - Die Startzeit, die Dauer, die Verarbeitungszeit und die Laufzeit der Methode.

Sie können für jeden Knoten auch die Ergebnisse regelmäßiger Prüfungen der Ressourcennutzung anzeigen. Die Protokoll- und Setupdateien befinden sich in diesem Ordner:

- `~/logs/perf`: Legen Sie `--resource_monitor_interval` fest, um das Prüfintervall in Sekunden zu ändern. Das Standardintervall ist `600` (ungefähr 10 Minuten). Um die Überwachung zu beenden, legen Sie den Wert auf `0` fest. Jeder Ordner `<ip_address>` enthält Folgendes:

    - `os/`: Informationen zu allen laufenden Prozessen im Knoten. Bei einer Prüfung wird ein Betriebssystembefehl ausgeführt und das Ergebnis in einer Datei gespeichert. Unter Linux ist der Befehl `ps`. Verwenden Sie für Windows `tasklist`.
        - `%Y%m%d%H`: Der Name des Unterordners ist die Uhrzeit zur vollen Stunde.
            - `processes_%M`: Die Datei endet mit der Minute der Prüfzeit.
    - `node_disk_usage.csv`: Detaillierte Datenträgernutzung des Knotens.
    - `node_resource_usage.csv`: Übersicht über die Ressourcennutzung des Knotens.
    - `processes_resource_usage.csv`: Übersicht über die Ressourcennutzung der einzelnen Prozesse.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Wie protokolliere ich mein Benutzerskript aus einem Remotekontext?

ParallelRunStep kann mehrere Prozesse auf einem Knoten entsprechend dem process_count_per_node ausführen. Wir empfehlen die unten gezeigte Verwendung der ParallelRunStep-Protokollierung, um die Protokolle der einzelnen Prozesse auf dem Knoten zu organisieren und die Druck- und Protokollanweisungen zu kombinieren. Sie können eine Protokollierung von EntryScript abrufen, damit die Protokolle im Ordner **logs/user** im Portal aufgeführt werden.

**Ein Beispiel für Eingabeskript mit der Protokollierung:**
```python
from azureml_user.parallel_run import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Wie könnte ich eine seitliche Eingabe, z. B. eine oder mehrere Dateien mit einer Nachschlagetabelle, an alle meine Mitarbeiter weitergeben?

Der Benutzer kann mit dem Parameter side_inputs von ParalleRunStep Verweisdaten an das Skript übergeben. Alle als side_inputs bereitgestellten Datasets werden auf den einzelnen Workerknoten eingebunden. Der Benutzer kann den Speicherort der Eingabe durch Übergeben des Arguments erhalten.

Konstruieren Sie ein [Dataset](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py), das die Verweisdaten enthält, und registrieren Sie es in Ihrem Arbeitsbereich. Übergeben Sie es an den Parameter `side_inputs` für Ihr `ParallelRunStep`. Zusätzlich können Sie den Pfad im Abschnitt `arguments` hinzufügen, um einfach auf den eingebundenen Pfad zuzugreifen:

```python
label_config = label_ds.as_named_input("labels_input")
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

Danach können Sie in Ihrem Rückschlussskript (z. B. in Ihrer init()-Methode) wie folgt darauf zugreifen:

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

### <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>Wie werden Eingabedatasets mit Dienstprinzipalauthentifizierung verwendet?

Der Benutzer kann Eingabedatasets mit Dienstprinzipalauthentifizierung übergeben, die im Arbeitsbereich verwendet werden. Das Verwenden eines solchen Datasets in ParallelRunStep erfordert, dass das Dataset dafür registriert wird, damit die ParallelRunStep-Konfiguration erstellt werden kann.

```python
service_principal = ServicePrincipalAuthentication(
    tenant_id="***",
    service_principal_id="***",
    service_principal_password="***")
 
ws = Workspace(
    subscription_id="***",
    resource_group="***",
    workspace_name="***",
    auth=service_principal
    )
 
default_blob_store = ws.get_default_datastore() # or Datastore(ws, '***datastore-name***') 
ds = Dataset.File.from_files(default_blob_store, '**path***')
registered_ds = ds.register(ws, '***dataset-name***', create_new_version=True)
```

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich diese [Jupyter-Notebooks zur Veranschaulichung von Azure Machine Learning-Pipelines](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines) an.

* Hilfe zum Paket [azureml-pipeline-steps](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py) finden Sie in der SDK-Referenz. Zeigen Sie die [Referenzdokumentation](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?preserve-view=true&view=azure-ml-py) für die ParallelRunStep-Klasse an.

* Folgen Sie dem [erweiterten Tutorial](tutorial-pipeline-batch-scoring-classification.md) zur Verwendung von Pipelines mit ParallelRunStep. Das Tutorial zeigt, wie eine andere Datei als seitliche Eingabe übergeben werden kann.
