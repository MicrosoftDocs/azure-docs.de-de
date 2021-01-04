---
title: Was ist privater Endpunkt in Azure?
description: Informationen zum privaten Endpunkt in Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: allensu
ms.openlocfilehash: 8021d659c144bfb68c2714f1680b6ad27a51b56a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522344"
---
# <a name="what-is-azure-private-endpoint"></a>Was ist privater Endpunkt in Azure?

Ein privater Endpunkt in Azure ist eine Netzwerkschnittstelle, die Sie privat und sicher mit einem von Azure Private Link betriebenen Dienst verbindet. Der private Endpunkt verwendet eine private IP-Adresse in Ihrem VNet und bindet den Dienst effektiv in Ihr VNet ein. Dabei kann es sich um einen Azure-Dienst wie z. B. Azure Storage, Azure Cosmos DB, SQL usw. oder Ihren eigenen [Private Link-Dienst](private-link-service-overview.md) handeln.
  
## <a name="private-endpoint-properties"></a>Eigenschaften eines privaten Endpunkts 
 Für einen privaten Endpunkt werden die folgenden Eigenschaften angegeben: 


|Eigenschaft  |BESCHREIBUNG |
|---------|---------|
|Name    |    Ein eindeutiger Name innerhalb der Ressourcengruppe.      |
|Subnet    |  Das Subnetz, dem private IP-Adressen aus einem virtuellen Netzwerk bereitgestellt und zugeordnet werden. Anforderungen an das Subnetz finden Sie im Abschnitt „Einschränkungen“ in diesem Artikel.         |
|Private Link-Ressource    |   Die Private Link-Ressource, mit der anhand der Liste verfügbarer Typen eine Verbindung über die Ressourcen-ID oder den Alias hergestellt werden soll. Für den gesamten Datenverkehr an diese Ressource wird ein eindeutiger Netzwerkbezeichner generiert.       |
|Unterressource des Ziels   |      Die Unterressource, mit der eine Verbindung hergestellt wird. Jeder Private Link-Ressourcentyp verfügt über verschiedene Optionen, die je nach Präferenz ausgewählt werden können.    |
|Methode zur Genehmigung der Verbindung    |  Automatisch oder manuell. Je nach Berechtigungen gemäß rollenbasierter Zugriffssteuerung (Azure RBAC) kann Ihr privater Endpunkt automatisch genehmigt werden. Wenn Sie versuchen, sich mit einer Private Link-Ressource ohne Azure RBAC zu verbinden, verwenden Sie die manuelle Methode, um dem Besitzer der Ressource zu ermöglichen, die Verbindung zu genehmigen.        |
|Anforderungsnachricht     |  Sie können eine Nachricht für angeforderte Verbindungen angeben, die manuell genehmigt werden sollen. Mithilfe dieser Nachricht kann eine bestimmte Anforderung identifiziert werden.        |
|Verbindungsstatus   |   Eine schreibgeschützte Eigenschaft, die angibt, ob der private Endpunkt aktiv ist. Nur private Endpunkte in genehmigtem Zustand können zum Senden von Datenverkehr verwendet werden. Weitere verfügbare Zustände: <br>-**Genehmigt**: Die Verbindung wurde automatisch oder manuell genehmigt und ist zur Verwendung bereit.</br><br>-**Ausstehend**: Die Verbindung wurde manuell erstellt, wobei die Genehmigung des Besitzers der Private Link-Ressource aussteht.</br><br>-**Abgelehnt**: Die Verbindung wurde vom Besitzer der Private Link-Ressource abgelehnt.</br><br>-**Getrennt**: Die Verbindung wurde vom Besitzer der Private Link-Ressource entfernt. Der private Endpunkt dient nur noch Informationszwecken und sollte zur Bereinigung gelöscht werden. </br>|

Im Folgenden finden Sie einige wichtige Details zu privaten Endpunkten: 
- Ein privater Endpunkt ermöglicht die Verbindung zwischen den Consumern im selben VNet, in regionalen VNets mit Peering, globalen VNets mit Peering und lokalen Standorten mit [VPN](https://azure.microsoft.com/services/vpn-gateway/) oder [Express Route](https://azure.microsoft.com/services/expressroute/) und Diensten auf Basis von Private Link.
 
- Netzwerkverbindungen können nur von Clients initiiert werden, die eine Verbindung mit dem privaten Endpunkt herstellen. Dienstanbieter verfügen nicht über eine Routingkonfiguration, die Verbindungen mit Dienstconsumern initiieren kann. Verbindungen können nur in einer Richtung eingerichtet werden.

- Beim Erstellen eines privaten Endpunkts wird für die Lebensdauer der Ressource auch eine schreibgeschützte Netzwerkschnittstelle eingerichtet. Der Schnittstelle werden dynamisch private IP-Adressen aus dem Subnetz zugewiesen, das der Private Link-Ressource zugeordnet ist. Der Wert der privaten IP-Adresse bleibt über den gesamten Lebenszyklus des privaten Endpunkts unverändert.
 
- Der private Endpunkt muss in derselben Region wie das virtuelle Netzwerk bereitgestellt werden. 
 
- Die Private Link-Ressource kann in einer anderen Region als das virtuelle Netzwerk und der private Endpunkt bereitgestellt werden.
 
- Mehrere private Endpunkte können mithilfe derselben Private Link-Ressource erstellt werden. Für ein einzelnes Netzwerk mit einer herkömmlichen DNS-Serverkonfiguration wird empfohlen, einen einzigen privaten Endpunkt für eine bestimmte Private Link-Ressource zu verwenden, um doppelte Einträge oder Konflikte bei der DNS-Auflösung zu vermeiden. 
 
- Mehrere private Endpunkte können im gleichen oder in verschiedenen Subnetzen innerhalb desselben virtuellen Netzwerks erstellt werden. Die Anzahl der privaten Endpunkte, die Sie in einem Abonnement anlegen können, ist begrenzt. Ausführliche Informationen finden Sie im Artikel zu  [Azure-Grenzwerten](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

- Das Abonnement der Private Link-Ressource muss ebenfalls beim Ressourcenanbieter „Micosoft.Network“ registriert werden. Weitere Informationen finden Sie unter  [Azure-Ressourcenanbieter](../azure-resource-manager/management/resource-providers-and-types.md).

 
## <a name="private-link-resource"></a>Private Link-Ressource 
Eine Private Link-Ressource ist das Ziel eines bestimmten privaten Endpunkts. Es folgt eine Liste der verfügbaren Private Link-Ressourcentypen: 
 
|Name der Private Link-Ressource  |Ressourcentyp   |Unterressourcen  |
|---------|---------|---------|
|**Private Link-Dienst** (Ihr eigener Dienst)   |  Microsoft.Network/privateLinkServices       | empty |
|**Azure Automation** |  Microsoft.Automation/automationAccounts | Webhook, DSCAndHybridWorker |
|**Azure SQL-Datenbank** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        | 
|**Azure Storage (in englischer Sprache)**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Tabelle (table, table_secondary)<BR> Warteschlange (queue, queue_secondary)<BR> Datei (file, file_secondary)<BR> Web (web, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Data Lake-Dateisystem Gen2 (dfs, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts    | Sql, MongoDB, Cassandra, Gremlin, Table|
|**Azure Database for PostgreSQL – Einzelserver** | Microsoft.DBforPostgreSQL/servers    | postgresqlServer |
|**Azure Database for MySQL** | Microsoft.DBforMySQL/servers    | mysqlServer |
|**Azure Database for MariaDB** | Microsoft.DBforMariaDB/servers    | mariadbServer |
|**Azure IoT Hub** | Microsoft.Devices/IotHubs    | iotHub |
|**Azure Key Vault** | Microsoft.KeyVault/vaults    | Tresor |
|**Azure Kubernetes Service: Kubernetes-API** | Microsoft.ContainerService/managedClusters    | management |
|**Azure Search** | Microsoft.Search/searchService| searchService|  
|**Azure Container Registry** | Microsoft.ContainerRegistry/registries    | Registrierung |
|**Azure App Configuration** | Microsoft.Appconfiguration/configurationStores    | configurationStores |
|**Azure Backup** | Microsoft.RecoveryServices/vaults    | Tresor |
|**Azure Event Hub** | Microsoft.EventHub/namespaces    | Namespace |
|**Azure Service Bus** | Microsoft.ServiceBus/namespaces | Namespace |
|**Azure Relay** | Microsoft.Relay/namespaces | Namespace |
|**Azure Event Grid** | Microsoft.EventGrid/topics    | topic |
|**Azure Event Grid** | Microsoft.EventGrid/domains    | Domäne |
|**Azure App Service** | Microsoft.Web/sites    | sites |
|**Azure Machine Learning** | Microsoft.MachineLearningServices/workspaces    | Arbeitsbereich |
|**SignalR** | Microsoft.SignalRService/SignalR    | signalR |
|**Azure Monitor** | Microsoft.Insights/privateLinkScopes    | azuremonitor |
|**Cognitive Services** | (Microsoft.CognitiveServices/accounts    | account |
|**Azure-Dateisynchronisierung** | Microsoft.StorageSync/storageSyncServices    | Afs |
    
  

  
 
## <a name="network-security-of-private-endpoints"></a>Netzwerksicherheit privater Endpunkte 
Bei Verwenden privater Endpunkte für Azure-Dienste wird der Datenverkehr zu einer bestimmten Private Link-Ressource abgesichert. Die Plattform führt eine Zugriffssteuerung durch, um zu bestätigen, dass Netzwerkverbindungen nur die angegebene Private Link-Ressource erreichen. Um auf zusätzliche Ressourcen innerhalb desselben Azure-Diensts zuzugreifen, sind zusätzliche private Endpunkte erforderlich. 
 
Sie können Ihre Workloads vollständig vom Zugriff auf öffentliche Endpunkte ausschließen, um sich mit einem unterstützten Azure-Dienst zu verbinden. Dieses Steuerungsinstrument bietet Ihren Ressourcen eine zusätzliche Netzwerksicherheitsebene, indem es einen integrierten Exfiltrationsschutz bereitstellt, der den Zugriff auf andere Ressourcen verhindert, die im gleichen Azure-Dienst gehostet werden. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Zugriff auf eine Private Link-Ressource mithilfe des Genehmigungsworkflows 
Sie können sich mit einer Private Link-Ressource mit den folgenden Methoden zur Genehmigung von Verbindungen verbinden:
- **Automatische** Genehmigung, wenn Sie die spezifische private Link-Ressource besitzen oder über eine Berechtigung verfügen. Die erforderliche Berechtigung basiert auf dem Private Link-Ressourcentyp im folgenden Format: Microsoft.\<Provider>/<Ressourcentyp>/privateEndpointConnectionApproval/action
- **Manuelle** Anforderung, wenn Sie nicht über die erforderliche Berechtigung verfügen und den Zugriff anfordern möchten. Ein Genehmigungsworkflow wird ausgelöst. Der private Endpunkt und die anschließende Verbindung mit dem privaten Endpunkt werden im Zustand „Ausstehend“ erstellt. Der Besitzer der Private Link-Ressource ist für die Genehmigung der Verbindung verantwortlich. Nach der Genehmigung ist der private Endpunkt in der Lage, Datenverkehr normal zu senden (siehe das folgende Diagramm des Genehmigungsworkflows).  

![Genehmigungsworkflow](media/private-endpoint-overview/private-link-paas-workflow.png)
 
Der Besitzer der Private Link-Ressource kann über eine private Endpunktverbindung die folgenden Aktionen ausführen: 
- Überprüfen aller Details von Verbindungen mit privaten Endpunkten. 
- Genehmigen einer Verbindung mit einem privaten Endpunkt. Der entsprechende private Endpunkt wird für das Senden von Datenverkehr an die Private Link-Ressource aktiviert. 
- Ablehnen einer Verbindung mit einem privaten Endpunkt. Der entsprechende private Endpunkt wird aktualisiert, um den Status widerzuspiegeln.
- Löschen einer Verbindung mit einem privaten Endpunkt in beliebigem Zustand. Der entsprechende private Endpunkt wird mit getrenntem Zustand aktualisiert, um die Aktion widerzuspiegeln. Nur der Besitzer des privaten Endpunkts kann die Ressource an dieser Stelle löschen. 
 
> [!NOTE]
> Nur ein privater Endpunkt im genehmigten Zustand kann Datenverkehr an eine angegebene Private Link-Ressource senden. 

### <a name="connecting-using-alias"></a>Verbindungsherstellung per Alias
Der Alias ist ein eindeutiger Moniker, der generiert wird, wenn der Dienstbesitzer den Private Link-Dienst hinter einem Standardlastenausgleich erstellt. Der Dienstbesitzer kann diesen Alias mit seinen Consumern offline gemeinsam nutzen. Verbraucher können eine Verbindung mit dem Private Link-Dienst anfordern, indem sie entweder den Ressourcen-URI oder Alias verwenden. Wenn Sie eine Verbindung per Alias herstellen möchten, müssen Sie einen privaten Endpunkt mithilfe der manuellen Verbindungsgenehmigungsmethode erstellen. Um die manuelle Verbindungsgenehmigungsmethode zu verwenden, legen Sie den Parameter für die manuelle Anforderung während des Flows zum Erstellen eines privaten Endpunkts auf TRUE fest. Ausführliche Informationen finden Sie unter [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) und [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create). 

## <a name="dns-configuration"></a>DNS-Konfiguration 
Wenn Sie eine Verbindung mit einer Private Link-Ressource über einen voll qualifizierten Domänennamen (FQDN) als Teil der Verbindungszeichenfolge herstellen, ist es wichtig, Ihre DNS-Einstellungen so zu konfigurieren, dass sie in die zugewiesene private IP-Adresse aufgelöst werden. Bestehende Azure-Dienste verfügen möglicherweise bereits über eine DNS-Konfiguration, die beim Herstellen einer Verbindung über einen öffentlichen Endpunkt verwendet werden kann. Diese muss überschrieben werden, um eine Verbindung mithilfe Ihres privaten Endpunkts herzustellen. 
 
Die Netzwerkschnittstelle, die dem privaten Endpunkt zugeordnet ist, enthält alle Informationen, die zur Konfiguration Ihres DNS erforderlich sind, einschließlich FQDN und privater IP-Adressen, die einer bestimmten Private Link-Ressource zugewiesen wurden. 

Ausführliche Informationen zu bewährten Methoden und Empfehlungen finden Sie im Artikel [DNS-Konfiguration für private Azure-Endpunkte](private-endpoint-dns.md).



 
## <a name="limitations"></a>Einschränkungen
 
Die folgende Tabelle enthält eine Liste der bekannten Einschränkungen bei Verwendung privater Endpunkte: 


|Einschränkung |BESCHREIBUNG |Minderung  |
|---------|---------|---------|
|Regeln für Netzwerksicherheitsgruppen (NSGs) und benutzerdefinierte Routen gelten nicht für den privaten Endpunkt    |NSGs werden für private Endpunkte nicht unterstützt. Während Subnetzen, die den privaten Endpunkt enthalten, eine NSG zugeordnet sein kann, gelten die Regeln nicht für den vom privaten Endpunkt verarbeiteten Datenverkehr. Sie müssen die [Durchsetzung von Netzwerkrichtlinien deaktivieren](disable-private-endpoint-network-policy.md), um private Endpunkte in einem Subnetz bereitzustellen zu können. Die NSG wird weiterhin für andere Workloads erzwungen, die im selben Subnetz gehostet werden. Für Routen in einem beliebigen Clientsubnetz wird ein /32-Präfix verwendet. Zum Ändern des Standardverhaltens für das Routing ist eine ähnliche benutzerdefinierte Route erforderlich.  | Steuern Sie den Datenverkehr, indem Sie auf Quellclients NSG-Regeln für ausgehenden Datenverkehr verwenden. Stellen Sie einzelne Routen mit /32-Präfix zum Außerkraftsetzen von Routen privater Endpunkte bereit. NSG-Datenflussprotokolle und Überwachungsinformationen für ausgehende Verbindungen werden weiterhin unterstützt und können verwendet werden.        |


## <a name="next-steps"></a>Nächste Schritte
- [Erstellen eines privaten Endpunkts für Azure SQL-Datenbank im Portal](create-private-endpoint-portal.md)
- [Erstellen eines privaten Endpunkts für Azure SQL-Datenbank mit PowerShell](create-private-endpoint-powershell.md)
- [Erstellen eines privaten Endpunkts für Azure SQL-Datenbank mit der CLI](create-private-endpoint-cli.md)
- [Erstellen eines privaten Endpunkts für das Speicherkonto im Portal](./tutorial-private-endpoint-storage-portal.md)
- [Erstellen eines privaten Endpunkts für das Azure Cosmos-Konto im Portal](../cosmos-db/how-to-configure-private-endpoints.md)
- [Erstellen eines eigenen Private Link-Diensts mit Azure PowerShell](create-private-link-service-powershell.md)
- [Erstellen eines eigenen Private Link für Azure Database for PostgreSQL – Einzelserver im Portal](../postgresql/howto-configure-privatelink-portal.md)
- [Erstellen eines eigenen Private Link für Azure Database for PostgreSQL – Einzelserver über die Befehlszeilenschnittstelle](../postgresql/howto-configure-privatelink-cli.md)
- [Erstellen eines eigenen Private Link für Azure Database for MySQL im Portal](../mysql/howto-configure-privatelink-portal.md)
- [Erstellen eines eigenen Private Link für Azure Database for MySQL über die Befehlszeilenschnittstelle](../mysql/howto-configure-privatelink-cli.md)
- [Erstellen eines eigenen Private Link für Azure Database for MariaDB im Portal](../mariadb/howto-configure-privatelink-portal.md)
- [Erstellen eines eigenen Private Link für Azure Database for MariaDB über die Befehlszeilenschnittstelle](../mariadb/howto-configure-privatelink-cli.md)
- [Erstellen eines eigenen Private Link für Azure Key Vault im Portal und mit der CLI](../key-vault/general/private-link-service.md)