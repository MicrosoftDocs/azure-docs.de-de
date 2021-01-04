---
title: Transparent Data Encryption
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Hier finden Sie eine Übersicht über TDE (Transparent Data Encryption) für Azure SQL-Datenbank, Azure SQL Managed Instance und Azure Synapse Analytics. Das Dokument behandelt die Vorteile dieser Verschlüsselung sowie die Konfigurationsoptionen. Diese umfassen die von einem Dienst verwaltete transparente Datenverschlüsselung und Bring Your Own Key.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 10/12/2020
ms.openlocfilehash: 8fbbd7a2aabc9de417f1eefd2513edba3119bfc0
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791391"
---
# <a name="transparent-data-encryption-for-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>TDE (Transparent Data Encryption) für SQL-Datenbank, SQL Managed Instance und Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Transparente Datenverschlüsselung (Transparent Data Encryption, TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) trägt durch die Verschlüsselung von ruhenden Daten zum Schutz von Azure SQL-Datenbank, Azure SQL Managed Instance und Azure Synapse Analytics vor der Bedrohung durch schädliche Offlineaktivitäten bei. TDE ver- und entschlüsselt die Datenbank, die zugehörigen Sicherungen und die Transaktionsprotokolldateien im Ruhezustand in Echtzeit, ohne dass Änderungen an der Anwendung erforderlich sind. TDE ist standardmäßig für alle neu bereitgestellten SQL-Datenbank-Instanzen aktiviert und muss für ältere Datenbanken in Azure SQL-Datenbank oder Azure SQL Managed Instance manuell aktiviert werden. Für Azure Synapse Analytics muss TDE manuell aktiviert werden.

Die TDE führt die E/A-Verschlüsselung und -Entschlüsselung der Daten und auf Seitenebene durch. Jede Seite wird entschlüsselt, wenn sie in den Speicher gelesen wird, und dann verschlüsselt, bevor sie auf den Datenträger geschrieben wird. TDE verschlüsselt den Speicher einer gesamten Datenbank mithilfe eines symmetrischen Schlüssels, der als Datenbank-Verschlüsselungsschlüssel (Database Encryption Key, DEK) bezeichnet wird. Beim Datenbankstart wird der verschlüsselte DEK entschlüsselt und dann für die Entschlüsselung und erneute Verschlüsselung der Datenbankdateien im Prozess der SQL Server-Datenbank-Engine verwendet. Der DEK ist durch die TDE-Schutzvorrichtung geschützt. Beim TDE-Schutzvorrichtung handelt es sich entweder um ein von einem Dienst verwaltetes Zertifikat (dienstseitig verwaltete transparente Datenverschlüsselung) oder um einen asymmetrischen Schlüssel, der in [Azure Key Vault](../../key-vault/general/secure-your-key-vault.md) gespeichert ist (kundenseitig verwaltete transparente Datenverschlüsselung).

Bei Azure SQL-Datenbank und Azure Synapse ist der TDE-Schutz auf der [Serverebene](logical-servers.md) festgelegt und wird von allen Datenbanken übernommen, die diesem Server zugeordnet sind. Bei der verwalteten Azure SQL-Instanz ist die TDE-Schutzvorrichtung auf Instanzebene festgelegt und wird von allen verschlüsselten Datenbanken für diese Instanz geerbt. In diesem Dokument bezieht sich der Begriff *Server* sowohl auf den Server als auch die Instanz (sofern nicht anders angegeben).

> [!IMPORTANT]
> Alle neu erstellten Datenbanken in SQL-Datenbank werden standardmäßig mithilfe einer dienstseitig verwalteten transparenten Datenverschlüsselung verschlüsselt. Vorhandene SQL-Datenbanken, die vor Mai 2017 erstellt wurden, und SQL-Datenbanken, die durch Wiederherstellung, Georeplikation und Datenbankkopie erstellt wurden, sind standardmäßig nicht verschlüsselt. Vorhandene SQL Managed Instance-Datenbanken, die vor Februar 2019 erstellt wurden, sind standardmäßig nicht verschlüsselt. Azure SQL Managed Instance-Datenbanken, die durch Wiederherstellen erstellt wurden, erben den Verschlüsselungsstatus von der Quelle.

> [!NOTE]
> TDE kann nicht zum Verschlüsseln von Systemdatenbanken wie die **Masterdatenbank** in Azure SQL-Datenbank und Azure SQL Managed Instance verwendet werden. Die **master** -Datenbank enthält Objekte, die zum Ausführen der TDE-Vorgänge für die Benutzerdatenbanken erforderlich sind. Es wird empfohlen, keine vertraulichen Daten in den Systemdatenbanken zu speichern. Die [Infrastrukturverschlüsselung](transparent-data-encryption-byok-overview.md#doubleencryption), die Systemdatenbanken verschlüsselt (einschließlich der Masterdatenbank), wird jetzt eingeführt. 

## <a name="service-managed-transparent-data-encryption"></a>Von einem Dienst verwaltete transparente Datenverschlüsselung

In Azure ist die TDE standardmäßig so eingerichtet, dass der DEK durch ein integriertes Serverzertifikat geschützt ist. Das integrierte Serverzertifikat ist für jeden Server eindeutig, und der verwendete Verschlüsselungsalgorithmus ist AES 256. Wenn sich eine Datenbank in einer Georeplikationsbeziehung befindet, werden sowohl die primäre als auch die sekundäre Geodatenbank vom übergeordneten Serverschlüssel der primären Datenbank geschützt. Sind zwei Datenbanken mit dem gleichen Server verbunden, verwenden sie auch das gleiche integrierte Zertifikat. Microsoft rotiert diese Zertifikate gemäß der internen Sicherheitsrichtlinie automatisch, und der Stammschlüssel wird von einem Microsoft-internen Geheimnisspeicher geschützt. Kunden können die Compliance von SQL-Datenbank und SQL Managed Instance mit internen Sicherheitsrichtlinien in Überwachungsberichten unabhängiger Drittanbieter überprüfen, die im [Microsoft Trust Center](https://servicetrust.microsoft.com/) zur Verfügung stehen.

Microsoft verschiebt und verwaltet auch die Schlüssel nahtlos, die für die Georeplikation und Wiederherstellung benötigt werden.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Vom Kunden verwaltete Transparent Data Encryption – BYOK (Bring Your Own Key)

Die vom Kunden verwaltete transparente Datenverschlüsselung wird auch als BYOK-Unterstützung für TDE bezeichnet. In diesem Szenario ist die TDE-Schutzvorrichtung, die den DEK verschlüsselt, ein kundenseitig verwalteter asymmetrischer Schlüssel, der in einer kundeneigenen und kundenseitig verwalteten Azure Key Vault-Instanz – dem cloudbasierten externen Schlüsselverwaltungssystem von Azure – gespeichert ist und diesen Schlüsseltresor niemals verlässt. Der TDE-Schutz kann [vom Schlüsseltresor generiert oder von einem lokalen HSM-Gerät (Hardwaresicherheitsmodul) in den Schlüsseltresor übertragen](../../key-vault/keys/hsm-protected-keys.md) werden. SQL-Datenbank, SQL Managed Instance und Azure Synapse müssen Berechtigungen für den Schlüsseltresor des Kunden gewährt werden, damit der DEK ver- und entschlüsselt werden kann. Wenn die Serverberechtigungen für den Schlüsseltresor widerrufen werden, ist kein Zugriff auf die Datenbank möglich, und alle Daten werden verschlüsselt.

Mit TDE und Azure Key Vault-Integration können Benutzer Aufgaben der Schlüsselverwaltung steuern, z.B. Schlüsselrotationen, Schlüsseltresorberechtigungen und Schlüsselsicherungen. Außerdem können sie die Überwachung und Berichterstellung für alle TDE-Schutzvorrichtungen aktivieren, die Azure Key Vault-Funktionalität nutzen. Key Vault bietet eine zentrale Schlüsselverwaltung, verwendet streng überwachte HSMs und ermöglicht die Aufgabentrennung zwischen dem Verwalten von Schlüsseln und Daten, um Sicherheitsrichtlinien einzuhalten.
Weitere Informationen zu BYOK für Azure SQL-Datenbank und Azure Synapse finden Sie unter [Transparente Datenverschlüsselung mit Azure Key Vault-Integration](transparent-data-encryption-byok-overview.md).

Wenn Sie mit der Verwendung von TDE mit Azure Key Vault-Integration beginnen möchten, sehen Sie sich die Schrittanleitung [Aktivieren der transparenten Datenverschlüsselung mithilfe Ihres eigenen Schlüssels aus Key Vault](transparent-data-encryption-byok-configure.md) an.

## <a name="move-a-transparent-data-encryption-protected-database"></a>Verschieben einer durch die transparente Datenverschlüsselung gesicherte Datenbank

Für Vorgänge in Azure müssen Datenbanken nicht entschlüsselt werden. Die TDE-Einstellungen für die Quelldatenbank oder die primäre Datenbank werden transparent an das Ziel vererbt. Dies gilt für folgende Vorgänge:

- Geowiederherstellung
- Self-Service-Point-in-Time-Wiederherstellung
- Wiederherstellung einer gelöschten Datenbank
- Aktive Georeplikation
- Erstellung einer Datenbankkopie
- Wiederherstellen einer Sicherungsdatei in der verwalteten Azure SQL-Instanz

> [!IMPORTANT]
> Die manuelle COPY-ONLY-Sicherung einer Datenbank, die über einen vom Dienst verwalteten TDE-Vorgang verschlüsselt wurde, wird in Azure SQL Managed Instance nicht unterstützt, da nicht auf das für die Verschlüsselung genutzte Zertifikat zugegriffen werden kann. Verwenden Sie die Point-in-Time-Wiederherstellung, um diese Art von Datenbank auf eine andere SQL Managed Instance zu verschieben, oder wechseln Sie zu einem kundenseitig verwalteten Schlüssel.

Wenn Sie eine TDE-geschützte Datenbank exportieren, wird der exportierte Inhalt der Datenbank nicht verschlüsselt. Diese exportierten Inhalte werden in unverschlüsselten BACPAC-Dateien gespeichert. Achten Sie darauf, die BACPAC-Dateien in geeigneter Weise zu schützen und TDE zu aktivieren, sobald der Import der neuen Datenbank abgeschlossen ist.

Beispiel: Wenn die BACPAC-Datei von einer SQL Server-Instanz exportiert wird, wird der importierte Inhalt der neuen Datenbank nicht automatisch verschlüsselt. Wenn die BACPAC-Datei in eine SQL Server-Instanz exportiert wird, wird die neue Datenbank auch nicht automatisch verschlüsselt.

Eine Ausnahme besteht beim Exportieren einer Datenbank in und aus SQL-Datenbank. TDE ist für die neue Datenbank aktiviert, die BACPAC-Datei selbst ist allerdings noch nicht verschlüsselt.

## <a name="manage-transparent-data-encryption"></a>Verwalten von Transparent Data Encryption

# <a name="the-azure-portal"></a>[Azure-Portal](#tab/azure-portal)

Verwalten Sie TDE im Azure-Portal.

Um TDE über das Azure-Portal zu konfigurieren, müssen Sie als Azure-Besitzer, Azure-Mitwirkender oder SQL-Sicherheitsmanager verbunden sein.

Aktivieren und Deaktivieren Sie TDE auf Datenbankebene. Verwenden Sie für Azure SQL Managed Instance Transact-SQL (T-SQL), um TDE für eine Datenbank zu aktivieren oder zu deaktivieren. Für Azure SQL-Datenbank und Azure Synapse können Sie TDE für die Datenbank im [Azure-Portal](https://portal.azure.com) verwalten, nachdem Sie sich mit einem Konto als Azure-Administrator oder Azure-Mitwirkender angemeldet haben. Suchen Sie die TDE-Einstellungen in Ihrer Benutzerdatenbank. Standardmäßig wird die von einem Dienst verwaltete transparente Datenverschlüsselung verwendet. Für den Server, der die Datenbank enthält, wird automatisch ein TDE-Zertifikat generiert.

![Von einem Dienst verwaltete transparente Datenverschlüsselung](./media/transparent-data-encryption-tde-overview/service-managed-transparent-data-encryption.png)  

Sie legen den TDE-Hauptschlüssel, der auch als TDE-Schutz bezeichnet wird, auf Server- oder Instanzebene fest. Öffnen Sie die TDE-Einstellungen in Ihrem Server, um TDE mit der BYOK-Unterstützung zu verwenden und Ihre Datenbanken mit einem Schlüssel aus Key Vault zu schützen.

![Transparente Datenverschlüsselung mit Bring Your Own Key-Unterstützung](./media/transparent-data-encryption-tde-overview/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwalten Sie TDE mit PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das Azure Resource Manager-Modul von PowerShell wird weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch.

Um TDE über PowerShell zu konfigurieren, müssen Sie als Azure-Besitzer, Azure-Mitwirkender oder SQL-Sicherheitsmanager verbunden sein.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Cmdlets für Azure SQL-Datenbank und Azure Synapse

Verwenden Sie die folgenden Cmdlets für Azure SQL-Datenbank und Azure Synapse:

| Cmdlet | BESCHREIBUNG |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Aktiviert oder deaktiviert die transparente Datenverschlüsselung für eine Datenbank|
| [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Ruft den Status der transparenten Datenverschlüsselung für eine Datenbank ab |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Überprüft den Verschlüsselungsfortschritt für eine Datenbank |
| [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Fügt einem Server einen Key Vault-Schlüssel hinzu |
| [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Ruft die Key Vault-Schlüssel für einen Server ab  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Legt den Schutz der transparenten Datenverschlüsselung für einen Server fest |
| [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Ruft die Schutzvorrichtung für die transparente Datenverschlüsselung ab |
| [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Entfernt einen Key Vault-Schlüssel aus einem Server |
|  | |

> [!IMPORTANT]
> Verwenden Sie bei einer verwalteten Azure SQL-Instanz den T-SQL-Befehl [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database), um die TDE auf Datenbankebene zu aktivieren oder deaktivieren, und sehen Sie sich das [PowerShell-Beispielskript](transparent-data-encryption-byok-configure.md) an, um TDE auf Instanzebene zu verwalten.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)

Verwalten Sie TDE mithilfe von Transact-SQL.

Stellen Sie mit einem Konto, das ein Administratorkonto oder ein Mitglied der Rolle **dbmanager** in der Masterdatenbank ist, eine Verbindung mit der Datenbank her.

| Get-Help | BESCHREIBUNG |
| --- | --- |
| [ALTER DATABASE (Azure SQL-Datenbank)](/sql/t-sql/statements/alter-database-azure-sql-database) | Mit SET ENCRYPTION ON/OFF wird eine Datenbank verschlüsselt oder entschlüsselt. |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Gibt Informationen über den Verschlüsselungsstatus einer Datenbank und die ihr zugeordneten Verschlüsselungsschlüssel zurück. |
| [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Gibt Informationen über den Verschlüsselungsstatus jedes Azure Synapse-Knotens und der zugehörigen Verschlüsselungsschlüssel der Datenbank zurück. |
|  | |

Sie können die TDE-Schutzvorrichtung nicht per Transact-SQL durch einen Schlüssel von Key Vault ersetzen. Verwenden Sie PowerShell oder das Azure-Portal.

# <a name="rest-api"></a>[REST-API](#tab/azure-RESTAPI)

Verwalten Sie TDE mithilfe der REST-API.

Um TDE über die REST-API zu konfigurieren, müssen Sie als Azure-Besitzer, Azure-Mitwirkender oder SQL-Sicherheitsmanager verbunden sein.
Verwenden Sie die folgenden Befehle für Azure SQL-Datenbank und Azure Synapse:

| Get-Help | BESCHREIBUNG |
| --- | --- |
|[Server erstellen oder aktualisieren](/rest/api/sql/servers/createorupdate)|Fügt einem Server eine Azure Active Directory-Identität hinzu (wird verwendet, um Zugriff auf Key Vault zu gewähren)|
|[Serverschlüssel erstellen oder aktualisieren](/rest/api/sql/serverkeys/createorupdate)|Fügt einem Server einen Key Vault-Schlüssel hinzu|
|[Serverschlüssel entfernen](/rest/api/sql/serverkeys/delete)|Entfernt einen Key Vault-Schlüssel aus einem Server |
|[Serverschlüssel abrufen](/rest/api/sql/serverkeys/get)|Ruft einen bestimmten Key Vault-Schlüssel von einem Server ab|
|[Serverschlüssel nach Server auflisten](/rest/api/sql/serverkeys/listbyserver)|Ruft die Key Vault-Schlüssel für einen Server ab |
|[Verschlüsselungsschutzvorrichtung erstellen oder aktualisieren](/rest/api/sql/encryptionprotectors/createorupdate)|Legt den TDE-Schutz für einen Server fest|
|[Verschlüsselungsschutzvorrichtung abrufen](/rest/api/sql/encryptionprotectors/get)|Ruft den TDE-Schutz für einen Server ab|
|[Verschlüsselungsschutzvorrichtungen nach Server auflisten](/rest/api/sql/encryptionprotectors/listbyserver)|Ruft den TDE-Schutz für einen Server ab |
|[Transparent Data Encryption-Konfiguration erstellen oder aktualisieren](/rest/api/sql/transparentdataencryptions/createorupdate)|Aktiviert oder deaktiviert TDE für eine Datenbank|
|[Transparent Data Encryption-Konfiguration abrufen](/rest/api/sql/transparentdataencryptions/get)|Ruft die TDE-Konfiguration für eine Datenbank ab.|
|[Ergebnisse der Transparent Data Encryption-Konfiguration auflisten](/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Ruft das Verschlüsselungsergebnis für eine Datenbank ab|

## <a name="see-also"></a>Weitere Informationen

- SQL Server kann bei der Ausführung auf einem virtuellen Azure-Computer auch einen asymmetrischen Schlüssel aus Key Vault verwenden. Die Konfigurationsschritte unterscheiden sich von der Verwendung eines asymmetrischen Schlüssels in SQL-Datenbank und einer verwalteten SQL-Instanz. Weitere Informationen finden Sie unter [Erweiterbare Schlüsselverwaltung mit Azure Key Vault (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).
- Eine allgemeine Beschreibung von TDE finden Sie unter [Transparente Datenverschlüsselung](/sql/relational-databases/security/encryption/transparent-data-encryption).
- Weitere Informationen TDE mit BYOK-Unterstützung für Azure SQL-Datenbank, verwaltete Azure SQL-Instanzen und Azure Synapse finden Sie unter [Transparente Datenverschlüsselung mit Bring Your Own Key-Unterstützung](transparent-data-encryption-byok-overview.md).
- Wenn Sie mit der Verwendung von TDE mit BYOK-Unterstützung beginnen möchten, sehen Sie sich die Schrittanleitung [Aktivieren von Transparent Data Encryption mithilfe eines vom Kunden verwalteten Azure Key Vault-Schlüssels](transparent-data-encryption-byok-configure.md) an.
- Weitere Informationen zu Key Vault finden Sie unter [Sicherer Zugriff auf einen Schlüsseltresor](../../key-vault/general/secure-your-key-vault.md).