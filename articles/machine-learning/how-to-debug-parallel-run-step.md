---
title: Debugging und Problembehandlung von ParallelRunStep
titleSuffix: Azure Machine Learning
description: Debuggen und Problembehandlung für ParallelRunStep in Machine Learning-Pipelines im Azure Machine Learning SDK für Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: troubleshooting
ms.reviewer: jmartens, larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 09/23/2020
ms.openlocfilehash: 09f75e9e8f972ec44098e119dc5b30bd44638918
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630463"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Debugging und Problembehandlung von ParallelRunStep


In diesem Artikel erfahren Sie, wie Sie Debugging und Problembehandlung für die [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?preserve-view=true&view=azure-ml-py)-Klasse im [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) durchführen.

## <a name="testing-scripts-locally"></a>Lokales Testen von Skripts

Weitere Informationen finden Sie im Abschnitt [Lokales Testen von Skripts](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines) für Pipelines des maschinellen Lernens. Ihre ParallelRunStep-Instanz wird als Schritt in ML-Pipelines ausgeführt, sodass die gleiche Antwort für beide gilt.

## <a name="debugging-scripts-from-remote-context"></a>Debuggen von Skripts aus Remotekontext

Der Übergang vom lokalen Debuggen eines Bewertungsskripts zum Debuggen eines Bewertungsskripts in einer echten Pipeline kann einen schwierigen Schritt darstellen. Informationen zum Suchen Ihrer Protokolle im Portal finden Sie im [Abschnitt zu Pipelines des maschinellen Lernens über das Debuggen von Skripts aus einem Remotekontext](how-to-debug-pipelines.md). Die Informationen in diesem Abschnitt gelten auch für ParallelRunStep.

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

Sie finden auch Informationen zur Ressourcennutzung der Prozesse für jeden Worker. Diese Informationen liegen im CSV-Format vor und befinden sich unter `~/logs/sys/perf/<ip_address>/node_resource_usage.csv`. Informationen zu den einzelnen Prozessen finden Sie unter `~logs/sys/perf/<ip_address>/processes_resource_usage.csv`.

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
    tenant_id="**_",
    service_principal_id="_*_",
    service_principal_password="_*_")
 
ws = Workspace(
    subscription_id="_*_",
    resource_group="_*_",
    workspace_name="_*_",
    auth=service_principal
    )
 
default_blob_store = ws.get_default_datastore() # or Datastore(ws, '_*_datastore-name_*_') 
ds = Dataset.File.from_files(default_blob_store, '_*path**_')
registered_ds = ds.register(ws, '_*_dataset-name_*_', create_new_version=True)
```

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich [Jupyter-Notebooks zur Veranschaulichung von Azure Machine Learning-Pipelines](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines) an.

* Hilfe zum Paket [azureml-pipeline-steps](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py) finden Sie in der SDK-Referenz. Zeigen Sie die [Referenzdokumentation](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?preserve-view=true&view=azure-ml-py) für die ParallelRunStep-Klasse an.

* Folgen Sie dem [erweiterten Tutorial](tutorial-pipeline-batch-scoring-classification.md) zur Verwendung von Pipelines mit ParallelRunStep. Das Tutorial zeigt, wie eine andere Datei als seitliche Eingabe übergeben werden kann.