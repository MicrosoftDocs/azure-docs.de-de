---
title: PowerShell-Skripts mit Az.Search-Modul – Azure Search
description: Erstellen und Konfigurieren Sie einen Azure Search-Dienst mit PowerShell. Sie können einen Dienst zentral hoch- oder herunterskalieren, Administrator- und Abfrage-API-Schlüssel verwalten und Systeminformationen abfragen.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: heidist
ms.openlocfilehash: 8f07468ccff4431e1afdf66aedc72599ddc0c25b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60194273"
---
# <a name="manage-your-azure-search-service-with-powershell"></a>Verwalten des Azure Search-Diensts mit PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [REST-API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Sie können PowerShell-Cmdlets und -Skripts unter Windows oder Linux oder in [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) ausführen, um Azure Search zu erstellen und zu konfigurieren. Das **Az.Search**-Modul erweitert Azure PowerShell um vollständige Parität mit den [REST-APIs für die Azure Search-Verwaltung](https://docs.microsoft.com/rest/api/searchmanagement). Mit Azure PowerShell und **Az.Search** können Sie folgende Aufgaben ausführen:

> [!div class="checklist"]
> * [Auflisten aller Suchdienste in Ihrem Abonnement](#list-search-services)
> * [Abrufen von Informationen zu einem bestimmten Suchdienst](#get-search-service-information)
> * [Erstellen oder Löschen eines Diensts](#create-or-delete-a-service)
> * [Erneutes Generieren von Administrator-API-Schlüsseln](#regenerate-admin-keys)
> * [Erstellen oder Löschen von Abfrage-API-Schlüsseln](#create-or-delete-query-keys)
> * [Skalieren eines Diensts durch Erhöhen oder Verringern der Anzahl von Replikaten und Partitionen](#scale-replicas-and-partitions)

Mit PowerShell kann nicht der Name, die Region oder der Tarif Ihres Diensts geändert werden. Dedizierte Ressourcen werden beim Erstellen eines Diensts zugeordnet. Zum Ändern der zugrunde liegenden Hardware (Standort oder Knotentyp) ist ein neuer Dienst erforderlich. Es gibt keine Tools oder APIs für die Übertragung von Inhalten aus einem Dienst in einen anderen. Die gesamte Inhaltsverwaltung erfolgt über [REST](https://docs.microsoft.com/rest/api/searchservice/)- oder [.NET](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search)-APIs, und wenn Sie Indizes verschieben möchten, müssen Sie diese in einem neuen Dienst erneut erstellen und erneut laden. 

Zwar sind keine dedizierten PowerShell-Befehle für die Inhaltsverwaltung vorhanden, doch können Sie ein PowerShell-Skript schreiben, das REST oder .NET zum Erstellen und Laden von Indizes aufruft. Das **Az.Search**-Modul selbst stellt diese Vorgänge nicht bereit.

Nachfolgend sind noch andere Aufgaben aufgeführt, die nicht durch PowerShell oder eine andere API (nur Portal) unterstützt werden:
+ [Anfügen einer Cognitive Services-Ressource](cognitive-search-attach-cognitive-services.md) für [um KI erweiterte Indizierung](cognitive-search-concept-intro.md). Ein Cognitive Service wird einer Qualifikationsgruppe und nicht einem Abonnement oder Dienst angefügt.
+ [Add-On-Überwachungslösungen](search-monitor-usage.md#add-on-monitoring-solutions) oder [Durchsuchen der Datenverkehrsanalyse](search-traffic-analytics.md) zur Überwachung von Azure Search.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Überprüfen von Versionen und Laden von Modulen

Die Beispiele in diesem Artikel sind interaktiv und erfordern erhöhte Berechtigungen. Azure PowerShell (das **Az**-Modul) muss installiert sein. Weitere Informationen finden Sie unter [Installieren von Azure PowerShell](/powershell/azure/overview).

### <a name="powershell-version-check-51-or-later"></a>Überprüfen der PowerShell-Version (5.1 oder höher)

Auf jedem unterstützten Betriebssystem muss die lokale PowerShell-Version 5.1 oder höher sein.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Laden von Azure PowerShell

Wenn Sie nicht sicher sind, ob **Az** installiert ist, führen Sie den folgenden Befehl als Überprüfungsschritt aus. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Bei einigen Systemen werden Module nicht automatisch geladen. Wenn Sie als Reaktion auf den vorherigen Befehl eine Fehlermeldung erhalten, versuchen Sie, das Modul zu laden. Wenn das fehlschlägt, kehren Sie zu den Installationsanweisungen zurück, um festzustellen, ob Sie einen Schritt übersehen haben.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Herstellen einer Verbindung mit Azure mit einem Browser-Anmeldetoken

Mit Ihren Anmeldeinformationen für das Portal können Sie eine Verbindung mit einem Abonnement in PowerShell herstellen. Als Alternative steht die [nicht interaktive Authentifizierung mit einem Dienstprinzipal](../active-directory/develop/howto-authenticate-service-principal-powershell.md) zur Verfügung.

```azurepowershell-interactive
Connect-AzAccount
```

Wenn Sie über mehrere Azure-Abonnements verfügen, legen Sie Ihr Azure-Abonnement fest. Führen Sie den folgenden Befehl aus, um eine Liste Ihrer aktuellen Abonnements anzuzeigen.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Führen Sie den folgenden Befehl aus, um das Abonnement anzugeben. Im folgenden Beispiel ist der Name des Abonnements `ContosoSubscription`.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-all-azure-search-services-in-your-subscription"></a>Auflisten aller Azure Search-Dienste in Ihrem Abonnement

Die folgenden Befehle stammen von [**Az.Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources) und geben Informationen zu vorhandenen Ressourcen und Diensten zurück, die bereits in Ihrem Abonnement bereitgestellt sind. Wenn Sie nicht wissen, wie viele Suchdienste bereits erstellt wurden, geben die folgenden Befehle diese Information zurück und ersparen Ihnen dadurch das Aufrufen des Portals.

Der erste Befehl gibt alle Suchdienste zurück.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Geben Sie aus der Liste der Dienste Informationen zu einer bestimmten Ressource zurück.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Die Ergebnisse sollten in etwa der folgenden Ausgabe ähneln.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Importieren von Az.Search

Befehle von [**Az.Search**](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) sind erst nach dem Laden des Moduls verfügbar.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Auflisten aller Az.Search-Befehle

Geben Sie als Überprüfungsschritt eine Liste der im Modul bereitgestellten Befehle aus.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Die Ergebnisse sollten in etwa der folgenden Ausgabe ähneln.

```
CommandType     Name                                Version    Source
-----------     ----                                -------    ------
Cmdlet          Get-AzSearchAdminKeyPair            0.7.1      Az.Search
Cmdlet          Get-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          Get-AzSearchService                 0.7.1      Az.Search
Cmdlet          New-AzSearchAdminKey                0.7.1      Az.Search
Cmdlet          New-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          New-AzSearchService                 0.7.1      Az.Search
Cmdlet          Remove-AzSearchQueryKey             0.7.1      Az.Search
Cmdlet          Remove-AzSearchService              0.7.1      Az.Search
Cmdlet          Set-AzSearchService                 0.7.1      Az.Search
```

## <a name="get-search-service-information"></a>Abrufen von Suchdienstinformationen

Nachdem **Az.Search** importiert wurde und Ihnen die Ressourcengruppe mit Ihrem Suchdienst bekannt ist, führen Sie [Get-AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) aus, um die Dienstdefinition zurückzugeben. Dazu gehören Name, Region und Tarif sowie die Anzahl von Replikaten und Partitionen.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Die Ergebnisse sollten in etwa der folgenden Ausgabe ähneln.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>Erstellen oder Löschen eines Diensts

Mit [**New-AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) wird [ein neuer Suchdienst erstellt](search-create-service-portal.md).

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
Die Ergebnisse sollten in etwa der folgenden Ausgabe ähneln.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

## <a name="regenerate-admin-keys"></a>Erneutes Generieren von Administratorschlüsseln

Mit [**New-AzSearchAdminKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) wird ein Rollover von Administrator-[API-Schlüsseln](search-security-api-keys.md) ausgeführt. Mit jedem Dienst werden zwei Administratorschlüssel für den authentifizierten Zugriff erstellt. Schlüssel sind für jede Anforderung erforderlich. Beide Administratorschlüssel sind funktionell gleichwertig und gewähren vollständigen Schreibzugriff auf einen Suchdienst mit der Möglichkeit, alle Informationen abrufen oder ein beliebiges Objekt zu erstellen und zu löschen. Es sind zwei Schlüssel vorhanden, sodass Sie den einen verwenden können, während der andere ersetzt wird. 

Sie können immer nur jeweils einen Schlüssel erneut generieren, der entweder als `primary`- oder `secondary`-Schlüssel angegeben ist. Um einen ununterbrochenen Dienst zu gewährleisten, denken Sie daran, den gesamten Clientcode so zu aktualisieren, dass während des Rollovers des primären Schlüssels ein sekundärer Schlüssel verwendet wird. Vermeiden Sie das Ändern der Schlüssel, während Vorgänge ausgeführt werden.

Wenn Sie Schlüssel ohne Aktualisierung des Clientcodes erneut generieren, treten bei Anforderungen, bei denen der alte Schlüssel verwendet wird, Fehler auf. Durch das erneute Generieren aller neuen Schlüssel werden Sie nicht dauerhaft vom Dienst ausgesperrt, und Sie können weiterhin über das Portal auf den Dienst zugreifen. Nachdem Sie primäre und sekundäre Schlüssel erneut generiert haben, können Sie den Clientcode zur Verwendung der neuen Schlüssel aktualisieren, und die Vorgänge werden entsprechend fortgesetzt.

Werte für die API-Schlüssel werden vom Dienst generiert. Sie können keinen benutzerdefinierten Schlüssel für die Verwendung in Azure Search bereitstellen. Ebenso ist kein benutzerdefinierter Name für Administrator-API-Schlüssel vorhanden. Verweise auf den Schlüssel sind feste Zeichenfolgen, entweder `primary` oder `secondary`. 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Die Ergebnisse sollten in etwa der folgenden Ausgabe ähneln. Es werden beide Schlüssel zurückgegeben, obwohl Sie immer nur jeweils einen ändern.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Erstellen oder Löschen von Abfrageschlüsseln

Mit [**New-AzSearchQueryKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) werden Abfrage-[API-Schlüssel](search-security-api-keys.md) für schreibgeschützten Zugriff von Client-Apps auf einen Azure Search-Index erstellt. Abfrageschlüssel werden zum Authentifizieren bei einem bestimmten Index für das Abrufen von Suchergebnissen verwendet. Abfrageschlüssel gewähren keinen schreibgeschützten Zugriff auf andere Elemente im Dienst, z.B. Index, Datenquelle oder Indexer.

Sie können keinen Schlüssel für die Verwendung in Azure Search bereitstellen. API-Schlüssel werden vom Dienst generiert.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Skalieren von Replikaten und Partitionen

Mit [**Set-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) wird die [Anzahl von Replikaten und Partitionen erhöht oder verringert](search-capacity-planning.md), um abrechenbare Ressourcen innerhalb des Diensts anzupassen. Durch eine erhöhte Anzahl von Replikaten oder Partitionen erhöht sich auch Ihre Rechnung, die sowohl feste als auch variable Gebühren umfasst. Wenn Sie vorübergehend zusätzliche Verarbeitungsleistung benötigen, können Sie die Anzahl der Replikate und Partitionen zum Bewältigen der Workload erhöhen. Der Überwachungsbereich auf der Übersichtsseite des Portals weist Kacheln für die Abfragewartezeit, Abfragen pro Sekunde und Drosselung auf, die angeben, ob die aktuelle Kapazität ausreichend ist.

Das Hinzufügen oder Entfernen von Ressourcen kann eine Weile dauern. Anpassungen der Kapazität finden im Hintergrund statt, damit bestehende Workloads fortgesetzt werden können. Zusätzliche Kapazität wird für eingehende Anforderungen verwendet, sobald sie bereitsteht, und erfordert keine zusätzliche Konfiguration. 

Das Entfernen von Kapazität kann zu einer Unterbrechung führen. Es wird empfohlen, vor dem Verringern der Kapazität alle Indizierungs- und Indexeraufträge anzuhalten, um verworfene Anforderungen zu vermeiden. Wenn das nicht möglich ist, sollten Sie die Kapazität schrittweise verringern, jeweils ein Replikat und eine Partition nach der anderen, bis das neue Zielniveau erreicht ist.

Nachdem Sie den Befehl gesendet haben, gibt es keine Möglichkeit, ihn während der Ausführung zu beenden. Sie müssen warten, bis der Befehl abgeschlossen ist, bevor Sie die jeweilige Anzahl überarbeiten können.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Die Ergebnisse sollten in etwa der folgenden Ausgabe ähneln.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```


## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie einen [Index](search-what-is-an-index.md), verwenden Sie das Portal, REST-APIs oder das .NET SDK zum [Abfragen eines Index](search-query-overview.md).

* [Erstellen eines Azure Search-Index im Azure-Portal](search-create-index-portal.md)
* [Einrichten eines Indexers zum Laden von Daten anderer Dienste](search-indexer-overview.md)
* [Abfragen eines Azure Search-Index per Suchexplorer im Azure-Portal](search-explorer.md)
* [Verwenden von Azure Search aus einer .NET-Anwendung](search-howto-dotnet-sdk.md)