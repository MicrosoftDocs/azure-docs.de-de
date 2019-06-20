---
title: 'Azure Data Factory: Häufig gestellte Fragen | Microsoft-Dokumentation'
description: Hier finden Sie Antworten auf häufig gestellte Fragen zu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: shlo
ms.openlocfilehash: d704c32ee7417c6460ad6cc880e451adddfa61de
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61345753"
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory: Häufig gestellte Fragen
Dieser Artikel enthält Antworten auf häufig gestellte Fragen zu Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>Was ist Azure Data Factory? 
Data Factory ist ein vollständig verwalteter, cloudbasierter Datenintegrationsdienst, der das Verschieben und Transformieren von Daten automatisiert. Genau wie ein Betrieb, in dem Anlagen Rohmaterialien in Endprodukte umwandeln, organisiert Azure Data Factory vorhandene Dienste so, dass Rohdaten gesammelt und in nutzbare Informationen transformiert werden. 

Sie können mithilfe von Azure Data Factory datengesteuerte Workflows zum Verschieben von Daten zwischen lokalen Datenspeichern und Clouddatenspeichern erstellen. Und Sie können Daten mit Computediensten wie Azure HDInsight, Azure Data Lake Analytics und SQL Server Integration Services (SSIS) Integration Runtime verarbeiten und transformieren. 

Mit Data Factory können Sie die Datenverarbeitung in einem Azure-basierten Clouddienst oder in Ihrer eigenen selbstgehosteten Compute-Umgebung wie SSIS, SQL Server oder Oracle ausführen. Nachdem Sie eine Pipeline erstellt haben, die die gewünschte Aktion ausführt, können Sie die regelmäßige Ausführung der Pipeline planen (beispielsweise stündlich, täglich oder wöchentlich), einen Plan mit Zeitfenstern aufstellen oder die Pipeline über ein Ereignis auslösen. Weitere Informationen finden Sie unter [Einführung in Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Ablaufsteuerungen und Skalierung 
Zur Unterstützung der vielfältigen Integrationsabläufe und -muster eines modernen Data Warehouse ermöglicht Data Factory eine flexible Modellierung der Datenpipeline. Dies beinhaltet umfassende Paradigmen für die Programmierung der Ablaufsteuerung. Hierzu zählen etwa bedingte Ausführung, Verzweigung in Datenpipelines und die Möglichkeit zum expliziten Übergeben von Parametern für diese Abläufe (intern und übergreifend). Außerdem umfasst die Ablaufsteuerung die Transformation von Daten per Aktivitätsverteilung auf externe Ausführungsmodule und Datenflussfunktionen (beispielsweise bedarfsabhängige Datenverschiebungen) über die Kopieraktivität.

Mit Data Factory können Sie nun flexibel einen beliebigen Ablaufstil modellieren, der für die Datenintegration benötigt wird und entweder bedarfsabhängig oder nach einem bestimmten Zeitplan angewendet werden soll. Einige allgemeine Abläufe, die mit diesem Modell ermöglicht werden, sind:   

- Ablaufsteuerungen:
    - Aktivitäten können innerhalb einer Pipeline zu einer Sequenz verkettet werden.
    - Aktivitäten können innerhalb einer Pipeline verzweigt werden.
    - Parameter:
        - Parameter können auf der Pipelineebene definiert und Argumente beim bedarfs- oder triggergesteuerten Aufrufen der Pipeline übergeben werden.
        - Aktivitäten können die an die Pipeline übergebenen Argumente nutzen.
    - Übergeben von benutzerdefinierten Zuständen:
        - Aktivitätsausgaben (einschließlich des Zustands) können von einer Folgeaktivität in der Pipeline genutzt werden.
    - Schleifencontainer:
        - Die ForEach-Aktivität durchläuft eine bestimmte Sammlung von Aktivitäten in einer Schleife. 
- Triggerbasierte Abläufe:
    - Pipelines können nach Bedarf oder zeitgesteuert ausgelöst werden.
- Deltaabläufe:
    - Sie können mithilfe von Parametern die Obergrenze für Deltakopien beim Verschieben von Dimensions- oder Referenztabellen aus einem relationalen Speicher (entweder lokal oder in der Cloud) definieren, um die Daten in den Lake zu laden. 

Weitere Informationen finden Sie unter [Tutorial: Ablaufsteuerungen](tutorial-control-flow.md).

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Nach Bedarf transformierte Daten mit Pipelines ohne Code
Die neue browserbasierte Toolumgebung ermöglicht die Erstellung und Bereitstellung von Pipelines ohne Code mit einer modernen, interaktiven webbasierten Benutzeroberfläche.

Für Entwickler visueller Daten und Datentechniker ist die Data Factory-Webbenutzeroberfläche die codefreie Entwurfsumgebung für die Pipelineerstellung. Sie ist vollständig in Visual Studio Online Git integriert und ermöglicht die Integration für CI/CD sowie die iterative Entwicklung mit Debugoptionen.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Umfassende plattformübergreifende SDKs für erfahrene Benutzer
Data Factory V2 bietet verschiedenste SDKs für die Erstellung, Verwaltung und Überwachung von Pipelines in Ihrer bevorzugten IDE:
* Python SDK
* PowerShell CLI
* C# SDK

Benutzer können außerdem die dokumentierten REST-APIs verwenden, um mit Data Factory V2 zu interagieren.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Iteratives Entwickeln und Debuggen mit visuellen Tools
Die visuellen Tools von Azure Data Factory ermöglichen iteratives Entwickeln und Debuggen. Mit der Funktion **Debuggen** auf der Pipelinecanvas können Sie Pipelines erstellen und Testläufe durchführen, ohne eine einzige Codezeile zu schreiben. Die Ergebnisse der Testläufe können auf der Pipelinecanvas im Fenster **Ausgabe** angezeigt werden. Nach erfolgreicher Durchführung des Testlaufs können Sie der Pipeline weitere Aktivitäten hinzufügen und das iterative Debuggen fortsetzen. Bereits gestartete Testläufe können abgebrochen werden. 

Sie müssen Ihre Änderungen nicht für den Data Factory-Dienst veröffentlichen, bevor Sie **Debuggen** auswählen. Dies ist hilfreich, wenn Sie sich vergewissern möchten, dass die neuen Elemente oder Änderungen wie erwartet funktionieren, bevor Sie Ihre Data Factory-Workflows in Entwicklungs-, Test- oder Produktionsumgebungen aktualisieren. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Möglichkeit zum Bereitstellen von SSIS-Paketen in Azure 
Wenn Sie Ihre SSIS-Workloads verschieben möchten, können Sie eine Data Factory-Instanz erstellen und eine Azure-SSIS-Integration Runtime bereitstellen. Eine Azure-SSIS Integration Runtime ist ein vollständig verwalteter Cluster mit virtuellen Azure-Computern (Knoten), die speziell für die Ausführung von SSIS-Paketen in der Cloud bestimmt sind. Eine ausführliche Anleitung finden Sie im Tutorial [Bereitstellen von SSIS-Paketen in Azure](tutorial-create-azure-ssis-runtime-portal.md). 
 
### <a name="sdks"></a>SDKs
Für erfahrene Benutzer, die auf der Suche nach einer befehlsorientierten Benutzerschnittstelle sind, bietet Data Factory ein breites Spektrum an SDKs zum Erstellen, Verwalten und Überwachen von Pipelines mit Ihrer bevorzugten IDE. Die Sprachunterstützung umfasst .NET, PowerShell, Python und REST.

### <a name="monitoring"></a>Überwachung
Sie können Ihre Data Factorys per PowerShell, SDK oder mit den visuellen Überwachungstools auf der Benutzeroberfläche des Browsers überwachen. Sie können bedarfsabhängige, triggerbasierte und zeitgesteuerte benutzerdefinierte Datenflüsse auf effiziente und effektive Weise überwachen und verwalten. Brechen Sie vorhandene Aufgaben ab, nutzen Sie die Fehlerübersicht, zeigen Sie Detailinformationen an, um ausführliche Fehlermeldungen zu erhalten, und debuggen Sie alle Probleme an einem zentralen Ort, ohne den Kontext wechseln oder zwischen Bildschirmen navigieren zu müssen. 

### <a name="new-features-for-ssis-in-data-factory"></a>Neue Features für SSIS in Data Factory
Seit der ersten öffentlichen Vorschauversion im Jahr 2017 wurden mit Data Factory folgende Features für SSIS hinzugefügt:

-   Unterstützung von drei weiteren Konfigurationen/Varianten von Azure SQL-Datenbank zum Hosten der SSIS-Datenbank (SSISDB) mit Projekten/Paketen:
-   SQL-Datenbank mit VNET-Dienstendpunkten
-   Verwaltete Instanz
-   Pool für elastische Datenbanken
-   Unterstützung eines virtuellen Azure Resource Manager-Netzwerks auf der Grundlage eines klassischen virtuellen Netzwerks, das irgendwann als veraltet eingestuft wird. Dadurch können Sie Ihre Azure-SSIS Integration Runtime in ein virtuelles Netzwerk integrieren, das für SQL-Datenbank mit VNET-Dienstendpunkten, einer verwalteten Instanz und lokalem Datenzugriff konfiguriert ist. Weitere Informationen finden Sie unter [Verknüpfen einer Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk](join-azure-ssis-integration-runtime-virtual-network.md).
-   Unterstützung von Azure AD-Authentifizierung (Azure Active Directory) und SQL-Authentifizierung für die Verbindungsherstellung mit der SSISDB, um die Azure AD-Authentifizierung mit Ihrer von Data Factory verwalteten Identität für Azure-Ressourcen zu ermöglichen
-   Unterstützung der Verwendung Ihrer eigenen lokalen SQL Server-Lizenz zur Erzielung erheblicher Kosteneinsparungen über den Azure-Hybridvorteil
-   Unterstützung der Enterprise Edition der Azure-SSIS Integration Runtime mit erweiterten Features, Premium-Features, einer Schnittstelle für das benutzerdefinierte Setup, um zusätzliche Komponenten/Erweiterungen zu installieren, sowie mit einem Partnerökosystem. Weitere Informationen finden Sie unter [Enterprise Edition, benutzerdefiniertes Setup und Erweiterbarkeit durch Drittanbieter für SSIS in ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-   Tiefere Integration von SSIS in Data Factory, die Ihnen das Aufrufen bzw. Auslösen erstklassiger Aktivitäten zum Ausführen des SSIS-Pakets in Data Factory-Pipelines und die Planung per SSMS ermöglicht. Weitere Informationen finden Sie unter [Modernisieren und Erweitern von ETL/ELT-Workflows mit SSIS-Aktivitäten in ADF-Pipelines](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/).


## <a name="what-is-the-integration-runtime"></a>Was ist die Integration Runtime?
Die Integration Runtime ist die Computeinfrastruktur, mit der Azure Data Factory die folgenden Datenintegrationsfunktionen für verschiedene Netzwerkumgebungen bereitstellt:

- **Datenverschiebung:** Zur Datenverschiebung werden die Daten von der Integration Runtime zwischen dem Quell- und Zieldatenspeicher verschoben, während gleichzeitig Unterstützung für integrierte Connectors, Formatkonvertierungen, Spaltenzuordnungen und leistungsfähige sowie skalierbare Datenübertragungen bereitgestellt wird.
- **Verteilungsaktivitäten:** Für die Transformation bietet die Integration Runtime Funktionen zur nativen Ausführung von SSIS-Paketen.
- **SSIS-Pakete ausführen:** Die Integration Runtime führt SSIS-Pakete nativ in einer verwalteten Azure-Computeumgebung aus. Zudem unterstützt die Integration Runtime die Übermittlung und Überwachung von Transformationsaktivitäten, die in verschiedensten Computediensten wie Azure HDInsight, Azure Machine Learning, SQL-Datenbank und SQL Server ausgeführt werden.

Sie können zum Verschieben und Transformieren von Daten je nach Bedarf eine oder mehrere Integration Runtime-Instanzen bereitstellen. Die Integration Runtime kann in einem öffentlichen Azure-Netzwerk oder in einem privaten Netzwerk (lokal, Azure Virtual Network oder Virtual Private Cloud (VPC) von Amazon Web Services) ausgeführt werden. 

Weitere Informationen finden Sie unter [Integrationslaufzeit in Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Welcher Grenzwert besteht hinsichtlich der Anzahl von Integrationslaufzeiten?
Es gibt keine festen Grenzwerte hinsichtlich der Anzahl von Integration Runtime-Instanzen in einer Data Factory. Es gibt jedoch einen Grenzwert hinsichtlich der Anzahl von VM-Kernen, die von Integration Runtime pro Abonnement für die Ausführung von SSIS-Paketen verwendet werden können. Weitere Informationen finden Sie unter [Data Factory-Grenzwerte](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Wie lauten die Hauptkonzepte von Azure Data Factory?
Ein Azure-Abonnement kann über mindestens eine Azure Data Factory-Instanz (oder Data Factory) verfügen. Azure Data Factory enthält vier Hauptkomponenten, die zusammen als Plattform fungieren, auf der Sie datengesteuerte Workflows mit Schritten zum Verschieben und Transformieren von Daten zusammenstellen können.

### <a name="pipelines"></a>Pipelines
Eine Data Factory kann eine oder mehrere Pipelines haben. Bei einer Pipeline handelt es sich um eine logische Gruppierung von Aktivitäten zur Durchführung einer Arbeitseinheit. Gemeinsam führen die Aktivitäten einer Pipeline eine Aufgabe durch. Eine Pipeline kann beispielsweise eine Gruppe von Aktivitäten enthalten, die Daten aus einem Azure-Blob erfasst, und anschließend eine Hive-Abfrage in einem HDInsight-Cluster ausführen, um die Daten zu partitionieren. Der Vorteil ist, dass Sie eine Pipeline zum Verwalten der Aktivitäten als Gruppe verwenden können, statt jede Aktivität einzeln zu verwalten. Sie können die Aktivitäten in einer Pipeline miteinander verketten, um sie sequenziell auszuführen. Sie können sie aber auch unabhängig voneinander parallel ausführen.

### <a name="activities"></a>activities
Aktivitäten stellen einen Verarbeitungsschritt in einer Pipeline dar. Beispielsweise können Sie eine Kopieraktivität verwenden, um Daten zwischen zwei Datenspeichern zu kopieren. Analog dazu können Sie eine Hive-Aktivität verwenden, die eine Hive-Abfrage für einen Azure HDInsight-Cluster ausführt, um Ihre Daten zu transformieren oder zu analysieren. Data Factory unterstützt drei Arten von Aktivitäten: Datenverschiebungsaktivitäten, Datentransformationsaktivitäten und Steuerungsaktivitäten.

### <a name="datasets"></a>Datasets
Datasets stellen Datenstrukturen in den Datenspeichern dar, die einfach auf die Daten zeigen bzw. verweisen, die Sie in Ihren Aktivitäten als Eingaben oder Ausgaben verwenden möchten. 

### <a name="linked-services"></a>Verknüpfte Dienste
Verknüpfte Dienste ähneln Verbindungszeichenfolgen, mit denen die Verbindungsinformationen definiert werden, die für Data Factory zum Herstellen einer Verbindung mit externen Ressourcen erforderlich sind. Das können Sie sich wie folgt vorstellen: Ein verknüpfter Dienst definiert die Verbindung mit der Datenquelle, und ein Dataset stellt die Struktur der Daten dar. So gibt etwa ein verknüpfter Azure Storage-Dienst die Verbindungszeichenfolge an, um eine Verbindung mit einem Azure Storage-Konto herzustellen. Und ein Azure-Blobdataset gibt den Blobcontainer und den Ordner an, der die Daten enthält.

Verknüpfte Dienste haben in Data Factory zwei Funktionen:

- Sie stellen einen *Datenspeicher* dar, der beispielsweise eine lokale SQL Server-Instanz, eine Oracle-Datenbankinstanz, eine Dateifreigabe oder ein Azure Blob Storage-Konto enthalten kann (er ist aber nicht darauf beschränkt). Eine Liste der unterstützten Datenspeicher finden Sie unter [Kopieraktivität in Azure Data Factory](copy-activity-overview.md).
- Sie stellen eine *Computeressource* dar, die die Ausführung einer Aktivität hosten kann. So wird beispielsweise die HDInsight-Hive-Aktivität in einem HDInsight-Hadoop-Cluster ausgeführt. Eine Liste mit Transformationsaktivitäten und unterstützten Compute-Umgebungen finden Sie unter [Transformieren von Daten in Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Trigger
Trigger stellen Verarbeitungseinheiten dar, die bestimmen, wann eine Pipelineausführung initiiert wird. Es gibt verschiedene Arten von Triggern für unterschiedliche Arten von Ereignissen. 

### <a name="pipeline-runs"></a>Pipelineausführungen
Eine Pipelineausführung ist eine Instanz einer ausgeführten Pipeline. Zur Instanziierung einer Pipelineausführung werden in der Regel die Argumente an die in Pipelines definierten Parameter übergeben. Die Argumente können manuell oder im Rahmen der Triggerdefinition übergeben werden.

### <a name="parameters"></a>Parameter
Parameter sind Schlüssel-Wert-Paare in einer schreibgeschützten Konfiguration. Sie definieren Parameter in einer Pipeline, und Sie übergeben die Argumente für die definierten Parameter während der Ausführung über einen Ausführungskontext. Der Ausführungskontext wird durch einen Trigger oder über eine Pipeline erstellt, die Sie manuell ausführen. Die Parameterwerte werden von Aktivitäten in der Pipeline genutzt.

Ein Dataset ist ein stark typisierter Parameter und eine Entität, die Sie wiederverwenden oder auf die Sie verweisen können. Eine Aktivität kann auf Datasets verweisen und die Eigenschaften nutzen, die in der Datasetdefinition festgelegt sind.

Bei einem verknüpften Dienst handelt es sich ebenfalls um einen stark typisierten Parameter mit Verbindungsinformationen für einen Datenspeicher oder eine Compute-Umgebung. Er ist auch eine Entität, die Sie wiederverwenden oder auf die Sie verweisen können.

### <a name="control-flows"></a>Ablaufsteuerungen
Ablaufsteuerungen orchestrieren Pipelineaktivitäten. Dies umfasst die Verkettung von Aktivitäten in einer Sequenz, Verzweigungen, auf Pipelineebene definierte Parameter sowie Argumente, die beim bedarfs- oder triggergesteuerten Aufrufen der Pipeline übergeben werden. Ablaufsteuerungen umfassen zudem das Übergeben von benutzerdefinierten Zuständen und Schleifencontainern (ForEach-Iteratoren).


Weitere Informationen zu den Data Factory-Konzepten finden Sie in den folgenden Artikeln:

- [Datasets und verknüpfte Dienste](concepts-datasets-linked-services.md)
- [Pipelines und Aktivitäten](concepts-pipelines-activities.md)
- [Integrationslaufzeit](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Wie sieht das Preismodell für Data Factory aus?
Preisinformationen zu Azure Data Factory finden Sie unter [Data Factory – Preisübersicht](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Wie kann ich hinsichtlich der Informationen zu Data Factory auf dem neuesten Stand bleiben?
Aktuelle Informationen zu Azure Data Factory finden Sie auf den folgenden Websites:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Startseite der Dokumentation](/azure/data-factory)
- [Produktstartseite](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Technischer Einblick 

### <a name="how-can-i-schedule-a-pipeline"></a>Wie kann ich eine Pipeline planen? 
Sie können den Planer-Trigger oder Zeitfenster-Trigger verwenden, um eine Pipeline zu planen. Der Trigger verwendet einen Kalenderplan. Dadurch können Pipelines periodisch oder mit einem kalenderbasierten Wiederholungsmuster (etwa jeden Montag um 18:00 Uhr und jeden Donnerstag um 21:00 Uhr) geplant werden. Weitere Informationen finden Sie unter [Pipelineausführung und -trigger](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Kann ich Parameter an eine Pipelineausführung übergeben?
Ja. Parameter sind ein wichtiges Konzept in Data Factory. Sie können Parameter auf Pipelineebene definieren und Argumente übergeben, während die Pipelineausführung bei Bedarf oder mithilfe eines Triggers gestartet wird.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Kann ich Standardwerte für die Pipelineparameter definieren? 
Ja. Sie können Standardwerte für die Parameter in den Pipelines definieren. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Kann eine Aktivität in einer Pipeline Argumente verarbeiten, die an eine Pipelineausführung übergeben werden? 
Ja. Jede Aktivität innerhalb der Pipeline kann den Parameterwert verwenden, der über das `@parameter`-Konstrukt an die Pipelineausführung übergeben wird. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Kann die Ausgabeeigenschaft einer Aktivität in einer anderen Aktivität verwendet werden? 
Ja. Die Ausgabe einer Aktivität kann mit dem `@activity`-Konstrukt in einer nachfolgenden Aktivität verwendet werden.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Wie werden NULL-Werte ordnungsgemäß in der Ausgabe einer Aktivität behandelt? 
NULL-Werte können mithilfe des `@coalesce`-Konstrukts in den Ausdrücken ordnungsgemäß behandelt werden. 

## <a name="mapping-data-flows"></a>Zuordnen von Datenflüssen

### <a name="which-data-factory-version-do-i-use-to-create-data-flows"></a>Mit welcher Data Factory-Version können Datenflüsse erstellt werden?
Verwenden Sie zum Erstellen von Datenflüssen Data Factory V2.
  
### <a name="i-was-a-previous-private-preview-customer-who-used-data-flows-and-i-used-the-data-factory-v2-preview-version-for-data-flows"></a>Ich war zuvor Kunde mit privater Vorschau und habe die Vorschauversion von Data Factory V2 für Datenflüsse verwendet.
Diese Version ist mittlerweile veraltet. Verwenden Sie Data Factory V2 für Datenflüsse.
  
### <a name="what-has-changed-from-private-preview-to-limited-public-preview-in-regard-to-data-flows"></a>Was hat sich bei Datenflüssen zwischen der privaten Vorschau und der eingeschränkten öffentlichen Vorschau geändert?
Sie müssen nicht mehr Ihre eigenen Azure Databricks-Cluster bereitstellen. Data Factory kümmert sich um das Erstellen und Löschen von Clustern. Blobdatasets und Azure Data Lake Storage Gen2-Datasets sind in Datasets vom Typ „Text mit Trennzeichen“ und „Apache Parquet“ unterteilt. Zum Speichern dieser Dateien können Sie weiterhin Data Lake Storage Gen2 und Blob Storage verwenden. Verwenden Sie den entsprechenden verknüpften Dienst für diese Speicher-Engines.

### <a name="can-i-migrate-my-private-preview-factories-to-data-factory-v2"></a>Kann ich meine Factorys aus der privaten Vorschau zu Data Factory V2 migrieren?

Ja. Eine entsprechende Anleitung finden Sie [hier](https://www.slideshare.net/kromerm/adf-mapping-data-flow-private-preview-migration).

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Ich brauche Hilfe bei der Behandlung von Problemen mit meiner Datenflusslogik. Welche Informationen muss ich angeben, um Hilfe zu erhalten?

Wenn Microsoft Hilfe oder eine Problembehandlung für Datenflüsse bereitstellt, geben Sie bitte den DSL-Codeplan an. Gehen Sie dazu folgendermaßen vor:

1. Wählen Sie rechts oben im Datenfluss-Designer die Option **Code** aus. Dadurch wird der bearbeitbare JSON-Code für den Datenfluss angezeigt.
2. Wählen Sie rechts oben in der Codeansicht die Option **Plan** aus. Mit diesem Umschalter können Sie von JSON zum schreibgeschützten DSL-Skriptplan wechseln.
3. Kopieren Sie dieses Skript, und fügen Sie es ein, oder speichern Sie es in einer Textdatei.

### <a name="how-do-i-access-data-by-using-the-other-80-dataset-types-in-data-factory"></a>Wie kann ich mit den anderen 80 Datasettypen in Data Factory auf Daten zugreifen?

Mit dem Feature „Mapping Data Flow“ können aktuell Azure SQL-Datenbank, Azure SQL Data Warehouse, durch Trennzeichen getrennte Textdateien aus Azure Blob Storage oder Azure Data Lake Storage Gen2 sowie Parquet-Dateien aus Blob Storage oder Data Lake Storage Gen2 nativ für Quelle und Senke verwendet werden. 

Verwenden Sie die Kopieraktivität, um Daten aus einem der anderen Connectors bereitzustellen, und führen Sie dann eine Datenflussaktivität aus, um Daten nach der Bereitstellung zu transformieren. So führt Ihre Pipeline beispielsweise zuerst einen Kopiervorgang nach Blob Storage aus, und anschließend verwendet eine Datenflussaktivität ein Dataset in der Quelle, um diese Daten zu transformieren.

## <a name="next-steps"></a>Nächste Schritte
Schritt-für-Schritt-Anleitungen zum Erstellen einer Data Factory-Instanz finden Sie in den folgenden Tutorials:

- [Schnellstart: Erstellen einer Data Factory](quickstart-create-data-factory-dot-net.md)
- [Tutorial: Kopieren von Daten in die Cloud](tutorial-copy-data-dot-net.md)
