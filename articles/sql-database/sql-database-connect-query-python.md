---
title: Abfragen von Azure SQL-Datenbank mithilfe von Python | Microsoft-Dokumentation
description: In diesem Thema erfahren Sie, wie Sie unter Verwendung von Python ein Programm erstellen, das eine Verbindung mit einer Azure SQL-Datenbank herstellt und sie mithilfe von Transact-SQL-Anweisungen abfragt.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 7fbb9265ac12126fb13a26650fbb5d65f3d39260
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792078"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Schnellstart: Abfragen einer Azure SQL-Datenbank mithilfe von Python

 In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von [Python](https://python.org) eine Verbindung mit einer Azure SQL-Datenbank herstellen und Daten mithilfe von Transact-SQL-Anweisungen abfragen. Ausführlichere SDK-Informationen finden Sie in unserer [Referenzdokumentation](https://docs.microsoft.com/python/api/overview/azure/sql), im [pyodbc-GitHub-Repository](https://github.com/mkleehammer/pyodbc/wiki/) und in einem [pyodbc-Beispiel](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung benötigen Sie Folgendes:

- Eine Azure SQL-Datenbank. In den folgenden Schnellstartanleitungen erfahren Sie jeweils, wie Sie eine Datenbank in Azure SQL-Datenbank erstellen und anschließend konfigurieren:

  || Einzeldatenbank | Verwaltete Instanz |
  |:--- |:--- |:---|
  | Erstellen| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [BEFEHLSZEILENSCHNITTSTELLE (CLI)](scripts/sql-database-create-and-configure-database-cli.md) | [BEFEHLSZEILENSCHNITTSTELLE (CLI)](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurieren | [IP-Firewallregel auf Serverebene](sql-database-server-level-firewall-rule.md)| [Verbindung von einem virtuellen Computer](sql-database-managed-instance-configure-vm.md)|
  |||[Verbindung von einer lokalen Ressource](sql-database-managed-instance-configure-p2s.md)
  |Laden von Daten|Laden von Adventure Works gemäß Schnellstartanleitung|[Wiederherstellen von Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Wiederherstellen oder Importieren von Adventure Works über eine [BACPAC-Datei](sql-database-import.md) von [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Die Skripts in diesem Artikel sind für die Adventure Works-Datenbank geschrieben. Bei einer verwalteten Instanz müssen Sie entweder die Adventure Works-Datenbank in eine Instanzdatenbank importieren oder die Skripts in diesem Artikel zur Verwendung der Wide World Importers-Datenbank anpassen.
  
- Python und verwandte Software für Ihr Betriebssystem:
  
  - **MacOS**: Installieren Sie Homebrew und Python, installieren Sie den ODBC-Treiber und SQLCMD, und installieren Sie anschließend den Python-Treiber für SQL Server. Siehe Schritte 1.2, 1.3 und 2.1 in [Erstellen von Python-Apps mit SQL Server auf macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/). Weitere Informationen finden Sie unter [Installieren des Microsoft ODBC-Treibers unter Linux und macOS](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  - **Ubuntu**: Installieren Sie Python und andere erforderliche Pakete mit `sudo apt-get install python python-pip gcc g++ build-essential`. Laden Sie den ODBC-Treiber, SQLCMD und den Python-Treiber für SQL Server herunter, und installieren Sie sie. Anweisungen hierzu finden Sie unter [Konfigurieren einer Entwicklungsumgebung für pyodbc-Python](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  - **Windows:** Installieren Sie Python, den ODBC-Treiber und SQLCMD sowie den Python-Treiber für SQL Server. Anweisungen hierzu finden Sie unter [Konfigurieren einer Entwicklungsumgebung für pyodbc-Python](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

## <a name="get-sql-server-connection-information"></a>Abrufen von SQL Server-Verbindungsinformationen

Rufen Sie die Verbindungsinformationen ab, die Sie zum Herstellen einer Verbindung mit der Azure SQL-Datenbank benötigen. In den weiteren Verfahren benötigen Sie den vollqualifizierten Server- oder Hostnamen, den Datenbanknamen und die Anmeldeinformationen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Navigieren Sie zur Seite **SQL-Datenbanken** oder **Verwaltete SQL-Instanzen**.

3. Sehen Sie sich auf der Seite **Übersicht** bei einer Einzeldatenbank den vollqualifizierten Servernamen neben **Servername** und bei einer verwalteten Instanz den vollqualifizierten Servernamen neben **Host** an. Um den Namen des Servers oder Hosts zu kopieren, zeigen Sie darauf, und wählen Sie das Symbol **Kopieren** aus.

## <a name="create-code-to-query-your-sql-database"></a>Erstellen von Code zum Abfragen Ihrer SQL-Datenbank 

1. Erstellen Sie in einem Text-Editor eine neue Datei namens *sqltest.py*.  
   
1. Fügen Sie den folgenden Code hinzu: Geben Sie für \<Server>, \<Datenbank>, \<Benutzername> und \<Kennwort> Ihre eigenen Werte ein.
   
   >[!IMPORTANT]
   >Der Code in diesem Beispiel verwendet die AdventureWorksLT-Beispieldaten, die Sie beim Erstellen Ihrer Datenbank als Datenquelle auswählen können. Wenn Ihre Datenbank unterschiedliche Daten aufweist, verwenden Sie Tabellen aus Ihrer eigenen Datenbank in der SELECT-Abfrage. 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   driver= '{ODBC Driver 17 for SQL Server}'
   cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password)
   cursor = cnxn.cursor()
   cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
   row = cursor.fetchone()
   while row:
       print (str(row[0]) + " " + str(row[1]))
       row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>Ausführen des Codes

1. Führen Sie an der Eingabeaufforderung folgenden Befehl aus:

   ```cmd
   python sqltest.py
   ```

1. Stellen Sie sicher, dass die obersten 20 Kategorie-/Produktzeilen zurückgegeben werden, und schließen Sie das Befehlsfenster.

## <a name="next-steps"></a>Nächste Schritte

- [Entwurf Ihrer ersten Azure SQL-Datenbank](sql-database-design-first-database.md)
- [Microsoft-Python-Treiber für SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python Developer Center](https://azure.microsoft.com/develop/python/?v=17.23h)

