---
title: 'PowerShell und Befehlszeilenschnittstelle: Aktivieren von TDE für SQL – mit Azure Key Vault – BYOK (Bring Your Own Key) – Azure SQL-Datenbank | Microsoft-Dokumentation'
description: Erfahren Sie, wie eine Azure SQL-Datenbank- und Data Warehouse-Instanz mithilfe von PowerShell oder CLI für die Verwendung von Transparent Data Encryption (TDE) zur Verschlüsselung ruhender Daten konfiguriert wird.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: c42c6175512105de38a29be260c370851e152137
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60330872"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell und Befehlszeilenschnittstelle: Aktivieren von Transparent Data Encryption mithilfe eines vom Kunden verwalteten Azure Key Vault-Schlüssels

In diesem Artikel wird die Verwendung eines Schlüssels aus Azure Key Vault für Transparent Data Encryption (TDE) auf einer SQL-Datenbank- oder Data Warehouse-Instanz erläutert. Weitere Informationen zur Integration von TDE mit Azure Key Vault und BYOK-Unterstützung (Bring Your Own Key) finden Sie unter [TDE mit vom Kunden verwalteten Schlüsseln in Azure Key Vault](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>Voraussetzungen für PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von der Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az- und in den AzureRm-Modulen sind im Wesentlichen identisch.

- Sie müssen über ein Azure-Abonnement verfügen und ein Administrator für dieses Abonnement sein.
- [Empfohlen, aber optional] Sie sollten über ein Hardwaresicherheitsmodul (HSM) oder einen lokalen Schlüsselspeicher zum Erstellen einer lokalen Kopie des Schlüsselmaterials der TDE-Schutzvorrichtung verfügen.
- Azure PowerShell Version muss installiert sein und ausgeführt werden. 
- Erstellen Sie eine Azure Key Vault-Instanz und einen Schlüssel zur Verwendung für TDE.
  - [PowerShell-Anweisungen aus Key Vault](../key-vault/key-vault-overview.md)
  - [Anweisungen zur Verwendung eines Hardwaresicherheitsmodells (HSM) und Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - Der Schlüsseltresor muss die folgende Eigenschaft aufweisen, um für TDE verwendet werden zu können:
  - [soft-delete](../key-vault/key-vault-ovw-soft-delete.md)
  - [Verwenden des vorläufigen Löschens in Key Vault mit PowerShell](../key-vault/key-vault-soft-delete-powershell.md) 
- Der Schlüssel muss die folgenden Attribute aufweisen, um für TDE verwendet werden zu können:
   - Kein Ablaufdatum
   - Nicht deaktiviert
   - Fähigkeit zum Ausführen des *get*-, *wrap key*- und *unwrap key*-Vorgangs

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>Schritt 1: Zuweisen einer Azure AD-Identität zu einem Server 

Wenn bereits ein Server vorhanden ist, fügen Sie ihm mit dem folgenden Code eine Azure AD-Identität hinzu:

   ```powershell
   $server = Set-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

Wenn Sie einen Server erstellen, verwenden Sie das Cmdlet [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) mit dem Tag „-Identity“, um während der Servererstellung eine Azure AD-Identität hinzuzufügen:

   ```powershell
   $server = New-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Schritt 2: Gewähren von Key Vault-Berechtigungen für Ihren Server

Verwenden Sie das Cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), um Ihrem Server Zugriff auf den Schlüsseltresor zu gewähren, bevor Sie einen Schlüssel daraus für TDE verwenden.

   ```powershell
   Set-AzKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Schritt 3: Hinzufügen eines Key Vault-Schlüssels zum Server und Festlegen der TDE-Schutzvorrichtung

- Fügen Sie dem Server mit dem Cmdlet [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) den Schlüssel aus Key Vault hinzu.
- Legen Sie den Schlüssel mit dem Cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) als TDE-Schutzvorrichtung für alle Serverressourcen fest.
- Vergewissern Sie sich mit dem Cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector), dass die TDE-Schutzvorrichtung wie vorgesehen konfiguriert wurde.

> [!Note]
> Die kombinierte Länge für den Schlüsseltresornamen und Schlüsselnamen darf 94 Zeichen nicht überschreiten.
> 

>[!Tip]
>Beispiel für die KeyId-Eigenschaft in Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>Schritt 4: Aktivieren von TDE 

Aktivieren Sie TDE mit dem Cmdlet [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption).

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

Nun ist TDE für die Datenbank oder Data Warehouse-Instanz mit einem Verschlüsselungsschlüssel in Key Vault aktiviert.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Schritt 5: Überprüfen des Verschlüsselungsstatus und der Verschlüsselungsaktivität

Rufen Sie den Verschlüsselungsstatus mit dem Cmdlet [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) ab, und überprüfen Sie den Verschlüsselungsfortschritt für eine Datenbank oder Data Warehouse-Instanz mit dem Cmdlet [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity).

   ```powershell
   # Get the encryption state
   Get-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>Weitere nützliche PowerShell-Cmdlets

- Aktivieren Sie TDE mit dem Cmdlet [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption).

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled”
   ```
 
- Mit dem Cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) geben Sie die Liste der Key Vault-Schlüssel zurück, die dem Server hinzugefügt wurden.

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- Mit dem Cmdlet [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) entfernen Sie einen Key Vault-Schlüssel vom Server.

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>Problembehandlung

Überprüfen Sie Folgendes, wenn ein Problem auftritt:
- Wenn der Key Vault nicht gefunden werden kann, stellen Sie mithilfe des Cmdlets [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) sicher, dass Sie sich im richtigen Abonnement befinden.

   ```powershell
   Get-AzSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- Wenn der neue Schlüssel dem Server nicht hinzugefügt oder nicht als TDE-Schutzvorrichtung aktualisiert werden kann, überprüfen Sie Folgendes:
   - Der Schlüssel darf kein Ablaufdatum haben.
   - Für den Schlüssel müssen die *get*-, *wrap key*- und *unwrap key*-Vorgänge aktiviert sein.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie die TDE-Schutzvorrichtung eines Servers rotieren, um Sicherheitsanforderungen zu erfüllen: [Rotieren der Transparent Data Encryption-Schutzvorrichtung mithilfe von PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Im Fall eines Sicherheitsrisikos können Sie hier nachsehen, wie Sie eine möglicherweise kompromittierte TDE-Schutzvorrichtung entfernen: [Entfernen eines möglicherweise kompromittierten Schlüssels](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md). 

## <a name="prerequisites-for-cli"></a>Voraussetzungen für CLI

- Sie müssen über ein Azure-Abonnement verfügen und ein Administrator für dieses Abonnement sein.
- [Empfohlen, aber optional] Sie sollten über ein Hardwaresicherheitsmodul (HSM) oder einen lokalen Schlüsselspeicher zum Erstellen einer lokalen Kopie des Schlüsselmaterials der TDE-Schutzvorrichtung verfügen.
- Befehlszeilenschnittstellen-Version 2.0 oder höher. Informationen zum Installieren der aktuellen Version und Verbindungsaufbau mit Ihrem Azure-Abonnement, finden Sie unter [Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 
- Erstellen Sie eine Azure Key Vault-Instanz und einen Schlüssel zur Verwendung für TDE.
  - [Verwalten von Key Vault mit CLI 2.0](../key-vault/key-vault-manage-with-cli2.md)
  - [Anweisungen zur Verwendung eines Hardwaresicherheitsmodells (HSM) und Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - Der Schlüsseltresor muss die folgende Eigenschaft aufweisen, um für TDE verwendet werden zu können:
  - [soft-delete](../key-vault/key-vault-ovw-soft-delete.md)
  - [Verwenden des vorläufigen Löschens in Key Vault mit der CLI](../key-vault/key-vault-soft-delete-cli.md) 
- Der Schlüssel muss die folgenden Attribute aufweisen, um für TDE verwendet werden zu können:
   - Kein Ablaufdatum
   - Nicht deaktiviert
   - Fähigkeit zum Ausführen des *get*-, *wrap key*- und *unwrap key*-Vorgangs
   
## <a name="step-1-create-a-server-with-an-azure-ad-identity"></a>Schritt 1: Erstellen eines Servers mit einer Azure AD-Identität
      cli
      # create server (with identity) and database
      az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
      az sql db create --name <dbname> --server <servername> --resource-group <rgname>  
 
 
>[!Tip]
>Behalten Sie die „principalID“ aus der Servererstellung bei. Dies ist die Objekt-ID, die zum Zuweisen von Key Vault-Berechtigungen im nächsten Schritt verwendet wird.
>
 
## <a name="step-2-grant-key-vault-permissions-to-the-logical-sql-server"></a>Schritt 2: Gewähren von Key Vault-Berechtigungen für den logischen SQL Server
      cli
      # create key vault, key and grant permission
       az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
       az keyvault key create --name <keyname> --vault-name <kvname> --protection software
       az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get 


>[!Tip]
>Behalten Sie den Schlüssel-URI oder die KeyID für den nächsten Schritt bei, zum Beispiel: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
 
       
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Schritt 3: Hinzufügen eines Key Vault-Schlüssels zum Server und Festlegen der TDE-Schutzvorrichtung
  
     cli
     # add server key and update encryption protector
     az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
     az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>

        
  > [!Note]
> Die kombinierte Länge für den Schlüsseltresornamen und Schlüsselnamen darf 94 Zeichen nicht überschreiten.
> 

  
## <a name="step-4-turn-on-tde"></a>Schritt 4: Aktivieren von TDE 
      cli
      # enable encryption
      az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled 
      

Nun ist TDE für die Datenbank oder Data Warehouse-Instanz mit einem vom Kunden verwalteten Verschlüsselungsschlüssel in Azure Key Vault aktiviert.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Schritt 5: Überprüfen des Verschlüsselungsstatus und der Verschlüsselungsaktivität

     cli
      # get encryption scan progress
      az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

      # get whether encryption is on or off
      az sql db tde show --database <dbname> --server <servername> --resource-group <rgname> 

## <a name="sql-cli-references"></a>SQL-CLI-Referenz

https://docs.microsoft.com/cli/azure/sql 

https://docs.microsoft.com/cli/azure/sql/server/key 

https://docs.microsoft.com/cli/azure/sql/server/tde-key 

https://docs.microsoft.com/cli/azure/sql/db/tde 

