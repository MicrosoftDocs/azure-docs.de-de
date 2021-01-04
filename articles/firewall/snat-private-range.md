---
title: Azure Firewall –Private SNAT-IP-Adressbereiche
description: Sie können IP-Adressbereiche für SNAT konfigurieren.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/16/2020
ms.author: victorh
ms.openlocfilehash: 858343b6c5081b52d9e93909f9d52eaccd88a584
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660269"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure Firewall –Private SNAT-IP-Adressbereiche

Azure Firewall bietet eine SNAT für sämtlichen ausgehenden Datenverkehr an öffentliche IP-Adressen. Azure Firewall bietet standardmäßig kein SNAT mit Netzwerkregeln, wenn die Ziel-IP-Adresse ein privater IP-Adressbereich gemäß [IANA RFC 1918](https://tools.ietf.org/html/rfc1918) ist. Anwendungsregeln werden immer mithilfe eines [transparenten Proxys](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) unabhängig von der IP-Zieladresse angewendet.

Diese Logik funktioniert gut, wenn Sie Datenverkehr direkt an das Internet weiterleiten. Wenn Sie jedoch [Tunnelerzwingung](forced-tunneling.md) aktiviert haben, wird der Internet-gebundene Datenverkehr an eine der privaten IP-Adressen der Firewall in AzureFirewallSubnet übersetzt, wodurch die Quelle vor Ihrer lokalen Firewall verborgen wird.

Wenn Ihre Organisation einen öffentlichen IP-Adressbereich für private Netzwerke verwendet, leitet Azure Firewall den Datenverkehr per SNAT an eine der privaten IP-Adressen der Firewall in AzureFirewallSubnet weiter. Sie können Azure Firewall jedoch so konfigurieren, dass Ihr öffentlicher IP-Adressbereich **nicht** per SNAT weitergeleitet wird. Eine einzelne IP-Adresse können Sie beispielsweise so angeben: `192.168.1.10`. Einen Bereich von IP-Adressen können Sie wie folgt angeben: `192.168.1.0/24`.

- Wenn Sie Azure Firewall so konfigurieren möchten, dass SNAT unabhängig von der Ziel-IP-Adresse **niemals** angewendet wird, verwenden Sie **0.0.0.0/0** als privaten IP-Adressbereich. Mit dieser Konfiguration kann Azure Firewall niemals Datenverkehr direkt an das Internet weiterleiten. 

- Wenn Sie die Firewall so konfigurieren möchten, dass SNAT unabhängig von der Ziel-Adresse **immer** angewendet wird, verwenden Sie **255.255.255.255/32** als privaten IP-Adressbereich.

> [!IMPORTANT]
> Wenn Sie Ihre eigenen privaten IP-Adressbereiche festlegen und die standardmäßigen IANA RFC 1918-Adressbereiche beibehalten möchten, stellen Sie sicher, dass Ihre benutzerdefinierte Liste den IANA RFC 1918-Bereich noch enthält. 

## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>Konfigurieren von privaten SNAT-IP-Adressbereichen – Azure PowerShell

Sie können mithilfe von Azure PowerShell private IP-Adressbereiche für die Firewall angeben.

### <a name="new-firewall"></a>Neue Firewall

Bei einer neuen Firewall lautet der Azure PowerShell-Befehl so:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> „IANAPrivateRanges“ wird auf die aktuellen Standardeinstellungen von Azure Firewall erweitert, während die anderen Bereiche hinzugefügt werden. Damit der IANAPrivateRanges-Standard in Ihrer privaten Bereichsspezifikation beibehalten wird, muss er in Ihrer `PrivateRange`-Spezifikation verbleiben, wie in den folgenden Beispielen gezeigt wird.

Weitere Informationen finden Sie unter [New-AzFirewall](/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Vorhandene Firewall

Zum Konfigurieren einer vorhandenen Firewall verwenden Sie die folgenden Azure PowerShell-Befehle:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Vorlagen

Sie können im Abschnitt `additionalProperties` Folgendes hinzufügen:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>Konfigurieren von privaten SNAT-IP-Adressbereichen – Azure-Portal

Sie können mithilfe des Azure-Portals private IP-Adressbereiche für die Firewall angeben.

1. Wählen Sie Ihre Ressourcengruppe und dann Ihre Firewall aus.
2. Wählen Sie auf der Seite **Übersicht** die Option **Private IP-Bereiche** aus und dann den Standardwert **IANA RFC 1918**.

   Die Seite **Präfixe privater IP-Adressen bearbeiten** wird geöffnet:

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="Präfixe privater IP-Adressen bearbeiten":::

1. Standardmäßig ist **IANAPrivateRanges** konfiguriert.
2. Bearbeiten Sie die privaten IP-Adressbereiche für Ihre Umgebung, und wählen Sie dann **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Azure Firewall-Tunnelerzwingung](forced-tunneling.md).