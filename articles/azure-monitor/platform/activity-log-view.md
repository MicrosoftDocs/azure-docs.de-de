---
title: Anzeigen von Azure-Aktivitätsprotokollereignissen in Azure Monitor
description: Zeigen Sie das Azure-Aktivitätsprotokoll in Azure Monitor an, und rufen Sie es mit PowerShell, der CLI und der REST-API ab.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 32578f77f2b3f30d80953bdd1099d22c945c640b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245593"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen

Das [Azure-Aktivitätsprotokoll](activity-logs-overview.md) bietet Einblick in Ereignisse auf Abonnementebene, die in Azure aufgetreten sind. Dieser Artikel enthält Informationen zu verschiedenen Methoden für das Anzeigen und Abrufen von Aktivitätsprotokollereignissen.

## <a name="azure-portal"></a>Azure-Portal
Zeigen Sie das Aktivitätsprotokoll für alle Ressourcen über das Menü **Monitor** im Azure-Portal an. Das Aktivitätsprotokoll für eine bestimmte Ressource können Sie über die Option **Aktivitätsprotokoll** im Menü dieser Ressource anzeigen.

![Anzeigen des Aktivitätsprotokolls](./media/activity-logs-overview/view-activity-log.png)

Sie können Aktivitätsprotokollereignisse nach folgenden Feldern filtern:

* **Zeitraum**: Die Start- und Endzeit für Ereignisse.
* **Kategorie**: Die Ereigniskategorie wie unter [Kategorien im Aktivitätsprotokoll](activity-logs-overview.md#categories-in-the-activity-log) beschrieben.
* **Abonnement**: Name von mindestens einem Azure-Abonnement.
* **Ressourcengruppe**: Mindestens eine Ressourcengruppe in den ausgewählten Abonnements.
* **Ressource (Name)** : Der Name einer bestimmten Ressource.
* **Ressourcentyp:** Der Typ der Ressource, z.B. _Microsoft.Compute/virtualmachines_.
* **Vorgangsname**: Der Name eines Azure Resource Manager-Vorgangs, z.B. _Microsoft.SQL/servers/Write_.
* **Schweregrad**: Der Schweregrad des Ereignisses. Verfügbare Werte sind _Information_, _Warnung_, _Fehler_ und _Kritisch_.
* **Ereignis initiiert von:** Der Benutzer, der den Vorgang durchgeführt hat.
* **Suche öffnen**: Öffnet ein Textsuchfeld, mit dem in allen Feldern aller Ereignisse nach dieser Zeichenfolge gesucht wird.

### <a name="view-change-history"></a>Anzeigen des Änderungsverlaufs

Bei der Überprüfung des Aktivitätsprotokolls kann es hilfreich sein zu wissen, welche Änderungen um die Zeit eines bestimmten Ereignisses stattfanden. Diese Informationen können Sie mit dem **Änderungsverlauf** anzeigen. Wählen Sie aus dem Aktivitätsprotokoll ein Ereignis aus, das Sie eingehender überprüfen möchten. Wählen Sie die Registerkarte **Änderungsverlauf (Vorschau)** aus, um alle Änderungen anzuzeigen, die mit diesem Ereignis verknüpft sind.

![Ändern der Verlaufsliste für ein Ereignis](media/activity-logs-overview/change-history-event.png)

Wenn Änderungen zu dem Ereignis vorhanden sind, sehen Sie eine Liste der Änderungen, aus denen Sie eine Auswahl treffen können. Daraufhin öffnet sich die Seite **Änderungsverlauf (Vorschau)** . Auf dieser Seite sehen Sie die Änderungen an der Ressource. Wie Sie im folgenden Beispiel erkennen können, ist nicht nur zu sehen, dass sich die Größe des virtuellen Computer geändert hat, sondern es wird auch die Größe des virtuellen Computers vor und nach der Änderung angezeigt.

![Seite „Änderungsverlauf“ mit den Unterschieden](media/activity-logs-overview/change-history-event-details.png)

Weitere Informationen zum Änderungsverlauf finden Sie unter [Abrufen von Ressourcenänderungen](../../governance/resource-graph/how-to/get-resource-changes.md).


## <a name="log-analytics-workspace"></a>Log Analytics-Arbeitsbereich
Klicken Sie am oberen Rand der Seite **Aktivitätsprotokoll** auf **Protokolle**, um die [Überwachungslösung der Aktivitätsprotokollanalyse](activity-log-collect.md) für das Abonnement zu öffnen. Hiermit können Sie Analysen für das Aktivitätsprotokoll anzeigen und [Protokollabfragen](../log-query/log-query-overview.md) mit der Tabelle **AzureActivity** ausführen. Wenn Ihr Aktivitätsprotokoll nicht mit einem Log Analytics-Arbeitsbereich verbunden ist, werden Sie aufgefordert, diese Konfiguration vorzunehmen.



## <a name="powershell"></a>PowerShell
Verwenden Sie das Cmdlet [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog), um das Aktivitätsprotokoll über PowerShell abzurufen. Im Folgenden sind einige allgemeine Beispiele aufgeführt.

> [!NOTE]
> `Get-AzLog` stellt nur den Verlauf für 15 Tage bereit. Verwenden Sie den Parameter **-MaxEvents**, um die letzten N Ereignisse für mehr als 15 Tage abzufragen. Verwenden Sie für den Zugriff auf Ereignisse, die älter als 15 Tage sind, die REST-API oder das SDK. Wenn Sie **StartTime** nicht angeben, ist der Standardwert **EndTime** minus 1 Stunde. Wenn Sie **EndTime**nicht angeben, ist der Standardwert die aktuelle Zeit. Alle Zeitangaben sind in UTC.


Abrufen von Protokolleinträgen, die nach einem bestimmten Zeitpunkt erstellt wurden:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Abrufen von Protokolleinträgen in einem Datums-/Uhrzeitbereich:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Abrufen von Protokolleinträgen aus einer bestimmten Ressourcengruppe:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Abrufen von Protokolleinträgen von einem bestimmten Ressourcenanbieter in einem Datums-/Uhrzeitbereich:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Abrufen von Protokolleinträgen mit einem bestimmten Aufrufer:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Abrufen der letzten 1000 Ereignisse:

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>Befehlszeilenschnittstelle (CLI)
Verwenden Sie [az monitor activity-log](cli-samples.md#view-activity-log-for-a-subscription), um das Aktivitätsprotokoll über die CLI abzurufen. Im Folgenden sind einige allgemeine Beispiele aufgeführt.


Anzeigen aller verfügbaren Optionen:

```azurecli
az monitor activity-log list -h
```

Abrufen von Protokolleinträgen aus einer bestimmten Ressourcengruppe:

```azurecli
az monitor activity-log list --resource-group <group name>
```

Abrufen von Protokolleinträgen mit einem bestimmten Aufrufer:

```azurecli
az monitor activity-log list --caller myname@company.com
```

Abrufen von Protokollen nach Aufrufer für einen Ressourcentyp innerhalb eines Datumsbereichs:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>REST-API
Verwenden Sie die [Azure Monitor-REST-API](https://docs.microsoft.com/rest/api/monitor/), um das Aktivitätsprotokoll über einen REST-Client abzurufen. Im Folgenden sind einige allgemeine Beispiele aufgeführt.

Abrufen von Aktivitätsprotokollen mit Filter:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Abrufen von Aktivitätsprotokollen mit Filter und Auswahlfunktion:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Abrufen von Aktivitätsprotokollen mit Auswahlfunktion:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Abrufen von Aktivitätsprotokollen ohne Filter oder Auswahlfunktion:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>Nächste Schritte

* [Lesen einer Übersicht über das Aktivitätsprotokoll](activity-logs-overview.md)
* [Archivieren des Aktivitätsprotokolls im Speicher oder Streamen des Protokolls auf Event Hubs](activity-log-export.md)
* [Stream the Azure Activity Log to Event Hubs (Streamen des Azure-Aktivitätsprotokolls auf Event Hubs)](activity-logs-stream-event-hubs.md)
* [Archivieren des Aktivitätsprotokolls im Speicher](archive-activity-log.md)

