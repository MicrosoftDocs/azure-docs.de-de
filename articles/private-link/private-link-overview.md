---
title: Was ist Azure Private Link?
description: Übersicht über Features, Architektur und Implementierung von Azure Private Link. Es wird beschrieben, wie private Azure-Endpunkte und der Dienst „Azure Private Link“ funktionieren und wie Sie diese Features nutzen.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 01/28/2021
ms.author: allensu
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 34223298dbf0dad9d9214676a5190bef063a8228
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577024"
---
# <a name="what-is-azure-private-link"></a>Was ist Azure Private Link? 
Mit Azure Private Link können Sie über einen [privaten Endpunkt](private-endpoint-overview.md) in Ihrem virtuellen Netzwerk auf Azure-PaaS-Dienste (beispielsweise Azure Storage und SQL Database) sowie auf in Azure gehostete kundeneigene Dienste/Partnerdienste zugreifen.

Der Datenverkehr zwischen Ihrem virtuellen Netzwerk und dem Dienst verläuft über das Microsoft-Backbone-Netzwerk. Es ist nicht mehr erforderlich, dass Sie Ihren Dienst über das öffentliche Internet verfügbar machen. Sie können Ihren eigenen [Private Link-Dienst](private-link-service-overview.md) in Ihrem virtuellen Netzwerk erstellen und Ihren Kunden zur Verfügung stellen. Die Einrichtung und Nutzung von Azure Private Link ist in Azure PaaS-, Kunden- und gemeinsam genutzten Partnerdiensten einheitlich.

> [!IMPORTANT]
> Azure Private Link ist jetzt allgemein verfügbar. Sowohl der Dienst für private Endpunkte als auch der Dienst „Private Link“ (Dienst im Hintergrund von Load Balancer Standard) ist allgemein verfügbar. Für unterschiedliche Azure PaaS-Instanzen wird das Onboarding für Azure Private Link basierend auf verschiedenen Zeitplänen durchgeführt. Der Abschnitt [Verfügbarkeit](#availability) in diesem Artikel enthält Informationen zum genauen Status von Azure PaaS für Private Link. Weitere Informationen zu bekannten Einschränkungen finden Sie unter [Privater Endpunkt](private-endpoint-overview.md#limitations) und [Private Link-Dienst](private-link-service-overview.md#limitations). 

:::image type="content" source="./media/private-link-overview/private-link-center.png" alt-text="Azure Private Link Center im Azure-Portal" border="false":::

## <a name="key-benefits"></a>Hauptvorteile
Azure Private Link bietet folgende Vorteile:  
- **Private Zugriffsdienste auf der Azure-Plattform**: Verbinden Sie Ihr virtuelles Netzwerk mit Diensten in Azure ohne öffentliche IP-Adresse an der Quelle oder am Ziel. Dienstanbieter können ihre Dienste in ihrem eigenen virtuellen Netzwerk rendern, und Consumer können in ihrem lokalen virtuellen Netzwerk auf diese Dienste zugreifen. Die Private Link-Plattform verarbeitet die Konnektivität zwischen dem Consumer und den Diensten über das Azure-Backbone-Netzwerk. 
 
- **Lokale Netzwerke und Peernetzwerke**: Greifen Sie mithilfe privater Endpunkte von einer lokalen Umgebung über privates ExpressRoute-Peering, VPN-Tunnel und virtuelle Netzwerke mit Peering auf Dienste zu, die in Azure ausgeführt werden. Es ist nicht erforderlich, ExpressRoute-Microsoft-Peering einzurichten oder über das Internet auf den Dienst zuzugreifen. Private Link ist eine sichere Möglichkeit, um Workloads zu Azure zu migrieren.
 
- **Schutz vor Datenlecks**: Ein privater Endpunkt wird nicht dem gesamten Dienst, sondern der Instanz einer PaaS-Ressource zugeordnet. Consumer können nur eine Verbindung mit der entsprechenden Ressource herstellen. Der Zugriff auf alle anderen Ressourcen des Diensts ist blockiert. Dieser Mechanismus ermöglicht den Schutz vor Risiken aufgrund von Datenlecks. 
 
- **Globale Reichweite**: Stellen Sie private Verbindungen zu Diensten her, die in anderen Regionen ausgeführt werden. Das virtuelle Netzwerk des Consumers kann sich beispielsweise in Region A befinden und eine Verbindung mit Diensten hinter Private Link in Region B herstellen.  
 
- **Ausweiten auf Ihre eigenen Dienste**: Aktivieren Sie die gleichen Funktionen, um Ihren Dienst privat für Consumer in Azure zu rendern. Wenn Sie Ihren Dienst hinter einem Azure Load Balancer Standard platzieren, können Sie ihn für Private Link aktivieren. Der Consumer kann dann mithilfe eines privaten Endpunkts in seinem eigenen virtuellen Netzwerk eine direkte Verbindung mit dem Dienst herstellen. Die Verbindungsanforderungen können mithilfe eines Ablaufs für Genehmigungsaufrufe verwaltet werden. Azure Private Link funktioniert für Consumer und Dienste, die zu unterschiedlichen Azure Active Directory-Mandanten gehören. 

## <a name="availability"></a>Verfügbarkeit 
 In der folgenden Tabelle sind die Private Link-Dienste und die Regionen, in denen sie verfügbar sind, aufgelistet. 

|Unterstützte Dienste  |Verfügbare Regionen | Weitere Überlegungen | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Private Link-Dienste hinter Azure Load Balancer Standard | Alle öffentlichen Regionen<br/> Alle Government-Regionen<br/>Alle China-Regionen  | Wird von Load Balancer Standard unterstützt | Allgemein verfügbar <br/> [Erfahren Sie mehr über den Private Link-Dienst.](create-private-link-service-portal.md) |
| Azure Blob Storage (einschließlich Data Lake Storage Gen2)       |  Alle öffentlichen Regionen<br/> Alle Government-Regionen       |  Wird für die Kontoart „Universell V2“ unterstützt | Allgemein verfügbar <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Blobspeicher erstellen.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Files | Alle öffentlichen Regionen<br/> Alle Government-Regionen      | |   Allgemein verfügbar <br/> [Erfahren Sie, wie Azure Files-Netzwerkendpunkte erstellt werden.](../storage/files/storage-files-networking-endpoints.md)   |
| Azure-Dateisynchronisierung | Alle öffentlichen Regionen      | |   Allgemein verfügbar <br/> [Erfahren Sie, wie Azure Files-Netzwerkendpunkte erstellt werden.](../storage/files/storage-sync-files-networking-endpoints.md)   |
| Azure Queue Storage       |  Alle öffentlichen Regionen<br/> Alle Government-Regionen       |  Wird für die Kontoart „Universell V2“ unterstützt | Allgemein verfügbar <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Warteschlangenspeicher erstellen.](tutorial-private-endpoint-storage-portal.md) |
| Azure-Tabellenspeicher       |  Alle öffentlichen Regionen<br/> Alle Government-Regionen       |  Wird für die Kontoart „Universell V2“ unterstützt | Allgemein verfügbar <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Tabellenspeicher erstellen.](tutorial-private-endpoint-storage-portal.md)  |
|  Azure SQL-Datenbank         | Alle öffentlichen Regionen <br/> Alle Government-Regionen<br/>Alle China-Regionen      |  Wird für die Proxy-[Verbindungsrichtlinie](../azure-sql/database/connectivity-architecture.md#connection-policy) unterstützt | Allgemein verfügbar <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure SQL erstellen.](create-private-endpoint-portal.md)      |
|Azure Synapse Analytics| Alle öffentlichen Regionen <br/> Alle Government-Regionen |  Wird für die Proxy-[Verbindungsrichtlinie](../azure-sql/database/connectivity-architecture.md#connection-policy) unterstützt |Allgemein verfügbar <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Synapse Analytics erstellen.](../azure-sql/database/private-endpoint-overview.md)|
|Azure Cosmos DB|  Alle öffentlichen Regionen<br/> Alle Government-Regionen</br> Alle China-Regionen | |Allgemein verfügbar <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Cosmos DB erstellen.](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Azure Database for PostgreSQL (Einzelserver)         | Alle öffentlichen Regionen <br/> Alle Government-Regionen<br/>Alle China-Regionen     | Unterstützt für universelle und arbeitsspeicheroptimierte Tarife | Allgemein verfügbar <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Database for PostgreSQL erstellen.](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Azure Database for MySQL         | Alle öffentlichen Regionen<br/> Alle Government-Regionen<br/>Alle China-Regionen      |  | Allgemein verfügbar <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Database for MySQL erstellen.](../mysql/concepts-data-access-security-private-link.md)     |
|  Azure Database for MariaDB         | Alle öffentlichen Regionen<br/> Alle Government-Regionen<br/>Alle China-Regionen     |  | Allgemein verfügbar <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Database for MariaDB erstellen.](../mariadb/concepts-data-access-security-private-link.md)      |
|  Azure Digital Twins         | Alle von Azure Digital Twins unterstützten öffentlichen Regionen     |  | Vorschau <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Digital Twins erstellen.](../digital-twins/how-to-enable-private-link.md)      |
|  Azure-Schlüsseltresor         | Alle öffentlichen Regionen<br/> Alle Government-Regionen      |  | Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Key Vault erstellen.](../key-vault/general/private-link-service.md)   |
|Azure Kubernetes Service: Kubernetes-API | Alle öffentlichen Regionen      |  | Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Kubernetes Service erstellen.](../aks/private-clusters.md)   |
|Azure Search | Alle öffentlichen Regionen <br/> Alle Government-Regionen | Unterstützt mit Dienst im privaten Modus | Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Search erstellen.](../search/service-create-private-endpoint.md)    |
|Azure Container Registry | Alle öffentlichen Regionen<br/> Alle Government-Regionen    | Wird für den Premium-Tarif der Containerregistrierung unterstützt [Für Tarife auswählen](../container-registry/container-registry-skus.md)| Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Container Registry erstellen.](../container-registry/container-registry-private-link.md)   |
|Azure App Configuration | Alle öffentlichen Regionen      |  | Vorschau  </br> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure App Configuration erstellen.](../azure-app-configuration/concept-private-endpoint.md) |
|Azure Backup | Alle öffentlichen Regionen<br/> Alle Government-Regionen   |  | Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Backup erstellen.](../backup/private-endpoints.md)   |
|Azure Event Hub | Alle öffentlichen Regionen<br/>Alle Government-Regionen      |   | Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Event Hub erstellen.](../event-hubs/private-link-service.md)  |
|Azure Service Bus | Alle öffentlichen Regionen<br/>Alle Government-Regionen  | Wird für den Premium-Tarif von Azure Service Bus unterstützt [Für Tarife auswählen](../service-bus-messaging/service-bus-premium-messaging.md) | Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Service Bus erstellen.](../service-bus-messaging/private-link-service.md)    |
|Azure Relay | Alle öffentlichen Regionen      |  | Vorschau <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Relay erstellen.](../azure-relay/private-link-service.md)  |
|Azure Event Grid| Alle öffentlichen Regionen<br/> Alle Government-Regionen       |  | Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Event Grid erstellen.](../event-grid/network-security.md) |
|Azure-Web-Apps | Alle öffentlichen Regionen      | Unterstützt mit PremiumV2-, PremiumV3- oder Functions-Premium-Plan  | Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure-Web-Apps erstellen.](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure Machine Learning | Alle öffentlichen Regionen    |  | Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Machine Learning erstellen.](../machine-learning/how-to-configure-private-link.md)   |
| Azure-Automatisierung  | Alle öffentlichen Regionen<br/> Alle Government-Regionen |  | Vorschau </br> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Automation erstellen.](../automation/how-to/private-link-security.md)| |
| Azure IoT Hub | Alle öffentlichen Regionen    |  | Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure IoT Hub erstellen.](../iot-hub/virtual-network-support.md) |
| Azure SignalR | USA, OSTEN; USA, SÜDEN-MITTE;<br/>USA, WESTEN 2; Alle China-Regionen      |  | Vorschau   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure SignalR erstellen.](../azure-signalr/howto-private-endpoints.md)   |
| Azure Monitor <br/>(Log Analytics und Application Insights) | Alle öffentlichen Regionen      |  | Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Monitor erstellen.](../azure-monitor/logs/private-link-security.md)   | 
| Azure Batch | Alle öffentlichen Regionen, außer: Deutschland, MITTE, Deutschland, NORDOSTEN <br/> Alle Government-Regionen  | | Allgemein verfügbar <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Batch erstellen.](../batch/private-connectivity.md) |
|Azure Data Factory | Alle öffentlichen Regionen<br/> Alle Government-Regionen<br/>Alle China-Regionen    | Anmeldeinformationen müssen in einem Azure-Schlüsseltresor gespeichert werden.| Allgemein verfügbar   <br/> [Erfahren Sie, wie Sie einen privaten Endpunkt für Azure Data Factory erstellen.](../data-factory/data-factory-private-link.md)   |
|Azure Managed Disks | Alle öffentlichen Regionen<br/> Alle Government-Regionen<br/>Alle China-Regionen    | Informationen zu bekannten Einschränkungen finden Sie [hier](../virtual-machines/disks-enable-private-links-for-import-export-portal.md#limitations). | Allgemein verfügbar   <br/> Informationen zum Erstellen eines privaten Endpunkts für Azure Managed Disks finden Sie [hier](../virtual-machines/disks-enable-private-links-for-import-export-portal.md).   |



Aktuelle Benachrichtigungen finden Sie auf der Seite mit den [Azure Private Link-Updates](https://azure.microsoft.com/updates/?product=private-link).

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

Azure Private Link verfügt eine Integration in Azure Monitor. Diese Kombination ermöglicht Folgendes:

 - Archivierung von Protokollen in einem Speicherkonto
 - Streaming von Ereignissen an Ihren Event Hub
 - Azure Monitor-Protokollierung

Sie können über Azure Monitor auf die folgenden Informationen zugreifen: 
- **Privater Endpunkt**: 
    - Die vom privaten Endpunkt verarbeiteten Daten (ein-/ausgehend)
 
- **Private Link-Dienst**:
    - Die vom Private Link-Dienst verarbeiteten Daten (ein-/ausgehend)
    - NAT-Portverfügbarkeit  
 
## <a name="pricing"></a>Preise   
Ausführliche Preisinformationen finden Sie unter [Azure Private Link – Preise](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Häufig gestellte Fragen  
Häufig gestellte Fragen finden Sie unter [Häufig gestellte Fragen zu Azure Private Link](private-link-faq.md).
 
## <a name="limits"></a>Einschränkungen  
Informationen zu Einschränkungen finden Sie unter [Einschränkungen für Azure Private Link](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="service-level-agreement"></a>Vereinbarung zum Servicelevel
Informationen zur Vereinbarung zum Servicelevel (SLA) finden Sie unter [SLA für Azure Private Link](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/).

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstart: Erstellen eines privaten Endpunkts über das Azure-Portal](create-private-endpoint-portal.md)
- [Schnellstart: Erstellen eines Private Link-Diensts über das Azure-Portal](create-private-link-service-portal.md)