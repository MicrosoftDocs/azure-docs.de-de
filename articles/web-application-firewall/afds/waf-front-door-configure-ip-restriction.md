---
title: Konfigurieren einer IP-Einschränkungs-WAF-Regel für Azure Front Door
description: Erfahren Sie, wie Sie eine Web Application Firewall-Regel zum Einschränken von IP-Adressen für einen vorhandenen Azure Front Door-Endpunkt konfigurieren.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 03/26/2020
ms.author: tyao
ms.openlocfilehash: f260bfc7b097931cc1a978e790c1d9dd966703ac
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563510"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>Konfigurieren einer IP-Einschränkungsregel mit einer Web Application Firewall für Azure Front Door

In diesem Artikel wird erläutert, wie Sie IP-Einschränkungsregeln in Web Application Firewall (WAF) für Azure Front Door über das Azure-Portal, die Azure-Befehlszeilenschnittstelle, Azure PowerShell oder eine Azure Resource Manager-Vorlage konfigurieren.

Eine auf IP-Adressen basierende Zugriffssteuerungsregel ist eine benutzerdefinierte WAF-Regel, mit der Sie den Zugriff auf Ihre Webanwendungen steuern können. Dazu ist in der Regel eine Liste von IP-Adressen oder IP-Adressbereichen im CIDR-Format (Classless Inter-Domain Routing) angegeben.

Standardmäßig kann auf Ihre Webanwendungen über das Internet zugegriffen werden. Wenn Sie den Zugriff auf Clients entsprechend einer Liste bekannter IP-Adressen oder IP-Adressbereiche einschränken möchten, können Sie eine IP-Abgleichregel erstellen, die die Liste der IP-Adressen als abzugleichende Werte enthält und in der der Operator auf „Nicht“ (Negation ist wahr) und die Aktion auf **Blockieren** festgelegt wird. Nachdem eine IP-Einschränkungsregel angewandt wurde, erhalten Anforderungen von Adressen, die nicht in dieser Zulassungsliste enthalten sind, die Antwort „403 (Nicht zulässig)“.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>Konfigurieren einer WAF-Richtlinie über das Azure-Portal

### <a name="prerequisites"></a>Voraussetzungen

Gehen Sie zum Erstellen eines Azure Front Door-Profils gemäß den Anweisungen unter [Schnellstart: Erstellen Sie eine „Front Door“ für eine hoch verfügbare globale Webanwendung](../../frontdoor/quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Erstellen einer WAF-Richtlinie

1. Wählen Sie im Azure-Portal **Ressource erstellen** aus, geben Sie **Web Application Firewall** in das Suchfeld ein, und wählen Sie dann **Web Application Firewall (WAF)** aus.
2. Klicken Sie auf **Erstellen**.
3. Auf der Seite **WAF-Richtlinie erstellen** verwenden Sie die folgenden Werte, um die Registerkarte **Grundlagen** auszufüllen:
   
   |Einstellung  |Wert  |
   |---------|---------|
   |Richtlinie für     |Globale WAF (Front Door)|
   |Subscription     |Wählen Sie Ihr Abonnement aus.|
   |Resource group     |Wählen Sie die Ressourcengruppe aus, in der sich Ihr Front Door befindet.|
   |Richtlinienname     |Geben Sie einen Namen für Ihre Richtlinie ein.|
   |Richtlinienstatus     |Aktiviert|

   Klicken Sie auf **Weiter: Richtlinieneinstellungen**

1. Wählen Sie auf der Registerkarte **Richtlinieneinstellungen** die Option **Prävention** aus. Für den **Antworttext für blockierte Anforderungen** geben Sie *Sie wurden blockiert!* ein, damit Sie sehen können, dass Ihre benutzerdefinierte Regel wirksam ist.
2. Klicken Sie auf **Weiter: Verwaltete Regeln**.
3. Klicken Sie auf **Weiter: Benutzerdefinierte Regeln**.
4. Wählen Sie **Benutzerdefinierte Regel hinzufügen** aus.
5. Verwenden Sie auf der Seite **Benutzerdefinierte Regel hinzufügen** die folgenden Testwerte, um eine benutzerdefinierte Regel zu erstellen:

   |Einstellung  |Wert  |
   |---------|---------|
   |Name der benutzerdefinierten Regel     |FdWafCustRule|
   |Status     |Aktiviert|
   |Regeltyp     |Match|
   |Priority    |100|
   |Übereinstimmungstyp     |IP-Adresse|
   |Übereinstimmungsvariable|RemoteAddr|
   |Vorgang|Enthält nicht|
   |IP-Adresse oder Adressbereich|10.10.10.0/24|
   |Then|Datenverkehr ablehnen|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="Benutzerdefinierte Regel":::

   Wählen Sie **Hinzufügen**.
6. Klicken Sie auf **Weiter: Zuordnung**.
7. Wählen Sie **Front-End-Host hinzufügen** aus.
8. Wählen Sie für **Front-End-Host** Ihren Front-End-Host und dann **Hinzufügen** aus.
9. Klicken Sie auf **Überprüfen + erstellen**.
10. Nachdem Ihre Richtlinienüberprüfung bestanden ist, wählen Sie **Erstellen** aus.

### <a name="test-your-waf-policy"></a>Testen Ihrer WAF-Richtlinie

1. Nachdem die Bereitstellung der WAF-Richtlinien abgeschlossen ist, navigieren Sie zu Ihrem Front-End-Hostnamen.
2. Es sollte Ihre benutzerdefinierte Blockierungsmeldung angezeigt werden.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="WAF-Regeltest":::

   > [!NOTE]
   > In der benutzerdefinierten Regel wurde absichtlich eine private IP-Adresse verwendet, um die Auslösung der Regel sicherzustellen. Erstellen Sie bei einer tatsächlichen Bereitstellung Regeln für *Zulassen* und *Ablehnen* mit IP-Adressen für Ihre bestimmte Situation.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Konfigurieren einer WAF-Richtlinie über die Azure-Befehlszeilenschnittstelle

### <a name="prerequisites"></a>Voraussetzungen
Vor dem Konfigurieren einer Richtlinie für IP-Einschränkung müssen Sie zunächst die CLI-Umgebung einrichten und ein Azure Front Door-Profil erstellen.

#### <a name="set-up-the-azure-cli-environment"></a>Einrichten der Azure CLI-Umgebung
1. Installieren Sie die [Azure CLI](/cli/azure/install-azure-cli), oder verwenden Sie Azure Cloud Shell. Azure Cloud Shell ist eine kostenlose Bash-Shell, die Sie direkt im Azure-Portal ausführen können. Die Azure CLI ist vorinstalliert und für die Verwendung mit Ihrem Konto konfiguriert. Wählen Sie die Schaltfläche **Ausprobieren** in den folgenden CLI-Befehlen, und melden Sie sich bei Ihrem Azure-Konto in der Cloud Shell-Sitzung an, die geöffnet wird. Nachdem die Sitzung gestartet wurde, geben Sie `az extension add --name front-door` ein, um die Azure Front Door-Erweiterung hinzuzufügen.
 2. Wenn Sie die CLI lokal in Bash verwenden, melden Sie sich mit `az login` bei Azure an.

#### <a name="create-an-azure-front-door-profile"></a>Erstellen eines Azure Front Door-Profils
Gehen Sie zum Erstellen eines Azure Front Door-Profils gemäß den Anweisungen unter [Schnellstart: Erstellen Sie eine „Front Door“ für eine hoch verfügbare globale Webanwendung](../../frontdoor/quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Erstellen einer WAF-Richtlinie

Erstellen Sie mit dem Befehl [az network front-door waf-policy create](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create) eine WAF-Richtlinie. Ersetzen Sie im folgenden Beispiel den Richtliniennamen *IPAllowPolicyExampleCLI* durch einen eindeutigen Richtliniennamen.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Hinzufügen einer benutzerdefinierten Regel für die IP-Zugriffssteuerung

Fügen Sie mit dem Befehl [az network front-door waf-policy custom-rule create](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create) der im vorherigen Schritt erstellten WAF-Richtlinie eine benutzerdefinierte Regel für die IP-Zugriffssteuerung hinzu.

In den folgenden Beispielen:
-  Ersetzen Sie *IPAllowPolicyExampleCLI* durch die zuvor erstellte eindeutige Richtlinie.
-  Ersetzen Sie *ip-address-range-1* und *ip-address-range-2* durch Ihren gewünschten Bereich.

Erstellen Sie zunächst eine IP-Zulassungsregel für die Richtlinie, die Sie im vorherigen Schritt erstellt haben. 
> [!NOTE]
> Hier ist **--defer** erforderlich, da eine Regel eine Übereinstimmungsbedingung aufweisen muss, die im nächsten Schritt hinzugefügt wird.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Fügen Sie der Regel nun eine Übereinstimmungsbedingung hinzu:

```azurecli
az network front-door waf-policy rule match-condition add \
--match-variable RemoteAddr \
--operator IPMatch \
--values "ip-address-range-1" "ip-address-range-2" \
--negate true \
--name IPAllowListRule \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>Suchen der ID einer WAF-Richtlinie 
Mit dem Befehl [az network front-door waf-policy show](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) können Sie die ID einer WAF-Richtlinie suchen. Ersetzen Sie *IPAllowPolicyExampleCLI* im folgenden Beispiel durch die eindeutige Richtlinie, die Sie zuvor erstellt haben.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Verknüpfen einer WAF-Richtlinie mit einem Azure Front Door-Front-End-Host

Legen Sie mit dem Befehl [az network front-door update](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) die Azure Front Door-ID *WebApplicationFirewallPolicyLink* auf die Richtlinien-ID fest. Ersetzen Sie *IPAllowPolicyExampleCLI* durch die zuvor erstellte eindeutige Richtlinie.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
In diesem Beispiel wird die WAF-Richtlinie auf **FrontendEndpoints[0]** angewandt. Sie können die WAF-Richtlinie mit jedem der Front-Ends verknüpfen.
> [!Note]
> Die Eigenschaft **WebApplicationFirewallPolicyLink** muss nur einmal festgelegt werden, um eine WAF-Richtlinie mit einem Azure Front Door-Front-End zu verknüpfen. Nachfolgende Richtlinienaktualisierungen werden automatisch auf das Front-End angewandt.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Konfigurieren einer WAF-Richtlinie über Azure PowerShell

### <a name="prerequisites"></a>Voraussetzungen
Vor dem Konfigurieren einer Richtlinie für IP-Einschränkung müssen Sie zunächst die PowerShell-Umgebung einrichten und ein Azure Front Door-Profil erstellen.

#### <a name="set-up-your-powershell-environment"></a>Einrichten Ihrer PowerShell-Umgebung
Azure PowerShell bietet eine Reihe von Cmdlets, die das [Azure Resource Manager](../../azure-resource-manager/management/overview.md)-Modell für die Verwaltung von Azure-Ressourcen verwenden.

Sie können [Azure PowerShell](/powershell/azure/) auf Ihrem lokalen Computer installieren und in einer beliebigen PowerShell-Sitzung nutzen. Befolgen Sie die Anweisungen auf der Seite, um sich mit Ihren Azure-Anmeldeinformationen bei PowerShell anzumelden und das Az-Modul zu installieren.

1. Stellen Sie mit dem folgenden Befehl eine Verbindung zu Azure her, und verwenden Sie dann ein interaktives Dialogfeld für die Anmeldung.
    ```
    Connect-AzAccount
    ```
 2. Stellen Sie vor dem Installieren eines Azure Front Door-Moduls sicher, dass die aktuelle Version des PowerShellGet-Moduls installiert ist. Führen Sie dann den folgenden Befehl aus, und öffnen Sie dann erneut PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Installieren Sie das Az.FrontDoor-Modul mit dem folgendem Befehl. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Erstellen eines Azure Front Door-Profils
Gehen Sie zum Erstellen eines Azure Front Door-Profils gemäß den Anweisungen unter [Schnellstart: Erstellen Sie eine „Front Door“ für eine hoch verfügbare globale Webanwendung](../../frontdoor/quickstart-create-front-door.md).

### <a name="define-an-ip-match-condition"></a>Definieren einer IP-Übereinstimmungsbedingung
Definieren Sie mit dem Befehl [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) eine IP-Übereinstimmungsbedingung.
Ersetzen Sie im folgenden Beispiel *ip-address-range-1* und *ip-address-range-2* durch Ihren gewünschten Bereich.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Erstellen einer benutzerdefinierten IP-Zulassungsregel

Verwenden Sie den Befehl [New-AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject), um eine Aktion zu definieren und eine Priorität festzulegen. Im folgenden Beispiel werden Anforderungen, die von Client-IP-Adressen stammen, die nicht mit der Liste übereinstimmen, blockiert.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Konfigurieren einer WAF-Richtlinie
Suchen Sie mithilfe von `Get-AzResourceGroup` nach dem Namen der Ressourcengruppe, die das Azure Front Door-Profil enthält. Konfigurieren Sie dann mit dem Befehl [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) eine WAF-Richtlinie mit der IP-Regel.

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Verknüpfen einer WAF-Richtlinie mit einem Azure Front Door-Front-End-Host

Verknüpfen Sie ein WAF-Richtlinienobjekt mit einem vorhandenen Front-End-Host, und aktualisieren Sie die Azure Front Door-Eigenschaften. Rufen Sie zunächst mithilfe von [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) das Azure Front Door-Objekt ab. Legen Sie dann mit dem Befehl [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) die Eigenschaft **WebApplicationFirewallPolicyLink** auf die Ressourcen-ID des im vorherigen Schritt erstellten *$IPAllowPolicyExamplePS*-Objekts fest.

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> In diesem Beispiel wird die WAF-Richtlinie auf **FrontendEndpoints[0]** angewandt. Sie können eine WAF-Richtlinie mit jedem der Front-Ends verknüpfen. Die Eigenschaft **WebApplicationFirewallPolicyLink** muss nur einmal festgelegt werden, um eine WAF-Richtlinie mit einem Azure Front Door-Front-End zu verknüpfen. Nachfolgende Richtlinienaktualisierungen werden automatisch auf das Front-End angewandt.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Konfigurieren einer WAF-Richtlinie über eine Resource Manager-Vorlage
Zum Anzeigen der Vorlage, über die eine Azure Front Door- und eine WAF-Richtlinie mit benutzerdefinierten IP-Einschränkungsregeln erstellt werden, navigieren Sie zu [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [ein Azure Front Door-Profil erstellen](../../frontdoor/quickstart-create-front-door.md).