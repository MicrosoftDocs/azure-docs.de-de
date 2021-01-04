---
title: Entfernen der TDE-Schutzvorrichtung (PowerShell und Azure CLI)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Erfahren Sie, wie Sie auf eine potenziell gefährdete TDE-Schutzvorrichtung für Azure SQL-Datenbank oder Azure Synapse Analytics mit TDE und BYOK-Unterstützung (Bring Your Own Key) reagieren können.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: 31298be4d50c7f562e2e2b9adbf889b165b197e5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461868"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Entfernen einer Transparent Data Encryption (TDE)-Schutzvorrichtung mithilfe von PowerShell
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


In diesem Thema wird beschrieben, wie Sie auf eine potenziell gefährdete TDE-Schutzvorrichtung für eine Azure SQL-Datenbank- oder Azure Synapse Analytics-Instanz reagieren können, die TDE mit vom Kunden verwalteten Schlüsseln in Azure Key Vault mit BYOK-Unterstützung (Bring Your Own Key) verwendet. Weitere Informationen zu BYOK-Unterstützung für TDE finden Sie auf der [Übersichtsseite](transparent-data-encryption-byok-overview.md).

> [!CAUTION]
> Die in diesem Artikel beschriebenen Verfahren sollten nur in Ausnahmefällen oder in Testumgebungen ausgeführt werden. Lesen Sie die Schritte sorgfältig, da das Löschen von aktiv verwendeten TDE-Schutzvorrichtungen aus Azure Key Vault zur **Nichtverfügbarkeit der Datenbank** führen kann.

Wenn der Verdacht besteht, dass ein Schlüssel kompromittiert ist, d.h ein Dienst oder Benutzer unautorisierten Zugriff auf den Schlüssel hat, empfiehlt es sich, den Schlüssel zu löschen.

Beachten Sie: Sobald die TDE-Schutzvorrichtung in Key Vault gelöscht ist, beginnen alle verschlüsselten Datenbanken nach etwa 10 Minuten, mit einer entsprechenden Fehlermeldung alle Verbindungen zu verweigern und ihren Status in [Kein Zugriff](./transparent-data-encryption-byok-overview.md#inaccessible-tde-protector) zu ändern.

In diesem Leitfaden werden zwei Ansätze behandelt, die jeweils vom gewünschten Ergebnis nach der Reaktion auf kompromittierte Vorfälle abhängen:

- **Verhindern des Zugriffs** auf die Datenbanken in Azure SQL-Datenbank/Azure Synapse Analytics.
- **Verhindern des Zugriffs** auf die Datenbanken in Azure SQL-Datenbank/Azure Synapse Analytics

## <a name="prerequisites"></a>Voraussetzungen

- Sie müssen über ein Azure-Abonnement verfügen und ein Administrator für dieses Abonnement sein.
- Azure PowerShell muss installiert sein und ausgeführt werden.
- In diesem Leitfaden wird davon ausgegangen, dass Sie bereits einen Schlüssel aus Azure Key Vault als TDE-Schutzvorrichtung für Azure SQL-Datenbank oder Azure Synapse verwenden. Weitere Informationen finden Sie unter [Transparent Data Encryption mit BYOK-Unterstützung](transparent-data-encryption-byok-overview.md).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

 Anweisungen zur Installation des Az-Moduls finden Sie unter [Install Azure PowerShell](/powershell/azure/install-az-ps) (Installieren von Azure PowerShell). Spezifische Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> Das Azure Resource Manager-Modul (RM) von PowerShell wird weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Das AzureRM-Modul erhält mindestens bis Dezember 2020 weiterhin Fehlerbehebungen.  Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch. Weitere Informationen zur Kompatibilität finden Sie in der [Einführung in das neue Azure PowerShell Az-Modul](/powershell/azure/new-azureps-module-az).

# <a name="the-azure-cli"></a>[Die Azure-CLI](#tab/azure-cli)

Informationen zur Installation finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="check-tde-protector-thumbprints"></a>Überprüfen der Fingerabdrücke der TDE-Schutzvorrichtung

In den folgenden Schritten wird beschrieben, wie Sie die Fingerabdrücke der TDE-Schutzvorrichtung überprüfen, die von den virtuellen Protokolldateien (Virtual Log Files, VLF) einer bestimmten Datenbank weiterhin verwendet werden.
Der Fingerabdruck der aktuellen TDE-Schutzvorrichtung für die Datenbank und die Datenbank-ID können gefunden werden durch Ausführung von:

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint]
 FROM [sys].[dm_database_encryption_keys]
```

Die folgende Abfrage gibt die verwendeten VLFs und die entsprechenden Fingerabdrücke der TDE-Schutzvorrichtung zurück. Jeder andere Fingerabdruck bezieht sich auf einen anderen Schlüssel in Azure Key Vault (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

Alternativ können Sie PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Der PowerShell-Befehl **Get-AzureRmSqlServerKeyVaultKey**  stellt den Fingerabdruck der in der Abfrage verwendeten TDE-Schutzvorrichtung bereit, damit Sie sehen können, welche Schlüssel in AKV beibehalten und welche gelöscht werden müssen. Nur die von der Datenbank nicht mehr verwendeten Schlüssel können aus Azure Key Vault sicher gelöscht werden.

# <a name="the-azure-cli"></a>[Die Azure-CLI](#tab/azure-cli)

Der PowerShell-Befehl **az sql server key show**  stellt den Fingerabdruck der in der Abfrage verwendeten TDE-Schutzvorrichtung bereit, damit Sie sehen können, welche Schlüssel in AKV beibehalten und welche gelöscht werden müssen. Nur die von der Datenbank nicht mehr verwendeten Schlüssel können aus Azure Key Vault sicher gelöscht werden.

* * *

## <a name="keep-encrypted-resources-accessible"></a>Bewahren des Zugriffs auf verschlüsselte Ressourcen

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Erstellen Sie einen [neuen Schlüssel in Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Stellen Sie sicher, dass der neue Schlüssel in einem anderen Schlüsseltresor als der möglicherweise kompromittierten TDE-Schutzvorrichtung erstellt wird, da die Zugriffskontrolle auf Tresorebene bereitgestellt wird.

2. Fügen Sie den neuen Schlüssel mithilfe der Cmdlets [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) und [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) dem Server hinzu, und aktualisieren Sie ihn als neue TDE-Schutzvorrichtung des Servers.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Stellen Sie mithilfe des Cmdlets [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) sicher, dass der Server und alle Replikate auf die neue TDE-Schutzvorrichtung aktualisiert wurden.

   > [!NOTE]
   > Es kann einige Minuten dauern, bis sich die neue TDE-Schutzvorrichtung auf alle Datenbanken und sekundären Datenbanken auf dem Server verteilt.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Führen Sie eine [Sicherung des neuen Schlüssels](/powershell/module/az.keyvault/backup-azkeyvaultkey) in Key Vault durch.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Löschen Sie den kompromittierten Schlüssel mithilfe des Cmdlets [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) aus Key Vault.

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Zum künftigen Wiederherstellen eines Schlüssels in Key Vault verwenden Sie das Cmdlet [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey):

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="the-azure-cli"></a>[Die Azure-CLI](#tab/azure-cli)

Eine Befehlsreferenz finden Sie unter [Azure CLI: keyvault](/cli/azure/keyvault/key).

1. Erstellen Sie einen [neuen Schlüssel in Key Vault](/cli/azure/keyvault/key#az-keyvault-key-create). Stellen Sie sicher, dass der neue Schlüssel in einem anderen Schlüsseltresor als der möglicherweise kompromittierten TDE-Schutzvorrichtung erstellt wird, da die Zugriffskontrolle auf Tresorebene bereitgestellt wird.

2. Fügen Sie den neuen Schlüssel zum Server hinzu, und aktualisieren Sie diesen als neue TDE-Schutzvorrichtung des Servers.

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Stellen Sie sicher, dass der Server und alle Replikate mit der neuen TDE-Schutzvorrichtung aktualisiert wurden.

   > [!NOTE]
   > Es kann einige Minuten dauern, bis sich die neue TDE-Schutzvorrichtung auf alle Datenbanken und sekundären Datenbanken auf dem Server verteilt.

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Legen Sie eine Sicherung für den neuen Schlüssel in Azure Key Vault an.

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Löschen Sie den kompromittierten Schlüssel aus Azure Key Vault.

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. So stellen Sie ein Schlüssel in Azure Key Vault zukünftig wieder her:

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="make-encrypted-resources-inaccessible"></a>Verhindern des Zugriffs auf verschlüsselte Ressourcen

1. Löschen Sie die Datenbanken, die durch den möglicherweise kompromittierten Schlüssel verschlüsselt sind.

   Die Datenbank- und Protokolldateien werden automatisch gesichert, sodass eine Point-in-Time-Wiederherstellung der Datenbank jederzeit durchgeführt werden kann (solange Sie den Schlüssel bereitstellen). Die Datenbanken müssen gelöscht werden, bevor eine aktive TDE-Schutzvorrichtung gelöscht wird, um einen möglichen Datenverlust von bis zu zehn Minuten der letzten Transaktionen zu verhindern.

2. Sichern Sie das Schlüsselmaterial der TDE-Schutzvorrichtung in Key Vault.
3. Entfernen Sie den möglicherweise kompromittierten Schlüssel aus Key Vault.

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie die TDE-Schutzvorrichtung eines Servers rotieren, um Sicherheitsanforderungen zu erfüllen: [Rotieren der Transparent Data Encryption-Schutzvorrichtung mithilfe von PowerShell](transparent-data-encryption-byok-key-rotation.md)
- Erste Schritte mit der BYOK-Unterstützung (Bring Your Own Key) für TDE: [Aktivieren von TDE mit BYOK aus Key Vault mithilfe von PowerShell](transparent-data-encryption-byok-configure.md)