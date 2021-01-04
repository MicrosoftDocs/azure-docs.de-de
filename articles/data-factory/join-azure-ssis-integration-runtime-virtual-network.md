---
title: Beitreten einer Azure-SSIS-Integrationslaufzeit zu einem virtuellen Netzwerk
description: Erfahren Sie, wie Sie eine Azure-SSIS Integration Runtime mit einem Azure Virtual Network verknüpfen.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/02/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: e73126cfc54294a7b9d54ff62c406d5e686ac470
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95982710"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Beitreten einer Azure-SSIS-Integrationslaufzeit zu einem virtuellen Netzwerk

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bei Verwendung von SQL Server Integration Services (SSIS) in Azure Data Factory sollten Sie in folgenden Szenarien die Azure-SSIS Integration Runtime (IR) mit einem Azure Virtual Network verknüpfen:

- Sie möchten über SSIS-Pakete, die in der Azure-SSIS IR ausgeführt werden, eine Verbindung mit lokalen Datenspeichern herstellen, ohne eine selbstgehostete IR als Proxy zu konfigurieren oder zu verwalten. 

- Sie möchten die SSIS-Katalogdatenbank (SSISDB) in einer Azure SQL-Datenbank mit IP-Firewallregeln/VNET-Dienstendpunkten oder einer SQL Managed Instance mit einem privatem Endpunkt hosten. 

- Sie möchten über SSIS-Pakete, die in der Azure-SSIS IR ausgeführt werden, eine Verbindung zu Azure-Ressourcen herstellen, die mit VNET-Dienstendpunkten konfiguriert sind.

- Sie möchten über SSIS-Pakete, die in der Azure-SSIS IR ausgeführt werden, eine Verbindung zu Datenquellen/Ressourcen herstellen, die mit IP-Firewallregeln konfiguriert sind.

Mit Data Factory können Sie die Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen, das über das klassische Bereitstellungsmodell oder das Azure Resource Manager-Bereitstellungsmodell erstellt wurde.

> [!IMPORTANT]
> Das klassische virtuelle Netzwerk wird gerade als veraltet eingestuft. Daher sollten Sie stattdessen das virtuelle Azure Resource Manager-Netzwerk verwenden.  Wenn Sie das klassische virtuelle Netzwerk bereits verwenden, wechseln Sie so bald wie möglich zum virtuellen Azure Resource Manager-Netzwerk.

Im Tutorial zum [Konfigurieren einer Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) für die Verknüpfung mit einem virtuellen Netzwerk](tutorial-deploy-ssis-virtual-network.md) werden die grundlegenden Schritte mit dem Azure-Portal beschrieben. Dieser Artikel baut auf dem Tutorial auf, und es werden alle optionalen Aufgaben beschrieben:

- Sie verwenden ein virtuelles Netzwerk (klassisch).
- Sie verwenden Ihre eigenen öffentlichen IP-Adressen für die Azure-SSIS IR.
- Sie verwenden einen eigenen DNS-Server (Domain Name System).
- Sie verwenden eine Netzwerksicherheitsgruppe (NSG) im Subnetz.
- Sie verwenden Azure ExpressRoute oder eine benutzerdefinierte Route (User-Defined Route, UDR).
- Sie verwenden eine benutzerdefinierte Azure-SSIS IR.
- Sie verwenden die Azure PowerShell-Bereitstellung.

## <a name="access-to-on-premises-data-stores"></a>Zugriff auf lokale Datenspeicher

Wenn SSIS-Pakete auf lokale Datenspeicher zugreifen, können Sie die Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen, das mit dem lokalen Netzwerk verbunden ist. Sie können auch eine selbstgehostete IR als Proxy für die Azure-SSIS IR konfigurieren und verwalten. Weitere Informationen finden Sie unter [Konfigurieren einer selbstgehosteten IR als Proxy für Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md). 

Wenn Sie die Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen, sind einige wichtige Punkte zu beachten: 

- Wenn mit Ihrem lokalen Netzwerk kein virtuelles Netzwerk verbunden ist, erstellen Sie zunächst ein [virtuelles Azure Resource Manager-Netzwerk](../virtual-network/quick-create-portal.md#create-a-virtual-network), mit dem die Azure-SSIS IR verknüpft werden kann. Konfigurieren Sie dann eine Site-to-Site-[VPN-Gatewayverbindung](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) oder eine [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md)-Verbindung von diesem virtuellen Netzwerk zu Ihrem lokalen Netzwerk. 

- Wenn bereits ein virtuelles Azure Resource Manager-Netzwerk am selben Standort wie die Azure-SSIS IR mit Ihrem lokalen Netzwerk verbunden ist, können Sie die IR mit diesem virtuellen Netzwerk verknüpfen. 

- Wenn bereits ein klassisches virtuelles Netzwerk an einem anderen Standort als die Azure-SSIS IR mit Ihrem lokalen Netzwerk verbunden ist, können Sie ein [virtuelles Azure Resource Manager-Netzwerk](../virtual-network/quick-create-portal.md#create-a-virtual-network) erstellen, mit dem die Azure-SSIS IR verknüpft werden kann. Konfigurieren Sie dann eine [Verbindung zwischen dem klassischen virtuellen Netzwerk und dem virtuellen Azure Resource Manager-Netzwerk](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md). 
 
- Wenn bereits ein virtuelles Azure Resource Manager-Netzwerk an einem anderen Standort als die Azure-SSIS IR mit Ihrem lokalen Netzwerk verbunden ist, können Sie zuerst ein [virtuelles Azure Resource Manager-Netzwerk](../virtual-network/quick-create-portal.md#create-a-virtual-network) erstellen, mit dem die Azure-SSIS IR verknüpft werden kann. Konfigurieren Sie dann eine Verbindung zwischen den virtuellen Azure Resource Manager-Netzwerken. 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>Hosten des SSIS-Katalogs in der SQL-Datenbank

Wenn Sie Ihren SSIS-Katalog in einer Azure SQL-Datenbank mit VNET-Dienstendpunkten hosten, müssen Sie Ihre Azure-SSIS IR mit demselben virtuellen Netzwerk und Subnetz verknüpfen.

Wenn Sie Ihren SSIS-Katalog in einer SQL Managed Instance mit einem privaten Endpunkt hosten, müssen Sie Ihre Azure-SSIS IR mit demselben virtuellen Netzwerk, aber in einem anderen Subnetz als die verwaltete Instanz verknüpfen. Wenn Sie die Azure-SSIS IR mit einem anderen virtuellen Netzwerk als die SQL Managed Instance verknüpfen möchten, empfiehlt sich das Peering virtueller Netzwerke (das auf dieselbe Region beschränkt ist) oder eine Verbindung zwischen den virtuellen Netzwerken. Weitere Informationen finden Sie unter [Herstellen einer Verbindung zwischen einer Anwendung und einer Azure SQL Managed Instance](../azure-sql/managed-instance/connect-application-instance.md).

## <a name="access-to-azure-services"></a>Zugriff auf Azure-Dienste

Wenn Ihre SSIS-Pakete auf Azure-Ressourcen zugreifen, die [VNET-Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) unterstützen, und Sie den Zugriff auf diese Ressourcen ausgehend von Azure-SSIS IR sichern möchten, können Sie Ihre Azure-SSIS IR zu einem für VNET-Dienstendpunkte konfigurierten Subnetz hinzufügen und dann eine VNET-Regel zu den relevanten Azure-Ressourcen hinzufügen, um Zugriff ausgehend vom gleichen Subnetz zuzulassen.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>Zugriff auf Datenquellen, die durch eine IP-Firewallregel geschützt sind

Wenn Ihre SSIS-Pakete auf Datenspeicher/Ressourcen zugreifen, für die nur bestimmte statische öffentliche IP-Adressen zulässig sind, und Sie den Zugriff auf diese Ressourcen ausgehend von Azure-SSIS IR aus sichern möchten, können Sie Ihre eigenen [öffentlichen IP-Adressen](../virtual-network/virtual-network-public-ip-address.md) für Azure-SSIS IR verwenden, wenn Sie die IR mit einem virtuellen Netzwerk verknüpfen, und dann den relevanten Ressourcen eine IP-Firewallregel hinzufügen, um Zugriff ausgehend von diesen IP-Adressen zuzulassen.

In allen Fällen kann das virtuelle Netzwerk nur über das Azure Resource Manager-Bereitstellungsmodell bereitgestellt werden.

Die folgenden Abschnitte enthalten hierzu weitere Informationen. 

## <a name="virtual-network-configuration"></a>Konfiguration von virtuellen Netzwerken

Achten Sie bei der Einrichtung des virtuellen Netzwerks auf die Einhaltung der folgenden Anforderungen: 

- Stellen Sie sicher, dass `Microsoft.Batch` ein registrierter Anbieter unter dem Abonnement Ihres Subnetzes des virtuellen Netzwerks ist, in dem die Azure-SSIS IR gehostet wird. Fügen Sie der Rolle „Mitwirkender für klassische virtuelle Computer“ für das virtuelle Netzwerk auch `MicrosoftAzureBatch` hinzu, wenn Sie ein klassisches virtuelles Netzwerk verwenden. 

- Stellen Sie sicher, dass Sie über die erforderlichen Berechtigungen verfügen. Weitere Informationen finden Sie unter [Einrichten von Berechtigungen](#perms).

- Wählen Sie das richtige Subnetz zum Hosten der Azure-SSIS Integration Runtime aus. Weitere Informationen finden Sie unter [Auswählen des Subnetzes](#subnet). 

- Wenn Sie Ihre eigenen öffentlichen IP-Adressen für die Azure-SSIS IR verwenden, lesen Sie [Auswählen der statischen öffentlichen IP-Adressen](#publicIP).

- Wenn Sie Ihren eigenen DNS (Domain Name System)-Server im virtuellen Netzwerk verwenden, finden Sie weitere Informationen unter [Einrichten des DNS-Servers](#dns_server). 

- Wenn Sie eine Netzwerksicherheitsgruppe (NSG) im Subnetz verwenden, finden Sie Weitere Informationen unter [Einrichten einer NSG](#nsg). 

- Wenn Sie Azure ExpressRoute oder eine benutzerdefinierte Route (User-Defined Route, UDR) verwenden, finden Sie weitere Informationen unter [Verwenden von Azure ExpressRoute oder einer UDR](#route). 

- Stellen Sie sicher, dass die Ressourcengruppe des virtuellen Netzwerks (oder die Ressourcengruppe der öffentlichen IP-Adressen, wenn Sie Ihre eigenen öffentlichen IP-Adressen verwenden) bestimmte Azure-Netzwerkressourcen erstellen und löschen kann. Weitere Informationen finden Sie unter [Einrichten der Ressourcengruppe](#resource-group). 

- Wenn Sie die Azure-SSIS IR anpassen, wie im Thema zur [benutzerdefinierten Einrichtung der Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md) beschrieben, werden beim internen Prozess zum Verwalten der Knoten private IP-Adressen aus einem vordefinierten Bereich von 172.16.0.0 bis 172.31.255.255 verwendet. Daher sollten Sie sicherstellen, dass die Bereiche privater IP-Adressen Ihrer virtuellen oder lokalen Netzwerke nicht mit diesem Bereich kollidieren.

Dieses Diagramm veranschaulicht die für die Azure-SSIS IR erforderlichen Verbindungen:

![Diagramm der für die Azure-SSIS IR erforderlichen Verbindungen](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="set-up-permissions"></a><a name="perms"></a> Einrichten von Berechtigungen

Der Benutzer, der die Azure-SSIS IR erstellt, muss über die folgenden Berechtigungen verfügen:

- Wenn Sie Ihre SSIS IR in ein virtuelles Azure Resource Manager-Netzwerk einbinden, haben Sie zwei Möglichkeiten:

  - Verwenden Sie die integrierte Rolle „Netzwerkmitwirkender“. Diese Rolle umfasst die Berechtigung _Microsoft.Network/\*_ , die jedoch einen deutlich größeren Umfang als erforderlich hat.

  - Erstellen Sie eine benutzerdefinierte Rolle, die nur die erforderliche Berechtigung _Microsoft.Network/virtualNetworks/\*/join/action_ aufweist. Wenn Sie beim Verknüpfen der IR mit einem virtuellen Azure Resource Manager-Netzwerk Ihre eigenen öffentlichen IP-Adressen für die Azure SSIS IR verwenden möchten, beziehen Sie auch die Berechtigung _Microsoft.Network/publicIPAddresses/*/join/action_ mit in die Rolle ein.

- Wenn Sie die SSIS IR mit einem klassischen virtuellen Netzwerk verknüpfen, wird empfohlen, die integrierte Rolle „Mitwirkender für klassische virtuelle Computer“ zu verwenden. Andernfalls müssen Sie eine benutzerdefinierte Rolle definieren, die die Berechtigung zum Einbinden in das virtuelle Netzwerk enthält.

### <a name="select-the-subnet"></a><a name="subnet"></a> Auswählen des Subnetzes

Hinweise zur Auswahl eines Subnetzes: 

- Sie sollten nicht das GatewaySubnet zum Bereitstellen einer Azure-SSIS IR auswählen, da es Gateways für virtuelle Netzwerke vorbehalten ist. 

- Stellen Sie sicher, dass das ausgewählte Subnetz ausreichend verfügbaren Adressraum für die Azure-SSIS IR aufweist. Sie benötigen mindestens doppelt so viele verfügbare IP-Adressen, wie IR-Knoten vorhanden sind. Einige IP-Adressen innerhalb jedes Subnetzes sind in Azure reserviert. Diese Adressen können nicht verwendet werden. Die erste und letzte IP-Adresse der Subnetze sind aus Gründen der Protokollkonformität reserviert. Darüber hinaus werden drei weitere Adressen für Azure-Dienste verwendet. Weitere Informationen finden Sie unter [Unterliegen die in den Subnetzen verwendeten IP-Adressen bestimmten Beschränkungen?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- Verwenden Sie kein Subnetz, das ausschließlich von anderen Azure-Diensten genutzt wird (z. B. SQL Managed Instance einer SQL-Datenbank, App Service usw.). 

### <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>Auswählen der statischen öffentlichen IP-Adressen

Wenn Sie Ihre eigenen statischen öffentlichen IP-Adressen für die Azure-SSIS IR verwenden möchten, während Sie sie mit einem virtuellen Netzwerk verknüpfen, müssen die Adressen die folgenden Anforderungen erfüllen:

- Es müssen genau zwei nicht verwendete Adressen bereitgestellt werden, die noch nicht mit anderen Azure-Ressourcen verknüpft sind. Die zusätzliche Adresse wird bei der regelmäßigen Aktualisierung Ihrer Azure-SSIS IR verwendet. Beachten Sie, dass eine einzige öffentliche IP-Adresse nicht von Ihren aktiven Azure-SSIS-IRs gemeinsam genutzt werden kann.

- Beide Adressen müssen statische Standardadressen sein. Weitere Details finden Sie unter [SKUs der öffentlichen IP-Adresse](../virtual-network/public-ip-addresses.md#sku).

- Beide Adressen müssen einen DNS-Namen aufweisen. Wenn Sie bei der Erstellung keinen DNS-Namen angegeben haben, können Sie dies im Azure-Portal erledigen.

![Azure-SSIS-Integrationslaufzeit](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- Die Adressen und das virtuelle Netzwerk müssen sich im selben Abonnement und in derselben Region befinden.

### <a name="set-up-the-dns-server"></a><a name="dns_server"></a> Einrichten des DNS-Servers 
Wenn Sie Ihren eigenen DNS-Server in einem virtuellen Netzwerk verwenden müssen, das mit Ihrer Azure-SSIS IR verknüpft ist, stellen Sie zum Auflösen Ihres privaten Hostnamens sicher, dass auch globale Azure-Hostnamen aufgelöst werden können (z. B. der Azure Storage-Blobname `<your storage account>.blob.core.windows.net`). 

Nachstehend finden Sie eine empfohlene Vorgehensweise: 

-   Konfigurieren Sie den benutzerdefinierten DNS für die Weiterleitung von Anforderungen an Azure DNS. Sie können nicht aufgelöste DNS-Einträge an die IP-Adresse der rekursiven Azure-Resolver (168.63.129.16) auf Ihrem eigenen DNS-Server weiterleiten. 

Weitere Informationen finden Sie unter [Namensauflösung mithilfe eines eigenen DNS-Servers](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

> [!NOTE]
> Verwenden Sie einen vollqualifizierten Domänennamen für Ihren privaten Hostnamen (verwenden Sie z. B. `<your_private_server>.contoso.com` anstelle von `<your_private_server>`). Alternativ können Sie ein benutzerdefiniertes Standardsetup auf Ihrer Azure-SSIS IR verwenden, um automatisch ein eigenes DNS-Suffix (z. B. `contoso.com`) an alle nicht qualifizierten einteiligen Domänennamen anzufügen und sie in vollqualifizierte Domänennamen umzuwandeln, bevor diese in DNS-Abfragen verwendet werden. Weitere Informationen finden Sie unter [Beispiele für benutzerdefinierte Standardsetups](./how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples). 

### <a name="set-up-an-nsg"></a><a name="nsg"></a> Einrichten einer NSG
Wenn Sie eine NSG für das Subnetz implementieren müssen, das von der Azure-SSIS IR verwendet wird, lassen Sie eingehenden und ausgehenden Datenverkehr über die folgenden Ports zu: 

-   **Eingehende Anforderung von Azure-SSIS IR**

| Direction | Transportprotokoll | `Source` | Quellportbereich | Destination | Destination port range | Kommentare |
|---|---|---|---|---|---|---|
| Eingehend | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (wenn Sie die IR mit einem virtuellen Resource Manager-Netzwerk verknüpfen) <br/><br/>10100, 20100, 30100 (wenn Sie die IR mit einem klassischen virtuellen Netzwerk verknüpfen)| Der Data Factory-Dienst nutzt diese Ports für die Kommunikation mit den Knoten der Azure-SSIS IR im virtuellen Netzwerk. <br/><br/> Unabhängig davon, ob Sie eine NSG auf Subnetzebene erstellen, konfiguriert Data Factory immer eine NSG auf der Ebene der Netzwerkschnittstellenkarten (NICs), die an die virtuellen Computer angefügt sind, auf denen die Azure-SSIS IR gehostet wird. Nur eingehenden Datenverkehr von Data Factory-IP-Adressen für die angegebenen Ports wird durch diese NSG auf NIC-Ebene zugelassen. Auch wenn Sie diese Ports für den Internetdatenverkehr auf Subnetzebene öffnen, wird Datenverkehr von anderen IP-Adressen als Data Factory-IP-Adressen auf NIC-Ebene blockiert. |
| Eingehend | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | (Optional) Diese Regel ist nur erforderlich, wenn Microsoft-Supporter Kunden zum Öffnen für erweiterte Problembehandlung auffordern. Direkt nach Abschluss der Problembehandlung kann sie wieder geschlossen werden. Das Diensttag **CorpNetSaw** gestattet nur sicheren Zugriff auf Arbeitsstationen im Microsoft-Unternehmensnetzwerk, um den Remotedesktop verwenden zu können. Dieses Diensttag kann im Portal nicht ausgewählt werden und steht nur über Azure PowerShell oder die Azure-Befehlszeilenschnittstelle (Azure CLI) zur Verfügung. <br/><br/> Auf NIC-Ebene NSG ist Port 3389 standardmäßig geöffnet, und wir ermöglichen es Ihnen, Port 3389 auf Subnetzebene-NSG zu steuern. In der Zwischenzeit hat Azure-SSIS IR Port 3389 ausgehend von der Windows-Firewallregel auf jedem IR-Knoten für Schutzzwecke standardmäßig nicht zugelassen. |
||||||||

-   **Ausgehende Anforderung von Azure-SSIS IR**

| Direction | Transportprotokoll | `Source` | Quellportbereich | Destination | Destination port range | Kommentare |
|---|---|---|---|---|---|---|
| Ausgehend | TCP | VirtualNetwork | * | AzureCloud | 443 | Die Knoten Ihrer Azure-SSIS IR im virtuellen Netzwerk verwenden diesen Port für den Zugriff auf Azure-Dienste wie Azure Storage und Azure Event Hubs. |
| Ausgehend | TCP | VirtualNetwork | * | Internet | 80 | (Optional) Die Knoten Ihrer Azure-SSIS IR im virtuellen Netzwerk verwenden diesen Port zum Herunterladen einer Zertifikatssperrliste aus dem Internet. Wenn Sie diesen Datenverkehr blockieren, kann es beim Starten der IR zu einer Leistungsherabstufung kommen und die Möglichkeit zum Überprüfen der Zertifikatssperrliste im Hinblick auf die Zertifikatverwendung verloren gehen. Wenn Sie das Ziel auf bestimmte FQDNs weiter eingrenzen möchten, finden Sie entsprechende Informationen im Abschnitt **Verwenden von Azure ExpressRoute oder UDR**.|
| Ausgehend | TCP | VirtualNetwork | * | Sql | 1433, 11000–11999 | (Optional) Diese Regel ist nur erforderlich, wenn die Knoten Ihres Azure-SSIS IR im virtuellen Netzwerk auf eine von Ihrem Server gehostete SSISDB zugreifen. Wenn die Verbindungsrichtlinie Ihres Servers anstatt auf **Redirect** auf **Proxy** festgelegt ist, wird nur Port 1433 benötigt. <br/><br/> Diese ausgehende Sicherheitsregel ist auf eine SSISDB nicht anwendbar, die von Ihrer SQL Managed Instance im virtuellen Netzwerk oder auf der Azure-Datenbank gehostet wird, die bzw. der mit einem privaten Endpunkt konfiguriert wurde. |
| Ausgehend | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000–11999 | (Optional) Diese Regel ist nur erforderlich, wenn die Knoten Ihres Azure-SSIS IR im virtuellen Netzwerk auf eine SSISDB zugreifen, die von Ihrer SQL Managed Instance im virtuellen Netzwerk oder auf der SQL-Datenbank gehostet wird, die mit einem privaten Endpunkt konfiguriert wurde. Wenn die Verbindungsrichtlinie Ihres Servers anstatt auf **Redirect** auf **Proxy** festgelegt ist, wird nur Port 1433 benötigt. |
| Ausgehend | TCP | VirtualNetwork | * | Storage | 445 | (Optional) Diese Regel ist nur erforderlich, wenn Sie das in Azure Files gespeicherte SSIS-Paket ausführen möchten. |
||||||||

### <a name="use-azure-expressroute-or-udr"></a><a name="route"></a> Verwenden von Azure ExpressRoute oder UDR
Wenn Sie den ausgehenden Datenverkehr von Azure-SSIS IR überprüfen möchten, können Sie den von Azure-SSIS IR initiierten Datenverkehr über [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)-Tunnelerzwingung (mit der Ankündigung einer BGP-Route, 0.0.0.0/0, an das virtuelle Netzwerk) an die lokale Firewallappliance oder das virtuelle Netzwerkgerät (Network Virtual Appliance, NVA) als Firewall oder [Azure Firewall](../firewall/index.yml) über [UDRs](../virtual-network/virtual-networks-udr-overview.md) weiterleiten. 

![NVA-Szenario für Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png)

Sie müssen die folgenden Schritte ausführen, damit das gesamte Szenario funktioniert.
   -   Eingehender Datenverkehr zwischen Azure Batch-Verwaltungsdiensten und der Azure-SSIS IR kann nicht über die Firewallappliance weitergeleitet werden.
   -   Die Firewallappliance muss ausgehenden Datenverkehr zulassen, der von Azure-SSIS IR benötigt wird.

Eingehender Datenverkehr zwischen Azure Batch-Verwaltungsdiensten und der Azure-SSIS IR kann nicht an die Firewallappliance weitergeleitet werden.´, weil andernfalls der Datenverkehr aufgrund eines asymmetrischen Routingproblems unterbrochen wird. Routen müssen für eingehenden Datenverkehr definiert werden, damit der Datenverkehr auf dieselbe Weise wie beim Eintreffen antworten kann. Sie können bestimmte UDRs definieren, um Datenverkehr zwischen Azure Batch-Verwaltungsdiensten und der Azure-SSIS IR mit **Internet** als Typ des nächsten Hops weiterzuleiten.

Wenn sich Ihre Azure-SSIS IR z. B. unter `UK South` befindet und Sie den ausgehenden Datenverkehr über Azure Firewall überprüfen möchten, rufen Sie zunächst eine IP-Bereichsliste des Diensttags`BatchNodeManagement.UKSouth` über den [Downloadlink für den IP-Bereich des Diensttags](https://www.microsoft.com/download/details.aspx?id=56519) oder über die [Diensttagermittlungs-API](../virtual-network/service-tags-overview.md#service-tags-on-premises) ab. Wenden Sie dann die folgenden UDRs von verknüpften IP-Bereichs Routen mit dem Typ des nächsten Hops als **Internet** zusammen mit der Route „0.0.0.0/0“ mit dem Typ **virtuelles Gerät** an.

![Azure Batch-UDR-Einstellungen](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Bei diesem Ansatz fallen zusätzliche Wartungskosten an. Damit die Azure-SSIS IR funktionsfähig bleibt, sollten Sie regelmäßig den IP-Adressbereich überprüfen und der UDR neue IP-Adressbereiche hinzufügen. Es wird empfohlen, den IP-Adressbereich monatlich zu überprüfen. Nachdem die neue IP-Adresse im Diensttag angezeigt wurde, dauert es nämlich einen weiteren Monat, bis sie wirksam wird. 

Wenn Sie das Setup von UDR-Regeln vereinfachen möchten, können Sie das folgende PowerShell-Skript ausführen, um UDR-Regeln für Azure Batch-Verwaltungsdienste hinzuzufügen:
```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your Route Table]"
$RouteTableResourceName = "[resource name of your Azure Route Table ]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start to add rule for your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add rule $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch service tags, please confirm that your Location is valid."
}
```

Damit die Firewallappliance ausgehenden Datenverkehr zulässt, müssen Sie für die unten aufgeführten Ports „ausgehend“ zulassen, die den Anforderungen in NSG-Ausgangsregeln entsprechen.
-   Port 443 mit dem Ziel Azure-Clouddienste.

    Wenn Sie Azure Firewall verwenden, können Sie die Netzwerkregel mit dem Diensttag „AzureCloud“ angeben. Bei einer Firewall der anderen Typen können Sie das Ziel entweder einfach als „alle“ für Port 443 zulassen oder die folgenden FQDNs basierend auf dem Typ Ihrer Azure-Umgebung zulassen:

    | Azure-Umgebung | Endpunkte                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure – Öffentlich      | <ul><li><b>Azure Data Factory (Verwaltung)</b><ul><li>\*.frontend.clouddatahub.net</li></ul></li><li><b>Azure Storage (Verwaltung)</b><ul><li>\*.blob.core.windows.net</li><li>\*.table.core.windows.net</li></ul></li><li><b>Azure Container Registry (Benutzerdefiniertes Setup)</b><ul><li>\*.azurecr.io</li></ul></li><li><b>Event Hub (Protokollierung)</b><ul><li>\*.servicebus.windows.net</li></ul></li><li><b>Microsoft-Protokollierungsdienst (Interne Verwendung)</b><ul><li>gcs.prod.monitoring.core.windows.net</li><li>prod.warmpath.msftcloudes.com</li><li>azurewatsonanalysis-prod.core.windows.net</li></ul></li></ul> |
    | Azure Government  | <ul><li><b>Azure Data Factory (Verwaltung)</b><ul><li>\*.frontend.datamovement.azure.us</li></ul></li><li><b>Azure Storage (Verwaltung)</b><ul><li>\*.blob.core.usgovcloudapi.net</li><li>\*.table.core.usgovcloudapi.net</li></ul></li><li><b>Azure Container Registry (Benutzerdefiniertes Setup)</b><ul><li>\*.azurecr.us</li></ul></li><li><b>Event Hub (Protokollierung)</b><ul><li>\*.servicebus.usgovcloudapi.net</li></ul></li><li><b>Microsoft-Protokollierungsdienst (Interne Verwendung)</b><ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>azurewatsonanalysis.usgovcloudapp.net</li></ul></li></ul> |
    | Azure China 21Vianet     | <ul><li><b>Azure Data Factory (Verwaltung)</b><ul><li>\*.frontend.datamovement.azure.cn</li></ul></li><li><b>Azure Storage (Verwaltung)</b><ul><li>\*.blob.core.chinacloudapi.cn</li><li>\*.table.core.chinacloudapi.cn</li></ul></li><li><b>Azure Container Registry (Benutzerdefiniertes Setup)</b><ul><li>\*.azurecr.cn</li></ul></li><li><b>Event Hub (Protokollierung)</b><ul><li>\*.servicebus.chinacloudapi.cn</li></ul></li><li><b>Microsoft-Protokollierungsdienst (Interne Verwendung)</b><ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>azurewatsonanalysis.chinacloudapp.cn</li></ul></li></ul> |

    Wie bei den FQDNs von Azure Storage, Azure Container Registry und Event Hub können Sie wahlweise auch die folgenden Dienstendpunkte für Ihr virtuelles Netzwerk aktivieren, sodass der Netzwerkdatenverkehr zu diesen Endpunkten das Azure-Backbone-Netzwerk durchläuft, statt an Ihre Firewallappliance weitergeleitet zu werden:
    -  Microsoft.Storage
    -  Microsoft.ContainerRegistry
    -  Microsoft.EventHub


-   Port 80 mit dem Ziel CRL-Downloadwebsites.

    Sie sollten folgende FQDNs zulassen, die als CRL-Downloadwebsites (Certificate Revocation List, Zertifikatssperrliste) von Zertifikaten für Azure-SSIS IR-Verwaltungszwecke verwendet werden:
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    Wenn Sie Zertifikate mit einer anderen CRL verwenden, sollten Sie sie ebenfalls einbeziehen. Weitere Informationen zur [Zertifikatssperrliste](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx) finden Sie hier.

    Wenn Sie diesen Datenverkehr nicht zulassen, kann beim Starten der Azure-SSIS IR eine Leistungsminderung auftreten und es Ihnen nicht mehr möglich sein, die Zertifikatsperrliste auf die Zertifikatsverwendung zu überprüfen. Davon wird aus Sicherheitsgründen abgeraten.

-   Port 1433, 11000–11999 mit dem Ziel Azure SQL-Datenbank (nur erforderlich, wenn die Knoten Ihres Azure-SSIS IR im virtuellen Netzwerk auf eine von Ihrem Server gehostete SSISDB zugreifen).

    Wenn Sie Azure Firewall verwenden, können Sie eine Netzwerkregel mit dem Azure SQL-Diensttag angeben. Andernfalls könnten Sie das Ziel als spezifische Azure SQL-URL in der Firewallappliance zulassen.

-   Port 445 mit dem Ziel Azure Storage (nur erforderlich, wenn Sie das in Azure Files gespeicherte SSIS-Paket ausführen).

    Wenn Sie Azure Firewall verwenden, können Sie eine Netzwerkregel mit dem Speicherdiensttag angeben. Andernfalls könnten Sie das Ziel als spezifische Azure File Storage-URL in der Firewallappliance zulassen.

> [!NOTE]
> Wenn Sie bei Azure SQL und Azure Storage VNET-Dienstendpunkte in Ihrem Subnetz konfigurieren, wird der Datenverkehr zwischen Azure-SSIS IR und Azure SQL in derselben Region und Azure Storage in derselben Region oder einem Regionspaar direkt an das Microsoft Azure-Backbonenetzwerk (statt an Ihre Firewallappliance) weitergeleitet.

Wenn Sie den ausgehenden Datenverkehr von Azure-SSIS IR nicht überprüfen müssen, können Sie einfach die Route anwenden und so erzwingen, dass der gesamte Datenverkehr an den Typ des nächsten Hops, **Internet**, geleitet wird:

-   In einem Azure ExpressRoute-Szenario können Sie in dem Subnetz, in dem die Azure-SSIS IR gehostet wird, eine 0.0.0.0/0-Route anwenden, bei der der Typ des nächsten Hops **Internet** lautet. 
-   In einem NVA-Szenario können Sie die vorhandene 0.0.0.0/0-Route, die auf das die Azure-SSIS IR hostende Subnetz angewendet wird, vom Typ des nächsten Hops **virtuelle Appliance** in **Internet** ändern.

![Hinzufügen einer Route](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

> [!NOTE]
> Die Angabe einer Route mit dem Typ des nächsten Hobs **Internet** bedeutet, dass der gesamte Datenverkehr über das Internet gesendet wird. Solange die Zieladresse für einen der Azure-Dienste gilt, leitet Azure den Datenverkehr über das Azure-Backbonenetzwerk direkt an den Dienst weiter (und nicht ins Internet).

### <a name="set-up-the-resource-group"></a><a name="resource-group"></a> Einrichten der Ressourcengruppe

Die Azure SSIS-IR muss bestimmte Netzwerkressourcen unter der gleichen Ressourcengruppe erstellen wie das virtuelle Netzwerk. Diese Ressourcen umfassen Folgendes:
- Ein Azure-Lastenausgleich mit dem Namen *\<Guid>-azurebatch-cloudserviceloadbalancer*.
- Eine öffentliche Azure-IP-Adresse mit dem Namen *\<Guid>-azurebatch-cloudservicepublicip*.
- Eine Netzwerksicherheitsgruppe mit dem Namen *\<Guid>-azurebatch-cloudservicenetworksecuritygroup*. 

> [!NOTE]
> Sie können jetzt Ihre eigenen statischen öffentlichen IP-Adressen für die Azure-SSIS IR verwenden. In diesem Szenario erstellen wir nur den Azure-Lastenausgleich und die Netzwerksicherheitsgruppe in derselben Ressourcengruppe wie Ihre statischen öffentlichen IP-Adressen und nicht im virtuellen Netzwerk.

Die betreffenden Ressourcen werden beim Start der Azure-SSIS IR erstellt. Sie werden gelöscht, wenn die Azure-SSIS IR beendet wird. Wenn Sie Ihre eigenen statischen öffentlichen IP-Adressen für Azure-SSIS IR verwenden, werden diese beim Beenden der Azure-SSIS IR nicht gelöscht. Damit Ihre Azure-SSIS IR ordnungsgemäß beendet werden kann, sollten Sie diese Netzwerkressourcen nicht in anderen Ressourcen wiederverwenden.

Stellen Sie sicher, dass es keine Ressourcensperre für die Ressourcengruppe oder das Abonnement gibt, zu der bzw. dem das virtuelle Netzwerk gehört bzw. die öffentlichen IP-Adressen gehören. Wenn Sie eine Schreibschutzsperre oder eine Löschsperre konfigurieren, kann beim Starten und Beenden Ihrer Azure-SSIS IR ein Fehler auftreten oder die IR nicht mehr reagieren.

Stellen Sie sicher, dass Sie keine Azure Policy-Zuweisung haben, die verhindert, dass die folgenden Ressourcen unter der Ressourcengruppe oder dem Abonnement erstellt werden, zu der bzw. dem das virtuelle Netzwerk gehört bzw. die öffentlichen IP-Adressen gehören: 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAddresses 

Stellen Sie sicher, dass das Ressourcenkontingent Ihres Abonnements für die vorstehenden drei Netzwerkressourcen ausreicht. Insbesondere müssen Sie bei jeder im virtuellen Netzwerk erstellten Azure-SSIS IR zwei kostenlose Kontingente für jede der vorstehenden drei Netzwerkressourcen reservieren. Das eine zusätzliche Kontingent wird bei der regelmäßigen Aktualisierung Ihrer Azure-SSIS IR verwendet.

### <a name="faq"></a><a name="faq"></a> Häufig gestellte Fragen (FAQ)

- Wie kann ich die öffentliche IP-Adresse schützen, die in meiner Azure-SSIS IR für eingehende Verbindungen verfügbar gemacht wird? Ist es möglich, die öffentliche IP-Adresse zu entfernen?
 
  Derzeit wird automatisch eine öffentliche IP-Adresse erstellt, wenn Ihre Azure-SSIS IR mit einem virtuellen Netzwerk verknüpft wird. Wir verfügen über eine NSG auf NIC-Ebene, die ausschließlich eingehende Verbindungen von Azure Batch-Verwaltungsdiensten mit Ihrer Azure-SSIS IR zulässt. Sie können auch eine NSG auf Subnetzebene festlegen, um eingehende Verbindungen zu schützen.

  Wenn eine öffentliche IP-Adresse nicht verfügbar gemacht werden soll, können Sie eine [selbstgehostete IR als Proxy für die Azure-SSIS IR konfigurieren](./self-hosted-integration-runtime-proxy-ssis.md) anstatt die Azure-SSIS IR zu einem virtuellen Netzwerk hinzuzufügen, wenn dies auf Ihr Szenario zutrifft.
 
- Kann ich die öffentliche IP-Adresse meiner Azure-SSIS IR der Liste zugelassener IP-Adressen hinzufügen, die die Firewall für meine Datenquellen nutzt?

  Sie können jetzt Ihre eigenen statischen öffentlichen IP-Adressen für die Azure-SSIS IR verwenden. In diesem Fall können Sie Ihre IP-Adressen zur Liste zugelassener IP-Adressen der Firewall für Ihre Datenquellen hinzufügen. Sie können auch andere, unten aufgeführte Optionen in Erwägung ziehen, um den Datenzugriff ausgehend von Ihrer Azure-SSIS IR abhängig vom jeweiligen Szenario zu sichern:

  - Wenn Sie eine lokale Datenquelle verwenden, können Sie den privaten IP-Adressbereich dieses Subnetzes der Liste zugelassener IP-Adressen für Ihre Datenquellen hinzufügen, nachdem Sie eine Verbindung zwischen dem virtuellen Netzwerk und Ihrem lokalen Netzwerk hergestellt und Ihre Azure-SSIS IR mit dem Subnetz des virtuellen Netzwerks verknüpft haben.
  - Wenn es sich bei der Datenquelle um einen Azure-Dienst handelt, der VNET-Dienstendpunkte unterstützt, können Sie in Ihrem virtuellen Netzwerk einen VNET-Dienstendpunkt konfigurieren und Ihre Azure-SSIS IR mit dem betreffenden Subnetz verknüpfen. Anschließend können Sie der Firewall für die Datenquelle eine VNET-Regel für dieses Subnetz hinzufügen.
  - Wenn Ihre Datenquelle kein Azure-Clouddienst ist, können Sie eine UDR verwenden, um von Ihrer Azure-SSIS IR ausgehenden Datenverkehr über eine statische öffentliche IP-Adresse an eine NVA/Azure Firewall zu routen. Anschließend können Sie die statische öffentliche IP-Adresse Ihrer NVA/Azure Firewall zur Liste zugelassener IP-Adressen der Firewall für Ihre Datenquelle hinzufügen.
  - Wenn keine der oben genannten Optionen Ihre Anforderungen erfüllt, können Sie die [Konfiguration einer selbstgehosteten IR als Proxy für Ihre Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md) in Erwägung ziehen. Anschließend können Sie die statische öffentliche IP-Adresse des Hostcomputers für die selbstgehostete IR zur Liste zugelassener IP-Adressen der Firewall für Ihre Datenquelle hinzufügen.

- Warum muss ich zwei statische öffentliche Adressen angeben, wenn ich meine eigenen Adressen für die Azure-SSIS IR verwenden möchte?

  Die Azure-SSIS IR wird regelmäßig automatisch aktualisiert. Beim Upgrade werden neue Knoten erstellt und alte werden gelöscht. Um Ausfallzeiten zu vermeiden, werden die alten Knoten jedoch erst dann gelöscht, wenn die neuen einsatzbereit sind. Daher kann Ihre erste statische öffentliche IP-Adresse, die von den alten Knoten verwendet wird, nicht sofort freigegeben werden, sodass eine zweite statische öffentliche IP-Adresse zum Erstellen der neuen Knoten benötigt wird.

- Ich verwende meine eigenen statischen öffentlichen IP-Adressen für die Azure-SSIS IR. Warum kann die IR immer noch nicht auf meine Datenquellen zugreifen?

  - Vergewissern Sie sich, dass die beiden statischen öffentlichen IP-Adressen zur Liste zugelassener IP-Adressen der Firewall für Ihre Datenquellen hinzugefügt wurden. Bei jedem Upgrade der Azure-SSIS IR erfolgt ein Wechsel der statischen öffentlichen IP-Adresse zwischen den beiden eigenen Adressen. Wenn Sie nur eine Adresse zur Liste zugelassener IP-Adressen hinzufügen, ist nach dem Upgrade kein Datenzugriff für Ihre Azure-SSIS IR mehr möglich.
  - Wenn es sich bei Ihrer Datenquelle um einen Azure-Dienst handelt, überprüfen Sie, ob sie mit VNET-Dienstendpunkten konfiguriert wurde. Wenn dies der Fall ist, werden für den Datenverkehr von der Azure-SSIS IR zur Datenquelle die privaten IP-Adressen verwendet, die von Azure-Diensten verwaltet werden, sodass das Hinzufügen Ihrer eigenen statischen öffentlichen IP-Adressen zur Liste zugelassener IP-Adressen der Firewall für Ihre Datenquelle unwirksam ist.

## <a name="azure-portal-data-factory-ui"></a>Azure-Portal (Data Factory-Benutzeroberfläche)

In diesem Abschnitt erfahren Sie, wie Sie mithilfe des Microsoft Azure-Portals und der Data Factory-Benutzeroberfläche eine vorhandene Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen (klassisch oder Azure Resource Manager). 

Bevor Sie die Azure-SSIS IR mit dem virtuellen Netzwerk verknüpfen, müssen Sie das virtuelle Netzwerk ordnungsgemäß konfigurieren. Führen Sie die Schritte in dem Abschnitt aus, der dem Typ Ihres virtuellen Netzwerks entspricht (klassisch oder Azure Resource Manager). Befolgen Sie dann die Schritte im dritten Abschnitt, um die Azure-SSIS IR mit dem virtuellen Netzwerk zu verknüpfen. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Konfigurieren eines virtuellen Azure Resource Manager-Netzwerks

Konfigurieren Sie ein virtuelles Azure Resource Manager-Netzwerk mithilfe des Portals, bevor Sie eine Azure-SSIS IR mit dem Netzwerk verknüpfen.

1. Starten Sie Microsoft Edge oder Google Chrome. Derzeit wird die Data Factory-Benutzeroberfläche nur in diesen Webbrowsern unterstützt. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

1. Wählen Sie **Weitere Dienste**. Filtern Sie nach **Virtuelle Netzwerke**, und wählen Sie die Option aus. 

1. Filtern Sie nach Ihrem virtuellen Netzwerk, und wählen Sie es in der Liste aus. 

1. Wählen Sie auf der Seite **Virtuelles Netzwerk** die Option **Eigenschaften** aus. 

1. Wählen Sie für die **RESSOURCEN-ID** die Schaltfläche „Kopieren“ aus, um die Ressourcen-ID für das virtuelle Netzwerk in die Zwischenablage zu kopieren. Speichern Sie die ID aus der Zwischenablage in OneNote oder in einer Datei. 

1. Wählen Sie im linken Menü **Subnetze** aus. Stellen Sie sicher, dass die Anzahl der verfügbaren Adressen größer ist als die Anzahl der Knoten in Ihrer Azure-SSIS IR. 

1. Vergewissern Sie sich, dass dieser Azure Batch-Anbieter in dem Azure-Abonnement, das über das virtuelle Netzwerk verfügt, registriert ist. Alternativ können Sie auch den Azure Batch-Anbieter registrieren. Wenn in Ihrem Abonnement bereits ein Azure Batch-Konto enthalten ist, ist Ihr Abonnement für Azure Batch registriert. (Wenn Sie die Azure-SSIS Integration Runtime im Data Factory-Portal erstellen, wird der Azure Batch-Anbieter automatisch für Sie erstellt.) 

   1. Wählen Sie im Azure-Portal im linken Menü **Abonnements** aus. 

   1. Wählen Sie Ihr Abonnement aus. 

   1. Wählen Sie auf der linken Seite **Ressourcenanbieter** aus, und bestätigen Sie, dass **Microsoft.Batch** ein registrierter Anbieter ist. 

   ![Bestätigung des Status „Registriert“](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Wenn **Microsoft.Batch** nicht in der Liste angezeigt wird, erstellen Sie zum Registrieren [ein leeres Azure Batch-Konto](../batch/batch-account-create-portal.md) in Ihrem Abonnement. Sie können es später wieder löschen. 

### <a name="configure-a-classic-virtual-network"></a>Konfigurieren eines klassischen virtuellen Netzwerks

Konfigurieren Sie ein klassisches virtuelles Netzwerk mithilfe des Portals, bevor Sie eine Azure-SSIS IR mit dem Netzwerk verknüpfen. 

1. Starten Sie Microsoft Edge oder Google Chrome. Derzeit wird die Data Factory-Benutzeroberfläche nur in diesen Webbrowsern unterstützt. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

1. Wählen Sie **Weitere Dienste**. Filtern Sie nach **Virtuelle Netzwerke (klassisch)** , und wählen Sie die Option aus. 

1. Filtern Sie nach Ihrem virtuellen Netzwerk, und wählen Sie es in der Liste aus. 

1. Wählen Sie auf der Seite **Virtuelles Netzwerk (klassisch)** die Option **Eigenschaften** aus. 

   ![Ressourcen-ID des klassischen virtuellen Netzwerks](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Wählen Sie für **RESSOURCEN-ID** die Schaltfläche „Kopieren“ aus, um die Ressourcen-ID für das klassische Netzwerk in die Zwischenablage zu kopieren. Speichern Sie die ID aus der Zwischenablage in OneNote oder in einer Datei. 

1. Wählen Sie im linken Menü **Subnetze** aus. Stellen Sie sicher, dass die Anzahl der verfügbaren Adressen größer ist als die Anzahl der Knoten in Ihrer Azure-SSIS IR. 

   ![Anzahl der verfügbaren Adressen im virtuellen Netzwerk](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Verknüpfen Sie **MicrosoftAzureBatch** mit der Rolle **Mitwirkender für klassische virtuelle Computer** für das virtuelle Netzwerk. 

   1. Wählen Sie im linken Menü **Zugriffssteuerung (IAM)** aus, und wählen Sie die Registerkarte **Rollenzuweisungen** aus. 

       ![Schaltflächen „Zugriffssteuerung“ und „Hinzufügen“](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. Wählen Sie **Rollenzuweisung hinzufügen** aus.

   1. Wählen Sie auf der Seite **Rollenzuweisung hinzufügen** für **Rolle** die Option **Mitwirkender für klassische virtuelle Computer** aus. Fügen Sie in das Feld **Auswählen** die Zeichenfolge **ddbf3205-c6bd-46ae-8127-60eb93363864** ein, und wählen Sie dann in der Liste der Suchergebnisse **Microsoft Azure Batch** aus. 

       ![Suchergebnisse auf der Seite „Rollenzuweisung hinzufügen“](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. Wählen Sie **Speichern** aus, um die Einstellungen zu speichern und die Seite zu schließen. 

       ![Speichern der Zugriffseinstellungen](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. Vergewissern Sie sich, dass **Microsoft Azure Batch** in der Liste der Mitwirkenden angezeigt wird. 

       ![Bestätigen des Azure Batch-Zugriffs](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Vergewissern Sie sich, dass dieser Azure Batch-Anbieter in dem Azure-Abonnement, das über das virtuelle Netzwerk verfügt, registriert ist. Alternativ können Sie auch den Azure Batch-Anbieter registrieren. Wenn in Ihrem Abonnement bereits ein Azure Batch-Konto enthalten ist, ist Ihr Abonnement für Azure Batch registriert. (Wenn Sie die Azure-SSIS Integration Runtime im Data Factory-Portal erstellen, wird der Azure Batch-Anbieter automatisch für Sie erstellt.) 

   1. Wählen Sie im Azure-Portal im linken Menü **Abonnements** aus. 

   1. Wählen Sie Ihr Abonnement aus. 

   1. Wählen Sie auf der linken Seite **Ressourcenanbieter** aus, und bestätigen Sie, dass **Microsoft.Batch** ein registrierter Anbieter ist. 

   ![Bestätigung des Status „Registriert“](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Wenn **Microsoft.Batch** nicht in der Liste angezeigt wird, erstellen Sie zum Registrieren [ein leeres Azure Batch-Konto](../batch/batch-account-create-portal.md) in Ihrem Abonnement. Sie können es später wieder löschen. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Verknüpfen der Azure SSIS-IR mit einem virtuellen Netzwerk

Nachdem Sie Ihr virtuelles Azure Resource Manager-Netzwerk oder klassisches virtuelles Netzwerk konfiguriert haben, können Sie die Azure-SSIS IR mit dem virtuellen Netzwerk verknüpfen:

1. Starten Sie Microsoft Edge oder Google Chrome. Derzeit wird die Data Factory-Benutzeroberfläche nur in diesen Webbrowsern unterstützt. 

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Menü **Data Factorys** aus. Wenn **Data Factorys** nicht im Menü angezeigt wird, wählen Sie **Weitere Dienste** und dann im Abschnitt **INTELLIGENCE + ANALYSE** die Option **Data Factorys** aus. 

   ![Liste von Data Factorys](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Wählen Sie Ihre Data Factory mit der Azure-SSIS IR in der Liste aus. Die Startseite für Ihre Data Factory wird angezeigt. Wählen Sie die Kachel **Erstellen und überwachen** aus. Die Data Factory-Benutzeroberfläche wird auf einer separaten Registerkarte angezeigt. 

   ![Data Factory-Startseite](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Wechseln Sie in der Data Factory-Benutzeroberfläche zur Registerkarte **Bearbeiten**, klicken Sie auf **Verbindungen**, und wechseln Sie zur Registerkarte **Integration Runtimes**. 

   ![Registerkarte „Integration Runtimes“](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Wenn Ihre Azure-SSIS IR ausgeführt wird, wählen Sie in der Liste **Integration Runtimes** in der Spalte **Aktionen** für die Azure-SSIS IR die Schaltfläche **Beenden** aus. Sie können Ihre Azure-SSIS IR erst bearbeiten, wenn sie beendet wurde. 

   ![Beenden der IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Wählen Sie in der Liste **Integration Runtimes** in der Spalte **Aktionen** für die Azure-SSIS IR die Schaltfläche **Bearbeiten** aus. 

   ![Bearbeiten der Integration Runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Navigieren Sie im Bereich für die Integration Runtime-Einrichtung durch die Abschnitte **Allgemeine Einstellungen** und **SQL-Einstellungen**, indem Sie die Schaltfläche **Weiter** auswählen. 

1. Gehen Sie im Abschnitt **Erweiterte Einstellungen** folgendermaßen vor: 

   1. Aktivieren Sie das Kontrollkästchen **VNET für die Einbindung Ihrer Azure-SSIS Integration Runtime-Instanz auswählen, Erstellung bestimmter Netzwerkressourcen für ADF ermöglichen und optional eigene statische öffentliche IP-Adressen verwenden**. 

   1. Wählen Sie unter **Abonnement** das Azure-Abonnement aus, das Ihr virtuelles Netzwerk enthält.

   1. Für **Standort** ist derselbe Standort wie für Ihre Integration Runtime ausgewählt.

   1. Wählen Sie für **Typ** den Typ des virtuellen Netzwerks aus: „Klassisch“ oder „Azure Resource Manager“. Sie sollten ein virtuelles Azure Resource Manager-Netzwerk auswählen, da das klassische virtuelle Netzwerk bald veraltet sein wird.

   1. Wählen Sie unter **VNET-Name** den Namen des virtuellen Netzwerks aus. Es sollte mit dem Netzwerk übereinstimmen, das für SQL-Datenbank mit VNET-Dienstendpunkten oder die verwaltete SQL-Instanz mit einem privaten Endpunkt zum Hosten der SSISDB verwendet wird. Oder er sollte mit dem Namen des Netzwerks übereinstimmen, das mit Ihrem lokalen Netzwerk verbunden ist. Andernfalls kann es sich um ein beliebiges virtuelles Netzwerk handeln, damit Ihre eigenen statischen öffentlichen IP-Adressen für Azure-SSIS IR verwendet werden.

   1. Wählen Sie unter **Subnetzname** den Namen des Subnetzes für Ihr virtuelles Netzwerk aus. Es sollte mit dem Subnetz übereinstimmen, das für SQL-Datenbank mit VNET-Dienstendpunkten zum Hosten der SSISDB verwendet wird. Oder es sollte ein anderes Subnetz sein als das, das für Ihre SQL Managed Instance mit einem privaten Endpunkt zum Hosten der SSISDB verwendet wird. Andernfalls kann es sich um ein beliebiges Subnetz handeln, damit Ihre eigenen statischen öffentlichen IP-Adressen für Azure-SSIS IR verwendet werden.

   1. Aktivieren Sie das Kontrollkästchen **Statische öffentliche IP-Adressen für die Azure-SSIS Integration Runtime einbringen**, um festzulegen, ob Sie Ihre eigenen statischen öffentlichen IP-Adressen für Azure-SSIS IR verwenden möchten, damit Sie sie in der Firewall für Ihre Datenquellen zulassen können.

      Führen Sie die unten angegebenen Schritte aus, falls Sie das Kontrollkästchen aktivieren.

      1. Wählen Sie für **Erste statische öffentliche IP-Adresse** die erste statische öffentliche IP-Adresse aus, die [den Anforderungen für Ihre Azure-SSIS IR entspricht](#publicIP). Klicken Sie auf den Link **Neue erstellen**, wenn Sie über keine statischen öffentlichen IP-Adressen verfügen, um diese im Azure-Portal zu erstellen. Klicken Sie dann hier auf die Schaltfläche „Aktualisieren“, um sie auszuwählen.
      
      1. Wählen Sie für **Zweite statische öffentliche IP-Adresse** die zweite statische öffentliche IP-Adresse aus, die [den Anforderungen für Ihre Azure-SSIS IR entspricht](#publicIP). Klicken Sie auf den Link **Neue erstellen**, wenn Sie über keine statischen öffentlichen IP-Adressen verfügen, um diese im Azure-Portal zu erstellen. Klicken Sie dann hier auf die Schaltfläche „Aktualisieren“, um sie auszuwählen.

   1. Wählen Sie **VNET-Überprüfung** aus. Wenn die Überprüfung erfolgreich war, wählen Sie **Weiter** aus. 

   ![Erweiterte Einstellungen mit einem virtuellen Netzwerk](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Überprüfen Sie im Abschnitt **Zusammenfassung** alle Einstellungen für Ihre Azure-SSIS IR. Wählen Sie dann **Aktualisieren** aus.

1. Starten Sie die Azure-SSIS IR, indem Sie in der Spalte **Aktionen** für die Azure-SSIS IR die Schaltfläche **Starten** auswählen. Es dauert etwa 20 bis 30 Minuten, um die Azure-SSIS IR zu starten, die mit einem virtuellen Netzwerk verknüpft wird. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="define-the-variables"></a>Definieren der Variablen

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use SQL Database with IP firewall rules/virtual network service endpoints or SQL Managed Instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for SQL Database with virtual network service endpoints, or a different subnet from the one used for SQL Managed Instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

### <a name="configure-a-virtual-network"></a>Konfigurieren eines virtuellen Netzwerks

Sie müssen das virtuelle Netzwerk konfigurieren, bevor Sie die Azure-SSIS IR damit verknüpfen können. Um für die Azure-SSIS IR, die mit dem Netzwerk verknüpft werden soll, automatisch Berechtigungen und Einstellungen für virtuelle Netzwerke zu konfigurieren, fügen Sie das folgende Skript hinzu:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Erstellen einer Azure SSIS-IR und Verknüpfen dieser mit einem virtuellen Netzwerk

Sie können eine Azure SSIS-IR erstellen und gleichzeitig mit einem virtuellen Netzwerk verknüpfen. Das vollständige Skript und Anweisungen finden Sie unter [Erstellen einer Azure-SSIS IR](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Verknüpfen einer Azure SSIS-Integration Runtime mit einem virtuellen Netzwerk

Im Artikel [Erstellen einer Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md) wird veranschaulicht, wie im selben Skript eine Azure-SSIS IR erstellt und mit einem virtuellen Netzwerk verknüpft wird. Wenn Sie bereits über eine Azure-SSIS IR verfügen, führen Sie die folgenden Schritte aus, um sie mit dem virtuellen Netzwerk zu verknüpfen: 
1. Beenden Sie die Azure SSIS IR. 
1. Konfigurieren Sie die Azure SSIS-IR so, dass Sie mit dem virtuellen Netzwerk verknüpft wird. 
1. Starten Sie die Azure SSIS IR. 

### <a name="stop-the-azure-ssis-ir"></a>Beenden der Azure SSIS IR

Sie müssen die Azure-SSIS IR beenden, bevor Sie sie mit einem virtuellen Netzwerk verknüpfen können. Dieser Befehl gibt alle Knoten frei und beendet die Abrechnung:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Konfigurieren der Einstellungen des virtuellen Netzwerks für das Verknüpfen der Azure SSIS-IR

Verwenden Sie das folgende Skript, um Einstellungen für das virtuelle Netzwerk zu konfigurieren, mit dem die Azure-SSIS IR verknüpft werden soll: 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Konfigurieren der Azure SSIS IR

Um Ihre Azure-SSIS IR mit einem virtuellen Netzwerk zu verknüpfen, führen Sie den `Set-AzDataFactoryV2IntegrationRuntime`-Befehl aus: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -VnetId $VnetId `
    -Subnet $SubnetName

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-azure-ssis-ir"></a>Starten der Azure SSIS IR

Führen Sie den folgenden Befehl aus, um die Azure-SSIS IR zu starten: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

Das Abschließen dieses Befehls dauert 20 bis 30 Minuten.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure-SSIS IR finden Sie in den folgenden Artikeln: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Dieser Artikel enthält allgemeine konzeptionelle Informationen zu IRs, einschließlich der Azure-SSIS IR. 
- [Tutorial: Bereitstellen von SSIS-Paketen in Azure](./tutorial-deploy-ssis-packages-azure.md). Dieses Tutorial enthält ausführliche Anweisungen zum Erstellen einer Azure-SSIS IR. Dabei wird Azure SQL-Datenbank zum Hosten des SSIS-Katalogs verwendet. 
- [Erstellen einer Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Dieser Artikel baut auf dem Tutorial auf. Sie erfahren, wie Sie Azure SQL-Datenbank mit VNET-Dienstendpunkten oder mit einer SQL Managed Instance in einem virtuellen Netzwerk verwenden, um den SSIS-Katalog zu hosten. Außerdem wird veranschaulicht, wie Sie die Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen. 
- [Überwachen einer Azure-SSIS-Integrationslaufzeit](monitor-integration-runtime.md#azure-ssis-integration-runtime): In diesem Artikel erfahren Sie, wie Sie Informationen zur Azure-SSIS IR abrufen. Darüber hinaus enthält er Statusbeschreibungen für die zurückgegebenen Informationen. 
- [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md): In diesem Artikel wird beschrieben, wie Sie die Azure-SSIS IR beenden, starten oder löschen. Außerdem wird gezeigt, wie Sie Ihre Azure SSIS-IR aufskalieren, indem Sie weitere Knoten hinzufügen.
