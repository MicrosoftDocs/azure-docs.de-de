---
title: Migrieren des TDE-Zertifikats – Verwaltete Instanz
description: Migrieren des Zertifikats zum Schutz des Datenbankverschlüsselungsschlüssels einer Datenbank mit Transparent Data Encryption zu einer Azure SQL Managed Instance
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein, jovanpop
ms.date: 07/21/2020
ms.openlocfilehash: 80ff16156348db9c3a209757b48b7d54615d9104
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790694"
---
# <a name="migrate-a-certificate-of-a-tde-protected-database-to-azure-sql-managed-instance"></a>Migrieren des Zertifikats einer durch TDE geschützten Datenbank zu einer Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Beim Migrieren einer durch [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) geschützten Datenbank zu einer Azure SQL Managed Instance mithilfe der nativen Wiederherstellungsoption muss vor der Wiederherstellung der Datenbank das entsprechende Zertifikat aus der SQL-Server-Instanz migriert werden. Dieser Artikel stellt Ihnen schrittweise den Vorgang der manuellen Migration des Zertifikats zu einer verwalteten Azure SQL-Instanz vor:

> [!div class="checklist"]
>
> * Exportieren des Zertifikats in eine PFX-Datei (Personal Information Exchange)
> * Extrahieren des Zertifikats aus einer Datei in eine Base64-Zeichenfolge
> * Hochladen der Zeichenfolge mit einem PowerShell-Cmdlet

Eine alternative Option, einen vollständig verwalteten Dienst für die nahtlose Migration der durch TDE geschützten Datenbank und des entsprechenden Zertifikats zu verwenden, finden Sie unter [Migrieren Ihrer lokalen Datenbank zu einer verwalteten Azure SQL-Instanz mit Azure Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).

> [!IMPORTANT]
> Ein migriertes Zertifikat wird nur für die Wiederherstellung der durch TDE geschützten Datenbank verwendet. Kurz nach Abschluss der Wiederherstellung wird das migrierte Zertifikat durch eine andere Schutzvorrichtung ersetzt – entweder ein vom Dienst verwaltetes Zertifikat oder einen asymmetrischen Schlüssel aus dem Schlüsseltresor – je nach dem Typ der TDE, die Sie für die Instanz festlegen.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* Das [Pvk2pfx](/windows-hardware/drivers/devtest/pvk2pfx)-Befehlszeilentool, das auf dem lokalen Server oder einem anderen Computer mit Zugriff auf das Zertifikat installiert ist, das als Datei exportiert wurde. Das Pvk2pfx-Tool ist Bestandteil des [Enterprise Windows Driver Kit](/windows-hardware/drivers/download-the-wdk), einer eigenständigen Befehlszeilenumgebung.
* [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell), Version 5.0 oder höher.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Stellen Sie sicher, dass Sie über Folgendes verfügen:

* Das Azure PowerShell-Modul ist [installiert und aktualisiert](/powershell/azure/install-az-ps).
* [Az.Sql-Modul](https://www.powershellgallery.com/packages/Az.Sql).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Das Azure Resource Manager-Modul von PowerShell wird von der verwalteten Azure SQL-Instanz weiterhin unterstützt, alle zukünftigen Entwicklungen erfolgen jedoch für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRM-Modulen sind im Wesentlichen identisch.

Führen Sie die folgenden Befehle in PowerShell zum Installieren/Aktualisieren des Moduls aus:

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="export-the-tde-certificate-to-a-pfx-file"></a>Exportieren des TDE-Zertifikats in eine PFX-Datei

Das Zertifikat kann direkt aus der SQL Server-Quellinstanz oder aus dem Zertifikatspeicher exportiert werden, wenn es dort gespeichert ist.

### <a name="export-the-certificate-from-the-source-sql-server-instance"></a>Exportieren des Zertifikats aus der SQL Server-Quellinstanz

Führen Sie die folgenden Schritte aus, um das Zertifikat mit SQL Server Management Studio zu exportieren und in das PFX-Format zu konvertieren. Die generischen Namen *TDE_Cert* und *full_path* werden für die Zertifikat- und Dateinamen und Pfade in diesen Schritten verwendet. Sie sollten durch die tatsächlichen Namen ersetzt werden.

1. Öffnen Sie in SSMS ein neues Abfragefenster, und stellen Sie eine Verbindung mit der SQL Server-Quellinstanz her.

1. Verwenden Sie das folgende Skript, um durch TDE geschützte Datenbanken aufzulisten und den Namen des Zertifikats abzurufen, das die Verschlüsselung der zu migrierenden Datenbank schützt:

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![Liste der TDE-Zertifikate](./media/tde-certificate-migrate/onprem-certificate-list.png)

1. Führen Sie das folgende Skript zum Exportieren des Zertifikats in zwei Dateien (CER- und PVK-Datei) aus, die die Informationen zum öffentlichen und privaten Schlüssel enthalten:

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![Sichern des TDE-Zertifikats](./media/tde-certificate-migrate/backup-onprem-certificate.png)

1. Kopieren Sie mit der PowerShell-Konsole Zertifikatinformationen mit dem Pvk2Pfx-Tool aus zwei neu erstellten Dateien in eine PFX-Datei:

   ```cmd
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-the-certificate-from-a-certificate-store"></a>Exportieren des Zertifikats aus einem Zertifikatspeicher

Wenn das Zertifikat im Zertifikatspeicher des lokalen Computers mit SQL Server gespeichert wird, kann es mithilfe der folgenden Schritte exportiert werden:

1. Öffnen Sie die PowerShell-Konsole, und führen Sie den folgenden Befehl aus, um das Zertifikate-Snap-In der Microsoft-Verwaltungskonsole zu öffnen:

   ```cmd
   certlm
   ```

2. Erweitern Sie im Zertifikate-MMC-Snap-In den Pfad „Personal > Certificates“, um eine Liste der Zertifikate anzuzeigen.

3. Klicken Sie mit der rechten Maustaste auf das Zertifikat, und klicken Sie dann auf **Exportieren** .

4. Folgen Sie den Anweisungen des Assistenten, um das Zertifikat und den privaten Schlüssel in ein PFX-Format zu exportieren.

## <a name="upload-the-certificate-to-azure-sql-managed-instance-using-an-azure-powershell-cmdlet"></a>Hochladen des Zertifikats in eine Azure SQL Managed Instance mit dem Azure PowerShell-Cmdlet

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Beginnen Sie mit den Vorbereitungsschritten in PowerShell:

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. Nachdem alle Vorbereitungsschritte abgeschlossen sind, führen Sie die folgenden Befehle zum Hochladen des Base64-codierten Zertifikats in die verwaltete Zielinstanz aus:

   ```azurepowershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -AsByteStream
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Sie müssen zunächst mit ihrer *PFX* -Datei [einen Azure-Schlüsseltresor](../../key-vault/general/manage-with-cli2.md) einrichten.

1. Beginnen Sie mit den Vorbereitungsschritten in PowerShell:

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. Nachdem alle Vorbereitungsschritte abgeschlossen sind, führen Sie die folgenden Befehle zum Hochladen des Base64-codierten Zertifikats in die verwaltete Zielinstanz aus:

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

Das Zertifikat ist nun für die angegebene verwaltete Instanz verfügbar, und die Sicherung der entsprechenden durch TDE geschützten Datenbank kann erfolgreich wiederhergestellt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie das Zertifikat, das den Verschlüsselungsschlüssel der Datenbank mit Transparent Data Encryption schützt, aus dem lokalen oder IaaS-SQL-Server zu einer Azure SQL Managed Instance migriert wird.

Lesen Sie [Wiederherstellen einer Datenbanksicherung in einer verwalteten Azure SQL-Instanz](restore-sample-database-quickstart.md), um zu erfahren, wie Sie eine Datenbanksicherung in einer Azure SQL Managed Instance wiederherstellen können.