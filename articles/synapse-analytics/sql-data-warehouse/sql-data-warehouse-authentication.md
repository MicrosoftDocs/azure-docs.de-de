---
title: Authentifizierung für einen dedizierten SQL-Pool (früher SQL Data Warehouse)
description: Erfahren Sie, wie Sie die Authentifizierung bei einem dedizierten SQL-Pool (früher SQL Data Warehouse) für Azure Synapse Analytics durchführen, indem Sie die Azure Active Directory- oder SQL Server-Authentifizierung nutzen.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: b2b5ca024046c5bc46fff756c55688d3ff0cfea1
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451962"
---
# <a name="authenticate-to-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Authentifizieren beim dedizierten SQL-Pool (früher SQL Data Warehouse) in Azure Synapse Analytics

Erfahren Sie, wie Sie die Authentifizierung bei einem dedizierten SQL-Pool (früher SQL Data Warehouse) für Azure Synapse durchführen, indem Sie die Azure Active Directory- oder SQL Server-Authentifizierung nutzen.

Zum Herstellen einer Verbindung mit einem dedizierten SQL-Pool (früher SQL Data Warehouse) müssen Sie zu Authentifizierungszwecken Sicherheitsanmeldeinformationen übergeben. Beim Herstellen einer Verbindung werden bestimmte Verbindungseinstellungen im Rahmen der Einrichtung Ihrer Abfragesitzung konfiguriert.  

Weitere Informationen zur Sicherheit und zum Aktivieren von Verbindungen mit Ihrem dedizierten SQL-Pool (früher SQL Data Warehouse) finden Sie in der Dokumentation zum [Sichern einer Datenbank](sql-data-warehouse-overview-manage-security.md).

## <a name="sql-authentication"></a>SQL-Authentifizierung

Zum Herstellen einer Verbindung mit Ihrem dedizierten SQL-Pool (früher SQL Data Warehouse) müssen Sie die folgenden Informationen angeben:

* Vollqualifizierter Servername
* SQL-Authentifizierung
* Username
* Kennwort
* Standarddatenbank (optional)

Standardmäßig wird die Verbindung mit der *Masterdatenbank* hergestellt und nicht mit Ihrer Benutzerdatenbank. Sie haben zwei Möglichkeiten, um eine Verbindung mit Ihrer Benutzerdatenbank herzustellen:

* Geben Sie die Standarddatenbank an, wenn Sie Ihren Server mit dem SQL Server-Objekt-Explorer in SSDT oder SSMS registrieren, oder geben Sie die Datenbank in Ihrer Verbindungszeichenfolge für die Anwendung an. Beziehen Sie für eine ODBC-Verbindung beispielsweise den InitialCatalog-Parameter ein.
* Markieren Sie die Benutzerdatenbank, bevor Sie eine Sitzung in SSDT erstellen.

> [!NOTE]
> Die Transact-SQL-Anweisung **USE MyDatabase;** wird nicht zum Ändern der Datenbank für eine Verbindung unterstützt. Eine Anleitung zum Herstellen einer Verbindung mit einem SQL-Pool über SSDT finden Sie im Artikel [Abfragen mit Visual Studio](sql-data-warehouse-query-visual-studio.md).

## <a name="azure-active-directory-authentication"></a>Azure Active Directory-Authentifizierung

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)-Authentifizierung ist ein Mechanismus zum Herstellen einer Verbindung mit einem SQL-Pool, wozu Identitäten in Azure Active Directory (Azure AD) verwendet werden. Mithilfe der Azure Active Directory-Authentifizierung können Sie die Identitäten von Datenbankbenutzern und andere Microsoft-Dienste an einer zentralen Stelle verwalten. Die zentrale ID-Verwaltung ermöglicht es, Benutzer des dedizierten SQL-Pools (früher SQL Data Warehouse) zu verwalten, und vereinfacht die Berechtigungsverwaltung.

### <a name="benefits"></a>Vorteile

Azure Active Directory bietet folgende Vorteile:

* Es wird eine Alternative zur SQL Server-Authentifizierung bereitgestellt.
* Es wird einer unkontrollierten Ausbreitung von Benutzeridentitäten über Server hinweg Einhalt geboten.
* Es wird eine Kennwortrotation über eine zentrale Stelle ermöglicht.
* Datenbankberechtigungen können mithilfe externer Gruppen (Azure AD-Gruppen) verwaltet werden.
* Das Aktivieren der integrierten Windows-Authentifizierung und andere von Azure Active Directory unterstützte Authentifizierungsformen machen das Speichern von Kennwörtern überflüssig.
* Eigenständige Datenbankbenutzer werden zum Authentifizieren von Identitäten auf Datenbankebene verwendet.
* Unterstützt tokenbasierte Authentifizierung für Anwendungen, die eine Verbindung mit dem SQL-Pool herstellen.
* Die Multi-Factor Authentication über die universelle Active Directory-Authentifizierung wird für verschiedene Tools einschließlich [SQL Server Management Studio](../../azure-sql/database/authentication-mfa-ssms-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) und [SQL Server Data Tools](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) unterstützt.

> [!NOTE]
> Azure Active Directory ist immer noch relativ neu und weist einige Einschränkungen auf. Um sicherzustellen, dass Azure Active Directory für Ihre Umgebung geeignet ist, lesen Sie [Funktionen und Einschränkungen von Azure AD](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#azure-ad-features-and-limitations), und beachten Sie insbesondere den Abschnitt „Zusätzliche Überlegungen“.

### <a name="configuration-steps"></a>Konfigurationsschritte

Befolgen Sie diese Schritte zum Konfigurieren der Azure Active Directory-Authentifizierung.

1. Erstellen und Auffüllen eines Azure Active Directory-Verzeichnisses
2. Optional: Zuordnen oder Ändern des aktiven Verzeichnisses, das mit Ihrem Azure-Abonnement derzeit verknüpft ist
3. Erstellen eines Azure Active Directory-Administrators für Azure Synapse
4. Konfigurieren der Clientcomputer
5. Erstellen eigenständiger Datenbankbenutzer in der Datenbank, die Azure AD-Identitäten zugeordnet sind
6. Herstellen einer Verbindung mit Ihrem SQL-Pool unter Verwendung von Azure AD-Identitäten

Azure Active Directory-Benutzer werden derzeit nicht im SSDT-Objekt-Explorer angezeigt. Sie können die Benutzer in [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) anzeigen, um dieses Problem zu umgehen.

### <a name="find-the-details"></a>Suchen der Details

* Die Schritte zum Konfigurieren und Verwenden der Azure Active Directory-Authentifizierung sind fast identisch für die Azure SQL-Datenbank und Synapse SQL in Azure Synapse. Befolgen Sie die ausführlichen Schritte im Thema [Herstellen einer Verbindung mit SQL-Datenbank oder SQL-Pool unter Verwendung der Azure Active Directory-Authentifizierung](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
* Erstellen Sie benutzerdefinierte Datenbankrollen, und fügen Sie den Rollen Benutzer hinzu. Weisen Sie anschließend präzise Berechtigungen für die Rollen zu. Weitere Informationen finden Sie unter [Erste Schritte mit Berechtigungen für die Datenbank-Engine](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Abfragen mit Visual Studio und anderen Anwendungen finden Sie unter [Abfragen mit Visual Studio](sql-data-warehouse-query-visual-studio.md).
