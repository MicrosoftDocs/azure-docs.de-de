---
title: Grenzwerte und Kontingente von Azure-Abonnements
description: Stellt eine Liste allgemeiner Azure-Abonnements und Diensteinschränkungen, Kontingenten und Einschränkungen bereit. Dieser Artikel enthält Informationen zum Erhöhen von Grenzwerten und Höchstwerten.
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 4d56c5c3fd33d1cda05e23d5bc53e7540428671b
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547847"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen

In diesem Dokument sind einige der gängigsten Einschränkungen in Microsoft Azure aufgeführt, die bisweilen auch als „Kontingente“ bezeichnet werden.

Weitere Informationen zu den Azure-Preisen finden Sie in der [Azure-Preisübersicht](https://azure.microsoft.com/pricing/). Dort können Sie Ihre Kosten mit dem [Preisrechner](https://azure.microsoft.com/pricing/calculator/) schätzen. Sie können auch zur Seite mit Preisen für einen bestimmten Dienst wie z.B. [Windows-VMs](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) wechseln. Tipps zum Verwalten Ihrer Kosten finden Sie unter [Vermeiden unerwarteter Kosten bei der Azure-Abrechnung und -Kostenverwaltung](../../cost-management-billing/cost-management-billing-overview.md).

## <a name="managing-limits"></a>Verwalten von Grenzwerten

> [!NOTE]
> Einige Dienste verfügen über anpassbare Grenzwerte.
>
> Wenn ein Dienst keine anpassbaren Grenzwerte aufweist, verwenden die folgenden Tabellen den Header **Limit**. In diesen Fällen sind die Standard- und die maximalen Grenzwerte identisch.
>
> Wenn der Grenzwert angepasst werden kann, enthalten die Tabellen die Header **Default limit** und **Maximum limit**. Der Grenzwert kann über den Standardgrenzwert, aber nicht über den maximalen Grenzwert angehoben werden.
>
> Wenn Sie einen Grenzwert oder ein Kontingent über den Standardgrenzwert anheben möchten, können Sie eine [gebührenfreie Onlinekundensupport-Anforderung öffnen](../templates/error-resource-quota.md).
>
> Die Begriffe *Soft Limit* *und Hard Limit* werden häufig informell verwendet, um den aktuellen, anpassbaren Grenzwert (Soft Limit) und den maximalen Grenzwert (Hard Limit) zu beschreiben. Wenn ein Grenzwert nicht anpassbar ist, gibt es keinen Soft Limit, nur einen Hard Limit.
>

Bei [Abonnements mit einer kostenlosen Testversion](https://azure.microsoft.com/offers/ms-azr-0044p) sind Grenzwert- oder Kontingenterhöhungen nicht möglich. Wenn Sie über ein [Abonnement mit einer kostenlosen Testversion](https://azure.microsoft.com/offers/ms-azr-0044p) verfügen, können Sie ein Upgrade auf ein Abonnement mit [nutzungsbasierter Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/) durchführen. Weitere Informationen finden Sie unter [Upgrade your Azure Free Trial subscription to a Pay-As-You-Go subscription](../../cost-management-billing/manage/upgrade-azure-subscription.md) (Upgrade Ihres Abonnements mit kostenloser Azure-Testversion auf nutzungsbasierte Bezahlung) und [FAQ zum kostenlosen Azure-Konto](https://azure.microsoft.com/free/free-account-faq).

Einige Grenzwerte werden auf Regionsebene verwaltet.

Verwenden wir vCPU-Kontingente als Beispiel. Sie müssen festlegen, wie viele vCPUs Sie in welchen Regionen verwenden möchten, um eine Kontingenterhöhung mit vCPU-Unterstützung anzufordern. Dann fordern Sie explizit vCPU-Kontingente für Azure-Ressourcengruppen in der benötigten Menge für die gewünschten Regionen an. Wenn Sie für die Ausführung Ihrer Anwendung 30 vCPUs in „Europa, Westen“ benötigen, fordern Sie 30 vCPUs in „Europa, Westen“ an. Ihr vCPU-Kontingent erhöht sich in anderen Regionen nicht. Das Kontingent von 30 vCPUs gilt nur für „Europa, Westen“.

Deshalb sollten Sie entscheiden, welche Kontingente für Azure-Ressourcengruppen Sie für Ihre Workload in der jeweiligen Region benötigen. Fordern Sie dann diese Anzahl in jeder Region an, in der Sie bereitstellen möchten. Informationen zum Ermitteln Ihrer aktuellen Kontingente für bestimmte Regionen finden Sie unter [Behandeln von Fehlern bei Ressourcenkontingenten](../templates/error-resource-quota.md).

## <a name="general-limits"></a>Allgemeine Grenzwerte

Grenzwerte für Ressourcennamen finden Sie unter [Benennungsregeln und -einschränkungen für Azure-Ressourcen](resource-name-rules.md).

Informationen zum Umgang mit Grenzwerten für Lese- und Schreibvorgänge bei der Resource Manager-API finden Sie unter [Begrenzen von Resource Manager-Anforderungen](request-limits-and-throttling.md).

### <a name="management-group-limits"></a>Grenzwerte von Verwaltungsgruppen

Die folgenden Grenzwerte gelten für [Verwaltungsgruppen](../../governance/management-groups/overview.md).

[!INCLUDE [management-group-limits](../../../includes/management-group-limits.md)]

### <a name="subscription-limits"></a>Grenzwerte für Abonnements

Die folgenden Grenzwerte gelten bei Verwendung des Azure Resource Manager und von Azure-Ressourcengruppen.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Grenzwerte für Ressourcengruppen

[!INCLUDE [azure-resource-groups-limits](../../../includes/azure-resource-groups-limits.md)]

## <a name="active-directory-limits"></a>Active Directory-Grenzwerte

[!INCLUDE [AAD-service-limits](../../../includes/active-directory-service-limits-include.md)]

## <a name="api-management-limits"></a>API Management-Grenzwerte

[!INCLUDE [api-management-service-limits](../../../includes/api-management-service-limits.md)]

## <a name="app-service-limits"></a>App Service-Grenzwerte

Die folgenden App Service-Grenzwerte umfassen Grenzwerte für Web-Apps, Mobile Apps und API-Apps.

[!INCLUDE [azure-websites-limits](../../../includes/azure-websites-limits.md)]

## <a name="automation-limits"></a>Automatisierungsgrenzwerte

[!INCLUDE [automation-limits](../../../includes/azure-automation-service-limits.md)]

## <a name="azure-app-configuration"></a>Azure App Configuration

[!INCLUDE [app-configuration-limits](../../../includes/app-configuration-limits.md)]

## <a name="azure-cache-for-redis-limits"></a>Azure Cache for Redis-Grenzwerte

[!INCLUDE [redis-cache-service-limits](../../../includes/redis-cache-service-limits.md)]

## <a name="azure-cloud-services-limits"></a>Grenzwerte für Azure Cloud Services

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

## <a name="azure-cognitive-search-limits"></a>Grenzwerte für Azure Cognitive Search

Die Tarife bestimmen die Kapazität und die Beschränkungen des Suchdiensts. Folgende Tarife sind verfügbar:

* **Kostenloser** mehrinstanzenfähiger Dienst, der mit anderen Azure-Abonnenten gemeinsam genutzt wird und für die Auswertung und kleine Entwicklungsprojekte vorgesehen ist.
* **Basic** bietet dedizierte Computeressourcen für Produktionsworkloads mit geringerem Umfang, mit bis zu drei Replikaten für Abfrageworkloads mit hoher Verfügbarkeit.
* **Standard**, darunter S1, S2, S3 und S3 High Density, ist für größere Produktionsworkloads vorgesehen. Im Standard-Tarif sind mehrere Ebenen vorhanden, damit Sie eine Ressourcenkonfiguration auswählen können, die Ihrem Workloadprofil am besten entspricht.

**Grenzwerte pro Abonnement**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**Grenzwerte pro Suchdienst**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

Detailliertere Informationen zu Grenzwerten wie etwa für Dokumentgröße, Abfragen pro Sekunde, Schlüssel, Anforderungen und Antworten, finden Sie unter [Dienstgrenzwerte in der kognitiven Azure-Suche](../../search/search-limits-quotas-capacity.md).

## <a name="azure-cognitive-services-limits"></a>Grenzwerte für Azure Cognitive Services

[!INCLUDE [azure-cognitive-services-limits](../../../includes/azure-cognitive-services-limits.md)]

## <a name="azure-cosmos-db-limits"></a>Einschränkungen für Azure Cosmos DB

Azure Cosmos DB-Grenzwerte finden Sie unter [Grenzwerte in Azure Cosmos DB](../../cosmos-db/concepts-limits.md).

## <a name="azure-data-explorer-limits"></a>Azure Data Explorer-Limits

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL

Informationen zu Grenzwerten für Azure Database for MySQL finden Sie unter [Beschränkungen in Azure Database for MySQL](../../mysql/concepts-limits.md).

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL

Informationen zu Grenzwerten für Azure Database for PostgreSQL finden Sie unter [Beschränkungen in Azure Database for PostgreSQL](../../postgresql/concepts-limits.md).

## <a name="azure-functions-limits"></a>Azure Functions-Grenzwerte

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

Weitere Informationen finden Sie unter [Vergleich von Hostingplänen für Functions](../../azure-functions/functions-scale.md#hosting-plans-comparison).

## <a name="azure-kubernetes-service-limits"></a>Grenzwerte für Azure Kubernetes Service

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

## <a name="azure-machine-learning-limits"></a>Azure Machine Learning-Grenzwerte

Die aktuellen Werte für Azure Machine Learning Compute-Kontingente finden Sie auf der [Seite „Azure Machine Learning-Kontingent“](../../machine-learning/how-to-manage-quotas.md)

## <a name="azure-maps-limits"></a>Azure Maps-Grenzwerte

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

## <a name="azure-monitor-limits"></a>Grenzwerte für Azure Monitor

### <a name="alerts"></a>Alerts

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

### <a name="action-groups"></a>Aktionsgruppen

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

### <a name="autoscale"></a>Autoscale

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-autoscale.md)]

### <a name="log-queries-and-language"></a>Protokollieren von Abfragen und Sprache

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

### <a name="log-analytics-workspaces"></a>Log Analytics-Arbeitsbereiche

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-app-insights.md)]

## <a name="azure-policy-limits"></a>Azure Policy-Grenzwerte

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="azure-signalr-service-limits"></a>Grenzwerte für Azure SignalR Service

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

## <a name="backup-limits"></a>Backup-Grenzwerte

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

## <a name="batch-limits"></a>Batch-Grenzwerte

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

## <a name="classic-deployment-model-limits"></a>Grenzwerte für das klassische Bereitstellungsmodell

Wenn Sie das klassische Bereitstellungsmodells anstelle des Azure Resource Manager-Bereitstellungsmodells verwenden, gelten die folgenden Grenzwerte.

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

## <a name="container-instances-limits"></a>Grenzwerte für Container Instances

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

## <a name="container-registry-limits"></a>Grenzwerte für die Containerregistrierung

In der folgenden Tabelle werden die Features und Grenzwerte der [Dienstebenen](../../container-registry/container-registry-skus.md) „Basic“, „Standard“ und „Premium“ dargestellt.

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

## <a name="content-delivery-network-limits"></a>Grenzwerte für Content Delivery Network

[!INCLUDE [cdn-limits](../../../includes/cdn-limits.md)]

## <a name="data-factory-limits"></a>Data Factory-Grenzwerte

[!INCLUDE [azure-data-factory-limits](../../../includes/azure-data-factory-limits.md)]

## <a name="data-lake-analytics-limits"></a>Grenzwerte für Data Lake Analytics

[!INCLUDE [azure-data-lake-analytics-limits](../../../includes/azure-data-lake-analytics-limits.md)]

## <a name="data-lake-storage-limits"></a>Grenzwerte für Data Lake Storage

[!INCLUDE [azure-data-lake-store-limits](../../../includes/azure-data-lake-store-limits.md)]

## <a name="data-share-limits"></a>Grenzwerte für Data Share

[!INCLUDE [azure-data-share-limits](../../../includes/azure-data-share-limits.md)]

## <a name="database-migration-service-limits"></a>Grenzwerte für Database Migration Service

[!INCLUDE [database-migration-service-limits](../../../includes/database-migration-service-limits.md)]

## <a name="digital-twins-limits"></a>Grenzwerte für Digital Twins

> [!NOTE]
> In einigen Bereichen dieses Diensts lassen sich Grenzwerte anpassen. Diese sind in der nachfolgenden Tabelle in der Spalte *Anpassbar?* entsprechend gekennzeichnet. Wenn der Grenzwert angepasst werden kann, lautet der Wert der Spalte *Anpassbar?* *Ja*.

[!INCLUDE [digital-twins-limits](../../../includes/digital-twins-limits.md)]

## <a name="event-grid-limits"></a>Grenzwerte für Event Grid

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

## <a name="event-hubs-limits"></a>Event Hubs-Grenzwerte

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

## <a name="iot-central-limits"></a>IoT Central-Grenzwerte
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

## <a name="iot-hub-limits"></a>IoT Hub-Grenzwerte

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

## <a name="iot-hub-device-provisioning-service-limits"></a>Grenzwerte für den IoT Hub Device Provisioning-Dienst

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

## <a name="key-vault-limits"></a>Schlüsseltresor-Grenzwerte

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

## <a name="managed-identity-limits"></a>Grenzwerte für verwaltete Identitäten

[!INCLUDE [Managed-Identity-Limits](../../../includes/managed-identity-limits.md)]


## <a name="media-services-limits"></a>Media Services-Grenzwerte

[!INCLUDE [azure-mediaservices-limits](../../../includes/media-servieces-limits-quotas-constraints.md)]

### <a name="media-services-v2-legacy"></a>Media Services v2 (Vorgängerversion)

Spezifische Grenzwerte für Media Services v2 (Legacy) finden Sie unter [Media Services v2 (Legacy)](../../media-services/previous/media-services-quotas-and-limitations.md).

## <a name="mobile-services-limits"></a>Mobile Services-Grenzwerte

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

## <a name="multi-factor-authentication-limits"></a>Grenzwerte für Multi-Factor Authentication

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

## <a name="networking-limits"></a>Grenzwerte für Netzwerke

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

### <a name="expressroute-limits"></a>Einschränkungen für ExpressRoute

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

### <a name="virtual-network-gateway-limits"></a>Grenzwerte des Gateways des virtuellen Netzwerks

[!INCLUDE [virtual-network-gateway-limits](../../../includes/azure-virtual-network-gateway-limits.md)]

### <a name="nat-gateway-limits"></a>Grenzwerte für NAT Gateway

[!INCLUDE [nat-gateway-limits](../../../includes/azure-nat-gateway-limits.md)]

### <a name="virtual-wan-limits"></a>Grenzwerte für Virtual WAN

[!INCLUDE [virtual-wan-limits](../../../includes/virtual-wan-limits.md)]

### <a name="application-gateway-limits"></a>Application Gateway-Grenzwerte

Die folgende Tabelle gilt für v1, v2, Standard und WAF SKUs, sofern nichts anderes angegeben ist.
[!INCLUDE [application-gateway-limits](../../../includes/application-gateway-limits.md)]

### <a name="network-watcher-limits"></a>Network Watcher-Grenzwerte

[!INCLUDE [network-watcher-limits](../../../includes/network-watcher-limits.md)]

### <a name="private-link-limits"></a>Private Link-Grenzwerte

[!INCLUDE [private-link-limits](../../../includes/private-link-limits.md)]

## <a name="purview-limits"></a>Purview-Grenzwerte

Die aktuellen Werte für Azure Purview-Kontingente finden Sie auf der [Seite „Azure Purview-Kontingent“](../../purview/how-to-manage-quotas.md)

### <a name="traffic-manager-limits"></a>Traffic Manager-Grenzwerte

[!INCLUDE [traffic-manager-limits](../../../includes/traffic-manager-limits.md)]

### <a name="azure-bastion-limits"></a>Azure Bastion-Grenzwerte

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

### <a name="azure-dns-limits"></a>Grenzwerte für Azure DNS

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

### <a name="azure-firewall-limits"></a>Grenzwerte für Azure Firewall

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

### <a name="azure-front-door-service-limits"></a>Grenzwerte von Azure Front Door Service

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

## <a name="notification-hubs-limits"></a>Grenzwerte für Notification Hubs

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

## <a name="azure-role-based-access-control-limits"></a>Grenzwerte für rollenbasierte Zugriffssteuerung von Azure (RBAC)

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control-limits.md)]

## <a name="service-bus-limits"></a>Service Bus-Grenzwerte

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

## <a name="site-recovery-limits"></a>Site Recovery-Grenzwerte

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

## <a name="sql-database-limits"></a>Grenzwerte für SQL-Datenbanken

Weitere Informationen zu Grenzwerten für SQL-Datenbank finden Sie unter [Ressourcenlimits für Singletons mit dem auf virtuellen Kernen (V-Kernen) basierenden Kaufmodell](../../azure-sql/database/resource-limits-vcore-single-databases.md), [Ressourcenlimits für Pools für elastische Datenbanken, die das V-Kern-Kaufmodell verwenden](../../azure-sql/database/resource-limits-vcore-elastic-pools.md) und [Übersicht über Ressourcenlimits für Azure SQL Managed Instance](../../azure-sql/managed-instance/resource-limits.md).

## <a name="azure-synapse-analytics-limits"></a>Grenzwerte für Azure Synapse Analytics

Informationen zu den Grenzwerten für Azure Synapse Analytics finden Sie unter [Azure Synapse-Ressourcenlimits](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="storage-limits"></a>Speichergrenzwerte

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

Weitere Informationen zu den Grenzwerten für Standardspeicherkonten finden Sie unter [Skalierbarkeitsziele für Standardspeicherkonten](../../storage/common/scalability-targets-standard-account.md).

### <a name="storage-resource-provider-limits"></a>Grenzwerte für Speicherressourcenanbieter

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="azure-blob-storage-limits"></a>Limits bei Azure Blob Storage

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

### <a name="azure-files-limits"></a>Azure Files-Limits

Weitere Informationen zu Grenzwerten für Azure Files finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Files](../../storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-limits"></a>Grenzwerte der Azure-Dateisynchronisierung

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-queue-storage-limits"></a>Azure Queue-Speicherlimits

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

### <a name="azure-table-storage-limits"></a>Azure Table-Speicherlimits

[!INCLUDE [storage-tables-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
### <a name="virtual-machine-disk-limits"></a>Grenzwerte für Datenträger virtueller Computer

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

Weitere Informationen finden Sie unter [Größen virtueller Computer](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="disk-encryption-sets"></a>Datenträgerverschlüsselungssätze

Pro Region und Abonnement können maximal 50 Datenträgerverschlüsselungssätze erstellt werden. Weitere Informationen finden Sie in der Dokumentation zur Verschlüsselung für [Linux](../../virtual-machines/disk-encryption.md#restrictions)- oder [Windows](../../virtual-machines/disk-encryption.md#restrictions)-VMs. Wenn Sie das Kontingent erhöhen müssen, wenden Sie sich an den Azure-Support.

### <a name="managed-virtual-machine-disks"></a>Verwaltete VM-Datenträger

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

### <a name="unmanaged-virtual-machine-disks"></a>Nicht verwaltete VM-Datenträger

[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="storsimple-system-limits"></a>StorSimple-Systemgrenzwerte

[!INCLUDE [storsimple-limits-table](../../../includes/storsimple-limits-table.md)]

## <a name="stream-analytics-limits"></a>Stream Analytics-Grenzwerte

[!INCLUDE [stream-analytics-limits-table](../../../includes/stream-analytics-limits-table.md)]

## <a name="virtual-machines-limits"></a>Grenzwerte für virtuelle Computer

### <a name="virtual-machines-limits"></a>Grenzwerte für virtuelle Computer

[!INCLUDE [azure-virtual-machines-limits](../../../includes/azure-virtual-machines-limits.md)]

### <a name="virtual-machines-limits---azure-resource-manager"></a>Grenzwerte für virtuelle Computer – Azure-Ressourcen-Manager

Die folgenden Grenzwerte gelten bei Verwendung des Azure Resource Manager und von Azure-Ressourcengruppen.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="shared-image-gallery-limits"></a>Einschränkungen des Katalogs mit geteilten Images

Pro Abonnement gibt es Einschränkungen hinsichtlich der Bereitstellung von Ressourcen mithilfe des Katalogs mit geteilten Images:

- 100 Kataloge mit freigegebenen Images pro Abonnement und Region
- 1\.000 Imagedefinitionen pro Abonnement und Region
- 10.000 Imageversionen pro Abonnement und Region

## <a name="virtual-machine-scale-sets-limits"></a>Grenzwerte für VM-Skalierungsgruppen

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

## <a name="see-also"></a>Weitere Informationen

* [Understand Azure limits and increases](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) (Grundlegendes zu Grenzwerten und Erhöhungen für Azure)
* [Virtual machine and cloud service sizes for Azure](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Größen virtueller Computer und Clouddienste für Azure)
* [Sizes for Azure Cloud Services](../../cloud-services/cloud-services-sizes-specs.md) (Größen für Azure Cloud Services)
* [Benennungsregeln und -einschränkungen für Azure-Ressourcen](resource-name-rules.md)