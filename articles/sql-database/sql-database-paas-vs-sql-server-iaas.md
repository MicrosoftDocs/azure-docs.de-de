---
title: Auswählen einer SQL-Option in Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie sich zwischen den Bereitstellungsoptionen in Azure SQL-Datenbank und zwischen Azure SQL-Datenbank und SQL Server auf virtuellen Azure-Computern entscheiden.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
keywords: SQL Server-Cloud, SQL Server in der Cloud, PaaS-Datenbank, Cloud-SQL Server, DBaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/11/2019
ms.openlocfilehash: d9cd5ba0b697cbf67f943eb49d66010745d8561e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60584851"
---
# <a name="choose-the-right-sql-server-option-in-azure"></a>Auswählen der richtigen SQL Server-Option in Azure

In Azure können Sie Ihre SQL Server-Workloads in einer gehosteten Infrastruktur (IaaS) oder als gehosteten Dienst ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)) ausführen. Bei PaaS stehen Ihnen mehrere Bereitstellungsoptionen und Dienstebenen für jede Bereitstellungsoption zur Verfügung. Dies ist die wichtigste Frage, die Sie sich bei der Entscheidung zwischen PaaS und IaaS stellen müssen: Möchten Sie Ihre Datenbank selbst verwalten, Patches anwenden und Sicherungen ausführen, oder möchten Sie diese Vorgänge an Azure delegieren?
Je nach Antwort stehen folgende Optionen zur Auswahl:

- [Azure SQL-Datenbank](sql-database-technical-overview.md): Eine vollständig verwaltete SQL-Datenbank-Engine, basierend auf der letzten stabilen Enterprise Edition von SQL Server. Dies ist eine relationale DBaaS-Datenbank (Database-as-a-Service), die in der Azure-Cloud gehostet wird, die in die Branchenkategorie *Platform-as-a-Service (PaaS)* fällt. SQL-Datenbank bietet mehrere Bereitstellungsoptionen, die jeweils auf standardisierter Hardware und Software basieren, die Microsoft gehört und von Microsoft gehostet und verwaltet wird. Mit SQL-Datenbank können Sie integrierte Features und Funktionen nutzen, die in SQL Server eine umfangreiche Konfiguration erfordern würden (egal, ob lokal oder auf einem virtuellen Azure-Computer). Wenn Sie mit SQL-Datenbank arbeiten, bezahlen Sie für das, was Sie nutzen, und haben die Möglichkeit, horizontal oder zentral hochzuskalieren oder ohne Unterbrechung mehr Leistung hinzuzufügen. SQL-Datenbank bietet zusätzliche Funktionen, die in SQL Server nicht verfügbar sind, wie zum Beispiel für Hochverfügbarkeit, integrierte Intelligenz und die Verwaltung. Azure SQL-Datenbank bietet die folgenden Bereitstellungsoptionen:
  
  - Als [Singleton](sql-database-single-database.md) mit einem eigenen Ressourcensatz, der über einen SQL-Datenbank-Server verwaltet wird Ein Singleton ähnelt einer [eigenständigen Datenbank](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) in SQL Server. Diese Option ist für die Entwicklung neuer moderner cloudbasierter Anwendungen optimiert.
  - Als [Pool für elastische Datenbanken](sql-database-elastic-pool.md), bei dem es sich um eine Sammlung von Datenbanken mit einem gemeinsamen Ressourcensatz handelt, der über einen SQL-Datenbank-Server verwaltet wird Singletons können in und aus Pools für elastische Datenbanken verschoben werden. Diese Option ist für die Entwicklung neuer für die Cloud ausgelegter Anwendungen optimiert, die die mehrinstanzenfähige SaaS-Anwendung nutzen.
  - Als [verwaltete Instanz](sql-database-managed-instance.md), bei der es sich um eine Sammlung von System- und Benutzerdatenbanken mit einem gemeinsam genutzten Ressourcensatz handelt. Eine verwaltete Instanz ähnelt einer Instanz der [Microsoft SQL Server-Datenbank-Engine], die gemeinsam genutzte Ressourcen für Datenbanken und zusätzliche Funktionen auf Instanzenebene zur Verfügung stellt. Die verwaltete Instanz unterstützt die Datenbankmigration aus lokalen Umgebungen mit minimalen oder sogar ganz ohne Datenbankänderungen. Diese Option bietet alle PaaS-Vorteile von Azure SQL-Datenbank, fügt aber Funktionen hinzu, die bisher nur auf SQL-VMs verfügbar waren. Dies beinhaltet ein natives virtuelles Netzwerk (VNET) und nahezu 100%ige Kompatibilität mit der lokalen SQL Server-Instanz.
- [SQL Server auf Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/) fällt in die Branchenkategorie *Infrastructure-as-a-Service (IaaS)* und ermöglicht es Ihnen, SQL Server auf einem vollständig verwalteten virtuellen Computer in der Azure-Cloud auszuführen. [Virtuelle SQL Server-Computer](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) werden auch auf standardisierter Hardware ausgeführt, die Microsoft gehört und von Microsoft gehostet und verwaltet wird. Bei Verwendung von SQL Server auf einem virtuellen Computer können Sie entweder ein nutzungsbasiertes Zahlungsmodell für eine bereits in einem SQL Server-Image enthaltene SQL Server-Lizenz oder einfach eine vorhandene Lizenz verwenden. Sie können die VM auch nach Bedarf beenden oder fortsetzen. SQL Server wird in der Cloud auf virtuellen Windows Server- oder Linux-Computern in Azure installiert und gehostet – dies wird auch als Infrastructure-as-a-Service (IaaS) bezeichnet. SQL Server auf Azure-VMs ist eine gute Möglichkeit, um lokale SQL Server-Datenbanken und -Anwendungen ohne jegliche Datenbankänderung zu migrieren. Alle aktuellen Versionen und Editionen von SQL Server sind für die Installation in einem virtuellen IaaS-Computer verfügbar. Der wichtigste Unterschied zu SQL-Datenbank besteht darin, dass SQL Server-VMs eine vollständige Steuerung der Datenbank-Engine ermöglichen. Sie können wählen, wann die Wartung bzw. das Patchen beginnt, um das Wiederherstellungsmodell in ein einfaches oder massenprotokolliertes Modell umzuwandeln. Dadurch wird ein schnelleres Laden mit weniger Protokollen ermöglicht, lässt sich die Engine bei Bedarf anhalten oder starten und kann die SQL Server-Datenbank-Engine vollständig angepasst werden. Mit diesen zusätzlichen Steuerungsmöglichkeiten steigt auch die Verantwortung bei der Verwaltung der virtuellen Computer.

Die Hauptunterschiede zwischen diesen Optionen werden in der folgenden Tabelle aufgeführt:

| SQL Server auf virtuellem Computer | Verwaltete Instanz in SQL-Datenbank | Singleton/Pool für elastische Datenbanken in SQL-Datenbank |
| --- | --- | --- |
|Sie haben vollständige Kontrolle über die SQL Server-Engine.<br/>Bis zu 99,95 % Verfügbarkeit.<br/>Vollständige Parität mit der entsprechenden lokalen SQL Server-Version.<br/>Festgelegte, bekannte Datenbank-Engine-Version.<br/>Einfache Migration von lokalen SQL Server-Instanzen.<br/>Private IP-Adresse im virtuellen Azure-Netzwerk.<br/>Sie können Anwendungen oder Dienste auf dem Host bereitstellen, auf dem sich SQL Server befindet.| Ein hohes Maß an Kompatibilität mit lokalen SQL Server-Instanzen.<br/>99,99 % Verfügbarkeit garantiert.<br/>Integrierte Funktionen für Sicherungen, Patching und Wiederherstellung.<br/>Letzte stabile Datenbank-Engine-Version.<br/>Einfache Migration von SQL Server.<br/>Private IP-Adresse im virtuellen Azure-Netzwerk.<br/>Integrierte erweiterte Intelligenz- und Sicherheitsfeatures.<br/>Onlineänderung von Ressourcen (CPU/Speicher).|Es stehen die gängigsten SQL Server-Features zur Verfügung.<br/>99,99 % Verfügbarkeit garantiert.<br/>Integrierte Funktionen für Sicherungen, Patching und Wiederherstellung.<br/>Letzte stabile Datenbank-Engine-Version.<br/>Möglichkeit, einzelnen Datenbanken die erforderlichen Ressourcen (CPU/Speicher) zuzuweisen.<br/>Integrierte erweiterte Intelligenz- und Sicherheitsfeatures.<br/>Onlineänderung von Ressourcen (CPU/Speicher).|
|Sie müssen Ihre Sicherungen und Patches verwalten.<br>Sie müssen Ihre Hochverfügbarkeitslösung selbst implementieren.<br/>Während der Änderung von Ressourcen (CPU/Speicher) treten Ausfallzeiten auf.|Es gibt immer noch eine geringe Anzahl von SQL Server-Features, die nicht verfügbar sind.<br/>Es gibt keine exakte garantierte Wartungszeit (sie ist jedoch nahezu transparent).<br/>Die Kompatibilität mit der SQL Server-Version kann nur über Datenbank-Kompatibilitätsgrade erreicht werden.|Die Migration von SQL Server kann schwierig sein.<br/>Einige SQL Server-Features stehen nicht zur Verfügung.<br/>Es gibt keine exakte garantierte Wartungszeit (sie ist jedoch nahezu transparent).<br/>Die Kompatibilität mit der SQL Server-Version kann nur über Datenbank-Kompatibilitätsgrade erreicht werden.<br/>Private IP-Adressen können nicht zugewiesen werden (Sie können den Zugriff mithilfe von Firewallregeln begrenzen).|

Erfahren Sie, welchen Platz diese Bereitstellungsoptionen in der Microsoft-Datenplattform einnehmen und für welche geschäftlichen Anforderungen sie sich am besten eignen. Ob bei Ihnen Kosteneinsparungen oder minimaler Verwaltungsaufwand Priorität haben – dieser Artikel kann Ihnen dabei helfen, zu entscheiden, welcher Ansatz der richtige ist, um Ihre wichtigsten geschäftlichen Anforderungen zu erfüllen.

## <a name="microsofts-sql-data-platform"></a>Microsoft-SQL-Datenplattform

Eines der ersten Dinge, die in jeder Diskussion von Azure im Vergleich zu lokalen SQL Server-Datenbanken verstanden werden müssen, ist, dass Sie alles verwenden können. Die Microsoft-Datenplattform nutzt SQL Server-Technologie und stellt sie auf unterschiedlichen lokalen physischen Computern, in privaten Cloudumgebungen, in von Drittanbietern gehosteten privaten Cloudumgebungen und in öffentlichen Clouds zur Verfügung. Mit SQL Server auf virtuellen Azure-Computern können Sie dank einer Kombination aus lokalen und cloudbasierten Bereitstellungen verschiedene individuelle Geschäftsanforderungen erfüllen und dabei umgebungsübergreifend auf die gleichen Serverprodukte, Entwicklungstools und Kenntnisse zurückgreifen.

   ![SQL Server-Cloudoptionen: SQL Server auf IaaS oder SQL-Datenbank (SaaS) in der Cloud.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Wie Sie im Diagramm sehen können, kann jedes dieser Angebote durch die Administrationsebene in Bezug auf die Infrastruktur (auf der x-Achse) und den Grad der erreichten Kosteneffizienz durch Datenbankkonsolidierung und Automatisierung (auf der y-Achse) beschrieben werden.

Beim Entwurf einer Anwendung stehen vier grundlegende Optionen für das Hosten des SQL Server-Teils der Anwendung zur Verfügung:

- SQL Server auf nicht virtualisierten physischen Computern
- SQL Server auf lokalen virtualisierten Computern (private Cloud)
- SQL Server in Azure Virtual Machine (öffentliche Cloud von Microsoft)
- Azure SQL-Datenbank (öffentliche Cloud von Microsoft)

In den folgenden Abschnitten erfahren Sie mehr über SQL Server in der öffentlichen Cloud von Microsoft: Azure SQL-Datenbank und SQL Server auf Azure-VMs. Darüber hinaus finden Sie hier Informationen zu allgemeinen betriebswirtschaftlichen Faktoren, die bestimmen, welche Option für Ihre Anwendung am besten geeignet ist.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Genauere Betrachtung von Azure SQL-Datenbank und SQL Server auf Azure Virtual Machines

Im Allgemeinen sind diese beiden SQL-Optionen für unterschiedliche Zwecke optimiert:

- **Azure SQL-Datenbank**

Wurde zur Senkung der Gesamtverwaltungskosten für die Bereitstellung und Verwaltung vieler Datenbanken auf ein absolutes Mindestmaß optimiert. Die laufenden Administrationskosten werden reduziert, weil Sie keine virtuellen Computer, Betriebssysteme oder Datenbanksoftware verwalten müssen. Sie müssen sich nicht um Upgrades, Hochverfügbarkeit oder [Sicherungen](sql-database-automated-backups.md)kümmern. Im Allgemeinen lässt sich mit Azure SQL-Datenbank sich die Anzahl der Datenbanken, die von einer einzelnen IT- oder Entwicklungsressource verwaltet werden können, drastisch steigern. [Pools für elastische Datenbanken](sql-database-elastic-pool.md) unterstützen auch SaaS-Anwendungsarchitekturen mit mehreren Mandanten durch Funktionen wie Mandantenisolation und die Skalierbarkeit zur Kostensenkung durch datenbankübergreifende Ressourcennutzung. [Verwaltete Instanzen](sql-database-managed-instance.md) bieten Unterstützung für instanzbezogene Funktionen, die eine einfache Migration bestehender Anwendungen sowie eine datenbankübergreifende Ressourcennutzung ermöglichen.

- **SQL Server auf virtuellen Azure-Computern**

Für die Migration vorhandener Anwendungen zu Azure bzw. für die Erweiterung vorhandener lokaler Anwendungen auf die Cloud (in Hybridbereitstellungen) optimiert. Darüber hinaus können Sie SQL Server auf einem virtuellen Computer zum Entwickeln und Testen herkömmlicher SQL Server-Anwendungen verwenden. Mit SQL Server auf Azure Virtual Machines verfügen Sie über eine dedizierte Instanz von SQL Server und einen cloudbasierten virtuellen Computer (VM, Virtual Machine) mit vollständigen Administratorrechten. Dies ist die perfekte Lösung, wenn ein Unternehmen bereits IT-Ressourcen verfügbar hat, um die virtuellen Computer zu verwalten. Mithilfe dieser Funktionen können Sie ein maßgeschneidertes System einrichten, das die spezifischen Leistungs- und Verfügbarkeitsanforderungen Ihrer Anwendung erfüllt.

In der folgenden Tabelle sind die wesentlichen Merkmale von SQL-Datenbank und SQL Server auf Azure Virtual Machines zusammengefasst:

| | SQL-Datenbank-Pools für elastische Datenbanken und -Singletons | Verwaltete SQL-Datenbank-Instanzen |Virtuelle Azure-Computer mit SQL Server |
| --- | --- | --- |---|
| **Am besten geeignet für:** |Neue cloudbasierte Anwendungen, für die Sie die neuesten stabilen SQL Server-Funktionen nutzen möchten und zeitliche Einschränkungen in der Entwicklung und im Marketing haben. | Neue Anwendungen oder vorhandene lokale Anwendungen, für die Sie die neuesten stabilen SQL Server-Funktionen nutzen möchten und die mit minimalen Änderungen zur Cloud migriert werden.  | Vorhandene Anwendungen, die eine schnelle Migration zur Cloud mit minimalen oder gar keinen Änderungen erfordern. Schnelle Entwicklungs- und Testszenarien, wenn Sie keine lokale SQL Server-Hardware für andere Zwecke als für den Produktivbetrieb erwerben möchten. |
|  | Teams, die integrierte Hochverfügbarkeit, Notfallwiederherstellung und Upgrades für die Datenbank benötigen. | Es gilt dasselbe wie für Einzel- und Pooldatenbanken in SQL-Datenbank. | Teams, die Hochverfügbarkeit, Notfallwiederherstellung und Patches für SQL Server konfigurieren, optimieren und verwalten können. Dies wird durch einige bereitgestellte Automatisierungsfeatures erheblich vereinfacht. |
|  | Teams, die das zugrunde liegende Betriebssystem und Konfigurationseinstellungen nicht selbst verwalten möchten. | Es gilt dasselbe wie für Einzel- und Pooldatenbanken in SQL-Datenbank. | Sie benötigen eine angepasste Umgebung mit uneingeschränkten Administratorrechten. |
|  | Datenbanken von bis zu 100 TB. | Bis zu 8 TB. | SQL Server-Instanzen mit bis zu 64 TB Speicherplatz. Die Instanz unterstützt beliebig viele Datenbanken. |
| **Kompatibilität** | Unterstützt die meisten lokalen Funktionen auf Datenbankebene. | Unterstützt fast alle Funktionen auf Instanz- und Datenbankebene. | Unterstützt alle lokalen Funktionen. |
| **Ressourcen:** | Sie möchten keine IT-Ressourcen für die Konfiguration und Verwaltung der zugrunde liegenden Infrastruktur aufwenden, sondern sich auf die Anwendungsebene konzentrieren. | Es gilt dasselbe wie für Einzel- und Pooldatenbanken in SQL-Datenbank. | Sie verfügen über einige IT-Ressourcen für die Konfiguration und Verwaltung. Dies wird durch einige bereitgestellte Automatisierungsfeatures erheblich vereinfacht. |
| **Gesamtbetriebskosten:** | Beseitigt Hardwarekosten und reduziert die Verwaltungskosten. | Es gilt dasselbe wie für Einzel- und Pooldatenbanken in SQL-Datenbank. | Eliminiert Hardwarekosten. |
| **Geschäftskontinuität:** |Zusätzlich zu den [integrierten Fehlertoleranz-Infrastrukturfunktionen](sql-database-high-availability.md) stellt Azure SQL-Datenbank zur Verbesserung der Geschäftskontinuität Features wie [automatische Sicherungen](sql-database-automated-backups.md), [Point-in-Time-Wiederherstellung](sql-database-recovery-using-backups.md#point-in-time-restore), [geografische Wiederherstellung](sql-database-recovery-using-backups.md#geo-restore), [aktive Georeplikation](sql-database-active-geo-replication.md) und [Autofailover-Gruppen](sql-database-auto-failover-group.md) bereit. Weitere Informationen finden Sie unter [Übersicht über die Geschäftskontinuität in SQL-Datenbank](sql-database-business-continuity.md). | Wie Einzel- und Pooldatenbanken in SQL-Datenbank, wobei zusätzlich benutzerinitiierte, ausschließlich kopierbare Sicherungen verfügbar sind. | Mit SQL Server auf virtuellen Azure-Computern können Sie eine Hochverfügbarkeits- und Notfallwiederherstellungslösung für die spezifischen Anforderungen Ihrer Datenbank einrichten. Aus diesem Grund haben Sie ein System, das genau für Ihre Anwendung optimiert ist. Sie können bei Bedarf Failovers selbst testen und ausführen. Weitere Informationen finden Sie unter [Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md). |
| **Hybrid Cloud:** |Die lokale Anwendung kann auf Daten in der Azure SQL-Datenbank zugreifen. | [Native Implementierung von und Konnektivität mit virtuellen Netzwerken](sql-database-managed-instance-vnet-configuration.md) in der lokalen Umgebung unter Verwendung von Azure ExpressRoute oder VPN Gateway. | Mit SQL Server auf Azure Virtual Machines können Sie Anwendungen betreiben, die teilweise in der Cloud und teilweise lokal ausgeführt werden. Beispielsweise können Sie Ihr lokales Netzwerk und Ihre Active Directory-Domäne mit [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)in die Cloud ausdehnen. Weitere Informationen zu Hybrid Cloud-Lösungen finden Sie unter [Erweitern lokaler Datenlösungen auf die Cloud](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |
|  | Unterstützt die [SQL Server-Transaktionsreplikation](https://msdn.microsoft.com/library/mt589530.aspx) als Abonnent für die Datenreplikation. | Replikation wird für verwaltete Instanzen als Previewfunktion unterstützt. | Unterstützt uneingeschränkt [SQL Server-Transaktionsreplikation](https://msdn.microsoft.com/library/mt589530.aspx), [AlwaysOn-Verfügbarkeitsgruppen](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), Integrationsdienste und Protokollversand für die Datenreplikation. Auch herkömmliche SQL Server-Sicherungen werden uneingeschränkt unterstützt. |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Entscheidungskriterien für die Wahl zwischen Azure SQL-Datenbank und SQL Server auf Azure Virtual Machines

Es gibt verschiedene Faktoren, die Ihre Entscheidung zwischen PaaS und IaaS zum Hosten Ihrer SQL-Datenbank-Instanzen beeinflussen:

- [Kosten](#cost): Sowohl PaaS als auch IaaS enthalten einen Basispreis, der die zugrunde liegende Infrastruktur und Lizenzierung abdeckt. Bei der IaaS-Option müssen Sie allerdings zusätzlich Zeit und Ressourcen für die Verwaltung Ihrer Datenbank investieren, während diese Verwaltungsfeatures bei PaaS im Preis enthalten sind. Bei der IaaS-Option können Sie zum Einsparen von Kosten Ressourcen herunterfahren, während sie nicht verwendet werden. Bei der PaaS-Option dagegen werden Ressource immer ausgeführt, wenn Sie diese nicht löschen und dann wieder neu erstellen, wenn sie benötigt werden.
- [Verwaltung](#administration): PaaS-Optionen reduzieren die Zeit, die Sie in die Verwaltung der Datenbank investieren müssen. Sie beschränken jedoch auch den Bereich der benutzerdefinierten Verwaltungsaufgaben und Skripts, die Sie ausführen können. Die CLR wird z.B. nicht mit einzelnen oder in einem Pool zusammengefassten Datenbanken unterstützt, aber für eine verwaltete Instanz. Darüber hinaus unterstützen keine Bereitstellungsoptionen in PaaS die Verwendung von Ablaufverfolgungsflags.
- [Vereinbarung zum Servicelevel](#service-level-agreement-sla) (Service-Level Agreement, SLA): Sowohl IaaS als auch PaaS bieten eine hohe SLA nach Branchenstandard. Die PaaS-Option garantiert eine SLA von 99,99 %. IaaS garantiert eine SLA von 99,95 % für die Infrastruktur, was bedeutet, dass Sie zusätzliche Mechanismen implementieren müssen, um die Verfügbarkeit Ihrer Datenbanken sicherzustellen. Wenn Sie eine Hochverfügbarkeitslösung implementieren möchten, die der Garantie von PaaS entspricht, müssen Sie im Extremfall möglicherweise zusätzliche SQL Server-Instanzen auf VMs erstellen und AlwaysOn-Verfügbarkeitsgruppen konfigurieren, wodurch sich die Kosten Ihrer Datenbank verdoppeln könnten.
- [Zeitraum für die Verschiebung zu Azure](#market): SQL Server-Instanzen auf virtuellen Azure-Computern sind eine exakte Entsprechung Ihrer Umgebung – die Migration von der lokalen Umgebung zu virtuellen Azure-SQL-Computern unterscheidet sich daher nicht von der Verschiebung der Datenbanken von einem lokalen Server auf einen anderen. Verwaltete Instanzen ermöglichen ebenfalls eine sehr einfache Migration. Allerdings müssen Sie möglicherweise einige Änderungen vornehmen, bevor Sie die Migration zu verwalteten Instanzen durchführen können.

Diese Faktoren werden in den folgenden Abschnitten detailliert erläutert.

### <a name="cost"></a>Kosten

Ob Sie ein Startup-Unternehmen mit wenig liquiden Mitteln oder ein Team in einem etablierten Unternehmen sind, das unter engen Budgetbeschränkungen arbeitet: häufig sind begrenzte Mittel die primäre Motivation bei der Entscheidung, wie Sie Ihre Datenbanken hosten. Dieser Abschnitt enthält grundlegende Informationen zur Abrechnung und Lizenzierung in Azure in Verbindung mit den folgenden Optionen für relationale Datenbanken: SQL-Datenbank und SQL Server auf Azure-VMs. Außerdem erfahren Sie hier, wie Sie die Gesamtkosten der Anwendung berechnen.

#### <a name="billing-and-licensing-basics"></a>Abrechnungs- und Lizenzierungsgrundlagen

Derzeit sind für den Dienst **SQL-Datenbank** verschiedene Bereitstellungsoptionen und Dienstebenen mit unterschiedlichen Preisen für Ressourcen verfügbar, die nach einem festen Stundensatz je nach ausgewählter Dienstebene und Computegröße berechnet werden. Die neuesten Informationen zu aktuell unterstützten Dienstebenen, Computegrößen und Speichermengen finden Sie unter [DTU-basiertes Kaufmodell für Azure SQL-Datenbank](sql-database-service-tiers-dtu.md) und [vCore-basiertes Kaufmodell](sql-database-service-tiers-vcore.md).

- Bei SQL-Einzeldatenbanken können Sie aus einer Vielzahl von Tarifen – ab einem Preis von 5 USD pro Monat für „Basic“ – die Dienstebene auswählen, die Ihre Anforderungen erfüllt.
- Sie können [Pools für elastische Datenbanken](sql-database-elastic-pool.md) erstellen, um Ressourcen zwischen Datenbankinstanzen freizugeben, um Kosten zu senken und die Auslastung anzupassen.
- Mit einer verwalteten SQL-Datenbank-Instanz können Sie zudem Ihre eigene Lizenz verwenden. Weitere Informationen über das Verwenden eigener Lizenzen finden Sie unter [Lizenzmobilität durch Software Assurance für Azure](https://azure.microsoft.com/pricing/license-mobility/). Sie können auch den [Einsparungsrechner für den Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) verwenden, um zu ermitteln, wie Sie **bis zu 40 %** sparen können.

Außerdem wird Ihnen der ausgehende Internetdatenverkehr basierend auf den üblichen [Datenübertragungsraten](https://azure.microsoft.com/pricing/details/data-transfers/)berechnet. Sie können Dienstebenen und Computegrößen entsprechend den unterschiedlichen Durchsatzanforderungen Ihrer Anwendung dynamisch anpassen.

Bei **SQL-Datenbank** wird die Datenbanksoftware automatisch von Microsoft konfiguriert, gepatcht und aktualisiert. Dadurch sinken Ihre Verwaltungskosten. Darüber hinaus helfen Ihnen die [integrierten Datensicherungsfunktionen](sql-database-automated-backups.md) dabei, erhebliche Kosteneinsparungen zu erzielen, vor allem bei einer großen Anzahl von Datenbanken.

Mit **SQL Server auf virtuellen Azure-Computern** können Sie entweder eines der über die Plattform bereitgestellten SQL Server-Images (mit enthaltener Lizenz) oder aber Ihre eigene SQL Server-Lizenz verwenden. Alle unterstützten SQL Server-Versionen (2008R2, 2012, 2014, 2016) und Editionen (Developer, Express, Web, Standard, Enterprise) stehen zur Verfügung. Darüber hinaus sind BYOL-Versionen (Bring Your Own License) der Images verfügbar. Bei Verwendung der von Azure bereitgestellten Images hängen die Betriebskosten von der Größe des virtuellen Computers und von der gewählten SQL Server-Edition ab. Unabhängig von der Größe des virtuellen Computers und der SQL Server-Edition bezahlen Sie einen minutenbezogenen Lizenzpreis für SQL Server sowie für den Windows- oder Linux-Server. Hinzu kommt noch der Azure Storage-Preis für die VM-Datenträger. Mit der minutenbezogenen Abrechnungsoption können Sie SQL Server solange wie nötig verwenden, ohne zusätzliche SQL Server-Lizenzen zu erwerben. Wenn Sie eine eigene SQL Server-Lizenz in Azure einbringen, werden Ihnen nur Server- und Speicherkosten in Rechnung gestellt. Weitere Informationen über das Einbringen eigener Lizenzen finden Sie unter [Lizenzmobilität durch Software Assurance für Azure](https://azure.microsoft.com/pricing/license-mobility/). Außerdem wird Ihnen der ausgehende Internetdatenverkehr basierend auf den üblichen [Datenübertragungsraten](https://azure.microsoft.com/pricing/details/data-transfers/)berechnet.

#### <a name="calculating-the-total-application-cost"></a>Berechnen der gesamten Anwendungskosten

Wenn Sie erstmals eine Cloud-Plattform verwenden, enthalten die Kosten der Anwendungsausführung die Kosten für Neuentwicklung und fortlaufende Verwaltung sowie die Dienstkosten für die öffentliche Cloud-Plattform.

**Bei Verwendung von Azure SQL-Datenbank:**

- Minimalste Verwaltungskosten
- Begrenzte Entwicklungskosten für migrierte Anwendungen (verwaltete Instanzen)
- Kosten für SQL-Datenbank-Dienst
- Keine Anschaffungskosten für Hardware

**Bei Verwendung von SQL Server auf Azure Virtual Machines:**

- Höhere Verwaltungskosten
- Begrenzte oder sogar keine Entwicklungskosten für migrierte Anwendungen
- Kosten für den Dienst „Virtueller Computer“
- Keine Anschaffungskosten für Hardware

Weitere Informationen zur Preisberechnung finden Sie in den folgenden Ressourcen:

- [SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-database/)
- [Virtual Machines – Preise](https://azure.microsoft.com/pricing/details/virtual-machines/) für [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) und für [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Verwaltung

Bei vielen Unternehmen wird die Entscheidung zum Wechsel auf einen Clouddienst genauso stark vom Bestreben um eine Vereinfachung der Verwaltung wie von den erhofften Kostenvorteilen getrieben. Mit IaaS und PaaS verwaltet Microsoft die zugrunde liegende Infrastruktur und repliziert automatisch alle Daten für eine Notfallwiederherstellung, konfiguriert und aktualisiert die Datenbanksoftware, verwaltet den Lastenausgleich und führt bei einem Serverausfall ein transparentes Failover innerhalb eines Rechenzentrums durch.

- Mit **Azure SQL-Datenbank** können Sie sich weiterhin um die Verwaltung Ihrer Datenbank kümmern, müssen aber die Datenbank-Engine, das Betriebssystem und die Hardware nicht mehr verwalten. Weiterhin verwalten können Sie z. B. die Datenbanken und Anmeldungen, die Index- und Abfrageoptimierung, die Überwachung und die Sicherheit. Darüber hinaus erfordert die Konfiguration der Hochverfügbarkeit für ein anderes Rechenzentrum einen minimalen Konfigurations- und Verwaltungsaufwand.
- Bei **SQL Server auf virtuellen Azure-Computern**haben Sie die uneingeschränkte Kontrolle über das Betriebssystem und die Konfiguration der SQL Server-Instanzen. Bei einem virtuellen Computer können Sie entscheiden, wann das Betriebssystem und die Datenbanksoftware aktualisiert und wann zusätzliche Programme wie etwa Antivirentools installiert werden sollen. Durch einige bereitgestellte Automatisierungsfeatures lassen sich Patching, Sicherungen und Hochverfügbarkeit erheblich vereinfachen. Darüber hinaus können Sie die Größe des virtuellen Computers, die Anzahl der Laufwerke und deren Speicherkonfigurationen steuern. Mit Azure lässt sich die Größe eines virtuellen Computers nach Bedarf anpassen. Weitere Informationen finden Sie unter [Größen von virtuellen Computern und Clouddiensten für Azure](../virtual-machines/windows/sizes.md).

### <a name="service-level-agreement-sla"></a>Vereinbarung zum Servicelevel (SLA)

Für viele IT-Abteilungen hat die Einhaltung vereinbarter Betriebszeiten und Servicelevels höchste Priorität. In diesem Abschnitt wird erläutert, welche Vereinbarung zum Servicelevel (SLA) für die jeweilige Datenbank-Hostingoption gilt.

Für **SQL-Datenbank** bietet Microsoft eine Verfügbarkeits-SLA von 99,99%. Aktuelle Informationen finden Sie unter [Vereinbarung zum Servicelevel](https://azure.microsoft.com/support/legal/sla/sql-database/).

Für **SQL Server auf Azure Virtual Machines** bietet Microsoft eine Verfügbarkeits-SLA von 99,95%, die nur den virtuellen Computer abdeckt. Diese SLA deckt nicht die Prozesse ab, die auf dem virtuellen Computer ausgeführt werden (z. B. SQL Server). Außerdem erfordert sie, dass mindestens zwei VM-Instanzen in einer Verfügbarkeitsgruppe gehostet werden. Aktuelle Informationen finden Sie unter [SLA für Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Für eine besonders hohe Datenbankverfügbarkeit auf den virtuellen Computern sollten Sie eine der unterstützten Hochverfügbarkeitsoptionen in SQL Server konfigurieren, wie z.B. [AlwaysOn-Verfügbarkeitsgruppen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Durch die Verwendung einer Hochverfügbarkeitsoption erhalten Sie zwar keine zusätzliche SLA, können aber eine Datenbankverfügbarkeit von >99,99 Prozent erreichen.

### <a name="market"></a>Zeit für einen Wechsel nach Azure

**SQL-Datenbank mit Einzeldatenbanken oder Pools für elastische Datenbanken** ist die geeignete Lösung für cloudbasierte Anwendungen, wenn Entwicklerproduktivität und eine schnelle Markteinführung für neue Lösungen entscheidend sind. Durch programmgesteuerte DBA-ähnliche Funktionen ist sie perfekt für Cloud-Architekten und Entwickler, da sie den Verwaltungsaufwand für das zugrunde liegende Betriebssystem und die Datenbank verringert.

**Verwaltete SQL-Datenbank-Instanzen** vereinfachen die Migration bestehender Anwendungen zu Azure SQL-Datenbank erheblich, sodass Sie eine migrierte Datenbankanwendung in Azure schnell auf den Markt bringen können.

**SQL Server auf virtuellen Azure-Computern** eignet sich hervorragend, wenn Ihre bereits vorhandenen oder neuen Anwendungen große Datenbanken oder Zugriff auf sämtliche Features in SQL Server oder Windows/Linux benötigen und Sie Zeit und Kosten für den Erwerb neuer lokaler Hardware sparen möchten. Diese Option ist auch eine gute Wahl, wenn Sie vorhandene lokale Anwendungen und Datenbanken unverändert zu Azure migrieren möchten – in Fällen, in denen sich die verwaltete Azure SQL-Datenbank-Instanz nicht gut eignet. Da Sie keine Änderungen an der Präsentations-, Anwendungs- und Datenschicht vornehmen müssen, sparen Sie sich die Zeit und das Geld, die sonst für den Neuentwurf der vorhandenen Lösung nötig wären. Sie können in diesem Fall alle Lösungen sofort zu Azure migrieren und müssen lediglich einige Leistungsoptimierungen durchführen, die möglicherweise von der Azure-Plattform verlangt werden. Weitere Informationen finden Sie unter [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu den ersten Schritten für SQL-Datenbank finden Sie unter [Erstellen einer ersten Azure SQL-Datenbank](sql-database-single-database-get-started.md).
- Informationen finden Sie unter [Preise für SQL-Datenbank](https://azure.microsoft.com/pricing/details/sql-database/).
- Informationen zu den ersten Schritten mit SQL Server auf virtuellen Azure-Computern finden Sie unter [Bereitstellen eines virtuellen Computers mit SQL Server in Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) .
