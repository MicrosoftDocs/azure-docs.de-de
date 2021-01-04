---
title: Steuern des Speicherkontozugriffs für einen serverlosen SQL-Pool
description: In diesem Artikel erfahren Sie, wie ein serverloser SQL-Pool auf Azure Storage zugreift und wie Sie den Speicherzugriff für den serverlosen SQL-Pool in Azure Synapse Analytics steuern.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 06/11/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 6eff662ac0140e7a64cc3bab28856178708cb9b2
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400674"
---
# <a name="control-storage-account-access-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Steuern des Speicherkontozugriffs für einen serverlosen SQL-Pool in Azure Synapse Analytics

Eine Abfrage eines serverlosen SQL-Pools liest Dateien direkt aus Azure Storage. Berechtigungen für den Zugriff auf Dateien in Azure Storage werden auf zwei Ebenen gesteuert:
- **Speicherebene**: Der Benutzer sollte über die Berechtigung für den Zugriff auf zugrunde liegende Speicherdateien verfügen. Der Speicheradministrator sollte dem Azure AD-Prinzipal das Lesen/Schreiben von Dateien gestatten oder einen SAS-Schlüssel generieren, der für den Speicherzugriff verwendet wird.
- **SQL-Dienstebene**: Benutzern müssen die Berechtigung zum Lesen von Daten mithilfe einer [externen Tabelle](develop-tables-external-tables.md) oder zum Ausführen der Funktion `OPENROWSET` erteilt haben. Informationen zu den erforderlichen Berechtigungen finden Sie in [diesem Abschnitt](develop-storage-files-overview.md#permissions).

In diesem Artikel wird beschrieben, welche Arten von Anmeldeinformationen Sie verwenden können und wie die Suche nach Anmeldeinformationen für SQL- und Azure AD-Benutzer funktioniert.

## <a name="supported-storage-authorization-types"></a>Unterstützte Autorisierungstypen für den Speicherzugriff

Ein bei einem serverlosen SQL-Pool angemeldeter Benutzer muss für den Zugriff auf und die Abfrage von Dateien in Azure Storage autorisiert sein, wenn die Dateien nicht öffentlich verfügbar sind. Sie können drei Autorisierungstypen für den Zugriff auf nicht öffentlichen Speicher verwenden: [Benutzeridentität](?tabs=user-identity), [Shared Access Signature](?tabs=shared-access-signature) und [Verwaltete Identität](?tabs=managed-identity).

> [!NOTE]
> **Azure AD-Pass-Through** ist das Standardverhalten, wenn Sie einen Arbeitsbereich erstellen.

### <a name="user-identity"></a>[Benutzeridentität](#tab/user-identity)

Die **Benutzeridentität** (auch „Azure AD-Passthrough“ genannt) ist ein Autorisierungstyp, bei dem die Identität des bei einem serverlosen SQL-Pool angemeldeten Azure AD-Benutzers verwendet wird, um den Datenzugriff zu autorisieren. Vor dem Zugriff auf die Daten muss der Azure Storage-Administrator dem Azure AD-Benutzer die erforderlichen Berechtigungen erteilen. Wie Sie der Tabelle unten entnehmen können, wird diese Art der Autorisierung für den Typ „SQL-Benutzer“ nicht unterstützt.

> [!IMPORTANT]
> Sie müssen über eine Rolle als Besitzer, Mitwirkender oder Leser für Storage-Blobdaten verfügen, um mithilfe Ihrer Identität auf die Daten zugreifen zu können.
> Auch wenn Sie Besitzer eines Speicherkontos sind, müssen Sie sich selbst zu einer der Rollen für den Zugriff auf Storage-Blobdaten hinzufügen.
>
> Weitere Informationen zur Zugriffssteuerung in Azure Data Lake Storage Gen2 finden Sie im Artikel [Zugriffssteuerung in Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-access-control.md).
>

### <a name="shared-access-signature"></a>[Shared Access Signature (SAS)](#tab/shared-access-signature)

**Shared Access Signature (SAS)** bietet delegierten Zugriff auf Ressourcen in einem Speicherkonto. Mit einer SAS kann ein Kunde Clients Zugriff auf Ressourcen in einem Speicherkonto gewähren, ohne dafür Kontoschlüssel weitergeben zu müssen. Eine SAS bietet die Möglichkeit, präzise zu steuern, welche Art von Zugriff Sie Clients gewähren möchten, die über eine SAS verfügen. Dazu stehen Parameter wie Gültigkeitsintervall, erteilte Berechtigungen, zulässiger IP-Adressbereich und zulässiges Protokoll (HTTPS/HTTP) zur Verfügung.

Um ein SAS-Token abzurufen, navigieren Sie zu **Azure-Portal > Speicherkonto > Shared Access Signature > Berechtigungen konfigurieren > SAS und Verbindungszeichenfolge generieren**.

> [!IMPORTANT]
> Wenn ein SAS-Token generiert wird, enthält es am Anfang ein Fragezeichen („?“). Um das Token in einem serverlosen SQL-Pool zu verwenden, müssen Sie das Fragezeichen (?) beim Erstellen der Anmeldeinformation entfernen. Beispiel:
>
> SAS-Token: ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D

Um Zugriff über ein SAS-Token zu ermöglichen, müssen Sie datenbankbezogene oder serverbezogene Anmeldeinformationen erstellen. 

### <a name="managed-identity"></a>[Verwaltete Identität](#tab/managed-identity)

Eine **verwaltete Identität** wird auch als MSI bezeichnet. Hierbei handelt es sich um ein Feature von Azure Active Directory (Azure AD), das Azure-Dienste für einen serverlosen SQL-Pool bereitstellt. Es stellt außerdem eine automatisch verwaltete Identität in Azure AD bereit. Diese Identität kann verwendet werden, um eine Anforderung für den Datenzugriff in Azure Storage zu autorisieren.

Vor dem Zugriff auf die Daten muss der Azure Storage-Administrator der verwalteten Identität Zugriffsberechtigungen für die Daten erteilen. Das Erteilen von Berechtigungen für eine verwaltete Identität erfolgt auf die gleiche Weise wie das Erteilen von Berechtigungen für jeden anderen Azure AD-Benutzer auch.

### <a name="anonymous-access"></a>[Anonymer Zugriff](#tab/public-access)

Sie können auf öffentlich verfügbare Dateien in Azure-Speicherkonten zugreifen, die [anonymen Zugriff zulassen](/azure/storage/blobs/storage-manage-access-to-resources).

---

### <a name="supported-authorization-types-for-databases-users"></a>Unterstützte Autorisierungstypen für Datenbankbenutzer

In der folgenden Tabelle sind die verfügbaren Autorisierungstypen aufgeführt:

| Autorisierungstyp                    | *SQL-Benutzer*    | *Azure AD-Benutzer*     |
| ------------------------------------- | ------------- | -----------    |
| [Benutzeridentität](?tabs=user-identity#supported-storage-authorization-types)       | Nicht unterstützt | Unterstützt      |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)       | Unterstützt     | Unterstützt      |
| [Verwaltete Identität](?tabs=managed-identity#supported-storage-authorization-types) | Nicht unterstützt | Unterstützt      |

### <a name="supported-storages-and-authorization-types"></a>Unterstützte Speicher- und Autorisierungstypen

Sie können die folgenden Kombinationen aus Autorisierungstypen und Azure Storage-Typen verwenden:

| Autorisierungstyp  | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)    | Unterstützt\*      | Nicht unterstützt   | Unterstützt\*     |
| [Verwaltete Identität](?tabs=managed-identity#supported-storage-authorization-types) | Unterstützt      | Unterstützt        | Unterstützt     |
| [Benutzeridentität](?tabs=user-identity#supported-storage-authorization-types)    | Unterstützt\*      | Unterstützt\*        | Unterstützt\*     |

\* SAS-Token und Azure AD-Identität können für den Zugriff auf Speicher verwendet werden, der nicht durch eine Firewall geschützt ist.

> [!IMPORTANT]
> Beim Zugriff auf Speicher, der mit der Firewall geschützt ist, kann nur die verwaltete Identität verwendet werden. Sie müssen [vertrauenswürdige Microsoft-Dienste zulassen](../../storage/common/storage-network-security.md#trusted-microsoft-services) und der [systemseitig zugewiesenen Identität](../../active-directory/managed-identities-azure-resources/overview.md) für diese Ressourceninstanz explizit [eine Azure-Rolle zuweisen](../../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights). In diesem Fall entspricht der Zugriffsbereich für die Instanz der Azure-Rolle, die der verwalteten Identität zugewiesen ist.
>

## <a name="credentials"></a>Anmeldeinformationen

Um eine Datei in Azure Storage abzufragen, benötigt der Endpunkt für Ihren serverlosen SQL-Pool eine Anmeldeinformation, die die Authentifizierungsinformationen enthält. Es werden zwei Arten von Anmeldeinformationen verwendet:
- Eine Anmeldeinformation auf Serverebene wird für Ad-hoc-Abfragen verwendet, die mit der `OPENROWSET`-Funktion ausgeführt werden. Der Name der Anmeldeinformation muss mit der Speicher-URL identisch sein.
- Eine datenbankbezogene Anmeldeinformation wird für externe Tabellen verwendet. Eine externe Tabelle verweist mit der Anmeldeinformation auf `DATA SOURCE`, die für den Speicherzugriff verwendet werden soll.

Um einem Benutzer das Erstellen oder Verwerfen von Anmeldeinformationen zu ermöglichen, können Administratoren dem Benutzer die Berechtigung GRANT/DENY ALTER ANY CREDENTIAL erteilen:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

Datenbankbenutzer, die auf den externen Speicher zugreifen, müssen über die Berechtigung zur Verwendung von Anmeldeinformationen verfügen.

### <a name="grant-permissions-to-use-credential"></a>Erteilen von Berechtigungen zur Verwendung einer Anmeldeinformation

Um eine Anmeldeinformation verwenden zu können, muss ein Benutzer über die Berechtigung `REFERENCES` für diese Anmeldeinformation verfügen. Um dem Benutzer „specific_user“ die Berechtigung `REFERENCES` für die Anmeldeinformation „storage_credential“ zu erteilen, führen Sie folgenden Befehl aus:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

Um reibungslose Azure AD-Pass-Through-Vorgänge sicherzustellen, verfügen alle Benutzer standardmäßig über die Berechtigung zur Verwendung der Anmeldeinformation `UserIdentity`.

## <a name="server-scoped-credential"></a>Serverbezogene Anmeldeinformationen

Serverbezogene Anmeldeinformationen werden verwendet, wenn die SQL-Anmeldung die `OPENROWSET`-Funktion ohne `DATA_SOURCE` aufruft, um Dateien in einem Speicherkonto zu lesen. Der Name der serverbezogenen Anmeldeinformation **muss** der URL des Azure-Speichers entsprechen. Eine Anmeldeinformation wird durch Ausführen von [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) hinzugefügt. Sie müssen ein CREDENTIAL NAME-Argument angeben. Dieses muss entweder einem Teil des Pfads oder dem gesamten Pfad zu den Daten in Storage entsprechen (siehe unten).

> [!NOTE]
> Das Argument `FOR CRYPTOGRAPHIC PROVIDER` wird nicht unterstützt.

Der Name der Anmeldeinformation auf Serverebene muss dem vollständigen Pfad zum Speicherkonto (und optional dem Container) im Format `<prefix>://<storage_account_path>/<storage_path>` entsprechen. Die Speicherkontopfade sind in der folgenden Tabelle aufgeführt:

| Externe Datenquelle       | Präfix | Speicherkontopfad                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <Speicherkonto>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | https  | <Speicherkonto>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | https  | <Speicherkonto>.dfs.core.windows.net              |

Serverbezogene Anmeldeinformationen ermöglichen den Zugriff auf den Azure-Speicher mithilfe der folgenden Authentifizierungstypen:

### <a name="user-identity"></a>[Benutzeridentität](#tab/user-identity)

Azure AD-Benutzer können auf alle Dateien im Azure-Speicher zugreifen, wenn sie über die Rolle `Storage Blob Data Owner`, `Storage Blob Data Contributor` oder `Storage Blob Data Reader` verfügen. Azure AD-Benutzer benötigen keine Anmeldeinformationen für den Zugriff auf den Speicher. 

SQL-Benutzer können Azure AD-Authentifizierung nicht für den Zugriff auf den Speicher verwenden.

### <a name="shared-access-signature"></a>[Shared Access Signature (SAS)](#tab/shared-access-signature)

Mit dem folgenden Skript wird eine Anmeldeinformation auf Serverebene erstellt, die von der `OPENROWSET`-Funktion für den Zugriff auf eine beliebige Datei im Azure-Speicher mit dem SAS-Token verwendet werden kann. Erstellen Sie diese Anmeldeinformation, um dem SQL-Prinzipal, der die `OPENROWSET`-Funktion ausführt, das Lesen von Dateien zu ermöglichen, die mit dem SAS-Schlüssel in dem Azure-Speicher geschützt sind, der der URL im Anmeldeinformationsnamen entspricht.

Ersetzen Sie <*mystorageaccountname*> durch den tatsächlichen Namen Ihres Speicherkontos und <*mystorageaccountcontainername*> durch den tatsächlichen Namen des Containers:

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

### <a name="managed-identity"></a>[Verwaltete Identität](#tab/managed-identity)

Mit dem folgenden Skript wird eine Anmeldeinformation auf Serverebene erstellt, die von der `OPENROWSET`-Funktion für den Zugriff auf eine beliebige Datei im Azure-Speicher mit der verwalteten Identität eines Arbeitsbereichs verwendet werden kann.

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='Managed Identity'
```

### <a name="public-access"></a>[Öffentlicher Zugriff](#tab/public-access)

Es ist keine datenbankbezogene Anmeldeinformation für den Zugriff auf öffentlich verfügbare Dateien erforderlich. Erstellen Sie eine [Datenquelle ohne datenbankbezogene Anmeldeinformation](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source), um auf öffentlich verfügbare Dateien im Azure-Speicher zuzugreifen.

---

## <a name="database-scoped-credential"></a>Datenbankbezogene Anmeldeinformationen

Datenbankbezogene Anmeldeinformationen werden verwendet, wenn ein Prinzipal die `OPENROWSET`-Funktion mit `DATA_SOURCE` aufruft oder Daten aus [externen Tabellen](develop-tables-external-tables.md) auswählt, die nicht auf öffentliche Dateien zugreifen. Die datenbankweit gültigen Anmeldeinformationen müssen nicht mit dem Namen des Speicherkontos übereinstimmen. Sie werden explizit in dem DATA_SOURCE-Ausdruck verwendet, der den Standort des Speichers definiert.

Datenbankbezogene Anmeldeinformationen ermöglichen den Zugriff auf den Azure-Speicher mithilfe der folgenden Authentifizierungstypen:

### <a name="azure-ad-identity"></a>[Azure AD-Identität](#tab/user-identity)

Azure AD-Benutzer können auf alle Dateien im Azure-Speicher zugreifen, wenn sie mindestens über die Rolle `Storage Blob Data Owner`, `Storage Blob Data Contributor` oder `Storage Blob Data Reader` verfügen. Azure AD-Benutzer benötigen keine Anmeldeinformationen für den Zugriff auf den Speicher.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
)
```

SQL-Benutzer können Azure AD-Authentifizierung nicht für den Zugriff auf den Speicher verwenden.

### <a name="shared-access-signature"></a>[Shared Access Signature (SAS)](#tab/shared-access-signature)

Mit dem folgenden Skript wird eine Anmeldeinformation erstellt, die für den Zugriff auf Dateien im Speicher mit dem in der Anmeldeinformation angegebenen SAS-Token verwendet wird. Mit dem Skript wird eine externe Beispieldatenquelle erstellt, die dieses SAS-Token für den Zugriff auf Speicher verwendet.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>'
GO
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SasToken
)
```

### <a name="managed-identity"></a>[Verwaltete Identität](#tab/managed-identity)

Mit dem folgenden Skript wird eine datenbankbezogene Anmeldeinformation erstellt, mit der der aktuelle Azure AD-Benutzer die verwaltete Identität des Diensts annehmen kann. Mit dem Skript wird eine externe Beispieldatenquelle erstellt, die die Arbeitsbereichsidentität für den Zugriff auf Speicher verwendet.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>
CREATE DATABASE SCOPED CREDENTIAL SynapseIdentity
WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SynapseIdentity
)
```

Die datenbankbezogene Anmeldeinformation muss nicht mit dem Namen des Speicherkontos übereinstimmen, da sie explizit in der Datenquelle verwendet wird, die den Speicherort des Speichers definiert.

### <a name="public-access"></a>[Öffentlicher Zugriff](#tab/public-access)

Es ist keine datenbankbezogene Anmeldeinformation für den Zugriff auf öffentlich verfügbare Dateien erforderlich. Erstellen Sie eine [Datenquelle ohne datenbankbezogene Anmeldeinformation](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source), um auf öffentlich verfügbare Dateien im Azure-Speicher zuzugreifen.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.blob.core.windows.net/<container>/<path>'
)
```
---

Datenbankbezogene Anmeldinformationen werden in externen Datenquellen verwendet, um anzugeben, welche Authentifizierungsmethode für den Zugriff auf diesen Speicher verwendet werden soll:

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = <name of database scoped credential> 
)
```

## <a name="examples"></a>Beispiele

### <a name="access-a-publicly-available-data-source"></a>**Zugreifen auf eine öffentlich verfügbare Datenquelle**

Verwenden Sie das folgende Skript, um eine Tabelle zu erstellen, die auf eine öffentlich verfügbare Datenquelle zugreift.

```sql
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat]
       WITH ( FORMAT_TYPE = PARQUET)
GO
CREATE EXTERNAL DATA SOURCE publicData
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<public_container>/<path>' )
GO

CREATE EXTERNAL TABLE dbo.userPublicData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [publicData],
       FILE_FORMAT = [SynapseParquetFormat] )
```

Der Datenbankbenutzer kann den Inhalt der Dateien aus der Datenquelle mithilfe einer externen Tabelle oder der [OPENROWSET](develop-openrowset.md)-Funktion lesen, die auf die Datenquelle verweist:

```sql
SELECT TOP 10 * FROM dbo.userPublicData;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet',
                                DATA_SOURCE = 'mysample',
                                FORMAT='PARQUET') as rows;
GO
```

### <a name="access-a-data-source-using-credentials"></a>**Zugreifen auf eine Datenquelle mithilfe von Anmeldeinformationen**

Ändern Sie das folgende Skript, um eine externe Tabelle zu erstellen, die mit dem SAS-Token, der Azure AD-Identität des Benutzers oder der verwalteten Identität des Arbeitsbereichs auf den Azure-Speicher zugreift.

```sql
-- Create master key in databases with some password (one-off per database)
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Y*********0'
GO

-- Create databases scoped credential that use Managed Identity or SAS token. User needs to create only database-scoped credentials that should be used to access data source:

CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity
WITH IDENTITY = 'Managed Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL SasCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2019-10-1********ZVsTOL0ltEGhf54N8KhDCRfLRI%3D'

-- Create data source that one of the credentials above, external file format, and external tables that reference this data source and file format:

CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO

CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
-- Uncomment one of these options depending on authentication method that you want to use to access data source:
--,CREDENTIAL = WorkspaceIdentity 
--,CREDENTIAL = SasCredential 
)

CREATE EXTERNAL TABLE dbo.userData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [mysample],
       FILE_FORMAT = [SynapseParquetFormat] );

```

Der Datenbankbenutzer kann den Inhalt der Dateien aus der Datenquelle mithilfe einer [externen Tabelle](develop-tables-external-tables.md) oder der [OPENROWSET](develop-openrowset.md)-Funktion lesen, die auf die Datenquelle verweist:

```sql
SELECT TOP 10 * FROM dbo.userdata;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = 'mysample', FORMAT='PARQUET') as rows;
GO
```

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln finden Sie wichtige Informationen dazu, wie Sie verschiedene Arten von Ordnern und Dateien abfragen und Sichten erstellen und verwenden:

- [Abfragen einer einzelnen CSV-Datei](query-single-csv-file.md)
- [Abfragen von Ordnern und mehreren CSV-Dateien](query-folders-multiple-csv-files.md)
- [Abfragen bestimmter Dateien](query-specific-files.md)
- [Abfragen von Parquet-Dateien](query-parquet-files.md)
- [Erstellen und Verwenden von Sichten](create-use-views.md)
- [Abfragen von JSON-Dateien](query-json-files.md)
- [Abfragen von geschachtelten Parquet-Typen](query-parquet-nested-types.md)
