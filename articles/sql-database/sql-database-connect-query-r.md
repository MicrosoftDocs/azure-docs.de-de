---
title: Abfragen einer Azure SQL-Datenbank-Instanz mithilfe von R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In diesem Artikel erfahren Sie, wie Sie mit einem R-Skript eine Verbindung mit einer Azure SQL-Datenbank-Instanz herstellen und sie mithilfe von Transact-SQL-Anweisungen abfragen.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 05/29/2019
ms.openlocfilehash: 1d4b17cf1e0349bf877c676cb4e591fc20ad4113
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416365"
---
# <a name="quickstart-use-r-to-query-an-azure-sql-database-preview"></a>Schnellstart: Verwenden eines R-Skripts zum Abfragen einer Azure SQL-Datenbank-Instanz (Vorschauversion)

 In dieser Schnellstartanleitung erfahren Sie, wie Sie unter Verwendung von [R](https://www.r-project.org/) mit Machine Learning Services eine Verbindung mit einer Azure SQL-Datenbank-Instanz herstellen und mithilfe von Transact-SQL-Anweisungen Daten abfragen. Machine Learning Services ist eine Funktion von Azure SQL-Datenbank zum Ausführen von R-Skripts in der Datenbank. Weitere Informationen finden Sie unter [Machine Learning Services mit R in Azure SQL-Datenbank (Vorschauversion)](sql-database-machine-learning-services-overview.md).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung benötigen Sie Folgendes:

- Eine Azure SQL-Datenbank. In den folgenden Schnellstartanleitungen erfahren Sie jeweils, wie Sie eine Datenbank in Azure SQL-Datenbank erstellen und anschließend konfigurieren:

<!-- Managed instance is not supported during the preview
  || Single database | Managed instance |
  |:--- |:--- |:---|
  | Create| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configure | [Server-level IP firewall rule](sql-database-server-level-firewall-rule.md) | [Connectivity from a VM](sql-database-managed-instance-configure-vm.md) |
  ||| [Connectivity from on-site](sql-database-managed-instance-configure-p2s.md) |
  | Load data | Adventure Works loaded per quickstart | [Restore Wide World Importers](sql-database-managed-instance-get-started-restore.md) |
  ||| Restore or import Adventure Works from [BACPAC](sql-database-import.md) file from [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) |
  |||
-->

  || Einzeldatenbank |
  |:--- |:--- |
  | Erstellen| [Portal](sql-database-single-database-get-started.md) |
  || [BEFEHLSZEILENSCHNITTSTELLE (CLI)](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | Konfigurieren | [IP-Firewallregel auf Serverebene](sql-database-server-level-firewall-rule.md) |
  | Laden von Daten | Laden von Adventure Works gemäß Schnellstartanleitung |
  |||

  > [!NOTE]
  > Während der Vorschau der Azure SQL-Datenbank-Machine Learning Services mit R wird die Bereitstellungsoption für verwaltete Instanzen nicht unterstützt.

<!-- Managed instance is not supported during the preview
  > [!IMPORTANT]
  > The scripts in this article are written to use the Adventure Works database. With a managed instance, you must either import the Adventure Works database into an instance database or modify the scripts in this article to use the Wide World Importers database.
-->

- Machine Learning Services (mit R) aktiviert. Während der öffentlichen Vorschauphase führt Microsoft für Sie das Onboarding durch und aktiviert das maschinelle Lernen für Ihre vorhandene oder neue Datenbank. Führen Sie die Schritte in [Registrieren für die Vorschauversion](sql-database-machine-learning-services-overview.md#signup) aus.

- Das aktuelle [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). In dieser Schnellstartanleitung verwenden Sie SSMS, obwohl Sie auch mithilfe anderer Datenbankverwaltungen oder Abfragetools R-Skripts ausführen können.

## <a name="get-sql-server-connection-information"></a>Abrufen von SQL Server-Verbindungsinformationen

Rufen Sie die Verbindungsinformationen ab, die Sie zum Herstellen einer Verbindung mit der Azure SQL-Datenbank benötigen. In den weiteren Verfahren benötigen Sie den vollqualifizierten Server- oder Hostnamen, den Datenbanknamen und die Anmeldeinformationen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Navigieren Sie zur Seite **SQL-Datenbanken** oder **Verwaltete SQL-Instanzen**.

3. Sehen Sie sich auf der Seite **Übersicht** bei einer Einzeldatenbank den vollqualifizierten Servernamen neben **Servername** und bei einer verwalteten Instanz den vollqualifizierten Servernamen neben **Host** an. Um den Namen des Servers oder Hosts zu kopieren, zeigen Sie darauf, und wählen Sie das Symbol **Kopieren** aus.

## <a name="create-code-to-query-your-sql-database"></a>Erstellen von Code zum Abfragen Ihrer SQL-Datenbank

1. Öffnen Sie **SQL Server Management Studio**, und stellen Sie eine Verbindung mit Ihrer SQL-Datenbank her.

   Wenn Sie Unterstützung beim Herstellen der Verbindung benötigen, nutzen Sie die Informationen in [Schnellstart: Verwenden von SQL Server Management Studio zum Herstellen der Verbindung mit einer Instanz von Azure SQL-Datenbank und deren Abfrage](sql-database-connect-query-ssms.md).

1. Übergeben Sie das vollständige R-Skript an die gespeicherte Prozedur [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

   Das Skript wird über das `@script`-Argument übergeben. Das `@script`-Argument darf nur gültigen R-Code enthalten.
   
   >[!IMPORTANT]
   >Der Code in diesem Beispiel verwendet die AdventureWorksLT-Beispieldaten, die Sie beim Erstellen Ihrer Datenbank als Datenquelle auswählen können. Wenn Ihre Datenbank unterschiedliche Daten aufweist, verwenden Sie Tabellen aus Ihrer eigenen Datenbank in der SELECT-Abfrage. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Falls Sie Fehler erhalten, kann dies daran liegen, dass die öffentliche Vorschauversion von Machine Learning Services (mit R) für Ihre SQL-Datenbank nicht aktiviert ist. Weitere Informationen finden Sie oben unter [Voraussetzungen](#prerequisites).

## <a name="run-the-code"></a>Ausführen des Codes

1. Führen Sie die gespeicherte Prozedur [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) aus.

1. Stellen Sie sicher, dass die obersten 20 Kategorie-/Produktzeilen im Fenster **Meldungen** zurückgegeben werden.

## <a name="next-steps"></a>Nächste Schritte

- [Entwurf Ihrer ersten Azure SQL-Datenbank](sql-database-design-first-database.md)
- [Machine Learning Services (mit R) in Azure SQL-Datenbank](sql-database-machine-learning-services-overview.md)
- [Erstellen und Ausführen einfacher R-Skripts in Machine Learning Services von Azure SQL-Datenbank (Vorschauversion)](sql-database-quickstart-r-create-script.md)
- [Schreiben erweiterter R-Funktionen in Azure SQL-Datenbank mit Machine Learning Services (Vorschauversion)](sql-database-machine-learning-services-functions.md)
