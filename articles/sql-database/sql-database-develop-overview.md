---
title: SQL-Datenbankanwendungsentwicklung – Übersicht | Microsoft-Dokumentation
description: Hier erfahren Sie mehr über verfügbare Verbindungsbibliotheken und bewährte Methoden für Anwendungen, die eine Verbindung mit SQL-Datenbank herstellen.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: efb6d932e616ada6b8dfff637af469c16fc2f293
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60723407"
---
# <a name="sql-database-application-development-overview"></a>Übersicht zur SQL-Datenbankanwendungsentwicklung

In diesem Artikel werden die grundlegenden Aspekte beschrieben, die ein Entwickler beim Schreiben von Code zum Herstellen einer Verbindung mit Azure SQL-Datenbank berücksichtigen sollte. Dieser Artikel gilt für alle Bereitstellungsmodelle von Azure SQL-Datenbank (Einzeldatenbank, Pools für elastische Datenbanken, verwaltete Instanz).

> [!TIP]
> Wenn Sie Ihre Azure SQL-Datenbank-Instanz einrichten müssen, lesen Sie die ersten Schritte zu [Singletons](sql-database-single-database-quickstart-guide.md) und [verwalteten Instanzen](sql-database-managed-instance-quickstart-guide.md).
>

## <a name="language-and-platform"></a>Sprache und Plattform

Sie können verschiedene [Programmiersprachen und Plattformen](sql-database-connect-query.md) zum Herstellen einer Verbindung mit und Abfragen von Azure SQL-Datenbank verwenden. Sie können mithilfe von [Beispielanwendungen](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) eine Verbindung mit Azure SQL-Datenbank herstellen.

Sie können Open-Source-Tools wie [Cheetah](https://github.com/wunderlist/cheetah), [Sql-Cli](https://www.npmjs.com/package/sql-cli) und [VS Code](https://code.visualstudio.com/) nutzen. Darüber hinaus funktioniert Azure SQL-Datenbank mit Microsoft-Tools wie [Visual Studio](https://www.visualstudio.com/downloads/) und [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). Sie können auch das Azure-Portal, PowerShell und REST-APIs nutzen, um noch produktiver zu arbeiten.

## <a name="authentication"></a>Authentication

Der Zugriff auf Azure SQL-Datenbank wird durch Anmeldungen und Firewalls geschützt. Azure SQL-Datenbank unterstützt Benutzer und Anmeldungen mit SQL Server-Authentifizierung sowie [Azure Active Directory-Authentifizierung (AAD)](sql-database-aad-authentication.md). AAD-Anmeldungen sind nur in der verwalteten Instanz verfügbar. 

Erfahren Sie mehr zum [Verwalten des Datenbankzugriffs und der Anmeldung](sql-database-manage-logins.md).

## <a name="connections"></a>Verbindungen

Ändern Sie in der Clientverbindungslogik das Standardtimeout in 30 Sekunden. Der Standardwert von 15 Sekunden ist zu kurz für Verbindungen, die über das Internet hergestellt werden.

Wenn Sie einen [Verbindungspool](https://msdn.microsoft.com/library/8xx3tyca.aspx)verwenden, trennen Sie unbedingt die Verbindung, sobald Ihre Anwendung sie nicht aktiv verwendet und nicht gerade auf die erneute Verwendung der Verbindung vorbereitet wird.

Vermeiden Sie lang andauernde Transaktionen, weil jeder Infrastruktur- oder Verbindungsfehler zu einem Rollback der Transaktion führen kann. Teilen Sie die Transaktion (wenn möglich) in mehrere kleinere Transaktionen auf, und verwenden Sie [Batchverarbeitung zum Verbessern der Leistung](sql-database-use-batching-to-improve-performance.md).

## <a name="resiliency"></a>Resilienz

Azure SQL-Datenbank ist ein Clouddienst, in dem ggf. vorübergehende Fehler in der zugrunde liegenden Infrastruktur oder bei der Kommunikation zwischen Cloudentitäten auftreten können. Obwohl Azure SQL-Datenbank bei transitiven infrastrukturausfällen resilient ist, können solche Fehler die Konnektivität beeinträchtigen. Tritt beim Herstellen der Verbindung mit SQL-Datenbank ein vorübergehender Fehler auf, sollte Ihr Code [den Aufruf wiederholen](sql-database-connectivity-issues.md). Die Wiederholungslogik sollte Backofflogik verwenden, damit die SQL-Datenbank nicht unnötig überlastet wird, wenn mehrere Clients die Wiederholung gleichzeitig durchführen. Die Wiederholungslogik hängt von den [Fehlermeldungen für Clientprogramme von SQL-Datenbank](sql-database-develop-error-messages.md) ab.

Weitere Informationen zur Vorbereitung auf geplante Wartungsereignisse in Azure SQL-Datenbank finden Sie unter [Planen von Azure-Wartungsereignissen in Azure SQL-Datenbank](sql-database-planned-maintenance.md).

## <a name="network-considerations"></a>Netzwerküberlegungen

- Vergewissern Sie sich, dass auf dem Computer, der das Clientprogramm hostet, die Firewall ausgehende TCP-Kommunikation über Port 1433 zulässt.  Weitere Informationen: [Konfigurieren einer Firewall für Azure SQL-Datenbank](sql-database-configure-firewall-settings.md).
- Wenn Ihr Clientprogramm eine Verbindung mit SQL-Datenbank herstellt, wobei der Client auf einem virtuellen Azure-Computer ausgeführt wird, müssen Sie bestimmte Portbereiche auf dem virtuellen Computer öffnen. Weitere Informationen: [Andere Ports als 1433 für ADO.NET 4.5 und SQL-Datenbank](sql-database-develop-direct-route-ports-adonet-v12.md).
- Bei Clientverbindungen mit Azure SQL-Datenbank wird der Proxy manchmal umgangen und direkt mit der Datenbank interagiert. Andere Ports als 1433 werden wichtig. Weitere Informationen finden Sie unter [Verbindungsarchitektur der Azure SQL-Datenbank](sql-database-connectivity-architecture.md) und [Andere Ports als 1433 für ADO.NET 4.5 und SQL-Datenbank](sql-database-develop-direct-route-ports-adonet-v12.md).
- Informationen zur Netzwerkkonfiguration für eine verwaltete Instanz finden Sie unter [Netzwerkkonfiguration für verwaltete Instanzen](sql-database-howto-managed-instance.md#network-configuration).

## <a name="next-steps"></a>Nächste Schritte

Entdecken Sie alle [Funktionen von SQL-Datenbank](sql-database-technical-overview.md).
