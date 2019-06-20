---
title: Azure SQL-Datenbank – Schnellstartanleitung für Verbindungsherstellung und Abfragen | Microsoft-Dokumentation
description: In dieser Schnellstartanleitung zu Azure SQL-Datenbank wird beschrieben, wie Sie für Azure SQL-Datenbank eine Verbindung herstellen und Abfragen durchführen.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: f1eac83f2b5460bf56ddff1bf15d9180adb49a48
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66382280"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>Schnellstartanleitungen: Azure SQL-Datenbank – Verbindungsherstellung und Abfragen

Das folgende Dokument enthält Links zu Azure-Beispielen, in denen veranschaulicht wird, wie Sie eine Verbindung mit einer Azure SQL-Datenbank herstellen und Daten in der Datenbank abfragen. Darüber hinaus enthält es einige Empfehlungen zur Sicherheit auf Transportebene.

## <a name="quickstarts"></a>Schnellstarts

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|In diesem Schnellstart wird veranschaulicht, wie Sie SSMS zum Herstellen einer Verbindung mit einer Azure SQL-Datenbank verwenden und anschließend mithilfe von Transact-SQL-Anweisungen Daten in der Datenbank abfragen, aktualisieren oder löschen bzw. in diese einfügen.|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|In dieser Schnellstartanleitung erfahren Sie, wie Sie Azure Data Studio zum Herstellen einer Verbindung mit einer Azure SQL-Datenbank-Instanz verwenden und dann mit T-SQL-Anweisungen (Transact-SQL) die in Tutorials für Azure Data Studio verwendete TutorialDB-Komponente erstellen.|
|[Azure-Portal](sql-database-connect-query-portal.md)|In diesem Schnellstart wird veranschaulicht, wie Sie den Abfrage-Editor zum Herstellen einer Verbindung mit einer SQL-Datenbank verwenden und anschließend Transact-SQL-Anweisungen nutzen, um Daten in der Datenbank abzufragen, einzufügen, zu aktualisieren und zu löschen.|
|[Visual Studio Code](sql-database-connect-query-vscode.md)|In diesem Schnellstart wird veranschaulicht, wie Sie Visual Studio Code zum Herstellen einer Verbindung mit einer Azure SQL-Datenbank verwenden und anschließend Transact-SQL-Anweisungen nutzen, um Daten in der Datenbank abzufragen, zu aktualisieren und zu löschen bzw. in diese einzufügen.|
|[.NET mit Visual Studio](sql-database-connect-query-dotnet-visual-studio.md)|In dieser Schnellstartanleitung erfahren Sie, wie Sie unter Verwendung von .NET Framework ein C#-Programm mit Visual Studio erstellen, das eine Verbindung mit einer Azure SQL-Datenbank herstellt, und mithilfe von Transact-SQL-Anweisungen Daten abfragen.|
|[.NET Core](sql-database-connect-query-dotnet-core.md)|In dieser Schnellstartanleitung erfahren Sie, wie Sie unter Verwendung von .NET Core unter Windows/Linux/macOS ein C#-Programm erstellen, das eine Verbindung mit einer Azure SQL-Datenbank herstellt, und mithilfe von Transact-SQL-Anweisungen Daten abfragen.|
|[Go](sql-database-connect-query-go.md)|In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von Go eine Verbindung mit einer Azure SQL-Datenbank herstellen. Außerdem werden Transact-SQL-Anweisungen zum Abfragen und Ändern von Daten veranschaulicht.|
|[Java](sql-database-connect-query-java.md)|In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von Java eine Verbindung mit einer Azure SQL-Datenbank herstellen und anschließend Daten mithilfe von Transact-SQL-Anweisungen abfragen.|
|[Node.js](sql-database-connect-query-nodejs.md)|In dieser Schnellstartanleitung erfahren Sie, wie Sie unter Verwendung von Node.js ein Programm erstellen, das eine Verbindung mit einer Azure SQL-Datenbank herstellt, und mithilfe von Transact-SQL-Anweisungen Daten abfragen.|
|[PHP](sql-database-connect-query-php.md)|In dieser Schnellstartanleitung erfahren Sie, wie Sie unter Verwendung von PHP ein Programm erstellen, das eine Verbindung mit einer Azure SQL-Datenbank herstellt, und mithilfe von Transact-SQL-Anweisungen Daten abfragen.|
|[Python](sql-database-connect-query-python.md)|In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von Python eine Verbindung mit einer Azure SQL-Datenbank herstellen und Daten mithilfe von Transact-SQL-Anweisungen abfragen. |
|[Ruby](sql-database-connect-query-ruby.md)|In dieser Schnellstartanleitung erfahren Sie, wie Sie unter Verwendung von Ruby ein Programm erstellen, das eine Verbindung mit einer Azure SQL-Datenbank herstellt, und mithilfe von Transact-SQL-Anweisungen Daten abfragen.|
|[R](sql-database-connect-query-r.md)|In dieser Schnellstartanleitung erfahren Sie, wie Sie unter Verwendung von R mit dem Machine Learning Services-Feature von Azure SQL-Datenbank eine Verbindung mit einer Azure SQL-Datenbank-Instanz herstellen und mithilfe von Transact-SQL-Anweisungen Daten abfragen.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>Überlegungen zu TLS für Verbindungen mit einer SQL-Datenbank
Transport Layer Security (TLS) wird von allen Treibern verwendet, die Microsoft zum Herstellen einer Verbindung mit Azure SQL-Datenbank bereitstellt oder unterstützt. Es ist keine spezielle Konfiguration erforderlich. Für alle Verbindungen mit SQL Server oder Azure SQL-Datenbank empfehlen wir, dass alle Anwendungen auf die folgenden Konfigurationen oder deren Entsprechungen festgelegt sind:

 - **Verschlüsseln = Ein**
 - **TrustServerCertificate = Aus**

Einige Systeme verwenden andere, jedoch entsprechende Schlüsselwörter für diese Konfigurationsschlüsselwörter. Durch diese Konfigurationen wird sichergestellt, dass der Clienttreiber die Identität des vom Server empfangenen TLS-Zertifikats überprüft.

Außerdem empfiehlt es sich, TLS 1.1 und 1.0 auf dem Client zu deaktivieren, wenn Sie den Payment Card Industry Data Security Standard (PCI-DSS) einhalten müssen.

Nicht von Microsoft stammende Treiber verwenden TLS möglicherweise nicht standardmäßig. Dies kann beim Herstellen einer Verbindung mit Azure SQL-Datenbank ein Faktor sein. Bei Anwendungen mit eingebetteten Treibern können Sie diese Verbindungseinstellungen möglicherweise nicht steuern. Wir empfehlen Ihnen, die Sicherheit solcher Treiber und Anwendungen zu überprüfen, bevor Sie sie auf Systemen verwenden, die mit vertraulichen Daten interagieren.

## <a name="libraries"></a>Bibliotheken

Sie können verschiedene Bibliotheken und Frameworks zum Herstellen einer Verbindung mit Azure SQL-Datenbank verwenden. Unsere [Tutorials „Erste Schritte“](https://aka.ms/sqldev) ermöglichen Ihnen den schnellen Einstieg in Programmiersprachen wie C#, Java, Node.js, PHP und Python. Erstellen Sie anschließend mit SQL Server unter Linux oder Windows oder mit Docker unter macOS eine App.

Die folgende Tabelle enthält Verbindungsbibliotheken oder *Treiber*, die Clientanwendungen unter Verwendung zahlreicher Programmiersprachen nutzen können, um eine Verbindung mit SQL Server in einer lokalen Umgebung oder in der Cloud herzustellen und SQL Server zu verwenden. Sie können unter Linux, Windows oder mit Docker verwendet werden, um Verbindungen mit Azure SQL-Datenbank und Azure SQL Data Warehouse herzustellen. 

| Sprache | Plattform | Zusätzliche Ressourcen | Download | Erste Schritte |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET für SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Download](https://www.microsoft.com/net/download/) | [Erste Schritte](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC-Treiber für SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) | [Download](https://go.microsoft.com/fwlink/?linkid=852460) |  [Erste Schritte](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [PHP SQL-Treiber für SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Download](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Erste Schritte](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Node.js-Treiber für SQL Server](https://msdn.microsoft.com/library/mt652093.aspx) | [Installieren](https://msdn.microsoft.com/library/mt652094.aspx) |  [Erste Schritte](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Python-SQL-Treiber](https://msdn.microsoft.com/library/mt652092.aspx) | Installationsoptionen: <br/> \*[pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \*[pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [Erste Schritte](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Ruby-Treiber für SQL Server](https://msdn.microsoft.com/library/mt691981.aspx) | [Installieren](https://msdn.microsoft.com/library/mt711041.aspx) | [Erste Schritte](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Microsoft ODBC-Treiber für SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Download](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

Die folgende Tabelle enthält Beispiele für ORM (Object-Relational Mapping)-Frameworks und Webframeworks, die Clientanwendungen mit SQL Server in einer lokalen Umgebung oder in der Cloud nutzen können. Die Frameworks können unter Linux, Windows oder mit Docker verwendet werden, um Verbindungen mit SQL-Datenbank und SQL Data Warehouse herzustellen. 

| Sprache | Plattform | ORM(s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernate ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquent)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Verbindungsarchitektur finden Sie unter [Verbindungsarchitektur der Azure SQL-Datenbank](sql-database-connectivity-architecture.md).
- Finden von [SQL Server-Treibern](https://msdn.microsoft.com/library/mt654049.aspx) zum Herstellen einer Verbindung von Clientanwendungen
- Verbinden mit SQL-Datenbank:
  - [Herstellen von Verbindungen mit SQL-Datenbanken mithilfe von .NET (C#)](sql-database-connect-query-dotnet.md) 
  - [Herstellen von Verbindungen mit SQL-Datenbanken mithilfe von PHP](sql-database-connect-query-php.md) 
  - [Herstellen von Verbindungen mit SQL-Datenbanken mithilfe von Node.js](sql-database-connect-query-nodejs.md) 
  - [Herstellen von Verbindungen mit SQL-Datenbank mithilfe von Java](sql-database-connect-query-java.md) 
  - [Herstellen von Verbindungen mit SQL-Datenbanken mithilfe von Python](sql-database-connect-query-python.md)
  - [Herstellen von Verbindungen mit SQL-Datenbanken mithilfe von Ruby](sql-database-connect-query-ruby.md)
- Codebeispiele für Wiederholungslogik:
  - [Herstellen robuster Verbindungen mit SQL mit ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Herstellen robuster Verbindungen mit SQL mit PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
