---
title: Rotation in einer TDE-Schutzvorrichtung (PowerShell und die Azure CLI)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Erfahren Sie, wie Sie für einen Server in Azure, der von Azure SQL-Datenbank und Azure Synapse Analytics verwendet wird, mithilfe von PowerShell und der Azure CLI eine Rotation in der TDE-Schutzvorrichtung (Transparent Data Encryption) durchführen.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 37931aee24e2dbcab03bca400d58f236601c87e5
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321417"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector"></a>Durchführen einer Rotation in der Transparent Data Encryption-Schutzvorrichtung (TDE)
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


Dieser Artikel beschreibt die Schlüsselrotation für einen [Server](logical-servers.md) mithilfe einer TDE-Schutzvorrichtung aus Azure Key Vault. Die Rotation der logischen TDE-Schutzvorrichtung für einen Server bewirkt einen Wechsel zu einem neuen asymmetrischen Schlüssel, der die Datenbanken auf dem Server schützt. Die Schlüsselrotation ist ein Onlinevorgang, der in wenigen Sekunden abgeschlossen sein sollte, da nur der Datenverschlüsselungsschlüssel der Datenbank entschlüsselt und wieder verschlüsselt wird, aber nicht die gesamte Datenbank.

Dieser Leitfaden erläutert zwei Optionen zum Rotieren der TDE-Schutzvorrichtung auf dem Server.

> [!NOTE]
> Ein angehaltener dedizierter SQL-Pool in Azure Synapse Analytics muss vor Schlüsselrotationen fortgesetzt werden.

> [!IMPORTANT]
> Löschen Sie keine vorherigen Versionen des Schlüssels nach einem Rollover. Bei einem Rollover von Schlüsseln sind einige Daten noch immer mit den vorherigen Schlüsseln verschlüsselt, z.B. ältere Datenbanksicherungen.

## <a name="prerequisites"></a>Voraussetzungen

- In diesem Leitfaden wird davon ausgegangen, dass Sie bereits einen Schlüssel aus Azure Key Vault als TDE-Schutzvorrichtung für Azure SQL-Datenbank oder Azure Synapse Analytics verwenden. Siehe [Transparent Data Encryption mit BYOK-Unterstützung](transparent-data-encryption-byok-overview.md).
- Azure PowerShell muss installiert sein und ausgeführt werden.
- [Empfohlen, aber optional] Erstellen Sie das Schlüsselmaterial für die TDE-Schutzvorrichtung zunächst in einem Hardwaresicherheitsmodul (HSM) oder einem lokalen Schlüsselspeicher, und importieren Sie das Schlüsselmaterial in Azure Key Vault. Weitere Informationen erhalten Sie, wenn Sie die [Anweisungen zur Verwendung eines Hardwaresicherheitsmodells (HSM) und Key Vault](../../key-vault/general/overview.md) befolgen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Anweisungen zur Installation des Az-Moduls finden Sie unter [Install Azure PowerShell](/powershell/azure/install-az-ps) (Installieren von Azure PowerShell). Spezifische Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> Das Azure Resource Manager-Modul (RM) von PowerShell wird weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Das AzureRM-Modul erhält mindestens bis Dezember 2020 weiterhin Fehlerbehebungen.  Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch. Weitere Informationen zur Kompatibilität finden Sie in der [Einführung in das neue Azure PowerShell Az-Modul](/powershell/azure/new-azureps-module-az).

# <a name="the-azure-cli"></a>[Die Azure-CLI](#tab/azure-cli)

Informationen zur Installation finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="manual-key-rotation"></a>Manuelle Schlüsselrotation

Die manuelle Schlüsselrotation nutzt die folgenden Befehle zum Hinzufügen eines völlig neuen Schlüssels, der unter einem neuen Schlüsselnamen oder sogar in einem anderen Schlüsseltresor gespeichert werden kann. Bei diesem Ansatz kann derselbe Schlüssel unterschiedlichen Schlüsseltresoren hinzugefügt werden, um Hochverfügbarkeit und geografisch verteilte Notfallwiederherstellungen zu unterstützen.

> [!NOTE]
> Die kombinierte Länge für den Schlüsseltresornamen und Schlüsselnamen darf 94 Zeichen nicht überschreiten.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie die Cmdlets [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) und [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="the-azure-cli"></a>[Die Azure-CLI](#tab/azure-cli)

Verwenden Sie die Befehle [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create), [az sql server key create](/cli/azure/sql/server/key#az-sql-server-key-create) und [az sql server tde-key set](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set).

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="switch-tde-protector-mode"></a>Wechseln des TDE-Schutzmodus

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Zum Ändern der TDE-Schutzvorrichtung vom durch Microsoft verwalteten Modus in den BYOK-Modus verwenden Sie das Cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Zum Ändern der TDE-Schutzvorrichtung vom BYOK-Modus zum durch Microsoft verwalteten Modus verwenden Sie das Cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[Die Azure-CLI](#tab/azure-cli)

In den folgenden Beispielen wird der Befehl [az sql server tde-key set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) verwendet,

- um die TDE-Schutzvorrichtung aus der Microsoft-Verwaltung in den BYOK-Modus (Bring Your Own Key) zu wechseln.

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- um die TDE-Schutzvorrichtung aus dem BYOK-Modus in die Microsoft-Verwaltung zu wechseln.

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Nächste Schritte

- Im Falle eines Sicherheitsrisikos können Sie hier nachsehen, wie Sie eine möglicherweise kompromittierte TDE-Schutzvorrichtung entfernen: [Entfernen eines möglicherweise kompromittierten Schlüssels](transparent-data-encryption-byok-remove-tde-protector.md).

- Erste Schritte mit der Azure Key Vault-Integration und BYOK-Unterstützung (Bring Your Own Key) für TDE: [Aktivieren von TDE mit BYOK aus Key Vault mithilfe von PowerShell](transparent-data-encryption-byok-configure.md).