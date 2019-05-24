---
title: Erstellen einer privaten Azure DNS-Zone mit der Azure-Befehlszeilenschnittstelle
description: In diesem Tutorial erstellen und testen Sie eine private DNS-Zone und einen Eintrag in Azure DNS. Dies ist eine Schritt-für-Schritt-Anleitung zum Erstellen und Verwalten Ihrer ersten privaten DNS-Zone und Ihres ersten DNS-Eintrags mithilfe der Azure CLI.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 2758817d58fdd2e80b302b5f833308dbde1a6b63
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65916054"
---
# <a name="create-an-azure-dns-private-zone-using-the-azure-cli"></a>Erstellen einer privaten Azure DNS-Zone mit der Azure-Befehlszeilenschnittstelle

In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle Ihre erste private DNS-Zone und Ihren ersten DNS-Eintrag erstellen.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Eine DNS-Zone wird zum Hosten der DNS-Einträge für eine bestimmte Domäne verwendet. Wenn Sie eine Domäne in Azure DNS hosten möchten, müssen Sie eine DNS-Zone für diesen Domänennamen erstellen. Jeder DNS-Eintrag für Ihre Domäne wird dann in dieser DNS-Zone erstellt. Um eine private DNS-Zone in Ihrem virtuellen Netzwerk zu veröffentlichen, geben Sie die Liste mit den virtuellen Netzwerken an, für die das Auflösen von Einträgen in der Zone zulässig ist.  Diese werden als *virtuelle Auflösungsnetzwerke* bezeichnet. Sie können auch ein virtuelles Netzwerk angeben, für das Azure DNS Hostnameneinträge verwaltet, wenn ein virtueller Computer erstellt wird, sich seine IP-Adresse ändert oder der virtuelle Computer gelöscht wird.  Dies wird als *virtuelles Registrierungsnetzwerk* bezeichnet.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer privaten DNS-Zone
> * Erstellen von virtuellen Testcomputern
> * Erstellen eines zusätzlichen DNS-Eintrags
> * Testen der privaten Zone

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

Sie können dieses Tutorial auch mit [Azure PowerShell](private-dns-getstarted-powershell.md) durcharbeiten.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>Ressourcengruppe erstellen

Erstellen Sie zunächst eine Ressourcengruppe für die DNS-Zone: 

```azurecli
az group create --name MyAzureResourceGroup --location "East US"
```

## <a name="create-a-dns-private-zone"></a>Erstellen einer privaten DNS-Zone

Eine DNS-Zone wird erstellt, indem der `az network dns zone create`-Befehl mit dem Wert *Private* für den Parameter **ZoneType** verwendet wird. Im folgenden Beispiel wird eine DNS-Zone mit dem Namen **private.contoso.com** in der Ressourcengruppe **MyAzureResourceGroup** erstellt, und die DNS-Zone wird für das virtuelle Netzwerk **MyAzureVnet** verfügbar gemacht.

Die Zone wird bei Auslassung des Parameters **ZoneType** als öffentliche Zone erstellt. Er ist also erforderlich, wenn Sie eine private Zone erstellen möchten.

```azurecli
az network vnet create \
  --name myAzureVNet \
  --resource-group MyAzureResourceGroup \
  --location eastus \
  --address-prefix 10.2.0.0/16 \
  --subnet-name backendSubnet \
  --subnet-prefixes 10.2.0.0/24

az network dns zone create -g MyAzureResourceGroup \
   -n private.contoso.com \
  --zone-type Private \
  --registration-vnets myAzureVNet
```

Wenn Sie eine Zone nur für die Namensauflösung (keine automatische Hostnamenerstellung) erstellen möchten, können Sie anstelle des Parameters *registration-vnets* den Parameter *resolution-vnets* verwenden.

> [!NOTE]
> Sie können die automatisch erstellten Einträge für die Hostnamen nicht anzeigen. Später werden Sie jedoch Tests ausführen, um sicherzustellen, dass sie vorhanden sind.

### <a name="list-dns-private-zones"></a>Auflisten von privaten DNS-Zonen

Verwenden Sie zum Aufzählen von DNS-Zonen `az network dns zone list`. Entsprechende Hilfeinformationen finden Sie unter `az network dns zone list --help`.

Wenn Sie die Ressourcengruppe angeben, werden nur die Zonen innerhalb der Ressourcengruppe aufgelistet:

```azurecli
az network dns zone list \
  --resource-group MyAzureResourceGroup
```

Wenn Sie die Ressourcengruppe weglassen, werden alle Zonen im Abonnement aufgelistet:

```azurecli
az network dns zone list 
```

## <a name="create-the-test-virtual-machines"></a>Erstellen der virtuellen Testcomputer

Erstellen Sie nun zwei virtuelle Computer, um die private DNS-Zone zu testen:

```azurecli
az vm create \
 -n myVM01 \
 --admin-username test-user \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --image win2016datacenter

az vm create \
 -n myVM02 \
 --admin-username test-user \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --image win2016datacenter
```

Dies nimmt einige Minuten in Anspruch.

## <a name="create-an-additional-dns-record"></a>Erstellen eines zusätzlichen DNS-Eintrags

Verwenden Sie zum Erstellen eines DNS-Eintrags den Befehl `az network dns record-set [record type] add-record`. Hilfe zum Hinzufügen zu A-Einträgen finden Sie unter `azure network dns record-set A add-record --help`.

 Im folgenden Beispiel wird ein Eintrag mit dem relativen Namen **db** in der DNS-Zone **private.contoso.com** in der Ressourcengruppe **MyAzureResourceGroup** erstellt. Der vollqualifizierte Name des Ressourceneintragssatzes lautet **db.private.contoso.com**. Der Eintragstyp lautet „A“ mit der IP-Adresse „10.2.0.4“.

```azurecli
az network dns record-set a add-record \
  -g MyAzureResourceGroup \
  -z private.contoso.com \
  -n db \
  -a 10.2.0.4
```

### <a name="view-dns-records"></a>Anzeigen von DNS-Einträgen

Listen Sie die DNS-Einträge in Ihrer Zone mit diesem Befehl auf:

```azurecli
az network dns record-set list \
  -g MyAzureResourceGroup \
  -z private.contoso.com
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

```azurecli
az group delete --name MyAzureResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine private DNS-Zone bereitgestellt, einen DNS-Eintrag erstellt und die Zone getestet.
Im folgenden Tutorial finden Sie weitere Informationen zu privaten DNS-Zonen:

> [!div class="nextstepaction"]
> [Verwenden von Azure DNS für private Domänen](private-dns-overview.md)
