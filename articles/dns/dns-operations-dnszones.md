---
title: Verwalten von DNS-Zonen in Azure DNS – PowerShell | Microsoft-Dokumentation
description: Sie können DNS-Zonen mithilfe der Azure Powershell verwalten. In diesem Artikel wird das Aktualisieren, Löschen und Erstellen von DNS-Zonen in Azure DNS beschrieben.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: rohink
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6abcca9d9888dc8968d7233e7aee6cd76aa215f7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965747"
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>Verwalten von DNS-Zonen mithilfe der PowerShell

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Klassische Azure-Befehlszeilenschnittstelle](./dns-operations-dnszones-cli.md)
> * [Azure-Befehlszeilenschnittstelle](dns-operations-dnszones-cli.md)

In diesem Artikel wird gezeigt, wie DNS-Zonen mithilfe von Azure PowerShell verwaltet werden. Sie können Ihre DNS-Zonen auch mithilfe der plattformübergreifenden [Azure CLI](dns-operations-dnszones-cli.md) oder über das Azure-Portal verwalten.

Dieses Handbuch befasst sich insbesondere mit öffentlichen DNS-Zonen. Informationen zur Verwendung von Azure PowerShell zum Verwalten von privaten Zonen in Azure DNS finden Sie unter [Erste Schritte mit Azure DNS Private Zones mithilfe von Azure PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>Erstellen einer DNS-Zone

Eine DNS-Zone wird mit dem `New-AzureRmDnsZone` -Cmdlet erstellt.

Im folgenden Beispiel wird in der Ressourcengruppe namens *MyResourceGroup* eine DNS-Zone namens *contoso.com* erstellt:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

Das folgende Beispiel zeigt, wie Sie eine DNS-Zone mit zwei [Azure Resource Manager-Tags](dns-zones-records.md#tags) erstellen: *project = demo* und *env = test*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

Azure DNS unterstützt auch private DNS-Zonen.  Weitere Informationen zu privaten DNS-Zonen finden Sie unter [Using Azure DNS for private domains](private-dns-overview.md) (Verwenden von Azure DNS für private Domänen). Ein Beispiel zum Erstellen einer privaten DNS-Zone finden Sie unter [Erste Schritte mit privaten Azure DNS-Zonen mithilfe von PowerShell](./private-dns-getstarted-powershell.md).

## <a name="get-a-dns-zone"></a>Abrufen einer DNS-Zone

Verwenden Sie zum Abrufen einer DNS-Zone das Cmdlet `Get-AzureRmDnsZone`. Dieser Vorgang gibt ein DNS-Zonenobjekt zurück, das einer vorhandenen Zone in Azure DNS entspricht. Das Objekt enthält Daten über die Zone (z.B. die Anzahl der Ressourceneintragssätze), jedoch nicht die Ressourceneintragssätze selbst (siehe `Get-AzureRmDnsRecordSet`).

```powershell
Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-8ec2-f4879750d201
Tags                  : {project, env}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org.,
                        ns4-01.azure-dns.info.}
NumberOfRecordSets    : 2
MaxNumberOfRecordSets : 5000
```

## <a name="list-dns-zones"></a>Auflisten von DNS-Zonen

Durch Auslassen des Zonennamens in `Get-AzureRmDnsZone` können Sie alle Zonen in einer Ressourcengruppe auflisten: Dieser Vorgang gibt ein Array von Zonenobjekten zurück.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Durch das Weglassen des Zonennamens und des Ressourcengruppennamens aus `Get-AzureRmDnsZone` können Sie alle Zonen im Azure-Abonnement auflisten.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-zone"></a>Aktualisieren einer DNS-Zone

Änderungen an einer DNS-Zonenressource können mithilfe von `Set-AzureRmDnsZone`vorgenommen werden. Durch dieses Cmdlet wird keine der DNS-Datensatzgruppen in der Zone aktualisiert (siehe [Verwalten von DNS-Einträgen](dns-operations-recordsets.md)). Es wird nur verwendet, um Eigenschaften der Zonenressource selbst zu aktualisieren. Die schreibbaren Zoneneigenschaften sind derzeit auf die [Azure Resource Manager-„Tags“ für die Zonenressource](dns-zones-records.md#tags) beschränkt.

Verwenden Sie eine der folgenden zwei Möglichkeiten, um eine DNS-Zone zu aktualisieren:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Angeben der Zone mithilfe des Zonennamens und der Ressourcengruppe

Mit diesem Ansatz werden die vorhandenen Zonen-Tags durch die angegebenen Werte ersetzt.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>Angeben der Zone mithilfe eines $zone-Objekts

Mit diesem Ansatz werden das vorhandene Zonenobjekt abgerufen, die Tags geändert und dann ein Commit für die Änderungen ausgeführt. Auf diese Weise können vorhandene Tags beibehalten werden.

```powershell
# Get the zone object
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

# Remove an existing tag
$zone.Tags.Remove("project")

# Add a new tag
$zone.Tags.Add("status","approved")

# Commit changes
Set-AzureRmDnsZone -Zone $zone
```

Wenn Sie `Set-AzureRmDnsZone` mit einem $zone-Objekt verwenden, wird durch [ETag-Überprüfungen](dns-zones-records.md#etags) sichergestellt, dass gleichzeitige Änderungen nicht überschrieben werden. Diese Überprüfungen können mithilfe des optionalen Switchs `-Overwrite` unterdrückt werden.

## <a name="delete-a-dns-zone"></a>Löschen einer DNS-Zone

DNS-Zonen können mithilfe des `Remove-AzureRmDnsZone`-Cmdlets gelöscht werden.

> [!NOTE]
> Durch das Löschen einer DNS-Zone werden auch alle DNS-Einträge in der Zone gelöscht. Dieser Vorgang kann nicht rückgängig gemacht werden. Wenn die DNS-Zone verwendet wird, tritt in Diensten, die die Zone verwenden, ein Fehler auf.
>
>Informationen dazu, wie Sie Zonen vor versehentlichem Löschen schützen, finden Sie unter [How to protect DNS zones and records](dns-protect-zones-recordsets.md) (Schützen von DNS-Zonen und -Einträgen).


Verwenden Sie eine der beiden folgenden Möglichkeiten, um eine DNS-Zone zu entfernen:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Geben Sie die Zone mithilfe des Zonennamens und des Ressourcengruppennamens an.

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Angeben der Zone mithilfe eines $zone-Objekts

Sie können festlegen, dass die Zone mithilfe eines von `Get-AzureRmDnsZone` zurückgegebenen `$zone`-Objekts gelöscht werden soll.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

Das Zonenobjekt kann auch weitergeleitet werden, anstatt als Parameter übergeben zu werden:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone

```

Wie bei `Set-AzureRmDnsZone` ermöglicht die Angabe der Zone mithilfe eines `$zone`-Objekts die ETag-Überprüfung, um sicherzustellen, dass gleichzeitige Änderungen nicht gelöscht werden. Verwenden Sie den `-Overwrite`-Switch, um diese Überprüfungen zu unterdrücken.

## <a name="confirmation-prompts"></a>Bestätigungsaufforderungen

Die Cmdlets `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` und `Remove-AzureRmDnsZone` unterstützen jeweils Bestätigungsaufforderungen.

Sowohl `New-AzureRmDnsZone` als auch `Set-AzureRmDnsZone` fordern eine Bestätigung an, wenn die PowerShell-Einstellungsvariable `$ConfirmPreference` einen Wert von `Medium` oder weniger hat. Aufgrund der potenziell wesentlichen Auswirkungen der Löschung einer DNS-Zone forder das `Remove-AzureRmDnsZone`-Cmdlet zur Bestätigung auf, wenn die PowerShell-Variable `$ConfirmPreference` einen anderen Wert als `None` aufweist.

Da `High` der Standardwert für `$ConfirmPreference` ist, fordert nur `Remove-AzureRmDnsZone` standardmäßig zur Bestätigung auf.

Die aktuelle Einstellung für `$ConfirmPreference` kann mithilfe des `-Confirm`-Parameters überschrieben werden. Bei Angabe von `-Confirm` oder `-Confirm:$True` fordert das Cmdlet vor der Ausführung eine Bestätigung an. Bei Verwendung von `-Confirm:$False` fordert das Cmdlet keine Bestätigung an.

Weitere Informationen zu `-Confirm` und `$ConfirmPreference` finden Sie unter [About Preference Variables](/powershell/module/microsoft.powershell.core/about/about_preference_variables) (Informationen zu Einstellungsvariablen).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [Ressourceneintragssätze und Einträge in Ihrer DNS-Zone verwalten](dns-operations-recordsets.md).
<br>
Erfahren Sie, wie Sie [Ihre Domäne an Azure DNS delegieren](dns-domain-delegation.md).
<br>
Machen Sie sich mit der [Azure DNS PowerShell-Referenzdokumentation](/powershell/module/azurerm.dns) vertraut.