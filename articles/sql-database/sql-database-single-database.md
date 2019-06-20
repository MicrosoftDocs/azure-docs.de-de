---
title: Was ist eine Einzeldatenbank in Azure SQL-Datebank? | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Einzeldatenbank in Azure SQL-Datenbank
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 04/08/2019
ms.openlocfilehash: 36b1fb96ac1dd89375588a65063ce729f6ac825d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65794393"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Einzeldatenbank in Azure SQL-Datenbank?

Die Bereitstellungsoption für Singletons erstellt eine Datenbank in Azure SQL-Datenbank mit einem eigenen Satz von Ressourcen und wird über einen SQL-Datenbank-Server verwaltet. Bei einer Einzeldatenbank ist jede Datenbank isoliert und somit portabel, außerdem besitzt jede Datenbank eine eigene Dienstebene im [DTU-basierten Kaufmodell](sql-database-service-tiers-dtu.md) oder [vCore-basierten Kaufmodell](sql-database-service-tiers-vcore.md) mit einer garantierten Computegröße.

> [!IMPORTANT]
> Einzeldatenbank ist eine der drei Bereitstellungsoptionen für Azure SQL-Datenbank. Die anderen zwei sind [Pools für elastische Datenbanken](sql-database-elastic-pool.md) und [verwaltete Instanz](sql-database-managed-instance.md).
> [!NOTE]
> Ein Glossar von Begriffen in Azure SQL-Datenbank finden Sie unter [Azure SQL Database glossary of terms](sql-database-glossary-terms.md) (Azure SQL-Datenbank – Glossar).

## <a name="dynamic-scalability"></a>Dynamische Skalierbarkeit

Sie können Ihre erste App auf einer kleinen Einzeldatenbank kostengünstig auf der serverlosen (Vorschauversion) Computeebene oder einer kleinen Computegröße auf der bereitgestellten Computeebene erstellen. Die [Compute- oder Dienstebene](sql-database-single-database-scale.md) können Sie jederzeit manuell oder programmgesteuert ändern, um die Anforderungen Ihrer Lösung zu erfüllen. Die Leistungsanpassung ist möglich, ohne dass es für die App oder für Ihre Kunden zu Ausfallzeiten kommt. Dank der dynamischen Skalierbarkeit kann Ihre Datenbank in transparenter Form auf sich schnell ändernde Ressourcenanforderungen reagieren, und Sie zahlen nur für die Ressourcen, die Sie jeweils benötigen.

## <a name="single-databases-and-elastic-pools"></a>Einzeldatenbanken und Pools für elastische Datenbanken

Eine Einzeldatenbank kann in einen oder aus einem [Pool für elastische Datenbanken](sql-database-elastic-pool.md) für die gemeinsame Nutzung von Ressourcen verschoben werden. Für viele Unternehmen und Anwendungen genügt es, wenn Einzeldatenbanken erstellt werden können und sich die Leistung nach oben oder unten anpassen lässt – insbesondere, wenn die Nutzungsmuster relativ gut vorhersagbar sind. Bei unvorhersagbaren Nutzungsmustern kann es jedoch schwer sein, die Kosten und Ihr Geschäftsmodell zu verwalten. Pools für elastische Datenbanken sind darauf ausgelegt, dieses Problem zu beheben. Das Konzept ist denkbar einfach. Sie ordnen Leistungsressourcen nicht einer Einzeldatenbank, sondern einem Pool zu, sodass Sie für die gesamten Leistungsressourcen des Pools und nicht für die Leistung einer Einzeldatenbank bezahlen.

## <a name="monitoring-and-alerting"></a>Überwachung und Warnung

Verwenden Sie die [integrierten Features für die Leistungsüberwachung](sql-database-performance.md) sowie [Warnungstools](sql-database-insights-alerts-portal.md) in Kombination mit den Leistungsbewertungen. Mit diesen Tools können Sie die Auswirkungen des zentralen Hoch- oder Herunterskalierens je nach Ihren derzeitigen bzw. projektbezogenen Leistungsanforderungen schnell bewerten. Darüber hinaus kann SQL-Datenbank zur einfacheren Überwachung [Metriken und Diagnoseprotokolle ausgeben](sql-database-metrics-diag-logging.md).

## <a name="availability-capabilities"></a>Verfügbarkeitsfunktionen

Einzeldatenbanken, Pools für elastische Datenbanken und verwaltete Instanzen bieten zahlreiche Verfügbarkeitsmerkmale. Weitere Informationen finden Sie unter [Verfügbarkeitsmerkmale](sql-database-technical-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>Transact-SQL-Unterschiede

Die meisten Transact-SQL-Funktionen, die von Anwendungen verwendet werden, werden in Microsoft SQL Server und in der Azure SQL-Datenbank unterstützt. Die zentralen SQL-Komponenten wie z.B. Datentypen, Operatoren, Zeichenfolgen-, Arithmetik-, logische und Cursorfunktionen funktionieren in SQL Server und in der SQL-Datenbank gleich. Es gibt jedoch einige T-SQL-Unterschiede zwischen Elementen der Datendefinitionssprache (Data Definition Language, DDL) und der Datenbearbeitungssprache (Data Manipulation Language, DML), die dazu führen, dass T-SQL-Anweisungen und -Abfragen nur teilweise unterstützt werden (mehr dazu weiter unten in diesem Artikel).
Darüber hinaus werden einige Features und Syntax überhaupt nicht unterstützt, da die Azure SQL-Datenbank entwickelt wurde, um Features von Abhängigkeiten auf Masterdatenbank und Betriebssystem zu isolieren. An sich sind die meisten Aktivitäten auf Serverebene für die SQL-Datenbank nicht geeignet. T-SQL-Anweisungen und -Optionen sind nicht verfügbar, wenn sie Optionen auf Serverebene, Betriebssystemkomponenten oder bestimmte Dateisystemkonfigurationen konfigurieren. Wenn solche Funktionen erforderlich sind, dann ist häufig eine andere geeignete Alternative von der SQL-Datenbank oder aus einem anderen Azure-Feature oder -Dienst verfügbar.

Weitere Informationen finden Sie unter [Auflösen von Transact-SQL-Unterschieden während der Migration zur SQL-Datenbank](sql-database-transact-sql-information.md).

## <a name="security"></a>Sicherheit

SQL-Datenbank bietet eine Reihe von [integrierten Sicherheits- und Konformitätsfeatures](sql-database-security-overview.md), mit der Sie Ihre Anwendung an verschiedene Sicherheits- und Konformitätsanforderungen anpassen können.

> [!IMPORTANT]
> Azure SQL-Datenbank (alle Bereitstellungsoptionen) wurde anhand einer Reihe von Konformitätsstandards zertifiziert. Weitere Informationen finden Sie im [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), wo die aktuellste Liste von Compliance-Zertifizierungen für SQL-Datenbank angezeigt wird.

## <a name="next-steps"></a>Nächste Schritte

- Um schnell mit einer Einzeldatenbank zu beginnen, verwenden Sie die [Schnellstartanleitung für Einzeldatenbanken](sql-database-single-database-quickstart-guide.md).
- Weitere Informationen zum Migrieren einer SQL Server-Datenbank zu Azure finden Sie unter [Migrieren zu Azure SQL-Datenbank](sql-database-single-database-migrate.md).
- Informationen zu unterstützten Funktionen finden Sie unter [Features (Funktionen)](sql-database-features.md).