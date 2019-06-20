---
title: Archivieren von Azure-Diagnoseprotokollen
description: Hier erfahren Sie, wie Sie Ihre Azure-Diagnoseprotokolle zur langfristigen Aufbewahrung in einem Speicherkonto archivieren.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: bc1804e547bb1a29fc0dc680b948f1bb31af8307
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244920"
---
# <a name="archive-azure-diagnostic-logs"></a>Archivieren von Azure-Diagnoseprotokollen

In diesem Artikel erfahren Sie, wie Sie Ihre [Azure-Diagnoseprotokolle](diagnostic-logs-overview.md) über das Azure-Portal, mit PowerShell-Cmdlets, der CLI oder der REST-API in einem Speicherkonto archivieren. Diese Option ist hilfreich, wenn Sie Ihre Diagnoseprotokolle mit einer optionalen Aufbewahrungsrichtlinie zur Überwachung, statischen Analyse oder als Sicherungskopie aufbewahren möchten. Das Speicherkonto muss sich nicht unter demselben Abonnement befinden wie die Ressource, die Protokolle ausgibt, sofern der Benutzer, der die Einstellung konfiguriert, den entsprechenden RBAC-Zugriff auf beide Abonnements hat.

> [!WARNING]
> Das Format der Protokolldaten im Speicherkonto wird am 1. November 2018 in JSON Lines geändert. [Dieser Artikel enthält eine Beschreibung der Auswirkungen und der Aktualisierung Ihrer Tools zur Verarbeitung des neuen Formats.](./../../azure-monitor/platform/diagnostic-logs-append-blobs.md) 
>
> 

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, müssen Sie [ein Speicherkonto erstellen](../../storage/common/storage-quickstart-create-account.md), in dem Sie Ihre Diagnoseprotokolle archivieren können. Um den Zugriff auf Überwachungsdaten besser steuern zu können, wird dringend davon abgeraten, ein bereits vorhandenes Speicherkonto mit anderen, nicht überwachungsbezogenen Daten zu verwenden. Wenn Sie jedoch auch Ihr Aktivitätsprotokoll und Ihre Diagnosemetriken in einem Speicherkonto archivieren, ist es unter Umständen sinnvoll, dieses Speicherkonto auch für Ihre Diagnoseprotokolle zu verwenden, damit sich alle Überwachungsdaten an einem zentralen Ort befinden.

> [!NOTE]
>  Sie können derzeit keine Daten in einem Speicherkonto archivieren, das sich hinter einem geschützten virtuellen Netzwerk befindet.

## <a name="diagnostic-settings"></a>Diagnoseeinstellungen

Legen Sie eine **Diagnoseeinstellung** für eine bestimmte Ressource fest, um die Diagnoseprotokolle mit einer der weiter unten angegebenen Methoden zu archivieren. Eine Diagnoseeinstellung für eine Ressource definiert die Kategorien der Protokolle und Metrikdaten, die an ein Ziel (Speicherkonto, Event Hubs-Namespace oder Log Analytics-Arbeitsbereich) gesendet werden. Außerdem definiert sie die Aufbewahrungsrichtlinie (Anzahl von Tagen für die Aufbewahrung) für Ereignisse jeder Protokollkategorie und Metrikdaten, die in einem Speicherkonto gespeichert werden. Wird die Aufbewahrungsrichtlinie auf Null festgelegt, werden Ereignisse für diese Protokollkategorie dauerhaft (also für immer) gespeichert. Für eine Aufbewahrungsrichtlinie kann andernfalls eine beliebige Anzahl von Tagen zwischen 1 und 2.147.483.647 festgelegt werden. [Hier](../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings)erfahren Sie mehr über Diagnoseeinstellungen. Aufbewahrungsrichtlinien werden pro Tag angewendet, sodass Protokolle am Ende eines Tages (UTC) ab dem Tag, der nun außerhalb der Aufbewahrungsrichtlinie liegt, gelöscht werden. Beispiel: Wenn Sie eine Aufbewahrungsrichtlinie für einen Tag verwenden, werden heute am Anfang des Tages die Protokolle von vorgestern gelöscht. Der Löschvorgang beginnt um Mitternacht (UTC), jedoch kann es bis zu 24 Stunden dauern, bis die Protokolle aus Ihrem Speicherkonto gelöscht werden. 

> [!NOTE]
> Das Senden mehrdimensionaler Metriken über die Diagnoseeinstellungen wird derzeit nicht unterstützt. Metriken mit Dimensionen werden als vereinfachte eindimensionale Metriken exportiert und dimensionswertübergreifend aggregiert.
>
> *Beispiel*: Die Metrik „Eingehende Nachrichten“ in einem Event Hub kann auf Warteschlangenebene untersucht und in einem Diagramm dargestellt werden. Wenn Sie die Metrik allerdings über die Diagnoseeinstellungen exportieren, umfasst die Darstellung alle eingehenden Nachrichten für alle Warteschlangen im Event Hub.
>
>

## <a name="archive-diagnostic-logs-using-the-portal"></a>Archivieren von Diagnoseprotokollen mithilfe des Portals

1. Navigieren Sie im Portal zu Azure Monitor, und klicken Sie auf **Diagnoseeinstellungen**.

    ![Abschnitt „Überwachung“ von Azure Monitor](media/archive-diagnostic-logs/diagnostic-settings-blade.png)

2. Filtern Sie optional die Liste nach Ressourcengruppe oder Ressourcentyp, und klicken Sie auf die Ressource, für die Sie eine Diagnoseeinstellung festlegen möchten.

3. Wenn keine Einstellungen für die Ressource vorhanden sind, die Sie ausgewählt haben, werden Sie aufgefordert, eine Einstellung zu erstellen. Klicken Sie auf „Diagnose aktivieren“.

   ![Diagnoseeinstellung hinzufügen – keine Einstellungen vorhanden](media/archive-diagnostic-logs/diagnostic-settings-none.png)

   Wenn Einstellungen für die Ressource vorhanden sind, sehen Sie eine Liste der Einstellungen, die bereits für diese Ressource konfiguriert sind. Klicken Sie auf „Diagnoseeinstellung hinzufügen“.

   ![Diagnoseeinstellung hinzufügen – Einstellungen vorhanden](media/archive-diagnostic-logs/diagnostic-settings-multiple.png)

3. Geben Sie Ihrer Einstellung einen Namen, aktivieren Sie das Kontrollkästchen **In Speicherkonto exportieren**, und wählen Sie dann ein Speicherkonto aus. Legen Sie optional eine Anzahl von Tagen für die Aufbewahrung dieser Protokolle fest. Verwenden Sie dazu die Schieberegler unter **Aufbewahrung (Tage)** . Bei einer Aufbewahrung von 0 Tagen werden die Protokolle dauerhaft gespeichert.

   ![Diagnoseeinstellung hinzufügen – Einstellungen vorhanden](media/archive-diagnostic-logs/diagnostic-settings-configure.png)

4. Klicken Sie auf **Speichern**.

Nach einigen Augenblicken wird die neue Einstellung in der Liste der Einstellungen für diese Ressource angezeigt, und Diagnoseprotokolle werden in diesem Speicherkonto archiviert, sobald neue Ereignisdaten generiert werden.

## <a name="archive-diagnostic-logs-via-azure-powershell"></a>Archivieren von Diagnoseprotokollen mit Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| Eigenschaft | Erforderlich | BESCHREIBUNG |
| --- | --- | --- |
| resourceId |Ja |Ressourcen-ID der Ressource, für die Sie eine Diagnoseeinstellung festlegen möchten |
| StorageAccountId |Nein |Ressourcen-ID des Speicherkontos, in dem Diagnoseprotokolle gespeichert werden sollen |
| Categories |Nein |Durch Trennzeichen getrennte Liste der zu aktivierenden Protokollkategorien |
| Enabled |Ja |Boolescher Wert, der angibt, ob die Diagnose für diese Ressource aktiviert oder deaktiviert werden soll |
| RetentionEnabled |Nein |Boolescher Wert, der angibt, ob eine Aufbewahrungsrichtlinie für diese Ressource aktiviert ist |
| RetentionInDays |Nein |Anzahl von Tagen für die Aufbewahrung von Ereignissen (1 bis 2.147.483.647). Bei einem Wert von 0 werden die Protokolle dauerhaft gespeichert. |

## <a name="archive-diagnostic-logs-via-the-azure-cli"></a>Archivieren von Diagnoseprotokollen mit Azure CLI

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Sie können das Diagnoseprotokoll um weitere Kategorien ergänzen, indem Sie dem JSON-Array, das als `--logs`-Parameter übergeben wird, Wörterbücher hinzufügen.

Das `--resource-group`-Argument ist nur erforderlich, wenn `--storage-account` keine Objekt-ID ist. Die vollständige Dokumentation für die Archivierung von Diagnoseprotokollen im Speicher finden Sie in der [CLI-Befehlsreferenz](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>Archivieren von Diagnoseprotokollen mit der REST-API

[Dieses Dokument](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) enthält Informationen zum Einrichten einer Diagnoseeinstellung mit der Azure Monitor-REST-API.

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>Schema der Diagnoseprotokolle im Speicherkonto

Nach dem Einrichten der Archivierung wird in dem Speicherkonto ein Speichercontainer erstellt, sobald ein Ereignis in einer der aktivierten Protokollkategorien auftritt. Die Blobs innerhalb des Containers befolgen in Aktivitätsprotokollen und Diagnoseprotokollen übergreifend die gleiche Namenskonvention, wie hier zu sehen:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Beispiel für einen Blobnamen:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Jedes Blob vom Typ „PT1H.json“ enthält ein JSON-Blob mit Ereignissen, die innerhalb der in der Blob-URL angegebenen Stunde (Beispiel: h=12) aufgetreten sind. Während der aktuellen Stunde werden Ereignisse an die Datei „PT1H.json“ angefügt, sobald sie auftreten. Der Minutenwert (m=00) ist immer „00“, da Diagnoseprotokollereignisse stundenweise in einzelne Blobs unterteilt werden.

Die einzelnen Ereignisse werden innerhalb der Datei „PT1H.json“ im folgenden Format im Array „records“ gespeichert:

``` JSON
{
    "records": [
        {
            "time": "2016-07-01T00:00:37.2040000Z",
            "systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
            "category": "NetworkSecurityGroupRuleCounter",
            "resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
            "operationName": "NetworkSecurityGroupCounters",
            "properties": {
                "vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
                "subnetPrefix": "10.3.0.0/24",
                "macAddress": "000123456789",
                "ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
                "direction": "In",
                "type": "allow",
                "matchedConnections": 1988
            }
        }
    ]
}
```

| Elementname | BESCHREIBUNG |
| --- | --- |
| time |Zeitstempel der Ereignisgenerierung durch den Azure-Dienst, der die zum Ereignis gehörende Anforderung verarbeitet hat. |
| resourceId |Ressourcen-ID der betroffenen Ressource. |
| operationName |Name des Vorgangs. |
| category |Protokollkategorie des Ereignisses |
| properties |Satz mit `<Key, Value>` -Paaren (Wörterbuch), die die Details des Ereignisses beschreiben. |

> [!NOTE]
> Die Eigenschaften und deren Verwendung können je nach Ressource variieren.

## <a name="next-steps"></a>Nächste Schritte

* [Herunterladen von Blobs für die Analyse](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Streamen von Diagnoseprotokollen an einen Event Hubs-Namespace](../../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md)
* [Archivieren von Azure Active Directory-Protokollen mit Azure Monitor](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Weitere Informationen zu Diagnoseprotokollen](../../azure-monitor/platform/diagnostic-logs-overview.md)

