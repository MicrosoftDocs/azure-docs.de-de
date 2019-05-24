---
title: Konfigurieren von Azure Storage-Firewalls und virtuellen Netzwerken | Microsoft-Dokumentation
description: Konfigurieren Sie mehrstufige Netzwerksicherheit für Ihr Speicherkonto.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 56a25c95d9bc01078b3eff3729a8a693ee3cf510
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65520251"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurieren von Azure Storage-Firewalls und virtuellen Netzwerken

Azure Storage bietet ein mehrschichtiges Sicherheitsmodell. Dank dieses Modells können Sie Ihre Speicherkonten für eine bestimmte Gruppe unterstützter Netzwerke schützen. Wenn Netzwerkregeln konfiguriert wurden, können nur Anwendungen, die Daten aus der angegebenen Gruppe von Netzwerken anfordern, auf ein Speicherkonto zugreifen.

Eine Anwendung, die bei aktivierten Netzwerkregeln auf ein Speicherkonto zugreift, benötigt eine ordnungsgemäße Autorisierung für die Anforderung. Für die Autorisierung können Azure Active Directory (Azure AD)-Anmeldeinformationen für Blobs und Warteschlangen mit einem gültigen Kontozugriffsschlüssel oder mit einem SAS-Token verwendet werden.

> [!IMPORTANT]
> Wenn Sie Firewallregeln für Ihr Speicherkonto aktivieren, werden eingehende Datenanforderungen standardmäßig blockiert – es sei denn, die Anforderungen stammen von einem Dienst, der innerhalb eines virtuellen Azure-Netzwerks (VNET) agiert. Unter anderem werden Anforderungen von anderen Azure-Diensten, aus dem Azure-Portal und von Protokollierungs-/Metrikdiensten blockiert.
>
> Sie können Azure-Diensten, die innerhalb eines VNETs agieren, Zugriff gewähren, indem Sie das Subnetz der Dienstinstanz zulassen. Der im nächsten Abschnitt beschriebene [Ausnahmenmechanismus](#exceptions) ermöglicht eine begrenzte Anzahl von Szenarien. Der Zugriff auf das Azure-Portal muss über einen Computer erfolgen, der sich innerhalb der von Ihnen eingerichteten vertrauenswürdigen Grenze (IP-Adresse oder VNET) befindet.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Szenarien

Konfigurieren Sie Speicherkonten, um standardmäßig den Zugriff auf Datenverkehr aus allen Netzwerken (einschließlich Internetdatenverkehr) zu verweigern. Gewähren Sie anschließend Zugriff auf Datenverkehr aus bestimmten VNETs. Mit dieser Konfiguration können Sie eine sichere Netzwerkgrenze für Ihre Anwendungen erstellen. Darüber hinaus können Sie Zugriff auf öffentliche Internet-IP-Adressbereiche gewähren, um Verbindungen von bestimmten Internetclients oder lokalen Clients zu ermöglichen.

Netzwerkregeln werden für alle Netzwerkprotokolle in Azure Storage, einschließlich REST und SMB, erzwungen. Für den Datenzugriff über Tools wie Azure-Portal, Storage-Explorer und AZCopy sind explizite Netzwerkregeln erforderlich.

Netzwerkregeln können auf bereits vorhandene Speicherkonten oder beim Erstellen neuer Speicherkonten angewendet werden.

Angewendete Netzwerkregeln werden für alle Anforderungen erzwungen. SAS-Token, die Zugriff auf eine bestimmte IP-Adresse gewähren, beschränken den Zugriff des Tokeninhabers, gewähren jedoch keinen neuen Zugriff außerhalb der konfigurierten Netzwerkregeln.

Datenverkehr für VM-Datenträger (einschließlich Vorgängen zur Einbindung/Aufhebung der Einbindung sowie E/A-Vorgänge des Datenträgers) ist von Netzwerkregeln nicht betroffen. Der REST-Zugriff auf Seitenblobs wird durch Netzwerkregeln geschützt.

Firewalls und virtuelle Netzwerke werden von klassischen Speicherkonten nicht unterstützt.

Sie können nicht verwaltete Datenträger in Speicherkonten mit angewendeten Netzwerkregeln verwenden, um virtuelle Computer durch Erstellung einer Ausnahme zu sichern und wiederherzustellen. Dieser Prozess ist in diesem Artikel im Abschnitt [Ausnahmen](#exceptions) dokumentiert. Firewallausnahmen gelten nicht für verwaltete Datenträger, da sie bereits von Azure verwaltet werden.

## <a name="change-the-default-network-access-rule"></a>Ändern der Standard-Netzwerkzugriffsregel

Standardmäßig akzeptieren Speicherkonten Verbindungen von Clients in jedem Netzwerk. Um den Zugriff auf ausgewählte Netzwerke zu beschränken, müssen Sie zunächst die Standardaktion ändern.

> [!WARNING]
> Änderungen an Netzwerkregeln können die Fähigkeit von Anwendungen, eine Verbindung mit Azure Storage herzustellen, beeinträchtigen. Durch Festlegen der Standardnetzwerkregel auf **Verweigern** wird sämtlicher Zugriff auf die Daten blockiert – es sei denn, es werden auch bestimmte Netzwerkregeln angewendet, zum Zugriff zu **gewähren**. Gewähren Sie mithilfe von Netzwerkregeln Zugriff auf alle zulässigen Netzwerke, bevor Sie die Standardregel ändern, um Zugriff zu verweigern.

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

Sie können Speicherkonten so konfigurieren, dass nur über bestimmte VNETs zugegriffen werden kann.

Aktivieren Sie einen [Dienstendpunkt](/azure/virtual-network/virtual-network-service-endpoints-overview) für Azure Storage innerhalb des VNETs. Dieser Endpunkt sorgt für eine optimale Datenverkehrsroute zum Azure Storage-Dienst. Mit jeder Anforderung wird zudem die Identität des virtuellen Netzwerks und des Subnetzes übertragen. Administratoren können anschließend Netzwerkregeln für das Speicherkonto konfigurieren, die den Empfang von Anforderungen aus bestimmten Subnetzen im VNET zulassen. Clients, denen über diese Netzwerkregeln Zugriff gewährt wird, müssen weiterhin die Autorisierungsanforderungen des Speicherkontos erfüllen, um auf die Daten zugreifen zu können.

Jedes Speicherkonto unterstützt bis zu 100 VNET-Regeln, die mit [IP-Netzwerkregeln](#grant-access-from-an-internet-ip-range) kombiniert werden können.

### <a name="available-virtual-network-regions"></a>Verfügbare Regionen für virtuelle Netzwerke

Dienstendpunkte können in der Regel zwischen virtuellen Netzwerken und Dienstinstanzen in der gleichen Azure-Region verwendet werden. Wenn Dienstendpunkte mit Azure Storage verwendet werden, schließt dieser Bereich auch [Regionspaare](/azure/best-practices-availability-paired-regions) mit ein. Dienstendpunkte ermöglichen Kontinuität während eines regionalen Failovers sowie unterbrechungsfreien Zugriff auf Instanzen von georedundantem Speicher mit Lesezugriff (Read-Access Geo-Redundant Storage, RA-GRS). Netzwerkregeln, die Zugriff aus einem virtuellen Netzwerk auf ein Speicherkonto gewähren, gewähren auch Zugriff auf jede RA-GRS-Instanz.

Wenn Sie die Notfallwiederherstellung für einen regionalen Ausfall planen, sollten Sie die VNETs im Voraus im Regionspaar bereitstellen. Aktivieren Sie Dienstendpunkte für Azure Storage mit Netzwerkregeln, die den Zugriff über diese alternativen virtuellen Netzwerke gewähren. Wenden Sie diese Regeln dann auf Ihre georedundanten Speicherkonten an.

> [!NOTE]
> Dienstendpunkte gelten nicht für Datenverkehr außerhalb der Region des virtuellen Netzwerks und des angegebenen Regionspaars. Netzwerkregeln, die Zugriff aus virtuellen Netzwerken auf Speicherkonten gewähren, können nur in der primären Region eines Speicherkontos oder im angegebenen Regionspaar angewendet werden.

### <a name="required-permissions"></a>Erforderliche Berechtigungen

Wenn Sie eine VNET-Regel auf ein Speicherkonto anwenden möchten, muss der Benutzer über geeignete Berechtigungen für die hinzuzufügenden Subnetze verfügen. Die dazu erforderliche Berechtigung *Dienst mit einem Subnetz verknüpfen* ist Teil der integrierten Rolle *Speicherkontomitwirkender*. Sie kann aber auch benutzerdefinierten Rollendefinitionen hinzugefügt werden.

Das Speicherkonto und die virtuellen Netzwerke, denen Zugriff gewährt wurde, können sich in unterschiedlichen Abonnements befinden, diese müssen jedoch zum gleichen Azure AD-Mandanten gehören.

### <a name="managing-virtual-network-rules"></a>Verwalten von VNET-Regeln

VNET-Regeln für Speicherkonten können über das Azure-Portal, über PowerShell oder per CLI v2 verwaltet werden.

#### <a name="azure-portal"></a>Azure-Portal

1. Navigieren Sie zu dem Speicherkonto, das Sie schützen möchten.

1. Klicken Sie auf das Einstellungsmenü mit dem Namen **Firewalls und virtuelle Netzwerke**.

1. Vergewissern Sie sich, dass Sie den Zugriff über **Ausgewählte Netzwerke** ausgewählt haben.

1. Wenn Sie mit einer neuen Netzwerkregel den Zugriff auf ein virtuelles Netzwerk zulassen möchten, klicken Sie unter **Virtuelle Netzwerke** auf **Vorhandenes virtuelles Netzwerk hinzufügen**, wählen Sie die Optionen **Virtuelle Netzwerke** und **Subnetze** aus, und klicken Sie anschließend auf **Hinzufügen**. Wenn Sie ein neues virtuelles Netzwerk erstellen und ihm Zugriff gewähren möchten, klicken Sie auf **Neues virtuelles Netzwerk hinzufügen**. Geben Sie die erforderlichen Informationen zum Erstellen des neuen virtuellen Netzwerks an, und klicken Sie anschließend auf **Erstellen**.

    > [!NOTE]
    > Wenn für das ausgewählte virtuelle Netzwerk und die Subnetze noch kein Dienstendpunkt für Azure Storage konfiguriert wurde, können Sie dies im Rahmen dieses Vorgangs nachholen.

1. Wenn Sie eine Regel für virtuelle Netzwerke oder Subnetze entfernen möchten, klicken Sie auf **...**, um das Kontextmenü für das virtuelle Netzwerk oder Subnetz zu öffnen, und klicken Sie anschließend auf **Entfernen**.

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
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

1. Entfernen Sie eine Netzwerkregel für ein virtuelles Netzwerk und Subnetz.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Die Standardregel muss auf **Verweigern** festgelegt sein (siehe [Festlegen der Standardregel](#change-the-default-network-access-rule)). Andernfalls haben die Netzwerkregeln keine Wirkung.

## <a name="grant-access-from-an-internet-ip-range"></a>Gewähren von Zugriff aus einem Internet-IP-Adressbereich

Sie können Speicherkonten so konfigurieren, dass der Zugriff über bestimmte öffentliche Internet-IP-Adressbereichen zugelassen wird. Diese Konfiguration gewährt bestimmten internetbasierten Diensten und lokalen Netzwerken Zugriff und blockiert gleichzeitig den allgemeinen Internetdatenverkehr.

Geben Sie zulässige Internetadressbereiche in [CIDR-Notation](https://tools.ietf.org/html/rfc4632) im Format *16.17.18.0/24* oder als einzelne IP-Adressen (beispielsweise *16.17.18.19*) an.

   > [!NOTE]
   > Kleine Adressbereiche mit der Präfixgröße „/ 31“ oder „/ 32“ werden nicht unterstützt. Diese Bereiche müssen mit einzelnen IP-Adressregeln konfiguriert werden.

IP-Netzwerkregeln sind nur für **öffentliche Internet**-IP-Adressen zulässig. Für private Netzwerke reservierte IP-Adressbereiche (wie in [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3) definiert) sind in IP-Adressregeln nicht zulässig. Private Netzwerke enthalten Adressen, die mit _10.*_, _172.16.*_ - _172.31.*_ und _192.168.*_ beginnen.

   > [!NOTE]
   > IP-Netzwerkregeln haben keine Auswirkungen auf Anforderungen, die aus der Azure-Region stammen, in der sich auch das Speicherkonto befindet. Verwenden Sie [VNET-Regeln](#grant-access-from-a-virtual-network), um Anforderungen aus der gleichen Region zuzulassen.

Derzeit werden nur IPv4-Adressen unterstützt.

Jedes Speicherkonto unterstützt bis zu 100 IP-Netzwerkregeln, die mit [VNET-Regeln](#grant-access-from-a-virtual-network) kombiniert werden können.

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurieren des Zugriffs aus lokalen Netzwerken

Wenn Sie mit einer IP-Netzwerkregel den Zugriff über Ihre lokalen Netzwerke auf das Speicherkonto gewähren möchten, müssen Sie die von Ihrem Netzwerk verwendeten Internet-IP-Adressen ermitteln. Hilfe erhalten Sie von Ihrem Netzwerkadministrator.

Wenn Sie [ExpressRoute](/azure/expressroute/expressroute-introduction) lokal für öffentliches Peering oder für Microsoft-Peering verwenden, müssen Sie die verwendeten NAT-IP-Adressen identifizieren. Beim öffentlichen Peering werden für jede ExpressRoute-Verbindung standardmäßig zwei NAT-IP-Adressen verwendet. Diese werden auf den Datenverkehr der Azure-Dienste angewendet, wenn der Datenverkehr im Microsoft Azure-Netzwerk-Backbone eintrifft. Beim Microsoft-Peering werden die verwendeten NAT-IP-Adressen entweder vom Kunden oder vom Dienstanbieter bereitgestellt. Um den Zugriff auf Ihre Dienstressourcen zuzulassen, müssen Sie diese öffentlichen IP-Adressen in der Ressourceneinstellung der IP-Firewall zulassen. [Öffnen Sie über das Azure-Portal ein Supportticket für ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview), um die IP-Adressen Ihrer ExpressRoute-Verbindung für öffentliches Peering zu ermitteln. Erfahren Sie mehr über [NAT für öffentliches ExpressRoute-Peering und Microsoft-Peering](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering).

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

Netzwerkregeln ermöglichen in den meisten Szenarien eine sichere Netzwerkkonfiguration. Manchmal sind jedoch Ausnahmen erforderlich, damit sämtliche Funktionen genutzt werden können. Speicherkonten können mit Ausnahmen für vertrauenswürdige Microsoft-Dienste und für den Zugriff auf Speicheranalysedaten konfiguriert werden.

### <a name="trusted-microsoft-services"></a>Vertrauenswürdige Microsoft-Dienste

Einige Microsoft-Dienste, die mit Speicherkonten interagieren, agieren von Netzwerken aus, denen nicht mithilfe von Netzwerkregeln Zugriff gewährt werden kann.

Lassen Sie für vertrauenswürdige Microsoft-Dienste die Umgehung der Netzwerkregeln zu, damit solche Dienste ordnungsgemäß funktionieren. Diese Dienste verwenden dann eine strenge Authentifizierung, um auf das Speicherkonto zuzugreifen.

Wenn Sie die Ausnahme **Vertrauenswürdigen Microsoft-Diensten die Umgehung dieser Firewall erlauben?** aktivieren, wird den folgenden Diensten Zugriff auf das Speicherkonto gewährt (sofern sie in Ihrem Abonnement registriert sind):

|Dienst|Name des Ressourcenanbieters|Zweck|
|:------|:---------------------|:------|
|Azure Backup|Microsoft.RecoveryServices|Ausführen von Sicherungen und Wiederherstellungen von nicht verwalteten Datenträgern in virtuellen IAAS-Computern (nicht für verwaltete Datenträger erforderlich). [Weitere Informationen](/azure/backup/backup-introduction-to-azure-backup)|
|Azure Data Box|Microsoft.DataBox|Ermöglicht den Import von Daten in Azure über die Data Box. [Weitere Informationen](/azure/databox/data-box-overview)|
|Azure DevTest Labs|Microsoft.DevTestLab|Erstellung benutzerdefinierter Images und Installation von Artefakten. [Weitere Informationen](/azure/devtest-lab/devtest-lab-overview)|
|Azure Event Grid|Microsoft.EventGrid|Aktivieren Sie Blob Storage-Ereignisveröffentlichung, und erlauben Sie Event Grid die Veröffentlichung in Speicherwarteschlangen. Erfahren Sie mehr über [Blob Storage-Ereignisse](/azure/event-grid/event-sources) und das [Veröffentlichen in Warteschlangen](/azure/event-grid/event-handlers).|
|Azure Event Hubs|Microsoft.EventHub|Archivieren von Daten mit Event Hubs Capture. [Weitere Informationen](/azure/event-hubs/event-hubs-capture-overview).|
|Azure HDInsight|Microsoft.HDInsight|Stellen Sie die anfänglichen Inhalte des Standarddateisystems für einen neuen HDInsight-Cluster bereit. [Weitere Informationen](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/)|
|Azure Monitor|Microsoft.Insights|Ermöglicht das Schreiben von Überwachungsdaten in ein gesichertes Speicherkonto. [Weitere Informationen](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security)|
|Azure-Netzwerke|Microsoft.Network|Speichern und Analysieren von Protokollen des Netzwerkdatenverkehrs. [Weitere Informationen](/azure/network-watcher/network-watcher-packet-capture-overview)|
|Azure Site Recovery|Microsoft.SiteRecovery |Konfigurieren der Notfallwiederherstellung durch Aktivieren der Replikation für virtuelle Azure IaaS-Computer. Erforderlich, wenn Sie ein Cachespeicherkonto oder ein Quell- oder Zielspeicherkonto mit aktivierter Firewall verwenden.  [Weitere Informationen](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication)|
|Azure SQL Data Warehouse|Microsoft.Sql|Ermöglicht das Importieren und Exportieren von Szenarien aus bestimmten SQL-Datenbankinstanzen mithilfe von PolyBase. [Weitere Informationen](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)|

### <a name="storage-analytics-data-access"></a>Zugriff auf Storage Analytics-Daten

In manchen Fällen ist der Lesezugriff auf Diagnoseprotokolle und -metriken von außerhalb des Netzwerks erforderlich. Sie können Netzwerkregelausnahmen gewähren, um den Lesezugriff auf Protokolldateien und/oder Metriktabellen des Speicherkontos zuzulassen. [Weitere Informationen zum Arbeiten mit Storage Analytics](/azure/storage/storage-analytics)

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

Unter [Dienstendpunkte](/azure/virtual-network/virtual-network-service-endpoints-overview) erhalten Sie weitere Informationen zu Dienstendpunkten in Azure-Netzwerken.

Im [Azure Storage-Sicherheitsleitfaden](storage-security-guide.md) erhalten Sie weitere Informationen zur Sicherheit von Azure Storage.
