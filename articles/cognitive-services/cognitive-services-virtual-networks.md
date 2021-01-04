---
title: Virtuelle Netzwerke
titleSuffix: Azure Cognitive Services
description: Konfigurieren vom mehrschichtiger Netzwerksicherheit für Ihre Cognitive Services-Ressourcen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: aahi
ms.openlocfilehash: 3b6c2a5a50cedadd8818eae735df55b661e794ef
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97034019"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Konfigurieren von virtuellen Netzwerken für Azure Cognitive Services

Azure Cognitive Services bietet ein mehrschichtiges Sicherheitsmodell. Dank dieses Modells können Sie Ihre Cognitive Services-Konten für eine bestimmte Teilmenge von Netzwerken schützen. Wenn Netzwerkregeln konfiguriert wurden, können nur Anwendungen, die Daten über die angegebene Gruppe von Netzwerken anfordern, auf das Konto zugreifen. Sie können den Zugriff auf Ihre Ressourcen durch Filtern von Anforderungen einschränken. Dabei werden nur Anforderungen zugelassen, die von angegebenen IP-Adressen, IP-Adressbereichen oder einer Liste von Subnetzen in [Azure Virtual Networks](../virtual-network/virtual-networks-overview.md) stammen.

Eine Anwendung, die bei aktivierten Netzwerkregeln auf eine Cognitive Services-Ressource zugreift, benötigt Autorisierung. Autorisierung wird mit [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)-Anmeldeinformationen (Azure AD) oder mit einem gültigen API-Schlüssel unterstützt.

> [!IMPORTANT]
> Durch das Aktivieren von Firewallregeln für Ihr Cognitive Services-Konto werden eingehende Datenanforderungen standardmäßig blockiert. Um das Durchlassen von Anforderungen zuzulassen, muss eine der folgenden Bedingungen erfüllt sein:

> * Die Anforderung sollte von einem Dienst stammen, der in einem Azure Virtual Network (VNet) ausgeführt wird, das auf der Liste der zugelassenen Subnetze des Cognitive Services-Zielkontos steht. Der Endpunkt von Anforderungen, die aus einem VNET stammen, muss als die [benutzerdefinierte Unterdomäne](cognitive-services-custom-subdomains.md) Ihres Cognitive Services-Kontos festgelegt werden.
> * Alternativ sollte die Anforderung von einer Adresse aus einer Liste der zulässigen IP-Adressen stammen.
>
> Unter anderem werden Anforderungen von anderen Azure-Diensten, aus dem Azure-Portal und von Protokollierungs-/Metrikdiensten blockiert.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Szenarien

Zum Sichern Ihrer Cognitive Services-Ressource sollten Sie zuerst eine Regel so konfigurieren, dass der Zugriff auf Datenverkehr aus allen Netzwerken (einschließlich Internetdatenverkehr) standardmäßig verweigert wird. Anschließend sollten Sie Regeln konfigurieren, die den Zugriff auf Datenverkehr aus bestimmten VNETs gewähren. Mit dieser Konfiguration können Sie eine sichere Netzwerkgrenze für Ihre Anwendungen erstellen. Darüber hinaus können Sie Regeln konfigurieren, um den Zugriff auf Datenverkehr aus ausgewählten öffentlichen Internet-IP-Adressbereichen zu gewähren und so Verbindungen von bestimmten Internetclients oder lokalen Clients zu ermöglichen.

Netzwerkregeln werden für alle Netzwerkprotokolle in Azure Cognitive Services durchgesetzt, einschließlich REST und WebSocket. Für den Zugriff auf Daten mithilfe von Tools wie etwa den Azure-Testkonsolen, müssen explizite Netzwerkregeln konfiguriert werden. Sie können Netzwerkregeln auf vorhandene Cognitive Services-Ressourcen anwenden oder sie beim Erstellen neuer Cognitive Services-Ressourcen durchsetzen. Angewendete Netzwerkregeln werden für alle Anforderungen erzwungen.

## <a name="supported-regions-and-service-offerings"></a>Unterstützte Regionen und Dienstangebote

Virtuelle Netzwerke (VNETs) werden unterstützt in [Regionen, in denen Cognitive Services verfügbar ist](https://azure.microsoft.com/global-infrastructure/services/). Cognitive Services unterstützt Diensttags für die Konfiguration von Netzwerkregeln. Die unten aufgeführten Dienste sind im Diensttag **CognitiveServicesManagement** enthalten:

> [!div class="checklist"]
> * Anomalieerkennung
> * Maschinelles Sehen
> * Content Moderator
> * Custom Vision
> * Gesicht
> * Formularerkennung
> * Plastischer Reader
> * Language Understanding (LUIS)
> * Personalisierung
> * Spracherkennungsdienste
> * Textanalyse
> * QnA Maker
> * Textübersetzung


> [!NOTE]
> Wenn Sie den LUIS verwenden, ermöglicht es Ihnen der **CognitiveServicesManagement**-Tag nur, dass Sie den Dienst mithilfe des SDK oder der REST-API verwenden. Für den Zugriff auf das LUIS-Portal und seine Verwendung in einem virtuellen Netzwerk müssen Sie die folgenden Tags verwenden:  
> * **AzureActiveDirectory**
> * **AzureFrontDoor.Frontend**
> * **AzureResourceManager** 
> * **CognitiveServicesManagement**



## <a name="change-the-default-network-access-rule"></a>Ändern der Standard-Netzwerkzugriffsregel

Standardmäßig akzeptieren Cognitive Services-Ressourcen Verbindungen von Clients in jedem Netzwerk. Um den Zugriff auf ausgewählte Netzwerke zu beschränken, müssen Sie zunächst die Standardaktion ändern.

> [!WARNING]
> Änderungen an Netzwerkregeln können die Fähigkeit von Anwendungen beeinträchtigen, eine Verbindung mit Azure Cognitive Services herzustellen. Durch Festlegen der Standardnetzwerkregel auf **Verweigern** wird sämtlicher Zugriff auf die Daten blockiert – es sei denn, es werden auch bestimmte Netzwerkregeln angewendet, die Zugriff **gewähren**. Gewähren Sie mithilfe von Netzwerkregeln Zugriff auf alle zulässigen Netzwerke, bevor Sie die Standardregel ändern, um Zugriff zu verweigern. Wenn Sie IP-Adressen für Ihr lokales Netzwerk in die Zulassungsliste aufnehmen, achten Sie darauf, alle möglichen ausgehenden öffentlichen IP-Adressen Ihres lokalen Netzwerks hinzuzufügen.

### <a name="managing-default-network-access-rules"></a>Verwalten standardmäßiger Netzwerkzugriffsregeln

Standardmäßige Netzwerkzugriffsregeln für Cognitive Services-Ressourcen können über das Azure-Portal, über PowerShell oder per Azure-CLI verwaltet werden.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Navigieren Sie zu der Cognitive Services-Ressource, die Sie schützen möchten.

1. Wählen Sie das Menü der **RESSOURCENVERWALTUNG** mit dem Namen **Virtuelles Netzwerk** aus.

   ![Option „Virtuelles Netzwerk“](media/vnet/virtual-network-blade.png)

1. Wenn der Zugriff standardmäßig verweigert werden soll, wählen Sie aus, dass Zugriff über **Ausgewählte Netzwerke** gewährt werden soll. Mit der Einstellung **Ausgewählte Netzwerke** allein, ohne konfigurierte Werte für **Virtuelle Netzwerke** oder **Adressbereiche**, wird effektiv jeder Zugriff verweigert. Wenn der gesamte Zugriff verweigert wird, werden Anforderungen, die versuchen, die Cognitive Services-Ressource zu nutzen, nicht zugelassen. Das Azure-Portal, Azure PowerShell oder die Azure-CLI können trotzdem zum Konfigurieren der Cognitive Services-Ressource verwendet werden.
1. Wenn Sie Datenverkehr aus allen Netzwerken zulassen möchten, wählen Sie aus, dass der Zugriff über **Alle Netzwerke** gewährt werden soll.

   ![Virtuelle Netzwerke verweigern](media/vnet/virtual-network-deny.png)

1. Klicken Sie zum Übernehmen der Änderungen auf **Speichern**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Installieren Sie [Azure PowerShell](/powershell/azure/install-az-ps), und [melden Sie sich an](/powershell/azure/authenticate-azureps), oder wählen Sie **Testen** aus.

1. Zeigen Sie den Status der Standardregel für die Cognitive Services-Ressource an.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. Legen Sie die Standardregel auf das standardmäßige Verweigern jeglichen Netzwerkzugriffs fest.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. Legen Sie die Standardregel auf das standardmäßige Zulassen von Netzwerkzugriff fest.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Installieren Sie die [Azure-CLI](/cli/azure/install-azure-cli), und [melden Sie sich an](/cli/azure/authenticate-azure-cli), oder wählen Sie **Testen** aus.

1. Zeigen Sie den Status der Standardregel für die Cognitive Services-Ressource an.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. Legen Sie die Standardregel auf das standardmäßige Verweigern jeglichen Netzwerkzugriffs fest.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. Legen Sie die Standardregel auf das standardmäßige Zulassen von Netzwerkzugriff fest.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

**_

## <a name="grant-access-from-a-virtual-network"></a>Gewähren des Zugriffs aus einem virtuellen Netzwerk

Sie können Cognitive Services-Ressourcen so konfigurieren, dass nur über bestimmte Subnetze zugegriffen werden kann. Die zulässigen Subnetze gehören möglicherweise zu einem VNet in demselben Abonnement oder in einem anderen Abonnement – einschließlich Abonnements, die zu einem anderen Azure Active Directory-Mandanten gehören.

Aktivieren Sie einen [Dienstendpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) für Azure Cognitive Services innerhalb des VNets. Der Dienstendpunkt leitet Datenverkehr aus dem VNet über einen optimalen Pfad an den Azure Cognitive Services-Dienst weiter. Mit jeder Anforderung werden außerdem die Identitäten des Subnetzes und des virtuellen Netzwerks übertragen. Administratoren können anschließend Netzwerkregeln für die Cognitive Services-Ressource konfigurieren, die den Empfang von Anforderungen aus bestimmten Subnetzen in einem VNet zulassen. Clients, denen über diese Netzwerkregeln Zugriff gewährt wird, müssen weiterhin die Autorisierungsanforderungen der Cognitive Services-Ressource erfüllen, um auf die Daten zugreifen zu können.

Jede Cognitive Services-Ressource unterstützt bis zu 100 VNet-Regeln, die mit [IP-Netzwerkregeln](#grant-access-from-an-internet-ip-range) kombiniert werden können.

### <a name="required-permissions"></a>Erforderliche Berechtigungen

Wenn Sie eine VNet-Regel auf eine Cognitive Services-Ressource anwenden möchten, muss der Benutzer über geeignete Berechtigungen für die hinzuzufügenden Subnetze verfügen. Die erforderliche Berechtigung ist die Standardrolle _Mitwirkender* oder die Rolle *Cognitive Services-Mitwirkender*. Erforderliche Berechtigungen können aber auch benutzerdefinierten Rollendefinitionen hinzugefügt werden.

Die Cognitive Services-Ressource und die virtuellen Netzwerke, denen Zugriff gewährt wurde, können sich in verschiedenen Abonnements befinden – einschließlich Abonnements, die zu einem anderen Azure AD-Mandanten gehören.

> [!NOTE]
> Die Konfiguration von Regeln, die Zugriff auf Subnetze in virtuellen Netzwerken gewähren, die Teil eines anderen Azure Active Directory-Mandanten sind, wird zurzeit nur über PowerShell, CLI und Rest-APIs unterstützt. Diese Regeln können nicht über das Azure-Portal konfiguriert werden, obwohl sie möglicherweise im Portal angezeigt werden.

### <a name="managing-virtual-network-rules"></a>Verwalten von VNET-Regeln

Regeln für virtuelle Netzwerke für Cognitive Services-Ressourcen können über das Azure-Portal, über PowerShell oder per Azure-CLI verwaltet werden.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Navigieren Sie zu der Cognitive Services-Ressource, die Sie schützen möchten.

1. Wählen Sie das Menü der **RESSOURCENVERWALTUNG** mit dem Namen **Virtuelles Netzwerk** aus.

1. Vergewissern Sie sich, dass Sie den Zugriff über **Ausgewählte Netzwerke** ausgewählt haben.

1. Um Zugriff auf ein virtuelles Netzwerk mit einer vorhandenen Netzwerkregel zu erteilen, wählen Sie unter **Virtuelle Netzwerke** die Option **Vorhandenes virtuelles Netzwerk hinzufügen** aus.

   ![Vorhandenes vNet hinzufügen](media/vnet/virtual-network-add-existing.png)

1. Wählen Sie die Optionen **virtuelle Netzwerke** und **Subnetze** und dann **Aktivieren** aus.

   ![Vorhandenes vNet hinzufügen – Details](media/vnet/virtual-network-add-existing-details.png)

1. Wenn Sie ein neues virtuelles Netzwerk erstellen und ihm Zugriff gewähren möchten, wählen Sie **Neues virtuelles Netzwerk hinzufügen** aus.

   ![Neues vNet hinzufügen](media/vnet/virtual-network-add-new.png)

1. Geben Sie die erforderlichen Informationen zum Erstellen des neuen virtuellen Netzwerks an, und wählen Sie anschließend **Erstellen** aus.

   ![vNet erstellen](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Wenn für das ausgewählte virtuelle Netzwerk und die Subnetze noch kein Dienstendpunkt für Azure Cognitive Services konfiguriert wurde, können Sie dies im Rahmen dieses Vorgangs nachholen.
    >
    > Zurzeit werden nur virtuelle Netzwerke, die zu demselben Azure Active Directory-Mandanten gehören, während der Regelerstellung zur Auswahl angezeigt. Verwenden Sie PowerShell, die CLI oder REST-APIs, um den Zugriff auf ein Subnetz in einem virtuellen Netzwerk zu gewähren, das zu einem anderen Mandanten gehört.

1. Wenn Sie eine Regel für virtuelle Netzwerke oder Subnetze entfernen möchten, wählen Sie **...** aus, um das Kontextmenü für das virtuelle Netzwerk oder Subnetz zu öffnen, und wählen Sie anschließend **Entfernen** aus.

   ![vNet entfernen](media/vnet/virtual-network-remove.png)

1. Klicken Sie zum Übernehmen der Änderungen auf **Speichern**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Installieren Sie [Azure PowerShell](/powershell/azure/install-az-ps), und [melden Sie sich an](/powershell/azure/authenticate-azureps), oder wählen Sie **Testen** aus.

1. Listen Sie die VNET-Regeln auf.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. Aktivieren Sie den Dienstendpunkt für Azure Cognitive Services in einem vorhandenen virtuellen Netzwerk und Subnetz.

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. Fügen Sie eine Netzwerkregel für ein virtuelles Netzwerk und Subnetz hinzu.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > Wenn Sie eine Netzwerkregel für ein Subnetz in einem VNET hinzufügen möchten, das zu einem anderen Azure AD-Mandanten gehört, verwenden Sie einen voll qualifizierten **VirtualNetworkResourceId**-Parameter im Format „/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name“.

1. Entfernen Sie eine Netzwerkregel für ein virtuelles Netzwerk und Subnetz.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Installieren Sie die [Azure-CLI](/cli/azure/install-azure-cli), und [melden Sie sich an](/cli/azure/authenticate-azure-cli), oder wählen Sie **Testen** aus.

1. Listen Sie die VNET-Regeln auf.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. Aktivieren Sie den Dienstendpunkt für Azure Cognitive Services in einem vorhandenen virtuellen Netzwerk und Subnetz.

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. Fügen Sie eine Netzwerkregel für ein virtuelles Netzwerk und Subnetz hinzu.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > Verwenden Sie zum Hinzufügen einer Regel für ein Subnetz in einem VNET, das zu einem anderen Azure AD Mandanten gehört, eine voll qualifizierte Subnetz-ID im Format "/Subscriptions/Subscription-ID/resourceGroups/resourceGroup-Name/Providers/Microsoft.Network/virtualNetworks/vNet-Name/Subnets/Subnet-Name".
    > 
    > Sie können den Parameter **Abonnement** verwenden, um die Subnetz-ID für ein VNET abzurufen, das zu einem anderen Azure AD-Mandanten gehört.

1. Entfernen Sie eine Netzwerkregel für ein virtuelles Netzwerk und Subnetz.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

**_

> [!IMPORTANT]
> Die Standardregel muss auf _*Verweigern** festgelegt sein (siehe [Festlegen der Standardregel](#change-the-default-network-access-rule)). Andernfalls haben die Netzwerkregeln keine Wirkung.

## <a name="grant-access-from-an-internet-ip-range"></a>Gewähren von Zugriff aus einem Internet-IP-Adressbereich

Sie können Cognitive Services-Ressourcen so konfigurieren, dass der Zugriff über bestimmte öffentliche Internet-IP-Adressbereichen zugelassen wird. Diese Konfiguration gewährt bestimmten Diensten und lokalen Netzwerken Zugriff und blockiert gleichzeitig effektiv den allgemeinen Internetdatenverkehr.

Geben Sie zulässige Internetadressbereiche in [CIDR-Notation](https://tools.ietf.org/html/rfc4632) im Format `16.17.18.0/24` oder als einzelne IP-Adressen (beispielsweise `16.17.18.19`) an.

   > [!Tip]
   > Kleine Adressbereiche mit der Präfixgröße „/ 31“ oder „/ 32“ werden nicht unterstützt. Diese Bereiche müssen mit einzelnen IP-Adressregeln konfiguriert werden.

IP-Netzwerkregeln sind nur für **öffentliche Internet**-IP-Adressen zulässig. Für private Netzwerke reservierte IP-Adressbereiche (wie in [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3) definiert) sind in IP-Adressregeln nicht zulässig. Private Netzwerke enthalten Adressen, die mit `10.*`, `172.16.*` - `172.31.*` und `192.168.*` beginnen.

   > [!NOTE]
   > IP-Netzwerkregeln haben keine Auswirkungen auf Anforderungen, die aus der Azure-Region stammen, in der sich auch die Cognitive Services-Ressource befindet. Verwenden Sie [VNET-Regeln](#grant-access-from-a-virtual-network), um Anforderungen aus der gleichen Region zuzulassen.

Derzeit werden nur IPv4-Adressen unterstützt. Jede Cognitive Services-Ressource unterstützt bis zu 100 IP-Netzwerkregeln, die mit [Regeln für virtuelle Netzwerke](#grant-access-from-a-virtual-network) kombiniert werden können.

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurieren des Zugriffs aus lokalen Netzwerken

Wenn Sie mit einer IP-Netzwerkregel den Zugriff über Ihre lokalen Netzwerke auf die Cognitive Services-Ressource gewähren möchten, müssen Sie die von Ihrem Netzwerk verwendeten Internet-IP-Adressen ermitteln. Hilfe erhalten Sie von Ihrem Netzwerkadministrator.

Wenn Sie [ExpressRoute](../expressroute/expressroute-introduction.md) lokal für öffentliches Peering oder für Microsoft-Peering verwenden, müssen Sie die NAT-IP-Adressen identifizieren. Für öffentliches Peering verwendet jede ExpressRoute-Leitung standardmäßig zwei NAT IP-Adressen. Jede wird auf Azure-Dienstverkehr angewendet, wenn der Verkehr in den Backbone des Microsoft Azure-Netzwerk gelangt. Beim Microsoft-Peering werden die verwendeten NAT-IP-Adressen entweder vom Kunden oder vom Dienstanbieter bereitgestellt. Um den Zugriff auf Ihre Dienstressourcen zuzulassen, müssen Sie diese öffentlichen IP-Adressen in der Ressourceneinstellung der IP-Firewall zulassen. [Öffnen Sie über das Azure-Portal ein Supportticket für ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview), um die IP-Adressen Ihrer ExpressRoute-Verbindung für öffentliches Peering zu ermitteln. Erfahren Sie mehr über [NAT für öffentliches ExpressRoute-Peering und Microsoft-Peering](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering).

### <a name="managing-ip-network-rules"></a>Verwalten von IP-Netzwerkregeln

Regeln für IP-Netzwerke für Cognitive Services-Ressourcen können über das Azure-Portal, über PowerShell oder per Azure-CLI verwaltet werden.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Navigieren Sie zu der Cognitive Services-Ressource, die Sie schützen möchten.

1. Wählen Sie das Menü der **RESSOURCENVERWALTUNG** mit dem Namen **Virtuelles Netzwerk** aus.

1. Vergewissern Sie sich, dass Sie den Zugriff über **Ausgewählte Netzwerke** ausgewählt haben.

1. Geben Sie unter **Firewall** > **Adressbereich** die IP-Adresse oder den IP-Adressbereich (im [CIDR-Format](https://tools.ietf.org/html/rfc4632)) ein, um Zugriff auf einen Internet-IP-Adressbereich zu gewähren. Nur gültige öffentliche IP-Adressen (nicht reservierte Adressen) werden akzeptiert.

   ![IP-Bereich hinzufügen](media/vnet/virtual-network-add-ip-range.png)

1. Wenn Sie eine IP-Netzwerkregel entfernen möchten, wählen Sie das Papierkorbsymbol <span class="docon docon-delete x-hidden-focus"></span> neben dem Adressbereich aus.

   ![IP-Bereich löschen](media/vnet/virtual-network-delete-ip-range.png)

1. Klicken Sie zum Übernehmen der Änderungen auf **Speichern**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Installieren Sie [Azure PowerShell](/powershell/azure/install-az-ps), und [melden Sie sich an](/powershell/azure/authenticate-azureps), oder wählen Sie **Testen** aus.

1. Listen Sie IP-Netzwerkregeln auf.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. Fügen Sie eine Netzwerkregel für eine einzelne IP-Adresse hinzu.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Fügen Sie eine Netzwerkregel für einen IP-Adressbereich hinzu.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Entfernen Sie eine Netzwerkregel für eine einzelne IP-Adresse.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Entfernen Sie eine Netzwerkregel für einen IP-Adressbereich.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Installieren Sie die [Azure-CLI](/cli/azure/install-azure-cli), und [melden Sie sich an](/cli/azure/authenticate-azure-cli), oder wählen Sie **Testen** aus.

1. Listen Sie IP-Netzwerkregeln auf.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. Fügen Sie eine Netzwerkregel für eine einzelne IP-Adresse hinzu.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Fügen Sie eine Netzwerkregel für einen IP-Adressbereich hinzu.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. Entfernen Sie eine Netzwerkregel für eine einzelne IP-Adresse.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Entfernen Sie eine Netzwerkregel für einen IP-Adressbereich.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

**_

> [!IMPORTANT]
> Die Standardregel muss auf _*Verweigern** festgelegt sein (siehe [Festlegen der Standardregel](#change-the-default-network-access-rule)). Andernfalls haben die Netzwerkregeln keine Wirkung.

## <a name="use-private-endpoints"></a>Verwenden privater Endpunkte

Sie können [private Endpunkte](../private-link/private-endpoint-overview.md) für Ihre Cognitive Services-Ressourcen verwenden, um Clients in einem virtuellen Netzwerk (VNET) den sicheren Zugriff auf Daten über [Private Link](../private-link/private-link-overview.md) zu ermöglichen. Der private Endpunkt verwendet eine IP-Adresse aus dem VNET-Adressraum für Ihre Cognitive Services-Ressource. Der Netzwerkdatenverkehr zwischen den Clients im VNET und der Ressource wird über das VNET und eine private Verbindung im Microsoft-Backbonenetzwerk geleitet, sodass keine Offenlegung im öffentlichen Internet erfolgt.

Private Endpunkte für Cognitive Services Ressourcen ermöglichen Folgendes:

* das Schützen Ihrer Cognitive Services-Ressource, indem Sie die Firewall so konfigurieren, dass alle Verbindungen am öffentlichen Endpunkt für Cognitive Services blockiert werden
* das Erhöhen der Sicherheit für das VNET, indem Sie die Exfiltration von Daten aus dem VNET blockieren
* die sichere Verbindungsherstellung mit Cognitive Services-Ressourcen über lokale Netzwerke, die eine Verbindung mit dem VNET über [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) oder [ExpressRoutes](../expressroute/expressroute-locations.md) mit privatem Peering aufweisen

### <a name="conceptual-overview"></a>Konzeptionelle Übersicht

Ein privater Endpunkt ist eine spezielle Netzwerkschnittstelle für eine Azure-Ressource in Ihrem [VNET](../virtual-network/virtual-networks-overview.md). Wenn Sie einen privaten Endpunkt für Ihre Cognitive Services-Ressource erstellen, wird eine sichere Verbindung zwischen Clients in Ihrem VNET und Ihrer Ressource bereitgestellt. Dem privaten Endpunkt wird eine IP-Adresse aus dem IP-Adressbereich Ihres VNET zugewiesen. Für die Verbindung zwischen dem privaten Endpunkt und Cognitive Services wird eine sichere private Verbindung verwendet.

Anwendungen im VNET können eine nahtlose Verbindung mit dem Dienst über den privaten Endpunkt herstellen, indem die gleichen Verbindungszeichenfolgen und Autorisierungsmechanismen wie üblich verwendet werden. Die Speech-Dienste stellen eine Ausnahme dar, denn sie benötigen einen separaten Endpunkt. Weitere Informationen finden Sie im Abschnitt [Private Endpunkte in den Speech-Diensten](#private-endpoints-with-the-speech-services). Private Endpunkte können mit allen von der Cognitive Services-Ressource unterstützten Protokollen verwendet werden, auch mit REST.

Private Endpunkte können in Subnetzen erstellt werden, die [Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) verwenden. Clients in einem Subnetz können eine Verbindung mit einer Cognitive Services-Ressource über einen privaten Endpunkt herstellen, während für den Zugriff auf andere Ressourcen Dienstendpunkte verwendet werden.

Wenn Sie einen privaten Endpunkt für eine Cognitive Services-Ressource in Ihrem VNET erstellen, wird eine Einwilligungsanforderung zur Genehmigung an den Besitzer der Cognitive Services-Ressource gesendet. Wenn der Benutzer, der die Erstellung des privaten Endpunkts anfordert, auch ein Besitzer der Ressource ist, wird diese Einwilligungsanforderung automatisch genehmigt.

Besitzer von Cognitive Services-Ressourcen können Einwilligungsanforderungen und die privaten Endpunkte über die Registerkarte *Private Endpunkte* für die Cognitive Services-Ressource im [Azure-Portal](https://portal.azure.com) verwalten.

### <a name="private-endpoints"></a>Private Endpunkte

Während Sie den privaten Endpunkt erstellen, müssen Sie die Cognitive Services-Ressource angeben, mit der eine Verbindung hergestellt wird. Weitere Informationen zum Erstellen eines privaten Endpunkts finden Sie unter:

* [Erstellen eines privaten Endpunkts über das Private Link Center im Azure-Portal](../private-link/create-private-endpoint-portal.md)
* [Erstellen eines privaten Endpunkts mit Azure CLI](../private-link/create-private-endpoint-cli.md)
* [Erstellen eines privaten Endpunkts mit Azure PowerShell](../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>Herstellen einer Verbindung mit privaten Endpunkten

Clients in einem VNET, die den privaten Endpunkt verwenden, sollten dieselbe Verbindungszeichenfolge für die Cognitive Services-Ressource verwenden wie Clients, die eine Verbindung mit dem öffentlichen Endpunkt herstellen. Die Speech-Dienste stellen eine Ausnahme dar, denn sie benötigen einen separaten Endpunkt. Weitere Informationen finden Sie im Abschnitt [Private Endpunkte in den Speech-Diensten](#private-endpoints-with-the-speech-services). Das automatische Weiterleiten der Verbindungen vom VNET zur Cognitive Services-Ressource über eine private Verbindung basiert auf der DNS-Auflösung. 

Wir erstellen standardmäßig eine [private DNS-Zone](../dns/private-dns-overview.md), die an das VNET angehängt ist, mit den erforderlichen Updates für die privaten Endpunkte. Wenn Sie jedoch einen eigenen DNS-Server verwenden, müssen Sie möglicherweise zusätzliche Änderungen an Ihrer DNS-Konfiguration vornehmen. Im folgenden Abschnitt zu [DNS-Änderungen](#dns-changes-for-private-endpoints) werden die für private Endpunkte erforderlichen Updates beschrieben.

### <a name="private-endpoints-with-the-speech-services"></a>Private Endpunkte in den Speech-Diensten

Informationen erhalten Sie unter [Verwenden der Speech-Dienste mit von Azure Private Link bereitgestellten privaten Endpunkten](Speech-Service/speech-services-private-link.md).

### <a name="dns-changes-for-private-endpoints"></a>DNS-Änderungen für private Endpunkte

Wenn Sie einen privaten Endpunkt erstellen, wird der DNS CNAME-Ressourceneintrag für die Cognitive Services-Ressource auf einen Alias in einer Unterdomäne mit dem Präfix *privatelink* aktualisiert. Standardmäßig erstellen wir außerdem eine [private DNS-Zone](../dns/private-dns-overview.md), die der Unterdomäne *privatelink* entspricht, mit den DNS-A-Ressourceneinträgen für die privaten Endpunkte.

Wenn Sie die Speicherendpunkt-URL von außerhalb des VNET mit dem privaten Endpunkt auflösen, wird diese in den öffentlichen Endpunkt der Cognitive Services-Ressource aufgelöst. Bei Auflösung aus dem VNET, das den privaten Endpunkt hostet, wird die Endpunkt-URL in die IP-Adresse des privaten Endpunkts aufgelöst.

Diese Vorgehensweise ermöglicht den Zugriff auf die Cognitive Services-Ressource mithilfe derselben Verbindungszeichenfolge für Clients in dem VNET, das die privaten Endpunkte hostet, sowie für Clients außerhalb des VNET.

Wenn Sie einen benutzerdefinierten DNS-Server in Ihrem Netzwerk verwenden, müssen Clients in der Lage sein, den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) für den Cognitive Services-Ressourcenendpunkt in die IP-Adresse des privaten Endpunkts aufzulösen. Konfigurieren Sie den DNS-Server so, dass die Unterdomäne der privaten Verbindung an die private DNS-Zone für das VNET delegiert wird.

> [!TIP]
> Wenn Sie einen benutzerdefinierten oder lokalen DNS-Server verwenden, sollten Sie den DNS-Server so konfigurieren, dass der Name der Cognitive Services-Ressource in der Unterdomäne „privatelink“ in die IP-Adresse des privaten Endpunkts aufgelöst wird. Hierzu können Sie die Unterdomäne „privatelink“ an die private DNS-Zone des VNET delegieren oder die DNS-Zone auf dem DNS-Server konfigurieren und die DNS-A-Einträge hinzufügen.

Weitere Informationen zum Konfigurieren des eigenen DNS-Servers für die Unterstützung privater Endpunkte finden Sie in den folgenden Artikeln:

* [Namensauflösung für Ressourcen in virtuellen Azure-Netzwerken](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
* [DNS-Konfiguration für private Endpunkte](../private-link/private-endpoint-overview.md#dns-configuration)

### <a name="pricing"></a>Preise

Ausführliche Preisinformationen finden Sie unter [Azure Private Link – Preise](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Nächste Schritte

* Erkunden der verschiedenen [Azure Cognitive Services](./what-are-cognitive-services.md)
* Weitere Informationen zu [Azure VNet-Dienstendpunkten](../virtual-network/virtual-network-service-endpoints-overview.md).