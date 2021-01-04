---
title: 'Azure PowerShell-Skriptbeispiel: Erstellen von benutzerdefinierten WAF-Regeln'
description: 'Azure PowerShell-Skriptbeispiel: Erstellen von benutzerdefinierten Web Application Firewall-Regeln'
author: vhorne
ms.service: application-gateway
ms.topic: sample
ms.date: 6/7/2019
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 312f052671036d8153dd19fcf4e559e825fd8464
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93396990"
---
# <a name="create-web-application-firewall-waf-custom-rules-with-azure-powershell"></a>Erstellen von benutzerdefinierten Web Application Firewall-Regeln (WAF) mit Azure PowerShell

Dieses Skript erstellt eine Web Application Firewall-Instanz für Application Gateway, die benutzerdefinierte Regeln verwendet. Die benutzerdefinierte Regel blockiert Datenverkehr, wenn der Anforderungsheader den Benutzer-Agent *evilbot* enthält.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="azure-powershell-module"></a>Azure PowerShell-Modul

Wenn Sie Azure PowerShell lokal installieren und verwenden möchten, müssen Sie für dieses Skript mindestens Version 2.1.0 des Azure PowerShell-Moduls verwenden.

1. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu finden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu.
2. Führen Sie `Connect-AzAccount` aus, um eine Verbindung mit Azure herzustellen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Beispielskript

[!code-powershell[main](../../../powershell_scripts/application-gateway/waf-rules/waf-custom-rules.ps1 "Custom WAF rules")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Führen Sie den folgenden Befehl aus, um die Ressourcengruppe, das Anwendungsgateway und alle zugehörigen Ressourcen zu entfernen.

```powershell
Remove-AzResourceGroup -Name CustomRulesTest
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Dieses Skript verwendet die folgenden Befehle zum Erstellen der Bereitstellung. Jedes Element in der Tabelle ist mit der befehlsspezifischen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Erstellt die Subnetzkonfiguration. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Erstellt das virtuelle Netzwerk mit den Subnetzkonfigurationen. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Erstellt die öffentliche IP-Adresse für das Anwendungsgateway. |
| [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Erstellt die Konfiguration, die einem Subnetz das Anwendungsgateway zuordnet. |
| [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | Erstellt die Konfiguration, die dem Anwendungsgateway eine öffentliche IP-Adresse zuordnet. |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Weist einen Port zu, der für den Zugriff auf das Anwendungsgateway verwendet wird. |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Erstellt einen Back-End-Pool für ein Anwendungsgateway. |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | Konfiguriert Einstellungen für einen Back-End-Pool. |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Erstellt einen Listener. |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Erstellt eine Routingregel. |
| [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | Angeben von Ebene und Kapazität für ein Anwendungsgateway. |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | Erstellen eines Anwendungsgateways |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Entfernt eine Ressourcengruppe und alle darin enthaltenen Ressourcen. |
|[New-AzApplicationGatewayAutoscaleConfiguration](/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|Erstellt eine Konfiguration für die automatische Skalierung für Application Gateway.|
|[New-AzApplicationGatewayFirewallMatchVariable](/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|Erstellt eine Übereinstimmungsvariable für die Firewallbedingung.|
|[New-AzApplicationGatewayFirewallCondition](/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|Erstellt eine Übereinstimmungsbedingung für die benutzerdefinierte Regel.|
|[New-AzApplicationGatewayFirewallCustomRule](/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|Erstellt eine neue benutzerdefinierte Regel für die Application Gateway-Firewallrichtlinie.|
|[New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|Erstellt eine Application Gateway-Firewallrichtlinie.|
|[New-AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|Erstellt eine WAF-Konfiguration für ein Anwendungsgateway.|

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu benutzerdefinierten WAF-Regeln finden Sie unter [Custom rules for Web Application Firewall](../../web-application-firewall/ag/custom-waf-rules-overview.md) (Benutzerdefinierte Regeln für Web Application Firewall).
- Weitere Informationen zum Azure PowerShell-Modul finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/).
- Zusätzliche PowerShell-Skriptbeispiele zu Anwendungsgateways finden Sie in der [Dokumentation zu Azure Application Gateway](../powershell-samples.md).