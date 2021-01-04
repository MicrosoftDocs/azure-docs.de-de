---
title: 'Schnellstart: Erstellen einer privaten Azure DNS-Zone mithilfe von Azure PowerShell'
description: In diesem Schnellstart erfahren Sie, wie Sie mithilfe von Azure PowerShell Ihre erste private DNS-Zone und Ihren ersten DNS-Eintrag erstellen und verwalten.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.openlocfilehash: ee6dde6b34cccd415f9bf2052f65dcbe940715c1
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424393"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-azure-powershell"></a>Schnellstart: Erstellen einer privaten Azure DNS-Zone mithilfe von Azure PowerShell

In diesem Artikel wird Schritt für Schritt beschrieben, wie Sie Ihre erste private DNS-Zone und Ihren ersten DNS-Eintrag mithilfe von Azure PowerShell erstellen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Eine DNS-Zone wird zum Hosten der DNS-Einträge für eine bestimmte Domäne verwendet. Wenn Sie eine Domäne in Azure DNS hosten möchten, müssen Sie eine DNS-Zone für diesen Domänennamen erstellen. Jeder DNS-Eintrag für Ihre Domäne wird dann in dieser DNS-Zone erstellt. Um eine private DNS-Zone in Ihrem virtuellen Netzwerk zu veröffentlichen, geben Sie die Liste mit den virtuellen Netzwerken an, für die das Auflösen von Einträgen in der Zone zulässig ist.  Diese werden als *verknüpfte* virtuelle Netzwerke bezeichnet. Wenn die automatische Registrierung aktiviert ist, aktualisiert Azure DNS auch die Zoneneinträge, sobald ein virtueller Computer erstellt, seine IP-Adresse geändert oder der virtuelle Computer gelöscht wird.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

Sie können für diese Schnellstartanleitung auch die [Azure-Befehlszeilenschnittstelle](private-dns-getstarted-cli.md) verwenden.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>Erstellen der Ressourcengruppe

Erstellen Sie zunächst eine Ressourcengruppe für die DNS-Zone: 

```azurepowershell
New-AzResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-private-dns-zone"></a>Erstellen einer privaten DNS-Zone

Eine DNS-Zone wird mit dem `New-AzPrivateDnsZone` -Cmdlet erstellt.

Im folgenden Beispiel wird ein virtuelles Netzwerk namens **myAzureVNet** erstellt. Anschließend wird eine DNS-Zone mit dem Namen **private.contoso.com** in der Ressourcengruppe **MyAzureResourceGroup** erstellt, die DNS-Zone mit dem virtuellen Netzwerk **MyAzureVnet** verknüpft und die automatische Registrierung aktiviert.

```azurepowershell
Install-Module -Name Az.PrivateDns -force

$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

$zone = New-AzPrivateDnsZone -Name private.contoso.com -ResourceGroupName MyAzureResourceGroup

$link = New-AzPrivateDnsVirtualNetworkLink -ZoneName private.contoso.com `
  -ResourceGroupName MyAzureResourceGroup -Name "mylink" `
  -VirtualNetworkId $vnet.id -EnableRegistration
```

Wenn Sie eine Zone nur für die Namensauflösung erstellen möchten (keine automatische Hostnamenregistrierung), können Sie den Parameter `-EnableRegistration` auslassen.

### <a name="list-dns-private-zones"></a>Auflisten von privaten DNS-Zonen

Durch Auslassen des Zonennamens in `Get-AzPrivateDnsZone` können Sie alle Zonen in einer Ressourcengruppe auflisten: Dieser Vorgang gibt ein Array von Zonenobjekten zurück.

```azurepowershell
$zones = Get-AzPrivateDnsZone -ResourceGroupName MyAzureResourceGroup
$zones
```

Durch das Weglassen des Zonennamens und des Ressourcengruppennamens aus `Get-AzPrivateDnsZone` können Sie alle Zonen im Azure-Abonnement auflisten.

```azurepowershell
$zones = Get-AzPrivateDnsZone
$zones
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

Sie erstellen Ressourceneintragssätze mit dem Cmdlet `New-AzPrivateDnsRecordSet`. Im folgenden Beispiel wird ein Eintrag mit dem relativen Namen **db** in der DNS-Zone **private.contoso.com** in der Ressourcengruppe **MyAzureResourceGroup** erstellt. Der vollqualifizierte Name des Ressourceneintragssatzes lautet **db.private.contoso.com** . Der Eintragstyp ist „A“, die IP-Adresse lautet 10.2.0.4, und die Gültigkeitsdauer beträgt 3.600 Sekunden.

```azurepowershell
New-AzPrivateDnsRecordSet -Name db -RecordType A -ZoneName private.contoso.com `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>Anzeigen von DNS-Einträgen

Listen Sie die DNS-Einträge in Ihrer Zone mit diesem Befehl auf:

```azurepowershell
Get-AzPrivateDnsRecordSet -ZoneName private.contoso.com -ResourceGroupName MyAzureResourceGroup
```

## <a name="test-the-private-zone"></a>Testen der privaten Zone

Nun können Sie die Namensauflösung für die private Zone **private.contoso.com** testen.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurieren von virtuellen Computern zum Zulassen eingehender ICMP

Sie können den Ping-Befehl zum Testen der Namensauflösung verwenden. Konfigurieren Sie daher die Firewall auf beiden virtuellen Computern, um eingehende ICMP-Pakete zuzulassen.

1. Stellen Sie eine Verbindung mit „myVM01“ her, und öffnen Sie ein Windows PowerShell-Fenster mit Administratorrechten.
2. Führen Sie den folgenden Befehl aus:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

Wiederholen Sie den Schritt für „myVM02“.

### <a name="ping-the-vms-by-name"></a>Pingen der virtuellen Computer anhand des Namens

1. Führen Sie an der Windows PowerShell-Eingabeaufforderung von „myVM02“ einen Ping-Befehl für „myVM01“ aus. Verwenden Sie dazu den automatisch registrierten Hostnamen:

   ```
   ping myVM01.private.contoso.com
   ```

   Die angezeigte Ausgabe sollte so ähnlich aussehen wie die folgende:

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

2. Pingen Sie jetzt den Namen **db** , den Sie zuvor erstellt haben:

   ```
   ping db.private.contoso.com
   ```

   Die angezeigte Ausgabe sollte so ähnlich aussehen wie die folgende:

   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milliseconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe **MyAzureResourceGroup** , um die in diesem Artikel erstellten Ressourcen zu löschen, falls sie nicht mehr benötigt werden.

```azurepowershell
Remove-AzResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Azure DNS Private Zones-Szenarien](private-dns-scenarios.md)
