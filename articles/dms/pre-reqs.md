---
title: Voraussetzungen für Azure Database Migration Service
description: Hier finden Sie eine Übersicht über die Voraussetzungen für die Verwendung von Azure Database Migration Service zum Durchführen von Datenbankmigrationen.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: f6438fb1c21ce248f6e1b766e7e10cc79043db9f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961582"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Übersicht über die Voraussetzungen für die Verwendung von Azure Database Migration Service

Zur Gewährleistung reibungsloser Datenbankmigrationen mit Azure Database Migration Service müssen mehrere Voraussetzungen erfüllt werden. Einige der Voraussetzungen gelten für alle unterstützten Szenarien (Quelle-Ziel-Paare) des Diensts, andere nur für ein bestimmtes Szenario.

In den folgenden Abschnitten sind die Voraussetzungen für die Verwendung von Azure Database Migration Service aufgeführt.

## <a name="prerequisites-common-across-migration-scenarios"></a>Voraussetzungen, die für alle Migrationsszenarien gelten

Folgende Voraussetzungen von Azure Database Migration Service gelten für alle unterstützten Migrationsszenarien:

* Erstellen Sie ein Microsoft Azure Virtual Network für Azure Database Migration Service, indem Sie das Azure Resource Manager-Bereitstellungsmodell verwenden, das Site-to-Site-Konnektivität für Ihre lokalen Quellserver entweder über [ExpressRoute](../expressroute/expressroute-introduction.md) oder über [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) bereitstellt.
* Vergewissern Sie sich, dass die folgenden Kommunikationsports nicht durch Regeln für Netzwerksicherheitsgruppen (NSGs) des virtuellen Netzwerks blockiert werden: 443, 53, 9354, 445, 12000. Ausführlichere Informationen zur NSG-Datenverkehrsfilterung in einem virtuellen Netzwerk finden Sie im Artikel [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Wenn Sie eine Firewallappliance vor Ihren Quelldatenbanken verwenden, müssen Sie möglicherweise Firewallregeln hinzufügen, um Azure Database Migration Service den Zugriff auf die Quelldatenbanken für die Migration zu ermöglichen.
* Konfigurieren Sie Ihre [Windows-Firewall für Datenbank-Engine-Zugriff](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Aktivieren Sie das TCP/IP-Protokoll (dieses wird während der SQL Server Express-Installation standardmäßig deaktiviert), indem Sie die Anweisungen im Artikel [Aktivieren oder Deaktivieren eines Servernetzwerkprotokolls](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) befolgen.

    > [!IMPORTANT]
    > Bei der Erstellung einer Instanz von Azure Database Migration Service muss auf Einstellungen für das virtuelle Netzwerk zugegriffen werden, die sich normalerweise nicht in der gleichen Ressourcengruppe befinden. Benutzer, die eine DMS-Instanz erstellen möchten, müssen daher über eine entsprechende Berechtigung auf Abonnementebene verfügen. Führen Sie zum Erstellen der erforderlichen Rollen das folgende Skript aus. Die Rollen können dann nach Bedarf zugewiesen werden.
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read", `
    > "Microsoft.DataMigration/*/read", `
    > "Microsoft.Resources/subscriptions/resourceGroups/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action", `
    > "Microsoft.Network/virtualNetworks/subnets/join/action", `
    > "Microsoft.Network/virtualNetworks/write", `
    > "Microsoft.Network/virtualNetworks/read", `
    > "Microsoft.Resources/deployments/validate/action", `
    > "Microsoft.Resources/deployments/*/read", `
    > "Microsoft.Resources/deployments/*/write"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Voraussetzungen für die Migration von SQL Server zu Azure SQL-Datenbank

Neben den Voraussetzungen für Azure Database Migration Service, die für alle Migrationsszenarien gelten, gibt es auch Voraussetzungen, die nur für ein bestimmtes Szenario gelten.

Wenn Sie Azure Database Migration Service für Migrationen von SQL Server zu Azure SQL-Datenbank verwenden, gelten nicht nur die Voraussetzungen für alle Migrationsszenarien, sondern auch folgende zusätzliche Voraussetzungen:

* Erstellen Sie eine Azure SQL-Datenbankinstanz, indem Sie die Anweisungen im Artikel [Schnellstart: Erstellen einer Azure SQL-Einzeldatenbank](../azure-sql/database/single-database-create-quickstart.md) befolgen.
* Laden Sie den [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595), Version 3.3 oder höher, herunter, und installieren Sie ihn.
* Öffnen Sie Ihre Windows-Firewall, damit Azure Database Migration Service auf die SQL Server-Quellinstanz zugreifen kann (standardmäßig TCP-Port 1433).
* Bei der Ausführung mehrerer benannter SQL Server-Instanzen mit dynamischen Ports empfiehlt es sich, den SQL-Browser-Dienst zu aktivieren und den Zugriff auf den UDP-Port 1434 durch Ihre Firewalls zuzulassen, sodass Azure Database Migration Service eine Verbindung mit einer benannten Instanz auf Ihrem Quellserver herstellen kann.
* Erstellen Sie für SQL-Datenbank eine [Firewallregel](../azure-sql/database/firewall-configure.md) auf Serverebene, um für Azure Database Migration Service den Zugriff auf die Zieldatenbanken zu ermöglichen. Geben Sie den Subnetzbereich des für Azure Database Migration Service verwendeten virtuellen Netzwerks an.
* Stellen Sie sicher, dass die für die Verbindung mit der SQL Server-Quellinstanz verwendeten Anmeldeinformationen über [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql)-Berechtigungen verfügen.
* Stellen Sie sicher, dass die für die Verbindungsherstellung mit der Zieldatenbank verwendeten Anmeldeinformationen über die Berechtigung „CONTROL DATABASE“ für die Zieldatenbank verfügen.

   > [!NOTE]
   > Eine vollständige Liste der Voraussetzungen für die Verwendung von Azure Database Migration Service für Migrationen von SQL Server zu Azure SQL-Datenbank finden Sie im Tutorial [Migrieren von SQL Server zu Azure SQL-Datenbank](./tutorial-sql-server-to-azure-sql.md).
   >

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance"></a>Voraussetzungen für die Migration von SQL Server zu Azure SQL Managed Instance

* Eine Anleitung zum Erstellen einer SQL Managed Instance finden Sie im Artikel [Erstellen einer Instanz von Azure SQL Managed Instance im Azure-Portal](../azure-sql/managed-instance/instance-create-quickstart.md).
* Öffnen Sie die Firewalls, um SMB-Datenverkehr über Port 445 für die IP-Adresse oder den Subnetzbereich von Azure Database Migration Service zuzulassen.
* Öffnen Sie Ihre Windows-Firewall, damit Azure Database Migration Service auf die SQL Server-Quellinstanz zugreifen kann (standardmäßig TCP-Port 1433).
* Bei der Ausführung mehrerer benannter SQL Server-Instanzen mit dynamischen Ports empfiehlt es sich, den SQL-Browser-Dienst zu aktivieren und den Zugriff auf den UDP-Port 1434 durch Ihre Firewalls zuzulassen, sodass Azure Database Migration Service eine Verbindung mit einer benannten Instanz auf Ihrem Quellserver herstellen kann.
* Stellen Sie sicher, dass die zum Herstellen einer Verbindung zwischen der SQL Server-Quellinstanz und der verwalteten Zielinstanz verwendeten Anmeldungen Mitglieder der sysadmin-Serverrolle sind.
* Erstellen Sie eine Netzwerkfreigabe, mit der Azure Database Migration Service die Quelldatenbank sichern kann.
* Stellen Sie sicher, dass das Dienstkonto, unter dem die SQL Server-Quellinstanz ausgeführt wird, über Schreibberechtigungen für die Netzwerkfreigabe verfügt, die Sie erstellt haben, und dass das Computerkonto für den Quellserver über Lese-/Schreibzugriff auf dieselbe Freigabe verfügt.
* Notieren Sie sich einen Windows-Benutzer (mit Kennwort), der Vollzugriff auf die von Ihnen zuvor erstellte Netzwerkfreigabe hat. Azure Database Migration Service verwendet die Benutzeranmeldeinformationen, um die Sicherungsdateien für Wiederherstellungsvorgänge in den Azure Storage-Container hochzuladen.
* Erstellen Sie einen Blobcontainer, und rufen Sie dessen SAS-URI mithilfe der Schritte im Artikel [Verwalten von Azure Blob Storage-Ressourcen mit dem Storage-Explorer](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container) ab. Achten Sie beim Erstellen des SAS-URI darauf, im Richtlinienfenster alle Berechtigungen (Lesen, Schreiben, Löschen, Auflisten) auszuwählen.

   > [!NOTE]
   > Eine vollständige Liste der Voraussetzungen für die Verwendung von Azure Database Migration Service für Migrationen von SQL Server zu SQL Managed Instance finden Sie im Tutorial [Migrieren von SQL Server zu SQL Managed Instance](./tutorial-sql-server-to-managed-instance.md).

## <a name="next-steps"></a>Nächste Schritte

Eine Übersicht über Azure Database Migration Service und Informationen zur regionalen Verfügbarkeit finden Sie im Artikel [Was ist Azure Database Migration Service?](dms-overview.md).