---
title: Worum handelt es sich beim Azure SQL-Datenbank-Dienst? | Microsoft-Dokumentation
description: Lernen Sie die technischen Details und Funktionen von SQL-Datenbank kennen, des relationalen Datenbankmanagementsystems von Microsoft in der Cloud.
keywords: Einführung in SQL, Was ist SQL-Datenbank?
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 04/08/2019
ms.openlocfilehash: ed94677eea91e3543dced9825a1372f60550a524
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65073616"
---
# <a name="what-is-azure-sql-database-service"></a>Worum handelt es sich beim Azure SQL-Datenbank-Dienst?

SQL-Datenbank ist ein relationaler verwalteter Datenbankdienst in Microsoft Azure für allgemeine Zwecke, der Strukturen wie relationale Daten, JSON, räumliche Daten und XML unterstützt. SQL-Datenbank bietet eine dynamisch skalierbare Leistung und ist in zwei verschiedenen Kaufmodellen verfügbar: im vCore-basierten Kaufmodell und im DTU-basierten Kaufmodell. SQL-Datenbank stellt auch Optionen wie z.B. [Columnstore-Indizes](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) für extrem umfangreiche Analysen und Berichte und [In-Memory-OLTP](sql-database-in-memory.md) für aufwendigste Transaktionsverarbeitungen bereit. Microsoft kümmert sich um sämtliche Patches und Updates der SQL-Codebasis und übernimmt damit die Verwaltung der gesamten zugrunde liegenden Infrastruktur.

> [!NOTE]
> Ein Glossar von Begriffen in Azure SQL-Datenbank finden Sie unter [Azure SQL Database glossary of terms](sql-database-glossary-terms.md) (Azure SQL-Datenbank – Glossar).

Azure SQL-Datenbank bietet die folgenden Bereitstellungsoptionen für eine Azure SQL-Datenbank:

- Als [Singleton](sql-database-single-database.md) mit einem eigenen Ressourcensatz, der über einen SQL-Datenbank-Server verwaltet wird Ein Singleton ähnelt einer [eigenständigen Datenbank](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) in SQL Server.
- Als [Pool für elastische Datenbanken](sql-database-elastic-pool.md), bei dem es sich um eine Sammlung von Datenbanken mit einem gemeinsamen Ressourcensatz handelt, der über einen SQL-Datenbank-Server verwaltet wird Singletons können in und aus Pools für elastische Datenbanken verschoben werden.
- Als [verwaltete Instanz](sql-database-managed-instance.md), bei der es sich um eine Sammlung von System- und Benutzerdatenbanken mit einem gemeinsam genutzten Ressourcensatz handelt. Eine verwaltete Instanz ist vergleichbar mit einer Instanz der [Microsoft SQL Server-Datenbank-Engine](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation).

Die folgende Abbildung stellt diese Bereitstellungsoptionen dar:

![Bereitstellungsoptionen](./media/sql-database-technical-overview/deployment-options.png)

SQL-Datenbank nutzt diese Codebasis gemeinsam mit der [Microsoft SQL Server-Datenbank-Engine](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation). Mit der Cloud-First-Strategie von Microsoft werden die neuesten Funktionen von SQL Server zunächst in SQL-Datenbank und erst dann in SQL Server selbst veröffentlicht. Durch diesen Ansatz verfügen Sie immer über die neuesten Features von SQL Server – ohne den Mehraufwand für Patches oder Updates –, die in Millionen von Datenbanken getestet wurden. Informationen zu angekündigten neuen Funktionen finden Sie unter:

- **[Azure-Roadmap für SQL-Datenbank](https://azure.microsoft.com/roadmap/?category=databases)** :

  Hier finden Sie Informationen zu neuen und geplanten Funktionen.

- **[Azure SQL-Datenbank-Blog](https://azure.microsoft.com/blog/topics/database)** :

  Hier schreiben die Mitglieder von SQL Server-Produktteams Beiträge zu News und Features von SQL-Datenbank.

> [!IMPORTANT]
> Funktionelle Unterschiede zwischen SQL-Datenbank und SQL Server sowie Unterschiede zwischen verschiedenen Bereitstellungsoptionen für Azure SQL-Datenbank finden Sie unter [SQL-Funktionen](sql-database-features.md).

SQL-Datenbank bietet eine vorhersagbare Leistung mit mehreren Ressourcentypen, Dienstebenen und Computegrößen, die eine dynamische Skalierung ohne Downtime, integrierte intelligente Optimierung, globale Skalierbarkeit und Verfügbarkeit sowie erweiterte Sicherheitsoptionen bieten – alles mit nahezu keinem Verwaltungsaufwand. Mit diesen Funktionen können Sie sich auf die schnelle Entwicklung von Apps und die Verkürzung des Zeitraums bis zur Markteinführung konzentrieren, anstatt wertvolle Zeit und Ressourcen für die Verwaltung von virtuellen Computern und der Infrastruktur aufwenden zu müssen. Der SQL-Datenbank-Dienst wird derzeit in 38 Rechenzentren auf der ganzen Welt ausgeführt – und regelmäßig werden neue online geschaltet. Dies ermöglicht die Ausführung Ihrer Datenbank in einem Rechenzentrum in Ihrer Nähe.

## <a name="scalable-performance-and-pools"></a>Skalierbare Leistung und Pools

- Bei Singletons ist jede Datenbank von den anderen isoliert und damit portabel. Jede Datenbank verfügt über eine eigene garantierte Menge an Compute-, Arbeitsspeicher- und Speicherressourcen. SQL-Datenbank bietet verschiedene Compute-, Arbeitsspeicher- und Speicherressourcen für unterschiedliche Anforderungen – sowie die Möglichkeit, [Singletonressourcen dynamisch zentral hoch- und herunterzuskalieren](sql-database-single-database-scale.md). Mit der [Dienstebene „Hyperscale“](sql-database-service-tier-hyperscale.md) für Einzeldatenbanken können Sie bis zu 100 TB mit schnellen Sicherungs- und Wiederherstellungsfunktionen skalieren.
- Mit Pools für elastische Datenbanken können Sie neue Datenbanken erstellen oder Singletons in einen Ressourcenpool verschieben, um die Ressourcennutzung zu maximieren und Geld zu sparen. Außerdem haben Sie so die Möglichkeit, [Ressourcen für Pools für elastische Datenbanken dynamisch zentral hoch- und herunterzuskalieren](sql-database-elastic-pool-scale.md).
- Bei verwalteten Instanzen ist jede verwaltete Instanz von den anderen Instanzen isoliert und verfügt über garantierte Ressourcen. In einer verwalteten Instanz nutzen die Instanzdatenbanken einen gemeinsamen Satz von Ressourcen. Somit haben Sie die Möglichkeit, [Ressourcen für eine verwaltete Instanz dynamisch hoch- und herunterzuskalieren](sql-database-managed-instance-resource-limits.md).

Sie können zu einer geringen monatlichen Gebühr Ihre erste App in einer kleinen Einzeldatenbank in der Dienstebene „Universell“ erstellen und diese dann jederzeit manuell oder programmgesteuert in die Dienstebene „Unternehmenskritisch“ ändern, um die Anforderungen Ihrer Lösung zu erfüllen. Die Leistungsanpassung ist möglich, ohne dass es für die App oder für Ihre Kunden zu Ausfallzeiten kommt. Dank der dynamischen Skalierbarkeit kann Ihre Datenbank in transparenter Form auf sich schnell ändernde Ressourcenanforderungen reagieren, und Sie zahlen nur für die Ressourcen, die Sie jeweils benötigen.

Dynamische Skalierbarkeit ist nicht dasselbe wie automatische Skalierung. Bei der automatischen Skalierung wird ein Dienst automatisch auf der Grundlage von Kriterien skaliert. Die dynamische Skalierbarkeit ermöglicht dagegen eine manuelle Skalierung ohne Ausfallzeiten. Eine Einzeldatenbank unterstützt die manuelle dynamische Skalierbarkeit, aber keine automatische Skalierung. Ein höheres Maß an *Automatisierung* lässt sich bei Bedarf mithilfe von Pools für elastische Datenbanken erzielen, die die gemeinsame Nutzung eines Ressourcenpools auf der Grundlage individueller Datenbankanforderungen ermöglichen. Es gibt allerdings auch Skripts, die Sie bei der Automatisierung der Skalierbarkeit für eine Einzeldatenbank unterstützen. Ein Beispiel finden Sie unter [Überwachen und Skalieren einer Einzeldatenbank mit PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="purchasing-models-service-tiers-compute-sizes-and-storage-amounts"></a>Kaufmodelle, Dienstebenen, Computegrößen und Speichermengen

SQL-Datenbank bietet zwei Kaufmodelle:

- Das [DTU-basierte Kaufmodell](sql-database-service-tiers-dtu.md) bietet zur Unterstützung von einfachen bis hin zu komplexen Datenbankworkloads eine Mischung aus Compute-, Arbeitsspeicher- und E/A-Ressourcen in drei Dienstebenen. Die Computegrößen der einzelnen Ebenen bieten unterschiedliche Ressourcenzusammenstellungen, denen Sie zusätzliche Speicherressourcen hinzufügen können.
- Beim [vCore-basierten Kaufmodell](sql-database-service-tiers-vcore.md) können Sie die Anzahl virtueller Kerne, die Arbeitsspeichermenge sowie Menge und Geschwindigkeit des Speichers auswählen. Mit dem vCore-basierten Kaufmodell können Sie auch den [Azure-Hybridvorteil für SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) nutzen, um Kosten einzusparen. Weitere Informationen zum Azure-Hybridvorteil finden Sie in den [häufig gestellten Fragen](#sql-database-frequently-asked-questions-faq).

  

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Pools für elastische Datenbanken zum Maximieren der Ressourcenverwendung

Für viele Unternehmen und Anwendungen genügt es, wenn Einzeldatenbanken erstellt werden können und sich die Leistung nach oben oder unten anpassen lässt – insbesondere, wenn die Nutzungsmuster relativ gut vorhersagbar sind. Bei unvorhersagbaren Nutzungsmustern kann es jedoch schwer sein, die Kosten und Ihr Geschäftsmodell zu verwalten. [Pools für elastische Datenbanken](sql-database-elastic-pool.md) sind darauf ausgelegt, dieses Problem zu beheben. Das Konzept ist denkbar einfach. Sie ordnen Leistungsressourcen nicht einer Einzeldatenbank, sondern einem Pool zu, sodass Sie für die gesamten Leistungsressourcen des Pools und nicht für die Leistung einer Einzeldatenbank bezahlen.

   ![Pools für elastische Datenbanken](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Bei Pools für elastische Datenbanken müssen Sie sich nicht darauf konzentrieren, die Datenbankleistung nach oben oder unten anzupassen, wenn der Ressourcenbedarf schwankt. Die Pooldatenbanken nutzen die Leistungsressourcen des Pools für elastische Datenbanken je nach Bedarf. Pooldatenbanken nutzen die Grenzwerte des Pools, überschreiten sie aber nicht. Ihre Kosten bleiben also vorhersagbar, auch wenn sich die Nutzung der einzelnen Datenbanken nicht prognostizieren lässt. Darüber hinaus können Sie [Datenbanken im Pool hinzufügen und entfernen](sql-database-elastic-pool-manage-portal.md)und so Ihre App von einigen wenigen auf Tausende Datenbanken skalieren, und das in einem kontrollierten Kostenrahmen. Sie können außerdem die minimalen und maximalen Ressourcen steuern, die für die Datenbanken im Pool verfügbar sind. So können Sie sicherstellen, dass keine Datenbank im Pool alle Poolressourcen verbraucht und dass jede Pooldatenbank über ein garantiertes Minimum an Ressourcen verfügt. Weitere Informationen zu Entwurfsmustern für SaaS-Anwendungen, für die Pools für elastische Datenbanken verwendet werden, finden Sie unter [Entwurfsmuster für SaaS-Anwendungen mit mehreren Mandanten und SQL-Datenbank](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Skripts können Sie bei der Überwachung und Skalierung von Pools für elastische Datenbanken unterstützen. Ein Beispiel finden Sie unter [Verwenden von PowerShell zum Überwachen und Skalieren eines Pools für elastische SQL-Datenbanken in der Azure SQL-Datenbank](scripts/sql-database-monitor-and-scale-pool-powershell.md).

> [!IMPORTANT]
> Für eine verwaltete Instanz werden Pools für elastische Datenbanken nicht unterstützt. Eine verwaltete Instanz ist vielmehr eine Sammlung von Instanzdatenbanken, die die Ressourcen der verwalteten Instanz gemeinsam nutzen.

### <a name="blend-single-databases-with-pooled-databases"></a>Mischen von Einzeldatenbanken mit Pooldatenbanken

Sie haben die Möglichkeit, Einzeldatenbanken mit Pools für elastische Datenbanken zu kombinieren und die Dienstebenen von Einzeldatenbanken und Pools für elastische Datenbanken schnell und einfach an Ihre Bedürfnisse anzupassen. Mit der Leistungsstärke und Reichweite von Azure können Sie andere Azure-Dienste mit SQL-Datenbank kombinieren, um Ihre individuellen Anforderungen bei der App-Entwicklung zu erfüllen, Kosten- und Ressourceneffizienz zu fördern und neue Geschäftsmöglichkeiten zu erschließen.

### <a name="extensive-monitoring-and-alerting-capabilities"></a>Umfassende Funktionen für Überwachung und Warnung

Verwenden Sie die [integrierten Features für die Leistungsüberwachung](sql-database-performance.md) sowie [Warnungstools](sql-database-insights-alerts-portal.md) in Kombination mit den Leistungsbewertungen. Mit diesen Tools können Sie die Auswirkungen des zentralen Hoch- oder Herunterskalierens je nach Ihren derzeitigen bzw. projektbezogenen Leistungsanforderungen schnell bewerten. Darüber hinaus kann SQL-Datenbank zur einfacheren Überwachung [Metriken und Diagnoseprotokolle ausgeben](sql-database-metrics-diag-logging.md). Sie können SQL-Datenbank zum Speichern von Ressourcenverbrauch, Workern und Sitzungen sowie Verbindungen in einer der folgenden Azure-Ressourcen konfigurieren:

- **Azure Storage**: Ermöglicht die kostengünstige Archivierung großer Mengen von Telemetriedaten.
- **Azure Event Hub**: Ermöglicht die Integration von Telemetriedaten von SQL-Datenbank in Ihre benutzerdefinierte Überwachungslösung oder in Hotpipelines.
- **Azure Monitor-Protokolle:** Ermöglicht die Verwendung einer integrierten Überwachungslösung mit Funktionen für Berichterstellung, Warnungen und Problemlösung.

    ![Architektur](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Verfügbarkeitsfunktionen

In einer herkömmlichen SQL Server-Umgebung verfügen Sie in der Regel über (mindestens) zwei Computer, die lokal mit exakten (synchron verwalteten) Kopien der Daten eingerichtet sind (mit Funktionen wie AlwaysOn-Verfügbarkeitsgruppen oder Failoverclusterinstanzen), um sich vor dem Ausfall einzelner Computer/Komponenten zu schützen.  Dies gewährleistet Hochverfügbarkeit, bietet aber keinen Schutz vor einer Naturkatastrophe, die Ihr Rechenzentrum zerstört.

Die Notfallwiederherstellung geht davon aus, dass ein katastrophales Ereignis geografisch lokal so begrenzt ist, dass es an entfernter Stelle einen anderen Computer bzw. eine andere Gruppe von Computern mit einer Kopie Ihrer Daten gibt.  In SQL Server können Sie AlwaysOn-Verfügbarkeitsgruppen im asynchronen Modus verwenden, um diese Vorgabe zu erfüllen.  Die „Lichtgeschwindigkeitsprobleme“ bedeuten in der Regel, dass Benutzer nicht warten möchten, bis die Replikation in so weiter Entfernung entfernt erfolgt ist, bevor sie eine Transaktion committen, sodass es ein Potenzial für Datenverlust gibt, wenn Sie ungeplante Failover durchführen.

Datenbanken auf Premium- und unternehmenskritischen Dienstebenen [leisten bereits etwas sehr Ähnliches](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) für die Synchronisierung einer Verfügbarkeitsgruppe. Datenbanken auf niedrigeren Dienstebenen bieten Redundanz mithilfe von Speicher, der einen [anderen, aber vergleichbaren Mechanismus](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) verwendet. Es gibt Logik, die Schutz vor dem Ausfall eines einzelnen Computers bietet.  Das Feature „Aktive Georeplikation“ bietet Ihnen die Möglichkeit, sich vor Katastrophen zu schützen, bei denen eine ganze Region zerstört wird.

Azure-Verfügbarkeitszonen sind ein Beitrag zum Thema Hochverfügbarkeit.  Es wird versucht, Schutz gegen den Ausfall eines einzelnen Rechenzentrumsgebäudes innerhalb einer Region zu bieten.  Daher soll für ein Gebäude Schutz vor dem Verlust der Stromversorgung oder des Netzwerks geboten werden. In SQL Azure funktioniert dies, indem die verschiedenen Replikate in unterschiedlichen Verfügbarkeitszonen (also verschiedenen Gebäuden) platziert werden und ansonsten wie bisher weiterarbeiten.

Durch die Unterstützung eines globalen Netzwerks von durch Microsoft verwaltete Rechenzentren stellt die branchenführende Verfügbarkeit von Azure mit einer [Vereinbarung zum Servicelevel (SLA)](https://azure.microsoft.com/support/legal/sla/) von 99,99 % sicher, dass Ihre Apps rund um die Uhr ausgeführt werden. Die Azure-Plattform führt eine vollständige Verwaltung jeder Datenbank durch und garantiert die Vermeidung von Datenverlusten und einen hohen Prozentsatz in Bezug auf die Datenverfügbarkeit. In Azure werden Bereiche wie Patchen, Sicherungen, Replikation, Fehlererkennung, zugrunde liegende potenzielle Hardware-, Software- oder Netzwerkfehler, Bereitstellung von Fehlerbehebungen, Failover, Datenbankupgrades und andere Wartungsaufgaben automatisch durchgeführt. Die Standardverfügbarkeit wird erreicht, indem eine Unterteilung in Compute- und Speicherebenen vorgenommen wird. Premium-Verfügbarkeit wird erreicht, indem Compute- und Speicherbereich aus Leistungsgründen auf einem einzelnen Knoten angeordnet und dann im Hintergrund Technologien implementiert werden, die mit Always On-Verfügbarkeitsgruppen vergleichbar sind. Eine vollständige Beschreibung der Funktionen für Hochverfügbarkeit von Azure SQL-Datenbank finden Sie unter [Verfügbarkeit von Azure SQL-Datenbank](sql-database-high-availability.md). SQL-Datenbank bietet außerdem integrierte Features für die [Geschäftskontinuität und globale Skalierbarkeit](sql-database-business-continuity.md). Dazu gehören u.a.:

- **[Automatische Sicherungen:](sql-database-automated-backups.md)**

  SQL-Datenbank führt automatisch vollständige, differenzielle und Transaktionsprotokollsicherungen von Azure SQL-Datenbanken aus, sodass Sie diese zu einem beliebigen Zeitpunkt wiederherstellen können. Für Einzel- und Pooldatenbanken können Sie SQL-Datenbank zum Speichern von vollständigen Datenbanksicherungen in Azure Storage für die langfristige Aufbewahrung konfigurieren. Für verwaltete Instanzen können Sie auch ausschließlich kopierbare Sicherungen für die langfristige Sicherungsaufbewahrung ausführen.

- **[Point-in-Time-Wiederherstellungen:](sql-database-recovery-using-backups.md)**

  Alle Bereitstellungsoptionen von SQL-Datenbank unterstützen die Wiederherstellung zu einem beliebigen Zeitpunkt innerhalb der Vermerkdauer für automatische Sicherungen für sämtliche Azure SQL-Datenbanken.
- **[Aktive Georeplikation:](sql-database-active-geo-replication.md)**

  Bei Einzel- und Pooldatenbanken können Sie bis zu vier lesbare sekundäre Datenbanken konfigurieren – im gleichen Azure-Rechenzentrum oder in weltweit verteilten Azure-Rechenzentren.  Wenn Sie beispielsweise über eine SaaS-Anwendung mit einer Katalogdatenbank verfügen, die eine große Anzahl gleichzeitiger schreibgeschützter Transaktionen umfasst, können Sie mithilfe der aktiven Georeplikation eine globale Skalierung für das Lesen aktivieren und so Engpässe in der primären Datenbank beseitigen, die durch hohe Workloads aufgrund der Lesevorgänge verursacht werden. Verwenden Sie für verwaltete Instanzen Autofailover-Gruppen.
- **[Autofailover-Gruppen](sql-database-auto-failover-group.md)** :

  Alle Bereitstellungsoptionen von SQL-Datenbank ermöglichen die Verwendung von Failovergruppen, um Hochverfügbarkeit und Lastenausgleich auf globaler Ebene zu gewährleisten, einschließlich transparenter Georeplikation, Failovern großer Mengen von Datenbanken, Pools für elastische Datenbanken und verwalteter Instanzen. Failovergruppen erlauben das Erstellen von weltweit verteilten SaaS-Anwendungen mit minimalem Verwaltungsaufwand, bei dem alle komplexen Aufgaben für Überwachung, Weiterleitung und Failoverorchestrierung von SQL-Datenbank erledigt werden.
- **[Zonenredundante Datenbanken:](sql-database-high-availability.md)**

  SQL-Datenbank ermöglicht die Bereitstellung von Premium-Datenbanken, unternehmenskritischen Datenbanken oder Pools für elastische Datenbanken über mehrere Verfügbarkeitszonen hinweg. Datenbanken und Pools für elastische Datenbanken verfügen über mehrere redundante Replikate, um die Hochverfügbarkeit sicherzustellen. Die Anordnung dieser Replikate in mehreren Verfügbarkeitszonen sorgt für eine höhere Resilienz, die beispielsweise die automatische Wiederherstellung nach Ausfällen von Datencentern ohne Datenverlust ermöglicht.

## <a name="built-in-intelligence"></a>Integrierte Logik

SQL-Datenbanken stellt integrierte Logik bereit, die Ihnen hilft, den Aufwand für die Ausführung und Verwaltung von Datenbanken erheblich zu senken und die Leistung und Sicherheit Ihrer Anwendung zu maximieren. Bei der Ausführung von Millionen von Kundenworkloads rund um die Uhr sammelt und verarbeitet SQL-Datenbank eine riesige Menge an Telemetriedaten. Dabei bleiben die Kundendaten im Hintergrund jedoch permanent geschützt. Verschiedene Algorithmen werten fortlaufend die Telemetriedaten aus, damit der Dienst mit Ihrer Anwendung lernen und sich anpassen kann. Auf Grundlage dieser Analyse schlägt der Dienst Empfehlungen zur Verbesserung der Leistung vor, die auf Ihre Workload zugeschnitten sind.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatische Leistungsüberwachung und -optimierung

SQL-Datenbank bietet detaillierte Einblicke in die Abfragen, die Sie überwachen sollten. SQL-Datenbank erlernt Ihre Datenbankmuster und ermöglicht Ihnen das Anpassen Ihres Datenbankschemas an Ihre Workload. SQL-Datenbank stellt [Empfehlungen zur Leistungsoptimierung](sql-database-advisor.md) bereit, mit denen Sie Optimierungsschritte überprüfen und anwenden können.

Eine kontinuierliche Überwachung der Datenbank ist jedoch eine schwierige und aufwendige Aufgabe, insbesondere bei sehr vielen Datenbanken. [Intelligent Insights](sql-database-intelligent-insights.md) übernimmt diese Aufgabe für Sie, indem die skalierbare Leistung von SQL-Datenbank überwacht wird. Sie werden zudem über Probleme mit der Leistungsminderung informiert. Außerdem wird die Grundursache des Problems identifiziert, und Empfehlungen zur Leistungsverbesserung werden nach Möglichkeit bereitgestellt.

Das Verwalten einer großen Anzahl von Datenbanken ist eventuell nicht möglich, selbst mit allen verfügbaren Tools und Berichten, die SQL-Datenbank und das Azure-Portal bereitstellen. Anstelle der manuellen Überwachung und Optimierung der Datenbank sollten Sie erwägen, einige der Überwachungs- und Optimierungsaktionen mithilfe der [automatischen Optimierung](sql-database-automatic-tuning.md) an SQL-Datenbank zu delegieren. SQL-Datenbank wendet Empfehlungen automatisch an und testet und überprüft jede der Optimierungsaktionen, um sicherzustellen, dass sich die Leistung auch wirklich verbessert. Auf diese Weise passt sich SQL-Datenbank automatisch auf kontrollierte und sichere Weise an Ihre Workload an. Die automatische Optimierung bedeutet, dass die Leistung Ihrer Datenbank vor und nach jeder Optimierungsaktion sorgfältig überwacht und verglichen wird – und wenn sich die Leistung nicht verbessert, wird die Optimierungsaktion zurückgesetzt.

Bereits heute verlassen sich viele unserer Partner, die [mehrinstanzenfähige SaaS-Apps](sql-database-design-patterns-multi-tenancy-saas-applications.md) auf Grundlage von SQL-Datenbank ausführen, auf die automatische Leistungsoptimierung, um sicherzustellen, dass ihre Anwendungen immer eine stabile und zuverlässige Leistung zeigen. Für sie verringert dieses Feature das Risiko, dass mitten in der Nacht ein Leistungsproblem auftritt, enorm. Da darüber hinaus auch ein Teil ihrer Kundenbasis SQL Server verwendet, wenden sie dieselben Indizierungsempfehlungen von SQL-Datenbank an, um ihre SQL Server-Kunden zu unterstützen.

Es gibt zwei Aspekte der automatischen Optimierung, die [in SQL-Datenbank verfügbar](sql-database-automatic-tuning.md) sind:

- **Automatische Indexverwaltung**: Diese Funktion identifiziert Indizes, die der Datenbank hinzugefügt, und solche, die entfernt werden sollten.
- **Automatische Plankorrektur**: Diese Funktion erkennt problematische Pläne und korrigiert Leistungsprobleme mit SQL-Plänen (in Kürze, in SQL Server 2017 bereits verfügbar).

### <a name="adaptive-query-processing"></a>Adaptive Abfrageverarbeitung

Wir fügen darüber hinaus auch einen Featuresatz zur [adaptiven Abfrageverarbeitung](/sql/relational-databases/performance/intelligent-query-processing) in SQL-Datenbank hinzu. Dies schließt auch die überlappende Ausführung für Tabellenwertfunktionen mit mehreren Anweisungen, Feedback zur Speicherzuweisung im Batchmodus und adaptive Joins im Batchmodus ein. Jedes dieser Features zur adaptiven Abfrageverarbeitung wendet ähnliche Techniken zum Lernen und Anpassen an, um weitere Leistungsprobleme zu beheben, die durch traditionell schwierig zu lösende Probleme bei der Abfrageoptimierung verursacht werden.

## <a name="advanced-security-and-compliance"></a>Erweiterte Sicherheit und Konformität

SQL-Datenbank bietet eine Reihe von [integrierten Sicherheits- und Konformitätsfeatures](sql-database-security-overview.md), mit der Sie Ihre Anwendung an verschiedene Sicherheits- und Konformitätsanforderungen anpassen können.

> [!IMPORTANT]
> Azure SQL-Datenbank (alle Bereitstellungsoptionen) wurde anhand einer Reihe von Konformitätsstandards zertifiziert. Weitere Informationen finden Sie im [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), wo die aktuellste Liste von Compliance-Zertifizierungen für SQL-Datenbank angezeigt wird.

### <a name="advance-threat-protection"></a>Advanced Threat Protection

Advanced Data Security ist ein einheitliches Paket für erweiterte SQL-Sicherheitsfunktionen. Dazu zählen die Funktion zur Ermittlung und Klassifizierung sensibler Daten, die Verwaltung von Datenbankrisiken und die Erkennung ungewöhnlicher Aktivitäten, die eine Bedrohung für Ihre Datenbank darstellen können. Es bietet einen einzelnen Anlaufpunkt zum Aktivieren und Verwalten dieser Funktionen.

- [Datenermittlung und -klassifizierung:](sql-database-data-discovery-and-classification.md)

  Dieses Feature befindet sich derzeit in der Vorschauphase und bietet in Azure SQL-Datenbank integrierte Funktionen zum Ermitteln, Klassifizieren, Bezeichnen und Schützen sensibler Daten in Ihren Datenbanken. Das Feature kann Einblicke in den Zustand Ihrer Datenbankklassifizierung bereitstellen und den Zugriff auf sensible Daten innerhalb der Datenbank und außerhalb ihrer Grenzen verfolgen.
- [Sicherheitsrisikobewertung:](sql-vulnerability-assessment.md)

  Dieser Dienst kann potenzielle Schwachstellen in der Datenbank ermitteln, nachverfolgen und Sie bei der Beseitigung unterstützen. Er bietet Einblicke in Ihren Sicherheitsstatus, enthält umsetzbare Schritte zum Beheben von Sicherheitsproblemen und verbessert Ihre Datenbanksicherheit.
- [Bedrohungserkennung:](sql-database-threat-detection.md)

  Dieses Feature erkennt anormale Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hindeuten, auf Ihre Datenbank zuzugreifen oder sie missbräuchlich zu nutzen. Sie überwacht Ihre Datenbank fortlaufend auf verdächtige Aktivitäten und bietet sofortige Sicherheitswarnungen zu potenziellen Sicherheitslücken, Angriffe durch Einschleusung von SQL-Befehlen und ungewöhnliche Datenbankzugriffsmuster. Die Warnungen der Bedrohungserkennung enthalten Details zu verdächtigen Aktivitäten sowie Empfehlungen, wie die Bedrohung untersucht und abgewendet werden kann.

### <a name="auditing-for-compliance-and-security"></a>Überwachung auf Konformität und Sicherheit

Die [Überwachung](sql-database-auditing.md) verfolgt Datenbankereignisse und schreibt diese in ein Überwachungsprotokoll in Ihrem Azure Storage-Konto. Die Überwachung kann Ihnen dabei helfen, die gesetzlichen Bestimmungen einzuhalten, die Datenbankaktivität zu verstehen und Einblicke in Abweichungen und Anomalien zu erhalten, die auf geschäftsspezifische Bedenken oder mutmaßliche Sicherheitsverstöße hinweisen können.

### <a name="data-encryption"></a>Datenverschlüsselung

SQL-Datenbank schützt Ihre Daten, indem die Verschlüsselung für Daten in Bewegung mit [Transport Layer Security](https://support.microsoft.com/kb/3135244), für ruhende Daten mit [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) und für verwendete Daten mit [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) ermöglicht wird.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-Integration und Multi-Factor Authentication

SQL-Datenbank ermöglicht über die [Azure Active Directory-Integration](sql-database-aad-authentication.md) eine zentrale Verwaltung von Identitäten der Datenbankbenutzer und anderer Microsoft-Dienste. Diese Funktion vereinfacht die Berechtigungsverwaltung und erhöht die Sicherheit. Azure Active Directory unterstützt [Multi-Factor Authentication](sql-database-ssms-mfa-authentication.md) (MFA), um die Daten- und Anwendungssicherheit zu erhöhen, während gleichzeitig einmaliges Anmelden unterstützt wird.

### <a name="compliance-certification"></a>Konformitätszertifizierung

SQL-Datenbank ist an regulären Überwachungen beteiligt und wurde für mehrere Konformitätsstandards zertifiziert. Weitere Informationen finden Sie im [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), wo die aktuellste Liste von Compliance-Zertifizierungen für SQL-Datenbank angezeigt wird.

## <a name="easy-to-use-tools"></a>Benutzerfreundliche Tools

SQL-Datenbank sorgt für einfachere und produktivere Abläufe beim Erstellen und Verwalten von Anwendungen. Mit SQL-Datenbank können Sie sich auf das konzentrieren, was Sie am besten können: erstklassige Apps entwickeln. Sie können in SQL-Datenbank Verwaltungs- und Entwicklungsaufgaben mithilfe von Tools und Fertigkeiten durchführen, über die Sie bereits verfügen.

- **[Das Azure-Portal:](https://portal.azure.com/)**

  Eine webbasierte Anwendung für die Verwaltung aller Azure-Dienste.
- **[SQL Server Management Studio:](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)**

  Eine kostenlose, herunterladbare Clientanwendung für die Verwaltung beliebiger SQL-Infrastrukturen – von SQL Server bis SQL-Datenbank.
- **[SQL Server Data Tools in Visual Studio:](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)**

  Eine kostenlose, herunterladbare Clientanwendung, mit der Sie relationale SQL Server-Datenbanken, Azure SQL-Datenbanken, Integration Services-Pakete, Analysis Services-Datenmodelle und Reporting Services-Berichte erstellen können.
- **[Visual Studio Code:](https://code.visualstudio.com/docs)**

  Ein kostenloser, herunterladbarer Open-Source-Code-Editor für Windows, macOS und Linux, der Erweiterungen unterstützt – etwa die [mssql-Erweiterung](https://aka.ms/mssql-marketplace) zum Abfragen von Microsoft SQL Server, Azure SQL-Datenbank und SQL Data Warehouse.

SQL-Datenbank unterstützt das Erstellen von Anwendungen mit Python, Java, Node.js, PHP, Ruby und .NET unter macOS, Linux und Windows. SQL-Datenbank unterstützt dieselben [Verbindungsbibliotheken](sql-database-libraries.md) wie SQL Server.

## <a name="sql-database-frequently-asked-questions-faq"></a>Häufig gestellte Fragen (FAQ) zu SQL-Datenbank

### <a name="what-is-the-current-version-of-sql-database"></a>Was ist die aktuelle Version von SQL-Datenbank?

Die aktuelle Version von SQL-Datenbank ist V12. Version V11 wurde eingestellt.

### <a name="can-i-control-when-patching-downtime-occurs"></a>Kann ich den Zeitpunkt der Ausfallzeiten beim Patchen steuern?

Nein. Die Auswirkungen vom Patchen sind im Allgemeinen nicht merklich, wenn Sie in Ihrer App [Wiederholungslogik einsetzen](sql-database-develop-overview.md#resiliency). Weitere Informationen zur Vorbereitung auf geplante Wartungsereignisse in Azure SQL-Datenbank finden Sie unter [Planen von Azure-Wartungsereignissen in Azure SQL-Datenbank](sql-database-planned-maintenance.md).

### <a name="azure-hybrid-benefit-questions"></a>Fragen zum Azure-Hybridvorteil

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Gibt es für den Azure-Hybridvorteil für SQL Server Dualnutzungsrechte?

Sie verfügen für die Lizenz 180 Tage lang über Dualnutzungsrechte, um sicherzustellen, dass die Migrationen reibungslos durchgeführt werden können. Nach Ablauf der 180 Tage kann die SQL Server-Lizenz nur in der Cloud in SQL-Datenbank verwendet werden, und lokal und in der Cloud gelten keine Dualnutzungsrechte mehr.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Wie unterscheidet sich der Azure-Hybridvorteil für SQL Server von License Mobility?

Derzeit bieten wir License Mobility-Vorteile für SQL Server-Kunden mit Software Assurance an, in deren Rahmen die Neuzuweisung von Lizenzen zu freigegebenen Drittanbieterservern möglich ist. Dieser Vorteil kann für Azure IaaS und AWS EC2 verwendet werden.
Der Azure-Hybridvorteil für SQL Server unterscheidet sich in zwei wichtigen Bereichen von License Mobility:

- Er ist mit wirtschaftlichen Vorteilen in Bezug auf das Verlagern von hochgradig virtualisierten Workloads nach Azure verbunden. SQL EE-Kunden können in Azure für die SKU „Universell“ für jeden Kern, der lokal vorhanden ist, vier Kerne für hochgradig virtualisierte Anwendungen erhalten. Bei License Mobility ergeben sich bei der Verlagerung von virtualisierten Workloads in die Cloud keine besonderen Kostenvorteile.
- Hierbei ist ein PaaS-Ziel in Azure (eine verwaltete SQL-Datenbank-Instanz) vorhanden, das mit der lokalen SQL Server-Instanz hochgradig kompatibel ist.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Welche spezifischen Rechte gelten für den Azure-Hybridvorteil für SQL Server?

SQL-Datenbank-Kunden sind beim Azure-Hybridvorteil für SQL Server die folgenden Rechte zugeordnet:

|Lizenzspeicherbedarf|Was erhalten Sie beim Azure-Hybridvorteil für SQL Server?|
|---|---|
|SQL Server Enterprise Edition – Hauptkunden mit SA|<li>Zahlung des Basistarifs für SKU „Universell“ oder „Unternehmenskritisch“</li><br><li>1 Kern lokal = 4 Kerne in SKU „Universell“</li><br><li>1 Kern lokal = 1 Kern in SKU „Unternehmenskritisch“</li>|
|SQL Server Standard Edition – Hauptkunden mit SA|<li>Zahlung des Basistarifs nur für SKU „Universell“</li><br><li>1 Kern lokal = 1 Kern in SKU „Universell“</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>Kontakt mit dem SQL Server-Entwicklungsteam aufnehmen

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): Stellen Sie Fragen zur Datenbankverwaltung.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): Stellen Sie Fragen zur Entwicklung.
- [MSDN-Foren](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Stellen Sie technische Fragen.
- [Feedback](https://aka.ms/sqlfeedback): Hier können Sie Fehler melden und Features anfordern.
- [Reddit](https://www.reddit.com/r/SQLServer/): Tauschen Sie sich über SQL Server aus.

## <a name="next-steps"></a>Nächste Schritte

- Unter [Preise](https://azure.microsoft.com/pricing/details/sql-database/) finden Sie Preisvergleiche und Rechner für Einzeldatenbanken und Pools für elastische Datenbanken.
- Diese Schnellstarts erleichtern Ihnen den Einstieg:

  - [Create a SQL database in the Azure portal](sql-database-single-database-get-started.md) (Erstellen einer SQL-Datenbank im Azure-Portal)  
  - [Erstellen einer SQL-Datenbank mit der Azure CLI](sql-database-get-started-cli.md)
  - [Erstellen einer SQL-Datenbank mithilfe von PowerShell](sql-database-get-started-powershell.md)

- Azure CLI- und PowerShell-Beispiele finden Sie unter:
  - [Azure CLI-Beispiele für SQL-Datenbank](sql-database-cli-samples.md)
  - [Azure PowerShell-Beispiele für SQL-Datenbank](sql-database-powershell-samples.md)
