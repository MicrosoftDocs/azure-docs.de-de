---
title: Rolle „Verzeichnisleseberechtigte“ in Azure Active Directory für Azure SQL
description: Hier erfahren Sie etwas über die Rolle „Verzeichnisleseberechtigte“ in Azure AD für Azure SQL.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: 2374b1fb7f355b336c713a8a3240eacc8b1f188c
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675074"
---
# <a name="directory-readers-role-in-azure-active-directory-for-azure-sql"></a>Rolle „Verzeichnisleseberechtigte“ in Azure Active Directory für Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> Dieses Feature in diesem Artikel befindet sich in der **öffentlichen Vorschau** .

Azure Active Directory (Azure AD) hat die [Verwendung von Cloudgruppen zum Verwalten von Rollenzuweisungen in Azure Active Directory (Vorschau)](../../active-directory/roles/groups-concept.md) eingeführt. Dadurch können Azure AD-Rollen Gruppen zugewiesen werden.

Wenn Sie eine [verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) für Azure SQL-Datenbank, Azure SQL Managed Instance oder Azure Synapse Analytics aktivieren, muss der Identität die Azure AD-Rolle [**Verzeichnisleseberechtigte**](../../active-directory/roles/permissions-reference.md#directory-readers) zugewiesen werden, um Lesezugriff auf die [Azure AD Graph-API](../../active-directory/develop/active-directory-graph-api.md) zu ermöglichen. Die verwaltete Identität von SQL-Datenbank und Azure Synapse wird als Serveridentität bezeichnet. Die verwaltete Identität von SQL Managed Instance wird als verwaltete Instanzidentität bezeichnet und automatisch zugewiesen, wenn die Instanz erstellt wird. Weitere Informationen zum Zuweisen einer Serveridentität zu SQL-Datenbank oder Azure Synapse finden Sie unter [Aktivieren von Dienstprinzipalen zum Erstellen von Azure AD-Benutzern](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users).

Die Rolle **Verzeichnisleseberechtigte** ist für Folgendes erforderlich:

- Erstellen von Azure AD-Anmeldungen für SQL Managed Instance
- Annehmen der Identität von Azure AD Benutzern in Azure SQL
- Migrieren von SQL Server-Benutzern, die Windows-Authentifizierung verwenden, zu SQL Managed Instance mit Azure AD-Authentifizierung (mit dem Befehl [ALTER USER (Transact-SQL)](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-mi-current#d-map-the-user-in-the-database-to-an-azure-ad-login-after-migration))
- Ändern des Azure AD-Administrators für SQL Managed Instance
- Ermöglichen, dass [Dienstprinzipale (Anwendungen)](authentication-aad-service-principal.md) Azure AD-Benutzer in Azure SQL erstellen können

## <a name="assigning-the-directory-readers-role"></a>Zuweisen der Rolle „Verzeichnisleseberechtigte“

Um einer Identität die Rolle [**Verzeichnisleseberechtigte**](../../active-directory/roles/permissions-reference.md#directory-readers) zuzuweisen, ist ein Benutzer mit den Berechtigungen [Globaler Administrator](../../active-directory/roles/permissions-reference.md#global-administrator--company-administrator) oder [Administrator für privilegierte Rollen](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) erforderlich. Benutzer, die SQL-Datenbank, SQL Managed Instance oder Azure Synapse häufig verwalten oder bereitstellen, verfügen möglicherweise nicht über Zugriff auf diese hochprivilegierten Rollen. Dies kann häufig zu Komplikationen für Benutzer führen, die ungeplante Azure SQL-Ressourcen erstellen oder Hilfe von hochprivilegierten Rollenmitgliedern benötigen, auf die in großen Organisationen häufig nicht zugegriffen werden kann.

Bei SQL Managed Instance muss die Rolle **Verzeichnisleseberechtigte** der Identität der verwalteten Instanz zugewiesen werden, damit Sie [einen Azure AD-Administrator für die verwaltete Instanz einrichten](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) können. 

Bei SQL-Datenbank und Azure Synapse ist die Zuweisung der Rolle **Verzeichnisleseberechtigte** zur Serveridentität nicht erforderlich, wenn Sie einen Azure AD-Administrator für den logischen Server einrichten. Wenn Sie allerdings die Erstellung eines Azure AD-Objekts in SQL-Datenbank oder Azure Synapse im Auftrag einer Azure AD-Anwendung ermöglichen möchten, ist die Rolle **Verzeichnisleseberechtigte** erforderlich. Ist die Rolle nicht der Identität des logischen SQL-Servers zugewiesen, tritt beim Erstellen von Azure AD-Benutzern in Azure SQL ein Fehler auf. Weitere Informationen finden Sie unter [Azure Active Directory-Dienstprinzipal mit Azure SQL](authentication-aad-service-principal.md).

## <a name="granting-the-directory-readers-role-to-an-azure-ad-group"></a>Erteilen der Rolle „Verzeichnisleseberechtigter“ für eine Azure AD-Gruppe

In der **öffentlichen Vorschau** kann jetzt ein [Globaler Administrator](../../active-directory/roles/permissions-reference.md#global-administrator--company-administrator) oder [Administrator für privilegierte Rollen](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) eine Azure AD-Gruppe erstellen und der Gruppe die Berechtigung [**Verzeichnisleseberechtigter**](../../active-directory/roles/permissions-reference.md#directory-readers) zuweisen. Dadurch wird der Zugriff auf die Azure AD Graph-API für Mitglieder dieser Gruppe ermöglicht. Außerdem können Azure AD-Benutzer, die Besitzer dieser Gruppe sind, neue Mitglieder für diese Gruppe zuweisen, einschließlich Identitäten der logischen Azure SQL-Server.

Diese Lösung erfordert immer noch einen hochprivilegierten Benutzer („Globaler Administrator“ oder „Administrator für privilegierte Rollen“), um eine Gruppe zu erstellen und Benutzer als eine einmalige Aktivität zuzuweisen, aber die Azure AD-Gruppenbesitzer können in Zukunft weitere Mitglieder zuweisen. Damit entfällt in Zukunft die Notwendigkeit, einen Benutzer mit hohen Privilegien in die Konfiguration aller SQL-Datenbanken, SQL Managed Instances oder Azure Synapse-Server in ihrem Azure AD-Mandanten einzubeziehen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Zuweisen der Rolle „Verzeichnisleseberechtigte“ zu einer Azure AD-Gruppe und Verwalten von Rollenzuweisungen](authentication-aad-directory-readers-role-tutorial.md)