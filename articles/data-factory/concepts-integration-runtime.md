---
title: Integrationslaufzeit
description: Enthält Informationen zur Integrationslaufzeit in Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: d5e20b1fc0ce32eae8dc2888fdda982f0de95d90
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636645"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integrationslaufzeit in Azure Data Factory 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bei der Integrationslaufzeit (Integration Runtime, IR) handelt es sich um die Computeinfrastruktur, mit der Azure Data Factory die folgenden Datenintegrationsfunktionen für verschiedene Netzwerkumgebungen bereitstellt:

- **Datenfluss** : Ausführen eines [Datenflusses](concepts-data-flow-overview.md) in einer verwalteten Azure-Computeumgebung.  
- **Datenverschiebung:** Dient zum Kopieren von Daten in Datenspeichern im öffentlichen Netzwerk und Datenspeichern im privaten Netzwerk (lokales oder virtuelles privates Netzwerk). Es wird Unterstützung für integrierte Connectors, Formatkonvertierung, Spaltenzuordnung und eine leistungsstarke und skalierbare Datenübertragung bereitgestellt.
- **Aktivitätsverteilung:**  Dient zum Verteilen und Überwachen von Transformationsaktivitäten, die in vielen verschiedenen Computediensten wie Azure Databricks, Azure HDInsight, Azure Machine Learning, Azure SQL-Datenbank, SQL Server und vielen weiteren ausgeführt werden.
- **SSIS-Paketausführung:** Dient zum nativen Ausführen von SSIS-Paketen (SQL Server Integration Services) in einer verwalteten Azure-Computeumgebung.

In Data Factory wird mit einer Aktivität eine durchzuführende Aktion definiert. Mit einem verknüpften Dienst wird ein Zieldatenspeicher oder ein Computedienst definiert. Eine Integrationslaufzeit stellt die Brücke zwischen der Aktivität und verknüpften Diensten dar.  Sie wird vom verknüpften Dienst oder der Aktivität referenziert und stellt die Computeumgebung bereit, in der die Aktivität entweder ausgeführt wird oder aus der sie verteilt wird. Auf diese Weise kann die Aktivität in der Region durchgeführt werden, die dem Zieldatenspeicher bzw. dem Computedienst am nächsten liegt, und es kann die höchste Leistung erzielt werden, während gleichzeitig die Anforderungen an die Sicherheit und Konformität erfüllt werden.

Integration Runtimes sowie sämtliche Aktivitäten, Datasets oder Datenflüsse, die auf diese verweisen, können in der Benutzeroberfläche von Azure Data Factory über den [Verwaltungshub](author-management-hub.md) erstellt werden.

## <a name="integration-runtime-types"></a>Integrationslaufzeit-Typen

Data Factory bietet Integration Runtime-Typen. Wählen Sie den Typ aus, der die gewünschten Anforderungen an die Datenintegrationsfunktionen und die Netzwerkumgebung am besten erfüllt.  Diese drei Typen lauten:

- Azure
- Selbstgehostet
- Azure-SSIS

In der folgenden Tabelle sind die Funktionen und die Netzwerkunterstützung für die einzelnen Integrationslaufzeit-Typen beschrieben:

IR-Typ | Öffentliches Netzwerk | Privates Netzwerk
------- | -------------- | ---------------
Azure | Datenfluss<br/>Datenverschiebung<br/>Aktivitätsverteilung | Datenfluss<br/>Datenverschiebung<br/>Aktivitätsverteilung
Selbstgehostet | Datenverschiebung<br/>Aktivitätsverteilung | Datenverschiebung<br/>Aktivitätsverteilung
Azure-SSIS | SSIS-Paketausführung | SSIS-Paketausführung


## <a name="azure-integration-runtime"></a>Azure-Integrationslaufzeit

Eine Azure Integration Runtime bietet folgende Möglichkeiten:

- Ausführen von Datenflüssen in Azure 
- Ausführen von Kopieraktivitäten zwischen Clouddatenspeichern
- Bereitstellen der folgenden Transformationsaktivitäten im öffentlichen Netzwerk: Databricks Notebook-/ Jar-/ Python-Aktivität, HDInsight Hive-Aktivität, HDInsight Pig-Aktivität, HDInsight MapReduce-Aktivität, HDInsight Spark-Aktivität, HDInsight-Streamingaktivität, Azure Machine Learning Studio-Batchausführungsaktivität (klassisch), Azure Machine Learning Studio-Ressourcenaktualisierungsaktivitäten (klassisch), Aktivität „Gespeicherte Prozedur“, U-SQL-Aktivität für Data Lake Analytics, benutzerdefinierte .NET-Aktivität, Webaktivität, Lookup-Aktivität und Aktivität „Metadaten abrufen“.

### <a name="azure-ir-network-environment"></a>Azure-Integrationslaufzeit: Netzwerkumgebung

Die Azure Integration Runtime unterstützt die Herstellung von Verbindungen mit Datenspeichern und Computediensten mit öffentlich zugänglichen Endpunkten. Indem ein verwaltetes virtuelles Netzwerk aktiviert wird, unterstützt Azure Integration Runtime das Herstellen einer Verbindung mit Datenspeichern über den privaten Verknüpfungsdienst in einer privaten Netzwerkumgebung.

### <a name="azure-ir-compute-resource-and-scaling"></a>Azure-Integrationslaufzeit: Computeressource und Skalierung
Die Azure-Integrationslaufzeit stellt in Azure eine vollständig verwaltete, serverlose Computeressource bereit.  Sie müssen sich keine Gedanken um die Infrastrukturbereitstellung, die Softwareinstallation, das Patchen oder die Kapazitätsskalierung machen.  Darüber hinaus zahlen Sie nur für die tatsächliche Nutzungsdauer.

Mit der Azure-Integrationslaufzeit wird die native Computeressource zum sicheren, zuverlässigen und leistungsstarken Verschieben von Daten zwischen Clouddatenspeichern bereitgestellt.  Sie können festlegen, wie viele Datenintegrationseinheiten für die Kopieraktivität verwendet werden. Die Computegröße der Azure IR wird entsprechend elastisch zentral hochskaliert, ohne dass Sie die Größe der Azure-Integrationslaufzeit explizit anpassen müssen. 

Die Aktivitätsverteilung ist ein einfacher Vorgang zum Weiterleiten der Aktivität an den Zielcomputedienst. Deshalb muss die Computegröße bei diesem Szenario nicht zentral hochskaliert werden.

Informationen zur Erstellung und Konfiguration einer Azure IR finden Sie unter [Erstellen und Konfigurieren von Azure Integration Runtime](create-azure-integration-runtime.md). 

> [!NOTE] 
> Azure Integration Runtime verfügt über Eigenschaften, die mit der Datenflussruntime in Zusammenhang stehen, die die zugrunde liegende Computeinfrastruktur definiert, auf der die Datenflüsse ausgeführt würden. 

## <a name="self-hosted-integration-runtime"></a>Selbstgehostete Integrationslaufzeit

Eine selbstgehostete Integrationslaufzeit ermöglicht Folgendes:

- Ausführen einer Kopieraktivität zwischen einem Clouddatenspeicher und einem Datenspeicher im privaten Netzwerk.
- Bereitstellen der folgenden Transformationsaktivitäten für Computeressourcen im lokalen Netzwerk oder im virtuellen Azure-Netzwerk: HDInsight Hive-Aktivität (Bring Your Own Cluster, BYOC), HDInsight Pig-Aktivität (BYOC), HDInsight MapReduce-Aktivität (BYOC), HDInsight Spark-Aktivität (BYOC), HDInsight-Streamingaktivität (BYOC), Azure Machine Learning Studio-Batchausführungsaktivität (klassisch), Azure Machine Learning Studio-Ressourcenaktualisierungsaktivitäten (klassisch), Aktivität „Gespeicherte Prozedur“, U-SQL-Aktivität für Data Lake Analytics, benutzerdefinierte Aktivität (wird in Azure Batch ausgeführt), Lookup-Aktivität und Aktivität „Metadaten abrufen“.

> [!NOTE] 
> Verwenden Sie die selbstgehostete Integrationslaufzeit zum Unterstützen von Datenspeichern, für die „Bring-your-own Driver“ erforderlich ist, z.B. SAP Hana, MySQL usw.  Weitere Informationen finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE] 
> Die Java Runtime Environment (JRE) ist eine Abhängigkeit der selbstgehosteten IR. Stellen Sie sicher, dass die JRE auf dem gleichen Host installiert ist.

### <a name="self-hosted-ir-network-environment"></a>Selbstgehostete Integrationslaufzeit: Netzwerkumgebung

Wenn Sie die Datenintegration auf sichere Weise in einer privaten Netzwerkumgebung durchführen möchten, die nicht über eine direkte Sichtverbindung aus der öffentlichen Cloudumgebung verfügt, können Sie eine selbstgehostete IR in der lokalen Umgebung hinter Ihrer Unternehmensfirewall oder in einem virtuellen privaten Netzwerk installieren.  Die selbstgehostete Integrationslaufzeit stellt nur ausgehende HTTP-basierte Verbindungen in das offene Internet her.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Selbstgehostete Integrationslaufzeit: Computeressource und Skalierung

Installieren Sie die selbstgehostete IR auf einem lokalen Computer oder einem virtuellen Computer in einem privaten Netzwerk. Derzeit wird nur das Ausführen der selbstgehosteten Integrationslaufzeit auf einem Windows-Betriebssystem unterstützt.  

Zur Erzielung von Hochverfügbarkeit und Skalierbarkeit können Sie die selbstgehostete Integrationslaufzeit aufskalieren, indem Sie die logische Instanz mehreren lokalen Computern im Aktiv-Aktiv-Modus zuordnen.  Ausführlichere Informationen finden Sie in den Anleitungen im Artikel [Erstellen und Konfigurieren einer selbstgehosteten Integration Runtime](create-self-hosted-integration-runtime.md).

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS-Integrationslaufzeit

Für die Durchführung von Lift & Shift-Vorgängen für vorhandene SSIS-Workloads können Sie eine Azure-SSIS-Integrationslaufzeit erstellen, um SSIS-Pakete nativ auszuführen.

### <a name="azure-ssis-ir-network-environment"></a>Azure-SSIS-Integrationslaufzeit: Netzwerkumgebung

Die Azure-SSIS--Integrationslaufzeit kann entweder im öffentlichen oder im privaten Netzwerk bereitgestellt werden.  Der lokale Datenzugriff wird unterstützt, indem Azure-SSIS-IR mit einem virtuellen Netzwerk verknüpft wird, für das eine Verbindung mit Ihrem lokalen Netzwerk besteht.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Azure-SSIS-Integrationslaufzeit: Computeressource und Skalierung

Die Azure-SSIS-Integrationslaufzeit ist ein vollständig verwalteter Cluster mit Azure-VMs, die speziell für die Ausführung von SSIS-Paketen bestimmt sind. Sie können Ihre eigene Azure SQL-Datenbank-Instanz oder verwaltete SQL-Instanz für den Katalog mit den SSIS-Projekten/-Paketen (SSISDB) bereitstellen. Sie können die Computeleistung hochskalieren, indem Sie die Knotengröße angeben und aufskalieren. Geben Sie hierzu die Anzahl von Knoten im Cluster an. Sie können die Kosten für die Ausführung Ihrer Azure-SSIS-Integrationslaufzeit verwalten, indem Sie sie je nach Bedarf anhalten und starten.

Weitere Informationen finden Sie in den Anleitungen unter „Gewusst wie: Erstellen und Konfigurieren von Azure-SSIS-Integrationslaufzeit“.  Nach der Erstellung können Sie Ihre vorhandenen SSIS-Pakete mit nur wenigen oder auch ganz ohne Änderungen bereitstellen und verwalten, indem Sie vertraute Tools wie SQL Server Data Tools (SSDT) und SQL Server Management Studio (SSMS) verwenden – genauso wie bei der lokalen Nutzung von SSIS.

Weitere Informationen zur Azure-SSIS-Laufzeit finden Sie in den folgenden Artikeln: 

- [Tutorial: Bereitstellen von SSIS-Paketen in Azure](./tutorial-deploy-ssis-packages-azure.md): Dieser Artikel enthält schrittweise Anweisungen zum Erstellen einer Azure-SSIS-Integrationslaufzeit und verwendet eine Azure SQL-Datenbank zum Hosten des SSIS-Katalogs. 
- [Vorgehensweise: Azure-SSIS Integration Runtime in Azure Data Factory](create-azure-ssis-integration-runtime.md): In diesem Artikel wird das Tutorial vertieft, und er enthält Anleitungen zum Verwenden einer verwalteten Azure SQL-Instanz und zum Einbinden der IR in ein virtuelles Netzwerk. 
- [Überwachen einer Azure-SSIS-Integrationslaufzeit](monitor-integration-runtime.md#azure-ssis-integration-runtime): In diesem Artikel wird das Abrufen von Informationen zu einer Azure-SSIS-Integrationslaufzeit veranschaulicht, und er enthält Beschreibungen der Status in den zurückgegebenen Informationen. 
- [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md): In diesem Artikel wird beschrieben, wie Sie eine Azure-SSIS-Integrationslaufzeit beenden, starten oder entfernen. Es wird zudem gezeigt, wie Sie Ihre Azure-SSIS-Integrationslaufzeit aufskalieren, indem Sie der Integrationslaufzeit weitere Knoten hinzufügen. 
- [Verknüpfen einer Azure-SSIS-Integration Runtime mit einem virtuellen Netzwerk:](join-azure-ssis-integration-runtime-virtual-network.md). Dieser Artikel enthält grundlegende Informationen zum Verknüpfen einer Azure-SSIS-IR mit einem virtuellen Azure-Netzwerk. Darüber hinaus enthält er Schritte zur Verwendung des Azure-Portals zum Konfigurieren des virtuellen Netzwerks, damit die Azure-SSIS-IR dem virtuellen Netzwerk beitreten kann. 

## <a name="integration-runtime-location"></a>Ort der Integrationslaufzeit

### <a name="relationship-between-factory-location-and-ir-location"></a>Beziehung zwischen Speicherort der Factory und IR-Speicherort

Wenn ein Kunde eine Data Factory-Instanz erstellt, muss der Speicherort für diese angegeben werden. Am Data Factory-Standort werden die Metadaten der Data Factory gespeichert, und von diesem Standort wird auch die Auslösung der Pipeline initiiert. Die Metadaten für die Factory werden nur in der vom Kunden ausgewählten Region gespeichert, nicht in anderen Regionen.

Eine Data Factory kann währenddessen auf Datenspeicher und Compute Services in anderen Azure-Regionen zugreifen, um Daten zwischen Datenspeichern zu verschieben oder Daten mithilfe von Computediensten zu verarbeiten. Dieses Verhalten wird durch die [global verfügbare Integrationslaufzeit](https://azure.microsoft.com/global-infrastructure/services/) realisiert, um für Datenkonformität, Effizienz und geringere Kosten für ausgehenden Netzwerkdatenverkehr zu sorgen.

Mit dem Integrationslaufzeit-Standort wird der Standort der Back-End-Computeinstanz definiert. Somit ist dies auch der Standort, an dem die Datenverschiebung, Aktivitätsverteilung und SSIS-Paketausführung durchgeführt werden. Der Integrationslaufzeit-Standort kann sich vom Standort der Data Factory unterscheiden, zu der er gehört. 

### <a name="azure-ir-location"></a>Azure-Integrationslaufzeit: Standort

Wenn Sie für eine Azure-Integrationslaufzeit einen bestimmten Standort festlegen, erfolgt die Aktivitätsausführung oder -verteilung in der entsprechenden Region.

Bei Verwendung von Azure IR mit automatischer Auflösung (Standardeinstellung) in einem öffentlichen Netzwerk gilt Folgendes:

- Bei der Kopieraktivität versucht ADF, den Standort Ihres Senkendatenspeichers automatisch zu erkennen, und verwendet dann die Integrationslaufzeit entweder in derselben Region (falls verfügbar) oder in der nächstgelegenen Region im selben geografischen Gebiet. Wenn die Region des Senkendatenspeichers nicht erkannt werden kann, wird alternativ die Integrationslaufzeit in der Data Factory-Region verwendet.

  Beispiel: Sie haben Ihre Factory in „USA, Osten“ erstellt. 
  
  - Wenn ADF beim Kopieren von Daten in ein Azure-Blob in „USA, Westen“ erfolgreich erkannt hat, dass sich das Blob in „USA, Westen“ befindet, wird die Kopieraktivität in der Integrationslaufzeit in „USA, Westen“ ausgeführt. Schlägt die Regionserkennung fehl, wird die Kopieraktivität in der Integrationslaufzeit in „USA, Osten“ ausgeführt.
  - Wenn beim Kopieren von Daten in Salesforce die Region nicht erkannt werden kann, wird die Kopieraktivität in der Integrationslaufzeit in „USA, Osten“ ausgeführt.

  >[!TIP] 
  >Wenn Ihre Daten strengen Complianceanforderungen unterliegen und ein bestimmtes geografisches Gebiet nicht verlassen dürfen, können Sie explizit eine Azure-Integrationslaufzeit in einer bestimmten Region erstellen und den verknüpften Dienst mithilfe der ConnectVia-Eigenschaft auf diese Integrationslaufzeit verweisen. Wenn Sie beispielsweise Daten aus einem Blob in der Region „Vereinigtes Königreich, Süden“ in Azure Synapse Analytics in der Region „Vereinigtes Königreich, Süden“ kopieren und dabei sicherstellen möchten, dass die Daten das Vereinigte Königreich nicht verlassen, erstellen Sie eine Azure IR in der Region „Vereinigtes Königreich, Süden“, und verknüpfen Sie beide verknüpften Dienste mit dieser IR.

- Für Vorgänge zur Ausführung von Lookup-/GetMetadata-/Delete-Aktivitäten (auch als Pipelineaktivitäten bezeichnet), zur Verteilung von Transformationsaktivitäten (auch als externe Aktivitäten bezeichnet) und zur Erstellung (Verbindung testen, Ordner- und Tabellenliste durchsuchen, Daten als Vorschau anzeigen) verwendet ADF die IR in der Data Factory-Region.

- Für den Datenfluss verwendet ADF die IR in der Data Factory-Region. 

  > [!TIP] 
  > Eine bewährte Methode wäre, sicherzustellen, dass der Datenfluss (sofern möglich) in der gleichen Region wie Ihre entsprechenden Datenspeicher ausgeführt wird. Sie können dies entweder durch die automatische Auflösung der Azure IR-Instanz (falls der Datenspeicherort mit dem Data Factory-Standort identisch ist) oder das Erstellen einer neuen Azure-IR-Instanz, die sich in der gleichen Region wie Ihre Datenspeicher befindet, und anschließendes Ausführen des Datenflusses erreichen. 

Wenn Sie ein verwaltetes virtuelles Netzwerk für Azure IR mit automatischer Auflösung aktivieren, verwendet ADF die IR in der Data Factory-Region. 

In der Überwachungsansicht für Pipelineaktivitäten (auf der Benutzeroberfläche) oder über die Nutzlast für die Aktivitätsüberwachung können Sie überwachen, welcher Integrationslaufzeit-Standort bei der Aktivitätsausführung verwendet wird.

### <a name="self-hosted-ir-location"></a>Selbstgehostete Integrationslaufzeit: Standort

Die selbstgehostete Integrationslaufzeit wird logisch unter der Data Factory registriert, und die Computekomponente, mit der die Funktionen unterstützt werden, wird von Ihnen bereitgestellt. Aus diesem Grund ist für die selbstgehostete Integrationslaufzeit keine explizite Standorteigenschaft vorhanden. 

Bei Verwendung zum Durchführen der Datenverschiebung extrahiert die selbstgehostete Integrationslaufzeit Daten aus der Quelle und schreibt sie an das Ziel.

### <a name="azure-ssis-ir-location"></a>Azure-SSIS-Integrationslaufzeit: Standort

Die Auswahl des richtigen Standorts für Ihre Azure-SSIS-Integrationslaufzeit ist entscheidend, um für Ihre ETL-Workflows (Extrahieren-Transformieren-Laden) eine hohe Leistung zu erzielen.

- Der Standort der Azure-SSIS IR muss nicht dem Standort Ihrer Data Factory entsprechen. Es sollte aber derselbe Standort wie für Ihre eigene Azure SQL-Datenbank-Instanz bzw. verwaltete SQL-Instanz für die SSISDB sein. Ihre Azure-SSIS-Integrationslaufzeit kann dann leicht auf SSISDB zugreifen, ohne dass es zwischen unterschiedlichen Standorten zu übermäßig viel Datenverkehr kommt.
- Falls Sie nicht über eine SQL-Datenbank-Instanz oder eine verwaltete SQL-Instanz verfügen, aber über lokale Datenquellen bzw. -ziele, sollten Sie wie folgt vorgehen: Erstellen Sie eine neue Azure SQL-Datenbank-Instanz bzw. verwaltete SQL-Instanz am Standort eines virtuellen Netzwerks, das mit Ihrem lokalen Netzwerk verbunden ist.  Auf diese Weise können Sie an demselben Ort die Azure-SSIS IR mit der neuen Azure SQL-Datenbank-Instanz bzw. der verwalteten SQL-Instanz erstellen und den Beitritt zu diesem virtuellen Netzwerk durchführen. Hierdurch werden die Datenverschiebungen zwischen unterschiedlichen Standorten effektiv reduziert.
- Wenn der Standort Ihrer vorhandenen Azure SQL-Datenbank-Instanz bzw. verwalteten SQL-Instanz nicht dem Standort eines virtuellen Netzwerks entspricht, das mit Ihrem lokalen Netzwerk verbunden ist, sollten Sie wie folgt vorgehen: Erstellen Sie zuerst die Azure-SSIS IR, indem Sie eine vorhandene Azure SQL-Datenbank-Instanz bzw. eine verwaltete SQL-Instanz verwenden und den Beitritt zu einem anderen virtuellen Netzwerk an demselben Ort durchführen. Konfigurieren Sie anschließend eine VNET-zu-VNET-Verbindung zwischen unterschiedlichen Standorten.

Im folgenden Diagramm sind die Standorteinstellungen von Data Factory und die dazugehörigen Integrationslaufzeiten dargestellt:

![Ort der Integrationslaufzeit](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Ermitteln der richtigen Integrationslaufzeit

### <a name="copy-activity"></a>Copy-Aktivität

Für die Kopieraktivität sind hierbei verknüpfte Quellen- und Senkendienste zum Definieren der Datenflussrichtung erforderlich. Anhand der folgenden Logik wird ermittelt, welche Integrationslaufzeit-Instanz zum Durchführen des Kopiervorgangs verwendet wird: 

- **Kopieren zwischen zwei Clouddatenquellen** : Wenn sowohl der verknüpfte Quelldienst als auch der verknüpfte Senkendienst die Azure IR nutzt, verwendet ADF die regionale Azure IR (sofern angegeben) oder bestimmt automatisch einen Speicherort der Azure IR, sofern Sie wie unter [Ort der Integrationslaufzeit](#integration-runtime-location) beschrieben die IR mit automatischer Auflösung (Standardeinstellung) ausgewählt haben.
- **Kopieren zwischen einer Clouddatenquelle und einer Datenquelle im privaten Netzwerk** : Wenn entweder der verknüpfte Quellen- oder Senkendienst auf eine selbstgehostete Integrationslaufzeit zeigt, wird die Kopieraktivität unter dieser selbstgehosteten Integrationslaufzeit ausgeführt.
- **Kopieren zwischen zwei Datenquellen im privaten Netzwerk** : Sowohl der verknüpfte Quelldienst als auch der verknüpfte Senkendienst muss auf die gleiche Integrationslaufzeit verweisen, und diese Integrationslaufzeit wird zum Ausführen der Kopieraktivität verwendet.

### <a name="lookup-and-getmetadata-activity"></a>Lookup-/GetMetadata-Aktivität

Die Lookup-/GetMetadata-Aktivität wird für die Integrationslaufzeit ausgeführt, die dem verknüpften Datenspeicherdienst zugeordnet ist.

### <a name="external-transformation-activity"></a>Externe Transformationsaktivität

Jede externe Transformationsaktivität, die eine externe Compute-Engine nutzt, verfügt über einen verknüpften Zielcomputedienst, der auf eine Integration Runtime verweist. Diese Integration Runtime-Instanz bestimmt den Standort, von dem die externe manuell codierte Transformationsaktivität gesendet wird.

### <a name="data-flow-activity"></a>Datenflussaktivität

Datenflussaktivitäten werden auf der ihnen zugeordneten Azure Integration Runtime ausgeführt. Der für Datenflüsse genutzte Spark-Computedienst wird durch die Datenflusseigenschaften in Ihrer Azure Integration Runtime bestimmt und vollständig von ADF verwaltet.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in folgenden Artikeln:

- [Erstellen einer Azure Integration Runtime-Instanz](create-azure-integration-runtime.md)
- [Create self-hosted integration runtime](create-self-hosted-integration-runtime.md) (Erstellen einer selbstgehosteten Integrationslaufzeit)
- [Erstellen einer Azure-SSIS Integration Runtime in Azure Data Factory | Microsoft-Dokumentation](create-azure-ssis-integration-runtime.md). In diesem Artikel wird das Tutorial vertieft, und er enthält Anleitungen zum Verwenden einer verwalteten Azure SQL-Instanz und zum Einbinden der IR in ein virtuelles Netzwerk.