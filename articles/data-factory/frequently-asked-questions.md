---
title: 'Azure Data Factory: Häufig gestellte Fragen '
description: Hier finden Sie Antworten auf häufig gestellte Fragen zu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 2d1b9f0e25c460b7f26c31c3d2c6ebe51d958017
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485013"
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory: Häufig gestellte Fragen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Dieser Artikel enthält Antworten auf häufig gestellte Fragen zu Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>Was ist Azure Data Factory? 
Data Factory ist ein vollständig verwalteter, cloudbasierter ETL-Datenintegrationsdienst, der das Verschieben und Transformieren von Daten automatisiert. Genau wie ein Betrieb, in dem Anlagen Rohmaterialien in Endprodukte umwandeln, organisiert Azure Data Factory vorhandene Dienste so, dass Rohdaten gesammelt und in nutzbare Informationen transformiert werden. 

Sie können mithilfe von Azure Data Factory datengesteuerte Workflows zum Verschieben von Daten zwischen lokalen Datenspeichern und Clouddatenspeichern erstellen. Darüber hinaus können Sie Daten mit Datenflüssen verarbeiten und transformieren. ADF unterstützt außerdem mithilfe von Computediensten wie Azure HDInsight, Azure Databricks und SQL Server Integration Services (SSIS) Integration Runtime externe Computeengines für manuell programmierte Transformationen. 

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

Weitere Informationen finden Sie im [Tutorial: Ablaufsteuerungen](tutorial-control-flow.md).

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
Wenn Sie Ihre SSIS-Workloads verschieben möchten, können Sie eine Data Factory-Instanz erstellen und eine Azure-SSIS-Integration Runtime bereitstellen. Eine Azure-SSIS Integration Runtime ist ein vollständig verwalteter Cluster mit virtuellen Azure-Computern (Knoten), die speziell für die Ausführung von SSIS-Paketen in der Cloud bestimmt sind. Eine ausführliche Anleitung finden Sie im Tutorial [Bereitstellen von SSIS-Paketen in Azure](./tutorial-deploy-ssis-packages-azure.md). 
 
### <a name="sdks"></a>SDKs
Für erfahrene Benutzer, die auf der Suche nach einer befehlsorientierten Benutzerschnittstelle sind, bietet Data Factory ein breites Spektrum an SDKs zum Erstellen, Verwalten und Überwachen von Pipelines mit Ihrer bevorzugten IDE. Die Sprachunterstützung umfasst .NET, PowerShell, Python und REST.

### <a name="monitoring"></a>Überwachung
Sie können Ihre Data Factorys per PowerShell, SDK oder mit den visuellen Überwachungstools auf der Benutzeroberfläche des Browsers überwachen. Sie können bedarfsabhängige, triggerbasierte und zeitgesteuerte benutzerdefinierte Datenflüsse auf effiziente und effektive Weise überwachen und verwalten. Brechen Sie vorhandene Aufgaben ab, nutzen Sie die Fehlerübersicht, zeigen Sie Detailinformationen an, um ausführliche Fehlermeldungen zu erhalten, und debuggen Sie alle Probleme an einem zentralen Ort, ohne den Kontext wechseln oder zwischen Bildschirmen navigieren zu müssen. 

### <a name="new-features-for-ssis-in-data-factory"></a>Neue Features für SSIS in Data Factory
Seit der ersten öffentlichen Vorschauversion im Jahr 2017 wurden mit Data Factory folgende Features für SSIS hinzugefügt:

-    Unterstützung von drei weiteren Konfigurationen/Varianten von Azure SQL-Datenbank zum Hosten der SSIS-Datenbank (SSISDB) mit Projekten/Paketen:
-    SQL-Datenbank mit VNET-Dienstendpunkten
-    Verwaltete SQL-Instanz
-    Pool für elastische Datenbanken
-    Unterstützung eines virtuellen Azure Resource Manager-Netzwerks auf der Grundlage eines klassischen virtuellen Netzwerks, das irgendwann als veraltet eingestuft wird. Dadurch können Sie Ihre Azure-SSIS Integration Runtime in ein virtuelles Netzwerk integrieren, das für SQL-Datenbank mit VNET-Dienstendpunkten, einer verwalteten Instanz und lokalem Datenzugriff konfiguriert ist. Weitere Informationen finden Sie unter [Verknüpfen einer Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk](join-azure-ssis-integration-runtime-virtual-network.md).
-    Unterstützung von Azure AD-Authentifizierung (Azure Active Directory) und SQL-Authentifizierung für die Verbindungsherstellung mit der SSISDB, um die Azure AD-Authentifizierung mit Ihrer von Data Factory verwalteten Identität für Azure-Ressourcen zu ermöglichen
-    Unterstützung der Verwendung Ihrer eigenen SQL Server-Lizenz zur Erzielung erheblicher Kosteneinsparungen über den Azure-Hybridvorteil
-    Unterstützung der Enterprise Edition der Azure-SSIS Integration Runtime mit erweiterten Features, Premium-Features, einer Schnittstelle für das benutzerdefinierte Setup, um zusätzliche Komponenten/Erweiterungen zu installieren, sowie mit einem Partnerökosystem. Weitere Informationen finden Sie unter [Enterprise Edition, benutzerdefiniertes Setup und Erweiterbarkeit durch Drittanbieter für SSIS in ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-    Tiefere Integration von SSIS in Data Factory, die Ihnen das Aufrufen bzw. Auslösen erstklassiger Aktivitäten zum Ausführen des SSIS-Pakets in Data Factory-Pipelines und die Planung per SSMS ermöglicht. Weitere Informationen finden Sie unter [Modernisieren und Erweitern von ETL/ELT-Workflows mit SSIS-Aktivitäten in ADF-Pipelines](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/).


## <a name="what-is-the-integration-runtime"></a>Was ist die Integration Runtime?
Die Integration Runtime ist die Computeinfrastruktur, mit der Azure Data Factory die folgenden Datenintegrationsfunktionen für verschiedene Netzwerkumgebungen bereitstellt:

- **Datenverschiebung:** Zur Datenverschiebung werden die Daten von der Integration Runtime zwischen dem Quell- und Zieldatenspeicher verschoben, während gleichzeitig Unterstützung für integrierte Connectors, Formatkonvertierungen, Spaltenzuordnungen und leistungsfähige sowie skalierbare Datenübertragungen bereitgestellt wird.
- **Verteilungsaktivitäten:** Für die Transformation bietet die Integration Runtime Funktionen zur nativen Ausführung von SSIS-Paketen.
- **SSIS-Pakete ausführen:** Die Integration Runtime führt SSIS-Pakete nativ in einer verwalteten Azure-Computeumgebung aus. Zudem unterstützt die Integration Runtime die Übermittlung und Überwachung von Transformationsaktivitäten, die in verschiedensten Computediensten wie Azure HDInsight, Azure Machine Learning, SQL-Datenbank und SQL Server ausgeführt werden.

Sie können zum Verschieben und Transformieren von Daten je nach Bedarf eine oder mehrere Integration Runtime-Instanzen bereitstellen. Die Integration Runtime kann in einem öffentlichen Azure-Netzwerk oder in einem privaten Netzwerk (lokal, Azure Virtual Network oder Virtual Private Cloud (VPC) von Amazon Web Services) ausgeführt werden. 

Weitere Informationen finden Sie unter [Integrationslaufzeit in Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Welcher Grenzwert besteht hinsichtlich der Anzahl von Integrationslaufzeiten?
Es gibt keine festen Grenzwerte hinsichtlich der Anzahl von Integration Runtime-Instanzen in einer Data Factory. Es gibt jedoch einen Grenzwert hinsichtlich der Anzahl von VM-Kernen, die von Integration Runtime pro Abonnement für die Ausführung von SSIS-Paketen verwendet werden können. Weitere Informationen finden Sie unter [Data Factory-Grenzwerte](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Wie lauten die Hauptkonzepte von Azure Data Factory?
Ein Azure-Abonnement kann über mindestens eine Azure Data Factory-Instanz (oder Data Factory) verfügen. Azure Data Factory enthält vier Hauptkomponenten, die zusammen als Plattform fungieren, auf der Sie datengesteuerte Workflows mit Schritten zum Verschieben und Transformieren von Daten zusammenstellen können.

### <a name="pipelines"></a>Pipelines
Eine Data Factory kann eine oder mehrere Pipelines haben. Bei einer Pipeline handelt es sich um eine logische Gruppierung von Aktivitäten zur Durchführung einer Arbeitseinheit. Gemeinsam führen die Aktivitäten einer Pipeline eine Aufgabe durch. Eine Pipeline kann beispielsweise eine Gruppe von Aktivitäten enthalten, die Daten aus einem Azure-Blob erfasst, und anschließend eine Hive-Abfrage in einem HDInsight-Cluster ausführen, um die Daten zu partitionieren. Der Vorteil ist, dass Sie eine Pipeline zum Verwalten der Aktivitäten als Gruppe verwenden können, statt jede Aktivität einzeln zu verwalten. Sie können die Aktivitäten in einer Pipeline miteinander verketten, um sie sequenziell auszuführen. Sie können sie aber auch unabhängig voneinander parallel ausführen.

### <a name="data-flows"></a>Datenflüsse
Bei Datenflüssen handelt es sich um Objekte, die Sie visuell in Data Factory erstellen und die Daten bedarfsorientiert für Spark-Back-End-Dienste transformieren. Sie müssen nicht mit der Programmierung oder mit Spark vertraut sein. Entwerfen Sie einfach Ihre Datentransformationsabsicht mithilfe von Graphen (Mapping) oder Tabellenkalkulationen (Wrangling).

### <a name="activities"></a>activities
Aktivitäten stellen einen Verarbeitungsschritt in einer Pipeline dar. Beispielsweise können Sie eine Kopieraktivität verwenden, um Daten zwischen zwei Datenspeichern zu kopieren. Analog dazu können Sie eine Hive-Aktivität verwenden, die eine Hive-Abfrage für einen Azure HDInsight-Cluster ausführt, um Ihre Daten zu transformieren oder zu analysieren. Data Factory unterstützt drei Arten von Aktivitäten: Datenverschiebungsaktivitäten, Datentransformationsaktivitäten und Steuerungsaktivitäten.

### <a name="datasets"></a>Datasets
Datasets stellen Datenstrukturen in den Datenspeichern dar, die einfach auf die Daten zeigen bzw. verweisen, die Sie in Ihren Aktivitäten als Eingaben oder Ausgaben verwenden möchten. 

### <a name="linked-services"></a>Verknüpfte Dienste
Verknüpfte Dienste ähneln Verbindungszeichenfolgen, mit denen die Verbindungsinformationen definiert werden, die für Data Factory zum Herstellen einer Verbindung mit externen Ressourcen erforderlich sind. Das können Sie sich wie folgt vorstellen: Ein verknüpfter Dienst definiert die Verbindung mit der Datenquelle, und ein Dataset stellt die Struktur der Daten dar. So gibt etwa ein verknüpfter Azure Storage-Dienst die Verbindungszeichenfolge an, um eine Verbindung mit einem Azure Storage-Konto herzustellen. Und ein Azure-Blobdataset gibt den Blobcontainer und den Ordner an, der die Daten enthält.

Verknüpfte Dienste haben in Data Factory zwei Funktionen:

- Sie stellen einen *Datenspeicher* dar, der beispielsweise eine SQL Server-Instanz, eine Oracle-Datenbankinstanz, eine Dateifreigabe oder ein Azure Blob Storage-Konto enthalten kann (er ist aber nicht darauf beschränkt). Eine Liste der unterstützten Datenspeicher finden Sie unter [Kopieraktivität in Azure Data Factory](copy-activity-overview.md).
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
- [Startseite der Dokumentation](./index.yml)
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

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Ich brauche Hilfe bei der Behandlung von Problemen mit meiner Datenflusslogik. Welche Informationen muss ich angeben, um Hilfe zu erhalten?

Wenn Microsoft Hilfe oder eine Problembehandlung für Datenflüsse bereitstellt, geben Sie bitte das Datenflussskript an. Dies ist das CodeBehind-Skript aus dem Datenflussdiagramm. Öffnen Sie den Datenfluss über die ADF-Benutzeroberfläche, und klicken Sie dann oben rechts auf die Schaltfläche „Skript“. Kopieren Sie dieses Skript, und fügen Sie es ein, oder speichern Sie es in einer Textdatei.

### <a name="how-do-i-access-data-by-using-the-other-90-dataset-types-in-data-factory"></a>Wie kann ich mit den anderen 90 Datasettypen in Data Factory auf Daten zugreifen?

Mit dem Feature „Zuordnungsdatenfluss“ können aktuell Azure SQL-Datenbank, Azure Synapse Analytics, durch Trennzeichen getrennte Textdateien aus Azure Blob Storage oder Azure Data Lake Storage Gen2 sowie Parquet-Dateien aus Blob Storage oder Data Lake Storage Gen2 nativ für Quelle und Senke verwendet werden. 

Verwenden Sie die Kopieraktivität, um Daten aus einem der anderen Connectors bereitzustellen, und führen Sie dann eine Datenflussaktivität aus, um Daten nach der Bereitstellung zu transformieren. So führt Ihre Pipeline beispielsweise zuerst einen Kopiervorgang nach Blob Storage aus, und anschließend verwendet eine Datenflussaktivität ein Dataset in der Quelle, um diese Daten zu transformieren.

### <a name="is-the-self-hosted-integration-runtime-available-for-data-flows"></a>Ist die selbstgehostete Integration Runtime für Datenflüsse verfügbar?

Die selbstgehostete IR ist ein ADF-Pipelinekonstrukt, das Sie mit der Kopieraktivität zum Abrufen oder Verschieben von Daten in und aus lokalen oder VM-basierten Datenquellen und -senken verwenden können. Stellen Sie die Daten zuerst mit einem Kopiervorgang, dann einem Datenfluss für die Transformation und dann einem nachfolgenden Kopiervorgang bereit, wenn Sie die transformierten Daten zurück in den lokalen Speicher verschieben müssen.

### <a name="does-the-data-flow-compute-engine-serve-multiple-tenants"></a>Bedient die Datenfluss-Computerengine mehrere Mandanten?
Cluster werden nie gemeinsam genutzt. Wir garantieren die Isolation für jede Auftragsausführung in Produktionsläufen. Bei einem Debugszenario erhält eine einzige Person einen Cluster, und alle Debuggingfehler werden in diesen Cluster verschoben, der von dem betreffenden Benutzer gestartet wird.

## <a name="wrangling-data-flows"></a>Wranglingdatenflüsse

### <a name="what-are-the-supported-regions-for-wrangling-data-flow"></a>Welche Regionen werden für den Wranglingdatenfluss unterstützt?

Der Wranglingdatenfluss wird derzeit in Data Factorys unterstützt, die in folgenden Regionen erstellt wurden:

* Australien (Osten)
* Kanada, Mitte
* Indien, Mitte
* East US
* USA (Ost) 2
* Japan, Osten
* Nordeuropa
* Asien, Südosten
* USA Süd Mitte
* UK, Süden
* USA, Westen-Mitte
* Europa, Westen
* USA (Westen)
* USA, Westen 2

### <a name="what-are-the-limitations-and-constraints-with-wrangling-data-flow"></a>Welche Einschränkungen bestehen beim Wranglingdatenfluss?

Datasetnamen dürfen nur alphanumerische Zeichen enthalten. Die folgenden Datenspeicher werden unterstützt:

* DelimitedText-Dataset in Azure Blob Storage mithilfe der Kontoschlüsselauthentifizierung
* DelimitedText-Dataset in Azure Data Lake Storage gen2 mithilfe der Kontoschlüssel- oder Dienstprinzipalauthentifizierung
* DelimitedText-Dataset in Azure Data Lake Storage gen1 mithilfe der Dienstprinzipalauthentifizierung
* Azure SQL-Datenbank und Data Warehouse mithilfe der SQL-Authentifizierung. Siehe unterstützte SQL-Typen weiter unten. Es gibt keine PolyBase- oder Stagingunterstützung für Data Warehouse.

Derzeit wird die Key Vault-Integration verknüpfter Dienste in Wranglingdatenflüssen nicht unterstützt.

### <a name="what-is-the-difference-between-mapping-and-wrangling-data-flows"></a>Was ist der Unterschied zwischen Zuordnungsdatenflüssen und Wranglingdatenflüssen?

Mit Zuordnungsdatenflüssen können Sie Daten nach Maß transformieren, ohne Code schreiben zu müssen. Sie können einen Datentransformationsauftrag auf der Datenflusscanvas entwerfen, indem Sie eine Reihe von Transformationen erstellen. Beginnen Sie mit einer beliebigen Anzahl von Quelltransformationen, gefolgt von Datentransformationsschritten. Vervollständigen Sie Ihren Datenfluss mit einer Senke, damit Ihre Ergebnisse an ein Ziel gelangen. Der Zuordnungsdatenfluss eignet sich besonders für das Zuordnen und Transformieren von Daten mit bekannten und unbekannten Schemas in den Senken und Quellen.

Wranglingdatenflüsse ermöglichen Ihnen das Vorbereiten und Durchsuchen agiler Daten mithilfe des Power Query Online-Mashup-Editors in jeder Größenordnung per Spark-Ausführung. Mit dem Anstieg von Data Lakes ist es manchmal erforderlich, ein Dataset zu durchsuchen oder ein Dataset im Lake zu erstellen. Sie nehmen keine Zuordnung zu einem bekannten Ziel vor. Wranglingdatenflüsse werden für weniger formale und modellbasierte Analyseszenarien verwendet.

### <a name="what-is-the-difference-between-power-platform-dataflows-and-wrangling-data-flows"></a>Was ist der Unterschied zwischen Power Platform-Dataflows und Wranglingdatenflüssen?

Mithilfe von Power Platform-Dataflows können Benutzer Daten aus einer Vielzahl von Datenquellen in Common Data Service und Azure Data Lake importieren und transformieren, um PowerApps-Anwendungen, Power BI-Berichten oder Flow-Automatisierungen zu erstellen. Power Platform-Dataflows verwenden die bestehenden Power Query-Oberflächen für die Datenvorbereitung, ähnlich wie Power BI und Excel. Power Platform-Dataflows ermöglichen außerdem die einfache Wiederverwendung innerhalb einer Organisation und die automatische Orchestrierung (z.B. automatisches Aktualisieren von Datenflüssen, die von einem anderen Datenfluss abhängig sind, wenn Erstere aktualisiert werden).

Azure Data Factory (ADF) ist ein verwalteter Datenintegrationsdienst, mit dem Datentechniker und Datenintegratoren ohne Programmiererfahrung komplexe Hybridworkflows mit ETL (Extract-Transform-Load) und ELT (Extract-Load-Transform) erstellen können. Wranglingdatenflüsse in ADF stellen Benutzern eine codefreie, serverlose Umgebung bereit, die die Datenvorbereitung in der Cloud vereinfacht und ohne jegliche Infrastrukturverwaltung auf jede Datengröße skaliert werden kann. Hierbei wird die Datenvorbereitungstechnologie von Power Query (auch in Power Platform-Dataflows, Excel, Power BI verwendet) zum Vorbereiten und Formen der Daten verwendet. Wranglingdatenflüsse sind darauf ausgelegt, mit sämtlichen Komplexitäten und Skalierungsherausforderungen von Big Data-Integrationen umzugehen und ermöglichen Benutzern, Daten schnell in jeder Größenordnung per Spark-Ausführung vorzubereiten. Mit unserer browserbasierten Schnittstelle können Benutzer resiliente Datenpipelines in einer zugänglichen visuellen Umgebung erstellen und ADF die komplexe Spark-Ausführung überlassen. Erstellen Sie Zeitpläne für Ihre Pipelines, und überwachen Sie Ihre Datenflussausführungen über das ADF-Überwachungsportal. Verwalten Sie Datenverfügbarkeits-SLAs ganz einfach mit der umfangreichen Verfügbarkeitsüberwachung und den Warnungen von ADF. Nutzen Sie integrierte Funktionen für Continuous Integration und Continuous Deployment zum Speichern und Verwalten Ihrer Flüsse in einer verwalteten Umgebung. Richten Sie Warnungen ein, und zeigen Sie Ausführungspläne an, um beim Feinabstimmen Ihrer Datenflüsse sicherzustellen, dass Ihre Logik wie geplant abläuft.

### <a name="supported-sql-types"></a>Unterstützte SQL-Typen

Der Wranglingdatenfluss unterstützt die folgenden Datentypen in SQL. Bei Verwendung eines nicht unterstützten Datentyps wird ein Validierungsfehler angezeigt.

* short
* double
* real
* float
* char
* NCHAR
* varchar
* NVARCHAR
* integer
* INT
* bit
* boolean
* SMALLINT
* TINYINT
* BIGINT
* long
* text
* date
* datetime
* datetime2
* smalldatetime
* timestamp
* UNIQUEIDENTIFIER
* Xml

In Zukunft werden weitere Datentypen unterstützt.

## <a name="next-steps"></a>Nächste Schritte
Schritt-für-Schritt-Anleitungen zum Erstellen einer Data Factory-Instanz finden Sie in den folgenden Tutorials:

- [Schnellstart: Erstellen einer Data Factory](quickstart-create-data-factory-dot-net.md)
- [Tutorial: Kopieren von Daten in die Cloud](tutorial-copy-data-dot-net.md)