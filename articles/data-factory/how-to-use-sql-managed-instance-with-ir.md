---
title: Verwenden von verwalteten Azure SQL-Instanzen mit Azure SQL Server Integration Services (SSIS) in Azure Data Factory
description: Erfahren Sie, wie Sie verwaltete Azure SQL-Instanzen mit SQL Server Integration Services (SSIS) in Azure Data Factory verwenden.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 4/15/2020
ms.openlocfilehash: 86bff161e29384b10030ed3d524301f6dea6037e
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92634163"
---
# <a name="use-azure-sql-managed-instance-with-sql-server-integration-services-ssis-in-azure-data-factory"></a>Verwenden von verwalteten Azure SQL-Instanzen mit SQL Server Integration Services (SSIS) in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Sie können Ihre SQL Server Integration Services-Projekte, -Pakete und -Workloads (SSIS) nun in die Azure-Cloud verschieben. SSIS-Projekte und -Pakete können Sie in Azure SQL-Datenbank oder einer verwalteten SQL-Instanz mit gängigen Tools wie SQL Server Management Studio (SSMS) bereitstellen, ausführen und verwalten. In diesem Artikel werden die folgenden spezifischen Bereiche bei der Verwendung einer verwalteten Azure SQL-Instanz mit der Azure-SSIS Integration Runtime (IR) hervorgehoben:

- [Bereitstellen einer Azure-SSIS IR mit SSIS-Katalog (SSISDB), die auf einer verwalteten Azure SQL-Instanz gehostet wird](#provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance)
- [Ausführen von SSIS-Paketen mit einem Agent-Auftrag einer verwalteten Azure SQL-Datenbankinstanz](how-to-invoke-ssis-package-managed-instance-agent.md)
- [Bereinigen von SSISDB-Protokollen mit einem Agent-Auftrag einer verwalteten Azure SQL-Datenbankinstanz](#clean-up-ssisdb-logs)
- [Azure-SSIS IR-Failover bei einer verwalteten Azure SQL-Instanz](configure-bcdr-azure-ssis-integration-runtime.md#azure-ssis-ir-failover-with-a-sql-managed-instance)
- [Migrieren von lokalen SSIS-Workloads zu SSIS in ADF mit einer verwalteten Azure SQL-Instanz als Datenbank-Workloadziel](scenario-ssis-migration-overview.md#azure-sql-managed-instance-as-database-workload-destination)

## <a name="provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance"></a>Bereitstellen einer Azure-SSIS IR mit SSISDB, die auf einer verwalteten Azure SQL-Instanz gehostet wird

### <a name="prerequisites"></a>Voraussetzungen

1. [Aktivieren Sie Azure Active Directory (Azure AD) für verwaltete Azure SQL-Instanzen](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance), wenn Sie sich für die Azure Active Directory-Authentifizierung entscheiden.

1. Wählen Sie aus, wie die SQL Managed Instance verbunden werden soll – über einen privaten oder einen öffentlichen Endpunkt:

    - Über einen privaten Endpunkt (bevorzugt)

        1. Wählen Sie das virtuelle Netzwerk aus, das mit der Azure-SSIS IR verknüpft werden soll:
            - Im selben virtuellen Netzwerk wie die verwaltete Instanz mit **unterschiedlichen Subnetzen** .
            - In einem anderen virtuellen Netzwerk als die verwaltete Instanz über Peering virtueller Netzwerke (das aufgrund von Einschränkungen für globales VNET-Peering auf dieselbe Region beschränkt ist) oder eine Verbindung zwischen den virtuellen Netzwerken.

            Weitere Informationen zu SQL Managed Instance-Verbindungen finden Sie unter [Herstellen einer Verbindung zwischen einer Anwendung und Azure SQL Managed Instance](https://review.docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connect-app).

        1. [Konfigurieren Sie das virtuelle Netzwerk](#configure-virtual-network).

    - Über einen öffentlichen Endpunkt

        Verwaltete Azure SQL-Instanzen können Konnektivität über [öffentliche Endpunkte](../azure-sql/managed-instance/public-endpoint-configure.md) bereitstellen. Eingangs- und Ausgangsanforderungen, die erfüllt werden müssen, um Datenverkehr zwischen SQL Managed Instance und der Azure-SSIS IR zuzulassen:

        - Wenn sich die Azure-SSIS IR nicht in einem virtuellen Netzwerk befindet (bevorzugt)

            **Anforderung an eingehenden Datenverkehr von SQL Managed Instance** zum Zulassen von eingehendem Datenverkehr von der Azure-SSIS IR.

            | Transportprotokoll | `Source` | Quellportbereich | Destination | Destination port range |
            |---|---|---|---|---|
            |TCP|Azure-Clouddiensttag|*|VirtualNetwork|3342|

            Weitere Informationen finden Sie unter [Zulassen von Datenverkehr auf dem öffentlichen Endpunkt in der Netzwerksicherheitsgruppe](../azure-sql/managed-instance/public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).

        - Wenn sich die Azure-SSIS IR in einem virtuellen Netzwerk befindet

            Es gibt ein spezielles Szenario, in dem sich SQL Managed Instance in einer Region befindet, die von der Azure-SSIS IR nicht unterstützt wird, und sich die Azure-SSIS IR in einem virtuellen Netzwerk ohne VNET-Peering (aufgrund von Einschränkungen für globales VNET-Peering) befindet. In diesem Szenario stellt die **Azure-SSIS IR in einem virtuellen Netzwerk** eine Verbindung mit SQL Managed Instance **über einen öffentlichen Endpunkt** her. Verwenden Sie die nachstehenden Regeln für Netzwerksicherheitsgruppen (NSG), um Datenverkehr zwischen SQL Managed Instance und der Azure-SSIS IR zuzulassen:

            1. **Anforderung an eingehenden Datenverkehr von SQL Managed Instance** zum Zulassen von eingehendem Datenverkehr von der Azure-SSIS IR.

                | Transportprotokoll | `Source` | Quellportbereich | Destination |Destination port range |
                |---|---|---|---|---|
                |TCP|Statische IP-Adresse der Azure-SSIS IR <br> Ausführliche Informationen finden Sie unter [Bereitstellen einer eigenen öffentlichen IP-Adresse für eine Azure-SSIS IR](join-azure-ssis-integration-runtime-virtual-network.md#publicIP).|*|VirtualNetwork|3342|

             1. **Anforderung an ausgehenden Datenverkehr der Azure-SSIS IR** zum Zulassen von ausgehendem Datenverkehr an SQL Managed Instance.

                | Transportprotokoll | `Source` | Quellportbereich | Destination |Destination port range |
                |---|---|---|---|---|
                |TCP|VirtualNetwork|*|[IP-Adresse des öffentlichen Endpunkts von SQL Managed Instance](../azure-sql/managed-instance/management-endpoint-find-ip-address.md)|3342|

### <a name="configure-virtual-network"></a>Konfigurieren eines virtuellen Netzwerks

1. **Benutzerberechtigung** . Der Benutzer, der die Azure-SSIS IR erstellt, muss über die [Rollenzuweisung](../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope) mindestens für die Azure Data Factory-Ressource verfügen, und zwar mit einer der folgenden Optionen:

    - Verwenden Sie die integrierte Rolle „Netzwerkmitwirkender“. Diese Rolle umfasst die Berechtigung _Microsoft.Network/\*_ , die jedoch einen deutlich größeren Umfang als erforderlich hat.
    - Erstellen Sie eine benutzerdefinierte Rolle, die nur die erforderliche Berechtigung _Microsoft.Network/virtualNetworks/\*/join/action_ aufweist. Wenn Sie beim Verknüpfen der Azure-SSIS IR mit einem virtuellen Azure Resource Manager-Netzwerk Ihre eigenen öffentlichen IP-Adressen verwenden möchten, beziehen Sie auch die Berechtigung _Microsoft.Network/publicIPAddresses/*/join/action_ mit in die Rolle ein.

1. **Virtuelles Netzwerk** .

    1. Stellen Sie sicher, dass bestimmte Azure-Netzwerkressourcen durch die Ressourcengruppe des virtuellen Netzwerks erstellt und gelöscht werden können.

        Die Azure SSIS-IR muss bestimmte Netzwerkressourcen unter der gleichen Ressourcengruppe erstellen wie das virtuelle Netzwerk. Diese Ressourcen umfassen Folgendes:
        - Einen Azure-Lastenausgleich mit dem Namen *\<Guid>-azurebatch-cloudserviceloadbalancer*
        - Eine Netzwerksicherheitsgruppe mit dem Namen *\<Guid>-azurebatch-cloudservicenetworksecuritygroup
        - Eine öffentliche Azure-IP-Adresse mit dem Namen „-azurebatch-cloudservicepublicip“

        Diese Ressourcen werden beim Start Ihrer Azure-SSIS IR erstellt. Sie werden gelöscht, wenn die Azure-SSIS IR beendet wird. Damit Ihre Azure-SSIS IR ordnungsgemäß beendet werden kann, sollten Sie diese Netzwerkressourcen nicht in anderen Ressourcen wiederverwenden.

    1. Stellen Sie sicher, dass für die Ressourcengruppe/das Abonnement, zu der bzw. dem das virtuelle Netzwerk gehört, keine [Ressourcensperre](../azure-resource-manager/management/lock-resources.md) besteht. Wenn Sie eine Schreibschutzsperre oder eine Löschsperre konfigurieren, kann beim Starten und Beenden Ihrer Azure-SSIS IR ein Fehler auftreten oder die IR nicht mehr reagieren.

    1. Stellen Sie sicher, dass Sie keine Azure-Richtlinie haben, die verhindert, dass die folgenden Ressourcen unter der Ressourcengruppe/dem Abonnement erstellt werden, zu der bzw. dem das virtuelle Netzwerk gehört:
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

    1. Lassen Sie Datenverkehr in Netzwerksicherheitsgruppen-Regeln (NSG) zu, um Datenverkehr zwischen SQL Managed Instance und der Azure-SSIS IR sowie den für die Azure-SSIS IR erforderlichen Datenverkehr zuzulassen.
        1. **Anforderung an eingehenden Datenverkehr von SQL Managed Instance** zum Zulassen von eingehendem Datenverkehr von der Azure-SSIS IR.

            | Transportprotokoll | `Source` | Quellportbereich | Destination | Destination port range | Kommentare |
            |---|---|---|---|---|---|
            |TCP|VirtualNetwork|*|VirtualNetwork|1433, 11000–11999|Wenn die Verbindungsrichtlinie Ihres SQL-Datenbank-Servers anstatt auf **Redirect** auf **Proxy** festgelegt ist, wird nur Port 1433 benötigt.|

        1. **Anforderung an ausgehenden Datenverkehr der Azure-SSIS IR** zum Zulassen von ausgehendem Datenverkehr an SQL Managed Instance und anderem für die Azure-SSIS IR erforderlichem Datenverkehr.

        | Transportprotokoll | `Source` | Quellportbereich | Destination | Destination port range | Kommentare |
        |---|---|---|---|---|---|
        | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000–11999 |Lassen Sie ausgehenden Datenverkehr an SQL Managed Instance zu. Wenn die Verbindungsrichtlinie auf **Proxy** anstelle von **Redirect** festgelegt ist, wird nur Port 1433 benötigt. |
        | TCP | VirtualNetwork | * | AzureCloud | 443 | Die Knoten Ihrer Azure-SSIS IR im virtuellen Netzwerk verwenden diesen Port für den Zugriff auf Azure-Dienste wie Azure Storage und Azure Event Hubs. |
        | TCP | VirtualNetwork | * | Internet | 80 | (Optional) Die Knoten Ihrer Azure-SSIS IR im virtuellen Netzwerk verwenden diesen Port zum Herunterladen einer Zertifikatssperrliste aus dem Internet. Wenn Sie diesen Datenverkehr blockieren, kann es beim Starten der IR zu einer Leistungsherabstufung kommen und die Möglichkeit zum Überprüfen der Zertifikatssperrliste im Hinblick auf die Zertifikatverwendung verloren gehen. Wenn Sie das Ziel weiter auf bestimmte FQDNs eingrenzen möchten, finden Sie entsprechende Informationen im Abschnitt [Verwenden von Azure ExpressRoute oder UDR](./join-azure-ssis-integration-runtime-virtual-network.md#route).|
        | TCP | VirtualNetwork | * | Storage | 445 | (Optional) Diese Regel ist nur erforderlich, wenn Sie das in Azure Files gespeicherte SSIS-Paket ausführen möchten. |
        |||||||

        1. **Anforderung an eingehenden Datenverkehr der Azure-SSIS IR** zum Zulassen des für die Azure-SSIS IR erforderlichen Datenverkehrs.

        | Transportprotokoll | `Source` | Quellportbereich | Destination | Destination port range | Kommentare |
        |---|---|---|---|---|---|
        | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (wenn Sie die IR mit einem virtuellen Resource Manager-Netzwerk verknüpfen) <br/><br/>10100, 20100, 30100 (wenn Sie die IR mit einem klassischen virtuellen Netzwerk verknüpfen)| Der Data Factory-Dienst nutzt diese Ports für die Kommunikation mit den Knoten der Azure-SSIS IR im virtuellen Netzwerk. <br/><br/> Unabhängig davon, ob Sie eine NSG auf Subnetzebene erstellen, konfiguriert Data Factory immer eine NSG auf der Ebene der Netzwerkschnittstellenkarten (NICs), die an die virtuellen Computer angefügt sind, auf denen die Azure-SSIS IR gehostet wird. Nur eingehenden Datenverkehr von Data Factory-IP-Adressen für die angegebenen Ports wird durch diese NSG auf NIC-Ebene zugelassen. Auch wenn Sie diese Ports für den Internetdatenverkehr auf Subnetzebene öffnen, wird Datenverkehr von anderen IP-Adressen als Data Factory-IP-Adressen auf NIC-Ebene blockiert. |
        | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | (Optional:) Diese Regel ist nur erforderlich, wenn ein Mitarbeiter des Microsoft-Supports den Kunden zum Öffnen für erweiterte Problembehandlung auffordert. Direkt nach Abschluss der Problembehandlung können die Ports wieder geschlossen werden. Das Diensttag **CorpNetSaw** gestattet nur sicheren Zugriff auf Arbeitsstationen im Microsoft-Unternehmensnetzwerk, um den Remotedesktop verwenden zu können. Dieses Diensttag kann im Portal nicht ausgewählt werden und steht nur über Azure PowerShell oder die Azure-Befehlszeilenschnittstelle (Azure CLI) zur Verfügung. <br/><br/> Auf NIC-Ebene NSG ist Port 3389 standardmäßig geöffnet, und wir ermöglichen es Ihnen, Port 3389 auf Subnetzebene-NSG zu steuern. In der Zwischenzeit hat Azure-SSIS IR Port 3389 ausgehend von der Windows-Firewallregel auf jedem IR-Knoten für Schutzzwecke standardmäßig nicht zugelassen. |
        |||||||

    1. Weitere Informationen finden Sie unter [Konfiguration von virtuellen Netzwerken](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration):
        - Sie verwenden Ihre eigenen öffentlichen IP-Adressen für die Azure-SSIS IR.
        - Sie verwenden einen eigenen DNS-Server (Domain Name System).
        - Sie verwenden Azure ExpressRoute oder eine benutzerdefinierte Route (User-Defined Route, UDR).
        - Sie verwenden eine angepasste Azure-SSIS IR.

### <a name="provision-azure-ssis-integration-runtime"></a>Bereitstellen einer Azure-SSIS Integration Runtime

1. Wählen Sie einen privaten oder öffentlichen Endpunkt für SQL Managed Instance aus.

    Beim [Bereitstellen einer Azure-SSIS IR](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime) im Azure-Portal oder der ADF-App müssen Sie, wenn Sie den SSIS-Katalog (SSISDB) erstellen, auf der Seite mit den SQL-Einstellungen für SQL Managed Instance einen **privaten Endpunkt** oder einen **öffentlichen Endpunkt** verwenden.

    Der Hostname des öffentlichen Endpunkts weist das Format „<mi_name>.public.<dns_zone>.database.windows.net“ auf, und für die Verbindung wird Port 3342 verwendet.  

    ![Screenshot der Integration Runtime-Einrichtung mit aktivierter Option „SSIS-Katalog erstellen“ und eingegebenem Datenbankserver-Endpunkt für den Katalog](./media/how-to-use-sql-managed-instance-with-ir/catalog-public-endpoint.png)

1. Wählen Sie ggf. die Azure AD-Authentifizierung aus.

    ![catalog-public-endpoint](./media/how-to-use-sql-managed-instance-with-ir/catalog-aad.png)

    Weitere Informationen zum Aktivieren der Azure AD-Authentifizierung finden Sie unter [Aktivieren von Azure AD in der verwalteten Azure SQL-Instanz](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance).

1. Binden Sie die Azure-SSIS IR ggf. in ein virtuelles Netzwerk ein.

    Wählen Sie auf der Seite „Erweiterte Einstellungen“ das virtuelle Netzwerk und das Subnetz für den Beitritt aus.
    
    Wenn es sich um dasselbe virtuelle Netzwerk wie für SQL Managed Instance handelt, wählen Sie ein **anderes Subnetz** als SQL Managed Instance aus. 

    Weitere Informationen zum Verknüpfen einer Azure-SSIS IR mit einem virtuellen Netzwerk finden Sie unter [Aufnehmen einer Azure-SSIS Integration Runtime in ein virtuelles Netzwerk](join-azure-ssis-integration-runtime-virtual-network.md).

    ![Screenshot der erweiterten Einstellungen für die Integration Runtime-Einrichtung zur Auswahl eines virtuellen Netzwerks für den Beitritt Ihrer Runtime](./media/how-to-use-sql-managed-instance-with-ir/join-virtual-network.png)

Weitere Informationen zum Erstellen einer Azure-SSIS IR finden Sie im Artikel [Erstellen der Azure-SSIS Integration Runtime in Azure Data Factory](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime).

## <a name="clean-up-ssisdb-logs"></a>Bereinigen der SSISDB-Protokolle

Die Beibehaltungsrichtlinie für SSISDB-Protokolle wird durch die nachstehenden Eigenschaften in [catalog.catalog_properties](/sql/integration-services/system-views/catalog-catalog-properties-ssisdb-database?view=sql-server-ver15) definiert:

- OPERATION_CLEANUP_ENABLED

    Wenn der Wert TRUE ist, werden Vorgangsdetails und Vorgangsmeldungen, die älter als RETENTION_WINDOW (Tage) sind, aus dem Katalog gelöscht. Wenn der Wert FALSE ist, werden alle Vorgangsdetails und Vorgangsmeldungen im Katalog gespeichert. Hinweis: Die Vorgangsbereinigung erfolgt durch einen SQL Server-Auftrag.

- RETENTION_WINDOW

    Die Anzahl der Tage, für die Vorgangsdetails und Vorgangsmeldungen im Katalog gespeichert werden. Wenn der Wert –1 ist, besteht eine unbegrenzte Beibehaltungsdauer. Hinweis: Wenn keine Bereinigung erfolgen soll, legen Sie OPERATION_CLEANUP_ENABLED auf FALSE fest.

Um SSISDB-Protokolle zu entfernen, die außerhalb des vom Administrator festgelegten Aufbewahrungsfensters liegen, können Sie die gespeicherte Prozedur `[internal].[cleanup_server_retention_window_exclusive]` auslösen. Optional können Sie die Ausführung des Agent-Auftrags für SQL Managed Instance zum Auslösen der gespeicherten Prozedur planen.

## <a name="next-steps"></a>Nächste Schritte

- [Ausführen von SSIS-Paketen mit einem Agent-Auftrag einer verwalteten Azure SQL-Datenbankinstanz](how-to-invoke-ssis-package-managed-instance-agent.md)
- [Einrichten von Business Continuity & Disaster Recovery (BCDR)](configure-bcdr-azure-ssis-integration-runtime.md)
- [Migrieren von lokalen SSIS-Workloads zu SSIS in ADF](scenario-ssis-migration-overview.md)