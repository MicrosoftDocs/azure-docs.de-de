---
title: 'Verknüpfen eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung: PowerShell: klassisch: Azure | Microsoft-Dokumentation'
description: Dieses Dokument bietet Ihnen eine Übersicht über das Verknüpfen virtueller Netzwerke (VNETs) mit ExpressRoute-Verbindungen über das klassische Bereitstellungsmodell und PowerShell.
services: expressroute
documentationcenter: na
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: cherylmc
ms.openlocfilehash: 21676ff329613f792d6570713f044bb7440e58d4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370627"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Verbinden eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung mithilfe von PowerShell (klassisch)
> [!div class="op_single_selector"]
> * [Azure-Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure-Befehlszeilenschnittstelle](howto-linkvnet-cli.md)
> * [Video – Azure-Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisch)](expressroute-howto-linkvnet-classic.md)
>

In diesem Artikel können Sie virtuelle Netzwerke (VNETs) mithilfe von PowerShell mit Azure-ExpressRoute-Verbindungen verknüpfen. Ein einzelnes VNET kann mit bis zu vier ExpressRoute-Leitungen verknüpft werden. Erstellen Sie anhand der Schritte in diesem Artikel einen neuen Link für jede ExpressRoute-Leitung, mit der Sie eine Verbindung herstellen. Die ExpressRoute-Leitungen können sich im gleichen Abonnement, in verschiedenen Abonnements oder in einer Kombination aus beidem befinden. Dieser Artikel bezieht sich auf virtuelle Netzwerke, die mithilfe des klassischen Bereitstellungsmodells erstellt wurden.

Sie können bis zu 10 virtuelle Netzwerke mit einer ExpressRoute-Verbindung verknüpfen. Alle virtuellen Netzwerke müssen sich in derselben geopolitischen Region befinden. Wenn Sie das ExpressRoute Premium-Add-On aktiviert haben, können Sie eine größere Anzahl von virtuellen Netzwerken mit der ExpressRoute-Verbindung oder virtuelle Netzwerke verknüpfen, die sich in anderen geopolitischen Regionen befinden. Weitere Informationen zum Premium-Add-On finden Sie in den [häufig gestellten Fragen](expressroute-faqs.md).

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Informationen zu Azure-Bereitstellungsmodellen**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configuration-prerequisites"></a>Konfigurationsvoraussetzungen

* Lesen Sie, bevor Sie mit der Konfiguration beginnen, die Seiten [Voraussetzungen](expressroute-prerequisites.md), [Routinganforderungen](expressroute-routing.md) und [Workflows](expressroute-workflows.md).
* Sie benötigen eine aktive ExpressRoute-Verbindung.
   * Führen Sie die Schritte zum [Erstellen einer ExpressRoute-Verbindung](expressroute-howto-circuit-classic.md) aus, und lassen Sie sie vom Konnektivitätsanbieter aktivieren.
   * Stellen Sie sicher, dass privates Azure-Peering für die Verbindung konfiguriert ist. Informationen zum Routing finden Sie unter [Konfigurieren des Routings](expressroute-howto-routing-classic.md) .
   * Vergewissern Sie sich, dass das private Azure-Peering konfiguriert wurde und das BGP-Peering zwischen Ihrem Netzwerk und Microsoft aktiv ist, damit End-to-End-Konnektivität bereitgestellt werden kann.
   * Sie müssen ein virtuelles Netzwerk und ein virtuelles Netzwerkgateway erstellt und vollständig bereitgestellt haben. Befolgen Sie die Anweisungen zum [Konfigurieren eines virtuellen Netzwerks für ExpressRoute](expressroute-howto-vnet-portal-classic.md).

### <a name="download-the-latest-powershell-cmdlets"></a>Herunterladen der neuesten PowerShell-Cmdlets

Installieren Sie die aktuellen Versionen der PowerShell-Module für die Azure-Dienstverwaltung und das ExpressRoute-Modul. Beachten Sie bei Verwendung des folgenden Beispiels, dass sich die Versionsnummer (hier: 5.1.1) ändert, wenn neuere Versionen der Cmdlets veröffentlicht werden.

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

Weitere Informationen zu PowerShell und eine ausführliche Anleitung zum Konfigurieren des Computers für die Verwendung der Azure PowerShell-Module finden Sie unter [Erste Schritte mit Azure PowerShell-Cmdlets](/powershell/azure/overview).

### <a name="sign-in"></a>Anmelden

Verwenden Sie die folgenden Beispiele, um sich bei Ihrem Azure-Konto anzumelden:

1. Öffnen Sie die PowerShell-Konsole mit erhöhten Rechten, und stellen Sie eine Verbindung mit Ihrem Konto her.

   ```powershell
   Connect-AzAccount
   ```
2. Überprüfen Sie die Abonnements für das Konto.

   ```powershell
   Get-AzSubscription
   ```
3. Wenn Sie über mehr als ein Abonnement verfügen, wählen Sie das Abonnement aus, das Sie verwenden möchten.

   ```powershell
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```

4. Verwenden Sie als nächstes das folgende Cmdlet, um PowerShell Ihr Azure-Abonnement für das klassische Bereitstellungsmodell hinzuzufügen.

   ```powershell
   Add-AzureAccount
   ```

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Herstellen einer Verbindung zwischen einem virtuellen Netzwerk in demselben Abonnement und einer Verbindung
Sie können das folgende Cmdlet verwenden, um ein virtuelles Netzwerk mit einer ExpressRoute-Verbindung zu verknüpfen. Stellen Sie sicher, dass das Gateway für das virtuelle Netzwerk erstellt wurde und für das Erstellen von Verknüpfungen bereit ist, bevor Sie das Cmdlet ausführen.

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>Entfernen der Verknüpfung eines virtuellen Netzwerks mit einer Verbindung
Sie können das folgende Cmdlet verwenden, um die Verknüpfung eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung zu entfernen. Stellen Sie sicher, dass das aktuelle Abonnement für das angegebene virtuelle Netzwerk ausgewählt ist. 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Herstellen einer Verbindung zwischen einem virtuellen Netzwerk in einem anderen Abonnement und einer Verbindung
Sie können eine ExpressRoute-Verbindung für mehrere Abonnements freigeben. Die folgende Abbildung zeigt eine einfache schematische Darstellung der Freigabe von Lasten für ExpressRoute-Verbindungen für mehrere Abonnements.

Jede der kleineren Clouds innerhalb der großen Cloud stellt Abonnements dar, die zu verschiedenen Abteilungen innerhalb einer Organisation gehören. Jede der Abteilungen innerhalb der Organisation kann ihr eigenes Abonnement zum Bereitstellen von Diensten verwenden, für die Verbindung mit dem lokalen Netzwerk können die Abteilungen jedoch eine einzelne gemeinsam genutzte ExpressRoute-Verbindung verwenden. Eine einzelne Abteilung (in diesem Beispiel: IT) kann die ExpressRoute-Verbindung besitzen. Andere Abonnements innerhalb der Organisation können die ExpressRoute-Verbindung nutzen.

> [!NOTE]
> Konnektivitäts- und Bandbreitengebühren für die dedizierte Verbindung werden dem Besitzer der ExpressRoute-Verbindung in Rechnung gestellt. Alle virtuellen Netzwerke verwenden gemeinsam dieselbe Bandbreite.
> 
> 

![Abonnementübergreifende Konnektivität](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Verwaltung
Der *Verbindungsbesitzer* ist der Administrator/Co-Administrator des Abonnements, in dem die ExpressRoute-Verbindung erstellt wird. Der Besitzer der Verbindung kann Administratoren/Co-Administratoren anderer Abonnements ( *Verbindungsbenutzer*genannt) für die Nutzung der dedizierten Verbindung in seinem Besitz autorisieren. Verbindungsbenutzer, die für die Nutzung der ExpressRoute-Verbindung einer Organisation autorisiert sind, können ein virtuelles Netzwerk in ihrem Abonnement mit der ExpressRoute-Verbindung verknüpfen, sobald sie autorisiert wurden.

Der Besitzer der Verbindung hat die Möglichkeit, Autorisierungen jederzeit zu ändern und aufzuheben. Das Aufheben einer Autorisierung führt dazu, dass alle Verknüpfungen aus dem Abonnement gelöscht werden, dessen Zugriff aufgehoben wurde.

### <a name="circuit-owner-operations"></a>Aktionen als Verbindungsbesitzer

**Erstellen einer Autorisierung**

Der Verbindungsbesitzer autorisiert die Administratoren anderer Abonnements für die Nutzung der angegebenen Verbindung. Im folgenden Beispiel ermöglicht der Administrator der Verbindung (Contoso IT) dem Administrator eines anderen Abonnements (Dev-Test) das Verknüpfen von bis zu zwei virtuellen Netzwerken mit der Verbindung. Der Contoso-IT-Administrator ermöglicht dies durch die Angabe der Microsoft-ID von Dev-Test. Das Cmdlet sendet keine E-Mail an die angegebene Microsoft-ID. Der Verbindungsbesitzer muss den Besitzer des anderen Abonnements explizit darüber benachrichtigen, dass die Autorisierung erfolgt ist.

```powershell
New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
```

  Rückgabe:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : **********************************
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Überprüfen von Autorisierungen**

Mit dem folgenden Cmdlet kann der Besitzer einer Verbindung alle für eine bestimmte Verbindung ausgestellten Autorisierungen überprüfen:

```powershell
Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
```
  Rückgabe:

  ```powershell
  Description         : EngineeringTeam
  Limit               : 3
  LinkAuthorizationId : ####################################
  MicrosoftIds        : engadmin@contoso.com
  Used                : 1

  Description         : MarketingTeam
  Limit               : 1
  LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
  MicrosoftIds        : marketingadmin@contoso.com
  Used                : 0

  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : salesadmin@contoso.com
  Used                : 2
  ```

**Aktualisieren von Autorisierungen**

Mit dem folgenden Cmdlet kann der Besitzer einer Verbindung die Autorisierungen bearbeiten:

```powershell
Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
```

  Rückgabe:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 5
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Löschen von Autorisierungen**

Mit dem folgenden Cmdlet kann der Besitzer einer Verbindung Autorisierungen für einen Benutzer widerrufen oder löschen:

```powershell
Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"
```

### <a name="circuit-user-operations"></a>Aktionen als Verbindungsbenutzer

**Überprüfen von Autorisierungen**

Mit dem folgenden Cmdlet können Benutzer einer Verbindung Autorisierungen überprüfen.

```powershell
Get-AzureAuthorizedDedicatedCircuit
```

  Rückgabe:

  ```powershell
  Bandwidth                        : 200
  CircuitName                      : ContosoIT
  Location                         : Washington DC
  MaximumAllowedLinks              : 2
  ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  ServiceProviderName              : equinix
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  UsedLinks                        : 0
  ```

**Abrufen von Verknüpfungsautorisierungen**

Mit dem folgenden Cmdlet können Benutzer einer Verbindung eine Verknüpfungsautorisierung abrufen:

```powershell
New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'
```

  Rückgabe:

  ```powershell
  State VnetName
  ----- --------
  Provisioned SalesVNET1
  ```

Führen Sie diesen Befehl im neu verknüpften Abonnement für das virtuelle Netzwerk aus:

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über ExpressRoute finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).
