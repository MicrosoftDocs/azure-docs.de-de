---
title: Erstellen einer Azure Network Watcher-Instanz | Microsoft Docs
description: Erfahren Sie, wie Sie Network Watcher in einer Azure-Region aktivieren.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: c97f6dff17896b8a58c17aed9063e0b2b5733503
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64681581"
---
# <a name="create-an-azure-network-watcher-instance"></a>Erstellen einer Azure Network Watcher-Instanz

Network Watcher ist ein regionaler Dienst, mit dem Sie Bedingungen auf der Ebene von Netzwerkszenarien in Azure überwachen und diagnostizieren können. Die Überwachung auf Szenarioebene erlaubt die umfassende Diagnose von Problemen auf Netzwerkebene. Die Tools zur Netzwerkdiagnose und -visualisierung von Network Watcher helfen Ihnen dabei, Ihr Netzwerk in Azure zu verstehen, Diagnosen durchzuführen und Einblicke zu gewinnen. Network Watcher wird durch die Erstellung einer Network Watcher-Ressource aktiviert. Diese Ressource ermöglicht Ihnen die Verwendung von Network Watcher-Funktionen.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>Network Watcher wird automatisch aktiviert
Beim Erstellen oder Aktualisieren eines virtuellen Netzwerks in Ihrem Abonnement wird Network Watcher automatisch in der Region Ihres virtuellen Netzwerks aktiviert. Die automatische Aktivierung von Network Watcher hat keine Auswirkungen auf Ihre Ressourcen oder die damit verbundene Gebühr.

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Deaktivieren der automatischen Aktivierung von Network Watcher
Wenn Sie die automatische Aktivierung von Network Watcher deaktivieren möchten, können Sie hierfür die folgenden Befehle ausführen:

> [!WARNING]
> Die Deaktivierung der automatischen Aktivierung von Network Watcher stellt eine dauerhafte Änderung dar. Sollten Sie diese Funktion nach einer Deaktivierung wieder aktivieren wollen, müssen Sie hierfür den [Support kontaktieren](https://azure.microsoft.com/support/options/).

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>Erstellen einer Network Watcher-Instanz im Portal

Navigieren Sie zu **Alle Dienste** > **Netzwerk** > **Network Watcher**. Sie können alle Abonnements auswählen, für die Sie Network Watcher aktivieren möchten. Mit dieser Aktion erstellen Sie eine Network Watcher-Instanz in jeder verfügbaren Region.

![Erstellen einer Network Watcher-Instanz](./media/network-watcher-create/figure1.png)

Wenn Sie Network Watcher über das Portal aktivieren, wird der Name der Network Watcher-Instanz automatisch auf *NetworkWatcher_Regionsname* festgelegt, wobei *Regionsname* der Azure-Region entspricht, in der die Instanz aktiviert wurde. Ein in der Region „USA, Westen-Mitte“ aktivierter Network Watcher erhält beispielsweise den Namen *NetworkWatcher_westcentralus*.

Die Network Watcher-Instanz wird automatisch in einer Ressourcengruppe namens *NetworkWatcherRG* erstellt. Diese Ressourcengruppe wird erstellt, wenn sie nicht bereits vorhanden ist.

Wenn Sie den Namen einer Network Watcher-Instanz und der Ressourcengruppe, der sie angehört, anpassen möchten, können Sie die in den folgenden Abschnitten beschriebenen PowerShell-, Azure CLI-, REST-API- oder ARMClient-Methoden verwenden. Bei jeder Option muss die Ressourcengruppe bereits vorhanden sein, bevor Sie die Network Watcher-Instanz darin erstellen können.  

## <a name="create-a-network-watcher-with-powershell"></a>Erstellen einer Network Watcher-Instanz mit PowerShell

Um eine Network Watcher-Instanz zu erstellen, führen Sie das folgende Beispiel aus:

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Erstellen einer Network Watcher-Instanz mit der Azure CLI

Um eine Network Watcher-Instanz zu erstellen, führen Sie das folgende Beispiel aus:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Erstellen einer Network Watcher-Instanz mit der REST-API

Der ARMClient dient zum Aufrufen der REST-API mithilfe von PowerShell. Sie finden den ARMClient auf der [Chocolatey-Seite unter ARMClient](https://chocolatey.org/packages/ARMClient).

### <a name="log-in-with-armclient"></a>Anmelden mit ARMClient

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>Erstellen der Network Watcher-Instanz

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, nachdem Sie über eine Instanz von Network Watcher verfügen, welche Features Ihnen zur Verfügung stehen:

* [Topologie](network-watcher-topology-overview.md)
* [Paketerfassung](network-watcher-packet-capture-overview.md)
* [IP-Datenflussüberprüfung](network-watcher-ip-flow-verify-overview.md)
* [Nächster Hop](network-watcher-next-hop-overview.md)
* [Sicherheitsgruppenansicht](network-watcher-security-group-view-overview.md)
* [NSG-Datenflussprotokollierung](network-watcher-nsg-flow-logging-overview.md)
* [Beheben von Problemen bei Virtual Network-Gateways](network-watcher-troubleshoot-overview.md)

Sobald eine Network Watcher-Instanz erstellt wurde, können Sie die Paketerfassung auf virtuellen Computern aktivieren. Informationen hierzu finden Sie unter [Verwenden der Paketerfassung für die proaktive Netzwerküberwachung mit Warnungen und Azure Functions](network-watcher-alert-triggered-packet-capture.md).
