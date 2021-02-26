---
title: DNS-Konfiguration für private Azure-Endpunkte
description: Informationen zur DNS-Konfiguration von privaten Azure-Endpunkten
services: private-link
author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: allensu
ms.openlocfilehash: 3f858067d8a44b1d8c46b04b9912df9bbcf17b26
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526703"
---
# <a name="azure-private-endpoint-dns-configuration"></a>DNS-Konfiguration für private Azure-Endpunkte

Es ist wichtig, die DNS-Einstellungen ordnungsgemäß zu konfigurieren, um die IP-Adresse des privaten Endpunkts in den vollqualifizierten Domänennamen (FQDN) der Verbindungszeichenfolge aufzulösen.

Bestehende Microsoft Azure-Dienste verfügen möglicherweise bereits über eine DNS-Konfiguration für einen öffentlichen Endpunkt. Diese Konfiguration muss außer Kraft gesetzt werden, um eine Verbindung mithilfe Ihres privaten Endpunkts herzustellen. 
 
Die Netzwerkschnittstelle, die dem privaten Endpunkt zugeordnet ist, enthält die Informationen zum Konfigurieren des DNS. Die Informationen zur Netzwerkschnittstelle umfassen den vollqualifizierten Domänennamen und private IP-Adressen für Ihre Private Link-Ressource. 
 
Mit den folgenden Optionen können Sie Ihre DNS-Einstellungen für private Endpunkte konfigurieren: 
- **Hostdatei (wird nur für Tests empfohlen)** . Sie können die Hostdatei auf einem virtuellen Computer verwenden, um das DNS zu überschreiben.  
- **Private DNS-Zone**. Sie können [private DNS-Zonen](../dns/private-dns-privatednszone.md) verwenden, um die DNS-Auflösung für einen privaten Endpunkt außer Kraft zu setzen. Eine private DNS-Zone kann mit Ihrem virtuellen Netzwerk verknüpft werden, um bestimmte Domänen aufzulösen.
- **DNS-Weiterleitung (optional)** . Sie können Ihre DNS-Weiterleitung verwenden, um die DNS-Auflösung für eine Private Link-Ressource außer Kraft zu setzen. Erstellen Sie eine DNS-Weiterleitungsregel, um eine private DNS-Zone auf Ihrem [DNS-Server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) zu verwenden, der in einem virtuellen Netzwerk gehostet wird.

> [!IMPORTANT]
> Es wird nicht empfohlen, eine Zone zu überschreiben, die aktiv zur Auflösung öffentlicher Endpunkte genutzt wird. Verbindungen mit Ressourcen können ohne DNS-Weiterleitung an das öffentliche DNS nicht ordnungsgemäß aufgelöst werden. Um Probleme zu vermeiden, erstellen Sie einen anderen Domänennamen, oder halten Sie sich an den vorgeschlagenen Namen für jeden der folgenden Dienste. 

## <a name="azure-services-dns-zone-configuration"></a>DNS-Zonenkonfiguration für Azure-Dienste
Von Azure wird ein CNAME-DNS-Eintrag (Canonical Name, kanonischer Name) im öffentlichen DNS erstellt. Durch den CNAME-Eintrag wird die Auflösung zum Namen der privaten Domäne umgeleitet. Sie können die Auflösung mit der privaten IP-Adresse Ihrer privaten Endpunkte überschreiben. 
 
Ihre Anwendungen müssen die Verbindungs-URL nicht ändern. Beim Auflösen mithilfe eines öffentlichen DNS-Diensts wird der DNS-Server in Ihre privaten Endpunkte aufgelöst. Der Vorgang wirkt sich nicht auf Ihre vorhandenen Anwendungen aus. 

> [!IMPORTANT]
> Private Netzwerke, die bereits die private DNS-Zone für einen bestimmten Typ verwenden, können sich nur dann mit öffentlichen Ressourcen verbinden, wenn sie über keine privaten Endpunktverbindungen verfügen; andernfalls ist eine entsprechende DNS-Konfiguration für die private DNS-Zone erforderlich, um die DNS-Auflösungssequenz zu vervollständigen. 

Verwenden Sie für Azure-Dienste die empfohlenen Zonennamen in der folgenden Tabelle:

| Typ der Private Link-Ressource/Unterressource |Name der privaten DNS-Zone | Weiterleitungen der öffentlichen DNS-Zone |
|---|---|---|
| Azure Automation/(Microsoft.Automation/automationAccounts) / Webhook, DSCAndHybridWorker | privatelink.azure-automation.net | azure-automation.net |
| Azure SQL-Datenbank (Microsoft.Sql/servers)/SQL Server | privatelink.database.windows.net | database.windows.net |
| Azure Synapse Analytics (Microsoft.Sql/servers)/SQL Server  | privatelink.database.windows.net | database.windows.net |
| Storage-Konto (Microsoft.Storage/storageAccounts)/Blob (blob, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| Storage-Konto (Microsoft.Storage/storageAccounts)/Tabelle (table, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| Storage-Konto (Microsoft.Storage/storageAccounts)/Warteschlange (queue, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| Storage-Konto (Microsoft.Storage/storageAccounts)/Datei (file, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| Storage-Konto (Microsoft.Storage/storageAccounts)/Web (web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Azure Data Lake-Dateisystem Gen2 (Microsoft.Storage/storageAccounts)/Data Lake-Dateisystem Gen2 (dfs, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/Tabelle | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Azure Database for PostgreSQL – Einzelserver (Microsoft.DBforPostgreSQL/servers)/postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Azure Database for MySQL (Microsoft.DBforMySQL/servers)/mysqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Azure Database for MariaDB (Microsoft.DBforMariaDB/servers)/mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (Microsoft.KeyVault/vaults)/Tresor | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Azure Kubernetes Service: Kubernetes-API (Microsoft.ContainerService/managedClusters)/management | privatelink.{Region}.azmk8s.io | {Region}.azmk8s.io |
| Azure Search (Microsoft.Search/searchServices)/searchService | privatelink.search.windows.net | search.windows.net |
| Azure Container Registry (Microsoft.ContainerRegistry/registries)/Registrierung | privatelink.azurecr.io | azurecr.io |
| Azure App Configuration (Microsoft.AppConfiguration/configurationStores)/configurationStore | privatelink.azconfig.io | azconfig.io |
| Azure Backup (Microsoft.RecoveryServices/vaults)/Tresor | privatelink.{region}.backup.windowsazure.com | {Region}.backup.windowsazure.com |
| Azure Event Hubs (Microsoft.EventHub/namespaces)/Namespace | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Service Bus (Microsoft.ServiceBus/namespaces)/Namespace | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure IoT Hub (Microsoft.Devices/IotHubs)/iotHub | privatelink.azure-devices.net<br/>privatelink.servicebus.windows.net<sup>1</sup> | azure-devices.net<br/>servicebus.windows.net |
| Azure Relay (Microsoft.Relay/namespaces)/Namespace | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Event Grid (Microsoft.EventGrid/topics)/Thema | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Event Grid (Microsoft.EventGrid/domains)/Domäne | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Web-Apps (Microsoft.Web/sites)/sites | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning (Microsoft.MachineLearningServices/workspaces)/amlworkspace | privatelink.api.azureml.ms<br/>privatelink.notebooks.azure.net | api.azureml.ms<br/>notebooks.azure.net<br/>aznbcontent.net |
| IoT Hub (Microsoft.Devices/IotHubs)/IotHub | privatelink.azure-devices.net | azure-devices.net |
| SignalR (Microsoft.SignalRService/SignalR)/signalR | privatelink.service.signalr.net | service.signalr.net |
| Azure Monitor (Microsoft.Insights/privateLinkScopes)/azuremonitor | privatelink.monitor.azure.com<br/> privatelink.oms.opinsights.azure.com <br/> privatelink.ods.opinsights.azure.com <br/> privatelink.agentsvc.azure-automation.net | monitor.azure.com<br/> oms.opinsights.azure.com<br/> ods.opinsights.azure.com<br/> agentsvc.azure-automation.net |
| Cognitive Services (Microsoft.CognitiveServices/accounts)/account | privatelink.cognitiveservices.azure.com  | cognitiveservices.azure.com  |
| Azure File Sync (Microsoft.StorageSync/storageSyncServices) / afs |  privatelink.afs.azure.net  |  afs.azure.net  |
| Azure Data Factory (Microsoft.DataFactory/factories)/dataFactory |  privatelink.datafactory.azure.net  |  datafactory.azure.net  |
| Azure Data Factory (Microsoft.DataFactory/factories)/portal |  privatelink.azure.com  |  azure.com  |
| Azure Cache for Redis (Microsoft.Cache/Redis)/redisCache | privatelink.redis.cache.windows.net | redis.cache.windows.net |

<sup>1</sup> Für die Verwendung mit dem integrierten Event Hub-kompatiblen Endpunkt von IoT Hub. Weitere Informationen finden Sie unter [Integrierter, mit Event Hub kompatibler Endpunkt](../iot-hub/virtual-network-support.md#built-in-event-hub-compatible-endpoint).

### <a name="china"></a>China

| Typ der Private Link-Ressource/Unterressource |Name der privaten DNS-Zone | Weiterleitungen der öffentlichen DNS-Zone |
|---|---|---|
| Azure SQL-Datenbank (Microsoft.Sql/servers)/SQL Server | privatelink.database.chinacloudapi.cn | database.chinacloudapi.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/SQL | privatelink.documents.azure.cn | documents.azure.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/MongoDB | privatelink.mongo.cosmos.azure.cn | mongo.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/Cassandra | privatelink.cassandra.cosmos.azure.cn | cassandra.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/Gremlin | privatelink.gremlin.cosmos.azure.cn | gremlin.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/Tabelle | privatelink.table.cosmos.azure.cn | table.cosmos.azure.cn |
| Azure Database for PostgreSQL – Einzelserver (Microsoft.DBforPostgreSQL/servers)/postgresqlServer | privatelink.postgres.database.chinacloudapi.cn | postgres.database.chinacloudapi.cn |
| Azure Database for MySQL (Microsoft.DBforMySQL/servers)/mysqlServer | privatelink.mysql.database.chinacloudapi.cn  | mysql.database.chinacloudapi.cn  |
| Azure Database for MariaDB (Microsoft.DBforMariaDB/servers)/mariadbServer | privatelink.mariadb.database.chinacloudapi.cn | mariadb.database.chinacloudapi.cn |


## <a name="dns-configuration-scenarios"></a>Szenarien der DNS-Konfiguration

Der FQDN der Dienste wird automatisch in eine öffentliche IP-Adresse aufgelöst. Ändern Sie die DNS-Konfiguration, damit die Auflösung in die private IP-Adresse des privaten Endpunkts erfolgt.

DNS ist eine kritische Komponente für die ordnungsgemäße Funktion der Anwendung, indem die IP-Adresse des privaten Endpunkts erfolgreich aufgelöst wird.

Basierend auf Ihren Einstellungen sind die folgenden Szenarien mit integrierter DNS-Auflösung verfügbar:

- [Virtual Network-Workloads ohne benutzerdefinierten DNS-Server](#virtual-network-workloads-without-custom-dns-server)
- [Lokale Workloads mit DNS-Weiterleitung](#on-premises-workloads-using-a-dns-forwarder)
- [Lokale Workloads und Workloads in virtuellen Netzwerken mit DNS-Weiterleitung](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder)

> [!NOTE]
> Der [DNS-Proxy der Azure Firewall](../firewall/dns-settings.md#dns-proxy) kann als DNS-Weiterleitung für [lokale Workloads](#on-premises-workloads-using-a-dns-forwarder) und [virtuelle Netzwerkworkloads, die eine DNS-Weiterleitung verwenden](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder), verwendet werden.

## <a name="virtual-network-workloads-without-custom-dns-server"></a>Virtual Network-Workloads ohne benutzerdefinierten DNS-Server

Diese Konfiguration ist für Workloads virtueller Netzwerke ohne benutzerdefinierten DNS-Server geeignet. In diesem Szenario fragt der Client die IP-Adresse des privaten Endpunkts beim von Azure bereitgestellten DNS-Dienst als [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) ab. Azure DNS ist für die DNS-Auflösung der privaten DNS-Zonen verantwortlich.

> [!NOTE]
> In diesem Szenario wird die empfohlene private DNS-Zone für Azure SQL-Datenbank verwendet. Für andere Dienste können Sie das Modell mithilfe der folgenden Referenz anpassen: [DNS-Zonenkonfiguration für Azure-Dienste](#azure-services-dns-zone-configuration).

Für eine korrekte Konfiguration benötigen Sie die folgenden Ressourcen:

- Virtuelles Clientnetzwerk

- Private DNS-Zone [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) mit [A-Datensatz](../dns/dns-zones-records.md#record-types)

- Informationen zum privaten Endpunkt (Name des FQDN-Eintrags und private IP-Adresse)

Der folgende Screenshot veranschaulicht die DNS-Auflösungssequenz aus Workloads virtueller Netzwerke über private DNS-Zonen:

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Einzelnes virtuelles Netzwerk und von Azure bereitgestelltes DNS":::

Sie können dieses Modell auf virtuelle Netzwerke mit Peering ausweiten, die demselben privaten Endpunkt zugeordnet sind. Dazu [fügen Sie neue Verknüpfungen für virtuelle Netzwerke](../dns/private-dns-virtual-network-links.md) zur privaten DNS-Zone für alle mittels Peering verbundenen virtuellen Netzwerke hinzu.

> [!IMPORTANT]
> Für diese Konfiguration ist eine einzelne private DNS-Zone erforderlich. Zum Erstellen mehrerer Zonen mit demselben Namen für verschiedene virtuelle Netzwerke müssen die DNS-Einträge manuell zusammengeführt werden.

> [!IMPORTANT]
> Wenn Sie einen privaten Endpunkt in einem Hub-and-Spoke-Modell aus einem anderen Abonnement verwenden, sollten Sie dieselbe private DNS-Zone auf dem Hub wiederverwenden.

In diesem Szenario wird eine [Hub-and-Spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)-Netzwerktopologie verwendet. Die Spoke-Netzwerke nutzen einen gemeinsamen privaten Endpunkt. Die virtuellen Spoke-Netzwerke sind mit derselben privaten DNS-Zone verknüpft. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Hub-and-Spoke-Modell mit von Azure bereitgestelltem DNS":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>Lokale Workloads mit DNS-Weiterleitung

Damit der FQDN eines privaten Endpunkts von lokalen Workloads aufgelöst werden kann, verwenden Sie eine DNS-Weiterleitung, um die [öffentliche DNS-Zone](#azure-services-dns-zone-configuration) des Azure-Diensts in Azure aufzulösen.

Im folgenden Szenario wird ein lokales Netzwerk verwendet, das über eine DNS-Weiterleitung in Azure verfügt. Von dieser Weiterleitung werden DNS-Abfragen über eine Weiterleitung auf Serverebene in die von Azure bereitgestellte IP-Adresse [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) (DNS) aufgelöst. 

> [!NOTE]
> In diesem Szenario wird die empfohlene private DNS-Zone für Azure SQL-Datenbank verwendet. Für andere Dienste können Sie das Modell mithilfe der folgenden Referenz anpassen: [DNS-Zonenkonfiguration für Azure-Dienste](#azure-services-dns-zone-configuration).

Für eine korrekte Konfiguration benötigen Sie die folgenden Ressourcen:

- Lokales Netzwerk
- Virtuelles Netzwerk [mit Verbindung mit lokalem Standort](/azure/architecture/reference-architectures/hybrid-networking/)
- In Azure bereitgestellte DNS-Weiterleitung 
- Zonen im privaten DNS [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) mit [A-Datensatz](../dns/dns-zones-records.md#record-types)
- Informationen zum privaten Endpunkt (Name des FQDN-Eintrags und private IP-Adresse)

Das folgende Diagramm veranschaulicht die DNS-Auflösungssequenz eines lokalen Netzwerks. In der Konfiguration wird eine in Azure bereitgestellte DNS-Weiterleitung verwendet. Die Auflösung erfolgt durch eine private DNS-Zone, die [mit einem virtuellen Netzwerk verknüpft](../dns/private-dns-virtual-network-links.md) ist:

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Lokal per Azure DNS":::

Diese Konfiguration kann für ein lokales Netzwerk erweitert werden, für das bereits eine DNS-Lösung vorhanden ist. Die lokale DNS-Lösung ist so konfiguriert, dass der DNS-Datenverkehr über eine [bedingte Weiterleitung](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) an Azure geleitet wird. Die bedingte Weiterleitung verweist auf die in Azure bereitgestellte DNS-Weiterleitung.

> [!NOTE]
> In diesem Szenario wird die empfohlene private DNS-Zone für Azure SQL-Datenbank verwendet. Für andere Dienste können Sie das Modell mithilfe der folgenden Referenz anpassen: [DNS-Zonenkonfiguration für Azure-Dienste](#azure-services-dns-zone-configuration)

Für eine korrekte Konfiguration benötigen Sie die folgenden Ressourcen:

- Lokales Netzwerk mit einer benutzerdefinierten DNS-Lösung 
- Virtuelles Netzwerk [mit Verbindung mit lokalem Standort](/azure/architecture/reference-architectures/hybrid-networking/)
- In Azure bereitgestellte DNS-Weiterleitung
- Zonen im privaten DNS [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) mit [A-Datensatz](../dns/dns-zones-records.md#record-types)
- Informationen zum privaten Endpunkt (Name des FQDN-Eintrags und private IP-Adresse)

Das folgende Diagramm veranschaulicht die DNS-Auflösung eines lokalen Netzwerks. Die DNS-Auflösung wird bedingt an Azure weitergeleitet. Die Auflösung erfolgt durch eine private DNS-Zone, die [mit einem virtuellen Netzwerk verknüpft](../dns/private-dns-virtual-network-links.md) ist.

> [!IMPORTANT]
> Die bedingte Weiterleitung muss über die empfohlene [Weiterleitung der Zone des öffentlichen DNS erfolgen](#azure-services-dns-zone-configuration). Beispiel: `database.windows.net` anstelle von **privatelink**.database.windows.net.

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="Lokale Weiterleitung an Azure DNS":::

## <a name="virtual-network-and-on-premises-workloads-using-a-dns-forwarder"></a>Lokale Workloads und Workloads in virtuellen Netzwerken mit DNS-Weiterleitung

Verwenden Sie für Workloads, die von virtuellen und lokalen Netzwerken auf einen privaten Endpunkt zugreifen, eine DNS-Weiterleitung zum Auflösen der [öffentlichen DNS-Zone](#azure-services-dns-zone-configuration) des Azure-Diensts, die in Azure bereitgestellt wird.

Im folgenden Szenario wird ein lokales Netzwerk mit virtuellen Netzwerken in Azure verwendet. Beide Netzwerke greifen auf den privaten Endpunkt in einem Netzwerk mit freigegebenem Hub zu.

Diese DNS-Weiterleitung ist dafür zuständig, alle DNS-Abfragen über eine Weiterleitung auf Serverebene zum von Azure bereitgestellten DNS-Dienst [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) aufzulösen.

> [!IMPORTANT]
> Für diese Konfiguration ist eine einzelne private DNS-Zone erforderlich. Alle Clientverbindungen aus lokalen Netzwerken und [virtuellen Netzwerken mit Peering](../virtual-network/virtual-network-peering-overview.md) müssen ebenfalls dieselbe private DNS-Zone verwenden.

> [!NOTE]
> In diesem Szenario wird die empfohlene private DNS-Zone für Azure SQL-Datenbank verwendet. Für andere Dienste können Sie das Modell mithilfe der folgenden Referenz anpassen: [DNS-Zonenkonfiguration für Azure-Dienste](#azure-services-dns-zone-configuration).

Für eine korrekte Konfiguration benötigen Sie die folgenden Ressourcen:

- Lokales Netzwerk
- Virtuelles Netzwerk [mit Verbindung mit lokalem Standort](/azure/architecture/reference-architectures/hybrid-networking/)
- [Virtuelles Netzwerk mit Peering](../virtual-network/virtual-network-peering-overview.md) 
- In Azure bereitgestellte DNS-Weiterleitung
- Zonen im privaten DNS [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) mit [A-Datensatz](../dns/dns-zones-records.md#record-types)
- Informationen zum privaten Endpunkt (Name des FQDN-Eintrags und private IP-Adresse)

Das folgende Diagramm veranschaulicht die DNS-Auflösung für beide Netzwerke, d. h. das lokale und das virtuelle Netzwerk. Für die Auflösung wird eine DNS-Weiterleitung verwendet. Die Auflösung erfolgt durch eine private DNS-Zone, die [mit einem virtuellen Netzwerk verknüpft](../dns/private-dns-virtual-network-links.md) ist:

:::image type="content" source="media/private-endpoint-dns/hybrid-scenario.png" alt-text="Hybridszenario":::

## <a name="next-steps"></a>Nächste Schritte
- [Weitere Informationen zu privaten Endpunkten](private-endpoint-overview.md)
