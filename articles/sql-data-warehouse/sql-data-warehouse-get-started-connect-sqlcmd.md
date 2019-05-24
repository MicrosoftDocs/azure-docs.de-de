---
title: Verbinden mit Azure SQL Data Warehouse mithilfe von sqlcmd | Microsoft-Dokumentation
description: Verwenden Sie das Befehlszeilenprogramm sqlcmd, um eine Verbindung mit einer Azure SQL Data Warehouse-Instanz herzustellen und diese abzufragen.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 72760c5123703a664695c1be4d286a38e96ecc3f
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873316"
---
# <a name="connect-to-sql-data-warehouse-with-sqlcmd"></a>Verbinden mit SQL Data Warehouse per sqlcmd
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Verwenden Sie das Befehlszeilenprogramm [sqlcmd][sqlcmd], um eine Verbindung mit einer Azure SQL Data Warehouse-Instanz herzustellen und diese abzufragen.  

## <a name="1-connect"></a>1. Verbinden
Öffnen Sie zur Verwendung von [sqlcmd][sqlcmd] die Eingabeaufforderung, und geben Sie **sqlcmd** sowie die Verbindungszeichenfolge für Ihre SQL Data Warehouse-Datenbank ein. Die Verbindungszeichenfolge muss die folgenden Parameter enthalten:

* **Server (-S):** Server in Form von `<`Servername`>`.database.windows.net
* **Datenbank (-d):** Datenbankname.
* **Bezeichner in Anführungszeichen aktivieren (-I):** Bezeichner müssen in Anführungszeichen eingeschlossen sein, um die Verbindung mit einer SQL Data Warehouse-Instanz herzustellen.

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
> 
> 

## <a name="2-query"></a>2. Abfragen
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
Ausführlichere Informationen zu den verfügbaren Optionen für „sqlcmd“ finden Sie in der [Dokumentation zu „sqlcmd“][sqlcmd].

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
