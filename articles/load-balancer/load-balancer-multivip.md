---
title: Mehrere VIPs für einen Clouddienst
titlesuffix: Azure Load Balancer
description: Übersicht über MultiVIP und das Festlegen von mehreren virtuellen IP-Adressen für einen Clouddienst
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: bf5721e206316a4ce576253743e9ac65de47094a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60591769"
---
# <a name="configure-multiple-vips-for-a-cloud-service"></a>Konfigurieren mehrerer VIPs für einen Clouddienst

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Sie können über das öffentliche Internet auf Azure-Clouddienste zugreifen, indem Sie eine von Azure bereitgestellte IP-Adresse verwenden. Diese öffentliche IP-Adresse wird als VIP (virtuelle IP) bezeichnet, da sie nicht mit den VM-Instanzen im Clouddienst, sondern mit dem Azure-Lastenausgleich verknüpft ist. Sie können auf jede VM-Instanz innerhalb eines Clouddiensts über eine einzelne VIP zugreifen.

Es gibt jedoch Szenarios, in denen Sie möglicherweise mehr als eine VIP-Adresse als Einstiegspunkt für den gleichen Clouddienst benötigen. Beispielsweise kann der Clouddienst mehrere Websites hosten, die SSL-Verbindungen über den Standardport 443 erfordern, da jede Website für einen anderen Kunden oder Mandanten gehostet wird. In diesem Szenario benötigen Sie für jede Website eine andere öffentlich zugängliche IP-Adresse. Das folgende Diagramm veranschaulicht eine typische Webhosting-Konfiguration mit mehreren Mandanten, die mehrere SSL-Zertifikate auf dem gleichen öffentlichen Port erfordern.

![Multi-VIP-SSL-Szenario](./media/load-balancer-multivip/Figure1.png)

Im obigen Beispiel verwenden alle VIPs den gleichen öffentlichen Port (443), und der Datenverkehr wird zu einem oder mehreren virtuellen Computern mit Lastenausgleich auf einem eindeutigen privaten Port für die interne IP-Adresse des Clouddiensts umgeleitet, von dem alle Websites gehostet werden.

> [!NOTE]
> Eine weitere Situation, in der die Verwendung mehrerer VIPs erforderlich ist, ist das Hosten mehrerer Listener für SQL-AlwaysOn-Verfügbarkeitsgruppen in der gleichen Gruppe von Virtual Machines.

VIPs sind in der Standardeinstellung dynamisch, das bedeutet, dass die dem Clouddienst tatsächlich zugewiesene IP-Adresse sich mit der Zeit ändern kann. Um dies zu verhindern, können Sie eine VIP-Adresse für Ihren Dienst reservieren. Weitere Informationen zu reservierten virtuellen IP-Adressen finden Sie unter [Reservierte öffentliche IP-Adresse](../virtual-network/virtual-networks-reserved-public-ip.md).

> [!NOTE]
> Informationen zu Preisen für VIPs und reservierte IP-Adressen finden Sie unter [Preise für IP-Adressen](https://azure.microsoft.com/pricing/details/ip-addresses/) .

Sie können PowerShell verwenden, um die vom Clouddienst verwendeten virtuellen IP-Adressen zu überprüfen, um virtuelle IP-Adressen hinzuzufügen und zu entfernen, eine virtuelle IP-Adresse einem Endpunkt zuzuordnen sowie den Lastenausgleich für eine bestimmte virtuelle IP-Adresse zu konfigurieren.

## <a name="limitations"></a>Einschränkungen

Zum derzeitigen Zeitpunkt ist die Multi-VIP-Funktion auf die folgenden Szenarien beschränkt:

* **Nur IaaS**. Sie können Multi-VIP nur für Clouddienste aktivieren, die virtuelle Computer enthalten. Sie können Multi-VIP nicht in PaaS-Szenarien mit Rolleninstanzen verwenden.
* **Nur PowerShell**. Sie können Multi-VIP nur mithilfe von PowerShell verwalten.

Diese Einschränkungen sind nicht dauerhaft und können sich jederzeit ändern. Besuchen Sie diese Seite regelmäßig, um über zukünftige Änderungen informiert zu sein.

## <a name="how-to-add-a-vip-to-a-cloud-service"></a>So fügen Sie einem Clouddienst eine virtuelle IP-Adresse hinzu
Um Ihrem Dienst eine VIP-Adresse hinzuzufügen, führen Sie den folgenden PowerShell-Befehl aus:

```powershell
Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

Dieser Befehl zeigt ein Ergebnis an, das in etwa wie folgt aussieht:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Entfernen einer VIP-Adresse aus einem Clouddienst
Führen Sie den folgenden PowerShell-Befehl aus, um die VIP zu entfernen, die dem Dienst im obigen Skript hinzugefügt wurde:

```powershell
Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

> [!IMPORTANT]
> Sie können eine VIP-Adresse nur entfernen, wenn ihr keine Endpunkte zugeordnet sind.


## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Abrufen von VIP-Informationen aus einem Clouddienst
Um die einem Clouddienst zugeordneten virtuellen IP-Adressen abzurufen, führen Sie das folgende PowerShell-Skript aus:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

Dieses Skript zeigt ein Ergebnis an, das in etwa wie folgt aussieht:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

In diesem Beispiel hat der Clouddienst drei virtuelle IP-Adressen:

* **Vip1** ist die Standard-VIP, da der Wert für "IsDnsProgrammedName" auf "true" festgelegt ist.
* **Vip2** und **Vip3** werden nicht verwendet, da sie über keine IP-Adressen verfügen. Sie werden nur verwendet, wenn Sie der VIP-Adresse einen Endpunkt zuordnen.

> [!NOTE]
> Ihrem Abonnement werden zusätzliche VIPs nur in Rechnung gestellt, wenn sie einem Endpunkt zugeordnet sind. Weitere Informationen zu Preisen finden Sie unter [Preise für IP-Adressen](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>So ordnen Sie eine VIP-Adresse einem Endpunkt zu

Um eine VIP in einem Clouddienst einem Endpunkt zuzuordnen, führen Sie den folgenden PowerShell-Befehl aus:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 |
    Update-AzureVM
```

Durch den Befehl wird ein mit der VIP verknüpfter Endpunkt namens *Vip2* an *Port 80* erstellt und mit dem virtuellen Computer namens *myVM1* in einem Clouddienst mit dem Namen *myService* über *TCP* an Port *8080* verknüpft.

Führen Sie den folgenden PowerShell-Befehl aus, um die Konfiguration zu überprüfen:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

Die Ausgabe sieht in etwa wie im folgenden Beispiel aus:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>So aktivieren Sie den Lastenausgleich für eine bestimmten VIP-Adresse

Sie können eine einzelne VIP für den Lastenausgleich mehreren virtuellen Computern zuordnen. Ein Beispiel: Angenommen, Sie verfügen über einen Clouddienst mit dem Namen *myService* sowie über zwei virtuelle Computer namens *myVM1* und *myVM2*. Der Clouddienst verfügt über mehrere VIPs, eine davon heißt *Vip2*. Wenn Sie sicherstellen möchten, dass für den gesamten Datenverkehr an Port *81* für *Vip2* ein Lastenausgleich zwischen *myVM1* und *myVM2* an Port *8181* durchgeführt wird, führen Sie das folgende PowerShell-Skript aus:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2 -DefaultProbe |
    Update-AzureVM

Get-AzureVM -ServiceName myService -Name myVM2 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe |
    Update-AzureVM
```

Sie können den Lastenausgleich auch zur Verwendung einer anderen VIP aktualisieren. Wenn Sie beispielsweise den folgenden PowerShell-Befehl ausführen, wird festgelegt, dass für den Lastenausgleich eine VIP-Adresse namens „Vip1“ verwendet wird:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1
```

## <a name="next-steps"></a>Nächste Schritte

[Azure Monitor-Protokolle für den Azure Load Balancer](load-balancer-monitor-log.md)

[Lastenausgleich für Internetzugriff (Übersicht)](load-balancer-internet-overview.md)

[Erste Schritte mit Lastenausgleich für Internetzugriff](load-balancer-get-started-internet-arm-ps.md)

[Virtuelle Netzwerke im Überblick](../virtual-network/virtual-networks-overview.md)

[Reservierte IP-REST-APIs](https://msdn.microsoft.com/library/azure/dn722420.aspx)
