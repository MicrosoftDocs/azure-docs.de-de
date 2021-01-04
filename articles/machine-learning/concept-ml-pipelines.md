---
title: Was sind Azure Machine Learning-Pipelines?
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mithilfe von ML-Pipelines Machine-Learning-Workflows erstellen, optimieren und verwalten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 08/17/2020
ms.custom: devx-track-python
ms.openlocfilehash: c29ee87ab177357f4289134bb39353c764a0d75b
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94535298"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Beschreibung von Azure Machine Learning-Pipelines

In diesem Artikel erfahren Sie, wie Sie mithilfe von Machine Learning-Pipelines Machine-Learning-Workflows erstellen, optimieren und verwalten. Diese Workflows bieten eine Reihe von Vorteilen: 

+ Einfachheit
+ Geschwindigkeit
+ Wiederholbarkeit
+ Flexibilität
+ Versionsverwaltung und Nachverfolgung
+ Modularität 
+ Qualitätssicherung
+ Kostenkontrolle

Diese Vorteile werden in dem Moment wichtig, da Ihr Machine Learning-Projekt das Feld des reinen Experiments verlässt und in die Weiterentwicklung eintritt. Sogar einfache Pipelines mit nur einem Schritt können nützlich sein. Machine Learning-Projekte befinden sich oftmals in einem komplexen Zustand, und es kann wirklich erholsam sein, aus der präzisen Erfüllung eines einzelnen Workflows einen trivialen Vorgang zu machen.

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Welche Azure-Pipelinetechnologie sollte ich verwenden?

Die Azure-Cloud bietet mehrere andere Pipelines, die jeweils einem anderen Zweck dienen. Die folgende Tabelle listet die verschiedenen Pipelines und ihren Verwendungszweck auf:

| Szenario | Primäre Persona | Angebot von Azure | OSS-Angebot | Kanonische Pipe | Stärken | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Modellorchestrierung (maschinelles Lernen) | Data Scientist | Azure Machine Learning-Pipelines | Kubeflow-Pipelines | Daten -> Modell | Verteilung, Caching, Code-First, Wiederverwendung | 
| Datenorchestrierung (Datenvorbereitung) | Datentechniker | [Azure Data Factory-Pipelines](../data-factory/concepts-pipelines-activities.md) | Apache Airflow | Daten -> Daten | Stark typisierte Verschiebung, datenorientierte Aktivitäten |
| Code- und App-Orchestrierung (CI/CD) | App-Entwickler/Vorgänge | [Azure DevOps Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Code + Modell -> App/Dienst | Offenste und flexibelste Aktivitätsunterstützung, Genehmigungswarteschlangen, Phasen mit Beschränkung | 

## <a name="what-can-azure-ml-pipelines-do"></a>Wozu sind Azure ML-Pipelines imstande?

Eine Azure Machine Learning-Pipeline ist ein unabhängig ausführbarer Workflow einer vollständigen Machine Learning-Aufgabe. Teilaufgaben werden als eine Reihe von Schritten in der Pipeline gekapselt. Eine Azure Machine Learning-Pipeline kann ganz einfach sein und nur aus dem Aufruf eines Python-Skripts bestehen, also _kann_ sie fast alles. Pipelines _sollten_ aber den Schwerpunkt auf Machine Learning-Aufgaben legen, wie etwa:

+ Datenvorbereitung einschließlich Import, Validierung und Bereinigung, Verfremdung und Transformation, Normalisierung und Staging
+ Trainingskonfiguration einschließlich Parametrisierungsargumenten, Dateipfaden und Protokollierungs-/Berichtskonfigurationen
+ Effizienz und Wiederholung bei Training und Überprüfung. Effizienz ergibt sich möglicherweise durch die Angabe von bestimmten Datenteilmengen, verschiedenen Hardware-Computeressourcen sowie durch verteilte Verarbeitung und Fortschrittsüberwachung
+ Bereitstellung, einschließlich Versionierung, Skalierung, Bereitstellung und Zugriffssteuerung

Durch unabhängige Schritte können mehrere Datenanalysten gleichzeitig an derselben Pipeline arbeiten, ohne die Computeressourcen zu überlasten. Außerdem ist es bei einzelnen Schritten einfacher, für jeden Schritt verschiedene Computetypen/-größen zu verwenden.

Nachdem die Pipeline entworfen wurde, wird ihr Trainingsprozess in der Regel weiter optimiert. Wenn Sie eine Pipeline erneut ausführen, springt die Ausführung zu den Schritten, die wiederholt werden müssen, z.B. zu einem aktualisierten Trainingsskript. Schritte, die nicht erneut ausgeführt werden müssen, werden übersprungen. 

Mit Pipelines können Sie verschiedene Hardware für verschiedene Aufgaben verwenden. Azure koordiniert die verschiedenen [Computeziele](concept-azure-machine-learning-architecture.md), die Sie verwenden, sodass Ihre Zwischendaten nahtlos zu den nachfolgenden Computezielen fließen.

Für das [Nachverfolgen der Metriken für Ihre Pipelineexperimente](./how-to-track-experiments.md) haben Sie zwei Möglichkeiten: Nachverfolgung direkt im Azure-Portal oder über die [Landing Page Ihres Arbeitsbereichs (Vorschau)](https://ml.azure.com). Nach dem Veröffentlichen einer Pipeline können Sie einen REST-Endpunkt konfigurieren, mit dem Sie die Pipeline über eine beliebige Plattform bzw. einen beliebigen Stapel erneut ausführen können.

Kurz gesagt können alle komplexen Aufgaben des Machine Learning-Lebenszyklus mithilfe von Pipelines unterstützt werden. Andere Azure-Pipelinetechnologien weisen ihre eigenen Stärken auf. [Azure Data Factory-Pipelines](../data-factory/concepts-pipelines-activities.md) zeichnen sich durch die Arbeit mit Daten aus und [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) ist das richtige Tool für Continuous Integration und Continuous Deployment. Wenn Ihr Schwerpunkt jedoch auf Machine Learning liegt, sind Azure Machine Learning-Pipelines wahrscheinlich die beste Wahl für Ihre Workflowanforderungen. 

### <a name="analyzing-dependencies"></a>Analysieren von Abhängigkeiten

Viele Programmierökosysteme weisen Tools zum Orchestrieren von Ressourcen-, Bibliotheks- oder Kompilierungsabhängigkeiten auf. Im Allgemeinen verwenden diese Tools Dateizeitstempel zum Berechnen von Abhängigkeiten. Wenn eine Datei geändert wird, werden nur sie und ihre Abhängigkeiten aktualisiert (heruntergeladen, erneut kompiliert oder verpackt). Azure ML-Pipelines erweitern dieses Konzept. Wie herkömmliche Buildtools errechnen Pipelines Abhängigkeiten zwischen Schritten und wiederholen nur die erforderlichen Neuberechnungen. 

Die Abhängigkeitsanalyse in Azure ML-Pipelines ist jedoch anspruchsvoller als einfache Zeitstempel. Jeder Schritt kann in einer anderen Hardware- und Softwareumgebung ausgeführt werden. Die Datenaufbereitung ist möglicherweise ein zeitaufwändiger Vorgang, sie muss aber nicht auf Hardware mit leistungsstarken GPUs ausgeführt werden, für bestimmte Schritte ist vielleicht betriebssystemspezifische Software erforderlich, oder Sie entscheiden sich für verteiltes Training usw. 

Azure Machine Learning orchestriert automatisch alle Abhängigkeiten zwischen den Schritten der Pipeline. Diese Orchestrierung kann das Hoch- und Herunterfahren von Docker-Images, das Einbinden und Trennen von Computeressourcen und das automatisierte Verschieben der Daten zwischen den einzelnen Schritten in konsistenter Weise beinhalten.

### <a name="coordinating-the-steps-involved"></a>Koordinieren der beteiligten Schritte

Beim Erstellen und Ausführen eines `Pipeline`-Objekts finden die folgenden Schritte auf einer allgemeinen Ebene statt:

+ Für jeden Schritt berechnet der Dienst die Anforderungen für:
    + Hardware-Computeressourcen
    + Betriebssystemressourcen (Docker-Image(s))
    + Softwareresources (Conda/virtualenv-Abhängigkeiten)
    + Dateneingaben 
+ Der Dienst bestimmt die Abhängigkeiten zwischen Schritten, was zu einem dynamischen Ausführungsdiagramm führt
+ Wenn jeder Knoten in einem Ausführungsdiagramm ausgeführt wird:
    + konfiguriert der Dienst die erforderlichen Hardware- und Softwareumgebungen (möglicherweise unter Wiederverwendung vorhandener Ressourcen)
    + Der Schritt wird ausgeführt und bietet Protokollierungs- und Überwachungsinformationen für das ihn enthaltende `Experiment`-Objekt
    + Wenn der Schritt abgeschlossen wird, werden seine Ausgaben als Eingaben für den nächsten Schritt vorbereitet und/oder in den Speicher geschrieben
    + Nicht mehr benötigte Ressourcen werden finalisiert und getrennt

![Pipelineschritte](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Erstellen von Pipelines mit dem Python-SDK

Im [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) ist eine Pipeline ein Python-Objekt, das im `azureml.pipeline.core`-Modul definiert ist. Ein [Pipeline](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?preserve-view=true&view=azure-ml-py)-Objekt enthält eine geordnete Abfolge von einem oder mehreren [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?preserve-view=true&view=azure-ml-py)-Objekten. Die `PipelineStep`-Klasse ist abstrakt, und die eigentlichen Schritte gehören Unterklassen an, wie etwa [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?preserve-view=true&view=azure-ml-py), [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?preserve-view=true&view=azure-ml-py) oder [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?preserve-view=true&view=azure-ml-py). Die [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?preserve-view=true&view=azure-ml-py)-Klasse enthält eine wiederverwendbare Abfolge von Schritten, die von mehreren Pipelines gemeinsam verwendet werden können. Eine `Pipeline` wird als Teil einer `Experiment` ausgeführt.

Eine Azure ML-Pipeline ist einem Azure Machine Learning-Arbeitsbereich zugeordnet, und ein Pipelineschritt ist einem verfügbaren Computeziel in diesem Arbeitsbereich zugeordnet. Weitere Informationen finden Sie unter [Erstellen und Verwalten von Azure Machine Learning-Arbeitsbereichen im Azure-Portal](./how-to-manage-workspace.md) oder [Was sind Computeziele in Azure Machine Learning?](./concept-compute-target.md).

### <a name="a-simple-python-pipeline"></a>Eine einfache Python-Pipeline

Dieser Codeausschnitt zeigt die Objekte und Aufrufe, die zum Erstellen und Ausführen einer `Pipeline` erforderlich sind:

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))

output_data = PipelineData("output_data", datastore=blob_store)

input_named = input_data.as_named_input('input')

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_named.as_download(), "--output", output_data],
    inputs=[input_data],
    outputs=[output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

Dieser Codeausschnitt beginnt mit gebräuchlichen Azure Machine Learning-Objekten, einem `Workspace`, einem `Datastore`, einem [ComputeTarget](/python/api/azureml-core/azureml.core.computetarget?preserve-view=true&view=azure-ml-py) und einem `Experiment`. Anschließend erstellt der Code die Objekte, die `input_data` und `output_data` aufnehmen. Das Array `steps` enthält ein einzelnen Element, ein `PythonScriptStep`, das die Datenobjekte verwendet und auf dem `compute_target` ausgeführt wird. Anschließend instanziiert der Code das eigentliche `Pipeline`-Objekt und übergibt den Workspace und das Array der Schritte. Mit dem Aufruf von `experiment.submit(pipeline)` beginnt die Ausführung der Azure ML-Pipeline. Der Aufruf von `wait_for_completion()` wird dann bis zum Abschluss der Pipeline gesperrt. 

Weitere Informationen zum Verbinden Ihrer Pipeline mit Ihren Daten finden Sie in den Artikeln [Datenzugriff in Azure Machine Learning](concept-data.md) und [Verschieben von Daten in ML-Pipelineschritte und zwischen ML-Pipelineschritten (Python)](how-to-move-data-in-out-of-pipelines.md). 

## <a name="building-pipelines-with-the-designer"></a>Erstellen von Pipelines mit dem Designer

Entwickler, die eine visuelle Entwurfsoberfläche bevorzugen, können Pipelines mit dem Azure Machine Learning-Designer erstellen. Sie können über die Auswahl **Designer** auf der Startseite Ihres Arbeitsbereichs auf dieses Tool zugreifen.  Der Designer ermöglicht Ihnen, Schritte per Drag & Drop auf die Entwurfsoberfläche zu verschieben. 

Wenn Sie Pipelines visuell entwerfen, werden die Eingaben und Ausgaben eines Schritts sichtbar angezeigt. Für die Datenverbindungen wird Drag & Drop unterstützt, sodass Sie den Datenfluss Ihrer Pipeline schnell verstehen und ändern können.

![Azure Machine Learning-Designer: Beispiel](./media/concept-designer/designer-drag-and-drop.gif)

## <a name="key-advantages"></a>Hauptvorteile

Die wichtigsten Argumente für das Verwenden von Pipelines für Ihre Workflows mit maschinellem Lernen sind die folgenden:

|Vorteil|BESCHREIBUNG|
|:-------:|-----------|
|**Unbeaufsichtigte&nbsp;Ausführung**|Planen Sie Schritte, die zuverlässig und unbeaufsichtigt parallel oder nacheinander ausgeführt werden können. Die Datenvorbereitung und -modellierung kann Tage oder Wochen in Anspruch nehmen, und mit Pipelines können Sie sich auf andere Aufgaben konzentrieren, während der Prozess ausgeführt wird. |
|**Heterogenes Compute**|Verwenden Sie mehrere Pipelines, die zuverlässig über heterogene und skalierbare Computeressourcen und Speicherorte koordiniert werden. Um die verfügbaren Computeoptionen effizient zu nutzen, können einzelne Pipelineschritte auf verschiedenen Computezielen ausgeführt werden, z. B. HDInsight, Data Science-VMs mit GPU und Databricks.|
|**Wiederverwendbarkeit**|Erstellen Sie Pipelinevorlagen für bestimmte Szenarien wie erneutes Training und Batchbewertung. Lösen Sie veröffentlichte Pipelines von externen Systemen über einfache REST-Aufrufe aus.|
|**Nachverfolgung und Versionierung**|Statt Daten- und Ergebnispfade bei der Iteration manuell zu verfolgen, verwenden Sie das Pipelines SDK, um Ihre Datenquellen, Eingaben und Ausgaben explizit zu benennen und Versionen zu verwalten. Sie können Skripts und Daten auch separat verwalten, um die Produktivität zu steigern.|
| **Modularität** | Das Trennen von Bereichen mit verschiedenen Anliegen und das Isolieren von Änderungen ermöglicht die schnellere Entwicklung von Software mit höherer Qualität. | 
|**Kollaboration**|Pipelines ermöglichen Data Scientists, in allen Bereichen des Entwurfsprozesses für maschinelles Lernen zusammenzuarbeiten, während sie gleichzeitig an Pipelineschritten arbeiten können.|

## <a name="next-steps"></a>Nächste Schritte

Azure ML-Pipelines stellen ein leistungsfähiges Hilfsmittel dar, das schon in frühen Entwicklungsphasen Mehrwert produziert. Der Wert wächst mit der Größe von Team und Projekt. In diesem Artikel wurde erläutert, wie Pipelines mit dem Azure Machine Learning Python SDK festgelegt und in Azure orchestriert werden. Sie haben etwas einfachen Quellcode gesehen und haben einige der verfügbaren `PipelineStep`-Klassen kennengelernt. Sie sollten ein Gefühl dafür entwickeln können, wann Azure ML-Pipelines eingesetzt werden und wie Azure sie ausführt. 


+ Erfahren Sie, wie Sie [Ihre erste Pipeline erstellen](how-to-create-your-first-pipeline.md).

+ Erfahren Sie, wie Sie [Batchvorhersagen für große Datenmengen ausführen](tutorial-pipeline-batch-scoring-classification.md ).

+ Weitere Informationen zum [Kern von Pipelines](/python/api/azureml-pipeline-core/?preserve-view=true&view=azure-ml-py) und den [Pipelineschritten](/python/api/azureml-pipeline-steps/?preserve-view=true&view=azure-ml-py) finden Sie in der SDK-Referenzdokumentation.

+ Testen Sie Beispiele für Jupyter Notebooks, die [Azure Machine Learning-Pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines) veranschaulichen. Erfahren Sie, wie Sie [Notebooks ausführen, um diesen Dienst zu untersuchen](samples-notebooks.md).