---
title: Überwachen der Integrationslaufzeit in Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie verschiedene Arten der Integrationslaufzeit in Azure Data Factory überwacht werden.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/25/2018
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: b62cbe75730da8c5764839d41887deb7e6cd0e90
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66122610"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Überwachen einer Integrationslaufzeit in Azure Data Factory  
Bei der **Integrationslaufzeit** (Integration Runtime, IR) handelt es sich um die Computeinfrastruktur, mit der Azure Data Factory mehrere Datenintegrationsfunktionen übergreifend für verschiedene Netzwerkumgebungen bereitstellt. Es werden drei Arten von Integrationslaufzeiten von Azure Data Factory angeboten:

- Azure-Integrationslaufzeit
- Selbstgehostete Integrationslaufzeit
- Azure SSIS-Integrationslaufzeit

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Führen Sie den folgenden PowerShell-Befehl aus, um den Status einer Instanz der Integrationslaufzeit (IR) abzurufen: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

Das Cmdlet gibt für verschiedene Arten der Integrationslaufzeit unterschiedliche Informationen zurück. In diesem Artikel werden die Eigenschaften und Statusangaben für die einzelnen Arten der Integrationslaufzeit erläutert.  

## <a name="azure-integration-runtime"></a>Azure-Integrationslaufzeit
Die Computeressource für eine Azure-Integrationslaufzeit wird in Azure vollständig flexibel verwaltet. Die folgende Tabelle enthält Beschreibungen für Eigenschaften, die vom **Get-AzDataFactoryV2IntegrationRuntime**-Befehl zurückgegeben werden:

### <a name="properties"></a>Eigenschaften
Die folgende Tabelle enthält Beschreibungen für Eigenschaften, die vom Cmdlet für eine Azure-Integrationslaufzeit zurückgegeben werden:

| Eigenschaft | BESCHREIBUNG |
-------- | ------------- | 
| NAME | Name der Azure-Integrationslaufzeit. |  
| Zustand | Status der Azure-Integrationslaufzeit. | 
| Location | Standort der Azure-Integrationslaufzeit. Weitere Informationen zum Standort einer Azure-Integrationslaufzeit finden Sie unter [Einführung in die Integrationslaufzeit](concepts-integration-runtime.md). |
| DataFactoryName | Name der Data Factory, zu der die Azure-Integrationslaufzeit gehört. | 
| ResourceGroupName | Name der Ressourcengruppe, zu der die Data Factory gehört.  |
| BESCHREIBUNG | Beschreibung der Integrationslaufzeit.  |

### <a name="status"></a>Status
Die folgende Tabelle enthält die möglichen Statuswerte einer Azure-Integrationslaufzeit:

| Status | Kommentare/Szenarien | 
| ------ | ------------------ |
| Online | Die Azure-Integrationslaufzeit ist online und einsatzbereit. | 
| Offline | Die Azure-Integrationslaufzeit ist aufgrund eines internen Fehlers offline. |

## <a name="self-hosted-integration-runtime"></a>Selbstgehostete Integrationslaufzeit
Dieser Abschnitt enthält Beschreibungen für Eigenschaften, die vom Get-AzDataFactoryV2IntegrationRuntime-Cmdlet zurückgegeben werden. 

> [!NOTE] 
> Die zurückgegebenen Eigenschaften und Statuswerte enthalten Informationen zur allgemeinen selbstgehosteten Integrationslaufzeit und zu jedem Knoten in der Integrationslaufzeit.  

### <a name="properties"></a>Eigenschaften

Die folgende Tabelle enthält Beschreibungen von Überwachungseigenschaften für **jeder Knoten**:

| Eigenschaft | BESCHREIBUNG | 
| -------- | ----------- | 
| NAME | Name der selbstgehosteten Integrationslaufzeit und die ihr zugeordneten Knoten. Der Knoten ist ein lokaler Windows-Computer, auf dem sich die selbstgehostete Integrationslaufzeit befindet. |  
| Status | Der Status der allgemeinen selbstgehosteten Integrationslaufzeit und der einzelnen Knoten. Beispiel: Online/Offline/Eingeschränkt usw. Informationen zu diesen Status finden Sie im nächsten Abschnitt. | 
| Version | Die Version der selbstgehosteten Integrationslaufzeit und der einzelnen Knoten. Die Version der selbstgehosteten Integrationslaufzeit wird basierend auf der Version bestimmt, die die meisten Knoten der Gruppe aufweisen. Wenn Knoten mit unterschiedlichen Versionen am Setup der selbstgehosteten Integrationslaufzeit beteiligt sind, funktionieren nur die Knoten mit der gleichen Versionsnummer wie bei der selbstgehosteten Integrationslaufzeit richtig. Andere Knoten befinden sich im eingeschränkten Modus und müssen manuell aktualisiert werden (nur für den Fall, dass die automatische Aktualisierung fehlschlägt). | 
| Verfügbarer Arbeitsspeicher | Verfügbarer Arbeitsspeicher auf einem Knoten der selbstgehosteten Integrationslaufzeit. Dieser Wert steht für eine Momentaufnahme nahezu in Echtzeit. | 
| CPU-Auslastung | CPU-Auslastung für einen Knoten der selbstgehosteten Integrationslaufzeit. Dieser Wert steht für eine Momentaufnahme nahezu in Echtzeit. |
| Netzwerk (Eingang/Ausgang) | Netzwerkauslastung für einen Knoten der selbstgehosteten Integrationslaufzeit. Dieser Wert steht für eine Momentaufnahme nahezu in Echtzeit. | 
| Gleichzeitige Aufträge (ausgeführt/Limit) | **Wird ausgeführt**. Anzahl von Aufträgen oder Aufgaben, die auf den einzelnen Knoten ausgeführt werden. Dieser Wert steht für eine Momentaufnahme nahezu in Echtzeit. <br/><br/>**Limit**. Mit „Limit“ wird angegeben, wie viele Aufträge für einen Knoten jeweils gleichzeitig ausgeführt werden können. Dieser Wert wird basierend auf der Größe des Computers definiert. Wenn Zeitüberschreitungen für Aktivitäten auftreten, können Sie das Limit auch dann erhöhen, um die gleichzeitige Ausführung von Aufträgen in erweiterten Szenarien zentral hochzuskalieren, wenn CPU, Arbeitsspeicher oder Netzwerk nicht voll ausgelastet sind. Diese Funktion ist auch für eine selbstgehostete Integrationslaufzeit mit einem einzelnen Knoten verfügbar. |
| Rolle | Bei einer selbstgehosteten Integrationslaufzeit mit mehreren Knoten gibt es zwei Arten von Rollen: Verteiler und Worker. Alle Knoten sind Worker. Dies bedeutet, dass alle Knoten zum Ausführen von Aufträgen verwendet werden können. Es ist nur ein Verteilerknoten vorhanden, der zum Durchführen der Pullvorgänge für Aufgaben bzw. Aufträge von Clouddiensten und Verteilen an die einzelnen Workerknoten genutzt wird. Der Verteilerknoten ist auch ein Workerknoten. |

Einige Einstellungen der Eigenschaften sind sinnvoller, wenn in der selbstgehosteten Integrationslaufzeit (d.h. in einem Szenario mit horizontalem Hochskalieren) mindestens zwei Knoten enthalten sind.

#### <a name="concurrent-jobs-limit"></a>Limit für gleichzeitige Aufträge

Der Standardwert des Limits für gleichzeitige Aufträge basierend auf der Größe des Computers. Welche Faktoren zum Berechnen dieses Wertes verwendet werden, hängt von der Größe des Arbeitsspeichers und der Anzahl der CPU-Kerne des Computers ab. Je mehr Kerne und Arbeitsspeicher, desto höher das Standardlimit für gleichzeitige Aufträge.

Sie skalieren horizontal hoch, indem Sie die Anzahl der Knoten erhöhen. Wenn Sie die Anzahl von Knoten erhöhen, entspricht das Limit für gleichzeitige Aufträge der Summe der Grenzwerte für gleichzeitige Aufträge aller verfügbaren Knoten.  Wenn Sie z.B. auf einem Knoten maximal zwölf gleichzeitige Aufträge ausführen können und drei ähnliche Knoten hinzufügen, können Sie maximal 48 (d.h. 4 x 12) gleichzeitige Aufträge ausführen. Sie sollten das Limit für gleichzeitige Aufträge nur dann erhöhen, wenn Sie mit den Standardwerten auf jedem Knoten eine geringe Ressourcennutzung feststellen.

Sie können den berechneten Standardwert im Azure-Portal überschreiben. Wählen Sie „Autor > Verbindungen > Integration Runtimes > Bearbeiten > Knoten > Wert für gleichzeitige Aufträge pro Knoten ändern“ aus. Sie können auch den PowerShell-Befehl [update-Azdatafactoryv2integrationruntimenode](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) verwenden.
  
### <a name="status-per-node"></a>Status (pro Knoten)
Die folgende Tabelle enthält die möglichen Statuswerte eines Knotens einer selbstgehosteten Integrationslaufzeit:

| Status | BESCHREIBUNG |
| ------ | ------------------ | 
| Online | Der Knoten ist mit dem Data Factory-Dienst verbunden. |
| Offline | Knoten ist offline. |
| Wird aktualisiert | Der Knoten wird automatisch aktualisiert. |
| Eingeschränkt | Es besteht ein Konnektivitätsproblem. Dies kann ein Problem mit HTTP-Port 8050, mit der Service Bus-Konnektivität oder mit der Synchronisierung von Anmeldeinformationen sein. |
| Inaktiv | Die Konfiguration des Knotens unterscheidet sich von der Konfiguration anderer Mehrheitsknoten. |

Ein Knoten kann inaktiv sein, wenn er keine Verbindungen mit anderen Knoten herstellen kann.

### <a name="status-overall-self-hosted-integration-runtime"></a>Status (allgemeine selbstgehostete Integrationslaufzeit)
Die folgende Tabelle enthält die möglichen Statuswerte einer selbstgehosteten Integrationslaufzeit: Dieser Status hängt von den Statuswerten aller Knoten ab, die zur Laufzeit gehören. 

| Status | BESCHREIBUNG |
| ------ | ----------- | 
| Registrierung erforderlich | Für diese selbstgehostete Integrationslaufzeit ist noch kein Knoten registriert. |
| Online | Alle Knoten sind online. |
| Offline | Kein Knoten ist online. |
| Eingeschränkt | Nicht alle Knoten in dieser selbstgehosteten Integrationslaufzeit weisen einen fehlerfreien Status auf. Dieser Status ist eine Warnung, dass einige Knoten unter Umständen ausgefallen sind. Dieser Status kann aufgrund eines Problems mit der Synchronisierung von Anmeldeinformationen auf einem Verteiler- oder Workerknoten auftreten. |

Rufen Sie die JSON-Nutzlast, die die ausführlichen Eigenschaften der selbstgehosteten Integrationslaufzeit enthält, und deren Momentaufnahmewerte während der Ausführung des Cmdlets mithilfe des **Get-AzDataFactoryV2IntegrationRuntimeMetric**-Cmdlets ab.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName  | | ConvertTo-Json 
```

Beispielausgabe (es wird angenommen, dass dieser selbstgehosteten Integrationslaufzeit zwei Knoten zugeordnet sind):

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```


## <a name="azure-ssis-integration-runtime"></a>Azure SSIS-Integrationslaufzeit
Die Azure-SSIS-Integrationslaufzeit ist ein vollständig verwalteter Cluster mit virtuellen Azure-Computern (oder Knoten), die speziell für die Ausführung Ihrer SSIS-Pakete bestimmt sind. Es werden keine anderen Aktivitäten von Azure Data Factory ausgeführt. Nach der Bereitstellung können Sie ihre Eigenschaften abfragen und ihre allgemeinen/knotenabhängigen Status überwachen.

### <a name="properties"></a>Eigenschaften

| Eigenschaft/Status | BESCHREIBUNG |
| --------------- | ----------- |
| CreateTime | Die UTC-Zeit für die Erstellung Ihrer Azure-SSIS-Integrationslaufzeit. |
| Nodes | Die zugeordneten/verfügbaren Knoten Ihrer Azure-SSIS-Integrationslaufzeit mit knotenabhängigen Status (wird gestartet/verfügbar/wird wiederverwendet/nicht verfügbar) und handlungsrelevanten Fehlern. |
| OtherErrors | Die nicht knotenabhängigen handlungsrelevanten Fehler für Ihre Azure-SSIS-Integrationslaufzeit. |
| LastOperation | Das Ergebnis des letzten Vorgangs zum Starten/Beenden für Ihre Azure-SSIS-Integrationslaufzeit mit handlungsrelevanten Fehlern, wenn ein Fehler aufgetreten ist. |
| Zustand | Der allgemeine Status (Initial/Wird gestartet/Gestartet/Wird beendet/Beendet) Ihrer Azure-SSIS-Integrationslaufzeit. |
| Location | Der Standort Ihrer Azure-SSIS-Integrationslaufzeit. |
| NodeSize | Die Größe der einzelnen Knoten Ihrer Azure-SSIS-Integrationslaufzeit. |
| NodeCount | Die Anzahl der Knoten in Ihrer Azure-SSIS-Integrationslaufzeit. |
| MaxParallelExecutionsPerNode | Die Anzahl gleichzeitiger Ausführungen pro Knoten in Ihrer Azure-SSIS-Integrationslaufzeit. |
| CatalogServerEndpoint | Der Endpunkt Ihres vorhandenen Servers für die Azure SQL-Datenbank/verwaltete Instanz zum Hosten der SSISDB. |
| CatalogAdminUserName | Der Benutzername des Administrators Ihres vorhandenen Servers für die Azure SQL-Datenbank/verwaltete Instanz. Der Data Factory-Dienst verwendet diese Informationen zum Vorbereiten und Verwalten der SSISDB in Ihrem Namen. |
| CatalogAdminPassword | Das Kennwort des Administrators Ihres vorhandenen Servers für die Azure SQL-Datenbank/verwaltete Instanz. |
| CatalogPricingTier | Der Tarif für die SSISDB, die von Ihrem vorhandenen Azure SQL-Datenbank-Server gehostet wird.  Gilt nicht für die verwaltete Azure SQL-Datenbank-Instanz, die die SSISDB hostet. |
| VNetId | Die Ressourcen-ID des virtuellen Netzwerks zum Beitreten Ihrer Azure-SSIS-Integrationslaufzeit. |
| Subnetz | Der Subnetzname zum Beitreten Ihrer Azure-SSIS-Integrationslaufzeit. |
| ID | Die Ressourcen-ID Ihrer Azure-SSIS-Integrationslaufzeit. |
| Type | Der Typ (verwaltet/selbstgehostet) Ihrer Azure-SSIS-Integrationslaufzeit. |
| ResourceGroupName | Der Name Ihrer Azure-Ressourcengruppe, in dem die Data Factory und Azure-SSIS-Integrationslaufzeit erstellt wurden. |
| DataFactoryName | Der Name Ihrer Azure Data Factory. |
| NAME | Der Name Ihrer Azure-SSIS-Integrationslaufzeit. |
| BESCHREIBUNG | Die Beschreibung Ihrer Azure-SSIS-Integrationslaufzeit. |

  
### <a name="status-per-node"></a>Status (pro Knoten)

| Status | BESCHREIBUNG |
| ------ | ----------- | 
| Wird gestartet | Dieser Knoten wird vorbereitet. |
| Verfügbar | Dieser Knoten ist bereit für das Bereitstellen/Ausführen von SSIS-Paketen. |
| Wird wiederverwendet | Dieser Knoten wird repariert/neu gestartet. |
| Nicht verfügbar | Dieser Knoten ist nicht bereit zum Bereitstellen/Ausführen Ihrer SSIS-Pakete und weist handlungsrelevante Fehler/Probleme auf, die Sie beheben können. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Status (allgemeine Azure-SSIS-Integrationslaufzeit)

| Gesamtstatus | BESCHREIBUNG | 
| -------------- | ----------- | 
| Initial | Die Knoten Ihrer Azure-SSIS-Integrationslaufzeit wurden nicht zugeordnet/vorbereitet. | 
| Wird gestartet | Die Knoten Ihrer Azure-SSIS-Integrationslaufzeit werden zugeordnet/vorbereitet und die Abrechnung wurde gestartet. |
| Gestartet | Die Knoten Ihrer Azure-SSIS-Integrationslaufzeit wurden zugeordnet/vorbereitet und sie sind bereit für die Bereitstellung/Ausführung der SSIS-Pakete. |
| Wird beendet  | Die Knoten Ihrer Azure-SSIS-Integrationslaufzeit werden veröffentlicht. |
| Beendet | Die Knoten Ihrer Azure-SSIS-Integrationslaufzeit wurden veröffentlicht und die Abrechnung wurde beendet. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>Überwachen der Azure-SSIS Integration Runtime im Azure-Portal

Die folgenden Screenshots zeigen die Auswahl der zu überwachenden Azure-SSIS IR und zeigen ein Beispiel für die angezeigten Informationen.

![Wählen Sie die zu überwachende Azure-SSIS Integration Runtime aus.](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Rufen Sie Informationen zur Azure-SSIS Integration Runtime ab.](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>Überwachen Sie die Azure-SSIS Integration Runtime mit PowerShell.

Verwenden Sie ein ähnliches Skript wie im folgenden Beispiel zum Überprüfen des Status der Azure-SSIS IR.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Weitere Informationen über Azure-SSIS Integration Runtime

Weitere Informationen zur Azure-SSIS-Integrationslaufzeit finden Sie in den folgenden Artikeln:

- [Azure-SSIS-Integrationslaufzeit](concepts-integration-runtime.md#azure-ssis-integration-runtime): Dieser Artikel enthält konzeptionelle Informationen zu Integrationslaufzeiten, die im Allgemeinen die Azure-SSIS-Integrationslaufzeit einschließen. 
- [Tutorial: Bereitstellen von SSIS-Paketen in Azure](tutorial-create-azure-ssis-runtime-portal.md): Dieser Artikel enthält schrittweise Anweisungen zum Erstellen einer Azure-SSIS-Integrationslaufzeit und verwendet eine Azure SQL-Datenbank zum Hosten des SSIS-Katalogs. 
- [Erstellen der Azure-SSIS Integration Runtime in Azure Data Factory](create-azure-ssis-integration-runtime.md): In diesem Artikel wird das Tutorial vertieft, und er enthält Anweisungen zur Verwendung einer verwalteten Azure SQL-Datenbank-Instanz und zum Verknüpfen der IR mit einem virtuellen Netzwerk. 
- [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md): In diesem Artikel wird beschrieben, wie Sie eine Azure-SSIS-Integrationslaufzeit beenden, starten oder entfernen. Es wird zudem gezeigt, wie Sie Ihre Azure-SSIS-Integrationslaufzeit horizontal hochskalieren, indem Sie der Integrationslaufzeit weitere Knoten hinzufügen. 
- [Verknüpfen einer Azure-SSIS-Integration Runtime mit einem virtuellen Netzwerk:](join-azure-ssis-integration-runtime-virtual-network.md). Dieser Artikel enthält grundlegende Informationen zum Verknüpfen einer Azure-SSIS-Integrationslaufzeit mit einem Azure Virtual Network. Darüber hinaus enthält er Schritte zur Verwendung des Azure-Portals zum Konfigurieren des virtuellen Netzwerks, damit die Azure-SSIS-Integrationslaufzeit dem virtuellen Netzwerk beitreten kann. 

## <a name="next-steps"></a>Nächste Schritte
Informationen zum Überwachen von Pipelines auf unterschiedliche Weise finden Sie in den folgenden Artikeln: 

- [Schnellstart: Erstellen einer Data Factory](quickstart-create-data-factory-dot-net.md)
- [Verwenden von Azure Monitor zum Überwachen von Data Factory-Pipelines](monitor-using-azure-monitor.md)
