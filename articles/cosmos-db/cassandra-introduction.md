---
title: Einführung in die Cassandra-API für Azure Cosmos DB
description: Erfahren Sie, wie Sie Azure Cosmos DB für das „Lift & Shift“ vorhandener Anwendungen sowie das Erstellen neuer Anwendungen mithilfe der Cassandra-API unter Verwendung der Cassandra-Treiber und der bereits vertrauten CQL verwenden können.
author: kanshiG
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 05/21/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 82ca7814f756a12005ee5802c3e8a7fd28f6d398
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968969"
---
# <a name="introduction-to-the-azure-cosmos-db-cassandra-api"></a>Einführung in die Cassandra-API für Azure Cosmos DB

Mithilfe der Apache Cassandra-API kann Azure Cosmos DB als Datenspeicher für Apps verwendet werden, die für [Apache Cassandra](https://cassandra.apache.org/) geschrieben wurden. Dies bedeutet, dass durch Verwendung vorhandener [Apache-Treiber](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver), die mit CQLv4 kompatibel sind, Ihre vorhandene Cassandra-Anwendung jetzt mit der Cassandra-API für Azure Cosmos DB kommunizieren kann. In vielen Fällen können Sie durch einfaches Ändern einer Verbindungszeichenfolge von Apache Cassandra zur Cassandra-API für Azure Cosmos DB wechseln. 

Die Cassandra-API ermöglicht Ihnen die Interaktion mit Daten, die in Azure Cosmos DB gespeichert sind, mithilfe der Cassandra-Abfragesprache (Cassandra Query Language, CQL), Cassandra-basierten Tools (z. B. CQLSH) und bereits vertrauten Cassandra-Clienttreibern.

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>Welche Vorteile bietet die Verwendung der Apache Cassandra-API für Azure Cosmos DB?

**Keine Vorgangsverwaltung**: Als vollständig verwalteter Clouddienst entfernt die Cassandra-API für Azure Cosmos DB den Mehraufwand der Verwaltung und Überwachung von unzähligen Einstellungen für Betriebssystem, JVM und YAML-Dateien und deren Interaktionen. Azure Cosmos DB bietet Überwachung von Durchsatz, Latenz, Speicher, Verfügbarkeit und konfigurierbaren Warnungen.

**Leistungsverwaltung**: Azure Cosmos DB bietet garantierte Lese- und Schreibvorgänge mit geringer Latenz für das 99. Perzentil, die durch die SLAs gesichert sind. Benutzer müssen sich keine Gedanken zum Verwaltungsaufwand machen, um Lese- und Schreibvorgänge mit hoher Leistung und geringer Latenz sicherzustellen. Dies bedeutet: Benutzer müssen sich nicht mit der Planung von Komprimierung, Verwaltung von Tombstones sowie manuellem Einrichten von Bloomfiltern und Replikaten befassen. Azure Cosmos DB entfernt den Mehraufwand zur Verwaltung dieser Probleme und ermöglicht es Ihnen, sich auf die Anwendungslogik zu konzentrieren.

**Möglichkeit der Verwendung von vorhandenem Code und Tools**: Azure Cosmos DB bietet Kompatibilität auf Verbindungsprotokollebene mit vorhandenen SDKs und Tools. Durch diese Kompatibilität ist sichergestellt, dass Sie die vorhandene Codebasis mit der Cassandra-API für Azure Cosmos DB mit nur geringfügigen Änderungen verwenden können.

**Durchsatz- und Speicherflexibilität**: Azure Cosmos DB bietet regionsübergreifend garantierten Durchsatz und kann den bereitgestellten Durchsatz bei Azure-Portal-, PowerShell- oder CLI-Vorgängen skalieren. Sie können Speicher und Durchsatz für Ihre Tabellen flexibel nach Bedarf mit vorhersagbarer Leistung skalieren.

**Globale Verteilung und Verfügbarkeit**: Azure Cosmos DB bietet die Möglichkeit, Daten in allen Azure-Regionen global zu verteilen und die Daten lokal bereitzustellen, wobei Datenzugriff mit geringer Latenz und Hochverfügbarkeit sichergestellt wird. Azure Cosmos DB bietet 99,99 % Hochverfügbarkeit innerhalb einer Region und 99,999 % Lese- und Schreibverfügbarkeit in mehreren Regionen ohne höheren Betriebsaufwand. Weitere Informationen finden Sie im Artikel [Globale Verteilung von Daten](distribute-data-globally.md). 

**Auswahl der Konsistenzebene**: In Azure Cosmos DB kann zwischen fünf klar definierten Konsistenzebenen gewählt werden, um für ein ausgewogenes Verhältnis zwischen Konsistenz und Leistung zu sorgen. Diese Konsistenzebenen sind: „Strong“ (Sicher), „Bounded Staleness“ (Begrenzte Veraltung), „Session“ (Sitzung), „Consistent Prefix“ (Präfixkonsistenz) und „Eventual“ (Letztlich). Mit diesen gut abgegrenzten, praktischen und intuitiven Konsistenzebenen können Entwickler präzise Kompromisse zwischen Konsistenz, Verfügbarkeit und Latenz schließen. Erfahren Sie mehr im Artikel [Konsistenzebenen](consistency-levels.md). 

**Für große Unternehmen geeignet**: In Azure Cosmos DB stehen [Compliancezertifizierungen](https://www.microsoft.com/trustcenter) zur Verfügung, um die sichere Nutzung der Plattform durch Benutzer sicherzustellen. Azure Cosmos DB bietet außerdem Verschlüsselung ruhender Daten sowie von Daten während der Übertragung, eine IP-Firewall und Überwachungsprotokolle für Aktivitäten auf Steuerungsebene.

## <a name="next-steps"></a>Nächste Schritte

* Sie können mit dem Erstellen der folgenden sprachspezifischen Apps zum Erstellen und Verwalten von Cassandra-API-Daten schnell beginnen:
  - [Node.js-App](create-cassandra-nodejs.md)
  - [.NET-App](create-cassandra-dotnet.md)
  - [Python-App](create-cassandra-python.md)

* Erste Schritte mit dem [Erstellen eines Cassandra-API-Kontos, einer Datenbank und einer Tabelle](create-cassandra-api-account-java.md) mithilfe einer Java-Anwendung.

* [Laden von Beispieldaten in eine Cassandra-API-Tabelle von Azure Cosmos DB](cassandra-api-load-data.md) mithilfe einer Java-Anwendung

* [Abfragen von Daten aus dem Cassandra-API-Konto](cassandra-api-query-data.md) mithilfe einer Java-Anwendung.

* Informationen zu den Apache Cassandra-Features, die von der Cassandra-API für Azure Cosmos DB unterstützt werden, finden Sie im Artikel [Cassandra-Support](cassandra-support.md).

* Lesen Sie die [häufig gestellten Fragen (FAQ)](faq.md#cassandra).
