---
title: Netzwerktopologien für Migrationen von verwalteten SQL-Instanzen
titleSuffix: Azure Database Migration Service
description: Lernen Sie die Quell- und Zielkonfigurationen für Migrationen von verwalteten Azure SQL-Instanzen mithilfe von Azure Database Migration Service kennen.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: reference
ms.date: 01/08/2020
ms.openlocfilehash: ae036b7d893eb268ea55026054bf364dad0b610e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961548"
---
# <a name="network-topologies-for-azure-sql-managed-instance-migrations-using-azure-database-migration-service"></a>Netzwerktopologien für Migrationen von verwalteten Azure SQL-Instanzen mithilfe von Azure Database Migration Service

In diesem Artikel werden verschiedene Netzwerktopologien erläutert, die Azure Database Migration Service verwenden kann, um eine umfassende Migration von SQL Server-Instanzen zu einer verwalteten Azure SQL-Instanz zu ermöglichen.

## <a name="azure-sql-managed-instance-configured-for-hybrid-workloads"></a>Verwaltete Azure SQL-Instanz konfiguriert für Hybridworkloads 

Verwenden Sie diese Topologie, wenn die verwaltete Azure SQL-Instanz mit Ihrem lokalen Netzwerk verbunden ist. Dieser Ansatz nutzt das einfachste Netzwerkrouting bei maximalem Datendurchsatz während der Migration.

![Netzwerktopologie für Hybridworkloads](media/resource-network-topologies/hybrid-workloads.png)

**Anforderungen**

- In diesem Szenario werden die verwaltete SQL-Instanz und die Azure Database Migration Service-Instanz in derselben Microsoft Azure Virtual Network-Instanz erstellt, sie nutzen jedoch unterschiedliche Subnetze.  
- Das in diesem Szenario verwendete virtuelle Netzwerk ist über [ExpressRoute](../expressroute/expressroute-introduction.md) oder [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) auch mit dem lokalen Netzwerk verbunden.

## <a name="sql-managed-instance-isolated-from-the-on-premises-network"></a>Verwaltete SQL-Instanz isoliert vom lokalen Netzwerk

Verwenden Sie diese Netzwerktopologie, wenn Ihre Umgebung mindestens eines der folgenden Szenarien erfordert:

- Die verwaltete SQL-Instanz ist von der lokalen Konnektivität isoliert, aber die Azure Database Migration Service-Instanz ist mit dem lokalen Netzwerk verbunden.
- Es gelten rollenbasierte Azure-Richtlinien (rollenbasierte Azure-Zugriffssteuerung), und Sie müssen den Benutzerzugriff auf das Abonnement beschränken, unter dem die verwaltete SQL-Instanz gehostet wird.
- Die für die verwaltete SQL-Instanz und Azure Database Migration Service verwendeten virtuellen Netzwerke befinden sich in unterschiedlichen Abonnements.

![Netzwerktopologie für eine vom lokalen Netzwerk isolierte verwaltete Instanz](media/resource-network-topologies/mi-isolated-workload.png)

**Anforderungen**

- Das virtuelle Netzwerk, das Azure Database Migration Service für dieses Szenario verwendet, muss über ExpressRoute (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) oder [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) auch mit dem lokalen Netzwerk verbunden sein.
- Richten Sie ein [VNET-Netzwerkpeering](../virtual-network/virtual-network-peering-overview.md) zwischen dem virtuellen Netzwerk für die verwaltete SQL-Instanz und Azure Database Migration Service ein.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Cloud-zu-Cloud-Migrationen: gemeinsam genutztes virtuelles Netzwerk

Verwenden Sie diese Topologie, wenn die SQL Server-Quelle auf einer Azure-VM gehostet wird und dasselbe virtuelle Netzwerk wie die verwaltete SQL-Instanz und Azure Database Migration Service nutzt.

![Netzwerktopologie für Cloud-zu-Cloud-Migrationen mit einem gemeinsam genutzten VNet](media/resource-network-topologies/cloud-to-cloud.png)

**Anforderungen**

- Es bestehen keine weiteren Anforderungen.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Cloud-zu-Cloud-Migrationen: isoliertes virtuelles Netzwerk

Verwenden Sie diese Netzwerktopologie, wenn Ihre Umgebung mindestens eines der folgenden Szenarien erfordert:

- Die verwaltete SQL-Instanz wird in einem isolierten virtuellen Netzwerk bereitgestellt.
- Es gelten rollenbasierte Azure-Richtlinien (rollenbasierte Azure-Zugriffssteuerung), und Sie müssen den Benutzerzugriff auf das Abonnement beschränken, unter dem SQL Managed Instance gehostet wird.
- Die für die verwaltete SQL-Instanz und Azure Database Migration Service verwendeten virtuellen Netzwerke befinden sich in unterschiedlichen Abonnements.

![Netzwerktopologie für Cloud-zu-Cloud-Migrationen mit einem isolierten VNet](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Anforderungen**

- Richten Sie ein [VNET-Netzwerkpeering](../virtual-network/virtual-network-peering-overview.md) zwischen dem virtuellen Netzwerk für die verwaltete SQL-Instanz und Azure Database Migration Service ein.

## <a name="inbound-security-rules"></a>Eingangssicherheitsregeln

| **NAME**   | **PORT** | **PROTOKOLL** | **QUELLE** | **ZIEL** | **AKTION** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Any      | Any          | DMS-SUBNETZ | Any             | Allow      |

## <a name="outbound-security-rules"></a>Ausgangssicherheitsregeln

| **NAME**                  | **PORT**                                              | **PROTOKOLL** | **QUELLE** | **ZIEL**           | **AKTION** | **Grund für die Regel**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| management                | 443, 9354                                              | TCP          | Any        | Any                       | Allow      | Kommunikation auf Verwaltungsebene über Service Bus und Azure Blob Storage. <br/>(Wenn Microsoft-Peering aktiviert ist, benötigen Sie diese Regel möglicherweise nicht.)                                                             |
| Diagnose               | 12000                                                 | TCP          | Any        | Any                       | Allow      | DMS verwendet diese Regel zum Sammeln von Diagnoseinformationen für die Problembehandlung.                                                                                                                      |
| SQL-Quellserver         | 1433 (oder TCP-IP-Port, an dem SQL Server lauscht) | TCP          | Any        | Lokaler Adressraum | Allow      | SQL Server-Quellkonnektivität von DMS <br/>(Wenn Sie über Site-to-Site-Konnektivität verfügen, benötigen Sie diese Regel möglicherweise nicht.)                                                                                       |
| Benannte SQL Server-Instanz | 1434                                                  | UDP          | Any        | Lokaler Adressraum | Allow      | Quellkonnektivität der benannten SQL Server-Instanz von DMS <br/>(Wenn Sie über Site-to-Site-Konnektivität verfügen, benötigen Sie diese Regel möglicherweise nicht.)                                                                        |
| SMB-Freigabe                 | 445                                                   | TCP          | Any        | Lokaler Adressraum | Allow      | SMB-Netzwerkfreigabe, in der DMS Datenbank-Sicherungsdateien für Migrationen zu verwalteten Azure SQL-Datenbank-Instanzen und SQL Server-Instanzen auf virtuellen Azure-Computern speichert <br/>(Wenn Sie über Site-to-Site-Konnektivität verfügen, benötigen Sie diese Regel möglicherweise nicht.) |
| DMS_subnet                | Any                                                   | Any          | Any        | DMS_Subnet                | Allow      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Weitere Informationen

- [Migrieren von SQL Server zu einer verwalteten SQL-Instanz](./tutorial-sql-server-to-managed-instance.md)
- [Übersicht über die Voraussetzungen für die Verwendung von Azure Database Migration Service](./pre-reqs.md)
- [Erstellen eines virtuellen Netzwerks im Azure-Portal](../virtual-network/quick-create-portal.md)

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über Azure Database Migration Service finden Sie im Artikel [Was ist Azure Database Migration Service?](dms-overview.md).
- Neueste Informationen zur regionalen Verfügbarkeit von Azure Database Migration Service finden Sie auf der Seite [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration).