---
title: Aktivieren von AAD für Azure-SSIS Integration Runtime
description: In diesem Artikel wird beschrieben, wie Sie die Azure Active Directory-Authentifizierung mit der verwalteten Identität für Azure Data Factory aktivieren, um Azure-SSIS Integration Runtime zu erstellen.
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 07/09/2020
ms.openlocfilehash: cd3f590e1869b28f0ac08ce98da32a98160e4e86
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392730"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Aktivieren der Azure Active Directory-Authentifizierung für Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel erfahren Sie, wie Sie Azure Active Directory-Authentifizierung (Azure AD) mit der verwalteten Identität für Ihre Azure Data Factory (ADF) aktivieren und anstelle von herkömmlichen Authentifizierungsmethoden (wie SQL-Authentifizierung) verwenden, um Folgendes zu tun:

- Erstellen einer Azure-SSIS Integration Runtime (IR), die wiederum die SSIS-Katalogdatenbank (SSISDB) in SQL-Datenbank-Server oder SQL Managed Instance in Ihrem Namen bereitstellt.

- Herstellen einer Verbindung mit verschiedenen Azure-Ressourcen beim Ausführen von SSIS-Paketen in der Azure-SSIS IR.

Weitere Informationen zur verwalteten Identität für Ihre ADF-Instanz finden Sie unter [Verwaltete Identität für Azure Data Factory](./data-factory-service-identity.md).

> [!NOTE]
>
> - In diesem Szenario wird die Azure AD-Authentifizierung mit der verwalteten Identität für Ihre ADF-Instanz nur bei der Erstellung und den nachfolgenden Startvorgängen Ihrer SSIS IR verwendet, die wiederum zum Bereitstellen der SSISDB und dem Herstellen einer Verbindung mit dieser verwendet wird. Für Ausführungen von SSIS-Paketen stellt Ihre SSIS Integration Runtime trotzdem eine Verbindung mit der SSISDB mithilfe der SQL-Authentifizierung her. Dabei werden vollständig verwaltete Konten genutzt, die während der Bereitstellung der SSISDB erstellt wurden.
> - Wenn Sie bereits eine SSIS IR mit der SQL-Authentifizierung erstellt haben, können Sie die IR gegenwärtig nicht mit PowerShell für die Verwendung der Azure AD-Authentifizierung neu konfigurieren. Dies ist jedoch im Azure-Portal oder der ADF-App möglich. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Aktivieren von Azure AD in Azure SQL-Datenbank

SQL-Datenbank unterstützt das Erstellen einer Datenbank mit einem Azure AD-Benutzer. Zunächst müssen Sie eine Azure AD-Gruppe mit der verwalteten Identität für Ihre ADF als Mitglied erstellen. Anschließend müssen Sie einen Azure AD-Benutzer als Active Directory-Administrator für Ihre SQL-Datenbank-Instanz festlegen und dann in SQL Server Management Studio (SSMS) eine Verbindung mit dem Server über diesen Benutzer herstellen. Zum Schluss müssen Sie einen enthaltenen Benutzer erstellen, der die Azure AD-Gruppe darstellt, damit die verwaltete Identität für Ihre ADF von Azure-SSIS IR zum Erstellen von SSISDB in Ihrem Namen verwendet werden kann.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Erstellen einer Azure AD-Gruppe mit der verwalteten Identität für Ihre ADF als Mitglied

Sie können eine vorhandene Azure AD-Gruppe verwenden oder mithilfe von Azure AD PowerShell eine neue erstellen.

1.  Installieren Sie das [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)-Modul.

2.  Melden Sie sich mit `Connect-AzureAD` an, führen Sie das folgende Cmdlet aus, um eine Gruppe zu erstellen, und speichern Sie sie in einer Variablen:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Das Ergebnis sieht etwa wie im folgenden Beispiel aus, das auch den Variablenwert zeigt:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Fügen Sie der Gruppe die verwaltete Identität für Ihre ADF-Instanz hinzu. Sie können die Schritte im Artikel [Verwaltete Identität für Azure Data Factory](./data-factory-service-identity.md) ausführen, um die Objekt-ID der verwalteten Identität des Prinzipals abzurufen (z. B. 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, aber verwenden Sie für diesen Zweck nicht die Anwendungs-ID der verwalteten Identität).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Sie können die Gruppenmitgliedschaft auch anschließend überprüfen.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-sql-database"></a>Konfigurieren der Azure AD-Authentifizierung für SQL-Datenbank

Mit den folgenden Schritten können Sie die [Azure AD-Authentifizierung mit SQL konfigurieren und verwalten](../azure-sql/database/authentication-aad-configure.md):

1.  Wählen Sie im Azure-Portal im Navigationsbereich links **Alle Dienste** -> **SQL-Server** aus.

2.  Wählen Sie in SQL-Datenbank den Server aus, der mit Azure AD-Authentifizierung konfiguriert werden soll.

3.  Klicken Sie auf dem Blatt im Abschnitt **Einstellungen** auf **Active Directory-Administrator**.

4.  Wählen Sie in der Befehlsleiste **Administrator festlegen** aus.

5.  Wählen Sie ein Azure AD-Benutzerkonto als Administrator des Servers und dann **Auswählen** aus.

6.  Wählen Sie in der Befehlsleiste **Speichern** aus.

### <a name="create-a-contained-user-in-sql-database-representing-the-azure-ad-group"></a>Erstellen eines enthaltenen Benutzers in SQL-Datenbank, der die Azure AD-Gruppe darstellt

Für den nächsten Schritt benötigen Sie [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1. Starten Sie SSMS.

2. Geben Sie im Dialogfeld **Mit Server verbinden** im Feld **Servername** den Namen Ihres Servers ein.

3. Wählen Sie im Feld **Authentifizierung** die Option **Active Directory: universell mit MFA-Unterstützung** aus. (Sie können auch die beiden anderen Active Directory-Authentifizierungstypen verwenden. Informationen dazu finden Sie unter [Konfigurieren und Verwalten der Azure AD-Authentifizierung mit SQL](../azure-sql/database/authentication-aad-configure.md).)

4. Geben Sie im Feld **Benutzername** den Namen des Azure AD-Kontos ein, das Sie als Serveradministrator festlegen möchten, z. B. testuser@xxxonline.com.

5. Wählen Sie **Verbinden** aus, und schließen Sie den Anmeldevorgang ab.

6. Erweitern Sie im **Objekt-Explorer** unter **Datenbanken** ->  den Ordner **Systemdatenbanken**.

7. Klicken Sie mit der rechten Maustaste auf die **master**-Datenbank, und wählen Sie **Neue Abfrage** aus.

8. Geben Sie im Abfragefenster den folgenden T-SQL-Befehl ein, und wählen Sie auf der Symbolleiste **Ausführen** aus.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   Der Befehl sollte erfolgreich abgeschlossen und ein enthaltener Benutzer zur Darstellung der Gruppe erstellt werden.

9. Löschen Sie den Inhalt des Abfragefensters, geben Sie den folgenden T-SQL-Befehl ein, und klicken Sie auf der Symbolleiste auf **Ausführen**.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   Der Befehl sollte erfolgreich abgeschlossen werden und dem enthaltenen Benutzer die Möglichkeit gewähren, eine Datenbank (SSISDB) zu erstellen.

10. Wenn Ihre SSISDB mithilfe von SQL-Authentifizierung erstellt wurde und Sie zur Verwendung der Azure AD-Authentifizierung für Ihre Azure-SSIS IR wechseln möchten, um darauf zugreifen zu können, müssen Sie zunächst sicherstellen, dass die Schritte zum Erteilen der Berechtigung für die **Master**-Datenbank erfolgreich abgeschlossen wurden. Klicken Sie dann mit der rechten Maustaste auf die **SSISDB**-Datenbank, und wählen Sie **Neue Abfrage** aus.

11. Geben Sie im Abfragefenster den folgenden T-SQL-Befehl ein, und wählen Sie auf der Symbolleiste **Ausführen** aus.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    Der Befehl sollte erfolgreich abgeschlossen und ein enthaltener Benutzer zur Darstellung der Gruppe erstellt werden.

12. Löschen Sie den Inhalt des Abfragefensters, geben Sie den folgenden T-SQL-Befehl ein, und klicken Sie auf der Symbolleiste auf **Ausführen**.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    Der Befehl sollte erfolgreich abgeschlossen werden und dem in der Datenbank enthaltenen Benutzer die Möglichkeit gewähren, auf die SSISDB zuzugreifen.

## <a name="enable-azure-ad-on-sql-managed-instance"></a>Aktivieren von Azure AD für SQL Managed Instance

SQL Managed Instance unterstützt das direkte Erstellen einer Datenbank mit der verwalteten Identität für Ihre ADF. Sie müssen die verwaltete Identität für Ihre ADF weder mit einer Azure AD-Gruppe verknüpfen noch einen enthaltenen Benutzer zur Darstellung dieser Gruppe in SQL Managed Instance erstellen.

### <a name="configure-azure-ad-authentication-for-azure-sql-managed-instance"></a>Konfigurieren der Azure AD-Authentifizierung für Azure SQL Managed Instance

Führen Sie die unter [Bereitstellen eines Azure Active Directory-Administrators für SQL Managed Instance](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) beschriebenen Schritte aus.

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-sql-managed-instance"></a>Hinzufügen der verwalteten Identität für Ihre ADF als Benutzer in SQL Managed Instance

Für den nächsten Schritt benötigen Sie [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Starten Sie SSMS.

2.  Stellen Sie über ein SQL Server-Konto, das Berechtigungen als **Systemadministrator** hat, eine Verbindung mit SQL Managed Instance her. Dabei handelt es sich um eine temporäre Einschränkung, die aufgehoben wird, sobald Azure AD-Serverprinzipale (Anmeldungen) für Azure SQL Managed Instance allgemein verfügbar werden. Wenn Sie versuchen, die Anmeldung mit einem Azure AD-Administratorkonto zu erstellen, wird der folgende Fehler angezeigt: Meldung 15247, Ebene 16, Status 1, Zeile 1: Der Benutzer besitzt keine Berechtigung zum Ausführen dieser Aktion.

3.  Erweitern Sie im **Objekt-Explorer** unter **Datenbanken** ->  den Ordner **Systemdatenbanken**.

4.  Klicken Sie mit der rechten Maustaste auf die **master**-Datenbank, und wählen Sie **Neue Abfrage** aus.

5.  Führen Sie im Abfragefenster das folgende T-SQL-Skript aus, um die verwaltete Identität für Ihre ADF als Benutzer hinzuzufügen.

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    Der Befehl sollte erfolgreich abgeschlossen werden und der verwalteten Identität für Ihre ADF die Möglichkeit gewähren, eine Datenbank (SSISDB) zu erstellen.

6.  Wenn Ihre SSISDB mithilfe von SQL-Authentifizierung erstellt wurde und Sie zur Verwendung der Azure AD-Authentifizierung für Ihre Azure-SSIS IR wechseln möchten, um darauf zugreifen zu können, müssen Sie zunächst sicherstellen, dass die Schritte zum Erteilen der Berechtigung für die **Master**-Datenbank erfolgreich abgeschlossen wurden. Klicken Sie dann mit der rechten Maustaste auf die **SSISDB**-Datenbank, und wählen Sie **Neue Abfrage** aus.

7.  Geben Sie im Abfragefenster den folgenden T-SQL-Befehl ein, und wählen Sie auf der Symbolleiste **Ausführen** aus.

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    Der Befehl sollte erfolgreich abgeschlossen werden und der verwalteten Identität für Ihre ADF die Möglichkeit gewähren, auf die SSISDB zuzugreifen.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Bereitstellen von Azure-SSIS IR im Azure-Portal oder der ADF-App

Wenn Sie die Azure-SSIS IR im Azure-Portal oder der ADF-App bereitstellen, aktivieren Sie auf der Seite **SQL-Einstellungen** die Option **AAD-Authentifizierung mit der verwalteten Identität für Ihre ADF verwenden**. Im folgenden Screenshot werden die Einstellungen für die IR mit SQL-Datenbank zum Hosten von SSISDB gezeigt. Für die IR mit SQL Managed Instance zum Hosten von SSISDB sind die Einstellungen **Catalog Database Service Tier** (Katalogdatenbank-Dienstebene) und **Zugriff auf Azure-Dienste erlauben** nicht zutreffend, während andere Einstellungen identisch sind.

Weitere Informationen zum Erstellen einer Azure-SSIS IR finden Sie im Artikel [Erstellen der Azure-SSIS Integration Runtime in Azure Data Factory](./create-azure-ssis-integration-runtime.md).

![Einstellungen für die Azure SSIS Integration Runtime](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Bereitstellen von Azure-SSIS IR mit PowerShell

Um Ihre Azure SSIS IR mit PowerShell bereitzustellen, führen Sie folgende Schritte aus:

1.  Installieren Sie das [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018)-Modul.

2.  Legen Sie im Skript den `CatalogAdminCredential`-Parameter nicht fest. Beispiel:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Description $AzureSSISDescription `
                                               -Type Managed `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -Edition $AzureSSISEdition `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
    ```

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>Ausführen von SSIS-Paketen mit Authentifizierung der verwalteten Identität

Wenn Sie SSIS-Pakete in der Azure-SSIS IR ausführen, können Sie die Authentifizierung der verwalteten Identität zum Herstellen einer Verbindung mit verschiedenen Azure-Ressourcen verwenden. Zurzeit wird die Authentifizierung der verwalteten Identität bereits in den folgenden Verbindungs-Managern unterstützt.

- [OLE DB-Verbindungs-Manager](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET-Verbindungs-Manager](/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Azure Storage-Verbindungs-Manager](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
