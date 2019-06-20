---
title: 'PowerShell: Rotieren einer TDE-Schutzvorrichtung – Azure SQL-Datenbank | Microsoft-Dokumentation'
description: Erfahren Sie, wie die Transparent Data Encryption-Schutzvorrichtung (TDE) für einen Azure SQL-Server rotiert wird.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: jhubbard
ms.date: 03/12/2019
ms.openlocfilehash: 760b292e75b4cc64b85eaf51ffad0521b721dabf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60330855"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Rotieren der Transparent Data Encryption (TDE)-Schutzvorrichtung mithilfe von PowerShell

Dieser Artikel beschreibt die Schlüsselrotation für einen Azure SQL-Server mithilfe einer TDE-Schutzvorrichtung aus Azure Key Vault. Das Rotieren der TDE-Schutzvorrichtung eines Azure SQL-Servers bedeutet, dass zu einem neuen asymmetrischen Schlüssel gewechselt wird, der die Datenbanken auf dem Server schützt. Die Schlüsselrotation ist ein Onlinevorgang, der in wenigen Sekunden abgeschlossen sein sollte, da nur der Datenverschlüsselungsschlüssel der Datenbank entschlüsselt und wieder verschlüsselt wird, nicht die gesamte Datenbank.

Dieser Leitfaden erläutert zwei Optionen zum Rotieren der TDE-Schutzvorrichtung auf dem Server.

> [!NOTE]
> Ein angehaltener SQL Data Warehouse-Dienst muss vor den Schlüsselrotationen fortgesetzt werden.
>

> [!IMPORTANT]
> **Löschen Sie keine** vorherigen Versionen des Schlüssels nach einem Rollover.  Bei einem Rollover von Schlüsseln sind einige Daten noch immer mit den vorherigen Schlüsseln verschlüsselt, z.B. ältere Datenbanksicherungen. 
>

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az- und den AzureRm-Modulen sind im Wesentlichen identisch.

- In diesem Leitfaden wird davon ausgegangen, dass Sie bereits einen Schlüssel aus Azure Key Vault als TDE-Schutzvorrichtung für eine Azure SQL-Datenbank- oder Data Warehouse-Instanz verwenden. Siehe [Transparent Data Encryption mit BYOK-Unterstützung](transparent-data-encryption-byok-azure-sql.md).
- Azure PowerShell muss installiert sein und ausgeführt werden.
- [Empfohlen, aber optional] Erstellen Sie das Schlüsselmaterial für die TDE-Schutzvorrichtung zunächst in einem Hardwaresicherheitsmodul (HSM) oder einem lokalen Schlüsselspeicher, und importieren Sie das Schlüsselmaterial in Azure Key Vault. Weitere Informationen erhalten Sie, wenn Sie die [Anweisungen zur Verwendung eines Hardwaresicherheitsmodells (HSM) und Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) befolgen.

## <a name="manual-key-rotation"></a>Manuelle Schlüsselrotation

Bei der manuellen Schlüsselrotation wird mithilfe der Cmdlets [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) und [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) ein komplett neuer Schlüssel hinzugefügt, der unter einem neuen Schlüsselnamen oder sogar in einem anderen Schlüsseltresor gespeichert werden kann. Bei diesem Ansatz kann derselbe Schlüssel unterschiedlichen Schlüsseltresoren hinzugefügt werden, um Hochverfügbarkeit und geografisch verteilte Notfallwiederherstellungen zu unterstützen.

>[!NOTE]
>Die kombinierte Länge für den Schlüsseltresornamen und Schlüsselnamen darf 94 Zeichen nicht überschreiten.

   ```powershell
   # Add a new key to Key Vault
   Add-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
  
   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

## <a name="option-2-manual-rotation"></a>Option 2: Manuelle Rotation

Die Option fügt mithilfe der Cmdlets [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) und [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) einen komplett neuen Schlüssel hinzu, der unter einem neuen Schlüsselnamen oder sogar in einem anderen Schlüsseltresor gespeichert werden kann. 

>[!NOTE]
>Die kombinierte Länge für den Schlüsseltresornamen und Schlüsselnamen darf 94 Zeichen nicht überschreiten.
>

   ```powershell
   # Add a new key to Key Vault
   Add-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>   
  
   <# Set the key as the TDE protector for all resources 
   under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```
  
## <a name="other-useful-powershell-cmdlets"></a>Weitere nützliche PowerShell-Cmdlets

- Zum Ändern der TDE-Schutzvorrichtung vom durch Microsoft verwalteten Modus in den BYOK-Modus verwenden Sie das Cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Zum Ändern der TDE-Schutzvorrichtung vom BYOK-Modus zum durch Microsoft verwalteten Modus verwenden Sie das Cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>Nächste Schritte

- Im Falle eines Sicherheitsrisikos können Sie hier nachsehen, wie Sie eine möglicherweise kompromittierte TDE-Schutzvorrichtung entfernen: [Entfernen eines möglicherweise kompromittierten Schlüssels](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- Erste Schritte mit der Azure Key Vault-Integration und BYOK-Unterstützung (Bring Your Own Key) für TDE: [Aktivieren von TDE mit BYOK aus Key Vault mithilfe von PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
