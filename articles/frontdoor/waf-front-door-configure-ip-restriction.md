---
title: Konfigurieren einer IP-Einschränkungsregel mit einer Web Application Firewall für Azure Front Door Service
description: Erfahren Sie, wie Sie eine Web Application Firewall-Regel zum Einschränken von IP-Adressen für einen vorhandenen Azure Front Door Service -Endpunkt konfigurieren.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud;tyao
ms.openlocfilehash: 4a0b4a7d088eb7e9d7e329b5a7a5ef9c9fb57335
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729912"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>Konfigurieren einer IP-Einschränkungsregel mit einer Web Application Firewall für Azure Front Door Service
In diesem Artikel wird erläutert, wie Sie IP-Einschränkungsregeln in Web Application Firewall (WAF) für Azure Front Door Service über die Azure-Befehlszeilenschnittstelle, Azure PowerShell oder eine Azure Resource Manager-Vorlage konfigurieren.

Eine auf IP-Adressen basierende Zugriffssteuerungsregel ist eine benutzerdefinierte WAF-Regel, mit der Sie den Zugriff auf Ihre Webanwendungen steuern können, indem Sie eine Liste von IP-Adressen oder IP-Adressbereichen im CIDR-Format (Classless Inter-Domain Routing) angeben.

Standardmäßig kann auf Ihre Webanwendungen über das Internet zugegriffen werden. Wenn Sie den Zugriff auf Clients aus einer Liste von bekannten IP-Adressen oder IP-Adressbereichen beschränken möchten, müssen Sie zwei IP-Abgleichsregeln erstellen. Die erste IP-Abgleichsregel enthält die Liste der IP-Adressen als übereinstimmende Werte und legt die Aktion auf **Allow** fest. Mit der zweiten Regel mit niedrigerer Priorität werden alle anderen IP-Adressen mithilfe des Operators **ALL** blockiert und die Aktion auf **Block** festgelegt. Nachdem eine IP-Einschränkungsregel angewandt wurde, erhalten Anforderungen von Adressen, die nicht in dieser Zulassungsliste enthalten sind, die Antwort „403 (Nicht zulässig)“.  

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Konfigurieren einer WAF-Richtlinie über die Azure-Befehlszeilenschnittstelle

### <a name="prerequisites"></a>Voraussetzungen
Vor dem Konfigurieren einer Richtlinie für IP-Einschränkung müssen Sie zunächst die CLI-Umgebung einrichten und ein Azure Front Door Service-Profil erstellen.

#### <a name="set-up-the-azure-cli-environment"></a>Einrichten der Azure CLI-Umgebung
1. Installieren Sie die [Azure CLI](/cli/azure/install-azure-cli), oder verwenden Sie Azure Cloud Shell. Azure Cloud Shell ist eine kostenlose Bash-Shell, die Sie direkt im Azure-Portal ausführen können. Die Azure CLI ist vorinstalliert und für die Verwendung mit Ihrem Konto konfiguriert. Wählen Sie die Schaltfläche **Ausprobieren** in den folgenden CLI-Befehlen, und melden Sie sich bei Ihrem Azure-Konto in der Cloud Shell-Sitzung an, die geöffnet wird. Nachdem die Sitzung gestartet wurde, geben Sie `az extension add --name front-door` ein, um die Azure Front Door Service-Erweiterung hinzuzufügen.
 2. Wenn Sie die CLI lokal in Bash verwenden, melden Sie sich mit `az login` bei Azure an.

#### <a name="create-an-azure-front-door-service-profile"></a>Erstellen eines Azure Front Door Service-Profils
Gehen Sie zum Erstellen eines Azure Front Door Service-Profils gemäß den Anweisungen unter [Schnellstart: Erstellen Sie eine „Front Door“ für eine hoch verfügbare globale Webanwendung](quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Erstellen einer WAF-Richtlinie

Erstellen Sie mit dem Befehl [az network waf-policy create](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create) eine WAF-Richtlinie. Ersetzen Sie im folgenden Beispiel den Richtliniennamen *IPAllowPolicyExampleCLI* durch einen eindeutigen Richtliniennamen.

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Hinzufügen einer benutzerdefinierten Regel für die IP-Zugriffssteuerung

Fügen Sie mit dem Befehl [az network waf-policy custom-rule create](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) der im vorherigen Schritt erstellten WAF-Richtlinie eine benutzerdefinierte Regel für die IP-Zugriffssteuerung hinzu.

In den folgenden Beispielen:
-  Ersetzen Sie *IPAllowPolicyExampleCLI* durch die zuvor erstellte eindeutige Richtlinie.
-  Ersetzen Sie *ip-address-range-1* und *ip-address-range-2* durch Ihren gewünschten Bereich.

Erstellen Sie zunächst die IP-Zulassungsregel für die angegebenen Adressen.

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch ["<ip-address-range-1>","<ip-address-range-2>"] \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
Erstellen Sie dann eine **Regel zum Blockieren aller IP-Adressen** mit einer niedrigeren Priorität als die vorherige **Zulassungsregel**. Ersetzen Sie *IPAllowPolicyExampleCLI* im folgenden Beispiel erneut durch die eindeutige Richtlinie, die Sie zuvor erstellt haben.

```azurecli
az network waf-policy custom-rule create \
  --name IPDenyAllRule\
  --priority 2 \
  --rule-type MatchRule \
  --match-condition RemoteAddr Any
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
    
### <a name="find-the-id-of-a-waf-policy"></a>Suchen der ID einer WAF-Richtlinie 
Mit dem Befehl [az network waf-policy show](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show) können Sie die ID einer WAF-Richtlinie suchen. Ersetzen Sie *IPAllowPolicyExampleCLI* im folgenden Beispiel durch die eindeutige Richtlinie, die Sie zuvor erstellt haben.

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Verknüpfen einer WAF-Richtlinie mit einem Azure Front Door Service-Front-End-Host

Legen Sie mit dem Befehl [az network front-door update](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) die Azure Front Door Service-ID *WebApplicationFirewallPolicyLink* auf die Richtlinien-ID fest. Ersetzen Sie *IPAllowPolicyExampleCLI* durch die zuvor erstellte eindeutige Richtlinie.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
In diesem Beispiel wird die WAF-Richtlinie auf **FrontendEndpoints[0]** angewandt. Sie können die WAF-Richtlinie mit jedem der Front-Ends verknüpfen.
> [!Note]
> Die Eigenschaft **WebApplicationFirewallPolicyLink** muss nur einmal festgelegt werden, um eine WAF-Richtlinie mit einem Azure Front Door Service-Front-End zu verknüpfen. Nachfolgende Richtlinienaktualisierungen werden automatisch auf das Front-End angewandt.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Konfigurieren einer WAF-Richtlinie über Azure PowerShell

### <a name="prerequisites"></a>Voraussetzungen
Vor dem Konfigurieren einer Richtlinie für IP-Einschränkung müssen Sie zunächst die PowerShell-Umgebung einrichten und ein Azure Front Door Service-Profil erstellen.

#### <a name="set-up-your-powershell-environment"></a>Einrichten Ihrer PowerShell-Umgebung
Azure PowerShell bietet eine Reihe von Cmdlets, die das [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-Modell für die Verwaltung von Azure-Ressourcen verwenden.

Sie können [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) auf Ihrem lokalen Computer installieren und in einer beliebigen PowerShell-Sitzung nutzen. Befolgen Sie die Anweisungen auf der Seite, um sich mit Ihren Azure-Anmeldeinformationen bei PowerShell anzumelden und das Az-Modul zu installieren.

1. Stellen Sie mit dem folgenden Befehl eine Verbindung zu Azure her, und verwenden Sie dann ein interaktives Dialogfeld für die Anmeldung.
    ```
    Connect-AzAccount
    ```
 2. Stellen Sie vor dem Installieren eines Azure Front Door Service-Moduls sicher, dass die aktuelle Version des PowerShellGet-Moduls installiert ist. Führen Sie dann den folgenden Befehl aus, und öffnen Sie dann erneut PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Installieren Sie das Az.FrontDoor-Modul mit dem folgendem Befehl. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>Erstellen eines Azure Front Door Service-Profils
Gehen Sie zum Erstellen eines Azure Front Door Service-Profils gemäß den Anweisungen unter [Schnellstart: Erstellen Sie eine „Front Door“ für eine hoch verfügbare globale Webanwendung](quickstart-create-front-door.md).

### <a name="define-an-ip-match-condition"></a>Definieren einer IP-Übereinstimmungsbedingung
Definieren Sie mit dem Befehl [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) eine IP-Übereinstimmungsbedingung.
Ersetzen Sie im folgenden Beispiel *ip-address-range-1* und *ip-address-range-2* durch Ihren gewünschten Bereich.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue ["ip-address-range-1", "ip-address-range-2"]
```
Erstellen Sie mit dem folgenden Befehl eine *Bedingungsregel für die Übereinstimmung aller IP-Adressen*:
```powershell
$IPMatchALlCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty Any        
  ```
    
### <a name="create-a-custom-ip-allow-rule"></a>Erstellen einer benutzerdefinierten IP-Zulassungsregel

Verwenden Sie den Befehl [New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject), um eine Aktion zu definieren und eine Priorität festzulegen. Im folgenden Beispiel sind Anforderungen von Client-IP-Adressen zulässig, die mit der Liste übereinstimmen.

```powershell
$IPAllowRule = New-AzFrontDoorCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Allow -Priority 1
```
Erstellen Sie eine **Regel zum Blockieren aller IP-Adressen** mit einer niedrigeren Priorität als die vorherige **Zulassungsregel**.
```powershell
$IPBlockAll = New-AzFrontDoorCustomRuleObject `
-Name "IPDenyAll" `
-RuleType MatchRule `
-MatchCondition $IPMatchALlCondition `
-Action Block `
-Priority 2
```

### <a name="configure-a-waf-policy"></a>Konfigurieren einer WAF-Richtlinie
Suchen Sie mithilfe von `Get-AzResourceGroup` nach dem Namen der Ressourcengruppe, die das Azure Front Door Service-Profil enthält. Konfigurieren Sie dann mit dem Befehl [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) eine WAF-Richtlinie mit der **Regel zum Blockieren aller IP-Adressen**.

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Verknüpfen einer WAF-Richtlinie mit einem Azure Front Door Service-Front-End-Host

Verknüpfen Sie ein WAF-Richtlinienobjekt mit einem vorhandenen Front-End-Host, und aktualisieren Sie die Azure Front Door Service-Eigenschaften. Rufen Sie zunächst mithilfe von [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) das Azure Front Door Service-Objekt ab. Legen Sie dann mit dem Befehl [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) die Eigenschaft **WebApplicationFirewallPolicyLink** auf die Ressourcen-ID des im vorherigen Schritt erstellten *$IPAllowPolicyExamplePS*-Objekts fest.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> In diesem Beispiel wird die WAF-Richtlinie auf **FrontendEndpoints[0]** angewandt. Sie können eine WAF-Richtlinie mit jedem der Front-Ends verknüpfen. Die Eigenschaft **WebApplicationFirewallPolicyLink** muss nur einmal festgelegt werden, um eine WAF-Richtlinie mit einem Azure Front Door Service-Front-End zu verknüpfen. Nachfolgende Richtlinienaktualisierungen werden automatisch auf das Front-End angewandt.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Konfigurieren einer WAF-Richtlinie über eine Resource Manager-Vorlage
Zum Anzeigen der Vorlage, über die eine Azure Front Door Service- und eine WAF-Richtlinie mit benutzerdefinierten IP-Einschränkungsregeln erstellt werden, navigieren Sie zu [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [ein Azure Front Door Service-Profil erstellen](quickstart-create-front-door.md).
