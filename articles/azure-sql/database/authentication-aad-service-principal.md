---
title: Azure Active Directory-Dienstprinzipal mit Azure SQL
description: Azure AD-Anwendungen (Dienstprinzipale) unterstützen die Azure AD-Benutzererstellung in Azure SQL-Datenbank, Azure SQL Managed Instance und Azure Synapse Analytics.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/21/2020
ms.openlocfilehash: 6e397242bd699adcba4737014ebbce72aadc8ec2
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92669810"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>Azure Active Directory-Dienstprinzipal mit Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Unterstützung bei der Erstellung von Azure Active Directory (Azure AD)-Benutzern in Azure SQL-Datenbank (SQL-DB) und [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) im Auftrag von Azure AD-Anwendungen (Dienstprinzipale) steht zurzeit in der **öffentlichen Vorschau** zur Verfügung.

> [!NOTE]
> Diese Funktionalität wird für SQL Managed Instance bereits unterstützt.

## <a name="service-principal-azure-ad-applications-support"></a>Unterstützung für Dienstprinzipal (Azure AD-Anwendungen)

Dieser Artikel gilt für Anwendungen, die in Azure AD integriert und Teil der Azure AD-Registrierung sind. Diese Anwendungen benötigen oft Authentifizierungs- und Autorisierungszugriff auf Azure SQL zur Ausführung verschiedener Aufgaben. Dieses Feature in der **öffentlichen Vorschau** ermöglicht Dienstprinzipalen jetzt das Erstellen von Azure AD-Benutzern in SQL-Datenbank und Azure-Synapse. Es gab eine Einschränkung, die das Erstellen von Azure AD-Objekten im Auftrag von Azure AD Anwendungen verhinderte. Diese Einschränkung wurde aufgehoben.

Beim Registrieren einer Azure AD-Anwendung über das Azure-Portal oder mithilfe eines PowerShell-Befehls werden im Azure AD-Mandanten zwei Objekte erstellt:

- ein Anwendungsobjekt
- ein Dienstprinzipalobjekt

Weitere Informationen zu Azure AD-Anwendungen finden Sie unter [Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md) und [Erstellen eines Azure-Dienstprinzipals mit Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Die folgenden Azure AD-Objekte werden von SQL-Datenbank, Azure Synapse und SQL Managed Instance unterstützt:

- Azure AD-Benutzer (verwaltet, Partner und Gast)
- Azure AD-Gruppen (verwaltet und Partner)
- Azure AD-Anwendungen 

Der T-SQL-Befehl `CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER` im Auftrag einer Azure AD-Anwendung wird jetzt bei SQL-Datenbank und Azure Synapse unterstützt.

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>Funktionalität der Azure AD-Benutzererstellung mithilfe von Dienstprinzipalen

Die Unterstützung dieser Funktionalität ist hilfreich in Automatisierungsprozessen für Azure AD-Anwendungen, in denen Azure AD-Objekte in SQL-Datenbank und Azure Synapse ohne menschlichen Eingriff erstellt und verwaltet werden. Dienstprinzipale können ein Azure AD-Administrator für den logischen SQL-Server, als Teil einer Gruppe oder einzelner Benutzer, sein. Die Anwendung kann die Erstellung von Azure AD-Objekten in SQL-Datenbank und Azure Synapse automatisieren, wenn sie als Systemadministrator ausgeführt wird und keine zusätzlichen SQL-Berechtigungen erfordert. Dies ermöglicht eine vollständige Automatisierung der Erstellung eines Datenbankbenutzers. Dieses Feature wird auch für vom System zugewiesene verwaltete Identität und vom Benutzer zugewiesene verwaltete Identität unterstützt. Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="enable-service-principals-to-create-azure-ad-users"></a>Aktivieren von Dienstprinzipalen zum Erstellen von Azure AD-Benutzern

Zum Aktivieren einer Azure AD-Objekterstellung in SQL-Datenbank und Azure Synapse im Auftrag einer Azure AD-Anwendung sind die folgenden Einstellungen erforderlich:

1. Zuweisen der Serveridentität
    - Führen Sie bei einem neuen logischen Azure SQL-Server den folgenden PowerShell-Befehl aus:
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    Weitere Informationen finden Sie unter dem Befehl [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver).

    - Führen Sie bei vorhandenen logischen Azure SQL-Servern den folgenden Befehl aus:
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    Weitere Informationen finden Sie unter dem [AzSqlServer](/powershell/module/az.sql/set-azsqlserver)-Befehl.

    - Führen Sie den Befehl „Get-azsqlserver“ aus, um zu überprüfen, ob die Serveridentität dem Server zugewiesen wurde.

    > [!NOTE]
    > Die Serveridentität kann auch mithilfe von CLI-Befehlen zugewiesen werden. Weitere Informationen finden Sie unter [az sql server create](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create&preserve-view=true) und [az sql server update](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-update&preserve-view=true).

2. Erteilen Sie die Azure AD-Berechtigung [**Verzeichnisleseberechtigte**](../../active-directory/roles/permissions-reference.md#directory-readers) für die Serveridentität, die erstellt oder dem Server zugewiesen wurde.
    - Führen Sie zum Erteilen dieser Berechtigung die Schritte in der Beschreibung für SQL Managed Instance aus, die im folgenden Artikel zu finden ist: [Bereitstellen eines Azure AD-Administrators (SQL Managed Instance)](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance)
    - Der Azure AD-Benutzer, der diese Berechtigung erteilt, muss der Azure AD-Rolle **Globaler Administrator** oder **Administrator für privilegierte Rollen** angehören.

> [!IMPORTANT]
> Die Schritte 1 und 2 müssen in der vorstehenden Reihenfolge ausgeführt werden. Erstellen Sie zuerst die Serveridentität, oder weisen Sie sie zu, und erteilen Sie dann die Berechtigung [**Verzeichnisleseberechtigte**](../../active-directory/roles/permissions-reference.md#directory-readers). Wenn Sie einen dieser Schritte oder beide auslassen, tritt während der Erstellung eines Azure AD-Objekts in Azure SQL im Auftrag einer Azure AD-Anwendung ein Ausführungsfehler auf.
>
> Wenn Sie für das Festlegen oder Löschen des Azure AD-Administrators den Dienstprinzipal verwenden, muss die Anwendung auch über die Berechtigung [Directory.Read.All](/graph/permissions-reference#application-permissions-18) der Anwendungs-API in Azure AD verfügen. Weitere Informationen zu den [erforderlichen Berechtigungen für das Festlegen von Azure AD-Administratoren](authentication-aad-service-principal-tutorial.md#permissions-required-to-set-or-unset-the-azure-ad-admin) sowie Schrittanleitungen zum Erstellen eines Azure AD-Benutzers im Namen einer Azure AD-Anwendung finden Sie im [Tutorial: Erstellen von Azure AD-Benutzern mithilfe von Azure AD-Anwendungen](authentication-aad-service-principal-tutorial.md).
>
> In der **öffentlichen Vorschau** können Sie die Rolle **Verzeichnisleseberechtigte** einer Gruppe in Azure AD zuweisen. Die Gruppenbesitzer können dann die verwaltete Identität als Mitglied dieser Gruppe hinzufügen. Dadurch wird die Notwendigkeit eines **Globalen Administrators** oder **Administrators für privilegierte Rollen** umgangen, um die Rolle **Verzeichnisleseberechtigter** zuzuweisen. Weitere Informationen zu diesem Feature finden Sie unter [Rolle „Verzeichnisleseberechtigte“ in Azure Active Directory für Azure SQL](authentication-aad-directory-readers-role.md).

## <a name="troubleshooting-and-limitations-for-public-preview"></a>Problembehandlung und Einschränkungen für die öffentliche Vorschau

- Beim Erstellen von Azure AD-Objekten in Azure SQL im Auftrag einer Azure AD-Anwendung ohne Aktivierung der Serveridentität und Erteilen der Berechtigung **Verzeichnisleseberechtigte** können die folgenden Fehler auftreten. Der folgende Beispielfehler betrifft eine PowerShell-Befehlsausführung zum Erstellen eines Azure SQL-Datenbankbenutzers `myapp` im Artikel [Tutorial: Erstellen von Azure AD-Benutzern mithilfe von Azure AD-Anwendungen](authentication-aad-service-principal-tutorial.md).
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved.`
    - `User or server identity does not have permission to read from Azure Active Directory.`
      - Führen Sie beim vorstehenden Fehler die Schritte zum [Zuweisen einer Identität zum logischen Azure SQL-Server](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server) und [Zuweisen der Berechtigung „Verzeichnisleseberechtigte“ zur Identität des logischen SQL-Servers](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity) aus.
    > [!NOTE]
    > Die oben aufgeführten Fehlermeldungen werden vor dem Feature GA (General Availability, allgemeine Verfügbarkeit) geändert, um die fehlende Setupanforderung zur Unterstützung der Azure AD-Anwendung eindeutig identifizieren zu können.
- Das Festlegen der Azure AD-Anwendung als Azure AD-Administrator für SQL Managed Instance wird nur mit dem CLI-Befehl und dem PowerShell-Befehl bei [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) oder höher unterstützt. Weitere Informationen finden Sie unter den Befehlen [az sql mi ad-admin create](/cli/azure/sql/mi/ad-admin?view=azure-cli-latest&preserve-view=true#az-sql-mi-ad-admin-create) und [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator). 
    - Wenn Sie zum Festlegen des Azure AD-Administrators das Azure-Portal für SQL Managed Instance verwenden möchten, ist eine mögliche Problemumgehung das Erstellen einer Azure AD-Gruppe. Fügen Sie dieser Gruppe dann den Dienstprinzipal (Azure AD-Anwendung) hinzu, und legen Sie sie als Azure AD-Administrator für die SQL Managed Instance fest.
    - Das Festlegen des Dienstprinzipals (Azure AD-Anwendung) als Azure AD-Administrator für SQL-Datenbank und Azure Synapse wird über das Azure-Portal, mithilfe von [PowerShell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse) und [CLI](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse)-Befehlen unterstützt.
- Die Verwendung einer Azure AD-Anwendung bei einem Dienstprinzipal aus einem anderen Azure AD-Mandanten schlägt beim Zugriff auf SQL-Datenbank oder SQL Managed Instance fehl, die in einem anderen Mandanten erstellt wurden. Ein dieser Anwendung zugewiesener Dienstprinzipal muss von demselben Mandanten wie der logische SQL-Server oder Managed Instance sein.
- Das [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0)-Modul oder höher ist erforderlich, wenn Sie PowerShell verwenden, um eine einzelne Azure AD Anwendung als Azure AD-Administrator für Azure SQL einzurichten. Stellen Sie sicher, dass Sie ein Upgrade auf das neueste Modul ausgeführt haben.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Erstellen von Azure AD-Benutzern mithilfe von Azure AD-Anwendungen](authentication-aad-service-principal-tutorial.md)