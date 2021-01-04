---
title: Bereitstellen einer Azure Firewall mit mehreren öffentlichen IP-Adressen mithilfe von PowerShell
description: In diesem Artikel erfahren Sie, wie Sie eine Azure Firewall mit mehreren öffentlichen IP-Adressen mithilfe von Azure PowerShell bereitstellen.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: cbad025a0d0c4d679ea9cdc7557c81b5145798fd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023668"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Bereitstellen einer Azure Firewall mit mehreren öffentlichen IP-Adressen mithilfe von Azure PowerShell

Diese Funktion ermöglicht die folgenden Szenarios:

- **DNAT:** Sie können mehrere Standardportinstanzen auf Ihre Back-End-Server übersetzen. Wenn Sie beispielsweise über zwei öffentliche IP-Adressen verfügen, können Sie den TCP-Port 3389 (RDP) für beide IP-Adressen übersetzen.
- **SNAT:** Für ausgehende SNAT-Verbindungen stehen zusätzliche Ports zur Verfügung, was die Gefahr einer Überlastung des SNAT-Ports verringert. Aktuell wählt Azure Firewall die öffentliche IP-Quelladresse für eine Verbindung nach dem Zufallsprinzip aus. Wenn Sie in Ihrem Netzwerk über eine nachgeschaltete Filterung verfügen, müssen Sie alle öffentlichen IP-Adressen zulassen, die mit Ihrer Firewall verbunden sind. Verwenden Sie ggf. ein [Präfix für öffentliche IP-Adressen](../virtual-network/public-ip-address-prefix.md), um diese Konfiguration zu vereinfachen.
 
Azure Firewall mit mehreren öffentlichen IP-Adressen ist über das Azure-Portal, Azure PowerShell, die Azure CLI, REST und Vorlagen verfügbar. Sie können eine Azure Firewall mit bis zu 250 öffentlichen IP-Adressen bereitstellen.

Die folgenden Azure PowerShell-Beispiele zeigen, wie Sie öffentliche IP-Adressen für Azure Firewall konfigurieren, hinzufügen und entfernen können.

> [!NOTE]
> Die erste ipConfiguration kann nicht von der Seite für die Konfiguration der öffentlichen IP-Adressen in Azure Firewall entfernt werden. Wenn Sie die IP-Adresse ändern möchten, können Sie Azure PowerShell verwenden.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Erstellen einer Firewall mit mindestens zwei öffentlichen IP-Adressen

In diesem Beispiel wird eine Firewall erstellt, die an das virtuelle Netzwerk *vnet* mit zwei öffentlichen IP-Adressen angefügt ist.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Hinzufügen einer öffentlichen IP-Adresse zu einer vorhandenen Firewall

In diesem Beispiel ist die öffentliche IP-Adresse *azFwPublicIp1* an die Firewall angefügt.

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Entfernen einer öffentlichen IP-Adresse aus einer vorhandenen Firewall

In diesem Beispiel ist die öffentliche IP-Adresse *azFwPublicIp1* von der Firewall getrennt.

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Erstellen einer Azure Firewall mit mehreren öffentlichen IP-Adressen – Resource Manager-Vorlage](quick-create-multiple-ip-template.md)
