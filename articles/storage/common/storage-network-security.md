---
title: Konfigurieren von Azure Storage-Firewalls und virtuellen Netzwerken | Microsoft-Dokumentation
description: Konfigurieren Sie mithilfe von Azure Storage-Firewalls und Azure Virtual Network eine mehrstufige Netzwerksicherheit für Ihr Speicherkonto.
services: storage
author: santoshc
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 07ad0714d0294ad90150acb9df14f17bfc1f5f0d
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905365"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurieren von Azure Storage-Firewalls und virtuellen Netzwerken

Azure Storage bietet ein mehrschichtiges Sicherheitsmodell. Mit diesem Modell können Sie die Zugriffsebene für Ihre Speicherkonten sichern und steuern, die von Ihren Anwendungen und Unternehmensumgebungen gefordert werden – abhängig vom Typ und der Teilmenge der verwendeten Netzwerke. Wenn Netzwerkregeln konfiguriert wurden, können nur Anwendungen, die Daten über die angegebene Gruppe von Netzwerken anfordern, auf ein Speicherkonto zugreifen. Sie können den Zugriff auf Ihr Speicherkonto auf Anforderungen beschränken, die aus angegebenen IP-Adressen, IP-Adressbereichen oder einer Liste von Subnetzen in einem virtuellen Azure-Netzwerk (VNet) stammen.

Speicherkonten verfügen über einen öffentlichen Endpunkt, auf den über das Internet zugegriffen werden kann. Sie können auch [private Endpunkte für Ihr Speicherkonto](storage-private-endpoints.md) erstellen, das dem Speicherkonto eine private IP-Adresse aus Ihrem VNet zuweist und den gesamten Datenverkehr zwischen Ihrem VNet und dem Speicherkonto über einen privaten Link sichert. Die Azure Storage-Firewall ermöglicht Zugriffssteuerung für den öffentlichen Endpunkt Ihres Speicherkontos. Sie können die Firewall auch zum Blockieren des gesamten Zugriffs über den öffentlichen Endpunkt einsetzen, wenn private Endpunkte verwendet werden. Ihre Storage-Firewallkonfiguration ermöglicht auch die Auswahl vertrauenswürdiger Azure-Plattformdienste für sicheren Zugriff auf das Speicherkonto.

Eine Anwendung, die bei aktivierten Netzwerkregeln auf ein Speicherkonto zugreift, benötigt weiterhin eine ordnungsgemäße Autorisierung für die Anforderung. Für die Autorisierung können Azure Active Directory (Azure AD)-Anmeldeinformationen für Blobs und Warteschlangen mit einem gültigen Kontozugriffsschlüssel oder mit einem SAS-Token verwendet werden.

> [!IMPORTANT]
> Wenn Sie Firewallregeln für Ihr Speicherkonto aktivieren, werden eingehende Datenanforderungen standardmäßig blockiert – es sei denn, die Anforderungen stammen von einem Dienst, der innerhalb eines virtuellen Azure-Netzwerks (VNet) agiert, oder aus zulässigen öffentlichen IP-Adressen. Unter anderem werden Anforderungen von anderen Azure-Diensten, aus dem Azure-Portal und von Protokollierungs-/Metrikdiensten blockiert.
>
> Sie können Azure-Diensten, die innerhalb eines VNETs agieren, Zugriff gewähren, indem Sie Datenverkehr aus dem Subnetz zulassen, das die Dienstinstanz hostet. Sie können auch eine begrenzte Anzahl von Szenarien über den nachstehend beschriebenen Mechanismus [Ausnahmen](#exceptions) aktivieren. Der Zugriff auf Daten aus dem Speicherkonto über das Azure-Portal muss über einen Computer erfolgen, der sich innerhalb der von Ihnen eingerichteten vertrauenswürdigen Grenze (IP-Adresse oder VNET) befindet.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Szenarien

Zum Sichern Ihres Speicherkontos sollten Sie zuerst eine Regel so konfigurieren, dass der Zugriff auf Datenverkehr aus allen Netzwerken (einschließlich Internetdatenverkehr) auf dem öffentlichen Endpunkt standardmäßig verweigert wird. Anschließend sollten Sie Regeln konfigurieren, die den Zugriff auf Datenverkehr aus bestimmten VNETs gewähren. Darüber hinaus können Sie Regeln konfigurieren, um den Zugriff auf Datenverkehr aus ausgewählten öffentlichen Internet-IP-Adressbereichen zu gewähren und so Verbindungen von bestimmten Internetclients oder lokalen Clients zu ermöglichen. Mit dieser Konfiguration können Sie eine sichere Netzwerkgrenze für Ihre Anwendungen erstellen.

Sie können Firewallregeln kombinieren, die den Zugriff aus bestimmten virtuellen Netzwerken und aus öffentlichen IP-Adressbereichen in demselben Speicherkonto zulassen. Storage-Firewallregeln können auf vorhandene Speicherkonten oder bei der Erstellung neuer Speicherkonten angewendet werden.

Storage-Firewallregeln gelten für den öffentlichen Endpunkt eines Speicherkontos. Sie benötigen keine Firewallzugriffsregeln, um Datenverkehr für private Endpunkte eines Speicherkontos zuzulassen. Der Vorgang zur Genehmigung der Erstellung eines privaten Endpunkts gewährt impliziten Zugriff auf Datenverkehr aus dem Subnetz, das den privaten Endpunkt hostet.

Netzwerkregeln werden für alle Netzwerkprotokolle in Azure Storage, einschließlich REST und SMB, erzwungen. Für den Zugriff auf Daten mithilfe von Tools wie Azure-Portal, Storage-Explorer und AZCopy müssen explizite Netzwerkregeln konfiguriert werden.

Angewendete Netzwerkregeln werden für alle Anforderungen erzwungen. SAS-Token, die Zugriff auf eine bestimmte IP-Adresse gewähren, beschränken den Zugriff des Tokeninhabers, gewähren jedoch keinen neuen Zugriff außerhalb der konfigurierten Netzwerkregeln.

Datenverkehr für VM-Datenträger (einschließlich Vorgängen zur Einbindung/Aufhebung der Einbindung sowie E/A-Vorgänge des Datenträgers) ist von Netzwerkregeln nicht betroffen. Der REST-Zugriff auf Seitenblobs wird durch Netzwerkregeln geschützt.

Firewalls und virtuelle Netzwerke werden von klassischen Speicherkonten nicht unterstützt.

Sie können nicht verwaltete Datenträger in Speicherkonten mit angewendeten Netzwerkregeln verwenden, um virtuelle Computer durch Erstellung einer Ausnahme zu sichern und wiederherzustellen. Dieser Prozess ist in diesem Artikel im Abschnitt [Ausnahmen](#exceptions) dokumentiert. Firewallausnahmen gelten nicht für verwaltete Datenträger, da sie bereits von Azure verwaltet werden.

## <a name="change-the-default-network-access-rule"></a>Ändern der Standard-Netzwerkzugriffsregel

Standardmäßig akzeptieren Speicherkonten Verbindungen von Clients in jedem Netzwerk. Um den Zugriff auf ausgewählte Netzwerke zu beschränken, müssen Sie zunächst die Standardaktion ändern.

> [!WARNING]
> Änderungen an Netzwerkregeln können die Fähigkeit von Anwendungen, eine Verbindung mit Azure Storage herzustellen, beeinträchtigen. Durch Festlegen der Standardnetzwerkregel auf **Verweigern** wird sämtlicher Zugriff auf die Daten blockiert – es sei denn, es werden auch bestimmte Netzwerkregeln angewendet, die Zugriff **gewähren**. Gewähren Sie mithilfe von Netzwerkregeln Zugriff auf alle zulässigen Netzwerke, bevor Sie die Standardregel ändern, um Zugriff zu verweigern.

### <a name="managing-default-network-access-rules"></a>Verwalten standardmäßiger Netzwerkzugriffsregeln

Standardmäßige Netzwerkzugriffsregeln für Speicherkonten können über das Azure-Portal, über PowerShell oder per CLI v2 verwaltet werden.

#### <a name="azure-portal"></a>Azure-Portal

1. Navigieren Sie zu dem Speicherkonto, das Sie schützen möchten.

1. Klicken Sie auf das Einstellungsmenü mit dem Namen **Firewalls und virtuelle Netzwerke**.

1. Wenn der Zugriff standardmäßig verweigert werden soll, wählen Sie aus, dass Zugriff über **Ausgewählte Netzwerke** gewährt werden soll. Wenn Sie Datenverkehr aus allen Netzwerken zulassen möchten, wählen Sie aus, dass der Zugriff über **Alle Netzwerke** gewährt werden soll.

1. Klicken Sie zum Übernehmen der Änderungen auf **Speichern**.

#### <a name="powershell"></a>PowerShell

1. Installieren Sie [Azure PowerShell](/powershell/azure/install-Az-ps), und [melden Sie sich an](/powershell/azure/authenticate-azureps).

1. Zeigen Sie den Status der Standardregel für das Speicherkonto an.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Legen Sie die Standardregel auf das standardmäßige Verweigern jeglichen Netzwerkzugriffs fest.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Legen Sie die Standardregel auf das standardmäßige Zulassen von Netzwerkzugriff fest.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLI v2

1. Installieren Sie die [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli), und [melden Sie sich an](/cli/azure/authenticate-azure-cli).

1. Zeigen Sie den Status der Standardregel für das Speicherkonto an.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Legen Sie die Standardregel auf das standardmäßige Verweigern jeglichen Netzwerkzugriffs fest.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Legen Sie die Standardregel auf das standardmäßige Zulassen von Netzwerkzugriff fest.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>Gewähren des Zugriffs aus einem virtuellen Netzwerk

Sie können Speicherkonten so konfigurieren, dass nur über bestimmte Subnetze zugegriffen werden kann. Die zulässigen Subnetze gehören möglicherweise zu einem VNET in demselben Abonnement oder in einem anderen Abonnement – einschließlich Abonnements, die zu einem anderen Azure Active Directory-Mandanten gehören.

Aktivieren Sie einen [Dienstendpunkt](../../virtual-network/virtual-network-service-endpoints-overview.md) für Azure Storage innerhalb des VNETs. Der Dienstendpunkt leitet Datenverkehr aus dem VNET über einen optimalen Pfad an den Azure Storage-Dienst weiter. Mit jeder Anforderung werden außerdem die Identitäten des Subnetzes und des virtuellen Netzwerks übertragen. Administratoren können anschließend Netzwerkregeln für das Speicherkonto konfigurieren, die den Empfang von Anforderungen aus bestimmten Subnetzen in einem VNET zulassen. Clients, denen über diese Netzwerkregeln Zugriff gewährt wird, müssen weiterhin die Autorisierungsanforderungen des Speicherkontos erfüllen, um auf die Daten zugreifen zu können.

Jedes Speicherkonto unterstützt bis zu 200 VNET-Regeln, die mit [IP-Netzwerkregeln](#grant-access-from-an-internet-ip-range) kombiniert werden können.

### <a name="available-virtual-network-regions"></a>Verfügbare Regionen für virtuelle Netzwerke

Dienstendpunkte können in der Regel zwischen virtuellen Netzwerken und Dienstinstanzen in der gleichen Azure-Region verwendet werden. Wenn Dienstendpunkte mit Azure Storage verwendet werden, schließt dieser Bereich auch [Regionspaare](../../best-practices-availability-paired-regions.md) mit ein. Dienstendpunkte ermöglichen Kontinuität während eines regionalen Failovers sowie unterbrechungsfreien Zugriff auf Instanzen von georedundantem Speicher mit Lesezugriff (Read-Access Geo-Redundant Storage, RA-GRS). Netzwerkregeln, die Zugriff aus einem virtuellen Netzwerk auf ein Speicherkonto gewähren, gewähren auch Zugriff auf jede RA-GRS-Instanz.

Wenn Sie die Notfallwiederherstellung für einen regionalen Ausfall planen, sollten Sie die VNETs im Voraus im Regionspaar bereitstellen. Aktivieren Sie Dienstendpunkte für Azure Storage mit Netzwerkregeln, die den Zugriff über diese alternativen virtuellen Netzwerke gewähren. Wenden Sie diese Regeln dann auf Ihre georedundanten Speicherkonten an.

> [!NOTE]
> Dienstendpunkte gelten nicht für Datenverkehr außerhalb der Region des virtuellen Netzwerks und des angegebenen Regionspaars. Netzwerkregeln, die Zugriff aus virtuellen Netzwerken auf Speicherkonten gewähren, können nur in der primären Region eines Speicherkontos oder im angegebenen Regionspaar angewendet werden.

### <a name="required-permissions"></a>Erforderliche Berechtigungen

Wenn Sie eine VNET-Regel auf ein Speicherkonto anwenden möchten, muss der Benutzer über geeignete Berechtigungen für die hinzuzufügenden Subnetze verfügen. Die dazu erforderliche Berechtigung *Dienst mit einem Subnetz verknüpfen* ist Teil der integrierten Rolle *Speicherkontomitwirkender*. Sie kann aber auch benutzerdefinierten Rollendefinitionen hinzugefügt werden.

Das Speicherkonto und die virtuellen Netzwerke, denen Zugriff gewährt wurde, können sich in verschiedenen Abonnements befinden – einschließlich Abonnements, die zu einem anderen Azure AD-Mandanten gehören.

> [!NOTE]
> Die Konfiguration von Regeln, die Zugriff auf Subnetze in virtuellen Netzwerken gewähren, die Teil eines anderen Azure Active Directory-Mandanten sind, wird zurzeit nur über PowerShell, CLI und Rest-APIs unterstützt. Diese Regeln können nicht über das Azure-Portal konfiguriert werden, obwohl sie möglicherweise im Portal angezeigt werden.

### <a name="managing-virtual-network-rules"></a>Verwalten von VNET-Regeln

VNET-Regeln für Speicherkonten können über das Azure-Portal, über PowerShell oder per CLI v2 verwaltet werden.

#### <a name="azure-portal"></a>Azure-Portal

1. Navigieren Sie zu dem Speicherkonto, das Sie schützen möchten.

1. Klicken Sie auf das Einstellungsmenü mit dem Namen **Firewalls und virtuelle Netzwerke**.

1. Vergewissern Sie sich, dass Sie den Zugriff über **Ausgewählte Netzwerke** ausgewählt haben.

1. Wenn Sie mit einer neuen Netzwerkregel den Zugriff auf ein virtuelles Netzwerk zulassen möchten, klicken Sie unter **Virtuelle Netzwerke** auf **Vorhandenes virtuelles Netzwerk hinzufügen**, wählen Sie die Optionen **Virtuelle Netzwerke** und **Subnetze** aus, und klicken Sie anschließend auf **Hinzufügen**. Wenn Sie ein neues virtuelles Netzwerk erstellen und ihm Zugriff gewähren möchten, klicken Sie auf **Neues virtuelles Netzwerk hinzufügen**. Geben Sie die erforderlichen Informationen zum Erstellen des neuen virtuellen Netzwerks an, und klicken Sie anschließend auf **Erstellen**.

    > [!NOTE]
    > Wenn für das ausgewählte virtuelle Netzwerk und die Subnetze noch kein Dienstendpunkt für Azure Storage konfiguriert wurde, können Sie dies im Rahmen dieses Vorgangs nachholen.
    >
    > Zurzeit werden nur virtuelle Netzwerke, die zu demselben Azure Active Directory-Mandanten gehören, während der Regelerstellung zur Auswahl angezeigt. Verwenden Sie PowerShell, die CLI oder REST-APIs, um den Zugriff auf ein Subnetz in einem virtuellen Netzwerk zu gewähren, das zu einem anderen Mandanten gehört.

1. Wenn Sie eine Regel für virtuelle Netzwerke oder Subnetze entfernen möchten, klicken Sie auf **...** , um das Kontextmenü für das virtuelle Netzwerk oder Subnetz zu öffnen, und klicken Sie anschließend auf **Entfernen**.

1. Klicken Sie zum Übernehmen der Änderungen auf **Speichern**.

#### <a name="powershell"></a>PowerShell

1. Installieren Sie [Azure PowerShell](/powershell/azure/install-Az-ps), und [melden Sie sich an](/powershell/azure/authenticate-azureps).

1. Listen Sie die VNET-Regeln auf.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Aktivieren Sie den Dienstendpunkt für Azure Storage in einem vorhandenen virtuellen Netzwerk und Subnetz.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Fügen Sie eine Netzwerkregel für ein virtuelles Netzwerk und Subnetz hinzu.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Wenn Sie eine Netzwerkregel für ein Subnetz in einem VNET hinzufügen möchten, das zu einem anderen Azure AD-Mandanten gehört, verwenden Sie einen voll qualifizierten **VirtualNetworkResourceId**-Parameter im Format „/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name“.

1. Entfernen Sie eine Netzwerkregel für ein virtuelles Netzwerk und Subnetz.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Die Standardregel muss auf **Verweigern** festgelegt sein (siehe [Festlegen der Standardregel](#change-the-default-network-access-rule)). Andernfalls haben die Netzwerkregeln keine Wirkung.

#### <a name="cliv2"></a>CLI v2

1. Installieren Sie die [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli), und [melden Sie sich an](/cli/azure/authenticate-azure-cli).

1. Listen Sie die VNET-Regeln auf.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Aktivieren Sie den Dienstendpunkt für Azure Storage in einem vorhandenen virtuellen Netzwerk und Subnetz.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Fügen Sie eine Netzwerkregel für ein virtuelles Netzwerk und Subnetz hinzu.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Verwenden Sie zum Hinzufügen einer Regel für ein Subnetz in einem VNET, das zu einem anderen Azure AD-Mandanten gehört, eine vollqualifizierte Subnetz-ID im Format „/subscriptions/\<subscription-ID\>/resourceGroups/\<resourceGroup-Name\>/providers/Microsoft.Network/virtualNetworks/\<vNet-name\>/subnets/\<subnet-name\>“.
    >
    > Sie können den Parameter **Abonnement** verwenden, um die Subnetz-ID für ein VNET abzurufen, das zu einem anderen Azure AD-Mandanten gehört.

1. Entfernen Sie eine Netzwerkregel für ein virtuelles Netzwerk und Subnetz.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Die Standardregel muss auf **Verweigern** festgelegt sein (siehe [Festlegen der Standardregel](#change-the-default-network-access-rule)). Andernfalls haben die Netzwerkregeln keine Wirkung.

## <a name="grant-access-from-an-internet-ip-range"></a>Gewähren von Zugriff aus einem Internet-IP-Adressbereich

Sie können Speicherkonten so konfigurieren, dass der Zugriff über bestimmte öffentliche Internet-IP-Adressbereichen zugelassen wird. Diese Konfiguration gewährt bestimmten internetbasierten Diensten und lokalen Netzwerken Zugriff und blockiert gleichzeitig den allgemeinen Internetdatenverkehr.

Geben Sie zulässige Internetadressbereiche in [CIDR-Notation](https://tools.ietf.org/html/rfc4632) im Format *16.17.18.0/24* oder als einzelne IP-Adressen (beispielsweise *16.17.18.19*) an.

   > [!NOTE]
   > Kleine Adressbereiche mit der Präfixgröße „/ 31“ oder „/ 32“ werden nicht unterstützt. Diese Bereiche müssen mit einzelnen IP-Adressregeln konfiguriert werden.

IP-Netzwerkregeln sind nur für **öffentliche Internet**-IP-Adressen zulässig. Für private Netzwerke reservierte IP-Adressbereiche (wie in [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3) definiert) sind in IP-Adressregeln nicht zulässig. Private Netzwerke enthalten Adressen, die mit _10.*_ , _172.16.*_  - _172.31.*_ und _192.168.*_ beginnen.

   > [!NOTE]
   > IP-Netzwerkregeln haben keine Auswirkungen auf Anforderungen, die aus der Azure-Region stammen, in der sich auch das Speicherkonto befindet. Verwenden Sie [VNET-Regeln](#grant-access-from-a-virtual-network), um Anforderungen aus der gleichen Region zuzulassen.

  > [!NOTE]
  > Dienste, die in derselben Region wie das Speicherkonto bereitgestellt werden, verwenden für die Kommunikation private Azure-IP-Adressen. Deshalb können Sie den Zugriff auf bestimmte Azure-Dienste nicht basierend auf deren IP-Adressbereich für öffentlichen ausgehenden Datenverkehr einschränken.

Für die Konfiguration von Storage-Firewallregeln werden nur IPv4-Adressen unterstützt.

Jedes Speicherkonto unterstützt bis zu 200 IP-Netzwerkregeln.

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurieren des Zugriffs aus lokalen Netzwerken

Wenn Sie mit einer IP-Netzwerkregel den Zugriff über Ihre lokalen Netzwerke auf das Speicherkonto gewähren möchten, müssen Sie die von Ihrem Netzwerk verwendeten Internet-IP-Adressen ermitteln. Hilfe erhalten Sie von Ihrem Netzwerkadministrator.

Wenn Sie [ExpressRoute](../../expressroute/expressroute-introduction.md) lokal für öffentliches Peering oder für Microsoft-Peering verwenden, müssen Sie die verwendeten NAT-IP-Adressen identifizieren. Beim öffentlichen Peering werden für jede ExpressRoute-Verbindung standardmäßig zwei NAT-IP-Adressen verwendet. Diese werden auf den Datenverkehr der Azure-Dienste angewendet, wenn der Datenverkehr im Microsoft Azure-Netzwerk-Backbone eintrifft. Beim Microsoft-Peering werden die verwendeten NAT-IP-Adressen entweder vom Kunden oder vom Dienstanbieter bereitgestellt. Um den Zugriff auf Ihre Dienstressourcen zuzulassen, müssen Sie diese öffentlichen IP-Adressen in der Ressourceneinstellung der IP-Firewall zulassen. [Öffnen Sie über das Azure-Portal ein Supportticket für ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview), um die IP-Adressen Ihrer ExpressRoute-Verbindung für öffentliches Peering zu ermitteln. Erfahren Sie mehr über [NAT für öffentliches ExpressRoute-Peering und Microsoft-Peering](../../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering).

### <a name="managing-ip-network-rules"></a>Verwalten von IP-Netzwerkregeln

IP-Netzwerkregeln für Speicherkonten können über das Azure-Portal, über PowerShell oder per CLI v2 verwaltet werden.

#### <a name="azure-portal"></a>Azure-Portal

1. Navigieren Sie zu dem Speicherkonto, das Sie schützen möchten.

1. Klicken Sie auf das Einstellungsmenü mit dem Namen **Firewalls und virtuelle Netzwerke**.

1. Vergewissern Sie sich, dass Sie den Zugriff über **Ausgewählte Netzwerke** ausgewählt haben.

1. Geben Sie unter **Firewall** > **Adressbereich** die IP-Adresse oder den IP-Adressbereich (im CIDR-Format) ein, um Zugriff auf einen Internet-IP-Adressbereich zu gewähren.

1. Wenn Sie eine IP-Netzwerkregel entfernen möchten, klicken Sie auf das Papierkorbsymbol neben dem Adressbereich.

1. Klicken Sie zum Übernehmen der Änderungen auf **Speichern**.

#### <a name="powershell"></a>PowerShell

1. Installieren Sie [Azure PowerShell](/powershell/azure/install-Az-ps), und [melden Sie sich an](/powershell/azure/authenticate-azureps).

1. Listen Sie IP-Netzwerkregeln auf.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Fügen Sie eine Netzwerkregel für eine einzelne IP-Adresse hinzu.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Fügen Sie eine Netzwerkregel für einen IP-Adressbereich hinzu.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Entfernen Sie eine Netzwerkregel für eine einzelne IP-Adresse.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Entfernen Sie eine Netzwerkregel für einen IP-Adressbereich.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Die Standardregel muss auf **Verweigern** festgelegt sein (siehe [Festlegen der Standardregel](#change-the-default-network-access-rule)). Andernfalls haben die Netzwerkregeln keine Wirkung.

#### <a name="cliv2"></a>CLI v2

1. Installieren Sie die [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli), und [melden Sie sich an](/cli/azure/authenticate-azure-cli).

1. Listen Sie IP-Netzwerkregeln auf.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Fügen Sie eine Netzwerkregel für eine einzelne IP-Adresse hinzu.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Fügen Sie eine Netzwerkregel für einen IP-Adressbereich hinzu.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Entfernen Sie eine Netzwerkregel für eine einzelne IP-Adresse.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Entfernen Sie eine Netzwerkregel für einen IP-Adressbereich.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Die Standardregel muss auf **Verweigern** festgelegt sein (siehe [Festlegen der Standardregel](#change-the-default-network-access-rule)). Andernfalls haben die Netzwerkregeln keine Wirkung.

## <a name="exceptions"></a>Ausnahmen

Mithilfe von Netzwerkregeln können Sie in den meisten Szenarien eine sichere Umgebung für Verbindungen zwischen Ihren Anwendungen und Ihren Daten erstellen. Einige Anwendungen sind jedoch von Azure-Diensten abhängig, die über Regeln für virtuelle Netzwerke oder IP-Adressen nicht eindeutig isoliert werden können. Diese Dienste müssen jedoch für den Speicher gewährt werden, um eine vollständige Anwendungsfunktionalität zu ermöglichen. In solchen Fällen können Sie über die Einstellung *_Vertrauenswürdige Microsoft-Dienste zulassen_* diesen Diensten den Zugriff auf Ihre Daten, Protokolle oder Analysen ermöglichen.

### <a name="trusted-microsoft-services"></a>Vertrauenswürdige Microsoft-Dienste

Einige Microsoft-Dienste werden aus Netzwerken betrieben, die in Ihren Netzwerkregeln nicht enthalten sein können. Sie können einer Teilmenge solcher vertrauenswürdiger Microsoft-Dienste Zugriff auf das Speicherkonto gewähren und gleichzeitig Netzwerkregeln für andere Apps beibehalten. Diese vertrauenswürdigen Dienste stellen dann mithilfe einer strengen Authentifizierung eine sichere Verbindung mit Ihrem Speicherkonto her. Wir haben zwei Modi für den vertrauenswürdigen Zugriff für Microsoft-Dienste ermöglicht.

- Ressourcen einiger Dienste können, **sofern sie in Ihrem Abonnement registriert sind**, für bestimmte Vorgänge auf Ihr Speicherkonto **im gleichen Abonnement** zugreifen. Hierzu zählen beispielsweise Sicherungsvorgänge und das Schreiben von Protokollen.
- Ressourcen einiger Dienste kann durch **Zuweisen einer Azure-Rolle** zur vom System zugewiesenen verwalteten Identität der explizite Zugriff auf Ihr Speicherkonto gewährt werden.


Wenn Sie die Einstellung **Vertrauenswürdige Microsoft-Dienste zulassen** festlegen, wird Ressourcen der folgenden Dienste, die im gleichen Abonnement registriert sind wie das Speicherkonto, Zugriff für eine eingeschränkte Gruppe von Vorgängen gewährt:

| Dienst                  | Name des Ressourcenanbieters     | Zulässige Vorgänge                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft.RecoveryServices | Ausführen von Sicherungen und Wiederherstellungen von nicht verwalteten Datenträgern in virtuellen IAAS-Computern (nicht für verwaltete Datenträger erforderlich). [Weitere Informationen](../../backup/backup-overview.md) |
| Azure Data Box           | Microsoft.DataBox          | Ermöglicht den Import von Daten in Azure über die Data Box. [Weitere Informationen](../../databox/data-box-overview.md) |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Erstellung benutzerdefinierter Images und Installation von Artefakten. [Weitere Informationen](../../devtest-labs/devtest-lab-overview.md) |
| Azure Event Grid         | Microsoft.EventGrid        | Aktivieren Sie Blob Storage-Ereignisveröffentlichung, und erlauben Sie Event Grid die Veröffentlichung in Speicherwarteschlangen. Erfahren Sie mehr über [Blob Storage-Ereignisse](../../event-grid/overview.md#event-sources) und das [Veröffentlichen in Warteschlangen](../../event-grid/event-handlers.md). |
| Azure Event Hubs         | Microsoft.EventHub         | Archivieren von Daten mit Event Hubs Capture. [Weitere Informationen](../../event-hubs/event-hubs-capture-overview.md). |
| Azure-Dateisynchronisierung          | Microsoft.StorageSync      | Ermöglicht das Transformieren eines lokalen Dateiservers in einen Cache für Azure-Dateifreigaben. Ermöglicht die Synchronisierung mit mehreren Standorten, eine schnelle Notfallwiederherstellung und die cloudbasierte Sicherung. [Weitere Informationen](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | Stellen Sie die anfänglichen Inhalte des Standarddateisystems für einen neuen HDInsight-Cluster bereit. [Weitere Informationen](../../hdinsight/hdinsight-hadoop-use-blob-storage.md) |
| Azure Import/Export      | Microsoft.ImportExport     | Ermöglicht das Importieren von Daten in Azure Storage oder das Exportieren von Daten aus Azure Storage mit dem Dienst „Azure Storage Import/Export“. [Weitere Informationen](./storage-import-export-service.md)  |
| Azure Monitor            | Microsoft.Insights         | Dieser Dienst ermöglicht das Schreiben von Überwachungsdaten in ein sicheres Speicherkonto, einschließlich Ressourcenprotokollen, Azure Active Directory-Anmelde- und -Überwachungsprotokollen sowie Microsoft Intune-Protokollen. [Weitere Informationen](../../azure-monitor/platform/roles-permissions-security.md) |
| Azure-Netzwerke         | Microsoft.Network          | Speichern und analysieren Sie Netzwerk-Datenverkehrsprotokolle, beispielsweise mit Network Watcher und Traffic Analytics-Diensten. [Weitere Informationen](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Aktivieren Sie die Replikation für die Notfallwiederherstellung von virtuellen Azure-IaaS-Computern bei Verwendung von firewallfähigen Cache-, Quell- oder Zielspeicherkonten.  [Weitere Informationen](../../site-recovery/azure-to-azure-tutorial-enable-replication.md) |

Die Einstellung **Hiermit erlauben Sie vertrauenswürdigen Microsoft-Diensten ...** ermöglicht auch einer bestimmten Instanz der folgenden Dienste den Zugriff auf das Speicherkonto, wenn Sie der [vom System zugewiesenen verwalteten Identität](../../active-directory/managed-identities-azure-resources/overview.md) für diese Ressourceninstanz explizit [eine Azure-Rolle zuweisen](storage-auth-aad.md#assign-azure-roles-for-access-rights). In diesem Fall entspricht der Zugriffsbereich für die Instanz der Azure-Rolle, die der verwalteten Identität zugewiesen ist.

| Dienst                        | Name des Ressourcenanbieters                 | Zweck            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure API Management           | Microsoft.ApiManagement/service        | Ermöglicht dem API Management-Dienst Zugriff auf Speicherkonten hinter der Firewall mithilfe von Richtlinien. [Weitere Informationen](../../api-management/api-management-authentication-policies.md#use-managed-identity-in-send-request-policy) |
| Azure Cognitive Search         | Microsoft.Search/searchServices        | Ermöglicht Cognitive Search-Diensten den Zugriff auf Speicherkonten zur Indizierung, Verarbeitung und Abfrage. |
| Azure Cognitive Services       | Microsoft.CognitiveService             | Ermöglicht Cognitive Services den Zugriff auf Speicherkonten. |
| Azure Container Registry Tasks | Microsoft.ContainerRegistry/registries | ACR Tasks können beim Erstellen von Containerimages auf Speicherkonten zugreifen. |
| Azure Data Factory             | Microsoft.DataFactory/factories        | Ermöglicht den Zugriff auf Speicherkonten über die ADF Runtime. |
| Azure Data Share               | Microsoft.DataShare/accounts           | Ermöglicht den Zugriff auf Speicherkonten über Data Share. |
| Azure IoT Hub                  | Microsoft.Devices/IotHubs              | Ermöglicht das Schreiben von Daten aus einem IoT-Hub in den Blobspeicher. [Weitere Informationen](../../iot-hub/virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) |
| Azure Logic Apps               | Microsoft.Logic/workflows              | Ermöglicht Logik-Apps den Zugriff auf Speicherkonten. [Weitere Informationen](../../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) |
| Azure Machine Learning-Dienst | Microsoft.MachineLearningServices      | Autorisierte Azure Machine Learning-Arbeitsbereiche schreiben Experimentausgaben, Modelle und Protokolle in Blob Storage und lesen die Daten. [Weitere Informationen](../../machine-learning/how-to-network-security-overview.md#secure-the-workspace-and-associated-resources) | 
| Azure Synapse Analytics       | Microsoft.Sql                          | Ermöglicht den Import und Export von Daten aus bestimmten SQL-Datenbanken unter Verwendung der COPY-Anweisung oder von PolyBase. [Weitere Informationen](../../azure-sql/database/vnet-service-endpoint-rule-overview.md) |
| Azure SQL-Datenbank       | Microsoft.Sql                          | Ermöglicht den [Import](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage) von Daten aus Speicherkonten sowie das [Schreiben](../../azure-sql/database/audit-write-storage-account-behind-vnet-firewall.md) von Überwachungsdaten in Speicherkonten hinter einer Firewall. |
| Azure Stream Analytics         | Microsoft.StreamAnalytics             | Ermöglicht das Schreiben von Daten aus einem Streamingauftrag in den BLOB-Speicher. [Weitere Informationen](../../stream-analytics/blob-output-managed-identity.md) |
| Azure Synapse Analytics        | Microsoft.Synapse/workspaces          | Dies ermöglicht in Azure Storage den Zugriff auf Daten von Azure Synapse Analytics. |


### <a name="storage-analytics-data-access"></a>Zugriff auf Storage Analytics-Daten

In manchen Fällen ist der Lesezugriff auf Ressourcenprotokolle und -metriken von außerhalb des Netzwerks erforderlich. Wenn Sie für vertrauenswürdige Dienste den Zugriff auf das Speicherkonto konfigurieren, können Sie den Lesezugriff für die Protokolldateien, Metriktabellen oder beides erlauben. [Weitere Informationen zum Arbeiten mit Storage Analytics](./storage-analytics.md)

### <a name="managing-exceptions"></a>Verwalten von Ausnahmen

Netzwerkregelausnahmen können über das Azure-Portal, über PowerShell oder per Azure CLI v2 verwaltet werden.

#### <a name="azure-portal"></a>Azure-Portal

1. Navigieren Sie zu dem Speicherkonto, das Sie schützen möchten.

1. Klicken Sie auf das Einstellungsmenü mit dem Namen **Firewalls und virtuelle Netzwerke**.

1. Vergewissern Sie sich, dass Sie den Zugriff über **Ausgewählte Netzwerke** ausgewählt haben.

1. Wählen Sie unter **Ausnahmen** die Ausnahmen aus, die Sie gewähren möchten.

1. Klicken Sie zum Übernehmen der Änderungen auf **Speichern**.

#### <a name="powershell"></a>PowerShell

1. Installieren Sie [Azure PowerShell](/powershell/azure/install-Az-ps), und [melden Sie sich an](/powershell/azure/authenticate-azureps).

1. Zeigen Sie die Ausnahmen für die Speicherkonto-Netzwerkregeln an.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Konfigurieren Sie die Ausnahmen von den Speicherkonto-Netzwerkregeln.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Entfernen Sie die Ausnahmen von den Speicherkonto-Netzwerkregeln.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Die Standardregel muss auf **Verweigern** festgelegt sein (siehe [Festlegen der Standardregel](#change-the-default-network-access-rule)). Andernfalls hat das Entfernen von Ausnahmen keine Wirkung.

#### <a name="cliv2"></a>CLI v2

1. Installieren Sie die [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli), und [melden Sie sich an](/cli/azure/authenticate-azure-cli).

1. Zeigen Sie die Ausnahmen für die Speicherkonto-Netzwerkregeln an.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. Konfigurieren Sie die Ausnahmen von den Speicherkonto-Netzwerkregeln.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Entfernen Sie die Ausnahmen von den Speicherkonto-Netzwerkregeln.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Die Standardregel muss auf **Verweigern** festgelegt sein (siehe [Festlegen der Standardregel](#change-the-default-network-access-rule)). Andernfalls hat das Entfernen von Ausnahmen keine Wirkung.

## <a name="next-steps"></a>Nächste Schritte

Unter [Dienstendpunkte](../../virtual-network/virtual-network-service-endpoints-overview.md) erhalten Sie weitere Informationen zu Dienstendpunkten in Azure-Netzwerken.

Im [Azure Storage-Sicherheitsleitfaden](../blobs/security-recommendations.md) erhalten Sie weitere Informationen zur Sicherheit von Azure Storage.