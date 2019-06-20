---
title: 'Tutorial: Erstellen einer privaten Azure DNS-Zone mithilfe von Azure PowerShell'
description: In diesem Tutorial erstellen und testen Sie eine private DNS-Zone und einen Eintrag in Azure DNS. Dies ist eine Schritt-für-Schritt-Anleitung zum Erstellen und Verwalten Ihrer ersten privaten DNS-Zone und Ihres ersten DNS-Eintrags mithilfe von Azure PowerShell.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 2b88454f06d2e2d42298e52feeaa26ae9d1a4902
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730258"
---
# <a name="tutorial-create-an-azure-dns-private-zone-using-azure-powershell"></a>Tutorial: Erstellen einer privaten Azure DNS-Zone mithilfe von Azure PowerShell

In diesem Tutorial wird Schritt für Schritt beschrieben, wie Sie Ihre erste private DNS-Zone und Ihren ersten DNS-Eintrag mithilfe von Azure PowerShell erstellen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Eine DNS-Zone wird zum Hosten der DNS-Einträge für eine bestimmte Domäne verwendet. Wenn Sie eine Domäne in Azure DNS hosten möchten, müssen Sie eine DNS-Zone für diesen Domänennamen erstellen. Jeder DNS-Eintrag für Ihre Domäne wird dann in dieser DNS-Zone erstellt. Um eine private DNS-Zone in Ihrem virtuellen Netzwerk zu veröffentlichen, geben Sie die Liste mit den virtuellen Netzwerken an, für die das Auflösen von Einträgen in der Zone zulässig ist.  Diese werden als *virtuelle Auflösungsnetzwerke* bezeichnet. Sie können auch ein virtuelles Netzwerk angeben, für das Azure DNS Hostnameneinträge verwaltet, wenn ein virtueller Computer erstellt wird, sich seine IP-Adresse ändert oder der virtuelle Computer gelöscht wird.  Dies wird als *virtuelles Registrierungsnetzwerk* bezeichnet.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer privaten DNS-Zone
> * Erstellen von virtuellen Testcomputern
> * Erstellen eines zusätzlichen DNS-Eintrags
> * Testen der privaten Zone

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

Sie können für dieses Tutorial auch die [Azure-Befehlszeilenschnittstelle](private-dns-getstarted-cli.md) verwenden.

<!--- ## Get the Preview PowerShell modules
These instructions assume you have already installed and signed in to Azure PowerShell, including ensuring you have the required modules for the Private Zone feature. -->

<!---[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)] -->

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>Ressourcengruppe erstellen

Erstellen Sie zunächst eine Ressourcengruppe für die DNS-Zone: 

```azurepowershell
New-AzResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-dns-private-zone"></a>Erstellen einer privaten DNS-Zone

Eine DNS-Zone wird erstellt, indem das `New-AzDnsZone`-Cmdlet mit dem Wert *Private* für den Parameter **ZoneType** verwendet wird. Im folgenden Beispiel wird eine DNS-Zone mit dem Namen **private.contoso.com** in der Ressourcengruppe **MyAzureResourceGroup** erstellt, und die DNS-Zone wird für das virtuelle Netzwerk **MyAzureVnet** verfügbar gemacht.

Die Zone wird bei Auslassung des Parameters **ZoneType** als öffentliche Zone erstellt. Er ist also erforderlich, wenn Sie eine private Zone erstellen möchten. 

```azurepowershell
$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

New-AzDnsZone -Name private.contoso.com -ResourceGroupName MyAzureResourceGroup `
   -ZoneType Private `
   -RegistrationVirtualNetworkId @($vnet.Id)
```

Wenn Sie eine Zone nur für die Namensauflösung (keine automatische Hostnamenerstellung) erstellen möchten, können Sie anstelle des Parameters *RegistrationVirtualNetworkId* den Parameter *ResolutionVirtualNetworkId* verwenden.

> [!NOTE]
> Sie können die automatisch erstellten Einträge für die Hostnamen nicht anzeigen. Später werden Sie jedoch Tests ausführen, um sicherzustellen, dass sie vorhanden sind.

### <a name="list-dns-private-zones"></a>Auflisten von privaten DNS-Zonen

Durch Auslassen des Zonennamens in `Get-AzDnsZone` können Sie alle Zonen in einer Ressourcengruppe auflisten: Dieser Vorgang gibt ein Array von Zonenobjekten zurück.

```azurepowershell
Get-AzDnsZone -ResourceGroupName MyAzureResourceGroup
```

Durch das Weglassen des Zonennamens und des Ressourcengruppennamens aus `Get-AzDnsZone` können Sie alle Zonen im Azure-Abonnement auflisten.

```azurepowershell
Get-AzDnsZone
```

## <a name="create-the-test-virtual-machines"></a>Erstellen der virtuellen Testcomputer

Erstellen Sie nun zwei virtuelle Computer, um die private DNS-Zone zu testen:

```azurepowershell
New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM01" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389

New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM02" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389
```

Dies nimmt einige Minuten in Anspruch.

## <a name="create-an-additional-dns-record"></a>Erstellen eines zusätzlichen DNS-Eintrags

Sie erstellen Ressourceneintragssätze mit dem Cmdlet `New-AzDnsRecordSet`. Im folgenden Beispiel wird ein Eintrag mit dem relativen Namen **db** in der DNS-Zone **private.contoso.com** in der Ressourcengruppe **MyAzureResourceGroup** erstellt. Der vollqualifizierte Name des Ressourceneintragssatzes lautet **db.private.contoso.com**. Der Eintragstyp ist „A“, die IP-Adresse lautet 10.2.0.4, und die Gültigkeitsdauer beträgt 3.600 Sekunden.

```azurepowershell
New-AzDnsRecordSet -Name db -RecordType A -ZoneName private.contoso.com `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>Anzeigen von DNS-Einträgen

Listen Sie die DNS-Einträge in Ihrer Zone mit diesem Befehl auf:

```azurepowershell
Get-AzDnsRecordSet -ZoneName private.contoso.com -ResourceGroupName MyAzureResourceGroup
```
Beachten Sie, dass die automatisch erstellten A-Einträge für Ihre beiden virtuellen Testcomputer nicht angezeigt werden.

## <a name="test-the-private-zone"></a>Testen der privaten Zone

Nun können Sie die Namensauflösung für die private Zone **private.contoso.com** testen.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurieren von virtuellen Computern zum Zulassen eingehender ICMP

Sie können den Ping-Befehl zum Testen der Namensauflösung verwenden. Konfigurieren Sie daher die Firewall auf beiden virtuellen Computern, um eingehende ICMP-Pakete zuzulassen.

1. Stellen Sie eine Verbindung mit „myVM01“ her, und öffnen Sie ein Windows PowerShell-Fenster mit Administratorrechten.
2. Führen Sie den folgenden Befehl aus:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Wiederholen Sie den Schritt für „myVM02“.

### <a name="ping-the-vms-by-name"></a>Pingen der virtuellen Computer anhand des Namens

1. Führen Sie an der Windows PowerShell-Eingabeaufforderung von „myVM02“ einen Ping-Befehl für „myVM01“ aus. Verwenden Sie dazu den automatisch registrierten Hostnamen:
   ```
   ping myVM01.private.contoso.com
   ```
   Eine ähnliche Ausgabe wie die folgende sollte angezeigt werden:
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Pingen Sie jetzt den Namen **db**, den Sie zuvor erstellt haben:
   ```
   ping db.private.contoso.com
   ```
   Eine ähnliche Ausgabe wie die folgende sollte angezeigt werden:
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Löschen aller Ressourcen

Löschen Sie die Ressourcengruppe **MyAzureResourceGroup**, wenn Sie sie nicht mehr benötigen, um die in diesem Tutorial erstellten Ressourcen zu löschen.

```azurepowershell
Remove-AzResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine private DNS-Zone bereitgestellt, einen DNS-Eintrag erstellt und die Zone getestet.
Im folgenden Tutorial finden Sie weitere Informationen zu privaten DNS-Zonen:

> [!div class="nextstepaction"]
> [Verwenden von Azure DNS für private Domänen](private-dns-overview.md)




