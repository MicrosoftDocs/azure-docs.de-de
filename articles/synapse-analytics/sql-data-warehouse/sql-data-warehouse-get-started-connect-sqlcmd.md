---
title: Herstellen einer Verbindung mit sqlcmd
description: Verwenden Sie das Befehlszeilenprogramm sqlcmd, um eine Verbindung mit einem Synapse-SQL-Pool herzustellen und diesen abzufragen.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f61f2932ad9c6342a81ec05df335e9ea4d659fce
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461978"
---
# <a name="connect-to-sql-pool-in-azure-synapse-analytics-with-sqlcmd"></a>Herstellen einer Verbindung mit einem SQL-Pool in Azure Synapse Analytics mithilfe von sqlcmd

> [!div class="op_single_selector"]
>
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

Verwenden Sie das Befehlszeilenprogramm sqlcmd, um eine Verbindung mit einem SQL-Pool herzustellen und diesen abzufragen.  

## <a name="1-connect"></a>1. Verbinden

Öffnen Sie zur Verwendung von sqlcmd die Eingabeaufforderung, und geben Sie **sqlcmd** gefolgt von der Verbindungszeichenfolge für Ihren SQL-Pool ein. Die Verbindungszeichenfolge muss die folgenden Parameter enthalten:

* **Server (-S):** Server in Form von `<`Servername`>`.database.windows.net
* **Datenbank (-d):** Name des SQL-Pools
* **Bezeichner in Anführungszeichen aktivieren (-I):** Bezeichner in Anführungszeichen müssen aktiviert sein, um eine Verbindung mit einer SQL-Pool-Instanz herzustellen.

Zur Verwendung der SQL Server-Authentifizierung müssen Sie die Parameter für Benutzername und Kennwort hinzufügen:

* **Benutzer (-U):** Serverbenutzer im Format `<`Benutzer`>`
* **Kennwort (-P):** Das Kennwort des Benutzers.

Die Verbindungszeichenfolge kann beispielsweise wie folgt aussehen:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Um die in Azure Active Directory integrierte Authentifizierung verwenden zu können, müssen Sie die Azure Active Directory-Parameter hinzufügen:

* **Azure Active Directory-Authentifizierung (-G):** Azure Active Directory für die Authentifizierung verwenden

Die Verbindungszeichenfolge kann beispielsweise wie folgt aussehen:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Sie müssen [Azure Active Directory-Authentifizierung aktivieren](sql-data-warehouse-authentication.md) , um die Authentifizierung mithilfe von Active Directory zu ermöglichen.

## <a name="2-query"></a>2. Abfrage

Nach dem Herstellen der Verbindung können Sie alle unterstützten Transact-SQL-Anweisungen für die Instanz ausgeben.  In diesem Beispiel werden Abfragen im interaktiven Modus gesendet.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Die nächsten Beispiele zeigen, wie Sie Ihre Abfragen im Batchmodus ausführen können, indem Sie die Option „-Q“ oder für den SQL-Code ein Pipe-Zeichen zur Verknüpfung mit „sqlcmd“ verwenden.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>Nächste Schritte

Ausführlichere Informationen zu den in sqlcmd verfügbaren Optionen finden Sie in der [Dokumentation zu sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).
