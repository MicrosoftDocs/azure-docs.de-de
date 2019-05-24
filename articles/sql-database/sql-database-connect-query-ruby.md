---
title: Abfragen von Azure SQL-Datenbank mithilfe von Ruby | Microsoft-Dokumentation
description: In diesem Thema erfahren Sie, wie Sie unter Verwendung von Ruby ein Programm erstellen, das eine Verbindung mit einer Azure SQL-Datenbank herstellt und sie mithilfe von Transact-SQL-Anweisungen abfragt.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ruby
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: d674928bbe585174db897b2a052a5fd09bcee329
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792072"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>Schnellstart: Abfragen einer Azure SQL-Datenbank mithilfe von Ruby

In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von [Ruby](https://www.ruby-lang.org) eine Verbindung mit einer Azure SQL-Datenbank herstellen und Daten mit Transact-SQL-Anweisungen abfragen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durchführen dieser Schnellstartanleitung benötigen Sie Folgendes:

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
  
- Ruby und verwandte Software für Ihr Betriebssystem:
  
  - **MacOS**: Installieren Sie Homebrew, rbenv und ruby-build, Ruby, FreeTDS und TinyTDS. Siehe Schritte 1.2, 1.3, 1.4, 1.5 und 2.1 in [Erstellen von Ruby-Apps mit SQL Server unter macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
  
  - **Ubuntu**: Installieren Sie die Voraussetzungen für Ruby, rbenv und ruby-build, Ruby, FreeTDS und TinyTDS. Siehe Schritte 1.2, 1.3, 1.4, 1.5 und 2.1 in [Erstellen von Ruby-Apps mit SQL Server unter Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).
  
  - **Windows:** Installieren Sie Ruby, Ruby Devkit und TinyTDS. Siehe [Konfigurieren der Entwicklungsumgebung für die Ruby-Entwicklung](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development).

## <a name="get-sql-server-connection-information"></a>Abrufen von SQL Server-Verbindungsinformationen

Rufen Sie die Verbindungsinformationen ab, die Sie zum Herstellen einer Verbindung mit der Azure SQL-Datenbank benötigen. In den weiteren Verfahren benötigen Sie den vollqualifizierten Server- oder Hostnamen, den Datenbanknamen und die Anmeldeinformationen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Navigieren Sie zur Seite **SQL-Datenbanken** oder **Verwaltete SQL-Instanzen**.

3. Sehen Sie sich auf der Seite **Übersicht** bei einer Einzeldatenbank den vollqualifizierten Servernamen neben **Servername** und bei einer verwalteten Instanz den vollqualifizierten Servernamen neben **Host** an. Um den Namen des Servers oder Hosts zu kopieren, zeigen Sie darauf, und wählen Sie das Symbol **Kopieren** aus. 

## <a name="create-code-to-query-your-sql-database"></a>Erstellen von Code zum Abfragen Ihrer SQL-Datenbank

1. Erstellen Sie in einem Text- oder Code-Editor eine neue Datei namens *sqltest.rb*.
   
1. Fügen Sie den folgenden Code hinzu: Ersetzen Sie die Werte aus der Azure SQL-Datenbank für `<server>`, `<database>`, `<username>` und `<password>`.
   
   >[!IMPORTANT]
   >Der Code in diesem Beispiel verwendet die AdventureWorksLT-Beispieldaten, die Sie beim Erstellen Ihrer Datenbank als Datenquelle auswählen können. Wenn Ihre Datenbank unterschiedliche Daten aufweist, verwenden Sie Tabellen aus Ihrer eigenen Datenbank in der SELECT-Abfrage. 
   
   ```ruby
   require 'tiny_tds'
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   client = TinyTds::Client.new username: username, password: password, 
       host: server, port: 1433, database: database, azure: true
   
   puts "Reading data from table"
   tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
           FROM [SalesLT].[ProductCategory] pc
           JOIN [SalesLT].[Product] p
           ON pc.productcategoryid = p.productcategoryid"
   result = client.execute(tsql)
   result.each do |row|
       puts row
   end
   ```

## <a name="run-the-code"></a>Ausführen des Codes

1. Führen Sie an der Eingabeaufforderung folgenden Befehl aus:

   ```bash
   ruby sqltest.rb
   ```
   
1. Stellen Sie sicher, dass die obersten 20 Kategorie-/Produktzeilen aus Ihrer Datenbank zurückgegeben werden. 

## <a name="next-steps"></a>Nächste Schritte
- [Entwurf Ihrer ersten Azure SQL-Datenbank](sql-database-design-first-database.md)
- [GitHub-Repository für TinyTDS](https://github.com/rails-sqlserver/tiny_tds)
- [Problemmeldungen oder Fragestellungen für TinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues)
- [Ruby-Treiber für SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)
