---
title: Überwachen einer Integration Runtime in Azure Data Factory
description: Erfahren Sie, wie verschiedene Arten der Integrationslaufzeit in Azure Data Factory überwacht werden.
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/11/2020
author: dcstwh
ms.author: weetok
ms.openlocfilehash: a52fad39e19bdf2edf110990c8f0e392ec5803ce
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377498"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Überwachen einer Integrationslaufzeit in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]
  
Bei der **Integrationslaufzeit** (Integration Runtime, IR) handelt es sich um die Computeinfrastruktur, mit der Azure Data Factory (ADF) mehrere Datenintegrationsfunktionen übergreifend für verschiedene Netzwerkumgebungen bereitstellt. Es werden drei Arten von Integrationslaufzeiten von Azure Data Factory angeboten:

- Azure-Integrationslaufzeit
- Selbstgehostete Integrationslaufzeit
- Azure-SQL Server Integration Services (SSIS) Integration Runtime

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
| Name | Name der Azure-Integrationslaufzeit. |  
| State | Status der Azure-Integrationslaufzeit. | 
| Standort | Standort der Azure-Integrationslaufzeit. Weitere Informationen zum Standort einer Azure-Integrationslaufzeit finden Sie unter [Einführung in die Integrationslaufzeit](concepts-integration-runtime.md). |
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
| Name | Name der selbstgehosteten Integrationslaufzeit und die ihr zugeordneten Knoten. Der Knoten ist ein lokaler Windows-Computer, auf dem sich die selbstgehostete Integrationslaufzeit befindet. |  
| Status | Der Status der allgemeinen selbstgehosteten Integrationslaufzeit und der einzelnen Knoten. Beispiel: Online/Offline/Eingeschränkt usw. Informationen zu diesen Status finden Sie im nächsten Abschnitt. | 
| Version | Die Version der selbstgehosteten Integrationslaufzeit und der einzelnen Knoten. Die Version der selbstgehosteten Integrationslaufzeit wird basierend auf der Version bestimmt, die die meisten Knoten der Gruppe aufweisen. Wenn Knoten mit unterschiedlichen Versionen am Setup der selbstgehosteten Integrationslaufzeit beteiligt sind, funktionieren nur die Knoten mit der gleichen Versionsnummer wie bei der selbstgehosteten Integrationslaufzeit richtig. Andere Knoten befinden sich im eingeschränkten Modus und müssen manuell aktualisiert werden (nur für den Fall, dass die automatische Aktualisierung fehlschlägt). | 
| Verfügbarer Arbeitsspeicher | Verfügbarer Arbeitsspeicher auf einem Knoten der selbstgehosteten Integrationslaufzeit. Dieser Wert steht für eine Momentaufnahme nahezu in Echtzeit. | 
| CPU-Auslastung | CPU-Auslastung für einen Knoten der selbstgehosteten Integrationslaufzeit. Dieser Wert steht für eine Momentaufnahme nahezu in Echtzeit. |
| Netzwerk (Eingang/Ausgang) | Netzwerkauslastung für einen Knoten der selbstgehosteten Integrationslaufzeit. Dieser Wert steht für eine Momentaufnahme nahezu in Echtzeit. | 
| Gleichzeitige Aufträge (ausgeführt/Limit) | **Wird ausgeführt**. Anzahl von Aufträgen oder Aufgaben, die auf den einzelnen Knoten ausgeführt werden. Dieser Wert steht für eine Momentaufnahme nahezu in Echtzeit. <br/><br/>**Limit**. Mit „Limit“ wird angegeben, wie viele Aufträge für einen Knoten jeweils gleichzeitig ausgeführt werden können. Dieser Wert wird basierend auf der Größe des Computers definiert. Wenn Zeitüberschreitungen für Aktivitäten auftreten, können Sie das Limit auch dann erhöhen, um die gleichzeitige Ausführung von Aufträgen in erweiterten Szenarien hochzuskalieren, wenn CPU, Arbeitsspeicher oder Netzwerk nicht voll ausgelastet sind. Diese Funktion ist auch für eine selbstgehostete Integrationslaufzeit mit einem einzelnen Knoten verfügbar. |
| Role | Bei einer selbstgehosteten Integrationslaufzeit mit mehreren Knoten gibt es zwei Arten von Rollen: Verteiler und Worker. Alle Knoten sind Worker. Dies bedeutet, dass alle Knoten zum Ausführen von Aufträgen verwendet werden können. Es ist nur ein Verteilerknoten vorhanden, der zum Durchführen der Pullvorgänge für Aufgaben bzw. Aufträge von Clouddiensten und Verteilen an die einzelnen Workerknoten genutzt wird. Der Verteilerknoten ist auch ein Workerknoten. |

Einige Einstellungen der Eigenschaften sind sinnvoller, wenn in der selbstgehosteten Integrationslaufzeit (d.h. in einem Szenario mit Aufskalieren) mindestens zwei Knoten enthalten sind.

#### <a name="concurrent-jobs-limit"></a>Limit für gleichzeitige Aufträge

Der Standardwert des Limits für gleichzeitige Aufträge basierend auf der Größe des Computers. Welche Faktoren zum Berechnen dieses Wertes verwendet werden, hängt von der Größe des Arbeitsspeichers und der Anzahl der CPU-Kerne des Computers ab. Je mehr Kerne und Arbeitsspeicher, desto höher das Standardlimit für gleichzeitige Aufträge.

Sie skalieren auf, indem Sie die Anzahl der Knoten erhöhen. Wenn Sie die Anzahl von Knoten erhöhen, entspricht das Limit für gleichzeitige Aufträge der Summe der Grenzwerte für gleichzeitige Aufträge aller verfügbaren Knoten.  Wenn Sie z.B. auf einem Knoten maximal zwölf gleichzeitige Aufträge ausführen können und drei ähnliche Knoten hinzufügen, können Sie maximal 48 (d.h. 4 x 12) gleichzeitige Aufträge ausführen. Sie sollten das Limit für gleichzeitige Aufträge nur dann erhöhen, wenn Sie mit den Standardwerten auf jedem Knoten eine geringe Ressourcennutzung feststellen.

Sie können den berechneten Standardwert im Azure-Portal überschreiben. Wählen Sie „Autor > Verbindungen > Integration Runtimes > Bearbeiten > Knoten > Wert für gleichzeitige Aufträge pro Knoten ändern“ aus. Sie können auch den PowerShell-Befehl [update-Azdatafactoryv2integrationruntimenode](/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) verwenden.
  
### <a name="status-per-node"></a>Status (pro Knoten)

Die folgende Tabelle enthält die möglichen Statuswerte eines Knotens einer selbstgehosteten Integrationslaufzeit:

| Status | BESCHREIBUNG |
| ------ | ------------------ | 
| Online | Der Knoten ist mit dem Data Factory-Dienst verbunden. |
| Offline | Knoten ist offline. |
| Wird aktualisiert | Der Knoten wird automatisch aktualisiert. |
| Eingeschränkt | Es besteht ein Konnektivitätsproblem. Dies kann auf ein Problem mit HTTP-Port 8060, mit der Service Bus-Konnektivität oder mit der Synchronisierung von Anmeldeinformationen zurückzuführen sein. |
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
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
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

Azure-SSIS IR ist ein vollständig verwalteter Cluster mit virtuellen Azure-Computern (VMs oder Knoten), die speziell für die Ausführung Ihrer SSIS-Pakete bestimmt sind. Es gibt verschiedene Möglichkeiten, die SSIS-Paketausführung unter Azure-SSIS IR aufzurufen, beispielsweise über für Azure aktivierte SQL Server Data Tools (SSDT), das Befehlszeilenprogramm AzureDTExec, T-SQL in SQL Server Management Studio (SSMS)/SQL Server-Agent und Aktivitäten zum Ausführen von SSIS-Paketen in ADF-Pipelines. Azure-SSIS IR führt keine anderen ADF-Aktivitäten aus. Nach der Bereitstellung können Sie die allgemeinen/knotenspezifischen Eigenschaften und Status über Azure PowerShell, Azure-Portal und Azure Monitor überwachen.

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-powershell"></a>Überwachen der Azure-SSIS Integration Runtime mit Azure PowerShell

Verwenden Sie das folgende Azure PowerShell-Cmdlet zum Überwachen der allgemeinen/knotenspezifischen Eigenschaften und Status von Azure-SSIS IR.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

#### <a name="properties"></a>Eigenschaften

Die folgende Tabelle enthält Beschreibungen für Eigenschaften, die vom erwähnten Cmdlet für Azure-SSIS IR zurückgegeben werden:

| Eigenschaft/Status              | BESCHREIBUNG                  |
| ---------------------------- | ---------------------------- |
| CreateTime                   | Die UTC-Zeit für die Erstellung Ihrer Azure-SSIS IR. |
| Nodes                        | Die zugeordneten/verfügbaren Knoten Ihrer Azure-SSIS IR mit knotenabhängigen Status (wird gestartet/verfügbar/wird wiederverwendet/nicht verfügbar) und handlungsrelevanten Fehlern. |
| OtherErrors                  | Die nicht knotenabhängigen handlungsrelevanten Fehler für Ihre Azure-SSIS IR. |
| LastOperation                | Das Ergebnis des letzten Vorgangs zum Starten/Beenden für Ihre Azure-SSIS IR mit handlungsrelevanten Fehlern, wenn ein Fehler aufgetreten ist. |
| State                        | Der allgemeine Status (Initial/Wird gestartet/Gestartet/Wird beendet/Beendet) Ihrer Azure-SSIS IR. |
| Standort                     | Der Standort Ihrer Azure-SSIS IR. |
| NodeSize                     | Die Größe der einzelnen Knoten in Ihrer Azure-SSIS IR. |
| NodeCount                    | Die Anzahl der Knoten in Ihrer Azure-SSIS IR. |
| MaxParallelExecutionsPerNode | Die maximale Anzahl gleichzeitiger Ausführungen pro Knoten in Ihrer Azure-SSIS IR. |
| CatalogServerEndpoint        | Der Endpunkt Ihres vorhandenen Azure SQL-Datenbank-Servers oder Ihrer verwalteten Instanz zum Hosten des SSIS-Katalogs (SSISDB). |
| CatalogAdminUserName         | Der Benutzername des Administrators Ihres vorhandenen Azure SQL-Datenbank-Servers oder Ihrer verwalteten Instanz. ADF verwendet diese Informationen zum Vorbereiten und Verwalten der SSISDB in Ihrem Namen. |
| CatalogAdminPassword         | Das Kennwort des Administrators Ihres vorhandenen Azure SQL-Datenbank-Servers oder Ihrer verwalteten Instanz. |
| CatalogPricingTier           | Der Tarif für die SSISDB, die vom Azure SQL-Datenbank-Server gehostet wird.  Gilt nicht für die verwaltete Azure SQL-Instanz, die die SSISDB hostet. |
| VNetId                       | Die Ressourcen-ID des virtuellen Netzwerks zum Beitreten Ihrer Azure-SSIS IR. |
| Subnet                       | Der Subnetzname zum Beitreten Ihrer Azure-SSIS IR. |
| id                           | Die Ressourcen-ID Ihrer Azure-SSIS IR. |
| type                         | Der IR-Typ (verwaltet/selbstgehostet) Ihrer Azure-SSIS IR. |
| ResourceGroupName            | Der Name Ihrer Azure-Ressourcengruppe, in der Ihre ADF und Azure-SSIS IR erstellt wurden. |
| DataFactoryName              | Name Ihrer ADF-Instanz. |
| Name                         | Name Ihrer Azure-SSIS IR. |
| BESCHREIBUNG                  | Beschreibung Ihrer Azure-SSIS IR. |
  
#### <a name="status-per-azure-ssis-ir-node"></a>Status (pro Azure-SSIS IR-Knoten)

Die folgende Tabelle enthält die möglichen Status eines Azure-SSIS IR-Knotens:

| Knotenspezifischer Status | BESCHREIBUNG |
| -------------------- | ----------- | 
| Wird gestartet             | Dieser Knoten wird vorbereitet. |
| Verfügbar            | Dieser Knoten ist bereit für das Bereitstellen/Ausführen von SSIS-Paketen. |
| Wird wiederverwendet            | Dieser Knoten wird repariert/neu gestartet. |
| Nicht verfügbar          | Dieser Knoten ist nicht bereit zum Bereitstellen/Ausführen Ihrer SSIS-Pakete und weist handlungsrelevante Fehler/Probleme auf, die Sie beheben können. |

#### <a name="status-overall-azure-ssis-ir"></a>Status (Azure-SSIS IR insgesamt)

Die folgende Tabelle enthält die möglichen allgemeinen Status einer Azure-SSIS IR: Der allgemeine Status hängt wiederum von den kombinierten Status aller Knoten ab, die zur Azure-SSIS IR gehören. 

| Gesamtstatus | BESCHREIBUNG | 
| -------------- | ----------- | 
| Initial        | Die Knoten Ihrer Azure-SSIS IR wurden nicht zugeordnet/vorbereitet. | 
| Wird gestartet       | Die Knoten Ihrer Azure-SSIS IR werden zugeordnet/vorbereitet und die Abrechnung wurde gestartet. |
| Gestartet        | Die Knoten Ihrer Azure-SSIS IR wurden zugeordnet/vorbereitet und sie sind bereit für die Bereitstellung/Ausführung der SSIS-Pakete. |
| Wird beendet       | Die Knoten Ihrer Azure-SSIS IR werden freigegeben. |
| Beendet        | Die Knoten Ihrer Azure-SSIS IR wurden freigegeben und die Abrechnung wurde beendet. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-azure-portal"></a>Überwachen der Azure-SSIS Integration Runtime im Azure-Portal

Zur Überwachung Ihrer Azure-SSIS IR im Azure-Portal wechseln Sie in der ADF-Benutzeroberfläche zur Seite **Integration Runtimes** des **Überwachungshubs**. Hier werden alle Integration Runtimes angezeigt.

![Überwachen aller Integration Runtimes](media/monitor-integration-runtime/monitor-integration-runtimes.png)

Wählen Sie als Nächstes den Namen Ihrer Azure-SSIS IR aus, um die Überwachungsseite zu öffnen. Hier werden alle allgemeinen/knotenspezifischen Eigenschaften und Status angezeigt. Auf dieser Seite finden Sie verschiedene Informations-/Funktionskacheln abhängig von Ihrer Konfiguration der allgemeinen und erweiterten Einstellungen sowie der Bereitstellungseinstellungen für Ihre Azure-SSIS IR.  Die Informationskacheln **TYP** und **REGION** zeigen den Typ bzw. die Region Ihrer Azure-SSIS IR an. Die Informationskachel **KNOTENGRÖSSE** zeigt die SKU (SSIS edition_VM tier_VM-Reihe), die Anzahl der CPU-Kerne und die Größe des RAM pro Knoten für Ihre Azure-SSIS IR an. Die Informationskachel **AUSGEFÜHRTE/ANGEFORDERTE KNOTEN** vergleicht die Anzahl der aktuell ausgeführten Knoten mit der Gesamtzahl der zuvor für Ihre Azure-SSIS IR angeforderten Knoten. Die Funktionskacheln werden im Folgenden näher beschrieben.

![Überwachen Ihrer Azure-SSIS IR](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime.png)

#### <a name="status-tile"></a>Kachel „STATUS“

Auf der Kachel **STATUS** Ihrer Azure-SSIS IR-Überwachungsseite wird der allgemeine Status angezeigt, z. B. **Wird ausgeführt** oder **Beendet**. Wenn Sie den Status **Wird ausgeführt** auswählen, wird ein Fenster mit einer aktiven Schaltfläche zum **Beenden** angezeigt, mit der Sie Ihre Azure-SSIS IR beenden können. Wenn Sie den Status **Beendet** auswählen, wird ein Fenster mit einer aktiven Schaltfläche zum **Starten** angezeigt, mit der Sie Ihre Azure-SSIS IR starten können. Das Popupfenster verfügt auch über eine Schaltfläche **SSIS-Paket ausführen** zur automatischen Generierung einer ADF-Pipeline mit der Aktivität „SSIS-Paket ausführen“, die auf Ihrer Azure-SSIS IR ausgeführt wird (siehe [Ausführen eines SSIS-Pakets als Aktivität „SSIS-Paket ausführen“ in ADF-Pipelines](./how-to-invoke-ssis-package-ssis-activity.md)) und ein Textfeld **Ressourcen-ID**, aus dem Sie die Ressourcen-ID Ihrer Azure-SSIS IR kopieren können (`/subscriptions/YourAzureSubscripton/resourcegroups/YourResourceGroup/providers/Microsoft.DataFactory/factories/YourADF/integrationruntimes/YourAzureSSISIR`). Das Suffix der Ressourcen-ID Ihrer Azure-SSIS IR, das Ihre ADF- und Azure-SSIS IR-Namen enthält, bildet eine Cluster-ID, die verwendet werden kann, um zusätzliche kostenpflichtige/lizenzierte SSIS-Komponenten von unabhängigen Softwareanbietern (ISVs) zu erwerben und sie an Ihre Azure-SSIS IR zu binden (siehe [Installieren kostenpflichtiger oder lizenzierter benutzerdefinierter Komponenten für Azure SSIS Integration Runtime](./how-to-develop-azure-ssis-ir-licensed-components.md)).

![Überwachen Ihrer Azure-SSIS IR – Kachel „STATUS“](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-status.png)

#### <a name="ssisdb-server-endpoint-tile"></a>Kachel „SSISDB-SERVERENDPUNKT“

Wenn Sie Ihr Projektbereitstellungsmodell dort verwenden, wo Ihre Pakete in der von Ihrem Azure SQL-Datenbank-Server oder der verwalteten Instanz gehosteten SSISDB gespeichert sind, wird die Kachel **SSISDB-SERVERENDPUNKT** auf der Azure-SSIS IR-Überwachungsseite angezeigt (Informationen hierzu finden Sie unter [Konfigurieren der Azure-SSIS IR-Bereitstellungseinstellungen](./tutorial-deploy-ssis-packages-azure.md#deployment-settings-page)). Auf dieser Kachel können Sie einen Link zu Ihrem Azure SQL-Datenbank-Server oder zur verwalteten Instanz auswählen, um ein Fenster aufzurufen, in dem Sie über ein Textfeld den Serverendpunkt kopieren und beim Herstellen einer Verbindung über SSMS verwenden können, um Ihre Pakete bereitzustellen, zu konfigurieren, auszuführen und zu verwalten. Im Popupfenster können Sie auch den Link zum **Anzeigen der Einstellungen der Azure SQL-Datenbank oder der verwalteten Instanz** auswählen, um Ihre SSISDB im Azure-Portal neu zu konfigurieren oder deren Größe zu ändern.

![Überwachen Ihrer Azure-SSIS IR – Kachel „SSISDB“](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-ssisdb.png)

#### <a name="proxy--staging-tile"></a>Kachel „PROXY/STAGING“

Wenn Sie eine selbstgehostete IR (SHIR) als Proxy für Ihr Azure-SSIS IR herunterladen, installieren und konfigurieren, um auf lokale Daten zuzugreifen, wird die Kachel **PROXY/STAGING** auf Ihrer Azure-SSIS IR-Überwachungsseite angezeigt (siehe [Konfigurieren einer selbstgehosteten IR als Proxy für eine Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md)). Auf dieser Kachel können Sie einen Link auswählen, der Ihre SHIR angibt, deren Überwachungsseite Sie öffnen möchten. Sie können auch einen anderen Link auswählen, der Ihre Azure Blob Storage-Instanz für das Staging bestimmt, um den verknüpften Dienst neu zu konfigurieren.

#### <a name="validate-vnet--subnet-tile"></a>Kachel „VNET/SUBNETZ ÜBERPRÜFEN“

Wenn Sie Ihre Azure-SSIS IR mit einem VNet verknüpfen, wird die Kachel **VNET/SUBNETZ ÜBERPRÜFEN** auf der Azure-SSIS IR-Überwachungsseite angezeigt (Informationen hierzu finden Sie unter [Verknüpfen der Azure-SSIS IR mit einem VNet](./join-azure-ssis-integration-runtime-virtual-network.md)). Auf dieser Kachel können Sie einen Link zu Ihrem VNet und Subnetz auswählen, um ein Fenster aufzurufen, in dem Sie über Textfelder die Ressourcen-ID Ihres VNets (`/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworks/YourARMVNet`) und den Subnetznamen kopieren können. Zudem können Sie die Konfigurationen von VNet und Subnetz überprüfen, um sicherzustellen, dass der erforderliche eingehende/ausgehende Netzwerkdatenverkehr und die Verwaltung der Azure-SSIS IR nicht behindert werden.

![Überwachen Ihrer Azure-SSIS IR – Kachel „ÜBERPRÜFEN“](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-validate.png)

#### <a name="diagnose-connectivity-tile"></a>Kachel „DIAGNOSE DER KONNEKTIVITÄT“

Auf der Kachel **DIAGNOSE DER KONNEKTIVITÄT** der Azure-SSIS IR-Überwachungsseite können Sie den Link **Verbindung testen** auswählen, um ein Fenster aufzurufen, in dem Sie die Verbindungen zwischen der Azure-SSIS IR und entsprechenden Paket-/Konfigurations-/Datenspeichern sowie Verwaltungsdiensten anhand des vollqualifizierter Domänennamens (Fully Qualified Domain Name, FQDN) oder der IP-Adresse und dem jeweiligen Port prüfen können (Informationen hierzu finden Sie unter [Testen der Verbindungen von der Azure-SSIS IR](./ssis-integration-runtime-diagnose-connectivity-faq.md)).

![Screenshot der Seite, auf der Sie die Verbindungen zwischen Azure-SSIS IR und relevanten Paketen/Konfigurationen/Datenspeichern testen können](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-diagnose.png)

#### <a name="static-public-ip-addresses-tile"></a>Kachel „STATISCHE ÖFFENTLICHE IP-ADRESSEN“

Wenn Sie Ihre eigenen statischen öffentlichen IP-Adressen für Azure-SSIS IR verwenden, wird die Kachel **STATISCHE ÖFFENTLICHE IP-ADRESSEN** auf Ihrer Azure-SSIS IR-Überwachungsseite angezeigt (siehe [Nutzung der eigenen statischen öffentlichen IP-Adressen für Azure-SSIS IR](./join-azure-ssis-integration-runtime-virtual-network.md#publicIP)). Auf dieser Kachel können Sie Links auswählen, die Ihre erste/zweite statische öffentliche IP-Adresse für Azure-SSIS IR bezeichnen, um ein Fenster zu öffnen, in dem Sie deren Ressourcen-ID (`/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/publicIPAddresses/YourPublicIPAddress`) aus einem Textfeld kopieren können. Im Popupfenster können Sie auch den Link  **für Ihre erste/zweite statische öffentliche IP-Adresse anzeigen** auswählen, um Ihre erste/zweite statische öffentliche IP-Adresse im Azure-Portal zu verwalten.

![Screenshot der Seite, auf der Sie Ihre erste/zweite statische öffentliche IP-Adresse festlegen können](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-static.png)

#### <a name="package-stores-tile"></a>Kachel „PAKETSPEICHER“

Wenn Sie Ihr Paketbereitstellungsmodell dort verwenden, wo Ihre Pakete in der Dateisystem-/Azure Files-/SQL Server-Datenbankinstanz (MSDB) gespeichert werden, die von Azure SQL Managed Instance gehostet und über Azure-SSIS IR-Paketspeicher verwaltet wird, wird die Kachel **PAKETSPEICHER** auf der Azure-SSIS IR-Überwachungsseite angezeigt (Informationen hierzu finden Sie unter [Konfigurieren der Azure-SSIS IR-Bereitstellungseinstellungen](./tutorial-deploy-ssis-packages-azure.md#deployment-settings-page)). Auf dieser Kachel können Sie einen Link auswählen, der die Anzahl der mit der Azure-SSIS IR verbundenen Paketspeicher angibt, um ein Fenster aufzurufen, in dem Sie die jeweils verknüpften Dienste für Ihre Azure-SSIS IR-Paketspeicher zusätzlich zu den von Azure SQL Managed Instance gehosteten Dateisystem-/Azure Files-/MSDB-Instanzen neu konfigurieren können.

![Überwachen Ihrer Azure-SSIS IR – Kachel „PAKET“](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-package.png)

#### <a name="errors-tile"></a>Kachel „FEHLER“

Wenn beim Starten/Beenden/Warten/Upgraden der Azure-SSIS IR Probleme auftreten, wird auf der Azure-SSIS IR-Überwachungsseite zusätzlich die Kachel **FEHLER** angezeigt. Auf dieser Kachel können Sie einen Link auswählen, der die Anzahl der von der Azure-SSIS IR generierten Fehler angibt, um ein Fenster aufzurufen, in dem diese Fehler mit mehr Details angezeigt werden. Diese können Sie kopieren, um in unserem Leitfaden zur Problembehandlung (siehe [Problembehandlung bei der Azure-SSIS IR](./ssis-integration-runtime-management-troubleshoot.md)) nach der empfohlenen Lösung zu suchen.

![Überwachen Ihrer Azure-SSIS IR – Kachel „DIAGNOSE“](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-error.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-monitor"></a>Überwachen der Azure-SSIS Integration Runtime mit Azure Monitor

Informationen zum Überwachen der Azure-SSIS IR mit Azure Monitor finden Sie unter [Überwachen von SSIS-Vorgängen mit Azure Monitor](./monitor-using-azure-monitor.md#monitor-ssis-operations-with-azure-monitor).

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Weitere Informationen über Azure-SSIS Integration Runtime

Weitere Informationen zur Azure-SSIS-Integrationslaufzeit finden Sie in den folgenden Artikeln:

- [Azure-SSIS-Integrationslaufzeit](concepts-integration-runtime.md#azure-ssis-integration-runtime): Dieser Artikel enthält allgemeine konzeptionelle Informationen zu Integration Runtimes, einschließlich der Azure SSIS-IR. 
- [Tutorial: Bereitstellen von SSIS-Paketen in Azure](./tutorial-deploy-ssis-packages-azure.md): Dieser Artikel enthält schrittweise Anleitungen zum Erstellen Ihrer Azure-SSIS IR, und darin wird eine Azure SQL-Datenbank zum Hosten des SSIS-Katalogs (SSISDB) verwendet. 
- [Vorgehensweise: Azure-SSIS Integration Runtime in Azure Data Factory](create-azure-ssis-integration-runtime.md): Dieser Artikel baut auf dem Tutorial auf und enthält Anweisungen zur Verwendung von Azure SQL Managed Instance zum Hosten von SSISDB. 
- [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md): In diesem Artikel wird beschrieben, wie Sie die Azure-SSIS IR starten, beenden oder löschen. Es wird zudem gezeigt, wie Sie sie horizontal hochskalieren, indem Sie weitere Knoten hinzufügen. 
- [Verknüpfen einer Azure-SSIS-Integration Runtime mit einem virtuellen Netzwerk:](join-azure-ssis-integration-runtime-virtual-network.md). Dieser Artikel enthält Anweisungen zum Hinzufügen Ihrer Azure-SSIS IR zu einem virtuellen Netzwerk.

## <a name="next-steps"></a>Nächste Schritte
Informationen zum Überwachen von Pipelines auf unterschiedliche Weise finden Sie in den folgenden Artikeln: 

- [Schnellstart: Erstellen einer Data Factory](quickstart-create-data-factory-dot-net.md)
- [Verwenden von Azure Monitor zum Überwachen von Data Factory-Pipelines](monitor-using-azure-monitor.md)
