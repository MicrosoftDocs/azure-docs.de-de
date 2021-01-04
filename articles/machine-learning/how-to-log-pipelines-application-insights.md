---
title: Überwachen und Sammeln von Pipelineprotokolldateien
titleSuffix: Azure Machine Learning
description: Fügen Sie Ihren Trainings- und Batchbewertungspipelines Protokollierung hinzu, und zeigen Sie die protokollierten Ergebnisse in Application Insights an.
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.date: 08/11/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 624409be4d7e2cfba37dbe16e083904766ae1389
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93309663"
---
# <a name="collect-machine-learning-pipeline-log-files-in-application-insights-for-alerts-and-debugging"></a>Sammeln von Machine Learning-Pipelineprotokolldateien in Application Insights für Warnungen und Debuggen


Die [OpenCensus](https://opencensus.io/quickstart/python/)-Python-Bibliothek kann zum Weiterleiten von Protokollen an Application Insights in Ihren Skripts verwendet werden. Durch das Aggregieren der Protokolle von Pipelineausführungen an einem zentralen Ort können Sie Abfragen erstellen und Probleme diagnostizieren. Mithilfe von Application Insights können Sie Protokolle im Lauf der Zeit nachverfolgen und Pipelineprotokolle zwischen den Ausführungen vergleichen.

Dadurch, dass sich alle Ihre Protokolle am selben Speicherort befinden, erhalten Sie einen Verlauf der Ausnahmen und Fehlermeldungen. Da Application Insights in Azure-Warnungen integriert ist, können Sie auch Warnungen basierend auf Application Insights Abfragen erstellen.

## <a name="prerequisites"></a>Voraussetzungen

* Führen Sie die Schritte zum Erstellen eines [Azure Machine Learning](./how-to-manage-workspace.md)-Arbeitsbereichs aus, und [erstellen Sie Ihre erste Pipeline](./how-to-create-your-first-pipeline.md).
* [Konfigurieren Sie Ihre Entwicklungsumgebung](./how-to-configure-environment.md) für die Installation des Azure Machine Learning-SDKs.
* Installieren Sie das [OpenCensus Azure Monitor Exporter](https://pypi.org/project/opencensus-ext-azure/)-Paket lokal:
  ```python
  pip install opencensus-ext-azure
  ```
* Erstellen Sie eine [Application Insights-Instanz](../azure-monitor/app/opencensus-python.md). (Dieses Dokument enthält auch Informationen zum Abrufen der Verbindungszeichenfolge für die Ressource.)

## <a name="getting-started"></a>Erste Schritte

Dieser Abschnitt ist eine Einführung in die Verwendung von OpenCensus aus einer Azure Machine Learning-Pipeline. Ein ausführliches Tutorial finden Sie unter [OpenCensus Azure Monitor Exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure).

Fügen Sie Ihrer Azure Machine Learning-Pipeline einen PythonScriptStep hinzu. Konfigurieren Sie Ihre [RunConfiguration](/python/api/azureml-core/azureml.core.runconfiguration?preserve-view=true&view=azure-ml-py) mit der Abhängigkeit von opencensus-ext-azure. Konfigurieren Sie die Umgebungsvariable `APPLICATIONINSIGHTS_CONNECTION_STRING`.

```python
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import RunConfiguration
from azureml.pipeline.core import Pipeline
from azureml.pipeline.steps import PythonScriptStep

# Connecting to the workspace and compute target not shown

# Add pip dependency on OpenCensus
dependencies = CondaDependencies()
dependencies.add_pip_package("opencensus-ext-azure>=1.0.1")
run_config = RunConfiguration(conda_dependencies=dependencies)

# Add environment variable with Application Insights Connection String
# Replace the value with your own connection string
run_config.environment.environment_variables = {
    "APPLICATIONINSIGHTS_CONNECTION_STRING": 'InstrumentationKey=00000000-0000-0000-0000-000000000000'
}

# Configure step with runconfig
sample_step = PythonScriptStep(
        script_name="sample_step.py",
        compute_target=compute_target,
        runconfig=run_config
)

# Submit new pipeline run
pipeline = Pipeline(workspace=ws, steps=[sample_step])
pipeline.submit(experiment_name="Logging_Experiment")
```

Erstellen Sie eine Datei namens `sample_step.py`. Importieren Sie die AzureLogHandler-Klasse, um Protokolle an Application Insights weiterzuleiten. Sie müssen auch die Python-Protokollierungsbibliothek importieren.

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

Fügen Sie als Nächstes der Python-Protokollierung AzureLogHandler hinzu.

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>Protokollierung mit benutzerdefinierten Dimensionen
 
Standardmäßig haben an Application Insights weitergeleitete Protokolle keinen ausreichenden Kontext für die Rückverfolgung auf die Ausführung oder das Experiment. Damit die Protokolle für die Diagnose von Problemen verwendet werden können, sind zusätzliche Felder erforderlich. 

Dazu können benutzerdefinierte Dimensionen als Felder hinzugefügt werden, um Kontext für eine Protokollnachricht bereitzustellen. Ein Beispiel hierfür ist, wenn ein Benutzer die Protokolle für mehrere Schritte in derselben Pipelineausführung anzeigen möchte.

Benutzerdefinierte Dimensionen bilden ein Wörterbuch von Schlüssel-Wert-Paaren (in der Form „Zeichenfolge, Zeichenfolge“ gespeichert). Das Wörterbuch wird dann an Application Insights gesendet und als Spalte in den Abfrageergebnissen angezeigt. Die einzelnen Dimensionen können als [Abfrageparameter](#additional-helpful-queries) verwendet werden.

### <a name="helpful-context-to-include"></a>Hilfreicher Kontext zum Einschließen

| Feld                          | Begründung/Beispiel                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | Abfragen von Protokollen für andere mit derselben parent_run_id, um die Protokolle im Zeitverlauf für alle Schritte anzuzeigen, anstatt jeden Schritt einzeln zu untersuchen                                        |
| step_id                        | Abfragen von Protokollen mit derselben step_id, um zu sehen, wo ein Problem mit einem engen Bereich nur für den einzelnen Schritt aufgetreten ist                                                        |
| step_name                      | Abfragen von Protokollen, um die Schrittleistung im Zeitverlauf anzuzeigen. Unterstützt auch das Auffinden einer step_id für kürzliche Ausführungen, ohne die Benutzeroberfläche des Portals anzuzeigen                                          |
| experiment_name                | Abfragen über Protokolle hinweg, um die Leistung des Experiments im Zeitverlauf anzuzeigen. Unterstützt auch das Auffinden einer parent_run_id oder step_id für kürzliche Ausführungen, ohne die Benutzeroberfläche des Portals anzuzeigen                   |
| run_url                 | Bereitstellen eines Links direkt zurück zur Runtime für die weitere Untersuchung |

**Weitere nützliche Felder**

Diese Felder erfordern möglicherweise eine weitere Codeinstrumentierung und werden nicht vom Ausführungskontext bereitgestellt.

| Feld                   | Begründung/Beispiel                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | Wenn Sie für die Bereitstellung CI/CD verwenden, kann dieses Feld Protokolle mit der Codeversion, die die Schritt- und Pipelinelogik bereitgestellt hat, korrelieren. Dieser Link kann weiter bei der Diagnose von Problemen oder beim Identifizieren von Modellen mit bestimmten Merkmalen (Protokoll-/Metrikwerte) helfen. |
| run_type                       | Unterscheiden zwischen verschiedenen Modelltypen oder zwischen Trainings- und Bewertungsausführungen |

### <a name="creating-a-custom-dimensions-dictionary"></a>Erstellen eines benutzerdefinierten Dimensionswörterbuchs

```python
from azureml.core import Run

run = Run.get_context(allow_offline=False)

custom_dimensions = {
    "parent_run_id": run.parent.id,
    "step_id": run.id,
    "step_name": run.name,
    "experiment_name": run.experiment.name,
    "run_url": run.parent.get_portal_url(),
    "run_type": "training"
}

# Assumes AzureLogHandler was already registered above
logger.info("I will be sent to Application Insights with Custom Dimensions", custom_dimensions)
```

## <a name="opencensus-python-logging-considerations"></a>Überlegungen zur Python-Protokollierung mit OpenCensus

Der OpenCensus-AzureLogHandler wird zum Weiterleiten von Python-Protokollen an Application Insights verwendet. Daher sollten die Besonderheiten der Python-Protokollierung berücksichtigt werden. Wenn eine Protokollierung erstellt wird, weist sie eine Standardprotokollebene auf und zeigt Protokolle an, die sich auf oder über dieser Ebene befinden. Eine gute Referenz zur Verwendung der Python-Protokollierungsfunktionen stellt das [Logging Cookbook](https://docs.python.org/3/howto/logging-cookbook.html) dar.

Die Umgebungsvariable `APPLICATIONINSIGHTS_CONNECTION_STRING` ist für die OpenCensus-Bibliothek erforderlich. Es wird empfohlen, diese Umgebungsvariable festzulegen, anstatt sie als Pipelineparameter zu übergeben, um die Weitergabe von Textverbindungszeichenfolgen zu vermeiden.

## <a name="querying-logs-in-application-insights"></a>Abfragen von Protokollen in Application Insights

Die an Application Insights weitergeleiteten Protokolle werden als „Ablaufverfolgungen“ oder „Ausnahmen“ aufgeführt. Passen Sie Ihr Zeitfenster unbedingt so an, dass die Pipelineausführung enthalten ist.

![Abfrageergebnisse von Application Insights](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

Das Ergebnis in Application Insights enthält die Protokollmeldung und -ebene, den Dateipfad und die Nummer der Codezeile. Außerdem werden alle enthaltenen benutzerdefinierten Dimensionen angezeigt. In dieser Abbildung weist das Wörterbuch „customDimensions“ die Schlüssel-Wert-Paare aus dem vorherigen [Codebeispiel](#creating-a-custom-dimensions-dictionary) auf.

### <a name="additional-helpful-queries"></a>Weitere nützliche Abfragen

Einige der nachstehenden Abfragen verwenden „customDimensions.Level“. Diese Schweregrade entsprechen der Ebene, mit der das Python-Protokoll ursprünglich gesendet wurde. Weitere Abfrageinformationen finden Sie unter [Protokollabfragen in Azure Monitor](/azure/data-explorer/kusto/query/).

| Anwendungsfall                                                               | Abfrage                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Protokollergebnisse für eine bestimmte benutzerdefinierte Dimension, z. B. „parent_run_id“ | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| Protokollergebnisse für alle Trainingsausführungen in den letzten 7 Tagen                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| Protokollergebnisse mit dem severityLevel „Fehler“ in den letzten 7 Tagen              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| Anzahl der Protokollergebnisse mit dem severityLevel „Fehler“ in den letzten 7 Tagen     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie in Ihrer Application Insights-Instanz Protokolle verwendet haben, können Sie sie zum Festlegen von [Azure Monitor-Warnungen](../azure-monitor/platform/alerts-overview.md#what-you-can-alert-on) basierend auf den Abfrageergebnissen verwenden.

Sie können auch einem [Azure-Dashboard](../azure-monitor/learn/tutorial-app-dashboards.md#add-logs-query) Ergebnisse aus Abfragen hinzufügen, um weitere Erkenntnisse zu gewinnen.