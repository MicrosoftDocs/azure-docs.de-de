---
title: Contoso-Migrationsserie | Microsoft-Dokumentation
description: Bietet eine Übersicht über die Migrationsstrategie und Szenarien, die Contoso verwendet, um das lokale Rechenzentrum zu Azure zu migrieren.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: raynew
ms.openlocfilehash: 048772edadca36a63870a2965c703ca7e6ec8c63
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66729901"
---
# <a name="contoso-migration-series"></a>Contoso-Migrationsserie


Verschiedene Artikel veranschaulichen, wie die fiktive Organisation Contoso ihre lokale Infrastruktur zur [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/)-Cloud migriert. 

Die Reihe enthält Informationen und Szenarien, die veranschaulichen, wie die Infrastruktur einer Migration eingerichtet wird und wie verschiedene Migrationstypen ausgeführt werden. Die Komplexität der Szenarien erhöht sich hierbei immer mehr. Die Artikel zeigen, wie das Unternehmen Contoso seine Migration durchführt, verweisen jedoch durchgängig auf allgemeine Informationen und spezielle Anweisungen.

## <a name="migration-articles"></a>Artikel zur Migration

Die Artikel der Reihe werden in der folgenden Tabelle zusammengefasst.  

- Die einzelnen Migrationsszenarien basieren auf geringfügig variierten Geschäftszielen, die die Migrationsstrategie bestimmen.
- Für die einzelnen Bereitstellungsszenarien erhalten Sie Informationen zu Business-Treibern und Zielen, einer vorgeschlagenen Architektur, Schritten zum Ausführen der Migration sowie Empfehlungen zur Bereinigung und den nächsten Schritten nach Abschluss der Migration.

**Artikel** | **Details** 
--- | --- 
[Artikel 1: Übersicht](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Dies ist eine Übersicht über die Artikelreihe, die Migrationsstrategie von Contoso und die in der Reihe verwendeten Beispiel-Apps. 
[Artikel 2: Bereitstellen einer Azure-Infrastruktur](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso bereitet seine lokale Infrastruktur und die Azure-Infrastruktur für die Migration vor. Für alle Migrationsartikel der Reihe wird dieselbe Infrastruktur verwendet. 
[Artikel 3: Bewerten der lokalen Ressourcen für die Migration zu Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-assessment)  | Contoso führt eine Bewertung seiner lokalen App SmartHotel360 durch, die in VMware ausgeführt wird. Contoso bewertet virtuelle Computer der App mit dem Azure Migrate-Dienst und die SQL Server-Datenbank der App mit dem Datenmigrations-Assistenten.
[Artikel 4: Rehosten einer App auf einer Azure-VM und einer verwalteten Azure SQL-Datenbank-Instanz](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso führt für seine lokale App SmartHotel360 eine Migration per Lift & Shift zu Azure aus. Contoso migriert den virtuellen Front-End-Computer der App mithilfe von [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migriert die App-Datenbank mit dem [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) zu einer verwalteten Azure SQL-Datenbank-Instanz.
[Artikel 5: Rehosten einer App auf Azure-VMs](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso migriert die VMs der SmartHotel360-App mithilfe des Site Recovery-Diensts zu Azure-VMs. 
[Artikel 6: Zuweisen eines neuen Hosts für eine App auf Azure-VMs und in einer SQL Server Always On-Verfügbarkeitsgruppe](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |Contoso migriert die App SmartHotel360. Contoso verwendet Site Recovery, um die App-VMs zu migrieren. Der Database Migration Service wird verwendet, um die App-Datenbank zu einem SQL Server-Cluster zu migrieren, der mit einer Always On-Verfügbarkeitsgruppe geschützt ist. 
[Artikel 7: Rehosten einer Linux-App auf Azure-VMs](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso führt mithilfe des Site Recovery-Diensts per Lift & Shift-Vorgang eine Migration seiner Linux-App „osTicket“ zu virtuellen Azure-Computern durch.
[Artikel 8: Rehosten einer Linux-App auf Azure-VMs und Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso migriert seine Linux-App „osTicket“ mithilfe von Site Recovery zu Azure-VMs. Die App-Datenbank wird zu Azure Database for MySQL migriert, indem MySQL Workbench verwendet wird. 
[Artikel 9: Umgestalten einer App in eine Azure-Web-App und in Azure SQL-Datenbank](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso migriert die SmartHotel360-App zu einer Azure-Web-App und die App-Datenbank mithilfe des Datenbankmigrations-Assistenten zu einer Azure SQL Server-Instanz.     
[Artikel 10: Umgestalten einer Linux-App in eine Azure-Web-App und in Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso migriert die Linux-App „osTicket“ mithilfe von Azure Traffic Manager zu einer Azure-Web-App in mehreren Azure-Regionen. Zur Sicherstellung der Continuous Delivery erfolgt eine Integration in GitHub. Contoso migriert die App-Datenbank zu einer Azure Database for MySQL-Instanz. 
[Artikel 11: Umgestalten von Team Foundation Server in Azure DevOps Services](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso migriert die lokale Team Foundation Server-Bereitstellung zu Azure DevOps Services in Azure.
[Artikel 12: Umstrukturieren einer App in einen Azure-Container und Azure SQL-Datenbank](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rearchitect-container-sql) | Contoso migriert seine SmartHotel-App zu Azure. Anschließend wird die App-Webschicht zu einem Windows-Container umstrukturiert, der in Azure Service Fabric ausgeführt wird, und die Datenbank wird zu einer Azure SQL-Datenbank umstrukturiert. 
[Artikel 13: Neuerstellen einer App in Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso erstellt die SmartHotel-App mit verschiedenen Azure-Funktionen und -Diensten neu – z.B. Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services und Azure Cosmos DB.  
[Artikel 14: Skalieren einer Migration zu Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Nachdem Contoso verschiedene Kombinationen für die Migration getestet hat, bereitet das Unternehmen sich jetzt auf eine vollständige Migration nach Azure in großem Umfang vor. 


    

## <a name="next-steps"></a>Nächste Schritte

[Informationen zur Cloudmigration](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) 

