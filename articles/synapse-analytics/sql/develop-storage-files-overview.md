---
title: Zugreifen auf Dateien im Speicher in einem serverlosen SQL-Pool
description: Hier erfahren Sie, wie Sie Speicherdateien mithilfe eines serverlosen SQL-Pools in Azure Synapse Analytics abfragen.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/19/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 82a4ded3a64a8a8bbc62f99a8854eb6d2b5f0d0b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446570"
---
# <a name="access-external-storage-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Zugreifen auf externen Speicher mithilfe eines serverlosen SQL-Pools in Azure Synapse Analytics

In diesem Dokument wird beschrieben, wie Benutzer Daten aus den Dateien lesen können, die in Azure Storage in einem serverlosen SQL-Pool gespeichert sind. Benutzer haben die folgenden Optionen, um auf Speicher zuzugreifen:

- Funktion [OPENROWSET](develop-openrowset.md) zum Durchführen von Ad-hoc-Abfragen für die Dateien in Azure Storage.
- [Externe Tabelle](develop-tables-external-tables.md), bei der es sich um eine vordefinierte Datenstruktur handelt, die auf einer Gruppe von externen Dateien basiert.

Benutzer können [verschiedene Authentifizierungsmethoden](develop-storage-files-storage-access-control.md) verwenden, z. B. Azure AD-Pass-Through-Authentifizierung (Standard für Azure AD-Prinzipale) und SAS-Authentifizierung (Standard für SQL-Prinzipale).

## <a name="query-files-using-openrowset"></a>Abfragen von Dateien mit OPENROWSET

Mit OPENROWSET können Benutzer externe Dateien im Azure-Speicher abfragen, sofern sie über Zugriff auf den Speicher verfügen. Der Benutzer, der mit einem serverlosen SQL-Pool verbunden ist, sollte die folgende Abfrage verwenden, um den Inhalt der Dateien im Azure-Speicher zu lesen:

```sql
SELECT * FROM
 OPENROWSET(BULK 'https://<storage_account>.dfs.core.windows.net/<container>/<path>/*.parquet', format= 'parquet') as rows
```

Der Benutzer kann mit den folgenden Zugriffsregeln auf den Speicher zugreifen:

- Azure AD-Benutzer: Bei `OPENROWSET` wird die Azure AD-Identität des Aufrufers verwendet, um auf Azure Storage zuzugreifen, oder der Zugriff auf den Speicher erfolgt anonym.
- SQL-Benutzer: Bei `OPENROWSET` erfolgt der Zugriff auf den Speicher anonym, oder die Identität kann mithilfe eines SAS-Tokens oder der verwalteten Identität des Arbeitsbereichs angenommen werden.

### <a name="impersonation"></a>[Identitätswechsel](#tab/impersonation)

Für SQL-Prinzipale kann OPENROWSET auch verwendet werden, um Dateien direkt abzufragen, die mit SAS-Token oder per verwalteter Identität des Arbeitsbereichs geschützt sind. Wenn ein SQL-Benutzer diese Funktion ausführt, muss ein Poweruser mit der Berechtigung `ALTER ANY CREDENTIAL` serverbezogene Anmeldeinformationen erstellen, die mit der URL in der Funktion übereinstimmen (mit Verwendung eines Speichernamens und Containers). Darüber hinaus muss dem Aufrufer der Funktion OPENROWSET die Berechtigung REFERENCES für diese Anmeldeinformationen gewährt werden:

```sql
EXECUTE AS somepoweruser

CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sas token';

GRANT REFERENCES CREDENTIAL::[https://<storage_account>.dfs.core.windows.net/<container>] TO sqluser
```

Falls keine serverbezogenen Anmeldeinformationen vorhanden sind, die mit der URL übereinstimmen, oder wenn der SQL-Benutzer nicht die Berechtigung REFERENCES für diese Anmeldeinformationen hat, wird der Fehler zurückgegeben. Für SQL-Prinzipale kann die Identität nicht über eine Azure AD-Identität gewechselt werden.

### <a name="direct-access"></a>[Direktzugriff](#tab/direct-access)

Es ist kein zusätzliches Setup erforderlich, um Azure AD-Benutzern den Zugriff auf die Dateien unter Verwendung ihrer Identitäten zu ermöglichen.
Jeder Benutzer kann auf den Azure-Speicher zugreifen, der anonymen Zugriff zulässt (es ist kein zusätzliches Setup erforderlich).

---

> [!NOTE]
> Diese Version von OPENROWSET ist auf eine schnelle und einfache Datenuntersuchung per Standardauthentifizierung ausgelegt. Verwenden Sie OPENROWSET mit DATASOURCE wie im nächsten Abschnitt beschrieben, um den Identitätswechsel oder die verwaltete Identität zu nutzen.

## <a name="query-data-sources-using-openrowset"></a>Abfragen von Datenquellen mit OPENROWSET

Mit OPENROWSET können Benutzer die Dateien abfragen, die sich auf einer externen Datenquelle befinden:

```sql
SELECT * FROM
 OPENROWSET(BULK 'file/path/*.parquet',
 DATASOURCE = MyAzureInvoices,
 FORMAT= 'parquet') as rows
```

Der Benutzer, der diese Abfrage ausführt, muss auf die Dateien zugreifen können. Die Identität der Benutzer muss mithilfe eines [SAS-Tokens](develop-storage-files-storage-access-control.md?tabs=shared-access-signature) oder der [verwalteten Identität des Arbeitsbereichs](develop-storage-files-storage-access-control.md?tabs=managed-identity) angenommen werden, wenn der direkte Zugriff auf die Dateien mithilfe ihrer [Azure AD-Identität](develop-storage-files-storage-access-control.md?tabs=user-identity) oder [anonym](develop-storage-files-storage-access-control.md?tabs=public-access) nicht möglich ist.

### <a name="impersonation"></a>[Identitätswechsel](#tab/impersonation)

Mit `DATABASE SCOPED CREDENTIAL` wird angegeben, wie auf Dateien in der referenzierten Datenquelle zugegriffen wird (derzeit SAS und verwaltete Identität). Ein Poweruser mit der Berechtigung `CONTROL DATABASE` muss datenbankbezogene Anmeldeinformationen (`DATABASE SCOPED CREDENTIAL`), die zum Zugreifen auf den Speicher verwendet werden, sowie eine externe Datenquelle (`EXTERNAL DATA SOURCE`) erstellen, für die die zugehörige URL und die zu verwendenden Anmeldeinformationen angegeben sind:

```sql
EXECUTE AS somepoweruser;

-- Create MASTER KEY if it doesn't exists in database
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'some very strong password';

CREATE DATABASE SCOPED CREDENTIAL AccessAzureInvoices
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&amp;sp=rwac&amp;se=2017-02-01T00:55:34Z&amp;st=201********' ;

CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>/' ,
 CREDENTIAL = AccessAzureInvoices) ;
```

Der Aufrufer muss über eine der folgenden Berechtigungen verfügen, um die Funktion OPENROWSET ausführen zu können:

- Eine der Berechtigungen zum Ausführen von OPENROWSET:
  - `ADMINISTER BULK OPERATIONS` ermöglicht die Anmeldung zum Ausführen der Funktion OPENROWSET.
  - Mit `ADMINISTER DATABASE BULK OPERATIONS` kann der Datenbankbenutzer die Funktion OPENROWSET ausführen.
- `REFERENCES DATABASE SCOPED CREDENTIAL` für die Anmeldeinformationen, auf die in `EXTERNAL DATA SOURCE` verwiesen wird

### <a name="direct-access"></a>[Direktzugriff](#tab/direct-access)

Benutzer können die externe Datenquelle (EXTERNAL DATA SOURCE) ohne Anmeldeinformationen erstellen, mit denen auf öffentlich zugänglichen Speicher verwiesen wird, ODER die Azure AD-Pass-Through-Authentifizierung verwenden:

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>') ;
```
---
## <a name="external-table"></a>EXTERNAL TABLE

Benutzer mit den Berechtigungen zum Lesen der Tabelle können auf externe Dateien über eine externe Tabelle (EXTERNAL TABLE) zugreifen, die basierend auf einer Gruppe von Azure Storage-Ordnern und -Dateien erstellt wurde.

Benutzer, die über [Berechtigungen zum Erstellen einer externen Tabelle](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=sql-server-ver15#permissions&preserve-view=true) verfügen (z. B. CREATE TABLE und ALTER ANY CREDENTIAL oder REFERENCES DATABASE SCOPED CREDENTIAL), können mit dem folgenden Skript basierend auf der Azure Storage-Datenquelle eine Tabelle erstellen:

```sql
CREATE EXTERNAL TABLE [dbo].[DimProductexternal]
( ProductKey int, ProductLabel nvarchar, ProductName nvarchar )
WITH
(
LOCATION='/DimProduct/year=*/month=*' ,
DATA_SOURCE = AzureDataLakeStore ,
FILE_FORMAT = TextFileFormat
) ;
```

Ein Benutzer, der Daten aus dieser Tabelle liest, muss auf die Dateien zugreifen können. Die Identität der Benutzer muss mithilfe eines [SAS-Tokens](develop-storage-files-storage-access-control.md?tabs=shared-access-signature) oder der [verwalteten Identität des Arbeitsbereichs](develop-storage-files-storage-access-control.md?tabs=managed-identity) angenommen werden, wenn der direkte Zugriff auf die Dateien mithilfe ihrer [Azure AD-Identität](develop-storage-files-storage-access-control.md?tabs=user-identity) oder [anonym](develop-storage-files-storage-access-control.md?tabs=public-access) nicht möglich ist.

### <a name="impersonation"></a>[Identitätswechsel](#tab/impersonation)

Mit DATABASE SCOPED CREDENTIAL wird angegeben, wie auf die Dateien auf der referenzierten Datenquelle zugegriffen wird. Benutzer mit der Berechtigung CONTROL DATABASE müssen datenbankbezogene Anmeldeinformationen (DATABASE SCOPED CREDENTIAL) erstellen, die zum Zugreifen auf den Speicher verwendet werden, sowie eine externe Datenquelle (EXTERNAL DATA SOURCE), für die die zugehörige URL und die zu verwendenden Anmeldeinformationen angegeben sind:

```sql
EXECUTE AS somepoweruser;

-- Create MASTER KEY if it doesn't exists in database
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'some very strong password';

CREATE DATABASE SCOPED CREDENTIAL cred
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=201********' ;

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>' ,
 CREDENTIAL = cred
 ) ;
```

### <a name="direct-access"></a>[Direktzugriff](#tab/direct-access)

Benutzer können die externe Datenquelle (EXTERNAL DATA SOURCE) ohne Anmeldeinformationen erstellen, mit denen auf öffentlich zugänglichen Speicher verwiesen wird, ODER die Azure AD-Pass-Through-Authentifizierung verwenden:

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>') ;
```
---

### <a name="read-external-files-with-external-table"></a>Lesen von externen Dateien mit EXTERNAL TABLE

Mit EXTERNAL TABLE können Sie Daten aus den Dateien lesen, auf die über die Datenquelle verwiesen wird, indem Sie die SQL-SELECT-Standardanweisung verwenden:

```sql
SELECT *
FROM dbo.DimProductsExternal
```

Der Aufrufer muss über die folgenden Berechtigungen zum Lesen von Daten verfügen:
- Berechtigung `SELECT` für die externe Tabelle
- Berechtigung `REFERENCES DATABASE SCOPED CREDENTIAL`, wenn `DATA SOURCE` über `CREDENTIAL` verfügt

## <a name="permissions"></a>Berechtigungen

In der folgenden Tabelle sind die erforderlichen Berechtigungen für die oben angegebenen Vorgänge aufgeführt.

| Abfrage | Erforderliche Berechtigungen|
| --- | --- |
| OPENROWSET(BULK) ohne Datenquelle | `ADMINISTER BULK OPERATIONS`, `ADMINISTER DATABASE BULK OPERATIONS` oder SQL-Anmeldename muss über „REFERENCES CREDENTIAL::\<URL>“ für den per SAS geschützten Speicher verfügen |
| OPENROWSET(BULK) mit Datenquelle ohne Anmeldeinformationen | `ADMINISTER BULK OPERATIONS` oder `ADMINISTER DATABASE BULK OPERATIONS` |
| OPENROWSET(BULK) mit Datenquelle mit Anmeldeinformationen | `REFERENCES DATABASE SCOPED CREDENTIAL` und `ADMINISTER BULK OPERATIONS` oder `ADMINISTER DATABASE BULK OPERATIONS` |
| CREATE EXTERNAL DATA SOURCE | `ALTER ANY EXTERNAL DATA SOURCE` und `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CREATE EXTERNAL TABLE | `CREATE TABLE`, `ALTER ANY SCHEMA`, `ALTER ANY EXTERNAL FILE FORMAT` und `ALTER ANY EXTERNAL DATA SOURCE` |
| SELECT FROM EXTERNAL TABLE | `SELECT TABLE` und `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CETAS | Erstellen einer Tabelle: `CREATE TABLE`, `ALTER ANY SCHEMA`, `ALTER ANY DATA SOURCE` und `ALTER ANY EXTERNAL FILE FORMAT`. Lesen von Daten: `ADMINISTER BULK OPERATIONS`, `REFERENCES CREDENTIAL` oder `SELECT TABLE` pro Tabelle/Ansicht/Funktion in der Abfrage sowie Lese-/Schreibberechtigung im Speicher |

## <a name="next-steps"></a>Nächste Schritte

Nun können Sie mit den folgenden Anleitungsartikeln fortfahren:

- [Abfragen von Daten im Speicher](query-data-storage.md)

- [Abfragen einer CSV-Datei](query-single-csv-file.md)

- [Abfragen von Parquet-Dateien](query-parquet-files.md)

- [Abfragen von JSON-Dateien](query-json-files.md)

- [Abfragen von Ordnern und mehreren Dateien](query-folders-multiple-csv-files.md)

- [Verwenden von Dateimetadaten in Abfragen](query-specific-files.md)

- [Abfragen von geschachtelten Typen](query-parquet-nested-types.md)

- [Erstellen und Verwenden von Sichten](create-use-views.md)
