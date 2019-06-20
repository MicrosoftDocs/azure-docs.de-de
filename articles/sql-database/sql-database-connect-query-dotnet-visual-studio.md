---
title: Verwenden von Visual Studio mit .NET und C# zum Abfragen der Azure SQL-Datenbank | Microsoft-Dokumentation
description: Verwenden Sie Visual Studio, um eine C#-App zu erstellen, die sich mit einer Azure SQL-Datenbank verbindet und diese mit Transact-SQL-Anweisungen abfragt.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 7870322746d8cb647547e5c85036579611ac3292
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66303305"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>Schnellstart: Herstellen einer Verbindung mit einer Azure SQL-Datenbank und Abfragen der Datenbank mit .NET und C# in Visual Studio

Dieser Schnellstart zeigt, wie Sie mit dem [.NET-Framework](https://www.microsoft.com/net/) und C#-Code in Visual Studio eine Azure SQL-Datenbank mit Transact-SQL-Anweisungen abfragen können.

## <a name="prerequisites"></a>Voraussetzungen

Für die Durchführung dieses Schnellstarts benötigen Sie Folgendes:

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

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) – Community, Professional oder Enterprise Edition.

## <a name="get-sql-server-connection-information"></a>Abrufen von SQL Server-Verbindungsinformationen

Rufen Sie die Verbindungsinformationen ab, die Sie zum Herstellen einer Verbindung mit der Azure SQL-Datenbank benötigen. In den weiteren Verfahren benötigen Sie den vollqualifizierten Server- oder Hostnamen, den Datenbanknamen und die Anmeldeinformationen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Navigieren Sie zur Seite **SQL-Datenbanken** oder **Verwaltete SQL-Instanzen**.

3. Sehen Sie sich auf der Seite **Übersicht** bei einer Einzeldatenbank den vollqualifizierten Servernamen neben **Servername** und bei einer verwalteten Instanz den vollqualifizierten Servernamen neben **Host** an. Um den Namen des Servers oder Hosts zu kopieren, zeigen Sie darauf, und wählen Sie das Symbol **Kopieren** aus. 

## <a name="create-code-to-query-the-sql-database"></a>Erstellen von Code zum Abfragen der SQL-Datenbank

1. Klicken Sie in Visual Studio auf **Datei** > **Neu** > **Projekt**. 
   
1. Wählen Sie im Dialogfeld **Neues Projekt** die Option **Visual C#** , und wählen Sie anschließend **Konsolen-App (.NET Framework)** aus.
   
1. Geben Sie als Projektname *sqltest* ein, und wählen Sie **OK** aus. Das neue Projekt wird erstellt. 
   
1. Wählen Sie **Projekt** >  **NuGet-Pakete verwalten** aus. 
   
1. Wählen Sie in **NuGet-Paket-Manager** die Registerkarte **Durchsuchen**, und suchen und wählen Sie dann **System.Data.SqlClient**.
   
1. Wählen Sie auf der Seite **System.Data.SqlClient** die Option **Installieren**. 
   - Wählen Sie bei Aufforderung **OK** aus, um die Installation fortzusetzen. 
   - Sollte das Fenster **Zustimmung zur Lizenz** angezeigt werden, wählen Sie **Ich stimme zu** aus.
   
1. Wenn die Installation abgeschlossen ist, können Sie den **NuGet-Paket-Manager** schließen. 
   
1. Ersetzen Sie den Inhalt von **Program.cs** im Code-Editor durch den folgenden Code. Ersetzen Sie Ihre Werte für `<server>`, `<username>`, `<password>` und `<database>`.
   
   >[!IMPORTANT]
   >Der Code in diesem Beispiel verwendet die AdventureWorksLT-Beispieldaten, die Sie beim Erstellen Ihrer Datenbank als Datenquelle auswählen können. Wenn Ihre Datenbank unterschiedliche Daten aufweist, verwenden Sie Tabellen aus Ihrer eigenen Datenbank in der SELECT-Abfrage. 
   
   ```csharp
   using System;
   using System.Data.SqlClient;
   using System.Text;
   
   namespace sqltest
   {
       class Program
       {
           static void Main(string[] args)
           {
               try 
               { 
                   SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                   builder.DataSource = "<server>.database.windows.net"; 
                   builder.UserID = "<username>";            
                   builder.Password = "<password>";     
                   builder.InitialCatalog = "<database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       connection.Open();       
                       StringBuilder sb = new StringBuilder();
                       sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                       sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                       sb.Append("JOIN [SalesLT].[Product] p ");
                       sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                       String sql = sb.ToString();
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           using (SqlDataReader reader = command.ExecuteReader())
                           {
                               while (reader.Read())
                               {
                                   Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                               }
                           }
                       }                    
                   }
               }
               catch (SqlException e)
               {
                   Console.WriteLine(e.ToString());
               }
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>Ausführen des Codes

1. Um die App auszuführen, wählen Sie **Debuggen** > **Debuggen starten**, oder wählen Sie **Start** in der Symbolleiste, oder drücken Sie **F5**.
1. Stellen Sie sicher, dass die obersten 20 Kategorie-/Produktzeilen aus Ihrer Datenbank zurückgegeben werden, und schließen Sie dann das Anwendungsfenster.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich darüber, wie Sie unter Linux/Windows/macOS [unter Verwendung von .NET Core eine Verbindung mit einer Azure SQL-Datenbank herstellen und die Datenbank abfragen](sql-database-connect-query-dotnet-core.md).  
- Informieren Sie sich über [die ersten Schritte mit .NET Core unter Windows/Linux/macOS bei Verwendung der Befehlszeile](/dotnet/core/tutorials/using-with-xplat-cli).
- Erfahren Sie, wie Sie [Ihre erste Azure SQL-Datenbank mithilfe von SSMS entwerfen](sql-database-design-first-database.md) oder wie Sie [Ihre erste Azure SQL-Datenbank mithilfe von .NET entwerfen](sql-database-design-first-database-csharp.md).
- Weitere Informationen zu .NET finden Sie in der [.NET-Dokumentation](https://docs.microsoft.com/dotnet/).
- Beispiel für eine Wiederholungslogik: [Herstellen robuster Verbindungen mit SQL mit ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

