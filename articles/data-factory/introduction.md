---
title: Einführung in Azure Data Factory
description: Hier finden Sie Informationen zu Azure Data Factory – ein cloudbasierter Datenintegrationsdienst, der das Verschieben und Transformieren von Daten orchestriert und automatisiert.
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: overview
ms.date: 09/30/2019
ms.openlocfilehash: 8fef4bbc0c0e77c1e152b8c2c44254e091b0e5a3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392543"
---
# <a name="what-is-azure-data-factory"></a>Was ist Azure Data Factory?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In Zeiten von Big Data werden unorganisierte Rohdaten häufig in relationalen, nicht relationalen und anderen Speichersystemen gespeichert. Für sich genommen mangelt es den Rohdaten allerdings an Kontext bzw. an der nötigen Aussagekraft, um von Analysten, Datenspezialisten oder Entscheidungsträgern in Unternehmen sinnvoll genutzt werden zu können. 

Für Big Data ist ein Dienst zur Orchestrierung und Operationalisierung von Prozessen erforderlich, mit dem diese enormen Rohdatenmengen in verwertbare geschäftliche Erkenntnisse verwandelt werden. Azure Data Factory ist ein spezieller verwalteter Clouddienst für diese komplexen Hybridprojekte mit ETL (Extrahieren, Transformieren und Laden), ELT (Extrahieren, Laden und Transformieren) und Datenintegration.

Beispiel: Angenommen, bei einem Spieleunternehmen fallen durch Spiele in der Cloud mehrere Petabyte an Spieleprotokollen an. Das Unternehmen möchte diese Protokolle analysieren, um Erkenntnisse zu Vorlieben von Kunden, zu demografischen Daten und zum Nutzungsverhalten zu erhalten. Außerdem möchte es Upselling- und Cross-Selling-Möglichkeiten ermitteln, mitreißende neue Features entwickeln, das Unternehmenswachstum vorantreiben und die Benutzerfreundlichkeit für Kunden erhöhen.

Zum Analysieren dieser Protokolle benötigt das Unternehmen Referenzdaten (beispielsweise Informationen zu den Kunden, zum Spiel und zur Marketingkampagne), die in einem lokalen Datenspeicher gespeichert sind. Das Unternehmen möchte diese Daten aus dem lokalen Datenspeicher zusammen mit zusätzlichen Protokolldaten aus einem Clouddatenspeicher nutzen. 

Zur Auswertung möchte das Unternehmen die gesamten Daten mithilfe eines Spark-Clusters in der Cloud (Azure HDInsight) verarbeiten und die transformierten Dateien in einem cloudbasierten Data Warehouse wie Azure Synapse Analytics veröffentlichen, um basierend darauf komfortabel einen Bericht erstellen zu können. Dieser Workflow soll automatisiert und nach einem täglichen Zeitplan überwacht und verwaltet werden. Darüber hinaus soll der Workflow ausgeführt werden, wenn Dateien in einem Blobspeichercontainer eintreffen.

Die Azure Data Factory-Plattform ist die richtige Lösung für solche Datenszenarien. Sie ist der *cloudbasierte ETL- und Datenintegrationsdienst, mit dem Sie datengesteuerte Workflows erstellen können, um Datenverschiebungen und -transformationen bedarfsgesteuert zu orchestrieren*. Mit Azure Data Factory können Sie datengesteuerte Workflows (so genannte Pipelines) erstellen und planen, die Daten aus unterschiedlichen Datenspeichern erfassen. Sie können komplexe ETL-Prozesse erstellen, bei denen Daten visuell mit Datenflüssen oder mit Computediensten wie Azure HDInsight Hadoop, Azure Databricks und Azure SQL-Datenbank transformiert werden. 

Außerdem können Sie Ihre transformierten Daten in Datenspeichern, z. B. Azure Synapse Analytics, veröffentlichen, damit sie von BI-Anwendungen (Business Intelligence) genutzt werden können. Über Azure Data Factory können Rohdaten letztendlich in aussagekräftigen Datenspeichern und Data Lakes organisiert und für bessere geschäftliche Entscheidungen genutzt werden.

![Übersicht über Data Factory](media/data-flow/overview.png)

## <a name="how-does-it-work"></a>Wie funktioniert dies?

Data Factory enthält eine Reihe von verbundenen Systemen, die eine umfassende End-to-End-Plattform für Datentechniker bilden.

### <a name="connect-and-collect"></a>Herstellen einer Verbindung und Sammeln von Daten

In Unternehmen sind verschiedene Arten von Daten in unterschiedlichen Quellen gespeichert (lokal, in der Cloud, strukturiert, unstrukturiert und teilweise strukturiert), und alle gehen in unterschiedlichen Intervallen und mit unterschiedlicher Geschwindigkeit ein. 

Der erste Schritt bei der Erstellung eines Informationssystems für die Produktion umfasst das Herstellen der Verbindung mit allen erforderlichen Daten- und Verarbeitungsquellen, z.B. SaaS-Dienste (Software-as-a-Service), Datenbanken, Dateifreigaben und FTP-Webdienste. Der nächste Schritt umfasst das Verschieben der Daten an einen zentralen Ort zur weiteren Verarbeitung.

Ohne Data Factory müssen Unternehmen benutzerdefinierte Komponenten für die Datenverschiebung erstellen oder benutzerdefinierte Dienste schreiben, um diese Datenquellen und die Verarbeitung zu integrieren. Das Integrieren und Verwalten dieser Systeme ist teuer und aufwändig. Häufig fehlen für Unternehmen geeignete Überwachungs- und Warnfunktionen sowie die Steuerungsmöglichkeiten eines vollständig verwalteten Diensts.

Mit Data Factory können Sie die [Kopieraktivität](copy-activity-overview.md) in einer Datenpipeline nutzen, um Daten sowohl aus lokalen als auch aus cloudbasierten Quelldatenspeichern zur weiteren Analyse in einen zentralen Datenspeicher in der Cloud zu verschieben. Beispielsweise können Sie Daten in Azure Data Lake Storage sammeln und später transformieren, indem Sie einen Azure Data Lake Analytics-Computedienst verwenden. Außerdem können Sie Daten in Azure Blob Storage sammeln und später per Azure HDInsight Hadoop-Cluster transformieren.

### <a name="transform-and-enrich"></a>Transformieren und Erweitern
Wenn Daten in einem zentralisierten Datenspeicher in der Cloud vorliegen, können Sie die gesammelten Daten mit ADF-Zuordnungsdatenflüssen verarbeiten oder transformieren. Mit Datenflüssen können Datentechniker Graphen für die Datentransformation erstellen und verwalten, die unter Spark ausgeführt werden, ohne mit Spark-Clustern oder der Spark-Programmierung vertraut sein zu müssen.

Falls Sie das manuelle Codieren von Transformationen vorziehen: ADF unterstützt externe Aktivitäten zur Ausführung Ihrer Transformationen mit Computediensten, z. B. HDInsight Hadoop, Spark, Data Lake Analytics und Machine Learning.

### <a name="cicd-and-publish"></a>CI/CD und Veröffentlichung
[Data Factory verfügt über vollständige Unterstützung von CI/CD](continuous-integration-deployment.md) für Ihre Datenpipelines per Azure DevOps und GitHub. Dies ermöglicht Ihnen das inkrementelle Entwickeln und Bereitstellen Ihrer ETL-Prozesse vor der Veröffentlichung des fertigen Produkts. Nachdem die Rohdaten in einem für Unternehmen nutzbaren Format vorliegen, laden Sie sie mit Azure Data Warehouse, Azure SQL-Datenbank, Azure CosmosDB oder einer anderen Analyse-Engine, auf die Ihre Benutzer in ihren Business Intelligence-Tools verweisen können.
### <a name="monitor"></a>Überwachen
Nachdem Sie Ihre Pipeline für die Datenintegration erfolgreich erstellt und bereitgestellt haben und einen geschäftlichen Nutzen aus den optimierten Daten ziehen können, können Sie die geplanten Aktivitäten und Pipelines auf Erfolgs- und Fehlerraten überwachen. Azure Data Factory bietet integrierte Unterstützung für die Pipelineüberwachung per Azure Monitor, API, PowerShell, Azure Monitor-Protokolle und Integritätsbereiche im Azure-Portal.

## <a name="top-level-concepts"></a>Allgemeine Konzepte
Ein Azure-Abonnement kann über mindestens eine Azure Data Factory-Instanz (bzw. Data Factory) verfügen. Azure Data Factory besteht aus den folgenden Hauptkomponenten:
- Pipelines
- Aktivitäten
- Datasets
- Verknüpfte Dienste
- Datenflüsse
- Integration Runtimes

Zusammen stellen sie die Plattform dar, auf der Sie datengesteuerte Workflows mit Schritten zum Verschieben und Transformieren von Daten zusammenstellen können.

### <a name="pipeline"></a>Pipeline
Eine Data Factory kann eine oder mehrere Pipelines aufweisen. Bei einer Pipeline handelt es sich um eine logische Gruppierung von Aktivitäten zur Durchführung einer Arbeitseinheit. Gemeinsam führen die Aktivitäten einer Pipeline eine Aufgabe durch. Eine Pipeline kann beispielsweise eine Gruppe mit Aktivitäten enthalten, die Daten aus einem Azure-Blob erfasst, und anschließend eine Hive-Abfrage in einem HDInsight-Cluster ausführen, um die Daten zu partitionieren. 

Der Vorteil hierbei ist, dass die Pipeline Ihnen das Verwalten der Aktivitäten als Gruppe ermöglicht – anstatt für jede Aktivität einzeln. Die Aktivitäten in einer Pipeline können miteinander verkettet und sequenziell durchgeführt oder unabhängig voneinander parallel durchgeführt werden.

### <a name="mapping-data-flows"></a>Zuordnen von Datenflüssen
Erstellen und verwalten Sie Graphen mit Logik für die Datentransformation, die Sie nutzen können, um Daten beliebiger Größe zu transformieren. Sie können eine wiederverwendbare Bibliothek mit Routinen für die Datentransformation erstellen und diese Prozesse mit horizontaler Skalierung über Ihre ADF-Pipelines ausführen. Data Factory führt Ihre Logik in einem Spark-Cluster aus, der je nach Bedarf hoch- bzw. heruntergefahren wird. Für diesen Cluster fällt für Sie kein Verwaltungs- oder Wartungsaufwand an.

### <a name="activity"></a>Aktivität
Aktivitäten stellen einen Verarbeitungsschritt in einer Pipeline dar. Beispielsweise können Sie eine Kopieraktivität verwenden, um Daten zwischen zwei Datenspeichern zu kopieren. Analog hierzu können Sie eine Hive-Aktivität verwenden, die eine Hive-Abfrage für einen Azure HDInsight-Cluster ausführt, um Ihre Daten zu transformieren oder zu analysieren. Data Factory unterstützt drei Arten von Aktivitäten: Datenverschiebungsaktivitäten, Datentransformationsaktivitäten und Steuerungsaktivitäten.

### <a name="datasets"></a>Datasets
Datasets stellen Datenstrukturen in den Datenspeichern dar, die einfach auf die Daten zeigen bzw. verweisen, die Sie in Ihren Aktivitäten als Eingaben oder Ausgaben verwenden möchten. 

### <a name="linked-services"></a>Verknüpfte Dienste
Verknüpfte Dienste ähneln Verbindungszeichenfolgen, mit denen die Verbindungsinformationen definiert werden, die für Data Factory zum Herstellen einer Verbindung mit externen Ressourcen erforderlich sind. Sie können sich dies wie folgt vorstellen: Ein verknüpfter Dienst definiert die Verbindung mit der Datenquelle, und ein Dataset stellt die Struktur der Daten dar. So gibt etwa ein verknüpfter Azure Storage-Dienst eine Verbindungszeichenfolge an, um eine Verbindung mit einem Azure Storage-Konto herzustellen. Außerdem gibt ein Azure-Blobdataset den Blobcontainer und den Ordner an, der die Daten enthält.

(Beispiele: Azure Storage, lokale SQL Server-Instanz, Azure HDInsight) Verknüpfte Dienste haben in Data Factory zwei Zwecke:

- Sie stellen einen **Datenspeicher** dar, der beispielsweise eine SQL Server-Datenbank, eine Oracle-Datenbank, eine Dateifreigabe oder ein Azure Blob Storage-Konto enthalten kann (aber nicht darauf beschränkt ist). Eine Liste mit unterstützten Datenspeichern finden Sie im [Artikel zur Kopieraktivität](copy-activity-overview.md).

- Sie stellen eine **Computeressource** dar, die die Ausführung einer Aktivität hosten kann. So wird beispielsweise die HDInsightHive-Aktivität in einem HDInsight-Hadoop-Cluster ausgeführt. Eine Liste mit Transformationsaktivitäten und unterstützten Compute-Umgebungen finden Sie im [Artikel zur Datentransformation](transform-data.md).

### <a name="triggers"></a>Trigger
Trigger stellen die Verarbeitungseinheit dar, die bestimmt, wann eine Pipelineausführung initiiert werden soll. Es gibt verschiedene Arten von Triggern für unterschiedliche Arten von Ereignissen.

### <a name="pipeline-runs"></a>Pipelineausführungen
Eine Pipelineausführung ist eine Instanz der ausgeführten Pipeline. Zur Instanziierung von Pipelineausführungen werden in der Regel die Argumente an die in Pipelines definierten Parameter übergeben. Die Argumente können manuell oder im Rahmen der Triggerdefinition übergeben werden.

### <a name="parameters"></a>Parameter
Parameter sind Schlüssel-Wert-Paare einer schreibgeschützten Konfiguration.  Parameter werden in der Pipeline definiert. Die Argumente für die definierten Parameter werden im Rahmen der Ausführung aus dem Ausführungskontext eines Triggers oder einer manuell ausgeführten Pipeline übergeben. Die Parameterwerte werden von Aktivitäten in der Pipeline genutzt.

Ein Dataset ist ein stark typisierter Parameter und eine wiederverwendbare bzw. referenzierbare Entität. Eine Aktivität kann auf Datasets verweisen und die Eigenschaften nutzen, die in der Datasetdefinition festgelegt sind.

Bei einem verknüpften Dienst handelt es sich ebenfalls um einen stark typisierten Parameter mit Verbindungsinformationen für einen Datenspeicher oder eine Compute-Umgebung. Er ist auch eine wiederverwendbare/referenzierbare Entität.

### <a name="control-flow"></a>Ablaufsteuerung
Die Ablaufsteuerung ist eine Orchestrierung von Pipelineaktivitäten und umfasst die Verkettung von Aktivitäten in einer Sequenz, Verzweigungen, Definition von Parametern auf Pipelineebene sowie Übergabe von Argumenten, während die Pipeline bedarfs- oder triggergesteuert aufgerufen wird. Darüber hinaus umfasst sie das Übergeben von benutzerdefinierten Zuständen und Schleifencontainern (ForEach-Iteratoren).

### <a name="variables"></a>Variables
Variablen können in Pipelines verwendet werden, um temporäre Werte zu speichern. Sie können auch zusammen mit Parametern eingesetzt werden, um die Übergabe von Werten zwischen Pipelines, Datenflüssen und anderen Aktivitäten zu ermöglichen.

## <a name="next-steps"></a>Nächste Schritte
Hier sind die Dokumente angegeben, die die Informationen zu den wichtigen nächsten Schritten enthalten:

- [Datasets und verknüpfte Dienste](concepts-datasets-linked-services.md)
- [Pipelines und Aktivitäten](concepts-pipelines-activities.md)
- [Integrationslaufzeit](concepts-integration-runtime.md)
- [Was sind Zuordnungsdatenflüsse?](concepts-data-flow-overview.md)
- [Data Factory-Benutzeroberfläche im Azure-Portal](quickstart-create-data-factory-portal.md)
- [Tool zum Kopieren von Daten im Azure-Portal](quickstart-create-data-factory-copy-data-tool.md)
- [PowerShell](quickstart-create-data-factory-powershell.md)
- [.NET](quickstart-create-data-factory-dot-net.md)
- [Python](quickstart-create-data-factory-python.md)
- [REST](quickstart-create-data-factory-rest-api.md)
- [Azure Resource Manager-Vorlage](quickstart-create-data-factory-resource-manager-template.md)
 
