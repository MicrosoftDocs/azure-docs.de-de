---
title: Erstellen und Trainieren von Modellen
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Modelle mit Azure Machine Learning trainieren. Erkunden Sie die verschiedenen Trainingsmethoden, und wählen Sie die richtige für Ihr Projekt aus.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 05/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: 2448f5f778f19674aec63291acb72536c65ca6c9
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555888"
---
# <a name="train-models-with-azure-machine-learning"></a>Trainieren von Modellen mit Azure Machine Learning

Azure Machine Learning bietet verschiedene Methoden zum Trainieren von Modellen, von Code-First-Lösungen mit dem SDK bis zu Low-Code-Lösungen wie automatisiertes maschinelles Lernen und visueller Designer. Anhand der folgenden Liste können Sie ermitteln, welche Trainingsmethode für Sie die richtige ist:

+ [Azure Machine Learning SDK für Python](#python-sdk): Das Python SDK bietet verschiedene Möglichkeiten, Modelle mit jeweils unterschiedlichen Funktionen zu trainieren.

    | Trainingsmethode | BESCHREIBUNG |
    | ----- | ----- |
    | [Laufzeitkonfiguration](#run-configuration) | Eine **typische Möglichkeit zum Trainieren von Modellen** besteht darin, ein Trainingsskript und eine Laufzeitkonfiguration zu verwenden. Die Laufzeitkonfiguration stellt die zum Konfigurieren der Trainingsumgebung, erforderlichen Informationen bereit, in der Ihr Modell trainiert wird. Sie können Ihr Trainingsskript, Ihr Computeziel und Ihre Azure ML-Umgebung in Ihrer Laufzeitkonfiguration angeben und einen Trainingsauftrag ausführen. |
    | [Automatisiertes maschinelles Lernen](#automated-machine-learning) | Mithilfe des automatisierten maschinellen Lernens können Sie **Modelle ohne umfassende Data Science- oder Programmierkenntnisse trainieren**. Für Personen mit Data Science- und Programmierungshintergrund bietet es eine Möglichkeit, Zeit und Ressourcen zu sparen, indem die Algorithmusauswahl und die Hyperparameteroptimierung automatisiert werden. Bei der Verwendung des automatisierten maschinellen Lernens müssen Sie sich nicht um die Definition einer Laufzeitkonfiguration kümmern. |
    | [Machine Learning-Pipeline](#machine-learning-pipeline) | Pipelines sind keine andere Trainingsmethode, sondern eine **Möglichkeit, einen Workflow mit modularen, wiederverwendbaren Schritten zu definieren**, die Training als Teil des Workflows enthalten können. Machine Learning-Pipelines unterstützen die Verwendung des automatisierten maschinellen Lernens sowie der Laufzeitkonfiguration zum Trainieren von Modellen. Da Pipelines nicht speziell auf das Training ausgerichtet sind, variieren die Gründe für den Einsatz einer Pipeline stärker als die anderen Trainingsmethoden. Im Allgemeinen können Sie eine Pipeline in folgenden Situationen verwenden:<br>* Sie möchten **unbeaufsichtigte Prozesse planen**, z. B. zeitintensive Trainingsaufträge oder Datenaufbereitungen.<br>* Sie möchten **mehrere Schritte** verwenden, die über heterogene Computeressourcen und Speicherorte hinweg koordiniert sind.<br>* Sie möchten die Pipeline als **wiederverwendbare Vorlage** für bestimmte Szenarien verwenden, z. B. für erneutes Training oder Batchbewertungen.<br>* **Nachverfolgung und Versionierung von Datenquellen, Eingaben und Ausgaben** für Ihren Workflow.<br>* Ihr Workflow wird von **verschiedenen Teams implementiert, die unabhängig voneinander an bestimmten Schritten arbeiten**. Die Schritte können dann in einer Pipeline zusammengeführt werden, um den Workflow zu implementieren. |

+ [Azure Machine Learning SDK für R (Vorschauversion)](#r-sdk-preview): Das SDK für R verwendet das Paket „reticulate“ zur Bindung an das Python-SDK von Azure Machine Learning. Dadurch erhalten Sie Zugriff auf die im Python-SDK implementierten Kernobjekte und -methoden aus jeder R-Umgebung.

+ **Designer**: Azure Machine Learning-Designer bietet einen einfachen Einstiegspunkt in das maschinelle Lernen zum Erstellen von Proof of Concepts oder für Benutzer mit wenig Programmiererfahrung. Sie ermöglicht es Ihnen, Modelle per Drag & Drop über eine webbasierte Benutzeroberfläche zu trainieren. Sie können Python-Code als Teil des Designs verwenden oder Modelle trainieren, ohne Code zu schreiben.

+ **CLI**: Die Befehlszeilenschnittstelle für das maschinelle Lernen stellt Befehle für häufige Aufgaben mit Azure Machine Learning bereit und wird häufig für **Skripting- und Automatisierungsaufgaben** verwendet. Nachdem Sie z. B. ein Trainingsskript oder eine Pipeline erstellt haben, können Sie mit der Befehlszeilenschnittstelle einen Trainingsdurchlauf starten, der sich nach einem Zeitplan oder nach der Aktualisierung der für das Training verwendeten Datendateien richtet. Für Trainingsmodelle werden Befehle bereitgestellt, die Trainingsaufträge übermitteln. Sie kann Aufträge über Laufzeitkonfigurationen oder Pipelines übermitteln.

Jede dieser Trainingsmethoden kann verschiedene Arten von Computeressourcen für das Training verwenden. Zusammenfassend werden diese Ressourcen als [__Computeziele__](concept-azure-machine-learning-architecture.md#compute-targets) bezeichnet. Ein Computeziel kann ein lokaler Computer oder eine Cloudressource sein, wie beispielsweise Azure Machine Learning Compute, Azure HDInsight oder ein virtueller Remotecomputer.

## <a name="python-sdk"></a>Python SDK

Das Azure Machine Learning SDK für Python ermöglicht es Ihnen, Workflows für maschinelles Lernen mit Azure Machine Learning zu erstellen und auszuführen. Sie können mit dem Dienst über eine interaktive Python-Sitzung, Jupyter Notebooks, Visual Studio Code oder eine andere integrierte Entwicklungsumgebung (IDE) interagieren.

* [Was ist das Azure Machine Learning SDK für Python?](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)
* [Installieren/Aktualisieren des SDKs](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)
* [Konfigurieren einer Entwicklungsumgebung für Azure Machine Learning](how-to-configure-environment.md)

### <a name="run-configuration"></a>Laufzeitkonfiguration

Ein generischer Trainingsauftrag mit Azure Machine Learning kann unter Verwendung von [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) definiert werden. Die Skriptlaufzeitkonfiguration wird dann zusammen mit Ihren Trainingsskripts verwendet, um ein Modell auf einem Computeziel zu trainieren.

Sie können mit einer Laufzeitkonfiguration für Ihren lokalen Computer beginnen und dann bei Bedarf zu einer Laufzeitkonfiguration für ein cloudbasiertes Computeziel wechseln. Wenn Sie das Computeziel ändern, wird nur die von Ihnen verwendete Laufzeitkonfiguration geändert. Eine Ausführung protokolliert auch Informationen zum Trainingsauftrag wie Eingaben, Ausgaben und Protokolle.

* [Was ist eine Laufzeitkonfiguration?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Tutorial: Trainieren Ihres ersten ML-Modells](tutorial-1st-experiment-sdk-train.md)
* [Beispiele: Jupyter Notebook- und Python-Beispiele für Trainingsmodelle](https://github.com/Azure/azureml-examples)
* [Vorgehensweise: Konfigurieren einer Trainingsausführung](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Automatisiertes maschinelles Lernen

Definieren Sie Iterationen, Hyperparametereinstellungen, Featurebereitstellungen und andere Einstellungen. Während des Trainings testet Azure Machine Learning verschiedene Algorithmen und Parameter gleichzeitig. Das Training wird beendet, sobald es die von Ihnen definierten Beendigungskriterien erfüllt.

> [!TIP]
> Zusätzlich zum Python SDK können Sie automatisiertes maschinelles Lernen auch über [Azure Machine Learning-Studio](https://ml.azure.com) verwenden.

* [Was ist automatisiertes maschinelles Lernen?](concept-automated-ml.md)
* [Tutorial: Erstellen Ihres ersten Klassifizierungsmodells mit automatisiertem maschinellen Lernen](tutorial-first-experiment-automated-ml.md)
* [Tutorial: Vorhersagen von Preisen für Taxifahrten mit automatisiertem maschinellen Lernen](tutorial-auto-train-models.md)
* [Beispiele: Jupyter Notebook-Beispiele für automatisiertes maschinelles Lernen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Vorgehensweise: Konfigurieren automatisierter ML-Experimente in Python](how-to-configure-auto-train.md)
* [Vorgehensweise: Automatisches Trainieren eines Modells für die Zeitreihenprognose](how-to-auto-train-forecast.md)
* [Vorgehensweise: Erstellen, Untersuchen und Bereitstellen von Experimenten mit automatisiertem maschinellem Lernen mit Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md)

### <a name="machine-learning-pipeline"></a>Machine Learning-Pipeline

Von Machine Learning-Pipelines können die zuvor erwähnten Trainingsmethoden verwendet werden. Bei Pipelines geht es mehr um die Erstellung eines Workflows, daher umfassen sie mehr als nur das Training von Modellen. In einer Pipeline können Sie ein Modell mit automatisiertem maschinellen Lernen oder Laufzeitkonfigurationen trainieren.

* [Was sind ML-Pipelines in Azure Machine Learning?](concept-ml-pipelines.md)
* [Erstellen und Ausführen von Machine Learning-Pipelines mit dem Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
* [Tutorial: Verwenden von Azure Machine Learning-Pipelines für die Batchbewertung](tutorial-pipeline-batch-scoring-classification.md)
* [Beispiele: Jupyter Notebook-Beispiele für Machine Learning-Pipelines](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Beispiele: Pipeline mit automatisiertem maschinellen Lernen](https://aka.ms/pl-automl)

### <a name="understand-what-happens-when-you-submit-a-training-job"></a>Verstehen, was beim Übermitteln eines Trainingsauftrags passiert

Der Azure-Trainingslebenszyklus besteht aus folgenden Schritten:

1. Zippen der Dateien in Ihrem Projektordner, wobei die in _.amlignore_ oder _.gitignore_ angegebenen Dateien ignoriert werden
1. Hochskalieren Ihres Computeclusters 
1. Erstellen oder Herunterladen des Dockerfiles auf den Computeknoten 
    1. Das System berechnet einen Hashwert aus: 
        - Dem Basisimage 
        - Benutzerdefinierten Docker-Schritten (siehe [Bereitstellen eines Modells mithilfe eines benutzerdefinierten Docker-Basisimages](./how-to-deploy-custom-docker-image.md))
        - Der Conda-Definitions-YAML-Datei (siehe [Erstellen und Verwenden von Softwareumgebungen in Azure Machine Learning](./how-to-use-environments.md))
    1. Das System verwendet diesen Hash als Schlüssel in einer Suche nach der Azure Container Registry (ACR) für den Arbeitsbereich.
    1. Wenn er nicht gefunden wird, wird nach einer Übereinstimmung in der globalen ACR gesucht.
    1. Wenn keine gefunden wird, erstellt das System ein neues Image (das zwischengespeichert und bei der ACR des Arbeitsbereichs registriert wird).
1. Herunterladen der gezippten Projektdatei in den temporären Speicher auf dem Serverknoten
1. Entzippen der Projektdatei
1. Ausführen von `python <entry script> <arguments>` auf dem Serverknoten
1. Speichern von Protokollen, Modelldateien und anderen Dateien, die in dem Speicherkonto, das dem Arbeitsbereich zugeordnet ist, in `./outputs` geschrieben werden
1. Herunterskalieren der Computevorgänge, einschließlich Entfernen des temporären Speichers 

Wenn Sie das Training auf Ihrem lokalen Computer ausführen möchten („für lokale Ausführung konfigurieren“), ist Docker nicht notwendig. Sie können Docker aber lokal verwenden, wenn Sie dies wünschen (ein Beispiel finden Sie im Abschnitt [Konfigurieren der ML-Pipeline](./how-to-debug-pipelines.md)).

## <a name="r-sdk-preview"></a>R SDK (Vorschau)

Mit dem R-SDK können Sie die Programmiersprache R mit Azure Machine Learning verwenden. Das SDK verwendet das Paket reticulate zur Bindung an das Python-SDK von Azure Machine Learning. Damit erhalten Sie in jeder R-Umgebung Zugriff auf wichtige Objekte und Methoden, die im Python SDK implementiert sind.

Weitere Informationen finden Sie in den folgenden Artikeln:

* [Tutorial: Erstellen eines Modells für logistische Regression](tutorial-1st-r-experiment.md)
* [Azure Machine Learning SDK für R – Referenz](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning-Designer

Mit dem Designer können Sie Modelle über eine Drag & Drop-Oberfläche in Ihrem Webbrowser trainieren.

+ [Was ist der Designer?](concept-designer.md)
+ [Tutorial: Prognostizieren von Fahrzeugpreisen](tutorial-designer-automobile-price-train-score.md)

## <a name="many-models-solution-accelerator"></a>Many Models Solution Accelerator (Projektmappenbeschleuniger für viele Modelle)

Der [Many Models Solution Accelerator](https://aka.ms/many-models) (Preview) (Projektmappenbeschleuniger für viele Modelle (Vorschau)) baut auf Azure Machine Learning auf und ermöglicht Ihnen Training, Betrieb und Verwaltung von hunderten oder sogar tausenden von Machine Learning-Modellen.

Die Erstellung eines Modells __für jede Instanz oder jede Einzelperson__ kann in den folgenden Szenarien z. B. zu verbesserten Ergebnissen führen:

* Vorhersage der Umsätze für jedes einzelne Geschäft
* Vorausschauende Wartung für Hunderte von Ölquellen
* Anpassen einer Erfahrung für einzelne Benutzer

Weitere Informationen finden Sie auf GitHub unter [Many Models Solution Accelerator](https://aka.ms/many-models) (Projektmappenbeschleuniger für viele Modelle).

## <a name="cli"></a>Befehlszeilenschnittstelle (CLI)

Die Machine Learning-Befehlszeilenschnittstelle ist eine Erweiterung für die Azure-Befehlszeilenschnittstelle. Es bietet plattformübergreifende CLI-Befehle für die Arbeit mit Azure Machine Learning. In der Regel verwenden Sie die Befehlszeilenschnittstelle zur Automatisierung von Aufgaben, z. B. das Training eines Machine Learning-Modells.

* [Verwenden der CLI-Erweiterung für Azure Machine Learning](reference-azure-machine-learning-cli.md)
* [MLOps unter Azure](https://github.com/microsoft/MLOps)

## <a name="vs-code"></a>VS-Code

Sie können mit der VS Code-Erweiterung Ihre Trainingsaufträge ausführen und verwalten. Weitere Informationen finden Sie unter [Verwalten von Azure Machine Learning-Ressourcen mit der VS Code-Erweiterung (Vorschau)](how-to-manage-resources-vscode.md#experiments).

## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich mit dem [Konfigurieren einer Trainingsausführung](how-to-set-up-training-targets.md) vertraut.