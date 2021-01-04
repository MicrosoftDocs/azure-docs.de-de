---
title: Bring Your Own ML in Azure Sentinel | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Sie Ihre eigenen Machine Learning-Algorithmen für die Datenanalyse in Azure Sentinel erstellen und verwenden.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: yelevin
ms.openlocfilehash: 6a891a301d5869603a7d90d28bb9063d7d5bdb1d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660575"
---
# <a name="bring-your-own-machine-learning-ml-into-azure-sentinel"></a>Bring Your Own Machine Learning (ML) in Azure Sentinel

Machine Learning (ML) ist eine der wichtigsten Grundlagen von Azure Sentinel und eines der wichtigsten Attribute, die es von anderen abhebt. Azure Sentinel bietet ML in verschiedenen Erfahrungen: integriert in die [Fusion](fusion.md)-Korrelations-Engine und in Jupyter-Notebooks sowie die neu verfügbare Build-Your-Own ML-Plattform (BYO-ML). 

ML-Erkennungsmodelle können sich an einzelne Umgebungen und Änderungen im Benutzerverhalten anpassen, um falsch positive Ergebnisse zu verringern und Bedrohungen zu identifizieren, die mit einem herkömmlichen Ansatz nicht gefunden würden. Viele Sicherheitsorganisationen verstehen den Wert von ML für die Sicherheit, obgleich nicht viele von Ihnen über den Luxus von Fachleuten verfügen, die Fachkenntnisse sowohl im Bereich Sicherheit als auch des ML besitzen. Wir haben das hier vorgestellte Framework für Sicherheitsorganisationen und Fachkräfte entwickelt, damit sie gemeinsam mit uns auf ihrer ML Journey wachsen können. Organisationen, die keine Erfahrung mit ML haben oder nicht das erforderliche Fachwissen besitzen, können einen erheblichen Schutzwert aus den integrierten ML-Funktionen von Azure Sentinel ziehen.

:::image type="content" source="./media/bring-your-own-ml/machine-learning-framework.png" alt-text="Machine Learning-Framework":::

## <a name="what-is-the-bring-your-own-machine-learning-byo-ml-platform"></a>Was ist die Bring Your Own Machine Learning-Plattform (BYO-ML)?

Organisationen, die über ML-Ressourcen verfügen und angepasste ML-Modelle für Ihre individuellen geschäftlichen Anforderungen erstellen möchten, bieten wir die **BYO-ML-Plattform**. Die Plattform nutzt die [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks)/[Apache Spark](http://spark.apache.org/)-Umgebung sowie Jupyter-Notebooks, um die ML-Umgebung zu erzeugen. Sie bietet die folgenden Komponenten:

- Ein BYO-ML-Paket, das Bibliotheken enthält, mit denen Sie auf Daten zugreifen und die Ergebnisse zurück an Log Analytics (LA) übertragen können, sodass Sie die Ergebnisse in Ihre Erkennung, Untersuchung und Suche integrieren können. 

- ML-Algorithmusvorlagen, die Sie so anpassen können, dass sie für bestimmte Sicherheitsprobleme in Ihrer Organisation passen. 

- Beispielnotebooks zum Trainieren des Modells und zum Planen der Modellbewertung. 

Außerdem können Sie Ihre eigenen ML-Modelle und/oder Ihre eigene Spark-Umgebung nutzen, um sie in Azure Sentinel zu integrieren.

Mit der BYO-ML-Plattform können Sie schnell loslegen und Ihre eigenen ML-Modelle entwickeln: 

- Das Notebook mit Beispieldaten hilft Ihnen dabei, praktische End-to-End-Erfahrungen zu sammeln, ohne sich Gedanken über die Verarbeitung von Produktionsdaten machen zu müssen.

- Das in die Spark-Umgebung integrierte Paket verringert die Herausforderungen und Probleme bei der Verwaltung der Infrastruktur.

- Die Bibliotheken unterstützen Datenverschiebungen. Trainings- und Bewertungsnotebooks veranschaulichen die End-to-End-Erfahrung und dienen als Vorlage, die Sie an Ihre Umgebung anpassen können.

### <a name="use-cases"></a>Anwendungsfälle
 
Die BYO-ML-Plattform und das Paket verkürzen signifikant die Zeit und den Aufwand, die Sie benötigen, um Ihre eigenen ML-Erkennungen zu erstellen, und sie verschaffen Ihnen die Möglichkeit, bestimmte Sicherheitsprobleme in Azure Sentinel zu behandeln. Die Plattform unterstützt die folgenden Anwendungsfälle:

**Trainieren eines ML-Algorithmus zur Erzeugung eines angepassten Modells:** Sie können einen vorhandenen ML-Algorithmus (von Microsoft oder von der Benutzercommunity geteilt) verwenden und diesen problemlos mit Ihren eigenen Daten trainieren, um ein angepasstes ML-Modell zu erhalten, das besser für Ihre Daten und Ihre Umgebung geeignet ist.

**Ändern einer ML-Algorithmusvorlage zur Erzeugung eines angepassten Modells:** Sie können eine ML-Algorithmusvorlage (von Microsoft oder von der Benutzercommunity geteilt) ändern und den geänderten Algorithmus mit Ihren eigenen Daten trainieren, um ein angepasstes Modell abzuleiten, das an Ihr spezifisches Problem angepasst ist.

**Erstellen Ihres eigenen Modells:** Erstellen Sie von Grund auf Ihr eigenes Modell mithilfe der BYO-ML-Plattform und der Hilfsprogramme von Azure Sentinel.

**Integrieren Ihrer Databricks/Spark-Umgebung:** Integrieren Sie Ihre vorhandene Databricks/Spark-Umgebung in Azure Sentinel, und verwenden Sie BYO-ML-Bibliotheken und -Vorlagen, um ML-Modelle für Ihre individuellen Situationen zu erstellen.

**Importieren Ihres eigenen ML-Modells:** Sie können Ihre eigenen ML-Modelle importieren und die BYO-ML-Plattform und -Hilfsprogramme verwenden, um sie in Azure Sentinel zu integrieren.

**Teilen eines ML-Algorithmus:** Teilen Sie einen ML-Algorithmus, den die Community übernehmen und anpassen kann.

**Verwenden von ML zur Unterstützung von SecOPs:** Verwenden Sie Ihr eigenes benutzerdefiniertes ML-Modell und dessen Ergebnisse für die Suche, Erkennungen, Untersuchungen und Antworten.

In diesem Artikel zeigen wir Ihnen die Komponenten der BYO-ML-Plattform und wie Sie die Plattform und den Algorithmus für anomalen Ressourcenzugriff verwenden, um eine angepasste ML-Erkennung mit Azure Sentinel bereitzustellen.

## <a name="azure-databricksspark-environment"></a>Azure Databricks/Spark-Umgebung

[Apache Spark™](http://spark.apache.org/) hat bei der Vereinfachung von Big Data einen großen Sprung vorwärts gemacht, indem es ein einheitliches Framework für das Erstellen von Datenpipelines bereitstellt. Azure Databricks geht noch darüber hinaus, indem es eine um Spark herum entwickelte, verwaltungsfreie Cloudplattform bereitstellt. Wir empfehlen, dass Sie Databricks für Ihre BYO-ML-Plattform verwenden, damit Sie sich auf das Finden von Antworten konzentrieren können, die unmittelbare Auswirkungen auf Ihr Unternehmen haben, anstatt Probleme mit Datenpipelines und Plattformen zu lösen.
Wenn Sie bereits über Databricks oder eine andere Spark-Umgebung verfügen und lieber die vorhandene Einrichtung verwenden möchten, funktioniert das BYO-ML-Paket auch damit ebenfalls problemlos. 

## <a name="byo-ml-package"></a>BYO-ML-Paket

Das BYO-ML-Paket integriert bewährte Methoden und Forschung von Microsoft in das ML-Front-End für Sicherheit. In diesem Paket stellen wir die folgende Liste von Hilfsprogrammen, Notebooks und Algorithmusvorlagen für Sicherheitsprobleme bereit.

| Dateiname | BESCHREIBUNG |
| --------- | ----------- |
| azure_sentinel_utilities.whl | Enthält Hilfsprogramme zum Lesen von Blobs aus Azure und zum Schreiben in Log Analytics. |
| AnomalousRASampleData | Ein Notebook, das die Verwendung des Modells für anomalen Ressourcenzugriff in Sentinel mit generierten Trainings- und Testbeispieldaten veranschaulicht. |
| AnomalousRATraining.ipynb | Ein Notebook zum Trainieren des Algorithmus, zum Erstellen und Speichern der Modelle. |
| AnomalousRAScoring.ipynb | Ein Notebook zum Planen der Ausführung des Modells, zum Visualisieren des Ergebnisses und zum Zurückschreiben der Bewertung in Azure Sentinel. |
|

Die erste ML-Algorithmusvorlage, die wir anbieten, ist für die [Erkennung anomalen Ressourcenzugriffs](https://github.com/Azure/Azure-Sentinel/tree/master/BYOML). Sie basiert auf einem kollaborativen Filteralgorithmus und wird mit Protokollen des Zugriffs auf Windows-Dateifreigaben trainiert (Sicherheitsereignisse mit Ereignis-ID 5140). Die Schlüsselinformationen, die Sie für dieses Modell im Protokoll benötigen, ist die Kopplung von Benutzern mit Ressourcen, auf die zugegriffen wird. 

## <a name="example-walkthrough-anomalous-file-share-access-detection"></a>Exemplarische Vorgehensweise: Erkennung anomaler Zugriffe auf Dateifreigaben 

Nachdem Sie nun mit den wichtigsten Komponenten der BYO-ML-Plattform vertraut sind, finden Sie hier ein Beispiel, das zeigt, wie Sie die Plattform und die Komponenten verwenden, um eine angepasste ML-Erkennung bereitzustellen.

### <a name="setup-the-databricksspark-environment"></a>Einrichten der Databricks/Spark-Umgebung

Sie müssen Ihre eigene Databricks-Umgebung einrichten, wenn Sie noch keine haben. Anweisungen hierzu finden Sie in dem Dokument [Databrick-Schnellstart](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal?tabs=azure-portal).

### <a name="auto-export-instruction"></a>Anweisung zum automatischen Export

Zum Erstellen von benutzerdefinierten ML-Modellen auf Grundlage Ihrer eigenen Daten in Sentinel müssen Sie Ihre Daten aus Log Analytics in eine Blob Storage- oder Event Hub-Ressource exportieren, damit das ML-Modell aus Databricks darauf zugreifen kann. Erfahren Sie, wie Sie [Daten in Azure Sentinel erfassen](connect-data-sources.md).

In diesem Beispiel müssen sich Ihre Trainingsdaten für das Dateifreigabe-Zugriffsprotokoll im Azure Blob-Speicher befinden. Das Format der Daten ist im Notebook und in den Bibliotheken dokumentiert.

Sie können Ihre Daten mithilfe der [Azure-Befehlszeilenschnittstelle (CLI)](/cli/azure/monitor/log-analytics) automatisch aus Log Analytics exportieren. 

Ihnen muss die Rolle **Mitwirkender** in Ihrem Log Analytics-Arbeitsbereich, Ihrem Speicherkonto und Ihrer EventHub-Ressource zugewiesen sein, um die Befehle ausführen zu können. 

Im Folgenden sehen Sie ein Beispielset von Befehlen zum Einrichten des automatischen Exports:

```azurecli

az –version

# Login with Azure CLI
 az login

# List all Log Analytics clusters
 az monitor log-analytics cluster list

# Set to specific subscription
 az account set --subscription "SUBSCRIPTION_NAME"
 
# Export to Storage - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIStr --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent
 
# Export to EventHub - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIEH --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent Heartbeat"]

# List export settings
az monitor log-analytics workspace data-export list --resource-group "RG_NAME" --workspace-name "WS_NAME"

# Delete export setting
 az monitor log-analytics workspace data-export delete --resource-group "RG_NAME" --workspace-name "WS_NAME" --name "NAME"
```

### <a name="export-custom-data"></a>Exportieren benutzerdefinierter Daten

Für benutzerdefinierte Daten, die vom automatischen Export von Log Analytics nicht unterstützt werden, können Sie Logic Apps oder andere Lösungen verwenden, um Ihre Daten zu verschieben. Weitere Informationen finden Sie im Blog und Skript zu [Exportieren von Log Analytics-Daten in Blob-Speicher](https://www.borninthecloud.com/exporting-log-analytics-data-to-blob-store/?preview=true).

### <a name="correlate-with-data-outside-of-azure-sentinel"></a>Korrelieren mit Daten außerhalb von Azure Sentinel

Sie können auch Daten von außerhalb von Azure Sentinel in den Blob-Speicher oder Event Hub übernehmen und sie mit den Sentinel-Daten korrelieren, um Ihre ML-Modelle zu erstellen. 
 
### <a name="copy-and-install-the-related-packages"></a>Kopieren und Installieren der relevanten Pakete

Kopieren Sie das BYO-ML-Paket aus dem oben erwähnten GitHub-Repository für Azure Sentinel in Ihre Databricks-Umgebung. Öffnen Sie dann die Notebooks, und befolgen Sie die Anweisungen im jeweiligen Notebook, um die erforderlichen Bibliotheken in Ihren Clustern zu installieren.

### <a name="model-training-and-scoring"></a>Training und Bewertung von Modellen

Befolgen Sie die Anweisungen in den beiden Notebooks, um die Konfigurationen gemäß Ihrer Umgebung und Ihrer Ressourcen zu ändern, führen Sie die Schritte zum Trainieren und Erstellen Ihres Modells aus, und planen Sie dann das Modell, um eingehende Dateifreigabe-Zugriffsprotokolle zu bewerten.

### <a name="write-results-to-log-analytics"></a>Schreiben von Ergebnissen in Log Analytics

Nachdem Sie die Bewertung geplant haben, können Sie das Modul im Bewertungsnotebook verwenden, um die Bewertungsergebnisse in den Log Analytics Arbeitsbereich zu schreiben, der Ihrer Azure Sentinel-Instanz zugeordnet ist.

### <a name="check-results-in-azure-sentinel"></a>Überprüfen von Ergebnissen in Azure Sentinel

Um sich Ihre bewerteten Ergebnisse zusammen mit den zugehörigen Protokolldetails anzusehen, wechseln Sie zurück zu Ihrem Azure Sentinel-Portal. In „**Protokolle** > Benutzerdefinierte Protokolle“ werden die Ergebnisse in der Tabelle **AnomalousResourceAccessResult_CL** (oder dem Namen Ihrer eigenen benutzerdefinierten Tabelle) angezeigt. Sie können diese Ergebnisse verwenden, um Ihre Untersuchungs- und Sucherfahrungen zu verbessern.

:::image type="content" source="./media/bring-your-own-ml/anomalous-resource-access-logs.png" alt-text="Protokolle über anomale Ressourcenzugriffe":::

### <a name="build-custom-analytics-rule-with-ml-results"></a>Erstellen benutzerdefinierter Analyseregeln mit ML-Ergebnissen

Nachdem Sie bestätigt haben, dass sich die ML-Ergebnisse in der Tabelle „Benutzerdefinierte Protokolle“ befinden und Sie mit der Genauigkeit der Bewertungen zufrieden sind, können Sie auf Grundlage der Ergebnisse eine Erkennung erstellen. Wechseln Sie im Azure Sentinel-Portal zu **Analysen**, und [erstellen Sie eine neue Erkennungsregel](tutorial-detect-threats-custom.md). Im Folgenden finden Sie ein Beispiel für die Abfrage, die zum Erstellen der Erkennung verwendet wird.

:::image type="content" source="./media/bring-your-own-ml/create-byo-ml-analytics-rule.png" alt-text="Erstellen einer benutzerdefinierten Analyseregel für B Y O M L-Erkennungen":::

### <a name="view-and-respond-to-incidents"></a>Anzeigen von und Reagieren auf Vorfälle
Nachdem Sie die Analyseregel auf Grundlage der ML-Ergebnisse eingerichtet haben, und sobald Ergebnisse über dem in der Abfrage festgelegten Schwellenwert liegen, wird ein Vorfall generiert und auf der Seite **Vorfälle** in Azure Sentinel angezeigt. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie die BYO-ML-Plattform von Azure Sentinel zum Erstellen oder Importieren Ihrer eigenen Machine Learning-Algorithmen verwenden, um Daten zu analysieren und Bedrohungen zu erkennen.

- Weitere Informationen finden Sie in Beiträgen zu Machine Learning und vielen anderen relevanten Themen im [Azure Sentinel-Blog](https://aka.ms/azuresentinelblog).