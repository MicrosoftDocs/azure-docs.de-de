---
title: Verbinden und Abfragen – PostgreSQL für Einzelserver
description: Links zu Schnellstarts zeigen, wie Sie eine Verbindung mit Ihrer Single Server-Instanz von Azure Database for PostgreSQL herstellen und Abfragen ausführen.
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/21/2020
ms.openlocfilehash: 413f5fc1f6579102b62042c1470816470c2e1449
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546550"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--single-server"></a>Überblick über Verbinden und Abfragen für Azure Database for PostgreSQL – Einzelserver

Das folgende Dokument enthält Links zu Beispielen, die zeigen, wie Sie eine Verbindung mit Azure Database for PostgreSQL-Einzelserver herstellen und Daten abfragen. Dieser Leitfaden enthält auch TLS-Empfehlungen und Erweiterungen, die Sie zum Herstellen einer Verbindung mit dem Server in den nachstehend aufgeführten unterstützten Sprachen verwenden können.

## <a name="quickstarts"></a>Schnellstarts

| Schnellstart | BESCHREIBUNG |
|---|---|
|[Pgadmin](https://www.pgadmin.org/)|Mithilfe von „pgadmin“ können Sie eine Verbindung mit dem Server herstellen. Außerdem vereinfacht sie die Erstellung, Wartung und Verwendung von Datenbankobjekten.|
|[psql in Azure Cloud Shell](quickstart-create-server-database-azure-cli.md#connect-to-the-azure-database-for-postgresql-server-by-using-psql)|In diesem Artikel wird gezeigt, wie Sie [**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) in [Azure Cloud Shell](../cloud-shell/overview.md) ausführen, um eine Verbindung mit Ihrem Server herzustellen, und dann Anweisungen zum Abfragen, Einfügen, Aktualisieren und Löschen von Daten in der Datenbank ausführen. Sie können **psql** ausführen, wenn sie in Ihrer Entwicklungsumgebung installiert wurde.|
|[PostgreSQL mit VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)|Mit der Azure-Datenbankerweiterung für VS Code (Vorschau) können Sie Ihren PostgreSQL-Server sowohl lokal als auch in der Cloud mithilfe von Notizbüchern mit umfangreichen Intellisense-Funktionen durchsuchen und abfragen. |
|[PHP](connect-php.md)|In diesem Schnellstart erfahren Sie, wie Sie mithilfe von PHP ein Programm erstellen, das eine Verbindung mit einer Datenbank herstellt, und anschließend mithilfe von Work Daten bei Datenbankobjekten abfragen.|
|[Java](connect-java.md)|In diesem Schnellstart erfahren Sie, wie Sie mithilfe von Java eine Verbindung mit einer Datenbank herstellen und anschließend mithilfe von Work Daten bei Datenbankobjekten abfragen.|
|[Node.js](connect-nodejs.md)|In diesem Schnellstart erfahren Sie, wie Sie mithilfe von Node.js ein Programm erstellen, das eine Verbindung mit einer Datenbank herstellt, und anschließend mithilfe von Work Daten bei Datenbankobjekten abfragen.|
|[.NET(C#)](connect-csharp.md)|In diesem Schnellstart erfahren Sie, wie Sie mithilfe von .NET(C#) ein C#-Programm erstellen, das eine Verbindung mit einer Datenbank herstellt, und anschließend mithilfe von Work Daten bei Datenbankobjekten abfragen.|
|[Go](connect-go.md)|In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von Go eine Verbindung mit einer Datenbank herstellen. Außerdem werden Transact-SQL-Anweisungen zum Abfragen und Ändern von Daten veranschaulicht.|
|[Python](connect-python.md)|In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von Python eine Verbindung mit einer Datenbank herstellen und anschließend mithilfe von Work Daten bei Datenbankobjekten abfragen. |
|[Ruby](connect-ruby.md)|In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von Ruby ein Programm erstellen, das eine Verbindung mit einer Datenbank herstellt, und anschließend mithilfe von Work Daten bei Datenbankobjekten abfragen.|

## <a name="tls-considerations-for-database-connectivity"></a>Überlegungen zu TLS für Datenbankverbindungen

Transport Layer Security (TLS) wird von allen Treibern verwendet, die Microsoft zum Herstellen einer Verbindung mit Datenbanken in Azure Database for PostgreSQL bereitstellt oder unterstützt. Eine besondere Konfiguration ist nicht erforderlich, aber erzwingen Sie unbedingt TLS 1.2 für neu erstellte Server. Wenn Sie TLS 1.0 und 1.1 verwenden, empfehlen wir, die TLS-Version für Ihre Server zu aktualisieren. Weitere Informationen finden Sie unter [Konfigurieren von TLS](howto-tls-configurations.md).

## <a name="postgresql-extensions"></a>PostgreSQL-Erweiterungen

PostgreSQL bietet die Möglichkeit, die Funktionalität Ihrer Datenbank mithilfe von Erweiterungen zu erweitern. Bei Erweiterungen werden mehrere zusammengehörige SQL-Objekte zu einem Paket gebündelt und mit nur einem Befehl in die Datenbank geladen oder daraus entfernt. Nach dem Laden in die Datenbank funktionieren Erweiterungen genauso wie integrierte Features.

- [Postgres 11-Erweiterungen](./concepts-extensions.md#postgres-11-extensions)
- [Postgres 10-Erweiterungen](./concepts-extensions.md#postgres-10-extensions)
- [Postgres 9.6-Erweiterungen](./concepts-extensions.md#postgres-96-extensions)
- [Postgres 9.5-Erweiterungen](./concepts-extensions.md#postgres-95-extensions)

Weitere Informationen finden Sie unter [Verwenden von PostgreSQL-Erweiterungen auf einem Einzelserver](concepts-extensions.md).

## <a name="next-steps"></a>Nächste Schritte

- [Migrieren von Daten durch Sicherungen und Wiederherstellungen](howto-migrate-using-dump-and-restore.md)
- [Migrieren von Daten durch Import und Export](howto-migrate-using-export-and-import.md)
