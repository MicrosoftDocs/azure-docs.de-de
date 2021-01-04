---
title: Anwendungsentwicklung – Übersicht
description: Hier erfahren Sie mehr über verfügbare Verbindungsbibliotheken und bewährte Methoden für Anwendungen, die eine Verbindung mit SQL-Datenbank herstellen.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 11/14/2019
ms.custom: sqldbrb=2
ms.openlocfilehash: 7ea3456dbd1d0942cba48c97d70982ae088d73f9
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782976"
---
# <a name="application-development-overview---sql-database--sql-managed-instance"></a>Übersicht über die Anwendungsentwicklung: SQL-Datenbank und SQL Managed Instance

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

In diesem Artikel werden die grundlegenden Aspekte beschrieben, die ein Entwickler beim Schreiben von Code zum Herstellen einer Verbindung mit Ihrer Datenbank in Azure berücksichtigen sollte. Dieser Artikel gilt für Azure SQL-Datenbank und Azure SQL Managed Instance.

## <a name="language-and-platform"></a>Sprache und Plattform

Sie können verschiedene [Programmiersprachen und Plattformen](connect-query-content-reference-guide.md) zum Herstellen einer Verbindung mit und Abfragen von Azure SQL-Datenbank verwenden. Sie können mithilfe von [Beispielanwendungen](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) eine Verbindung mit der Datenbank herstellen.

Sie können Open-Source-Tools wie [Cheetah](https://github.com/wunderlist/cheetah), [Sql-Cli](https://www.npmjs.com/package/sql-cli) und [VS Code](https://code.visualstudio.com/) nutzen. Darüber hinaus funktioniert Azure SQL-Datenbank mit Microsoft-Tools wie [Visual Studio](https://www.visualstudio.com/downloads/) und [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms). Sie können auch das Azure-Portal, PowerShell und REST-APIs nutzen, um noch produktiver zu arbeiten.

## <a name="authentication"></a>Authentifizierung

Der Zugriff auf Azure SQL-Datenbank wird durch Anmeldungen und Firewalls geschützt. Azure SQL-Datenbank unterstützt Benutzer und Anmeldungen sowohl mit SQL Server- als auch mit [Azure Active Directory-Authentifizierung](authentication-aad-overview.md). Azure Active Directory-Anmeldungen sind nur in SQL Managed Instance verfügbar. 

Erfahren Sie mehr zum [Verwalten des Datenbankzugriffs und der Anmeldung](logins-create-manage.md).

## <a name="connections"></a>Verbindungen

Ändern Sie in der Clientverbindungslogik das Standardtimeout in 30 Sekunden. Der Standardwert von 15 Sekunden ist zu kurz für Verbindungen, die über das Internet hergestellt werden.

Wenn Sie einen [Verbindungspool](/dotnet/framework/data/adonet/sql-server-connection-pooling)verwenden, trennen Sie unbedingt die Verbindung, sobald Ihre Anwendung sie nicht aktiv verwendet und nicht gerade auf die erneute Verwendung der Verbindung vorbereitet wird.

Vermeiden Sie lang andauernde Transaktionen, weil jeder Infrastruktur- oder Verbindungsfehler zu einem Rollback der Transaktion führen kann. Teilen Sie die Transaktion (wenn möglich) in mehrere kleinere Transaktionen auf, und verwenden Sie [Batchverarbeitung zum Verbessern der Leistung](../performance-improve-use-batching.md).

## <a name="resiliency"></a>Resilienz

Azure SQL-Datenbank ist ein Clouddienst, in dem ggf. vorübergehende Fehler in der zugrunde liegenden Infrastruktur oder bei der Kommunikation zwischen Cloudentitäten auftreten können. Obwohl Azure SQL-Datenbank bei transitiven infrastrukturausfällen resilient ist, können solche Fehler die Konnektivität beeinträchtigen. Tritt beim Herstellen der Verbindung mit SQL-Datenbank ein vorübergehender Fehler auf, sollte Ihr Code [den Aufruf wiederholen](troubleshoot-common-connectivity-issues.md). Die Wiederholungslogik sollte Backofflogik verwenden, damit die der Dienst nicht unnötig überlastet wird, wenn mehrere Clients den Wiederholungsvorgang gleichzeitig durchführen. Die Wiederholungslogik hängt von den [Fehlermeldungen für Clientprogramme von SQL-Datenbank](troubleshoot-common-errors-issues.md) ab.

Weitere Informationen zur Vorbereitung auf geplante Wartungsereignisse in Azure SQL-Datenbank finden Sie unter [Planen von Azure-Wartungsereignissen in Azure SQL-Datenbank](planned-maintenance.md).

## <a name="network-considerations"></a>Netzwerküberlegungen

- Vergewissern Sie sich, dass auf dem Computer, der das Clientprogramm hostet, die Firewall ausgehende TCP-Kommunikation über Port 1433 zulässt.  Weitere Informationen: [Konfigurieren einer Firewall für Azure SQL-Datenbank](firewall-configure.md).
- Wenn Ihr Clientprogramm eine Verbindung mit SQL-Datenbank herstellt, wobei der Client auf einem virtuellen Azure-Computer ausgeführt wird, müssen Sie bestimmte Portbereiche auf dem virtuellen Computer öffnen. Weitere Informationen: [Andere Ports als 1433 für ADO.NET 4.5 und SQL-Datenbank](adonet-v12-develop-direct-route-ports.md).
- Bei Clientverbindungen mit Azure SQL-Datenbank wird der Proxy manchmal umgangen und direkt mit der Datenbank interagiert. Andere Ports als 1433 werden wichtig. Weitere Informationen finden Sie unter [Verbindungsarchitektur der Azure SQL-Datenbank](connectivity-architecture.md) und [Andere Ports als 1433 für ADO.NET 4.5 und SQL-Datenbank](adonet-v12-develop-direct-route-ports.md).
- Informationen zur Netzwerkkonfiguration für eine Instanz von SQL Managed Instance finden Sie unter [Netzwerkkonfiguration für Instanzen von SQL Managed Instance](../managed-instance/how-to-content-reference-guide.md#network-configuration).

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über alle Funktionen von [SQL-Datenbank](sql-database-paas-overview.md) und [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md).

Informationen zu den ersten Schritten finden Sie in den Leitfäden zu [Azure SQL-Datenbank](quickstart-content-reference-guide.md) und [Azure SQL Managed Instance](../managed-instance/quickstart-content-reference-guide.md).