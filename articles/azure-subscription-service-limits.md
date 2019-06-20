---
title: Grenzwerte und Kontingente von Azure-Abonnements
description: Stellt eine Liste allgemeiner Azure-Abonnements und Diensteinschränkungen, Kontingenten und Einschränkungen bereit. Dieser Artikel enthält Informationen zum Erhöhen von Grenzwerten und Höchstwerten.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.topic: article
ms.date: 05/30/2019
ms.author: byvinyal
ms.openlocfilehash: 88c40021ff2e974ba16b3b3506ee54b45de90fda
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427595"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen
In diesem Dokument sind einige der gängigsten Einschränkungen in Microsoft Azure aufgeführt, die bisweilen auch als „Kontingente“ bezeichnet werden. Dieses Dokument behandelt derzeit nicht alle Azure-Dienste. Mit der Zeit wird diese Liste erweitert, um mehr Dienste abzudecken.

Weitere Informationen zu den Azure-Preisen finden Sie in der [Azure-Preisübersicht](https://azure.microsoft.com/pricing/). Dort können Sie Ihre Kosten mit dem [Preisrechner](https://azure.microsoft.com/pricing/calculator/) schätzen. Sie können auch zur Seite mit Preisen für einen bestimmten Dienst wie z.B. [Windows-VMs](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) wechseln. Tipps zum Verwalten Ihrer Kosten finden Sie unter [Vermeiden unerwarteter Kosten bei der Azure-Abrechnung und -Kostenverwaltung](billing/billing-getting-started.md).

> [!NOTE]
> Wenn Sie einen Grenzwert oder ein Kontingent über den Standardgrenzwert anheben möchten, können Sie eine [gebührenfreie Onlinekundensupport-Anforderung öffnen](azure-resource-manager/resource-manager-quota-errors.md). Die Grenzwerte können nicht über den maximalen Grenzwert in den folgenden Tabellen angehoben werden. Falls die Spalte für den maximalen Grenzwert nicht vorhanden ist, gibt es für die entsprechende Ressource keine anpassbaren Grenzwerte.
>
> Bei [Abonnements mit einer kostenlosen Testversion](https://azure.microsoft.com/offers/ms-azr-0044p) sind Grenzwert- oder Kontingenterhöhungen nicht möglich. Wenn Sie über ein [Abonnement mit einer kostenlosen Testversion](https://azure.microsoft.com/offers/ms-azr-0044p) verfügen, können Sie ein Upgrade auf ein Abonnement mit [nutzungsbasierter Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/) durchführen. Weitere Informationen finden Sie unter [Upgrade your Azure Free Trial subscription to a Pay-As-You-Go subscription](billing/billing-upgrade-azure-subscription.md) (Upgrade Ihres Abonnements mit kostenloser Azure-Testversion auf nutzungsbasierte Bezahlung) und [FAQ zum kostenlosen Azure-Konto](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-azure-resource-manager"></a>Grenzwerte und der Azure Resource Manager
Es ist jetzt möglich, mehrere Azure-Ressourcen in einer einzigen Azure-Ressourcengruppe zu kombinieren. Bei der Verwendung von Ressourcengruppen werden Grenzwerte, die bisher global waren, auf einer regionalen Ebene mit dem Azure Resource Manager verwaltet. Weitere Informationen zu Azure-Ressourcengruppen finden Sie unter [Übersicht über den Azure Resource Manager](azure-resource-manager/resource-group-overview.md).

In der folgenden Liste mit Grenzwerten zeigt eine neue Tabelle die abweichenden Grenzwerte bei der Verwendung des Azure Resource Manager an. Es gibt beispielsweise eine Tabelle für **Abonnementgrenzwerte** und eine Tabelle für **Abonnementgrenzwerte – Azure Resource Manager**. Wenn ein Grenzwert für beide Szenarios gilt, wird er nur in der ersten Tabelle angezeigt. Sofern nicht anders angegeben, gelten Grenzwerte global für alle Regionen.

> [!NOTE]
> Der Zugriff Ihres Abonnements auf Kontingente für Ressourcen in Azure-Ressourcengruppen erfolgt pro Region und nicht pro Abonnement wie bei Dienstverwaltungskontingenten. Verwenden wir vCPU-Kontingente als Beispiel. Sie müssen festlegen, wie viele vCPUs Sie in welchen Regionen verwenden möchten, um eine Kontingenterhöhung mit vCPU-Unterstützung anzufordern. Dann fordern Sie explizit vCPU-Kontingente für Azure-Ressourcengruppen in der benötigten Menge für die gewünschten Regionen an. Wenn Sie für die Ausführung Ihrer Anwendung 30 vCPUs in „Europa, Westen“ benötigen, fordern Sie 30 vCPUs in „Europa, Westen“ an. Ihr vCPU-Kontingent erhöht sich in anderen Regionen nicht. Das Kontingent von 30 vCPUs gilt nur für „Europa, Westen“.
> <!-- -->
> Deshalb sollten Sie entscheiden, welche Kontingente für Azure-Ressourcengruppen Sie für Ihre Workload in der jeweiligen Region benötigen. Fordern Sie dann diese Anzahl in jeder Region an, in der Sie bereitstellen möchten. Informationen zum Ermitteln Ihrer aktuellen Kontingente für bestimmte Regionen finden Sie unter [Behandeln von Bereitstellungsfehlern](resource-manager-common-deployment-errors.md).
>
>

## <a name="service-specific-limits"></a>Dienstspezifische Grenzwerte
* [Active Directory](#active-directory-limits)
* [API Management](#api-management-limits)
* [App Service](#app-service-limits)
* [Application Gateway](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [Automatisierung](#automation-limits)
* [Azure Cache for Redis](#azure-cache-for-redis-limits)
* [Azure Cloud Services](#azure-cloud-services-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Azure Database for MySQL](#azure-database-for-mysql)
* [Azure-Datenbank für PostgreSQL](#azure-database-for-postgresql)
* [Azure DNS](#azure-dns-limits)
* [Azure Firewall](#azure-firewall-limits)
* [Azure-Funktionen](#functions-limits)
* [Azure Kubernetes Service](#azure-kubernetes-service-limits)
* [Azure Machine Learning Service](#azure-machine-learning-service-limits)
* [Azure Maps](#azure-maps-limits)
* [Azure Monitor](#monitor-limits)
* [Azure Policy](#azure-policy-limits)
* [Azure Search](#azure-search-limits)
* [Azure SignalR Service](#azure-signalr-service-limits)
* [Sicherung](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [Containerinstanzen](#container-instances-limits)
* [Containerregistrierung](#container-registry-limits)
* [Content Delivery Network](#content-delivery-network-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Database Migration Service](#database-migration-service-limits)
* [Event Grid](#event-grid-limits)
* [Event Hubs](#event-hubs-limits)
* [Front Door Service](#azure-front-door-service-limits)
* [Identity Manager](#identity-manager-limits)
* [IoT Hub](#iot-hub-limits)
* [IoT Hub Device Provisioning-Dienst](#iot-hub-device-provisioning-service-limits)
* [Schlüsseltresor](#key-vault-limits)
* [Log Analytics](#log-analytics-limits)
* [Media Services](#media-services-limits)
* [Mobile Services](#mobile-services-limits)
* [Multi-Factor Authentication](#multi-factor-authentication-limits)
* [Netzwerk](#networking-limits)
  * [Application Gateway](#application-gateway-limits)
  * [Azure DNS](#azure-dns-limits)
  * [Azure Front Door Service](#azure-front-door-service-limits)
  * [Azure Firewall](#azure-firewall-limits)
  * [ExpressRoute](#expressroute-limits)
  * [Load Balancer](#load-balancer)
  * [Öffentliche IP-Adresse](#publicip-address)
  * [Network Watcher](#network-watcher-limits)
  * [Traffic Manager](#traffic-manager-limits)
  * [Virtual Network](#networking-limits)
* [Notification Hubs](#notification-hubs-limits)
* [Ressourcengruppe](#resource-group-limits)
* [Rollenbasierte Zugriffssteuerung](#role-based-access-control-limits)
* [Scheduler](#scheduler-limits)
* [Service Bus](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [SQL-Datenbank](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Speicher](#storage-limits)
* [StorSimple-System](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [Abonnement](#subscription-limits)
* [Virtuelle Computer](#virtual-machines-limits)
* [Skalierungsgruppen für virtuelle Computer](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Grenzwerte für Abonnements
#### <a name="subscription-limits---azure-service-management-classic-deployment-model"></a>Abonnementgrenzwerte – Azure-Dienstverwaltung (klassisches Bereitstellungsmodell)
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Abonnementgrenzwerte – Azure Resource Manager
Die folgenden Grenzwerte gelten bei Verwendung des Azure Resource Manager und von Azure-Ressourcengruppen. Grenzwerte, die sich durch den Azure Resource Manager nicht geändert haben, sind nicht aufgeführt. Diese Grenzwerte finden Sie in der vorherigen Tabelle.

Informationen zum Umgang mit Grenzwerten für Lese- und Schreibvorgänge bei der Resource Manager-API finden Sie unter [Begrenzen von Resource Manager-Anforderungen](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Grenzwerte für Ressourcengruppen
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Grenzwerte für virtuelle Computer
#### <a name="virtual-machines-limits"></a>Grenzwerte für virtuelle Computer
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Grenzwerte für virtuelle Computer – Azure-Ressourcen-Manager
Die folgenden Grenzwerte gelten bei Verwendung des Azure Resource Manager und von Azure-Ressourcengruppen. Grenzwerte, die sich durch den Azure Resource Manager nicht geändert haben, sind nicht aufgeführt. Diese Grenzwerte finden Sie in der vorherigen Tabelle.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

#### <a name="shared-image-gallery-limits"></a>Einschränkungen des Katalogs mit geteilten Images

Pro Abonnement gibt es Einschränkungen hinsichtlich der Bereitstellung von Ressourcen mithilfe des Katalogs mit geteilten Images:
- 100 Kataloge mit freigegebenen Images pro Abonnement und Region
- 1.000 Imagedefinitionen pro Abonnement und Region
- 10.000 Imageversionen pro Abonnement und Region

### <a name="virtual-machine-scale-sets-limits"></a>Grenzwerte für VM-Skalierungsgruppen
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Grenzwerte für Container Instances
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Grenzwerte für die Containerregistrierung
In der folgenden Tabelle werden die Features und Grenzwerte der [Dienstebenen](./container-registry/container-registry-skus.md) „Basic“, „Standard“ und „Premium“ dargestellt.

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="azure-kubernetes-service-limits"></a>Grenzwerte für Azure Kubernetes Service
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="azure-machine-learning-service-limits"></a>Grenzwerte des Azure Machine Learning-Diensts
Die aktuellen Werte für Azure Machine Learning Compute-Kontingente finden Sie auf der [Seite „Azure Machine Learning-Kontingent“](../articles/machine-learning/service/how-to-manage-quotas.md)

### <a name="networking-limits"></a>Grenzwerte für Netzwerke
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="expressroute-limits"></a>Einschränkungen für ExpressRoute
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="application-gateway-limits"></a>Application Gateway-Grenzwerte

Die folgende Tabelle gilt für v1, v2, Standard und WAF SKUs, sofern nichts anderes angegeben ist.
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Network Watcher-Grenzwerte
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>Traffic Manager-Grenzwerte
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="azure-dns-limits"></a>Grenzwerte für Azure DNS
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Grenzwerte für Azure Firewall
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Grenzwerte von Azure Front Door Service
[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>Speichergrenzwerte
<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

Weitere Informationen zu Speicherkontogrenzwerten finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Storage](storage/common/storage-scalability-targets.md).

#### <a name="storage-resource-provider-limits"></a>Grenzwerte für Speicherressourcenanbieter 

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Limits bei Azure Blob Storage
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Azure Files-Limits
Weitere Informationen zu Grenzwerten für Azure Files finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Files](storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Grenzwerte der Azure-Dateisynchronisierung
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Azure Queue-Speicherlimits
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Azure Table-Speicherlimits
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Grenzwerte für Datenträger virtueller Computer
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Weitere Informationen finden Sie unter [Größen virtueller Computer](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="managed-virtual-machine-disks"></a>Verwaltete VM-Datenträger

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Nicht verwaltete VM-Datenträger

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="azure-cloud-services-limits"></a>Grenzwerte für Azure Cloud Services
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>App Service-Grenzwerte
Die folgenden App Service-Grenzwerte umfassen Grenzwerte für Web-Apps, Mobile Apps und API-Apps.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="functions-limits"></a>Functions-Grenzwerte
[!INCLUDE [functions-limits](../includes/functions-limits.md)]

### <a name="scheduler-limits"></a>Scheduler-Grenzwerte
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Batch-Grenzwerte
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>BizTalk Services-Grenzwerte
In der folgenden Tabelle werden die Grenzwerte für Azure BizTalk Services aufgeführt.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Einschränkungen für Azure Cosmos DB
Azure Cosmos DB-Grenzwerte finden Sie unter [Grenzwerte in Azure Cosmos DB](cosmos-db/concepts-limits.md).

### <a name="azure-database-for-mysql"></a>Azure Database for MySQL
Informationen zu Grenzwerten für Azure Database for MySQL finden Sie unter [Beschränkungen in Azure Database for MySQL](mysql/concepts-limits.md).

### <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
Informationen zu Grenzwerten für Azure Database for PostgreSQL finden Sie unter [Beschränkungen in Azure Database for PostgreSQL](postgresql/concepts-limits.md).

### <a name="azure-search-limits"></a>Grenzwerte für Azure Search
Die Tarife bestimmen die Kapazität und die Beschränkungen des Suchdiensts. Folgende Tarife sind verfügbar:

* **Free** ist ein mehrinstanzenfähiger Dienst, der mit anderen Azure-Abonnenten gemeinsam genutzt wird und für Bewertung sowie kleine Entwicklungsprojekte vorgesehen ist.
* **Basic** bietet dedizierte Computeressourcen für Produktionsworkloads mit geringerem Umfang, mit bis zu drei Replikaten für Abfrageworkloads mit hoher Verfügbarkeit.
* **Standard**, darunter S1, S2, S3 und S3 High Density, ist für größere Produktionsworkloads vorgesehen. Im Standard-Tarif sind mehrere Ebenen vorhanden, damit Sie eine Ressourcenkonfiguration auswählen können, die Ihrem Workloadprofil am besten entspricht.

**Grenzwerte pro Abonnement**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Grenzwerte pro Suchdienst**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Detailliertere Informationen zu Grenzwerten wie etwa für Dokumentgröße, Abfragen pro Sekunde, Schlüssel, Anforderungen und Antworten, finden Sie unter [Grenzwerte für den Azure Search-Dienst](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Media Services-Grenzwerte
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="content-delivery-network-limits"></a>Grenzwerte für Content Delivery Network
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Mobile Services-Grenzwerte
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitor-limits"></a>Grenzwerte für Monitor
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hubs-limits"></a>Grenzwerte für Notification Hubs
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Event Hubs-Grenzwerte
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Service Bus-Grenzwerte
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>IoT Hub-Grenzwerte
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>Grenzwerte für den IoT Hub Device Provisioning-Dienst
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Data Factory-Grenzwerte
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Grenzwerte für Data Lake Analytics
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Grenzwerte für Data Lake Store
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Grenzwerte für Database Migration Service
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Stream Analytics-Grenzwerte
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Active Directory-Grenzwerte
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="event-grid-limits"></a>Grenzwerte für Event Grid
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Azure Maps-Grenzwerte
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="azure-policy-limits"></a>Azure Policy-Grenzwerte
[!INCLUDE [policy-limits](../includes/azure-policy-limits.md)]

### <a name="storsimple-system-limits"></a>StorSimple-Systemgrenzwerte
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="log-analytics-limits"></a>Log Analytics-Grenzwerte
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Backup-Grenzwerte
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="azure-signalr-service-limits"></a>Grenzwerte für Azure SignalR Service
[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>Site Recovery-Grenzwerte
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Application Insights-Grenzwerte
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>API Management-Grenzwerte
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Azure Cache for Redis-Grenzwerte
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Schlüsseltresor-Grenzwerte
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication-limits"></a>Grenzwerte für Multi-Factor Authentication
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Automatisierungsgrenzwerte
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="identity-manager-limits"></a>Grenzwerte für Identity Manager
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Grenzwerte für rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC)
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>Grenzwerte für SQL-Datenbanken
Weitere Informationen zu Grenzwerten für SQL-Datenbank finden Sie unter [SQL Database resource limits for single databases](sql-database/sql-database-vcore-resource-limits-single-databases.md) (Ressourcengrenzwerte für SQL-Datenbank für Singletons), [SQL Database resource limits for elastic pools and pooled databases](sql-database/sql-database-vcore-resource-limits-elastic-pools.md) (Ressourcengrenzwerte für SQL-Datenbank für Pools für elastische Datenbanken und Pooldatenbanken) und [SQL Database resource limits for managed instances](sql-database/sql-database-managed-instance-resource-limits.md) (Ressourcengrenzwerte für SQL-Datenbank für verwaltete Instanzen).

### <a name="sql-data-warehouse-limits"></a>Einschränkungen zu SQL Data Warehouse
Informationen zu Grenzwerten für SQL Data Warehouse finden Sie unter [SQL Data Warehouse resource limits](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md) (Ressourcengrenzwerte für SQL Data Warehouse).

## <a name="see-also"></a>Weitere Informationen
- [Understand Azure limits and increases](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) (Grundlegendes zu Grenzwerten und Erhöhungen für Azure)
- [Virtual machine and cloud service sizes for Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Größen virtueller Computer und Clouddienste für Azure)
- [Sizes for Azure Cloud Services](cloud-services/cloud-services-sizes-specs.md) (Größen für Azure Cloud Services)
