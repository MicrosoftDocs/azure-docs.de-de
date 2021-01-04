---
title: Diagnoseprotokolle
titleSuffix: Azure Content Delivery Network
description: Hier erfahren Sie, wie Sie Azure-Diagnoseprotokolle verwenden, um wichtige Analysen zu speichern, sodass Sie Nutzungsmetriken aus Ihrem Azure Content Delivery Network-Endpunkt exportieren können.
services: cdn
author: asudbring
manager: KumudD
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: allensu
ms.openlocfilehash: fdb609a243656e2c75159cd2d4e70e2f965ae896
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352115"
---
# <a name="diagnostic-logs---azure-content-delivery-network"></a>Diagnoseprotokolle: Azure Content Delivery Network

Mit Azure-Diagnoseprotokollen können Sie Daten der Basisanalyse anzeigen und diese an mindestens einem Ziel speichern, z. B.:

* Azure-Speicherkonto
* Log Analytics-Arbeitsbereich
* Azure Event Hubs

Dieses Feature ist für CDN-Endpunkte für alle Tarife verfügbar. 

Mit Diagnoseprotokollen können Sie Metriken zur grundlegenden Nutzung von Ihrem CDN-Endpunkt auf verschiedene Quellkomponenten exportieren, um sie auf die gewünschte Weise zu verwenden. Sie können die folgenden Arten von Datenexport durchführen:

* Exportieren von Daten in Blobspeicher, Exportieren in eine CSV-Datei und Generieren von Diagrammen in Excel
* Exportieren von Daten nach Event Hubs und Korrelieren mit Daten von anderen Azure-Diensten
* Exportieren von Daten in Azure Monitor-Protokolle und Anzeigen von Daten im eigenen Log Analytics-Arbeitsbereich

Für die folgenden Schritte ist ein Azure CDN-Profil erforderlich. Lesen Sie [Erstellen eines Azure CDN-Profils und eines Endpunkts](cdn-create-new-endpoint.md), bevor Sie fortfahren.

## <a name="enable-logging-with-the-azure-portal"></a>Aktivieren der Protokollierung über das Azure-Portal

Befolgen Sie diese Schritte, um die Protokollierung für Ihren Azure CDN-Endpunkt zu aktivieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

2. Navigieren Sie im Azure-Portal zu **Alle Ressourcen** -> **Ihr-CDN-Profil**

2. Wählen Sie den CDN-Endpunkt aus, für den Sie die Diagnoseprotokolle aktivieren möchten:

    :::image type="content" source="./media/cdn-diagnostics-log/02_browse-to-diagnostics-logs.png" alt-text="Wählen Sie den CDN-Endpunkt aus." border="true":::

3. Wählen Sie im Abschnitt **Überwachung** die Option **Diagnoseprotokolle** aus:

    :::image type="content" source="./media/cdn-diagnostics-log/03_diagnostics-logs-options.png" alt-text="Wählen Sie Diagnoseprotokolle aus." border="true":::

### <a name="enable-logging-with-azure-storage"></a>Aktivieren der Protokollierung mit Azure Storage

Führen Sie die folgenden Schritte aus, um ein Speicherkonto zum Speichern der Protokolle zu verwenden:

 >[!NOTE] 
 >Zur Durchführung dieser Schritte ist ein Speicherkonto erforderlich. Weitere Informationen finden Sie unter: Weitere Informationen finden Sie unter **[Erstellen eines Azure Storage-Kontos](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)** .
    
1. Geben Sie für **Name der Diagnoseeinstellung** einen Namen für Ihre Diagnoseprotokolleinstellungen ein.
 
2. Wählen Sie **In einem Speicherkonto archivieren** und dann **CoreAnalytics**. 

3. Wählen Sie für **Beibehaltungsdauer (Tage)** die Anzahl der Aufbewahrungstage. Bei einer Aufbewahrung von 0 Tagen werden die Protokolle dauerhaft gespeichert. 

4. Wählen Sie das Abonnement und Speicherkonto für die Protokolle aus.

    :::image type="content" source="./media/cdn-diagnostics-log/04_diagnostics-logs-storage.png" alt-text="Diagnoseprotokolle – Speicher." border="true":::

3. Wählen Sie **Speichern** aus.

### <a name="send-to-log-analytics"></a>An Log Analytics senden

Führen Sie die folgenden Schritte aus, um Log Analytics für die Protokolle zu verwenden:

>[!NOTE] 
>Zum Ausführen dieser Schritte ist ein Log Analytics-Arbeitsbereich erforderlich. Weitere Informationen finden Sie unter: Weitere Informationen finden Sie unter **[Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../azure-monitor/learn/quick-create-workspace.md)** .
    
1. Geben Sie für **Name der Diagnoseeinstellung** einen Namen für Ihre Diagnoseprotokolleinstellungen ein.

2. Wählen Sie **An Log Analytics senden** und anschließend **CoreAnalytics** aus. 

3. Wählen Sie das Abonnement und den Log Analytics-Arbeitsbereich für die Protokolle aus.

   :::image type="content" source="./media/cdn-diagnostics-log/05-la-workspace.png" alt-text="Diagnoseprotokolle – Log Analytics." border="true":::

4. Wählen Sie **Speichern** aus.

### <a name="stream-to-an-event-hub"></a>An einen Event Hub streamen

Gehen Sie folgendermaßen vor, um einen Event Hub für die Protokolle zu verwenden:

>[!NOTE] 
>Zur Durchführung dieser Schritte ist ein Event Hub erforderlich. Weitere Informationen finden Sie unter: **[Schnellstart: Erstellen eines Event Hubs mithilfe des Azure-Portals](../event-hubs/event-hubs-create.md)** .
    
1. Geben Sie für **Name der Diagnoseeinstellung** einen Namen für Ihre Diagnoseprotokolleinstellungen ein.

2. Wählen Sie **An einen Event Hub streamen** und anschließend **CoreAnalytics** aus. 

3. Wählen Sie das Abonnement und den Event Hub-Namespace für die Protokolle aus.

   :::image type="content" source="./media/cdn-diagnostics-log/06-eventhub-namespace.png" alt-text="Diagnoseprotokolle – Event Hub." border="true":::

4. Wählen Sie **Speichern** aus.


## <a name="enable-logging-with-powershell"></a>Aktivieren der Protokollierung mit PowerShell

Das folgende Beispiel zeigt, wie Sie Diagnoseprotokolle über die Azure PowerShell-Cmdlets aktivieren.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Aktivieren von Diagnoseprotokollen in einem Speicherkonto

1. Melden Sie sich bei Azure PowerShell an:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Geben Sie die folgenden Befehle ein, um Diagnoseprotokolle in einem Speicherkonto zu aktivieren. Ersetzen Sie die Variablen durch Ihre Werte:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Categories CoreAnalytics
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Aktivieren von Diagnoseprotokollen für den Log Analytics-Arbeitsbereich

1. Melden Sie sich bei Azure PowerShell an:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Geben Sie diese Befehle ein, um Diagnoseprotokolle für einen Log Analytics-Arbeitsbereich zu aktivieren. Ersetzen Sie die Variablen durch Ihre Werte:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Categories CoreAnalytics
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Aktivieren von Diagnoseprotokollen für den Event Hub-Namespace

1. Melden Sie sich bei Azure PowerShell an:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Geben Sie diese Befehle ein, um Diagnoseprotokolle für einen Log Analytics-Arbeitsbereich zu aktivieren. Ersetzen Sie die Variablen durch Ihre Werte:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $eventhubname = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhubname -Enabled $true -Categories CoreAnalytics
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Nutzen von Diagnoseprotokollen aus Azure Storage
In diesem Abschnitt werden das Schema der CDN-Basisanalyse und die Organisation innerhalb eines Azure-Speicherkontos beschrieben, und er enthält Beispielcode zum Herunterladen der Protokolle in einer CSV-Datei.

### <a name="using-microsoft-azure-storage-explorer"></a>Verwenden des Microsoft Azure Storage-Explorers
Informationen zum Herunterladen des Tools finden Sie unter [Azure Storage-Explorer](https://storageexplorer.com/). Konfigurieren Sie nach dem Herunterladen und Installieren der Software die Verwendung desselben Azure-Speicherkontos, das als Ziel für die CDN-Diagnoseprotokolle konfiguriert wurde.

1.  Öffnen Sie den **Microsoft Azure Storage-Explorer**.
2.  Ermitteln Sie das Speicherkonto.
3.  Erweitern Sie den Knoten **Blobcontainer** unter diesem Speicherkonto.
4.  Wählen Sie den Container mit dem Namen *insights-logs-coreanalytics* aus.
5.  Im Bereich auf der rechten Seite werden Ergebnisse angezeigt. Es wird mit der ersten Ebene begonnen: *resourceId=* . Wählen Sie weiterhin jede Ebene aus, bis Sie die Datei *PT1H.json* gefunden haben. Eine Erläuterung des Pfads finden Sie unter [Blobpfadformat](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  Jede *PT1H.json*-Datei des Blobs steht für die Analyseprotokolle für eine Stunde eines bestimmten CDN-Endpunkts oder dessen benutzerdefinierter Domäne.
7.  Das Schema der Inhalte dieser JSON-Datei wird im Abschnitt „Schema der Basisanalyseprotokolle“ beschrieben.


#### <a name="blob-path-format"></a>Blobpfadformat

Basisanalyseprotokolle werden stündlich generiert. Die Daten werden gesammelt und als JSON-Nutzlast in einem einzelnen Azure-Blob gespeichert. Das Storage-Explorer-Tool interpretiert „/“ als Verzeichnistrennzeichen und zeigt die Hierarchie an. Der Pfad zum Azure-Blob wird angezeigt, als ob es eine hierarchische Struktur gebe, und stellt den Blobnamen dar. Für den Namen des Blobs wird die folgende Benennungskonvention verwendet:    

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Beschreibung der Felder:**

|Wert|BESCHREIBUNG|
|-------|---------|
|Abonnement-ID    |ID des Azure-Abonnements im GUID-Format|
|Ressourcengruppenname |Name der Ressourcengruppe, der die CDN-Ressourcen angehören|
|Profile Name (Profilname) |Name des CDN-Profils|
|Endpoint Name (Endpunktname) |Name des CDN-Endpunkts|
|Jahr|  Vierstellige Jahresangabe, z.B. 2017|
|Month (Monat)| Zweistellige Monatsangabe. 01 = Januar bis 12 = Dezember|
|Day (Tag)|   Zweistellige Tagesangabe|
|PT1H.json| JSON-Datei, in der die Analysedaten gespeichert sind|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Exportieren der Basisanalysedaten in eine CSV-Datei

Für den Zugriff auf Basisanalysen wird Beispielcode für ein Tool bereitgestellt. Mit diesem Tool ist das Herunterladen der JSON-Dateien in einem flachen Dateiformat mit Kommas als Trennzeichen möglich. Diese Datei kann dann zum Erstellen von Diagrammen oder anderen Aggregationen verwendet werden.

Sie können das Tool wie folgt verwenden:

1.  Nutzen Sie den GitHub-Link: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Laden Sie den Code herunter.
3.  Befolgen Sie die Anleitung zum Kompilieren und Konfigurieren.
4.  Führen Sie das Tool aus.
5.  In der sich ergebenden CSV-Datei werden die Analysedaten in einer einfachen flachen Hierarchie angezeigt.

## <a name="log-data-delays"></a>Protokolldatenverzögerungen

Die folgende Tabelle zeigt die Verzögerungen bei Protokolldaten für **Azure CDN Standard von Microsoft**, **Azure CDN Standard von Akamai** und **Azure CDN Standard/Premium von Verizon**.

Microsoft-Protokolldatenverzögerungen | Verizon-Protokolldatenverzögerungen | Akamai-Protokolldatenverzögerungen
--- | --- | ---
Um eine Stunde verzögert | Um eine Stunde verzögert, und es dauert bis zu zwei Stunden, bis die Daten nach Abschluss der Verteilung auf die Endpunkte angezeigt werden. | Um 24 Stunden verzögert. Wenn die Daten vor mehr als 24 Stunden erstellt wurden, dauert es bis zu zwei Stunden, bis sie angezeigt werden. Falls die Daten vor Kurzem erstellt wurden, kann es bis zu 25 Stunden dauern, bis die Protokolle angezeigt werden.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Diagnoseprotokolltypen für CDN-Basisanalyse

Microsoft bietet derzeit nur Basisanalyseprotokolle an, die Metriken mit HTTP-Antwortstatistiken und -Ausgangsstatistiken für die CDN-POPs/-Edges enthalten.

### <a name="core-analytics-metrics-details"></a>Details zu Basisanalysemetriken
Die folgende Tabelle enthält eine Liste mit den Metriken, die in den Basisanalyseprotokollen für Folgendes verfügbar sind:

* **Azure CDN Standard von Microsoft**
* **Azure CDN Standard von Akamai**
* **Azure CDN Standard/Premium von Verizon**

Nicht alle Metriken sind für alle Anbieter verfügbar, aber die Unterschiede sind nur minimal. In dieser Tabelle ist jeweils auch angegeben, ob eine Metrik von einem Anbieter verfügbar ist. Die Metriken stehen nur für die CDN-Endpunkte zur Verfügung, für die Datenverkehr anfällt.


|Metrik                     | BESCHREIBUNG | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Gesamtzahl von Anforderungstreffern während dieses Zeitraums | Ja | Ja |Ja |
| RequestCountHttpStatus2xx | Anzahl aller Anforderungen, die zum HTTP-Code 2xx geführt haben (z.B. 200, 202) | Ja | Ja |Ja |
| RequestCountHttpStatus3xx | Anzahl aller Anforderungen, die zum HTTP-Code 3xx geführt haben (z.B. 300, 302) | Ja | Ja |Ja |
| RequestCountHttpStatus4xx | Anzahl aller Anforderungen, die zum HTTP-Code 4xx geführt haben (z.B. 400, 404) | Ja | Ja |Ja |
| RequestCountHttpStatus5xx | Anzahl aller Anforderungen, die zum HTTP-Code 5xx geführt haben (z.B. 500, 504) | Ja | Ja |Ja |
| RequestCountHttpStatusOthers | Anzahl aller anderen HTTP-Codes (außerhalb des Bereichs von 2xx bis 5xx) | Ja | Ja |Ja |
| RequestCountHttpStatus200 | Anzahl aller Anforderungen, die zu einer Antwort mit dem HTTP-Code 200 geführt haben | Ja | Nein  |Ja |
| RequestCountHttpStatus206 | Anzahl aller Anforderungen, die zu einer Antwort mit dem HTTP-Code 206 geführt haben | Ja | Nein  |Ja |
| RequestCountHttpStatus302 | Anzahl aller Anforderungen, die zu einer Antwort mit dem HTTP-Code 302 geführt haben | Ja | Nein  |Ja |
| RequestCountHttpStatus304 | Anzahl aller Anforderungen, die zu einer Antwort mit dem HTTP-Code 304 geführt haben | Ja | Nein  |Ja |
| RequestCountHttpStatus404 | Anzahl aller Anforderungen, die zu einer Antwort mit dem HTTP-Code 404 geführt haben | Ja | Nein  |Ja |
| RequestCountCacheHit | Anzahl aller Anforderungen, die zu einem Cachetreffer geführt haben. Das Objekt wurde direkt vom POP für den Client bereitgestellt. | Ja | Ja | Nein  |
| RequestCountCacheMiss | Anzahl aller Anforderungen, die zu einem Cachefehler geführt haben. Ein Cachefehler bedeutet, dass das Objekt nicht auf dem POP gefunden wurde, der die geringste Entfernung zum Client aufweist, und vom Ursprungsserver abgerufen wurde. | Ja | Ja | Nein |
| RequestCountCacheNoCache | Anzahl aller Anforderungen eines Objekts, die aufgrund einer Benutzerkonfiguration im Edgebereich nicht zwischengespeichert wurden. | Ja | Ja | Nein |
| RequestCountCacheUncacheable | Anzahl aller Anforderungen an Objekte, für die durch die Header „Cache-Control“ und „Expires“ des Objekts das Zwischenspeichern verhindert wird. Diese Anzahl zeigt an, dass keine Zwischenspeicherung auf einem POP oder durch den HTTP-Client erfolgen soll. | Ja | Ja | Nein |
| RequestCountCacheOthers | Anzahl aller Anforderungen mit einem Cachestatus, der durch die obigen Metriken nicht abgedeckt ist | Nein | Ja | Nein  |
| EgressTotal | Datenübertragung in ausgehender Richtung in GB | Ja |Ja |Ja |
| EgressHttpStatus2xx | Datenübertragung in ausgehender Richtung* für Antworten mit dem HTTP-Statuscode 2xx in GB | Ja | Ja | Nein  |
| EgressHttpStatus3xx | Datenübertragung in ausgehender Richtung für Antworten mit dem HTTP-Statuscode 3xx in GB | Ja | Ja | Nein  |
| EgressHttpStatus4xx | Datenübertragung in ausgehender Richtung für Antworten mit dem HTTP-Statuscode 4xx in GB | Ja | Ja | Nein  |
| EgressHttpStatus5xx | Datenübertragung in ausgehender Richtung für Antworten mit dem HTTP-Statuscode 5xx in GB | Ja | Ja | Nein |
| EgressHttpStatusOthers | Datenübertragung in ausgehender Richtung für Antworten mit anderen HTTP-Statuscodes in GB | Ja | Ja | Nein  |
| EgressCacheHit | Datenübertragung in ausgehender Richtung für Antworten, die direkt aus dem CDN-Cache auf den CDN-POPs/-Edges bereitgestellt wurden | Ja | Ja | Nein |
| EgressCacheMiss | Datenübertragung in ausgehender Richtung für Antworten, die nicht auf dem nächstgelegenen POP-Server gefunden und daher vom Ursprungsserver abgerufen wurden | Ja | Ja | Nein |
| EgressCacheNoCache | Datenübertragung in ausgehender Richtung für Objekte, die aufgrund einer Benutzerkonfiguration im Edgebereich nicht zwischengespeichert wurden | Ja | Ja | Nein |
| EgressCacheUncacheable | Datenübertragung in ausgehender Richtung für Objekte, für die durch die Header „Cache-Control“ und/oder „Expires“ das Zwischenspeichern verhindert wird. Mit diesen Headern wird angegeben, dass keine Zwischenspeicherung auf einem POP oder durch den HTTP-Client erfolgen soll. | Ja | Ja | Nein |
| EgressCacheOthers | Datenübertragungen in ausgehender Richtung für andere Cacheszenarien | Nein | Ja | Nein |

*Datenübertragung in ausgehender Richtung bezieht sich hier auf Datenverkehr, der von CDN-POP-Servern auf dem Client bereitgestellt wird.


### <a name="schema-of-the-core-analytics-logs"></a>Schema der Basisanalyseprotokolle 

Alle Protokolle werden im JSON-Format gespeichert, und jeder Eintrag enthält Zeichenfolgenfelder gemäß dem folgenden Schema:

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Hierbei steht *time* für die Startzeit der Stundengrenze, für die die Statistik gemeldet wird. Eine von einem CDN-Anbieter nicht unterstützte Metrik führt anstelle eines double- oder integer-Werts zu einem NULL-Wert. Mit diesem NULL-Wert wird das Fehlen einer Metrik angegeben. Dies ist ein anderer Wert als der Wert 0. Auf dem Endpunkt ist pro Domäne ein Satz mit diesen Metriken konfiguriert.

Beispiele für Eigenschaften:

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Azure-Diagnoseprotokolle](../azure-monitor/platform/platform-logs-overview.md)
* [Analysieren von Azure CDN-Verwendungsmustern](./cdn-analyze-usage-patterns.md)
* [Azure Monitor-Protokolle](../azure-monitor/log-query/log-query-overview.md)
* [Azure Log Analytics-REST-API](/rest/api/loganalytics)