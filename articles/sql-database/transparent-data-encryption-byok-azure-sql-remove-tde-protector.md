---
title: 'PowerShell: Entfernen einer TDE-Schutzvorrichtung – Azure SQL-Datenbank | Microsoft-Dokumentation'
description: Anleitung zum Reagieren auf eine möglicherweise kompromittierte TDE-Schutzvorrichtung für eine Azure SQL-Datenbank- oder Data Warehouse-Instanz, die TDE mit Bring Your Own Key (BYOK)-Unterstützung verwendet
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
ms.openlocfilehash: ad7e760bf84ee08e3928164432564fb23c10d211
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60330651"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Entfernen einer Transparent Data Encryption (TDE)-Schutzvorrichtung mithilfe von PowerShell

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az- und den AzureRm-Modulen sind im Wesentlichen identisch.

- Sie müssen über ein Azure-Abonnement verfügen und ein Administrator für dieses Abonnement sein.
- Azure PowerShell muss installiert sein und ausgeführt werden. 
- In diesem Leitfaden wird davon ausgegangen, dass Sie bereits einen Schlüssel aus Azure Key Vault als TDE-Schutzvorrichtung für eine Azure SQL-Datenbank- oder Data Warehouse-Instanz verwenden. Weitere Informationen finden Sie unter [Transparent Data Encryption mit BYOK-Unterstützung](transparent-data-encryption-byok-azure-sql.md).

## <a name="overview"></a>Übersicht

In dieser Anleitung wird beschrieben, wie auf eine möglicherweise kompromittierte TDE-Schutzvorrichtung für eine Azure SQL-Datenbank- oder Data Warehouse-Instanz zu reagieren ist, die TDE mit vom Kunden verwalteten Schlüsseln in Azure Key Vault mit BYOK-Unterstützung (Bring Your Own Key) verwendet. Weitere Informationen zu BYOK-Unterstützung für TDE finden Sie auf der [Übersichtsseite](transparent-data-encryption-byok-azure-sql.md). 

Die folgenden Verfahren sollten nur in Ausnahmefällen oder in Testumgebungen durchgeführt werden. Lesen Sie den Leitfaden sorgfältig, da das Löschen von aktiv verwendeten TDE-Schutzvorrichtungen aus Azure Key Vault zu **Datenverlust** führen kann. 

Wenn der Verdacht besteht, dass ein Schlüssel kompromittiert ist, d.h ein Dienst oder Benutzer unautorisierten Zugriff auf den Schlüssel hat, empfiehlt es sich, den Schlüssel zu löschen.

Denken Sie daran: Sobald die TDE-Schutzvorrichtung aus Key Vault gelöscht ist, **werden alle Verbindungen mit den verschlüsselten Datenbanken unter dem Server blockiert, und diese Datenbanken werden offline geschaltet und innerhalb von 24 Stunden gelöscht**. Auf alte Sicherungen, die mit dem kompromittierten Schlüssel verschlüsselt wurden, kann nicht mehr zugegriffen werden.

In den folgenden Schritten wird beschrieben, wie Sie die Fingerabdrücke der TDE-Schutzvorrichtung überprüfen, die von den virtuellen Protokolldateien (Virtual Log Files, VLF) einer bestimmten Datenbank weiterhin verwendet werden. Der Fingerabdruck der aktuellen TDE-Schutzvorrichtung für die Datenbank und die Datenbank-ID können gefunden werden durch Ausführung von: SELECT [database_id],       [encryption_state], [encryptor_type], /*asymmetrischer Schlüssel bedeutet „AKV“, „Zertifikat“ bedeutet „vom Dienst verwaltete Schlüssel“* / [encryptor_thumbprint], FROM [sys].[dm_database_encryption_keys] 
 
Die folgende Abfrage gibt die verwendeten VLFs und die der Verschlüsselung entsprechenden Fingerabdrücke zurück. Jeder andere Fingerabdruck bezieht sich auf einen anderen Schlüssel in Azure Key Vault (AKV): SELECT * FROM sys.dm_db_log_info (database_id) 

Der PowerShell-Befehl „Get-AzureRmSqlServerKeyVaultKey“ stellt den Fingerabdruck der in der Abfrage verwendeten TDE-Schutzvorrichtung bereit, damit Sie sehen können, welche Schlüssel in AKV beibehalten und welche gelöscht werden müssen. Nur die von der Datenbank nicht mehr verwendeten Schlüssel können aus Azure Key Vault sicher gelöscht werden.

In diesem Leitfaden werden zwei Ansätze behandelt, die jeweils vom gewünschten Ergebnis nach der Reaktion auf Vorfälle abhängen:

- Auf Azure SQL-Datenbank- bzw. Data Warehouse-Instanzen soll weiterhin **Zugriff** möglich sein
- Auf Azure SQL-Datenbank- bzw. Data Warehouse-Instanzen soll **kein Zugriff** mehr möglich sein

## <a name="to-keep-the-encrypted-resources-accessible"></a>Zugriff auf die verschlüsselten Ressourcen weiterhin ermöglichen

1. Erstellen Sie einen [neuen Schlüssel in Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Stellen Sie sicher, dass der neue Schlüssel in einem anderen Schlüsseltresor als der möglicherweise kompromittierten TDE-Schutzvorrichtung erstellt wird, da die Zugriffskontrolle auf Tresorebene bereitgestellt wird.
2. Fügen Sie den neuen Schlüssel mithilfe der Cmdlets [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) und [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) dem Server hinzu, und aktualisieren Sie ihn als neue TDE-Schutzvorrichtung des Servers.

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   # Set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. Stellen Sie mithilfe des Cmdlets [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) sicher, dass der Server und alle Replikate auf die neue TDE-Schutzvorrichtung aktualisiert wurden. 

   >[!NOTE]
   > Es kann einige Minuten dauern, bis sich die neue TDE-Schutzvorrichtung auf alle Datenbanken und sekundären Datenbanken auf dem Server verteilt.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Führen Sie eine [Sicherung des neuen Schlüssels](/powershell/module/az.keyvault/backup-azkeyvaultkey) in Key Vault durch.

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. Löschen Sie den kompromittierten Schlüssel mithilfe des Cmdlets [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) aus Key Vault. 

   ```powershell
   Remove-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. Zum künftigen Wiederherstellen eines Schlüssels in Key Vault verwenden Sie das Cmdlet [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey):
   ```powershell
   Restore-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Zugriff auf die verschlüsselten Ressourcen verweigern

1. Löschen Sie die Datenbanken, die durch den möglicherweise kompromittierten Schlüssel verschlüsselt sind.

   Die Datenbank- und Protokolldateien werden automatisch gesichert, sodass eine Point-in-Time-Wiederherstellung der Datenbank jederzeit durchgeführt werden kann (solange Sie den Schlüssel bereitstellen). Die Datenbanken müssen gelöscht werden, bevor eine aktive TDE-Schutzvorrichtung gelöscht wird, um einen möglichen Datenverlust von bis zu zehn Minuten der letzten Transaktionen zu verhindern. 
2. Sichern Sie das Schlüsselmaterial der TDE-Schutzvorrichtung in Key Vault.
3. Entfernen Sie den möglicherweise kompromittierten Schlüssel aus Key Vault.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie die TDE-Schutzvorrichtung eines Servers rotieren, um Sicherheitsanforderungen zu erfüllen: [Rotieren der Transparent Data Encryption-Schutzvorrichtung mithilfe von PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Erste Schritte mit der BYOK-Unterstützung (Bring Your Own Key) für TDE: [Aktivieren von TDE mit BYOK aus Key Vault mithilfe von PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
