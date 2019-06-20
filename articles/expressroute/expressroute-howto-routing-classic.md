---
title: 'Konfigurieren des Peerings für eine Verbindung – ExpressRoute: Azure – klassisch | Microsoft-Dokumentation'
description: In diesem Artikel werden Sie durch die Schritte zum Erstellen und Bereitstellen des privaten, öffentlichen und Microsoft-Peerings einer ExpressRoute-Verbindung geführt. Außerdem wird veranschaulicht, wie Sie den Status überprüfen, Updates durchführen oder Peerings für die Verbindung löschen.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: d1662d17f37e668e989103989df9de49036bab6a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64726215"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>Erstellen und Ändern des Peerings für eine ExpressRoute-Verbindung (klassisch)
> [!div class="op_single_selector"]
> * [Azure-Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure-Befehlszeilenschnittstelle](howto-routing-cli.md)
> * [Video – Privates Peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video – Öffentliches Peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video – Microsoft-Peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassisch)](expressroute-howto-routing-classic.md)
> 

In diesem Artikel werden Sie durch die Schritte zum Erstellen und Verwalten der Routingkonfiguration bzw. des Peerings einer ExpressRoute-Verbindung mithilfe von PowerShell und dem klassischen Bereitstellungsmodell geführt. In den Schritten unten wird auch veranschaulicht, wie Sie den Status prüfen, ein Update durchführen oder Peerings für eine ExpressRoute-Verbindung löschen oder deren Bereitstellung aufheben. Sie können ein, zwei oder alle drei Peerings (Azure privat, Azure öffentlich und Microsoft) für eine ExpressRoute-Verbindung konfigurieren. Sie können Peerings in beliebiger Reihenfolge konfigurieren. Sie müssen jedoch sicherstellen, dass Sie die Konfiguration jedes Peerings einzeln nacheinander durchführen. 

Diese Anweisungen gelten nur für Verbindungen, die über Dienstanbieter erstellt wurden, die Layer 2-Konnektivitätsdienste anbieten. Wenn Sie einen Dienstanbieter nutzen, der verwaltete Layer 3-Dienste anbietet (meist ein IP-VPN, z.B. MPLS), übernimmt Ihr Konnektivitätsanbieter die Konfiguration und Verwaltung des Routings für Sie.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Informationen zu Azure-Bereitstellungsmodellen**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configuration-prerequisites"></a>Konfigurationsvoraussetzungen

* Stellen Sie sicher, dass Sie vor Beginn der Konfiguration die Seiten [Voraussetzungen](expressroute-prerequisites.md), [Routinganforderungen](expressroute-routing.md) und [Workflows](expressroute-workflows.md) gelesen haben.
* Sie benötigen eine aktive ExpressRoute-Verbindung. Führen Sie die Schritte zum [Erstellen einer ExpressRoute-Verbindung](expressroute-howto-circuit-classic.md) aus, und lassen Sie sie vom Konnektivitätsanbieter aktivieren, bevor Sie fortfahren. Die ExpressRoute-Verbindung muss sich im Zustand „provisioned“ und „enabled“ befinden, damit Sie die unten beschriebenen Cmdlets ausführen können.

### <a name="download-the-latest-powershell-cmdlets"></a>Herunterladen der neuesten PowerShell-Cmdlets

Installieren Sie die aktuellen Versionen der PowerShell-Module für die Azure-Dienstverwaltung und das ExpressRoute-Modul. Wenn Sie das folgende Beispiel verwenden, beachten Sie, dass die Versionsnummer (in diesem Beispiel 5.1.1) sich ändert, sobald neue Versionen der Cmdlets veröffentlicht werden.

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

Eine Schrittanleitung zum Konfigurieren des Computers für die Verwendung der Azure PowerShell-Module finden Sie unter [Erste Schritte mit Azure PowerShell-Cmdlets](/powershell/azure/overview).

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

## <a name="azure-private-peering"></a>Privates Azure-Peering

Dieser Abschnitt enthält Anweisungen zum Erstellen, Abrufen, Aktualisieren und Löschen der privaten Azure-Peeringkonfiguration für eine ExpressRoute-Verbindung. 

### <a name="to-create-azure-private-peering"></a>So erstellen Sie ein privates Azure-Peering

1. **Erstellen Sie eine ExpressRoute-Verbindung.**

   Führen Sie die Schritte zum Erstellen einer [ExpressRoute-Verbindung](expressroute-howto-circuit-classic.md) aus, und lassen Sie sie vom Konnektivitätsanbieter bereitstellen. Wenn Ihr Konnektivitätsanbieter verwaltete Layer 3-Dienste im Angebot hat, können Sie anfordern, dass der Anbieter für Sie das private Azure-Peering aktiviert. In diesem Fall müssen Sie die Anweisungen in den nächsten Abschnitten nicht befolgen. Falls Ihr Konnektivitätsanbieter das Routing für Sie nicht verwaltet, müssen Sie nach dem Erstellen der Verbindung die unten angegebenen Anweisungen befolgen.
2. **Überprüfen Sie die ExpressRoute-Verbindung, um sicherzustellen, dass sie bereitgestellt wurde.**
   
   Überprüfen Sie, ob die ExpressRoute-Verbindung bereitgestellt (Provisioned) und aktiviert (Enabled) wurde.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Rückgabe:

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Vergewissern Sie sich, dass für die Verbindung „Provisioned“ und „Enabled“ angezeigt wird. Wenn dies nicht der Fall ist, wenden Sie sich an Ihren Konnektivitätsanbieter, um die Verbindung auf den erforderlichen Zustand und Status festlegen zu lassen.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **Konfigurieren Sie das private Azure-Peering für die Verbindung.**

   Stellen Sie sicher, dass Sie über die folgenden Elemente verfügen, bevor Sie mit den nächsten Schritten fortfahren:
   
   * Ein /30-Subnetz für die primäre Verknüpfung. Dieses darf nicht Teil eines Adressraums sein, der für virtuelle Netzwerke reserviert ist.
   * Ein /30-Subnetz für die sekundäre Verknüpfung. Dieses darf nicht Teil eines Adressraums sein, der für virtuelle Netzwerke reserviert ist.
   * Eine gültige VLAN-ID zum Einrichten dieses Peerings. Stellen Sie sicher, dass kein anderes Peering der Verbindung die gleiche VLAN-ID verwendet.
   * AS-Nummer für Peering. Sie können sowohl AS-Nummern mit 2 Byte als auch mit 4 Byte verwenden. Sie können eine private AS-Nummer für dieses Peering verwenden. Achten Sie darauf, dass Sie nicht 65515 verwenden.
   * Einen MD5-Hash, wenn Sie sich für dessen Einsatz entscheiden. **Optional**.
     
   Sie können Sie das folgende Beispiel verwenden, um das private Azure-Peering für Ihre Verbindung zu konfigurieren:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
   ```    

   Wenn Sie einen MD5-Hash verwenden möchten, konfigurieren Sie das private Peering für Ihre Verbindung mithilfe des folgenden Beispiels:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
   ```
     
   > [!IMPORTANT]
   > Stellen Sie sicher, dass Sie Ihre AS-Nummer als Peering-ASN angeben, nicht als Kunden-ASN.
   > 

### <a name="to-view-azure-private-peering-details"></a>So zeigen Sie Details zum privaten Azure-Peering an

Sie können die Konfigurationsdetails mit dem folgenden Cmdlet anzeigen:

```powershell
Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

Rückgabe:

```
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 100
```

### <a name="to-update-azure-private-peering-configuration"></a>So aktualisieren Sie die Konfiguration für privates Azure-Peering

Sie können einen beliebigen Teil der Konfiguration mit dem folgenden Cmdlet aktualisieren. Im folgenden Beispiel wird die VLAN-ID der Verbindung von 100 in 500 geändert.

```powershell
Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"
```

### <a name="to-delete-azure-private-peering"></a>So löschen Sie ein privates Azure-Peering

Sie können Ihre Peeringkonfiguration entfernen, indem Sie das folgende Cmdlet ausführen. Sie müssen sicherstellen, dass die Verknüpfungen aller virtuellen Netzwerke mit der ExpressRoute-Verbindung aufgehoben werden, bevor Sie dieses Cmdlet ausführen.

```powershell
Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

## <a name="azure-public-peering"></a>Öffentliches Azure-Peering

Dieser Abschnitt enthält Anweisungen zum Erstellen, Abrufen, Aktualisieren und Löschen der öffentlichen Azure-Peeringkonfiguration für eine ExpressRoute-Verbindung.

> [!NOTE]
> Öffentliches Azure-Peering gilt für neue Leitungen als veraltet.
>

### <a name="to-create-azure-public-peering"></a>So erstellen Sie ein öffentliches Azure-Peering

1. **Erstellen Sie eine ExpressRoute-Verbindung.**

   Führen Sie die Schritte zum Erstellen einer [ExpressRoute-Verbindung](expressroute-howto-circuit-classic.md) aus, und lassen Sie sie vom Konnektivitätsanbieter bereitstellen. Wenn Ihr Konnektivitätsanbieter verwaltete Layer 3-Dienste im Angebot hat, können Sie die Aktivierung für das private Azure-Peering anfordern. In diesem Fall müssen Sie die Anweisungen in den nächsten Abschnitten nicht befolgen. Falls Ihr Konnektivitätsanbieter das Routing für Sie nicht verwaltet, müssen Sie nach dem Erstellen der Verbindung die unten angegebenen Anweisungen befolgen.
2. **Überprüfen Sie die ExpressRoute-Verbindung, um sicherzustellen, dass sie bereitgestellt wurde.**

   Sie müssen zuerst überprüfen, ob die ExpressRoute-Verbindung bereitgestellt (Provisioned) und aktiviert (Enabled) wurde.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Rückgabe:

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Vergewissern Sie sich, dass für die Verbindung „Provisioned“ und „Enabled“ angezeigt wird. Wenn dies nicht der Fall ist, wenden Sie sich an Ihren Konnektivitätsanbieter, um die Verbindung auf den erforderlichen Zustand und Status festlegen zu lassen.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
4. **Konfigurieren Sie das öffentliche Azure-Peering für die Verbindung.**
   
   Stellen Sie vorab sicher, dass die folgenden Informationen vorliegen:
   
   * Ein /30-Subnetz für die primäre Verknüpfung. Dies muss ein gültiges öffentliches IPv4-Präfix sein.
   * Ein /30-Subnetz für die sekundäre Verknüpfung. Dies muss ein gültiges öffentliches IPv4-Präfix sein.
   * Eine gültige VLAN-ID zum Einrichten dieses Peerings. Stellen Sie sicher, dass kein anderes Peering der Verbindung die gleiche VLAN-ID verwendet.
   * AS-Nummer für Peering. Sie können sowohl AS-Nummern mit 2 Byte als auch mit 4 Byte verwenden.
   * Einen MD5-Hash, wenn Sie sich für dessen Einsatz entscheiden. **Optional**.

   > [!IMPORTANT]
   > Stellen Sie sicher, dass Sie Ihre AS-Nummer als Peering-ASN angeben, nicht als Kunden-ASN.
   >  
     
   Sie können das folgende Beispiel verwenden, um das öffentliche Azure-Peering für Ihre Verbindung zu konfigurieren:

   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
   ```
     
   Wenn Sie einen MD5-Hash verwenden möchten, konfigurieren Sie Ihre Verbindung mithilfe des folgenden Beispiels:
     
   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
   ```
     
### <a name="to-view-azure-public-peering-details"></a>So zeigen Sie Details zum öffentlichen Azure-Peering an

Um Konfigurationsdetails anzuzeigen, verwenden Sie das folgende Cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

Rückgabe:

```powershell
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 131.107.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 131.107.0.4/30
State                          : Enabled
VlanId                         : 200
```

### <a name="to-update-azure-public-peering-configuration"></a>Aktualisieren der Konfiguration für öffentliches Azure-Peering

Sie können einen beliebigen Teil der Konfiguration mit dem folgenden Cmdlet aktualisieren. In diesem Beispiel wird die VLAN-ID der Verbindung von 200 in 600 geändert.

```powershell
Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"
```

Vergewissern Sie sich, dass für die Verbindung „Provisioned“ und „Enabled“ angezeigt wird. 
### <a name="to-delete-azure-public-peering"></a>So löschen Sie ein öffentliches Azure-Peering

Sie können Ihre Peeringkonfiguration entfernen, indem Sie das folgende Cmdlet ausführen:

```powershell
Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

## <a name="microsoft-peering"></a>Microsoft-Peering

Dieser Abschnitt enthält Anweisungen zum Erstellen, Abrufen, Aktualisieren und Löschen der Microsoft-Peeringkonfiguration für eine ExpressRoute-Verbindung. 

### <a name="to-create-microsoft-peering"></a>So erstellen Sie Microsoft-Peering

1. **Erstellen Sie eine ExpressRoute-Verbindung.**
  
   Führen Sie die Schritte zum Erstellen einer [ExpressRoute-Verbindung](expressroute-howto-circuit-classic.md) aus, und lassen Sie sie vom Konnektivitätsanbieter bereitstellen. Wenn Ihr Konnektivitätsanbieter verwaltete Layer 3-Dienste im Angebot hat, können Sie anfordern, dass der Anbieter für Sie das private Azure-Peering aktiviert. In diesem Fall müssen Sie die Anweisungen in den nächsten Abschnitten nicht befolgen. Falls Ihr Konnektivitätsanbieter das Routing für Sie nicht verwaltet, müssen Sie nach dem Erstellen der Verbindung die unten angegebenen Anweisungen befolgen.
2. **Überprüfen Sie die ExpressRoute-Verbindung, um sicherzustellen, dass sie bereitgestellt wurde.**

   Vergewissern Sie sich, dass für die Verbindung „Provisioned“ und „Enabled“ angezeigt wird. 
   
   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Rückgabe:
   
   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Vergewissern Sie sich, dass für die Verbindung „Provisioned“ und „Enabled“ angezeigt wird. Wenn dies nicht der Fall ist, wenden Sie sich an Ihren Konnektivitätsanbieter, um die Verbindung auf den erforderlichen Zustand und Status festlegen zu lassen.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **Konfigurieren Sie das Microsoft-Peering für die Verbindung.**
   
    Stellen Sie vorab sicher, dass die folgenden Informationen vorliegen:
   
   * Ein /30-Subnetz für die primäre Verknüpfung. Dies muss ein gültiges öffentliches IPv4-Präfix sein, das sich in Ihrem Besitz befindet und über eine RIR/IRR-Registrierung verfügt.
   * Ein /30-Subnetz für die sekundäre Verknüpfung. Dies muss ein gültiges öffentliches IPv4-Präfix sein, das sich in Ihrem Besitz befindet und über eine RIR/IRR-Registrierung verfügt.
   * Eine gültige VLAN-ID zum Einrichten dieses Peerings. Stellen Sie sicher, dass kein anderes Peering der Verbindung die gleiche VLAN-ID verwendet.
   * AS-Nummer für Peering. Sie können sowohl AS-Nummern mit 2 Byte als auch mit 4 Byte verwenden.
   * Angekündigte Präfixe: Sie müssen eine Liste mit allen Präfixen bereitstellen, die Sie über die BGP-Sitzung ankündigen möchten. Nur öffentliche IP-Adresspräfixe werden akzeptiert. Sie können eine durch Trennzeichen getrennte Liste senden, wenn Sie planen, einen Satz mit Präfixen zu senden. Diese Präfixe müssen über eine RIR/IRR-Registrierung für Sie verfügen.
   * Kunden-ASN: Wenn Sie Präfixe ankündigen, die nicht für die Peering-AS-Nummer registriert sind, können Sie die AS-Nummer angeben, unter der sie registriert sind. **Optional**.
   * Routing-Registrierungsname: Sie können den RIR/IRR-Wert angeben, unter dem die AS-Nummer und die Präfixe registriert sind.
   * Einen MD5-Hash, wenn Sie sich für dessen Einsatz entscheiden. **Optional.**
     
   Führen Sie das folgende Cmdlet aus, um das Microsoft-Peering für Ihre Verbindung zu konfigurieren:
 
   ```powershell
   New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
   ```

### <a name="to-view-microsoft-peering-details"></a>So zeigen Sie die Details zum Microsoft-Peering an

Sie können die Konfigurationsdetails mit dem folgenden Cmdlet anzeigen:

```powershell
Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```
Rückgabe:

```powershell
AdvertisedPublicPrefixes       : 123.0.0.0/30
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 2245
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : ARIN
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 300
```

### <a name="to-update-microsoft-peering-configuration"></a>So aktualisieren Sie die Konfiguration des Microsoft-Peerings

Sie können einen beliebigen Teil der Konfiguration mit dem folgenden Cmdlet aktualisieren:

```powershell
Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
```

### <a name="to-delete-microsoft-peering"></a>So löschen Sie das Microsoft-Peering

Sie können Ihre Peeringkonfiguration entfernen, indem Sie das folgende Cmdlet ausführen:

```powershell
Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```

## <a name="next-steps"></a>Nächste Schritte

Nächster Schritt: [Verknüpfen eines VNet mit einer ExpressRoute-Verbindung](expressroute-howto-linkvnet-classic.md).

* Weitere Informationen zu Workflows finden Sie unter [ExpressRoute-Workflows](expressroute-workflows.md).
* Weitere Informationen zum Verbindungspeering finden Sie unter [ExpressRoute-Verbindungen und Routingdomänen](expressroute-circuit-peerings.md).
