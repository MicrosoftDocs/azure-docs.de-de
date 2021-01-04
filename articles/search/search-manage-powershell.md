---
title: PowerShell-Skripts mit Az.Search-Modul
titleSuffix: Azure Cognitive Search
description: Erstellen und konfigurieren Sie einen Azure Cognitive Search-Dienst mit PowerShell. Sie können einen Dienst zentral hoch- oder herunterskalieren, Administrator- und Abfrage-API-Schlüssel verwalten und Systeminformationen abfragen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: d7b672b7e2c3004eba4a38bd659965b7dee24db6
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422483"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>Verwalten des Azure Cognitive Search-Diensts mit PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [REST-API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Sie können PowerShell-Cmdlets und -Skripts unter Windows oder Linux oder in [Azure Cloud Shell](../cloud-shell/overview.md) ausführen, um Azure Cognitive Search zu erstellen und zu konfigurieren. Das **Az.Search** -Modul erweitert [Azure PowerShell](/powershell/) um vollständige Parität mit den [Search-Verwaltungs-REST-APIs](/rest/api/searchmanagement) und die Fähigkeit, die folgenden Aufgaben auszuführen:

> [!div class="checklist"]
> * [Auflisten von Suchdiensten in einem Abonnement](#list-search-services)
> * [Zurückgeben von Dienstinformationen](#get-search-service-information)
> * [Erstellen oder Löschen eines Diensts](#create-or-delete-a-service)
> * [Erneutes Generieren von Administrator-API-Schlüsseln](#regenerate-admin-keys)
> * [Erstellen oder Löschen von Abfrage-API-Schlüsseln](#create-or-delete-query-keys)
> * [Hoch- oder Herunterskalieren mit Replikaten und Partitionen](#scale-replicas-and-partitions)

Gelegentlich werden Fragen zu Aufgaben gestellt, die *nicht* in der oben stehenden Liste enthalten sind. Derzeit können Sie weder das **Az.Search** -Modul noch die Verwaltungs-REST-API verwenden, um einen Servernamen, eine Region oder eine Ebene zu ändern. Dedizierte Ressourcen werden beim Erstellen eines Diensts zugeordnet. Daher ist zum Ändern der zugrunde liegenden Hardware (Standort oder Knotentyp) ein neuer Dienst erforderlich. Ebenso gibt es keine Tools oder APIs für die Übertragung von Inhalten, wie z. B. einem Index, aus einem Dienst in einen anderen.

Innerhalb eines Diensts erfolgt die Erstellung und Verwaltung von Inhalten über die [Azure Search-Dienste-REST-API](/rest/api/searchservice/) oder das [.NET SDK](/dotnet/api/overview/azure/search.documents-readme). Zwar sind keine dedizierten PowerShell-Befehle für Inhalt vorhanden, doch können Sie ein PowerShell-Skript schreiben, das REST- oder .NET-APIs zum Erstellen und Laden von Indizes aufruft.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Überprüfen von Versionen und Laden von Modulen

Die Beispiele in diesem Artikel sind interaktiv und erfordern erhöhte Berechtigungen. Azure PowerShell (das **Az** -Modul) muss installiert sein. Weitere Informationen finden Sie unter [Installieren von Azure PowerShell](/powershell/azure/).

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

## <a name="list-services-in-a-subscription"></a>Auflisten von Diensten in einem Abonnement

Die folgenden Befehle stammen von [**Az.Resources**](/powershell/module/az.resources) und geben Informationen zu vorhandenen Ressourcen und Diensten zurück, die bereits in Ihrem Abonnement bereitgestellt sind. Wenn Sie nicht wissen, wie viele Suchdienste bereits erstellt wurden, geben die folgenden Befehle diese Information zurück und ersparen Ihnen dadurch das Aufrufen des Portals.

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

Befehle von [**Az.Search**](/powershell/module/az.search) sind erst nach dem Laden des Moduls verfügbar.

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

Nachdem **Az.Search** importiert wurde und Ihnen die Ressourcengruppe mit Ihrem Suchdienst bekannt ist, führen Sie [Get-AzSearchService](/powershell/module/az.search/get-azsearchservice) aus, um die Dienstdefinition zurückzugeben. Dazu gehören Name, Region und Tarif sowie die Anzahl von Replikaten und Partitionen.

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

Mit [**New-AzSearchService**](/powershell/module/az.search/new-azsearchadminkey) wird [ein neuer Suchdienst erstellt](search-create-service-portal.md).

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

Mit [**New-AzSearchAdminKey**](/powershell/module/az.search/new-azsearchadminkey) wird ein Rollover von Administrator- [API-Schlüsseln](search-security-api-keys.md) ausgeführt. Mit jedem Dienst werden zwei Administratorschlüssel für den authentifizierten Zugriff erstellt. Schlüssel sind für jede Anforderung erforderlich. Beide Administratorschlüssel sind funktionell gleichwertig und gewähren vollständigen Schreibzugriff auf einen Suchdienst mit der Möglichkeit, alle Informationen abrufen oder ein beliebiges Objekt zu erstellen und zu löschen. Es sind zwei Schlüssel vorhanden, sodass Sie den einen verwenden können, während der andere ersetzt wird. 

Sie können immer nur jeweils einen Schlüssel erneut generieren, der entweder als `primary`- oder `secondary`-Schlüssel angegeben ist. Um einen ununterbrochenen Dienst zu gewährleisten, denken Sie daran, den gesamten Clientcode so zu aktualisieren, dass während des Rollovers des primären Schlüssels ein sekundärer Schlüssel verwendet wird. Vermeiden Sie das Ändern der Schlüssel, während Vorgänge ausgeführt werden.

Wenn Sie Schlüssel ohne Aktualisierung des Clientcodes erneut generieren, treten bei Anforderungen, bei denen der alte Schlüssel verwendet wird, Fehler auf. Durch das erneute Generieren aller neuen Schlüssel werden Sie nicht dauerhaft vom Dienst ausgesperrt, und Sie können weiterhin über das Portal auf den Dienst zugreifen. Nachdem Sie primäre und sekundäre Schlüssel erneut generiert haben, können Sie den Clientcode zur Verwendung der neuen Schlüssel aktualisieren, und die Vorgänge werden entsprechend fortgesetzt.

Werte für die API-Schlüssel werden vom Dienst generiert. Sie können keinen benutzerdefinierten Schlüssel für die Verwendung in Azure Cognitive Search bereitstellen. Ebenso ist kein benutzerdefinierter Name für Administrator-API-Schlüssel vorhanden. Verweise auf den Schlüssel sind feste Zeichenfolgen, entweder `primary` oder `secondary`. 

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

Mit [**New-AzSearchQueryKey**](/powershell/module/az.search/new-azsearchquerykey) werden Abfrage- [API-Schlüssel](search-security-api-keys.md) für schreibgeschützten Zugriff von Client-Apps auf einen Azure Cognitive Search-Index erstellt. Abfrageschlüssel werden zum Authentifizieren bei einem bestimmten Index für das Abrufen von Suchergebnissen verwendet. Abfrageschlüssel gewähren keinen schreibgeschützten Zugriff auf andere Elemente im Dienst, z.B. Index, Datenquelle oder Indexer.

Sie können keinen Schlüssel für die Verwendung in Azure Cognitive Search bereitstellen. API-Schlüssel werden vom Dienst generiert.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Skalieren von Replikaten und Partitionen

Mit [**Set-AzSearchService**](/powershell/module/az.search/set-azsearchservice) wird die [Anzahl von Replikaten und Partitionen erhöht oder verringert](search-capacity-planning.md), um abrechenbare Ressourcen innerhalb des Diensts anzupassen. Durch eine erhöhte Anzahl von Replikaten oder Partitionen erhöht sich auch Ihre Rechnung, die sowohl feste als auch variable Gebühren umfasst. Wenn Sie vorübergehend zusätzliche Verarbeitungsleistung benötigen, können Sie die Anzahl der Replikate und Partitionen zum Bewältigen der Workload erhöhen. Der Überwachungsbereich auf der Übersichtsseite des Portals weist Kacheln für die Abfragewartezeit, Abfragen pro Sekunde und Drosselung auf, die angeben, ob die aktuelle Kapazität ausreichend ist.

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

* [Erstellen eines Indexes für Azure Cognitive Search im Azure-Portal](search-get-started-portal.md)
* [Einrichten eines Indexers zum Laden von Daten anderer Dienste](search-indexer-overview.md)
* [Abfragen eines Azure Cognitive Search-Indexes per Suchexplorer im Azure-Portal](search-explorer.md)
* [Verwendung von Azure Cognitive Search in .NET](search-howto-dotnet-sdk.md)