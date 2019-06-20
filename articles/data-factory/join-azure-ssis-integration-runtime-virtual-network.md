---
title: Verknüpfen einer Azure SSIS-Integration Runtime mit einem virtuellen Netzwerk | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Azure SSIS-Integration Runtime mit einem virtuellen Azure-Netzwerk verknüpfen.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/08/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 6978b83e66f58e468d9f98394904861c8a4d8bd0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66152763"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Beitreten einer Azure-SSIS-Integrationslaufzeit zu einem virtuellen Netzwerk
Verknüpfen Sie in folgenden Szenarien Ihre Azure SSIS-Integration Runtime (IR) mit einem virtuellen Azure-Netzwerk: 

- Sie möchten aus SSIS-Paketen, die in einer Azure-SSIS-Integrationslaufzeit ausgeführt werden, eine Verbindung mit lokalen Datenspeichern herstellen. 

- Sie hosten die SQL Server Integration Services-Katalogdatenbank (SSIS) in Azure SQL-Datenbank mit VNET-Dienstendpunkten/verwalteter Instanz. 

  Mit Azure Data Factory können Sie Ihre Azure SSIS-Integration Runtime mit einem virtuellen Netzwerk verknüpfen, das über das klassische Bereitstellungsmodell oder das Azure Resource Manager-Bereitstellungsmodell erstellt wurde. 

> [!IMPORTANT]
> Das klassische virtuelle Netzwerk wird demnächst als veraltet angesehen, daher sollten Sie stattdessen das virtuelle Azure Resource Manager-Netzwerk verwenden.  Wenn Sie das klassische virtuelle Netzwerk bereits verwenden, wechseln Sie so bald wie möglich zum virtuellen Azure Resource Manager-Netzwerk.

## <a name="access-to-on-premises-data-stores"></a>Zugriff auf lokale Datenspeicher
Wenn SSIS-Pakete nur auf Datenspeicher in öffentlichen Clouds zugreifen, müssen Sie die Azure SSIS-IR nicht mit einem virtuellen Netzwerk verknüpfen. Wenn SSIS-Pakete auf lokale Datenspeicher zugreifen, müssen Sie die Azure SSIS-IR mit einem virtuellen Netzwerk verknüpfen, das mit dem lokalen Netzwerk verbunden ist. 

Folgende wichtige Punkte sind zu beachten: 

- Wenn kein vorhandenes virtuelles Netzwerk mit Ihrem lokalen Netzwerk verbunden ist, erstellen Sie zunächst ein [virtuelles Azure Resource Manager-Netzwerk](../virtual-network/quick-create-portal.md#create-a-virtual-network) oder ein [klassisches virtuelles Netzwerk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md), in das Ihre Azure SSIS-Integration Runtime eingebunden werden kann. Konfigurieren Sie dann eine Site-to-Site-[VPN-Gateway-Verbindung](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) oder eine [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md)-Verbindung von diesem virtuellen Netzwerk zu Ihrem lokalen Netzwerk. 

- Wenn ein vorhandenes virtuelles Azure Resource Manager-Netzwerk oder ein klassisches virtuelles Netzwerk an demselben Standort wie Ihre Azure SSIS-IR mit Ihrem lokalen Netzwerk verbunden ist, können Sie die IR mit diesem virtuellen Netzwerk verknüpfen. 

- Wenn ein klassisches virtuelles Netzwerk an einem anderen Standort als Ihre Azure SSIS-IR mit Ihrem lokalen Netzwerk verbunden ist, können Sie zuerst ein [klassisches virtuelles Netzwerk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) erstellen, mit der dann die Azure SSIS-IR verknüpft werden kann. Konfigurieren Sie dann eine [Verbindung zwischen den klassischen virtuellen Netzwerken](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md). Alternativ können Sie auch ein [virtuelles Azure Resource Manager-Netzwerk](../virtual-network/quick-create-portal.md#create-a-virtual-network) für die Verknüpfung Ihrer Azure SSIS-Integration Runtime erstellen. Konfigurieren Sie dann eine [Verbindung zwischen dem klassischen virtuellen Netzwerk und dem virtuellen Azure Resource Manager-Netzwerk](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md). 
 
- Wenn ein virtuelles Azure Resource Manager-Netzwerk an einem anderen Standort als Ihre Azure SSIS-IR mit Ihrem lokalen Netzwerk verbunden ist, können Sie zuerst ein [virtuelles Azure Resource Manager-Netzwerk](../virtual-network/quick-create-portal.md##create-a-virtual-network) erstellen, in das dann die Azure SSIS-IR eingebunden werden kann. Konfigurieren Sie dann eine Verbindung zwischen den virtuellen Azure Resource Manager-Netzwerken. Alternativ können Sie auch ein [klassisches virtuelles Netzwerk](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) für die Verknüpfung Ihrer Azure SSIS-IR erstellen. Konfigurieren Sie dann eine [Verbindung zwischen dem klassischen virtuellen Netzwerk und dem virtuellen Azure Resource Manager-Netzwerk](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md). 

## <a name="host-the-ssis-catalog-database-in-azure-sql-database-with-virtual-network-service-endpointsmanaged-instance"></a>Hosten der SSIS-Katalogdatenbank in einer Azure SQL-Datenbank-Instanz mit VNET-Dienstendpunkten/verwalteter Instanz
Wenn der SSIS-Katalog in einer Azure SQL-Datenbank-Instanz mit VNET-Dienstendpunkten oder in einer verwalteten Instanz gehostet wird, können Sie Azure-SSIS IR mit folgenden Netzwerken verknüpfen: 

- Demselben virtuellen Netzwerk 
- Einem anderen virtuellen Netzwerk, das über eine Netzwerk-zu-Netzwerk-Verbindung mit dem Netzwerk verfügt, das für die verwaltete Instanz verwendet wird 

Wenn Sie Ihren SSIS-Katalog in Azure SQL-Datenbank mit VNET-Dienstendpunkten hosten, stellen Sie sicher, dass Sie Azure-SSIS IR mit demselben virtuellen Netzwerk und Subnetz verknüpfen.

Wenn Sie Ihre Azure-SSIS IR mit demselben virtuellen Netzwerk verknüpfen wie die verwaltete Instanz, stellen Sie sicher, dass sich die Azure-SSIS IR in einem anderen Subnetz als die verwaltete Instanz befindet. Wenn Sie die Azure-SSIS IR mit einem anderen virtuellen Netzwerk verknüpfen als die verwaltete Instanz, empfiehlt sich das Peering virtueller Netzwerke (begrenzt auf die gleiche Region) oder eine Verbindung zwischen den virtuellen Netzwerken. Weitere Informationen finden Sie unter [Herstellen einer Verbindung zwischen einer Anwendung und einer verwalteten Azure SQL-Datenbank-Instanz](../sql-database/sql-database-managed-instance-connect-app.md).

In allen Fällen kann das virtuelle Netzwerk nur über das Azure Resource Manager-Bereitstellungsmodell bereitgestellt werden.

Die folgenden Abschnitte enthalten hierzu weitere Informationen. 

## <a name="requirements-for-virtual-network-configuration"></a>Anforderungen an die Konfiguration des virtuellen Netzwerks
-   Stellen Sie sicher, dass `Microsoft.Batch` ein registrierter Anbieter unter dem Abonnement Ihres Subnetzes des virtuellen Netzwerks ist, in dem die Azure SSIS-IR gehostet wird. Fügen Sie der Rolle „Mitwirkender für klassische virtuelle Computer“ für das virtuelle Netzwerk auch `MicrosoftAzureBatch` hinzu, wenn Sie das klassische virtuelle Netzwerk verwenden. 

-   Stellen Sie sicher, dass Sie über die erforderlichen Berechtigungen verfügen. Weitere Informationen finden Sie unter [Erforderliche Berechtigungen](#perms).

-   Wählen Sie das richtige Subnetz zum Hosten der Azure-SSIS Integration Runtime aus. Weitere Informationen finden Sie unter [Auswählen des Subnetzes](#subnet). 

-   Wenn Sie Ihren eigenen DNS-Server (Domain Name Service) im virtuellen Netzwerk verwenden, helfen Ihnen die Informationen unter [Domain Name Service-Server](#dns_server) weiter. 

-   Falls Sie im Subnetz eine Netzwerksicherheitsgruppe (NSG) verwenden, helfen Ihnen die Informationen unter [Netzwerksicherheitsgruppe](#nsg) weiter. 

-   Wenn Sie Azure ExpressRoute verwenden oder eine benutzerdefinierte Route (User Defined Route, UDR) konfigurieren, helfen Ihnen die Informationen unter [Verwenden von Azure ExpressRoute oder einer benutzerdefinierten Route](#route) weiter. 

-   Stellen Sie sicher, dass für die Ressourcengruppe des virtuellen Netzwerks das Erstellen und Löschen bestimmter Ressourcen des Azure-Netzwerks möglich ist. Weitere Informationen finden Sie unter [Anforderungen an die Ressourcengruppe](#resource-group). 

Hier sehen Sie ein Diagramm mit den erforderlichen Verbindungen für Ihre Azure-SSIS IR:

![Azure-SSIS-Integrationslaufzeit](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a> Erforderliche Berechtigungen

Der Benutzer, der Azure-SSIS Integration Runtime erstellt, muss über die folgenden Berechtigungen verfügen:

- Wenn Sie Ihre SSIS IR in ein virtuelles Azure Resource Manager-Netzwerk einbinden, haben Sie zwei Möglichkeiten:

  - Verwenden Sie die integrierte Rolle *Netzwerkmitwirkender*. Diese Rolle umfasst die Berechtigung _Microsoft.Network/\*_ , die jedoch einen deutlich größeren Umfang als erforderlich hat.

  - Erstellen Sie eine benutzerdefinierte Rolle, die nur die erforderliche Berechtigung _Microsoft.Network/virtualNetworks/\*/join/action_ aufweist. 

- Wenn Sie die SSIS IR in ein klassisches virtuelles Netzwerk einbinden, wird empfohlen, die integrierte Rolle *Mitwirkender für klassische virtuelle Computer* zu verwenden. Andernfalls müssen Sie eine benutzerdefinierte Rolle definieren, die die Berechtigung zum Einbinden in das virtuelle Netzwerk enthält.

### <a name="subnet"></a> Auswählen des Subnetzes
-   Wählen Sie nicht „GatewaySubnet“ für die Bereitstellung einer Azure-SSIS-Integrationslaufzeit aus, da es für Gateways des virtuellen Netzwerks vorgesehen ist. 

-   Stellen Sie sicher, dass das ausgewählte Subnetz ausreichend verfügbaren Adressraum für die Verwendung durch die Azure-SSIS-Integrationslaufzeit aufweist. Belassen Sie mindestens die zweifache Anzahl der Integrationslaufzeitknoten in verfügbaren IP-Adressen. Einige IP-Adressen innerhalb jedes Subnetzes sind in Azure reserviert und können deshalb nicht genutzt werden. Die erste und letzte IP-Adresse der Subnetze sind aus Gründen der Protokollkonformität reserviert. Darüber hinaus sind drei weitere für Azure-Dienste verwendete IP-Adressen reserviert. Weitere Informationen finden Sie unter [Unterliegen die in den Subnetzen verwendeten IP-Adressen bestimmten Beschränkungen?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

-   Verwenden Sie kein Subnetz, das ausschließlich von anderen Azure-Diensten (z.B. verwaltete Azure SQL-Datenbank-Instanz, App Service) besetzt ist. 

### <a name="dns_server"></a> Domain Name Service-Server 
Wenn Sie Ihren eigenen DNS-Server (Domain Name Service) in einem virtuellen Netzwerk verwenden müssen, das mit Ihrer Azure-SSIS Integration Runtime verknüpft ist, sollten Sie sicherstellen, dass öffentliche Azure-Hostnamen aufgelöst werden können (z.B. ein Azure Storage-Blobname: `<your storage account>.blob.core.windows.net`). 

Die folgenden Schritte werden empfohlen: 

-   Konfigurieren Sie den benutzerdefinierten DNS für die Weiterleitung von Anforderungen an Azure DNS. Sie können nicht aufgelöste DNS-Einträge an die IP-Adresse der rekursiven Azure-Resolver (168.63.129.16) auf Ihrem eigenen DNS-Server weiterleiten. 

-   Richten Sie das benutzerdefinierte DNS als primäre Komponente und Azure DNS als sekundäre Komponente für das virtuelle Netzwerk ein. Registrieren Sie die IP-Adresse der rekursiven Azure-Resolver (168.63.129.16) als sekundären DNS-Server, falls Ihr eigener DNS-Server nicht verfügbar ist. 

Weitere Informationen finden Sie unter [Namensauflösung mithilfe eines eigenen DNS-Servers](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

### <a name="nsg"></a> Netzwerksicherheitsgruppe
Wenn Sie eine Netzwerksicherheitsgruppe (NSG) für das Subnetz implementieren müssen, das die Azure-SSIS Integration Runtime verwendet, lassen Sie eingehenden/ausgehenden Datenverkehr über die folgenden Ports zu: 

| Direction | Transportprotokoll | `Source` | Quellportbereich | Ziel | Zielportbereich | Kommentare |
|---|---|---|---|---|---|---|
| Eingehend | TCP | AzureCloud<br/>(oder im größeren Umfang wie z.B. im Internet) | * | VirtualNetwork | 29876, 29877 (wenn Sie die IR mit einem virtuellen Azure Resource Manager-Netzwerk verknüpfen) <br/><br/>10100, 20100, 30100 (wenn Sie die IR mit einem klassischen virtuellen Netzwerk verknüpfen)| Der Data Factory-Dienst nutzt diese Ports für die Kommunikation mit den Knoten Ihrer Azure SSIS-Integration Runtime im virtuellen Netzwerk. <br/><br/> Unabhängig davon, ob Sie eine NSG auf Subnetzebene erstellen, konfiguriert Data Factory immer eine NSG auf der Ebene der Netzwerkschnittstellenkarten (NICs), die den virtuellen Computern angefügt sind, auf denen die Azure-SSIS IR gehostet wird. Nur eingehenden Datenverkehr von Data Factory-IP-Adressen für die angegebenen Ports wird durch diese NSG auf NIC-Ebene zugelassen. Auch wenn Sie diese Ports für den Internetdatenverkehr auf Subnetzebene öffnen, wird Datenverkehr von anderen IP-Adressen als Data Factory-IP-Adressen auf NIC-Ebene blockiert. |
| Ausgehend | TCP | VirtualNetwork | * | AzureCloud<br/>(oder im größeren Umfang wie z.B. im Internet) | 443 | Die Knoten Ihrer Azure SSIS-Integration Runtime im virtuellen Netzwerk verwenden diesen Port für den Zugriff auf Azure-Dienste wie Azure Storage oder Azure Event Hubs. |
| Ausgehend | TCP | VirtualNetwork | * | Internet | 80 | Die Knoten Ihrer Azure-SSIS-Integration Runtime im virtuellen Netzwerk verwenden diesen Port zum Herunterladen einer Zertifikatsperrliste aus dem Internet. |
| Ausgehend | TCP | VirtualNetwork | * | Sql<br/>(oder im größeren Umfang wie z.B. im Internet) | 1433, 11000 - 11999, 14000 - 14999 | Die Knoten für Ihre Azure SSIS-Integration Runtime im virtuellen Netzwerk verwenden diese Ports für den Zugriff auf die SSISDB, die von Ihrer Azure SQL-Datenbank-Serverinstanz gehostet wird. Dieser Zweck gilt nicht für die SSISDB, die von der verwalteten Instanz gehostet wird. |
||||||||

### <a name="route"></a> Verwenden von Azure ExpressRoute oder einer benutzerdefinierten Route
Sie können eine [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)-Verbindung mit der Infrastruktur Ihres virtuellen Netzwerks verbinden und so Ihr lokales Netzwerk auf Azure ausdehnen. 

Eine gemeinsame Konfiguration muss Tunnelerzwingung verwenden (Ankündigen einer BGP-Route, 0.0.0.0/0 an das virtuelle Netzwerk), wodurch ausgehender Internetdatenverkehr gezwungen wird, aus dem virtuellen Netzwerk zur Überprüfung und Protokollierung an die lokale Netzwerk-Appliance zu fließen. Dieser Datenfluss unterbricht die Konnektivität zwischen der Azure SSIS-Integration Runtime im virtuellen Netzwerk mit abhängigen Azure Data Factory-Diensten. Die Lösung besteht darin, mindestens eine [benutzerdefinierte Route](../virtual-network/virtual-networks-udr-overview.md) (User-Defined Routes, UDRs) in dem Subnetz zu definieren, das die Azure-SSIS-Integrationslaufzeit enthält. Eine benutzerdefinierte Route definiert subnetzspezifische Routen, die anstelle der BGP-Route berücksichtigt werden. 

Sie können benutzerdefinierte Routen auch definieren, um für ausgehenden Internetdatenverkehr von dem Subnetz, in dem die Azure-SSIS Integration Runtime gehostet wird, den Wechseln in ein anderes Subnetz zu erzwingen, in dem ein virtuelles Netzwerkgerät als Firewall oder ein DMZ-Host zur Untersuchung und Protokollierung gehostet wird. 

Wenden Sie in beiden Fällen die Route 0.0.0.0/0, für die der Typ des nächsten Hops **Internet** lautet, auf das Subnetz an, in dem die Azure-SSIS Integration Runtime gehostet wird. Dies ist die Voraussetzung dafür, dass die Kommunikation zwischen dem Data Factory-Dienst und der Azure-SSIS Integration Runtime erfolgreich ist. 

![Hinzufügen einer Route](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

Wenn Sie sich Sorgen um den Verlust der Fähigkeit machen, ausgehenden Internetdatenverkehr von diesem Subnetz zu untersuchen, können Sie auch eine NSG-Regel im Subnetz hinzufügen, um ausgehende Ziele auf [IP-Adressen von Azure-Rechenzentren](https://www.microsoft.com/download/details.aspx?id=41653) zu beschränken. 

Ein Beispiel finden Sie in [diesem PowerShell-Skript](https://gallery.technet.microsoft.com/scriptcenter/Adds-Azure-Datacenter-IP-dbeebe0c). Sie müssen das Skript wöchentlich ausführen, um die IP-Adressenliste der Azure-Rechenzentren auf dem neuesten Stand zu halten. 

### <a name="resource-group"></a> Anforderungen an die Ressourcengruppe
-   Die Azure SSIS-IR muss bestimmte Netzwerkressourcen unter der gleichen Ressourcengruppe erstellen wie das virtuelle Netzwerk. Dazu gehören folgende Ressourcen:
    -   Ein Azure-Lastenausgleich mit dem Namen *\<GUID>-azurebatch-cloudserviceloadbalancer*.
    -   Eine öffentliche Azure-IP-Adresse mit dem Namen *\<GUID>-azurebatch-cloudservicepublicip*.
    -   Eine Netzwerksicherheitsgruppe mit dem Namen *\<GUID>-azurebatch-cloudservicenetworksecuritygroup*. 

-   Stellen Sie sicher, dass für die Ressourcengruppe oder das Abonnement, zu der bzw. dem das virtuelle Netzwerk gehört, keine Ressourcensperre besteht. Wenn Sie entweder eine Schreibschutzsperre oder eine Löschsperre konfigurieren, kann beim Starten und Beenden der Integration Runtime ein Fehler auftreten oder der Prozess nicht mehr reagieren. 

-   Stellen Sie sicher, dass Sie nicht über eine Azure-Richtlinie verfügen, die verhindert, dass die folgenden Ressourcen unter der Ressourcengruppe oder dem Abonnement erstellt werden, zu der bzw. dem das virtuelle Netzwerk gehört: 
    -   Microsoft.Network/LoadBalancers 
    -   Microsoft.Network/NetworkSecurityGroups 
    -   Microsoft.Network/PublicIPAddresses 

## <a name="azure-portal-data-factory-ui"></a>Azure-Portal (Data Factory-Benutzeroberfläche)
In diesem Abschnitt erfahren Sie, wie Sie mithilfe des Azure-Portals und der Data Factory-Benutzeroberfläche eine vorhandene Azure SSIS Runtime mit einem virtuellen Netzwerk (klassisch oder Azure Resource Manager) verknüpfen. Zunächst müssen Sie das virtuelle Netzwerk entsprechend konfigurieren, bevor Ihre Azure SSIS-IR mit dem virtuellen Netzwerk verknüpft werden kann. Führen Sie die in einem der nächsten beiden Abschnitte beschriebenen Schritte aus, abhängig davon, welchen Typ Ihr virtuelles Netzwerk aufweist (klassisch oder Azure Resource Manager). Fahren Sie dann mit dem dritten Abschnitt fort, um Ihre Azure SSIS-IR mit dem virtuellen Netzwerk zu verknüpfen. 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>Verwenden des Portals zum Konfigurieren eines virtuellen Azure Resource Manager-Netzwerks
Sie müssen ein virtuelles Netzwerk konfigurieren, bevor Sie eine Azure SSIS-IR mit ihm verknüpfen können. 

1. Starten Sie Microsoft Edge oder Google Chrome. Derzeit wird die Data Factory-Benutzeroberfläche nur in diesen Webbrowsern unterstützt. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

1. Wählen Sie **Weitere Dienste**. Filtern Sie nach **Virtuelle Netzwerke**, und wählen Sie die Option aus. 

1. Filtern Sie nach Ihrem virtuellen Netzwerk, und wählen Sie es in der Liste aus. 

1. Wählen Sie auf der Seite **Virtuelles Netzwerk** die Option **Eigenschaften** aus. 

1. Wählen Sie für die **RESSOURCEN-ID** die Schaltfläche „Kopieren“ aus, um die Ressourcen-ID für das virtuelle Netzwerk in die Zwischenablage zu kopieren. Speichern Sie die ID aus der Zwischenablage in OneNote oder in einer Datei. 

1. Wählen Sie im linken Menü **Subnetze** aus. Stellen Sie sicher, dass die Anzahl der **verfügbaren Adressen** größer ist als die Anzahl der Knoten in Ihrer Azure SSIS-Integration Runtime. 

1. Vergewissern Sie sich, dass dieser Azure Batch-Anbieter in dem Azure-Abonnement, das über das virtuelle Netzwerk verfügt, registriert ist. Alternativ können Sie auch den Azure Batch-Anbieter registrieren. Wenn in Ihrem Abonnement bereits ein Azure Batch-Konto enthalten ist, wird Ihr Abonnement für Azure Batch registriert. (Wenn Sie die Azure-SSIS Integration Runtime im Data Factory-Portal erstellen, wird der Azure Batch-Anbieter automatisch für Sie erstellt.) 

   a. Wählen Sie im Azure-Portal im linken Menü **Abonnements** aus. 

   b. Wählen Sie Ihr Abonnement aus. 

   c. Wählen Sie auf der linken Seite **Ressourcenanbieter** aus, und bestätigen Sie, dass **Microsoft.Batch** ein registrierter Anbieter ist. 

   ![Bestätigung des Status „Registriert“](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Wenn **Microsoft.Batch** nicht in der Liste angezeigt wird, erstellen Sie zum Registrieren [ein leeres Azure Batch-Konto](../batch/batch-account-create-portal.md) in Ihrem Abonnement. Sie können es später wieder löschen. 

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>Verwenden des Portals zum Konfigurieren eines klassischen virtuellen Netzwerks
Sie müssen ein virtuelles Netzwerk konfigurieren, bevor Sie eine Azure SSIS-IR mit ihm verknüpfen können. 

1. Starten Sie Microsoft Edge oder Google Chrome. Derzeit wird die Data Factory-Benutzeroberfläche nur in diesen Webbrowsern unterstützt. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

1. Wählen Sie **Weitere Dienste**. Filtern Sie nach **Virtuelle Netzwerke (klassisch)** , und wählen Sie die Option aus. 

1. Filtern Sie nach Ihrem virtuellen Netzwerk, und wählen Sie es in der Liste aus. 

1. Wählen Sie auf der Seite **Virtuelles Netzwerk (klassisch)** die Option **Eigenschaften** aus. 

   ![Ressourcen-ID des klassischen virtuellen Netzwerks](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Wählen Sie für **RESSOURCEN-ID** die Schaltfläche „Kopieren“ aus, um die Ressourcen-ID für das klassische Netzwerk in die Zwischenablage zu kopieren. Speichern Sie die ID aus der Zwischenablage in OneNote oder in einer Datei. 

1. Wählen Sie im linken Menü **Subnetze** aus. Stellen Sie sicher, dass die Anzahl der **verfügbaren Adressen** größer ist als die Anzahl der Knoten in Ihrer Azure SSIS-Integration Runtime. 

   ![Anzahl der verfügbaren Adressen im virtuellen Netzwerk](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Verknüpfen Sie **MicrosoftAzureBatch** mit der Rolle **Mitwirkender für klassische virtuelle Computer** für das virtuelle Netzwerk. 

    a. Klicken Sie im Menü links auf **Zugriffssteuerung (IAM)** , und wählen Sie dann die Registerkarte **Rollenzuweisung hinzufügen** aus. 

    ![Schaltflächen „Zugriffssteuerung“ und „Hinzufügen“](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Wählen Sie **Rollenzuweisung hinzufügen** aus.

    c. Wählen Sie auf der Seite **Rollenzuweisung hinzufügen** die Option **Mitwirkender für klassische virtuelle Computer** für **Rolle** aus. Fügen Sie **ddbf3205-c6bd-46ae-8127-60eb93363864** in das Textfeld **Auswählen** ein, und wählen Sie dann in der Liste der Suchergebnisse **Microsoft Azure Batch** aus. 

    ![Suchergebnisse auf der Seite „Rollenzuweisung hinzufügen“](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d. Wählen Sie **Speichern** aus, um die Einstellungen zu speichern und die Seite zu schließen. 

    ![Speichern der Zugriffseinstellungen](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    e. Vergewissern Sie sich, dass **Microsoft Azure Batch** in der Liste der Mitwirkenden angezeigt wird. 

    ![Bestätigen des Azure Batch-Zugriffs](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Vergewissern Sie sich, dass dieser Azure Batch-Anbieter in dem Azure-Abonnement, das über das virtuelle Netzwerk verfügt, registriert ist. Alternativ können Sie auch den Azure Batch-Anbieter registrieren. Wenn in Ihrem Abonnement bereits ein Azure Batch-Konto enthalten ist, wird Ihr Abonnement für Azure Batch registriert. (Wenn Sie die Azure-SSIS Integration Runtime im Data Factory-Portal erstellen, wird der Azure Batch-Anbieter automatisch für Sie erstellt.) 

   a. Wählen Sie im Azure-Portal im linken Menü **Abonnements** aus. 

   b. Wählen Sie Ihr Abonnement aus. 

   c. Wählen Sie auf der linken Seite **Ressourcenanbieter** aus, und bestätigen Sie, dass **Microsoft.Batch** ein registrierter Anbieter ist. 

   ![Bestätigung des Status „Registriert“](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Wenn **Microsoft.Batch** nicht in der Liste angezeigt wird, erstellen Sie zum Registrieren [ein leeres Azure Batch-Konto](../batch/batch-account-create-portal.md) in Ihrem Abonnement. Sie können es später wieder löschen. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Verknüpfen der Azure SSIS-IR mit einem virtuellen Netzwerk
1. Starten Sie Microsoft Edge oder Google Chrome. Derzeit wird die Data Factory-Benutzeroberfläche nur in diesen Webbrowsern unterstützt. 

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) links im Menü auf **Data Factorys**. Wenn die Option **Data Factorys** nicht im Menü angezeigt wird, wählen Sie **Weitere Dienste** und dann im Abschnitt **INTELLIGENCE + ANALYSE** die Option **Data Factorys** aus. 

   ![Liste von Data Factorys](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Wählen Sie Ihre Data Factory mit der Azure SSIS-Integration Runtime in der Liste aus. Die Startseite für Ihre Data Factory wird angezeigt. Wählen Sie die Kachel **Verfassen und bereitstellen** aus. Die Data Factory-Benutzeroberfläche wird auf einer separaten Registerkarte angezeigt. 

   ![Data Factory-Startseite](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Wechseln Sie in der Data Factory-Benutzeroberfläche zur Registerkarte **Bearbeiten**, klicken Sie auf **Verbindungen**, und wechseln Sie zur Registerkarte **Integration Runtimes**. 

   ![Registerkarte „Integration Runtimes“](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Wenn Ihre Azure SSIS IR ausgeführt wird, wählen Sie in der Integration Runtime-Liste in der Spalte **Aktionen** für Ihre Azure SSIS-IR die Schaltfläche **Beenden** aus. Sie können eine IR erst bearbeiten, wenn sie nicht länger ausgeführt wird. 

   ![Beenden der IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Wählen Sie in der Integration Runtime-Liste in der Spalte **Aktionen** für Ihre Azure SSIS-IR die Schaltfläche **Bearbeiten** aus. 

   ![Bearbeiten der Integration Runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Klicken Sie auf der Seite **Allgemeine Einstellungen** des Fensters **Integration Runtime-Setup** auf **Weiter**. 

   ![Allgemeine Einstellungen für IR-Setup](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)

1. Geben Sie auf der Seite **SQL-Einstellungen** das Administratorkennwort ein, und wählen Sie **Weiter** aus. 

   ![SQL Server-Einstellungen für IR-Setup](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)

1. Gehen Sie auf der Seite **Erweiterte Einstellungen** folgendermaßen vor: 

   a. Aktivieren Sie das Kontrollkästchen **Select a VNet for your Azure-SSIS Integration Runtime to join and allow Azure services to configure VNet permissions/settings** (VNET für Ihre Azure SSIS Integration Runtime zum Verknüpfen auswählen und Azure-Diensten das Konfigurieren von VNET-Berechtigungen/-Einstellungen erlauben). 

   b. Wählen Sie für **Typ** ein klassisches virtuelles Netzwerk oder ein virtuelles Azure Resource Manager-Netzwerk aus. 

   c. Wählen Sie unter **VNET-Name** Ihr virtuelles Netzwerk aus. 

   d. Wählen Sie unter **Subnetzname** Ihr Subnet in dem virtuellen Netzwerk aus. 

   e. Klicken Sie auf **VNET-Überprüfung** und bei erfolgreicher Überprüfung auf **Aktualisieren**. 

   ![Erweiterte Einstellungen für IR-Setup](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. Jetzt können Sie die IR starten, indem Sie in der Spalte **Aktionen** für Ihre Azure SSIS-IR die Schaltfläche **Starten** auswählen. Es dauert etwa 20 bis 30 Minuten, bis eine Azure SSIS-IR gestartet wird. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="configure-a-virtual-network"></a>Konfigurieren eines virtuellen Netzwerks
Sie müssen ein virtuelles Netzwerk konfigurieren, bevor Sie eine Azure SSIS-IR damit verknüpfen können. Um Berechtigungen/Einstellungen für das virtuelle Netzwerk für die Azure SSIS-Integration Runtime, die mit dem Netzwerk verknüpft werden soll, automatisch zu konfigurieren, fügen Sie das folgende Skript hinzu:

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
Sie können eine Azure SSIS-IR erstellen und gleichzeitig mit einem virtuellen Netzwerk verknüpfen. Das vollständige Skript und Anweisungen finden Sie unter [Erstellen einer Azure SSIS-Integration Runtime](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Verknüpfen einer Azure SSIS-Integration Runtime mit einem virtuellen Netzwerk
Das Skript im Artikel [Erstellen einer Azure SSIS Integration Runtime](create-azure-ssis-integration-runtime.md) veranschaulicht, wie eine Azure SSIS-Integration Runtime im selben Skript erstellt und mit einem virtuellen Netzwerk verknüpft wird. Wenn Sie über eine Azure SSIS-IR verfügen, führen Sie die folgenden Schritte aus, um sie mit dem virtuellen Netzwerk zu verknüpfen: 
1. Beenden Sie die Azure SSIS IR. 
1. Konfigurieren Sie die Azure SSIS-IR so, dass Sie mit dem virtuellen Netzwerk verknüpft wird. 
1. Starten Sie die Azure SSIS IR. 

### <a name="define-the-variables"></a>Definieren der Variablen
```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Beenden der Azure SSIS IR
Beenden Sie die Azure SSIS-Integration Runtime, bevor Sie sie mit einem virtuellen Netzwerk verknüpfen. Dieser Befehl gibt alle Knoten frei und beendet die Abrechnung:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Konfigurieren der Einstellungen des virtuellen Netzwerks für das Verknüpfen der Azure SSIS-IR
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
Um die Azure SSIS-Integration Runtime für die Verknüpfung mit dem virtuellen Netzwerk zu konfigurieren, führen Sie den Befehl `Set-AzDataFactoryV2IntegrationRuntime` aus: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Starten der Azure SSIS IR
Führen Sie den folgenden Befehl aus, um die Azure SSIS-Integration Runtime zu starten: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

Das Abschließen dieses Befehls dauert 20 bis 30 Minuten.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Azure SSIS-Runtime finden Sie in den folgenden Themen: 
- [Azure SSIS-Integration Runtime:](concepts-integration-runtime.md#azure-ssis-integration-runtime) Dieser Artikel enthält allgemeine konzeptionelle Informationen zu Integration Runtimes, einschließlich der Azure SSIS-IR. 
- [Tutorial: Bereitstellen von SSIS-Paketen in Azure](tutorial-create-azure-ssis-runtime-portal.md): Dieser Artikel enthält ausführliche Anweisungen zum Erstellen einer Azure SSIS-IR. Dabei wird Azure SQL-Datenbank zum Hosten des SSIS-Katalogs verwendet. 
- [Erstellen einer Azure-SSIS Integration Runtime in Azure Data Factory | Microsoft-Dokumentation](create-azure-ssis-integration-runtime.md). In diesem Artikel wird das Tutorial vertieft, und er enthält Anweisungen zur Verwendung einer Azure SQL-Datenbank-Instanz mit VNET-Dienstendpunkten/verwalteter Instanz zum Hosten des SSIS-Katalogs und Verknüpfen der IR mit einem virtuellen Netzwerk. 
- [Überwachen einer Azure-SSIS-Integrationslaufzeit](monitor-integration-runtime.md#azure-ssis-integration-runtime): In diesem Artikel wird das Abrufen von Informationen zu einer Azure-SSIS-Integrationslaufzeit veranschaulicht, und er enthält Beschreibungen der Status in den zurückgegebenen Informationen. 
- [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md): In diesem Artikel wird beschrieben, wie Sie eine Azure-SSIS-Integrationslaufzeit beenden, starten oder entfernen. Außerdem wird gezeigt, wie Sie Ihre Azure SSIS-IR horizontal hochskalieren, indem Sie weitere Knoten hinzufügen. 
