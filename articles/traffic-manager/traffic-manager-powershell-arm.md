---
title: Verwenden von PowerShell zum Verwalten von Traffic Manager in Azure
description: Verwenden von PowerShell für Traffic Manager mit Azure Resource Manager
services: traffic-manager
documentationcenter: na
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: allensu
ms.openlocfilehash: a2065ba51b74d7f55464a22df0f55cac4c6defcb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071025"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Verwenden von PowerShell zum Verwalten von Traffic Manager

Azure Resource Manager ist die bevorzugte Verwaltungsschnittstelle für Dienste in Azure. Azure Traffic Manager-Profile können mithilfe von APIs und Tools auf Basis von Azure Resource Manager verwaltet werden.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-model"></a>Ressourcenmodell

Azure Traffic Manager wird mithilfe einer Reihe von Einstellungen, dem sogenannten Traffic Manager-Profil, konfiguriert. Dieses Profil enthält DNS-Einstellungen, Einstellungen für das Routing von Datenverkehr, Endpunktüberwachungseinstellungen und eine Liste der Dienstendpunkte, an die der Datenverkehr weitergeleitet wird.

Jedes Traffic Manager-Profil wird durch eine Ressource vom Typ „TrafficManagerProfiles“ dargestellt. Auf der REST-API-Ebene lautet der URI für jedes Profil wie folgt:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Einrichten von Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Für diese Anweisungen wird Microsoft Azure PowerShell verwendet. Im folgenden Artikel wird die Installation und Konfiguration von Azure PowerShell erläutert.

* [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview)

In den Beispielen in diesem Artikel wird davon ausgegangen, dass Sie eine vorhandene Ressourcengruppe besitzen. Sie können mithilfe des folgenden Befehls eine Ressourcengruppe erstellen:

```powershell
New-AzResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Azure Resource Manager erfordert, dass alle Ressourcengruppen einen Speicherort haben. Dieser Speicherort wird als Standardspeicherort für in dieser Ressourcengruppe erstellte Ressourcen verwendet. Da Traffic Manager-Profilressourcen global und nicht regional sind, hat die Auswahl des Speicherorts für die Ressourcengruppe jedoch keine Auswirkungen auf Azure Traffic Manager.

## <a name="create-a-traffic-manager-profile"></a>Erstellen eines Traffic Manager-Profils

Verwenden Sie zum Erstellen eines Traffic Manager-Profils das Cmdlet `New-AzTrafficManagerProfile`:

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

In der folgenden Tabelle sind die Parameter beschrieben:

| Parameter | BESCHREIBUNG |
| --- | --- |
| NAME |Der Ressourcenname für die Traffic Manager-Profilressource. Profile in der gleichen Ressourcengruppe müssen eindeutige Namen aufweisen. Dieser Name unterscheidet sich von den DNS-Namen, die für DNS-Abfragen verwendet werden. |
| ResourceGroupName |Name der Ressourcengruppe, in der die Profilressource enthalten ist |
| TrafficRoutingMethod |Gibt die Routingmethode an. Hiermit wird ermittelt, welcher Endpunkt als Reaktion auf eine DNS-Abfrage zurückgegeben wird. Mögliche Werte sind „Leistung“, „Gewichtung“ und „Priorität“. |
| RelativeDnsName |Gibt den Hostnamenteil des DNS-Namens an, der von diesem Traffic Manager-Profil bereitgestellt wird. Dieser Wert wird mit dem DNS-Domänennamen kombiniert, der von Azure Traffic Manager zum Erstellen des vollqualifizierten Domänennamens (FQDN) des Profils verwendet wird. So wird z.B. der Wert „Contoso“ zu „contoso.trafficmanager.net“. |
| TTL |Gibt die DNS-Gültigkeitsdauer (Time-To-Live, TTL) in Sekunden an. Dadurch werden die lokale DNS-Auflösung und DNS-Clients informiert, wie lange die für dieses Traffic Manager-Profil bereitgestellten DNS-Antworten zwischengespeichert werden sollen. |
| MonitorProtocol |Gibt das Protokoll an, mit dem die Endpunktintegrität überwacht werden soll. Mögliche Werte sind „HTTP“ und „HTTPS“. |
| MonitorPort |Gibt den TCP-Port an, mit dem die Endpunktintegrität überwacht werden soll |
| MonitorPath |Gibt den Pfad relativ zum Endpunktdomänennamen an, mit dem die Endpunktintegrität getestet wird. |

Das Cmdlet erstellt ein Traffic Manager-Profil in Azure und gibt ein entsprechendes Profilobjekt an PowerShell zurück. Das Profil enthält zu diesem Zeitpunkt keine Endpunkte. Weitere Informationen zum Hinzufügen von Endpunkten zu einem Traffic Manager-Profil finden Sie unter „Hinzufügen von Traffic Manager-Endpunkten“.

## <a name="get-a-traffic-manager-profile"></a>Abrufen eines Traffic Manager-Profils

Rufen Sie ein vorhandenes Traffic Manager-Profilobjekt mit dem Cmdlet `Get-AzTrafficManagerProfle` ab:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Dieses Cmdlet gibt ein Traffic Manager-Profilobjekt zurück.

## <a name="update-a-traffic-manager-profile"></a>Aktualisieren eines Traffic Manager-Profils

Das Ändern von Traffic Manager-Profilen folgt einem dreistufigen Prozess:

1. Rufen Sie das Profil mit `Get-AzTrafficManagerProfile` ab, oder verwenden Sie das von `New-AzTrafficManagerProfile` zurückgegebene Profil.
2. Ändern Sie das Profil. Sie können Endpunkte hinzufügen und entfernen oder Endpunkt- oder Profilparameter ändern. Diese Änderungen sind Offline-Vorgänge. Sie ändern nur das lokale Objekt im Speicher, das das Profil darstellt.
3. Übertragen Sie die Änderungen mit dem Cmdlet `Set-AzTrafficManagerProfile`.

Alle Eigenschaften des Profils können mit Ausnahme von „RelativeDnsName“ des Profils geändert werden. Um „RelativeDnsName“ zu ändern, müssen Sie das Profil löschen und ein neues Profil mit einem neuen Namen erstellen.

Im folgenden Beispiel wird gezeigt, wie die TTL des Profils geändert werden kann:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$TmProfile.Ttl = 300
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

Es gibt drei Arten von Traffic Manager-Endpunkten:

1. **Azure-Endpunkte** sind Dienste, die unter Azure gehostet werden.
2. **Externe Endpunkte** sind Dienste, die außerhalb von Azure gehostet werden.
3. **Geschachtelte Endpunkte** werden verwendet, um geschachtelte Hierarchien von Traffic Manager-Profilen zu erstellen. Geschachtelte Endpunkte ermöglichen erweiterte Datenverkehrrouting-Konfigurationen für komplexe Anwendungen.

In allen drei Fällen können Endpunkte auf zwei Arten hinzugefügt werden:

1. Mithilfe des zuvor beschriebenen dreistufigen Verfahrens. Der Vorteil dieser Methode besteht darin, dass mehrere Änderungen am Endpunkt mit nur einem Update vorgenommen werden können.
2. Mithilfe des Cmdlets „New-AzureTrafficManagerEndpoint“. Mit diesem Cmdlet wird einem vorhandenen Traffic Manager-Profil in einem Schritt ein Endpunkt hinzugefügt.

## <a name="adding-azure-endpoints"></a>Hinzufügen von Azure-Endpunkten

Azure-Endpunkte verweisen auf in Azure gehostete Dienste. Zwei Arten von Azure-Endpunkten werden unterstützt:

1. Azure App Service
2. Azure-PublicIpAddress-Ressourcen (die an einen Load Balancer oder an die Netzwerkkarte eines virtuellen Computers angefügt werden können). Der PublicIpAddress-Ressource muss ein DNS-Name zugewiesen werden, damit sie in Traffic Manager verwendet werden kann.

In jedem Fall gilt Folgendes:

* Der Dienst wird mit dem Parameter „targetResourceId“ von `Add-AzTrafficManagerEndpointConfig` oder `New-AzTrafficManagerEndpoint` angegeben.
* „Target“ und „EndpointLocation“ werden von „targetResourceId“ impliziert.
* Die Angabe von „Weight“ ist optional. Eine Gewichtung (Weight) werden nur verwendet, wenn das Profil für die Verwendung der gewichteten Datenverkehr-Routingmethode konfiguriert ist. Andernfalls wird der Parameter ignoriert. Wenn angegeben, muss der Wert eine Zahl zwischen 1 und 1.000 sein. Der Standardwert ist „1“.
* Die Angabe von „Priority“ ist optional. Prioritäten werden nur verwendet, wenn das Profil für die Verwendung der Prioritätsmethode für das Datenverkehrrouting konfiguriert ist. Andernfalls wird der Parameter ignoriert. Gültige Werte reichen von 1 bis 1.000, wobei niedrigere Werte eine höhere Priorität anzeigen. Wenn Sie für einen Endpunkt Prioritäten angeben, müssen Sie für alle Endpunkte Prioritäten angeben. Wenn keine Prioritäten angegeben werden, werden Standardwerte beginnend mit 1 in der Reihenfolge angewendet, in der Endpunkte aufgeführt sind.

### <a name="example-1-adding-app-service-endpoints-using-add-aztrafficmanagerendpointconfig"></a>Beispiel 1: Hinzufügen von App Service-Endpunkten mithilfe von `Add-AzTrafficManagerEndpointConfig`

In diesem Beispiel erstellen wir ein Traffic Manager-Profil und fügen zwei App Service-Endpunkte mithilfe des Cmdlets `Add-AzTrafficManagerEndpointConfig` hinzu.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzWebApp -Name webapp1
Add-AzTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzWebApp -Name webapp2
Add-AzTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-aztrafficmanagerendpoint"></a>Beispiel 2: Hinzufügen eines publicIpAddress-Endpunkts mit `New-AzTrafficManagerEndpoint`

In diesem Beispiel wird eine Ressource für eine öffentliche IP-Adresse zum Traffic Manager-Profil hinzugefügt. Für die öffentliche IP-Adresse muss ein DNS-Name konfiguriert sein. Sie kann an die Netzwerkkarte eines virtuellen Computers oder an einen Load Balancer gebunden sein.

```powershell
$ip = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Hinzufügen externer Endpunkte

Traffic Manager verwendet externe Endpunkte zum Weiterleiten von Datenverkehr an außerhalb von Azure gehostete Dienste. Wie bei Azure-Endpunkten können externe Endpunkte entweder mit `Add-AzTrafficManagerEndpointConfig` gefolgt von `Set-AzTrafficManagerProfile` oder `New-AzTrafficManagerEndpoint` hinzugefügt werden.

Beachten Sie beim Angeben von externen Endpunkten Folgendes:

* Der Domänenname des Endpunkts muss mit dem Parameter „Target“ angegeben werden
* EndpointLocation ist erforderlich, wenn die Leistungsmethode für das Datenverkehrrouting verwendet wird. Andernfalls ist die Angabe optional. Der Wert muss ein [gültiger Azure-Regionsname](https://azure.microsoft.com/regions/) sein.
* „Weight“ und „Priority“ sind optional.

### <a name="example-1-adding-external-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Beispiel 1: Hinzufügen von externen Endpunkten mithilfe von `Add-AzTrafficManagerEndpointConfig` und `Set-AzTrafficManagerProfile`

In diesem Beispiel erstellen wir ein Traffic Manager-Profil, fügen zwei externe Endpunkte hinzu und übernehmen die Änderungen.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-adding-external-endpoints-using-new-aztrafficmanagerendpoint"></a>Beispiel 2: Hinzufügen von externen Endpunkten mithilfe von `New-AzTrafficManagerEndpoint`

In diesem Beispiel fügen wir einem vorhandenen Profil einen externen Endpunkt hinzu. Das Profil wird mithilfe der Profil- und Ressourcengruppennamen angegeben.

```powershell
New-AzTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>„Geschachtelte“ Endpunkte hinzufügen

Jedes Traffic Manager-Profil gibt eine einzelne Methode für das Datenverkehrsrouting an. Es gibt jedoch Szenarios, die ein detaillierteres Datenverkehrsrouting erfordern, als es von einem einzelnen Traffic Manager-Profil bereitgestellt werden kann. Sie können Traffic Manager-Profile schachteln, um die Vorteile mehrerer Datenverkehrsrouting-Methoden zu kombinieren. Mit geschachtelten Profilen können Sie das Standardverhalten von Traffic Manager überschreiben, um größere und komplexere Anwendungsbereitstellungen zu unterstützen. Weitere detaillierte Beispiele finden Sie unter [Geschachtelte Traffic Manager-Profile](traffic-manager-nested-profiles.md).

Geschachtelte Endpunkte sind beim übergeordneten Profil mittels eines speziellen Endpunkttyps „NestedEndpoints“ konfiguriert. Beachten Sie beim Angeben geschachtelter Endpunkte Folgendes:

* Der Endpunkt muss mit dem Parameter „targetResourceId“ angegeben werden.
* EndpointLocation ist erforderlich, wenn die Leistungsmethode für das Datenverkehrrouting verwendet wird. Andernfalls ist die Angabe optional. Der Wert muss ein [gültiger Azure-Regionsname](https://azure.microsoft.com/regions/) sein.
* „Weight“ und „Priority“ sind wie bei Azure-Endpunkten optional.
* Der Parameter „MinChildEndpoints“ ist optional. Der Standardwert ist „1“. Falls die Anzahl der verfügbaren Endpunkte unter diesen Schwellenwert fällt, betrachtet das übergeordnete Profil das untergeordnete Profil als „heruntergestuft“ und leitet den Verkehr auf die anderen Endpunkte des übergeordneten Profils um.

### <a name="example-1-adding-nested-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Beispiel 1: Hinzufügen von geschachtelten Endpunkten mithilfe von `Add-AzTrafficManagerEndpointConfig` und `Set-AzTrafficManagerProfile`

In diesem Beispiel erstellen wir neue unter- und übergeordnete Traffic Manager-Profile, fügen die untergeordneten Profile den übergeordneten als geschachtelte Endpunkte hinzu und übermitteln diese Änderungen.

```powershell
$child = New-AzTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzTrafficManagerProfile -TrafficManagerProfile $parent
```

Aus Platzgründen haben wir den untergeordneten oder übergeordneten Profilen in diesem Beispiel keine anderen Endpunkte hinzugefügt.

### <a name="example-2-adding-nested-endpoints-using-new-aztrafficmanagerendpoint"></a>Beispiel 2: Hinzufügen von geschachtelten Endpunkten mithilfe von `New-AzTrafficManagerEndpoint`

In diesem Beispiel fügen wir einem vorhandenen übergeordneten Profil ein vorhandenes untergeordnetes Profil als einen geschachtelten Endpunkt hinzu. Das Profil wird mithilfe der Profil- und Ressourcengruppennamen angegeben.

```powershell
$child = Get-AzTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Hinzufügen von Endpunkten aus einem anderen Abonnement

Der Traffic Manager kann mit Endpunkten aus anderen Abonnements verwendet werden. Um die erforderliche Eingabe abzurufen, müssen Sie zum Abonnement mit dem Endpunkt wechseln, der zum Traffic Manager hinzufügt werden soll. Dann müssen Sie mit dem Traffic Manager-Profil zu den Abonnements wechseln und diesen den Endpunkt hinzufügen. Im folgenden Beispiel wird gezeigt, wie dies mit einer öffentlichen IP-Adresse implementiert wird.

```powershell
Set-AzContext -SubscriptionId $EndpointSubscription
$ip = Get-AzPublicIpAddress -Name $IpAddressName -ResourceGroupName $EndpointRG

Set-AzContext -SubscriptionId $trafficmanagerSubscription
New-AzTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Aktualisieren eines Traffic Manager-Endpunkts

Es gibt zwei Möglichkeiten zum Aktualisieren eines vorhandenen Traffic Manager-Endpunkts:

1. Rufen Sie das Traffic Manager-Profil mithilfe von `Get-AzTrafficManagerProfile` ab, aktualisieren Sie die Endpunkteigenschaften im Profil, und übernehmen Sie anschließend die Änderungen mit `Set-AzTrafficManagerProfile`. Diese Methode hat den Vorteil, dass mehrere Endpunkte in einem einzigen Vorgang aktualisiert werden können.
2. Rufen Sie den Traffic Manager-Endpunkt mithilfe von `Get-AzTrafficManagerEndpoint` ab, aktualisieren Sie die Endpunkteigenschaften, und übernehmen Sie anschließend die Änderungen mit `Set-AzTrafficManagerEndpoint`. Diese Methode ist einfacher, da keine Indizierung im Endpunktarray im Profil erforderlich ist.

### <a name="example-1-updating-endpoints-using-get-aztrafficmanagerprofile-and-set-aztrafficmanagerprofile"></a>Beispiel 1: Aktualisieren von Endpunkten mithilfe von `Get-AzTrafficManagerProfile` und `Set-AzTrafficManagerProfile`

In diesem Beispiel ändern wir die Priorität für zwei Endpunkte innerhalb eines vorhandenen Profils.

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$TmProfile.Endpoints[0].Priority = 2
$TmProfile.Endpoints[1].Priority = 1
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-updating-an-endpoint-using-get-aztrafficmanagerendpoint-and-set-aztrafficmanagerendpoint"></a>Beispiel 2: Aktualisieren eines Endpunkts mithilfe von `Get-AzTrafficManagerEndpoint` und `Set-AzTrafficManagerEndpoint`

In diesem Beispiel ändern wir die Gewichtung für einen Endpunkt innerhalb eines vorhandenen Profils.

```powershell
$endpoint = Get-AzTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Aktivieren und Deaktivieren von Endpunkten und Profilen

Mit Traffic Manager können einzelne Endpunkte und sogar ganze Profile aktiviert und deaktiviert werden.
Diese Änderungen können durch Abrufen, Aktualisieren und Festlegen des Endpunkts oder der Profilressourcen vorgenommen werden. Um diese allgemeinen Vorgänge zu vereinfachen, werden sie auch über dedizierte Cmdlets unterstützt.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Beispiel 1: Aktivieren und Deaktivieren eines Traffic Manager-Profils

Verwenden Sie zum Aktivieren eines Traffic Manager-Profils `Enable-AzTrafficManagerProfile`. Das Profil kann mithilfe eines Profilobjekts angegeben werden. Das Profilobjekt kann über die Pipeline oder über den Parameter „-TrafficManagerProfile“ übergeben werden. In diesem Beispiel geben wir das Profil durch den Profil- und Ressourcegruppennamen an.

```powershell
Enable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

So deaktivieren Sie ein Traffic Manager-Profil:

```powershell
Disable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Das Cmdlet „Disable-AzureTrafficManagerProfile“ fordert zur Bestätigung auf. Diese Aufforderung kann mit dem Parameter '-Force' unterdrückt werden.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Beispiel 2: Aktivieren und Deaktivieren eines Traffic Manager-Endpunkts

Verwenden Sie zum Aktivieren eines Traffic Manager-Endpunkts `Enable-AzTrafficManagerEndpoint`. Es gibt zwei Möglichkeiten zum Angeben des Endpunkts:

1. Mithilfe eines TrafficManagerEndpoint-Objekts, das über die Pipeline übergeben wird, oder mithilfe des Parameters „-TrafficManagerEndpoint“
2. Mithilfe des Endpunktnamens, des Endpunkttyps, des Profilnamens und des Ressourcengruppennamens:

```powershell
Enable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Beim Deaktivieren eines Traffic Manager-Endpunkts gehen Sie ähnlich vor:

```powershell
Disable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Wie bei `Disable-AzTrafficManagerProfile` fordert das Cmdlet `Disable-AzTrafficManagerEndpoint` zur Bestätigung auf. Diese Aufforderung kann mit dem Parameter '-Force' unterdrückt werden.

## <a name="delete-a-traffic-manager-endpoint"></a>Löschen eines Traffic Manager-Endpunkts

Verwenden Sie zum Entfernen einzelner Endpunkte das Cmdlet `Remove-AzTrafficManagerEndpoint`:

```powershell
Remove-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Das Cmdlet fordert Sie zur Bestätigung auf. Diese Aufforderung kann mit dem Parameter '-Force' unterdrückt werden.

## <a name="delete-a-traffic-manager-profile"></a>Löschen eines Traffic Manager-Profils

Verwenden Sie zum Löschen eines Traffic Manager-Profils das Cmdlet `Remove-AzTrafficManagerProfile`. Geben Sie dabei den Profilnamen und den Ressourcengruppennamen an:

```powershell
Remove-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Das Cmdlet fordert Sie zur Bestätigung auf. Diese Aufforderung kann mit dem Parameter '-Force' unterdrückt werden.

Das zu löschende Profil kann auch mithilfe eines Profilobjekts angegeben werden:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile [-Force]
```

Diese Sequenz kann auch weitergeleitet werden:

```powershell
Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Nächste Schritte

[Traffic Manager-Überwachung](traffic-manager-monitoring.md)

[Leistungsüberlegungen zu Traffic Manager](traffic-manager-performance-considerations.md)
