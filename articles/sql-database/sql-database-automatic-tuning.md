---
title: Azure SQL-Datenbank – Automatische Optimierung | Microsoft-Dokumentation
description: Azure SQL-Datenbank analysiert SQL-Abfragen und führt eine automatische Anpassung an die Benutzerworkload durch.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 03/06/2019
ms.openlocfilehash: 6e818da29b7ee0d17ebe4f8e523648146973fa63
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61415761"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Automatische Optimierung in Azure SQL-Datenbank

Die automatische Optimierung von Azure SQL-Datenbank sorgt dank kontinuierlicher, auf künstlicher Intelligenz und maschinellem Lernen basierender Leistungsoptimierung für hohe Leistung und stabile Workloads.

Bei der automatischen Optimierung handelt es sich um einen vollständig verwalteten Leistungsdienst mit integrierten intelligenten Funktionen, der kontinuierlich Abfragen für eine Datenbank überwacht und automatisch deren Leistung verbessert. Dies wird durch eine dynamische Anpassung der Datenbank an die unterschiedlichen Workloads sowie durch die Anwendung von Optimierungsempfehlungen erreicht. Dank künstlicher Intelligenz lernt die automatische Optimierung horizontal von allen Datenbanken in Azure und verbessert dynamisch ihre Optimierungsmaßnahmen. Je länger die automatische Optimierung für eine Azure SQL-Datenbank verwendet wird, desto besser ist ihre Leistung.

Die automatische Optimierung von Azure SQL-Datenbank ist vielleicht eines der wichtigsten Features, die Sie aktivieren können, um stabile Datenbankworkloads mit hoher Leistung zu erhalten.

## <a name="what-can-automatic-tuning-do-for-you"></a>Vorteile der automatischen Optimierung

- Automatisierte Optimierung der Leistung von Azure SQL-Datenbanken
- Automatisierte Überprüfung von Leistungszuwächsen
- Automatisches Rollback und automatische Korrektur
- Optimierungsverlauf
- Optimierung von T-SQL-Aktionsskripts für manuelle Bereitstellungen
- Proaktive Überwachung der Workloadleistung
- Funktion für das horizontale Hochskalieren von einigen Hunderttausend Datenbanken
- Positive Auswirkung auf DevOps-Ressourcen und die Gesamtkosten

## <a name="safe-reliable-and-proven"></a>Sicher, zuverlässig und bewährt

Die Leistung anspruchsvoller Workloads wird durch auf Azure SQL-Datenbanken angewendete Optimierungsmaßnahmen in keinem Fall beeinträchtigt. Bei der Entwicklung des Systems wurde sehr genau darauf geachtet, dass es zu keinerlei Störung der Benutzerworkloads kommt. Empfehlungen der automatisierten Optimierung werden nur bei geringer Auslastung angewendet. Zur Wahrung der Workloadleistung kann das System automatische Optimierungsmaßnahmen auch vorübergehend deaktivieren. In diesem Fall wird im Azure-Portal eine entsprechende Meldung angezeigt. Die automatische Optimierung betrachtet Workloads mit der höchsten Ressourcenpriorität.

Die Mechanismen für die automatische Optimierung sind ausgereift und wurden auf Grundlage von Millionen von Datenbanken perfektioniert, die in Azure ausgeführt werden. Angewendete automatisierte Optimierungsmaßnahmen werden automatisch überprüft, um sicherzustellen, dass sie sich positiv auf die Workloadleistung auswirken. Zurückgestellte Leistungsempfehlungen werden dynamisch erkannt und unverzüglich zurückgesetzt. Anhand des aufgezeichneten Optimierungsverlaufs wird klar ersichtlich, dass die Leistung jeder einzelnen Azure SQL-Datenbank verbessert wurde. 

![Wie funktioniert die automatische Optimierung?](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Die automatische Optimierung von Azure SQL-Datenbank teilt sich seine Kernlogik mit der SQL Server-Engine für die automatische Optimierung. Weitere technische Informationen zum integrierten Intelligenzmechanismus finden Sie unter [Automatic tuning](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) (Automatische Optimierung).

## <a name="use-automatic-tuning"></a>Verwenden der automatischen Optimierung

Die automatische Optimierung muss für Ihr Abonnement aktiviert werden. Informationen zum Aktivieren der automatischen Optimierung über das Azure-Portal finden Sie unter [Aktivieren der automatischen Optimierung](sql-database-automatic-tuning-enable.md).

Die automatische Optimierung kann autonom arbeiten, automatisch Optimierungsempfehlungen anwenden und Leistungszuwächse automatisch überprüfen. 

Zur präziseren Steuerung können Sie die automatische Anwendung von Optimierungsempfehlungen deaktivieren und Optimierungsempfehlungen manuell über das Azure-Portal anwenden. Sie können die Lösung auch zum reinen Anzeigen von Empfehlungen der automatischen Optimierung verwenden und sie über Skripts und Tools Ihrer Wahl manuell anwenden. 

Im folgenden Video können Sie sich einen Überblick über die Funktionsweise der automatischen Optimierung in typischen Verwendungsszenarien verschaffen:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>Optionen für die automatische Optimierung

In Azure SQL-Datenbank stehen folgende Optionen für die automatische Optimierung zur Verfügung:

| Option für die automatische Optimierung | Unterstützung für Einzel- und Pooldatenbanken | Unterstützung der Instanzdatenbank |
| :----------------------------- | ----- | ----- |
| **CREATE INDEX:** Identifiziert Indizes, die die Leistung Ihrer Workload verbessern können, erstellt Indizes und überprüft automatisch, ob die Leistung der Abfragen verbessert wurde. | Ja | Nein | 
| **DROP INDEX**: Identifiziert täglich redundante und doppelte Indizes (mit Ausnahme von eindeutigen Indizes) sowie Indizes, die über einen langen Zeitraum hinweg (über 90 Tage) nicht verwendet wurden. Beachten Sie, dass die Option zurzeit nicht kompatibel mit Anwendungen ist, die Partitionswechsel und Indexhinweise verwenden. | Ja | Nein |
| **FORCE LAST GOOD PLAN** (automatische Plankorrektur): Identifiziert SQL-Abfragen mit einem Ausführungsplan, der langsamer als der vorherige gute Plan ist, und Abfragen, die den letzten bekannten guten Plan anstelle des zurückgestellten Plans verwenden. | Ja | Ja |

Die automatische Optimierung identifiziert Empfehlungen von **CREATE INDEX**, **DROP INDEX** und **FORCE LAST GOOD PLAN**, die Ihre Datenbank optimieren können, zeigt sie im [Azure-Portal](sql-database-advisor-portal.md) an, und macht sie über [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) und die [REST-API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning) verfügbar. Um mehr über FORCE LAST GOOD PLAN und die Konfiguration von automatischen Optimierungsoptionen über T-SQL zu erfahren, lesen Sie [Automatisches Optimieren führt zu einer automatischen Plankorrektur](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/).

Sie können die Optimierungsempfehlungen entweder manuell über das Portal anwenden oder es der automatischen Optimierung überlassen, diese Empfehlungen autonom für Sie anzuwenden. Wenn Sie es dem System überlassen, Optimierungsempfehlungen autonom anzuwenden, profitieren Sie davon, dass das System automatisch überprüft, ob eine Empfehlung die Workloadleistung tatsächlich positiv beeinflusst. Sollte keine erhebliche Leistungsverbesserung erkannt werden, macht das System die Optimierungsempfehlung automatisch rückgängig. Beachten Sie, dass bei Abfragen, auf die sich Optimierungsempfehlungen auswirken und die nicht häufig ausgeführt werden, die Überprüfungsphase bis zu 72 Stunden dauern kann.

Wenn Sie Optimierungsempfehlungen manuell anwenden, sind die Mechanismen für die automatische Leistungsüberprüfung und Umkehrung nicht verfügbar. Darüber hinaus bleiben manuell angewandte Empfehlungen aktiv und werden 24 bis 48 Stunden lang in der Liste der Empfehlungen angezeigt. Danach zieht sie das System automatisch zurück. Wenn Sie eine Empfehlung früher entfernen möchten, können Sie sie manuell verwerfen.

Die Optionen für die automatische Optimierung können unabhängig pro Datenbank aktiviert oder deaktiviert werden. Sie können auch auf SQL-Datenbank-Servern konfiguriert und auf jede Datenbank angewandt werden, die Einstellungen von diesem Server erbt. SQL-Datenbank-Server können Azure-Standardwerte für die Einstellungen für automatische Optimierung erben. In den Azure-Standardwerten sind derzeit FORCE_LAST_GOOD_PLAN und CREATE_INDEX aktiviert und DROP_INDEX deaktiviert.

Das Konfigurieren der Optionen für die automatische Optimierung auf dem Server und das Erben von Einstellungen durch die Datenbanken auf dem übergeordneten Server wird für die Konfiguration der automatischen Optimierung empfohlen, da es die Verwaltung der Optionen für die automatische Optimierung bei einer großen Anzahl von Datenbanken vereinfacht.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Aktivieren der automatischen Optimierung in Azure SQL-Datenbank und zum Verwalten Ihrer Workload finden Sie unter [Aktivieren der automatischen Optimierung](sql-database-automatic-tuning-enable.md).
- Informationen zum manuellen Überprüfen und Anwenden von Empfehlungen der automatischen Optimierung finden Sie unter [Suchen und Anwenden von Empfehlungen zur Leistung](sql-database-advisor-portal.md).
- Informationen zum Verwenden von T-SQL zum Anwenden und Anzeigen von automatischen Optimierungsempfehlungen finden Sie unter [Manage automatic tuning via T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) (Verwalten der automatischen Optimierung über T-SQL).
- Informationen zum Erstellen von E-Mail-Benachrichtigungen für automatische Optimierungsempfehlungen finden Sie unter [E-Mail-Benachrichtigungen zur automatischen Optimierung](sql-database-automatic-tuning-email-notifications.md).
- Informationen zur integrierten Intelligenz, die bei der automatischen Optimierung zum Einsatz kommt, finden Sie unter [Optimierung von Azure SQL-Datenbanken durch künstliche Intelligenz](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Informationen zur Funktionsweise der automatischen Optimierung in Azure SQL-Datenbank und SQL Server 2017 finden Sie unter [Automatische Optimierung](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) für SQL Server.
