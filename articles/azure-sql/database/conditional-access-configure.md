---
title: Bedingter Zugriff
description: Hier erfahren Sie, wie Sie den bedingten Zugriff für Azure SQL-Datenbank, Azure SQL Managed Instance und Azure Synapse Analytics konfigurieren.
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 04/28/2020
tag: azure-synpase
ms.openlocfilehash: eae7d0a34d6bd76dae5998f05f9d64e0d40f7d9b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321608"
---
# <a name="conditional-access-with-azure-sql-database-and-azure-synapse-analytics"></a>Bedingter Zugriff mit Azure SQL-Datenbank und Azure Synapse Analytics

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Azure SQL-Datenbank](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) und [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) unterstützen den bedingten Zugriff von Microsoft.

In den folgenden Schritten wird veranschaulicht, wie Sie Azure SQL-Datenbank, SQL Managed Instance oder Azure Synapse konfigurieren, um eine Richtlinie für bedingten Zugriff zu erzwingen.  

## <a name="prerequisites"></a>Voraussetzungen

- Sie müssen Azure SQL-Datenbank, Azure SQL Managed Instance oder einen dedizierten SQL-Pool in Azure Synapse konfigurieren, um die Azure Active Directory-Authentifizierung (Azure AD) zu unterstützen. Informationen zu den jeweiligen Schritten finden Sie unter [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit der SQL-Datenbank oder Azure Synapse](authentication-aad-configure.md).  
- Wenn die mehrstufige Authentifizierung (MFA) aktiviert ist, müssen Sie mithilfe eines unterstützten Tools eine Verbindung herstellen, z. B. die neueste Version von SSMS (SQL Server Management Studio). Weitere Informationen finden Sie unter [Konfigurieren der mehrstufigen Authentifizierung in Azure SQL-Datenbank für SQL Server Management Studio](authentication-mfa-ssms-configure.md).  

## <a name="configure-conditional-access"></a>Konfigurieren des bedingten Zugriffs

> [!NOTE]
> Im folgenden Beispiel wird Azure SQL-Datenbank verwendet, aber Sie sollten das entsprechende Produkt auswählen, für das Sie den bedingten Zugriff konfigurieren möchten.

1. Melden Sie sich beim Azure-Portal an, klicken Sie auf **Azure Active Directory** , und wählen Sie dann **Bedingter Zugriff** aus. Weitere Informationen finden Sie unter [Technische Referenz zum bedingten Azure Active Directory-Zugriff](../../active-directory/conditional-access/concept-conditional-access-conditions.md).  
   ![Blatt „Bedingter Zugriff“](./media/conditional-access-configure/conditional-access-blade.png)

2. Klicken Sie auf dem Blatt **Richtlinien für bedingten Zugriff** auf **Neue Richtlinie** , geben Sie einen Namen ein, und klicken Sie dann auf **Regeln konfigurieren**.  
3. Klicken Sie unter **Zuweisungen** auf **Benutzer und Gruppen** , aktivieren Sie **Benutzer und Gruppen auswählen** , und wählen Sie dann den Benutzer oder die Gruppe für „Bedingter Zugriff“ aus. Klicken Sie auf **Auswählen** und dann auf **Fertig** , um Ihre Auswahl zu bestätigen.  
   ![Auswählen von Benutzern und Gruppen](./media/conditional-access-configure/select-users-and-groups.png)  

4. Wählen Sie **Cloud-Apps** , und klicken Sie auf **Apps auswählen**. Alle für „Bedingter Zugriff“ verfügbaren Apps werden angezeigt. Wählen Sie **Azure SQL-Datenbank** , und klicken Sie unten auf **Auswählen** und dann auf **Fertig**.  
   ![Auswählen der SQL-Datenbank](./media/conditional-access-configure/select-sql-database.png)  
   Wenn Sie die im dritten Screenshot dargestellte Option **Azure SQL-Datenbank** nicht finden können, führen Sie folgende Schritte aus:
   - Verwenden Sie SSMS mit einem Azure AD-Administratorkonto, um eine Verbindung mit Ihrer Datenbank in Azure SQL-Datenbank herzustellen.  
   - Führen Sie `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER` aus.  
   - Melden Sie sich bei Azure AD an, und überprüfen Sie, ob Azure SQL-Datenbank, SQL Managed Instance oder Azure Synapse unter den Anwendungen in Ihrer Azure AD-Instanz aufgeführt wird.  

5. Wählen Sie **Zugriffskontrollen** und **Gewähren** , und aktivieren Sie dann die Richtlinie, die angewendet werden soll. In diesem Beispiel wählen wir **Mehrstufige Authentifizierung anfordern**.  
   ![Auswählen von „Zugriff gewähren“](./media/conditional-access-configure/grant-access.png)  

## <a name="summary"></a>Zusammenfassung

Mithilfe von Azure AD Premium erzwingt die ausgewählte Anwendung (Azure SQL-Datenbank) nun die ausgewählte Richtlinie für bedingten Zugriff ( **Mehrstufige Authentifizierung anfordern** ).

Bei Fragen zu Azure SQL-Datenbank und Azure Synapse bezüglich der mehrstufigen Authentifizierung wenden Sie sich unter <MFAforSQLDB@microsoft.com> an uns.  

## <a name="next-steps"></a>Nächste Schritte  

Ein Tutorial finden Sie unter [Schützen Ihrer Datenbank in Azure SQL-Datenbank](secure-database-tutorial.md).