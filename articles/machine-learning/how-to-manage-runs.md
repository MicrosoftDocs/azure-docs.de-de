---
title: Starten, Überwachen und Abbrechen von Trainingsausführungen in Python
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Ihre Experimentausführungen für das maschinelle Lernen mit dem Python SDK von Azure Machine Learning starten, überwachen und verwalten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 12/04/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-azurecli
ms.openlocfilehash: ec006636ed7e975b696aa32300b32089e3209bb5
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96600471"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Starten, Überwachen und Abbrechen von Trainingsausführungen in Python

Das [Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py), die [Machine Learning-CLI](reference-azure-machine-learning-cli.md) und [Azure Machine Learning Studio](https://ml.azure.com) bieten verschiedene Methoden zum Überwachen, Organisieren und Verwalten Ihrer Ausführungen für Training und Experimentieren.

In diesem Artikel finden Sie Beispiele für die folgenden Aufgaben:

* Überwachen der Ausführungsleistung
* Abbrechen oder Fehler von Ausführungen
* Erstellen untergeordneter Ausführungen
* Markieren und Suchen von Ausführungen

> [!TIP]
> Informationen zur Überwachung des Azure Machine Learning-Diensts und der zugehörigen Azure-Dienste finden Sie unter [Überwachen von Azure Machine Learning](monitor-azure-machine-learning.md).
> Informationen zur Überwachung von Modellen, die als Webdienste oder IoT Edge-Module bereitgestellt werden, finden Sie unter [Sammeln von Daten von Modellen in der Produktion](how-to-enable-data-collection.md) sowie unter [Überwachen und Erfassen von Daten von ML-Webdienst-Endpunkten](how-to-enable-app-insights.md).

## <a name="prerequisites"></a>Voraussetzungen

Sie benötige folgende Elemente:

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) noch heute aus.

* Ein [Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md).

* Das Azure Machine Learning SDK für Python (Version 1.0.21 oder höher). Wie Sie die neueste Version des SDK installieren oder auf diese aktualisieren, erfahren Sie unter [Installieren des Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).

    Um Ihre Version des Azure Machine Learning SDK zu überprüfen, verwenden Sie den folgenden Code:

    ```python
    print(azureml.core.VERSION)
    ```

* Die [Azure CLI](/cli/azure/?preserve-view=true&view=azure-cli-latest) und die [CLI-Erweiterung für Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="monitor-run-performance"></a>Überwachen der Ausführungsleistung

* Starten einer Ausführung und deren Protokollierungsprozess

    # <a name="python"></a>[Python](#tab/python)
    
    1. Richten Sie Ihr Experiment durch Importieren der Klassen [Workspace](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py), [Experiment](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py), [Run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) und [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) aus dem [azureml.core](/python/api/azureml-core/azureml.core?preserve-view=true&view=azure-ml-py)-Paket ein.
    
        ```python
        import azureml.core
        from azureml.core import Workspace, Experiment, Run
        from azureml.core import ScriptRunConfig
        
        ws = Workspace.from_config()
        exp = Experiment(workspace=ws, name="explore-runs")
        ```
    
    1. Starten Sie eine Ausführung und ihren Protokollierungsprozess mit der [`start_logging()`](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truestart-logging--args----kwargs-)-Methode.
    
        ```python
        notebook_run = exp.start_logging()
        notebook_run.log(name="message", value="Hello from run!")
        ```
        
    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    
    Führen Sie die folgenden Schritte aus, um eine Ausführung Ihres Experiments zu starten:
    
    1. Verwenden Sie in einer Shell oder Eingabeaufforderung die Azure CLI, um sich bei Ihrem Azure-Abonnement zu authentifizieren:
    
        ```azurecli-interactive
        az login
        ```
        
        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 
    
    1. Fügen Sie eine Arbeitsbereichskonfiguration an den Ordner an, der Ihr Trainingsskript enthält. Ersetzen Sie `myworkspace` durch Ihren Azure Machine Learning-Arbeitsbereich. Ersetzen Sie `myresourcegroup` durch die Azure-Ressourcengruppe, die Ihren Arbeitsbereich enthält:
    
        ```azurecli-interactive
        az ml folder attach -w myworkspace -g myresourcegroup
        ```
    
        Dieser Befehl erstellt ein `.azureml`-Unterverzeichnis, das RUNCONFIG-Beispieldateien und Conda-Umgebungsdateien enthält. Es enthält auch eine `config.json`-Datei, die für die Kommunikation mit dem Azure Machine Learning-Arbeitsbereich verwendet wird.
    
        Weitere Informationen finden Sie unter [az ml folder attach](/cli/azure/ext/azure-cli-ml/ml/folder?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).
    
    2. Verwenden Sie den folgenden Befehl zum Starten der Ausführung. Wenn Sie diesen Befehl verwenden, geben Sie den Namen der Runconfig-Datei (den Text vor „\*.runconfig“, wenn Sie auf Ihr Dateisystem schauen) für den -c-Parameter an.
    
        ```azurecli-interactive
        az ml run submit-script -c sklearn -e testexperiment train.py
        ```
    
        > [!TIP]
        > Mit dem Befehl `az ml folder attach` wurde ein `.azureml`-Unterverzeichnis erstellt, das zwei RUNCONFIG-Beispieldateien enthält.
        >
        > Wenn Sie ein Python-Skript haben, das programmgesteuert ein Laufzeitkonfigurationsobjekt erstellt, können Sie es mit [RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration?preserve-view=true&view=azure-ml-py#&preserve-view=truesave-path-none--name-none--separate-environment-yaml-false-) als RUNCPNFIG-Datei speichern.
        >
        > Weitere RUNCONFIG-Beispieldateien finden Sie unter [https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/).
    
        Weitere Informationen finden Sie unter [az ml run submit-script](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Führen Sie die folgenden Schritte aus, um eine Pipelineausführung im Designer zu übermitteln:
    
    1. Legen Sie ein Standardcomputeziel für Ihre Pipeline fest.
    
    1. Wählen Sie im oberen Bereich der Pipelinecanvas die Option **Ausführen** aus.
    
    1. Wählen Sie ein Experiment zum Gruppieren ihrer Pipelineausführungen aus.
    
    ---

* Überwachen des Status einer Ausführung

    # <a name="python"></a>[Python](#tab/python)
    
    * Rufen Sie den Status einer Ausführung mit der [`get_status()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-status--)-Methode ab.
    
        ```python
        print(notebook_run.get_status())
        ```
    
    * Rufen Sie die Ausführungs-ID, Ausführungszeit und zusätzliche Details zur Ausführung mit der [`get_details()`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-details--)-Methode ab.
    
        ```python
        print(notebook_run.get_details())
        ```
    
    * Wenn die Ausführung erfolgreich abgeschlossen wurde, verwenden Sie die [`complete()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecomplete--set-status-true-)-Methode, um sie als abgeschlossen zu markieren.
    
        ```python
        notebook_run.complete()
        print(notebook_run.get_status())
        ```
    
    * Wenn Sie das Python-Entwurfsmuster `with...as` verwenden, markiert sich die Ausführung automatisch selbst als abgeschlossen, wenn sie außerhalb des Bereichs liegt. Sie müssen die Ausführung nicht manuell als abgeschlossen markieren.
        
        ```python
        with exp.start_logging() as notebook_run:
            notebook_run.log(name="message", value="Hello from run!")
            print(notebook_run.get_status())
        
        print(notebook_run.get_status())
        ```
    
    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    
    * Verwenden Sie den folgenden Befehl, um eine Liste der Ausführungen für Ihr Experiment anzuzeigen. Ersetzen Sie `experiment` durch den Namen Ihres Experiments:
    
        ```azurecli-interactive
        az ml run list --experiment-name experiment
        ```
    
        Dieser Befehl gibt ein JSON-Dokument zurück, in dem Informationen zu Ausführungen für dieses Experiment aufgelistet sind.
    
        Weitere Informationen finden Sie unter [az ml experiment list](/cli/azure/ext/azure-cli-ml/ml/experiment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).
    
    * Verwenden Sie den folgenden Befehl, um Informationen zu einer bestimmten Ausführung anzuzeigen. Ersetzen Sie `runid` durch die ID der Ausführung:
    
        ```azurecli-interactive
        az ml run show -r runid
        ```
    
        Dieser Befehl gibt ein JSON-Dokument zurück, in dem Informationen zur Ausführung aufgelistet sind.
    
        Weitere Informationen finden Sie unter [az ml run show](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).
    
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    So zeigen Sie die Anzahl aktiver Ausführungen für Ihr Experiment in Studio an
    
    1. Navigieren Sie zum Abschnitt **Experimente**.
    
    1. Wählen Sie ein Experiment aus.
    
        Auf der Seite „Experiment“ sehen Sie die Anzahl der aktiven Computeziele und die Dauer jeder Ausführung. 
    
    1. Nehmen Sie Anpassungen an dem Experiment vor, indem Sie zu vergleichende Ausführungen auswählen, Diagramme hinzufügen oder Filter anwenden. Diese Änderungen können als **Benutzerdefinierte Ansicht** gespeichert werden, sodass Sie problemlos zu Ihrer Arbeit zurückkehren können. Benutzer mit Arbeitsbereichsberechtigungen können die benutzerdefinierte Ansicht bearbeiten oder anzeigen. Außerdem können Sie die benutzerdefinierte Ansicht für andere Benutzer freigeben, indem Sie die URL im Browser kopieren und einfügen.  
    
        :::image type="content" source="media/how-to-manage-runs/custom-views.gif" alt-text="Screenshot: Erstellen einer benutzerdefinierten Ansicht":::
    
    1. Wählen Sie eine bestimmte Ausführungsnummer aus.
    
    1. Auf der Registerkarte **Protokolle** finden Sie Diagnose- und Fehlerprotokolle für Ihre Pipelineausführung.
    
    ---
    
## <a name="cancel-or-fail-runs"></a>Abbrechen oder Fehler von Ausführungen

Wenn Sie einen Fehler bemerken oder die Ausführung zu lange dauert, können Sie die Ausführung abbrechen.

# <a name="python"></a>[Python](#tab/python)

Verwenden Sie zum Abbrechen einer Ausführung mit dem SDK die [`cancel()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecancel--)-Methode:

```python
src = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_run = exp.submit(src)
print(local_run.get_status())

local_run.cancel()
print(local_run.get_status())
```

Wenn Ihre Ausführung abgeschlossen ist, aber Fehler enthält (etwa weil ein falsches Trainingsskript verwendet wurde), können Sie sie mit der [`fail()`](/python/api/azureml-core/azureml.core.run%28class%29#fail-error-details-none--error-code-none---set-status-true-)-Methode als fehlerhaft markieren.

```python
local_run = exp.submit(src)
local_run.fail()
print(local_run.get_status())
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie zum Abbrechen einer Ausführung mit der CLI den folgenden Befehl. Ersetzen Sie `runid` durch die ID der Ausführung.

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Weitere Informationen finden Sie unter [az ml run cancel](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Führen Sie die folgenden Schritte aus, um eine Ausführung in Studio abzubrechen:

1. Navigieren Sie entweder im Abschnitt **Experimente** oder im Abschnitt **Pipelines** zur ausgeführten Pipeline. 

1. Wählen Sie die Nummer der Pipelineausführung aus, die Sie abbrechen möchten.

1. Wählen Sie auf der Symbolleiste die Option **Abbrechen** aus.

---

## <a name="create-child-runs"></a>Erstellen untergeordneter Ausführungen

Erstellen Sie untergeordnete Ausführungen, um verwandte Ausführungen zu gruppieren, wie etwa für unterschiedliche Iterationen zum Optimieren von Hyperparametern.

> [!NOTE]
> Untergeordnete Ausführungen können nur mit dem SDK erstellt werden.

Dieses Codebeispiel verwendet das `hello_with_children.py`-Skript, um einen Batch von fünf untergeordneten Ausführungen aus einer übermittelten Ausführung mithilfe der [`child_run()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truechild-run-name-none--run-id-none--outputs-none-)-Methode zu erstellen:

```python
!more hello_with_children.py
src = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_run = exp.submit(src)
local_run.wait_for_completion(show_output=True)
print(local_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> Sobald sie den gültigen Bereich verlassen haben, werden untergeordnete Ausführungen automatisch als abgeschlossen markiert.

Um viele untergeordnete Ausführungen effizient zu erstellen, verwenden Sie die [`create_children()`](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-children-count-none--tag-key-none--tag-values-none-)-Methode. Weil jeder Erstellungsvorgang zu einem Netzwerkaufruf führt, ist das Erstellen eines Ausführungsbatches effizienter als ein jeweils einzelnes Erstellen der Ausführungen.

### <a name="submit-child-runs"></a>Senden untergeordneter Ausführungen

Untergeordnete Ausführungen können auch aus einer übergeordneten Ausführung gesendet werden. So können Sie Hierarchien aus übergeordneten und untergeordneten Ausführungen erstellen. Es ist nicht möglich, eine untergeordnete Ausführung ohne eine übergeordnete Ausführung zu erstellen: Auch wenn die übergeordnete Ausführung lediglich untergeordnete Ausführungen startet, muss die Hierarchie trotzdem erstellt werden. Der Status aller Ausführungen ist unabhängig: Eine übergeordnete Ausführung kann den Erfolgsstatus `"Completed"` aufweisen, auch wenn eine untergeordnete Ausführung abgebrochen wurde oder zu einem Fehler führte.  

Möglicherweise möchten Sie, dass untergeordnete Ausführungen eine andere Ausführungskonfiguration verwenden als die übergeordnete Ausführung. Sie könnten beispielsweise eine weniger leistungsfähige CPU-basierte Konfiguration für die übergeordnete Ausführung und GPU-basierte Konfigurationen für die untergeordneten Ausführungen verwenden. Häufig ist es auch wünschenswert, verschiedene Argumente und Daten an die einzelnen untergeordneten Ausführung zu übergeben. Erstellen Sie zum Anpassen einer untergeordneten Ausführung ein Objekt vom Typ `ScriptRunConfig` für die untergeordnete Ausführung. Im unten bereitgestellten Codebeispiel werden folgende Schritte ausgeführt:

- Es ruft eine Computeressource namens `"gpu-cluster"` aus dem Arbeitsbereich `ws` ab.
- Es durchläuft verschiedene Argumentwerte, die an die untergeordneten `ScriptRunConfig`-Objekte übergeben werden sollen.
- Es erstellt und übermittelt eine neue untergeordnete Ausführung und verwendet dabei benutzerdefinierte Computeressourcen und Argumente.
- Es blockiert die weitere Ausführung, bis alle untergeordneten Ausführungen beendet sind.

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig

compute_target = ws.compute_targets["gpu-cluster"]

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], compute_target=compute_target)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

Um auf effiziente Weise mehrere untergeordnete Ausführungen mit identischen Konfigurationen, Argumenten und Eingaben zu erstellen, verwenden Sie die [`create_children()`](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-children-count-none--tag-key-none--tag-values-none-)-Methode. Weil jeder Erstellungsvorgang zu einem Netzwerkaufruf führt, ist das Erstellen eines Ausführungsbatches effizienter als ein jeweils einzelnes Erstellen der Ausführungen.

In einer untergeordneten Ausführung können Sie die ID der übergeordneten Ausführung anzeigen:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Abfragen von untergeordneten Ausführungen

Um die untergeordneten Ausführungen eines bestimmten übergeordneten Elements abzufragen, verwenden Sie die [`get_children()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-)-Methode. Über das Argument ``recursive = True`` können Sie eine geschachtelte Struktur aus untergeordneten Elementen und Enkelelementen abfragen.

```python
print(parent_run.get_children())
```

### <a name="log-to-parent-or-root-run"></a>Protokollieren in einer übergeordneten oder der Stammausführung

Sie können über das Feld `Run.parent` auf die Ausführung zugreifen, die die aktuelle untergeordnete Ausführung gestartet hat. Dies wird häufig genutzt, wenn die Protokollergebnisse an einer zentralen Stelle konsolidiert werden sollen. Beachten Sie, dass untergeordnete Ausführungen asynchron ausgeführt werden und es keine Garantie für die Reihenfolge oder Synchronisierung gibt. Die einzige Möglichkeit stellt das Warten auf die untergeordneten Ausführungen durch die übergeordnete Ausführung dar.

```python
# in child (or even grandchild) run

def root_run(self : Run) -> Run :
    if self.parent is None : 
        return self
    return root_run(self.parent)

current_child_run = Run.get_context()
root_run(current_child_run).log("MyMetric", f"Data from child run {current_child_run.id}")

```


## <a name="tag-and-find-runs"></a>Markieren und Suchen von Ausführungen

In Azure Machine Learning können Sie Eigenschaften und Tags zum Organisieren und Abfragen wichtiger Informationen Ihrer Ausführungen verwenden.

* Hinzufügen von Eigenschaften und Tags

    # <a name="python"></a>[Python](#tab/python)
    
    Um Ihren Ausführungen durchsuchbare Metadaten hinzuzufügen, verwenden Sie die [`add_properties()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueadd-properties-properties-)-Methode. Der folgende Code fügt der Ausführung z. B. die `"author"`-Eigenschaft hinzu:
    
    ```Python
    local_run.add_properties({"author":"azureml-user"})
    print(local_run.get_properties())
    ```
    
    Eigenschaften sind unveränderlich, sodass sie eine permanente Aufzeichnung zu Überwachungszwecken erstellen. Das folgende Codebeispiel führt zu einem Fehler, weil `"azureml-user"` im vorstehenden Code bereits als Wert der `"author"`-Eigenschaft hinzugefügt wurde:
    
    ```Python
    try:
        local_run.add_properties({"author":"different-user"})
    except Exception as e:
        print(e)
    ```
    
    Im Gegensatz zu Eigenschaften können Tags geändert werden. Um durchsuchbare und aussagekräftige Informationen für Consumer Ihres Experiments hinzuzufügen, verwenden Sie die [`tag()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truetag-key--value-none-)-Methode.
    
    ```Python
    local_run.tag("quality", "great run")
    print(local_run.get_tags())
    
    local_run.tag("quality", "fantastic run")
    print(local_run.get_tags())
    ```
    
    Sie können auch einfache Zeichenfolgentags hinzufügen. Wenn diese Tags in das Tag-Wörterbuch als Schlüssel aufgenommen wurden, haben sie den Wert `None`.
    
    ```Python
    local_run.tag("worth another look")
    print(local_run.get_tags())
    ```
    
    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    
    > [!NOTE]
    > Mit der CLI können Sie nur Tags hinzufügen oder aktualisieren.
    
    Verwenden Sie den folgenden Befehl, um ein Tag hinzuzufügen oder zu aktualisieren:
    
    ```azurecli-interactive
    az ml run update -r runid --add-tag quality='fantastic run'
    ```
    
    Weitere Informationen finden Sie unter [az ml run update](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Die Eigenschaften und Tags können in Studio angezeigt, aber dort nicht geändert werden.
    
    ---

* Abfragen von Eigenschaften und Tags

    Sie können Ausführungen in einem Experiment abfragen, um eine Liste der Ausführungen zurückzugeben, die mit bestimmten Eigenschaften und Tags übereinstimmen.

    # <a name="python"></a>[Python](#tab/python)
    
    ```Python
    list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
    list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
    ```
    
    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    
    Die Azure CLI unterstützt [JMESPath](http://jmespath.org)-Abfragen, mit denen Ausführungen nach Eigenschaften und Tags gefiltert werden können. Wenn Sie eine JMESPath-Abfrage mit der Azure CLI verwenden möchten, geben Sie diese mit dem Parameter `--query` an. Die folgenden Beispiele zeigen einige Abfragen mithilfe von Eigenschaften und Tags:
    
    ```azurecli-interactive
    # list runs where the author property = 'azureml-user'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user']
    # list runs where the tag contains a key that starts with 'worth another look'
    az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
    # list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
    ```
    
    Weitere Informationen zum Abfragen von Azure CLI-Ergebnissen finden Sie unter [Abfragen der Azure CLI-Befehlsausgabe](/cli/azure/query-azure-cli?preserve-view=true&view=azure-cli-latest).
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    1. Navigieren Sie zum Abschnitt **Pipelines**.
    
    1. Verwenden Sie die Suchleiste, um Pipelines anhand von Tags, Beschreibungen, Experimentnamen und Absendernamen zu filtern.
    
    ---

## <a name="example-notebooks"></a>Beispielnotebooks

Die folgenden Notebooks veranschaulichen die Konzepte in diesem Artikel:

* Weitere Informationen zu den Protokollierungs-APIs finden Sie im [Protokollierungs-API-Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb).

* Weitere Informationen zum Verwalten von Ausführungen mit dem Azure Machine Learning-SDK finden Sie in dem Beitrag zum [Verwalten von Notebooks von Ausführungen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Protokollieren von Metriken für Ihre Experimente finden Sie unter [Protokollieren von Metriken während Trainingsausführungen](how-to-track-experiments.md).
* Informationen zum Überwachen von Ressourcen und Protokollen aus Azure Machine Learning finden Sie unter [Überwachen von Azure Machine Learning](monitor-azure-machine-learning.md).