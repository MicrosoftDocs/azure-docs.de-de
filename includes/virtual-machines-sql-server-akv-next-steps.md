---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 6195b949cc71043dfa7a12bdece7a311dbde5e21
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95563891"
---
## <a name="next-steps"></a>Nächste Schritte

Nach dem Aktivieren der Azure-Schlüsseltresor-Integration können Sie die SQL Server-Verschlüsselung auf Ihrem virtuellen SQL-Computer aktivieren. Zuerst müssen Sie in Ihrem Schlüsseltresor einen asymmetrischen Schlüssel und in SQL Server auf Ihrem virtuellen Computer einen symmetrischen Schlüssel erstellen. Sie können dann T-SQL-Anweisungen ausführen, um die Verschlüsselung für Ihre Datenbanken und Backups zu aktivieren.

Es gibt verschiedene Arten der Verschlüsselung, die Sie nutzen können:

* [Transparente Datenverschlüsselung (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption)
* [Verschlüsselte Sicherungen](/sql/relational-databases/backup-restore/backup-encryption)
* [Column Level Encryption (CLE)](/sql/t-sql/functions/cryptographic-functions-transact-sql)

Die folgenden Transact-SQL-Skripts enthalten Beispiele für jeden dieser Bereiche.

### <a name="prerequisites-for-examples"></a>Erforderliche Komponenten für Beispiele

Jedes Beispiel basiert auf diesen beiden erforderlichen Komponenten: einem asymmetrischen Schlüssel aus Ihrem Schlüsseltresor mit dem Namen **CONTOSO_KEY** und Anmeldeinformationen, die mit der Funktion für die Azure-Schlüsseltresor-Integration erstellt wurden und den Namen **Azure_EKM_cred** haben. Die folgenden Transact-SQL-Befehle richten diese erforderlichen Komponenten für die Ausführung der Beispiele ein:

``` sql
USE master;
GO

--create credential
--The <<SECRET>> here requires the <Application ID> (without hyphens) and <Secret> to be passed together without a space between them.
CREATE CREDENTIAL Azure_EKM_cred
    WITH IDENTITY = 'keytestvault', --keyvault
    SECRET = '<<SECRET>>'
FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov;


--Map the credential to a SQL login that has sysadmin permissions. This allows the SQL login to access the key vault when creating the asymmetric key in the next step.
ALTER LOGIN [SQL_Login]
ADD CREDENTIAL Azure_EKM_cred;


CREATE ASYMMETRIC KEY CONTOSO_KEY
FROM PROVIDER [AzureKeyVault_EKM_Prov]
WITH PROVIDER_KEY_NAME = 'KeyName_in_KeyVault',  --The key name here requires the key we created in the key vault
CREATION_DISPOSITION = OPEN_EXISTING;
```

### <a name="transparent-data-encryption-tde"></a>TDE (Transparent Data Encryption)

1. Erstellen Sie eine SQL Server-Anmeldung, die von der Datenbank-Engine für TDE verwendet werden kann, und fügen Sie die Anmeldeinformationen hinzu.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it loads a database
   -- encrypted by TDE.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the TDE Login to add the credential for use by the
   -- Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred;
   GO
   ```

1. Erstellen Sie den Datenbankverschlüsselungsschlüssel, der für TDE verwendet werden soll.

   ``` sql
   USE ContosoDatabase;
   GO

   CREATE DATABASE ENCRYPTION KEY 
   WITH ALGORITHM = AES_128 
   ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the database to enable transparent data encryption.
   ALTER DATABASE ContosoDatabase
   SET ENCRYPTION ON;
   GO
   ```

### <a name="encrypted-backups"></a>Verschlüsselte Sicherungen

1. Erstellen Sie eine SQL Server-Anmeldung, die von der Datenbank-Engine zum Verschlüsseln von Sicherungen verwendet wird, und fügen Sie die Anmeldeinformationen hinzu.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it is encrypting the backup.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the Encrypted Backup Login to add the credential for use by
   -- the Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred ;
   GO
   ```

1. Sichern Sie die Datenbank, indem Sie die Verschlüsselung mit dem asymmetrischen Schlüssel angeben, der im Schlüsseltresor gespeichert ist.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>Column Level Encryption (CLE)

Mit diesem Skript wird ein symmetrischer Schlüssel erstellt, der durch den asymmetrischen Schlüssel im Schlüsseltresor geschützt ist, und anschließend wird der symmetrische Schlüssel zum Verschlüsseln der Daten in der Datenbank verwendet.

``` sql
CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
WITH ALGORITHM=AES_256
ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

DECLARE @DATA VARBINARY(MAX);

--Open the symmetric key for use in this session
OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY
DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

--Encrypt syntax
SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));

-- Decrypt syntax
SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));

--Close the symmetric key
CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Weitere Informationen zur Verwendung dieser Verschlüsselungsfunktionen finden Sie unter [Verwenden der erweiterbaren Schlüsselverwaltung mit SQL Server-Verschlüsselungsfunktionen](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server#UsesOfEKM).

Beachten Sie, dass bei den Schritten in diesem Artikel davon ausgegangen wird, dass bei Ihnen SQL Server bereits auf einem virtuellen Azure-Computer ausgeführt wird. Ist dies nicht der Fall, helfen Ihnen die Informationen unter [Bereitstellen eines virtuellen Computers mit SQL Server in Azure](../articles/azure-sql/virtual-machines/windows/create-sql-vm-portal.md) weiter. Weitere Informationen zum Ausführen von SQL Server auf virtuellen Azure-Computern finden Sie in der [Übersicht zu SQL Server auf virtuellen Azure-Computern](../articles/azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).