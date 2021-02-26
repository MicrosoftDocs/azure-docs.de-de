---
title: Was ist Azure SQL?
description: 'Erfahren Sie mehr über die verschiedenen Optionen in der Azure SQL-Dienstfamilie: Azure SQL-Datenbank, Azure SQL Managed Instance und SQL Server auf einer Azure-VM.'
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: overview
keywords: SQL Server-Cloud, SQL Server in der Cloud, PaaS-Datenbank, Cloud-SQL Server, DBaaS, IaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/27/2020
ms.openlocfilehash: 7f267b4909817e89444883403bef6662771332ad
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550877"
---
# <a name="what-is-azure-sql"></a>Was ist Azure SQL? 
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Azure SQL ist eine Familie verwalteter, sicherer und intelligenter Produkte, die die SQL Server-Datenbank-Engine in der Azure-Cloud nutzen.

- **Azure SQL-Datenbank**: Unterstützen Sie moderne Cloudanwendungen mit einem intelligenten, verwalteten Datenbankdienst, der serverloses Compute einschließt. 
- **Azure SQL Managed Instance**: Modernisieren Sie Ihre vorhandenen SQL Server-Anwendungen nach Maß mit einer intelligenten, vollständig verwalteten Instanz als Dienst mit nahezu 100 %-iger Featureparität mit der SQL Server-Datenbank-Engine. Ideal für die meisten Migrationen in die Cloud.
- **SQL Server auf virtuellen Azure-Computern**: Migrieren Sie Ihre SQL Server-Workloads per Lift & Shift, und sichern Sie sich 100 %-ige SQL Server-Kompatibilität und Zugriff auf Betriebssystemebene. 
 
Azure SQL baut auf der vertrauten SQL Server-Engine auf, sodass Sie Anwendungen problemlos migrieren und weiterhin die Tools, Sprachen und Ressourcen nutzen können, die Ihnen vertraut sind. Ihre Kompetenzen und Erfahrungen werden in die Cloud übertragen, sodass Sie mit der vorhandenen Infrastruktur noch mehr erreichen können. 

Erfahren Sie, wie jedes Produkt such in die Azure SQL-Datenplattform von Microsoft einfügt, um die richtige Wahl für Ihre Geschäftsanforderungen zu treffen. Ob bei Ihnen Kosteneinsparungen oder minimaler Verwaltungsaufwand Priorität haben – dieser Artikel kann Ihnen dabei helfen, zu entscheiden, welcher Ansatz der richtige ist, um die wichtigsten Anforderungen Ihres Unternehmens zu erfüllen.


Wenn Sie noch nicht mit Azure SQL vertraut sind, sehen Sie sich das Video mit den *Infos zu Azure SQL* aus der detaillierten [Azure SQL-Videoserie](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner) an:
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/What-is-Azure-SQL-3-of-61/player]



## <a name="overview"></a>Übersicht

In der heutigen datengesteuerten Welt hängt das Vorantreiben der digitalen Transformation zunehmend von unserer Fähigkeit ab, riesige Datenmengen zu verwalten und ihr Potenzial auszuschöpfen. Aber die heutigen Datenbestände werden immer komplexer, da die Daten lokal, in der Cloud oder am Netzwerk-Edge gehostet werden. Entwickler, die intelligente und überzeugende Anwendungen realisieren, können sich durch Einschränkungen ausgebremst fühlen, die sich letztlich auf ihre Benutzererfahrung auswirken können. Einschränkungen, die sich aus inkompatiblen Plattformen, mangelnder Datensicherheit, unzureichenden Ressourcen und Preis-Leistungs-Hürden ergeben, führen zu Komplexität, die die Modernisierung und Entwicklung von Apps behindern kann. 

Eines der ersten Dinge, die in jeder Diskussion von Azure im Vergleich zu lokalen SQL Server-Datenbanken verstanden werden müssen, ist, dass Sie alles verwenden können. Die Microsoft-Datenplattform nutzt SQL Server-Technologie und stellt sie auf unterschiedlichen lokalen physischen Computern, in privaten Cloudumgebungen, in von Drittanbietern gehosteten privaten Cloudumgebungen und in der öffentlichen Cloud zur Verfügung. 


### <a name="fully-managed-and-always-up-to-date"></a>Vollständig verwaltet und immer auf dem neuesten Stand 

Verbringen Sie mehr Zeit mit Innovationen und weniger Zeit mit dem Patchen, Aktualisieren und Sichern Ihrer Datenbanken. Azure ist die einzige Cloud mit dem Dauerbrenner SQL Server, die automatisch die neuesten Aktualisierungen und Patches anwendet, sodass Ihre Datenbanken immer auf dem neuesten Stand sind, wodurch Probleme aufgrund von auslaufendem Support vermieden werden. Selbst komplexe Aufgaben wie Leistungsoptimierung, Hochverfügbarkeit, Notfallwiederherstellung und Sicherungen sind automatisiert, sodass Sie sich ganz auf Anwendungen konzentrieren können.  

### <a name="protect-your-data-with-built-in-intelligent-security"></a>Schützen Ihrer Daten mit integrierter intelligenter Sicherheit 

Azure überwacht Ihre Daten ständig auf Bedrohungen. Azure SQL bietet folgende Möglichkeiten:

- Beseitigen Sie potenzielle Bedrohungen in Echtzeit durch intelligente [erweiterte Bedrohungserkennung](../security/fundamentals/threat-detection.md#threat-protection-features-other-azure-services) und proaktive Warnungen zur Beurteilung von Sicherheitsrisiken. 
- Profitieren Sie von branchenweit führendem, mehrschichtigem Schutz mit [integrierten Sicherheitskontrollen](https://azure.microsoft.com/overview/security/) einschließlich T-SQL, Authentifizierung, Netzwerk und Schlüsselverwaltung. 
- Kommen Sie in den Genuss der umfassendsten [Konformität](https://azure.microsoft.com/overview/trusted-cloud/compliance/) aller Datenbankdienste in der Cloud. 


### <a name="business-motivations"></a>Geschäftliche Beweggründe

Es gibt verschiedene Faktoren, die Ihre Entscheidung für eines der verschiedenen Datenangebote beeinflussen:

- [Kosten](#cost): Sowohl PaaS als auch IaaS sind mit einem Grundpreis ausgestattet, der die zugrunde liegende Infrastruktur und Lizenzierung abdeckt. Bei der IaaS-Option müssen Sie allerdings zusätzlich Zeit und Ressourcen für die Verwaltung Ihrer Datenbank investieren, während diese Verwaltungsfunktionen bei PaaS im Preis inbegriffen sind. IaaS ermöglicht Ihnen, Ressourcen zur Kostensenkung herunterzufahren, wenn Sie sie nicht nutzen. PaaS hingegen läuft immer, es sei denn, Sie entfernen Ihre Ressourcen und erstellen sie neu, wenn sie benötigt werden.
- [Verwaltung:](#administration) PaaS-Optionen verringern die Zeit, die Sie in die Verwaltung der Datenbank investieren müssen. Sie beschränken jedoch auch den Bereich der benutzerdefinierten Verwaltungsaufgaben und Skripts, die Sie ausführen können. Die CLR wird z. B. nicht mit SQL-Datenbank unterstützt, jedoch für eine Instanz von SQL Managed Instance. Darüber hinaus unterstützen keine Bereitstellungsoptionen in PaaS die Verwendung von Ablaufverfolgungsflags.
- [Vereinbarung zum Servicelevel:](#service-level-agreement-sla) Sowohl IaaS als auch PaaS bieten eine hohe SLA nach Branchenstandard. Die PaaS-Option garantiert eine SLA von 99,99 %. IaaS garantiert eine SLA von 99,95 % für die Infrastruktur, was bedeutet, dass Sie zusätzliche Mechanismen implementieren müssen, um die Verfügbarkeit Ihrer Datenbanken sicherzustellen. Sie können eine SLA mit 99,99 % erreichen, indem Sie eine zusätzliche SQL-VM erstellen und die Hochverfügbarkeitslösung „SQL Server Always On-Verfügbarkeitsgruppe“ implementieren. 
- [Zeit für einen Wechsel nach Azure:](#market) SQL Server auf Azure-VM ist eine genaue Entsprechung Ihrer Umgebung. Die Migration von der lokalen Umgebung zur Azure-VM unterscheidet sich daher nicht vom Verschieben der Datenbanken von einem lokalen Server auf einen anderen. SQL Managed Instance ermöglicht ebenfalls eine sehr einfache Migration. Allerdings müssen Sie möglicherweise vor der Migration einige Änderungen vornehmen. 


## <a name="service-comparison"></a>Dienstvergleich

   ![SQL Server-Cloudoptionen: SQL Server auf IaaS oder SQL-Datenbank (SaaS) in der Cloud.](./media/azure-sql-iaas-vs-paas-what-is-overview/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Wie Sie im Diagramm sehen können, kann jedes dieser Dienstangebote durch die Verwaltungsebene in Bezug auf die Infrastruktur und den Grad der erreichten Kosteneffizienz beschrieben werden.

In Azure können Sie Ihre SQL Server-Workloads als gehostete Dienste ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)) oder als gehostete Infrastruktur ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) ausführen. Bei PaaS stehen Ihnen für jede Option mehrere Produktoptionen und Dienstebenen zur Verfügung. Dies ist die wichtigste Frage, die Sie sich bei der Wahl von PaaS oder IaaS stellen müssen: Möchten Sie Ihre Datenbank selbst verwalten, Patches anwenden und Sicherungen ausführen, oder möchten Sie diese Vorgänge an Azure delegieren?

### <a name="azure-sql-database"></a>Azure SQL-Datenbank

[Azure SQL-Datenbank](database/sql-database-paas-overview.md) ist eine relationale DBaaS-Lösung (Database-as-a-Service), die in Azure gehostet wird und zur Branchenkategorie *Platform as a Service (PaaS)* gehört. 
- Ideal für moderne cloudbasierte Anwendungen, für die Sie die neuesten stabilen SQL Server-Funktionen nutzen möchten und zeitliche Einschränkungen in der Entwicklung und im Marketing haben. 
- Eine vollständig verwaltete SQL Server-Datenbank-Engine, basierend auf der letzten stabilen Enterprise Edition von SQL Server. SQL-Datenbank bietet zwei Bereitstellungsoptionen auf der Grundlage standardisierter Hardware und Software, die Microsoft gehört und von Microsoft gehostet und verwaltet wird. 

Mit SQL Server können Sie integrierte Features und Funktionen nutzen, die eine umfangreiche Konfiguration erfordern (egal, ob lokal oder auf einem virtuellen Azure-Computer). Wenn Sie mit SQL-Datenbank arbeiten, bezahlen Sie für das, was Sie nutzen, und haben die Möglichkeit, auf- oder hochzuskalieren oder ohne Unterbrechung mehr Leistung hinzuzufügen. SQL-Datenbank bietet zusätzliche Funktionen, die in SQL Server nicht verfügbar sind, zum Beispiel für Hochverfügbarkeit, integrierte Business Intelligence und die Verwaltung.


Azure SQL-Datenbank bietet die folgenden Bereitstellungsoptionen:
  - Als [*Einzeldatenbank*](database/single-database-overview.md) mit eigenem Ressourcensatz, die über einen [logischen SQL-Server](database/logical-servers.md) verwaltet wird. Ein Singleton ähnelt einer [eigenständigen Datenbank](/sql/relational-databases/databases/contained-databases) in SQL Server. Diese Option ist für die Entwicklung neuer moderner cloudbasierter Anwendungen optimiert. [Hyperscale](database/service-tier-hyperscale.md) und [serverlose](database/serverless-tier-overview.md) Optionen sind verfügbar.
  - Als [*Pool für elastische Datenbanken*](database/elastic-pool-overview.md), d. h. eine Sammlung von Datenbanken mit einem gemeinsamen Ressourcensatz, die über eine [logische SQL Server-Instanz](database/logical-servers.md) verwaltet wird. Singletons können in und aus Pools für elastische Datenbanken verschoben werden. Diese Option ist für die Entwicklung neuern für die Cloud ausgelegter Anwendungen optimiert, die die mehrinstanzenfähige SaaS-Anwendungsmuster nutzen. Pools für elastische Datenbanken stellen eine einfache und wirtschaftliche Lösung zum Erreichen der Leistungsziele mehrerer Datenbanken dar, die unterschiedliche Nutzungsmuster aufweisen.

### <a name="azure-sql-managed-instance"></a>Verwaltete Azure SQL-Instanz

[Azure SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md) gehört zur Branchenkategorie *Platform as a Service (PaaS)* und eignet sich für die meisten Migrationsvorgänge in die Cloud am besten. Bei SQL Managed Instance handelt es sich um eine Lift & Shift-fähige Sammlung von System- und Benutzerdatenbanken mit einem gemeinsam genutzten Ressourcensatz.  
- Ideal für neue Anwendungen oder vorhandene lokale Anwendungen, für die Sie die neuesten stabilen SQL Server-Funktionen nutzen möchten und die mit minimalen Änderungen in die Cloud migriert werden sollen. Eine SQL Managed Instance-Instanz ähnelt einer Instanz der [Microsoft SQL Server-Datenbank-Engine](/sql/database-engine/sql-server-database-engine-overview), die gemeinsam genutzte Ressourcen für Datenbanken und zusätzliche Funktionen auf Instanzebene zur Verfügung stellt. 
- SQL Managed Instance unterstützt Datenbankmigration von lokalen Standorten mit nur minimalen oder ganz ohne Datenbankänderungen. Diese Option bietet alle PaaS-Vorteile von Azure SQL-Datenbank, fügt aber Funktionen hinzu, die bisher nur in SQL Server-VMs verfügbar waren. Dies schließt ein natives virtuelles Netzwerk (VNET) und Kompatibilität mit der lokalen SQL Server-Instanz nahe 100 % ein. SQL Managed Instance-Instanzen bieten vollständigen Zugriff auf SQL Server und sind mit der Migration von SQL Server-Instanzen zu Azure kompatibel.

### <a name="sql-server-on-azure-vm"></a>SQL Server auf Azure-VMs

[SQL Server auf Azure-VM](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) gehört zur Branchenkategorie *Infrastructure-as-a-Service (IaaS)* und ermöglicht Ihnen, SQL Server auf einem vollständig verwalteten virtuellen Computer (VM) in Azure auszuführen. 
- Am besten geeignet für Migrationen und Anwendungen, die Zugriff auf Betriebssystemebene erfordern. SQL-VMs in Azure sind Lift & Shift-fähig für vorhandene Anwendungen, die eine schnelle Migration in die Cloud mit minimalen oder ganz ohne Änderungen erfordern. Virtuelle SQL-Computer bieten für die Migration in Azure eine umfassende administrative Kontrolle über die SQL Server-Instanz und das zugrunde liegende Betriebssystem. 
- Schnelle Entwicklungs- und Testszenarien, wenn Sie keine lokale SQL Server-Hardware für andere Zwecke als für den Produktivbetrieb erwerben möchten. Virtuelle SQL-Computer werden auch auf standardisierter Hardware ausgeführt, die Microsoft gehört und von Microsoft gehostet und verwaltet wird. Bei Verwendung von virtuellen SQL-Computern können Sie entweder ein nutzungsbasiertes Zahlungsmodell für eine bereits in einem SQL Server-Image enthaltene SQL Server-Lizenz oder einfach eine vorhandene Lizenz verwenden. Zudem können Sie die VM bei Bedarf beenden oder fortsetzen. 
- Wenn SQL Server in der Cloud installiert und gehostet wird, wird es auf virtuellen Windows Server- oder Linux-Computern in Azure ausgeführt. Dies wird auch als Infrastructure-as-a-Service (IaaS) bezeichnet. SQL-VMs sind eine gute Möglichkeit, lokale SQL Server-Datenbanken und -Anwendungen ohne jegliche Datenbankänderung zu migrieren. Alle aktuellen Versionen und Editionen von SQL Server sind für die Installation in einem virtuellen IaaS-Computer verfügbar. 

    Der wichtigste Unterschied zu SQL-Datenbank und SQL Managed Instance besteht darin, dass SQL Server in Azure Virtual Machines eine vollständige Kontrolle über die Datenbank-Engine ermöglicht. Sie können wählen, wann die Wartung bzw. das Patchen beginnen soll, das Wiederherstellungsmodell in ein einfaches oder massenprotokolliertes Modell umwandeln und den Dienst bei Bedarf anhalten oder starten. Darüber hinaus kann die SQL Server-Datenbank-Engine vollständig angepasst werden. Mit diesen zusätzlichen Steuerungsmöglichkeiten steigt auch die Verantwortung bei der Verwaltung der virtuellen Computer.
- Für die Migration vorhandener Anwendungen zu Azure bzw. für die Erweiterung vorhandener lokaler Anwendungen auf die Cloud (in Hybridbereitstellungen) optimiert. Darüber hinaus können Sie SQL Server auf einem virtuellen Computer zum Entwickeln und Testen herkömmlicher SQL Server-Anwendungen verwenden. Mit virtuellen SQL-Computern verfügen Sie über eine dedizierte Instanz von SQL Server und einen cloudbasierten virtuellen Computer mit vollständigen Administratorrechten. Dies ist die perfekte Lösung, wenn ein Unternehmen bereits IT-Ressourcen verfügbar hat, um die virtuellen Computer zu verwalten. Mithilfe dieser Funktionen können Sie ein maßgeschneidertes System einrichten, das die spezifischen Leistungs- und Verfügbarkeitsanforderungen Ihrer Anwendung erfüllt.

In der folgenden Tabelle sind weitere Unterschiede aufgeführt, *aber sowohl SQL-Datenbank als auch SQL Managed Instance sind so optimiert, dass die Verwaltungskosten für die Bereitstellung und Verwaltung vieler Datenbanken auf ein Minimum reduziert werden.* Laufende Verwaltungskosten werden reduziert, da Sie keine virtuellen Computer, Betriebssysteme oder Datenbanksoftware verwalten müssen. Sie müssen sich nicht um Upgrades, Hochverfügbarkeit oder [Sicherungen](database/automated-backups-overview.md)kümmern. 

Im Allgemeinen lässt sich mit SQL-Datenbank und SQL Managed Instance die Anzahl der Datenbanken, die von einem einzelnen IT- oder Entwicklungsmitarbeiter verwaltet werden können, drastisch steigern. [Pools für elastische Datenbanken](database/elastic-pool-overview.md) unterstützen auch SaaS-Anwendungsarchitekturen mit mehreren Mandanten durch Funktionen wie Mandantenisolation und die Skalierbarkeit zur Kostensenkung durch datenbankübergreifende Ressourcennutzung. [SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md) bietet Unterstützung für instanzbezogene Funktionen und ermöglichen somit eine einfache Migration bestehender Anwendungen und eine datenbankübergreifende Ressourcennutzung.

### <a name="comparison-table"></a>Vergleichstabelle

| Azure SQL-Datenbank | Verwaltete Azure SQL-Instanz | SQL Server auf Azure-VMs |
| :--- | :--- | :--- |
|Unterstützt die meisten lokalen Funktionen auf Datenbankebene. Es stehen die gängigsten SQL Server-Features zur Verfügung.<br/>99,995 % Verfügbarkeit garantiert.<br/>Integrierte Funktionen für Sicherungen, Patching und Wiederherstellung.<br/>Letzte stabile Datenbank-Engine-Version.<br/>Möglichkeit, einzelnen Datenbanken die erforderlichen Ressourcen (CPU/Speicher) zuzuweisen.<br/>Integrierte erweiterte Intelligenz- und Sicherheitsfeatures.<br/>Onlineänderung von Ressourcen (CPU/Speicher).| Unterstützt fast alle Funktionen auf Instanz- und Datenbankebene. Hohe Kompatibilität mit SQL Server.<br/>99,99 % Verfügbarkeit garantiert.<br/>Integrierte Funktionen für Sicherungen, Patching und Wiederherstellung.<br/>Letzte stabile Datenbank-Engine-Version.<br/>Einfache Migration von SQL Server.<br/>Private IP-Adresse im virtuellen Azure-Netzwerk.<br/>Integrierte erweiterte Intelligenz- und Sicherheitsfeatures.<br/>Onlineänderung von Ressourcen (CPU/Speicher).| Sie haben vollständige Kontrolle über die SQL Server-Engine. Unterstützt alle lokalen Funktionen.<br/>Verfügbarkeit von bis zu 99,99%.<br/>Vollständige Parität mit der entsprechenden lokalen SQL Server-Version.<br/>Festgelegte, bekannte Datenbank-Engine-Version.<br/>Einfache Migration von SQL Server.<br/>Private IP-Adresse im virtuellen Azure-Netzwerk.<br/>Sie können Anwendungen oder Dienste auf dem Host bereitstellen, auf dem sich SQL Server befindet.|
|Eine von SQL Server ausgehende Migration kann schwierig sein.<br/>Einige SQL Server-Features stehen nicht zur Verfügung.<br/>Es gibt keine exakte garantierte Wartungszeit (sie ist jedoch nahezu transparent).<br/>Die Kompatibilität mit der SQL Server-Version kann nur über Datenbank-Kompatibilitätsgrade erreicht werden.<br/>Unterstützung für private IP-Adressen mit [Azure Private Link](database/private-endpoint-overview.md).|Es gibt immer noch eine geringe Anzahl von SQL Server-Features, die nicht verfügbar sind.<br/>Es gibt keine exakte garantierte Wartungszeit (sie ist jedoch nahezu transparent).<br/>Die Kompatibilität mit der SQL Server-Version kann nur über Datenbank-Kompatibilitätsgrade erreicht werden.|Sie müssen Ihre Sicherungen und Patches verwalten.<br>Sie müssen Ihre Hochverfügbarkeitslösung selbst implementieren.<br/>Während der Änderung von Ressourcen (CPU/Speicher) treten Ausfallzeiten auf.|
| Datenbanken von bis zu 100 TB. | Bis zu 8 TB. | SQL Server-Instanzen mit bis zu 256 TB Speicherplatz. Die Instanz unterstützt beliebig viele Datenbanken. |
| Die lokale Anwendung kann auf Daten in der Azure SQL-Datenbank zugreifen. | [Native Implementierung von und Konnektivität mit virtuellen Netzwerken](managed-instance/vnet-existing-add-subnet.md) in der lokalen Umgebung unter Verwendung von Azure ExpressRoute oder VPN Gateway. | Mit virtuellen SQL-Computern können Sie Anwendungen betreiben, die teilweise in der Cloud und teilweise lokal ausgeführt werden. Beispielsweise können Sie Ihr lokales Netzwerk und Ihre Active Directory-Domäne mit [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)in die Cloud ausdehnen. Weitere Informationen zu Hybrid Cloud-Lösungen finden Sie unter [Erweitern lokaler Datenlösungen auf die Cloud](/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |


## <a name="cost"></a>Kosten

Ob Sie ein Startup-Unternehmen mit wenig liquiden Mitteln oder ein Team in einem etablierten Unternehmen sind, das unter engen Budgetbeschränkungen arbeitet: häufig sind begrenzte Mittel die primäre Motivation bei der Entscheidung, wie Sie Ihre Datenbanken hosten. In diesem Abschnitt lernen Sie die Grundlagen zu Abrechnung und Lizenzierung in Azure im Zusammenhang mit der Azure SQL-Dienstfamilie kennen.  Außerdem erfahren Sie hier, wie Sie die Gesamtkosten der Anwendung berechnen.

### <a name="billing-and-licensing-basics"></a>Abrechnungs- und Lizenzierungsgrundlagen

Derzeit sind für **SQL-Datenbank** und **SQL Managed Instance** verschiedene Bereitstellungsoptionen und Dienstebenen mit unterschiedlichen Preisen für Ressourcen verfügbar, die je nach ausgewählter Dienstebene und Computegröße mit einem festen Stundensatz abgerechnet werden. Die neuesten Informationen zu aktuell unterstützten Dienstebenen, Compute- und Speichergrößen finden Sie unter [DTU-basiertes Kaufmodell für SQL-Datenbank](database/service-tiers-dtu.md) und [vCore-basiertes Kaufmodell für SQL-Datenbank und SQL Managed Instance](database/service-tiers-vcore.md).

- Bei SQL-Datenbank können Sie zwischen einer Vielzahl von Dienstebenen – ab einem Preis von 5 USD pro Monat für Basic – diejenige auswählen, die Ihre Anforderungen erfüllt. Sie können [Pools für elastische Datenbanken](database/elastic-pool-overview.md) erstellen, um Ressourcen zwischen Datenbanken freizugeben und somit Kosten zu senken und Nutzungsspitzen abzufangen.
- Bei SQL Managed Instance können Sie zudem Ihre eigene Lizenz verwenden. Weitere Informationen über das Verwenden eigener Lizenzen finden Sie unter [License Mobility durch Software Assurance für Azure](https://azure.microsoft.com/pricing/license-mobility/). Sie können auch den [Einsparungsrechner für den Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) verwenden, um zu ermitteln, wie Sie **bis zu 40 %** sparen können.

Außerdem wird Ihnen der ausgehende Internetdatenverkehr basierend auf den üblichen [Datenübertragungsraten](https://azure.microsoft.com/pricing/details/data-transfers/)berechnet. Sie können Dienstebenen und Computegrößen entsprechend den unterschiedlichen Durchsatzanforderungen Ihrer Anwendung dynamisch anpassen.

Bei **SQL-Datenbank** und **SQL Managed Instance** wird die Datenbanksoftware automatisch von Azure konfiguriert, gepatcht und aktualisiert. Dadurch sinken Ihre Verwaltungskosten. Darüber hinaus helfen Ihnen die [integrierten Datensicherungsfunktionen](database/automated-backups-overview.md) dabei, erhebliche Kosteneinsparungen zu erzielen, vor allem bei einer großen Anzahl von Datenbanken.

Mit **SQL Server auf Azure-VMs** können Sie entweder eines der über die Plattform bereitgestellten SQL Server-Images (mit enthaltener Lizenz) oder aber Ihre eigene SQL Server-Lizenz verwenden. Alle unterstützten SQL Server-Versionen (2008 R2, 2012, 2014, 2016, 2017, 2019) und Editionen (Developer, Express, Web, Standard, Enterprise) stehen zur Verfügung. Darüber hinaus sind BYOL-Versionen (Bring Your Own License) der Images verfügbar. Bei Verwendung der von Azure bereitgestellten Images hängen die Betriebskosten von der Größe des virtuellen Computers und von der gewählten SQL Server-Edition ab. Unabhängig von der Größe des virtuellen Computers und der SQL Server-Edition bezahlen Sie einen minutenbezogenen Lizenzpreis für SQL Server sowie für den Windows- oder Linux-Server. Hinzu kommt noch der Azure Storage-Preis für die VM-Datenträger. Mit der minutenbezogenen Abrechnungsoption können Sie SQL Server solange wie nötig verwenden, ohne zusätzliche SQL Server-Lizenzen zu erwerben. Wenn Sie eine eigene SQL Server-Lizenz in Azure einbringen, werden Ihnen nur Server- und Speicherkosten in Rechnung gestellt. Weitere Informationen über das Einbringen eigener Lizenzen finden Sie unter [Lizenzmobilität durch Software Assurance für Azure](https://azure.microsoft.com/pricing/license-mobility/). Außerdem wird Ihnen der ausgehende Internetdatenverkehr basierend auf den üblichen [Datenübertragungsraten](https://azure.microsoft.com/pricing/details/data-transfers/)berechnet.

#### <a name="calculating-the-total-application-cost"></a>Berechnen der gesamten Anwendungskosten

Wenn Sie erstmals eine Cloud-Plattform verwenden, enthalten die Kosten der Anwendungsausführung die Kosten für Neuentwicklung und fortlaufende Verwaltung sowie die Dienstkosten für die öffentliche Cloud-Plattform.

Weitere Informationen zur Preisberechnung finden Sie in den folgenden Ressourcen:

- [Preise von SQL-Datenbank und SQL Managed Instance](https://azure.microsoft.com/pricing/details/sql-database/)
- [Virtual Machines – Preise](https://azure.microsoft.com/pricing/details/virtual-machines/) für [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) und für [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Verwaltung

Bei vielen Unternehmen wird die Entscheidung zum Wechsel auf einen Clouddienst genauso stark vom Bestreben um eine Vereinfachung der Verwaltung wie von den erhofften Kostenvorteilen getrieben. Beim IaaS- und PaaS-Modell verwaltet Azure die zugrunde liegende Infrastruktur und repliziert automatisch alle Daten für eine Notfallwiederherstellung, konfiguriert und aktualisiert die Datenbanksoftware, verwaltet den Lastenausgleich und führt bei einem Serverausfall ein transparentes Failover innerhalb eines Rechenzentrums durch.

- Mit **SQL-Datenbank** und **SQL Managed Instance** können Sie sich weiterhin um die Verwaltung Ihrer Datenbank kümmern, müssen aber die Datenbank-Engine, das Betriebssystem und die Hardware nicht mehr verwalten. Weiterhin verwalten können Sie z. B. die Datenbanken und Anmeldungen, die Index- und Abfrageoptimierung, die Überwachung und die Sicherheit. Darüber hinaus erfordert die Konfiguration der Hochverfügbarkeit für ein anderes Rechenzentrum einen minimalen Konfigurations- und Verwaltungsaufwand.
- Bei **SQL Server auf Azure-VM** haben Sie die uneingeschränkte Kontrolle über das Betriebssystem und die Konfiguration der SQL Server-Instanzen. Bei einer VM können Sie entscheiden, wann das Betriebssystem und die Datenbanksoftware aktualisiert und wann zusätzliche Programme wie etwa Antivirentools installiert werden sollen. Durch einige bereitgestellte Automatisierungsfeatures lassen sich Patching, Sicherungen und Hochverfügbarkeit erheblich vereinfachen. Darüber hinaus können Sie die Größe des virtuellen Computers, die Anzahl der Laufwerke und deren Speicherkonfigurationen steuern. Mit Azure lässt sich die Größe eines virtuellen Computers nach Bedarf anpassen. Weitere Informationen finden Sie unter [Größen von virtuellen Computern und Clouddiensten für Azure](../virtual-machines/sizes.md).

## <a name="service-level-agreement-sla"></a>Vereinbarung zum Servicelevel (SLA)

Für viele IT-Abteilungen hat die Einhaltung vereinbarter Betriebszeiten im Rahmen einer Vereinbarung zum Servicelevel höchste Priorität. In diesem Abschnitt wird erläutert, welche Vereinbarung zum Servicelevel (SLA) für die jeweilige Datenbank-Hostingoption gilt.

Für **Azure SQL-Datenbank** und **Azure SQL Managed Instance** bietet Microsoft eine SLA mit einer Verfügbarkeit 99,99 %. Aktuelle Informationen finden Sie unter [Vereinbarung zum Servicelevel](https://azure.microsoft.com/support/legal/sla/sql-database/).

Für **SQL Server auf Azure-VM** bietet Microsoft eine SLA mit einer Verfügbarkeit von 99,95 %, die nur den virtuellen Computer abdeckt. Diese SLA deckt nicht die Prozesse ab, die auf dem virtuellen Computer ausgeführt werden (z. B. SQL Server). Außerdem erfordert sie, dass mindestens zwei VM-Instanzen in einer Verfügbarkeitsgruppe gehostet werden. Aktuelle Informationen finden Sie unter [SLA für Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Für eine besonders hohe Datenbankverfügbarkeit auf VMs sollten Sie eine der unterstützten Hochverfügbarkeitsoptionen in SQL Server konfigurieren, wie z. B. [AlwaysOn-Verfügbarkeitsgruppen](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Durch die Verwendung einer Hochverfügbarkeitsoption erhalten Sie zwar keine zusätzliche SLA, können aber eine Datenbankverfügbarkeit von >99,99 Prozent erreichen.

## <a name="time-to-move-to-azure"></a><a name="market"></a>Zeit für einen Wechsel nach Azure

**Azure SQL-Datenbank** ist die geeignete Lösung für cloudbasierte Anwendungen, wenn Entwicklerproduktivität und eine schnelle Markteinführung für neue Lösungen entscheidend sind. Durch programmgesteuerte DBA-ähnliche Funktionen ist sie perfekt für Cloud-Architekten und Entwickler, da sie den Verwaltungsaufwand für das zugrunde liegende Betriebssystem und die Datenbank verringert.

**Azure SQL Managed Instance** vereinfacht die Migration bestehender Anwendungen zu Azure erheblich, sodass Sie eine migrierte Datenbankanwendung in Azure schnell auf den Markt bringen können.

**SQL Server auf Azure-VM** eignet sich hervorragend, wenn Ihre bereits vorhandenen oder neuen Anwendungen große Datenbanken oder Zugriff auf sämtliche Features in SQL Server oder Windows/Linux benötigen und Sie Zeit und Kosten für den Erwerb neuer lokaler Hardware einsparen möchten. Diese Option ist auch eine gute Wahl, wenn Sie vorhandene lokale Anwendungen und Datenbanken unverändert zu Azure migrieren möchten, d. h. in Fällen, in denen sich SQL-Datenbank oder SQL Managed Instance nicht gut eignet. Da Sie keine Änderungen an der Präsentations-, Anwendungs- und Datenschicht vornehmen müssen, sparen Sie sich die Zeit und das Geld, die sonst für den Neuentwurf der vorhandenen Lösung nötig wären. Sie können in diesem Fall alle Lösungen sofort zu Azure migrieren und müssen lediglich einige Leistungsoptimierungen durchführen, die möglicherweise von der Azure-Plattform verlangt werden. Weitere Informationen finden Sie unter [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](virtual-machines/windows/performance-guidelines-best-practices.md).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu den ersten Schritten für SQL-Datenbank finden Sie unter [Erstellen einer ersten Azure SQL-Datenbank](database/single-database-create-quickstart.md).
- Informationen zu den ersten Schritten mit Azure SQL Managed Instance finden Sie unter [Ihre erste Azure SQL Managed Instance](managed-instance/instance-create-quickstart.md). 
- Informationen finden Sie unter [Preise für SQL-Datenbank](https://azure.microsoft.com/pricing/details/sql-database/).
- Informationen zu den ersten Schritten mit SQL Server auf virtuellen Azure-Computern finden Sie unter [Bereitstellen eines virtuellen Computers mit SQL Server in Azure](virtual-machines/windows/create-sql-vm-portal.md) .
- [Bestimmen Sie die richtige SQL-Datenbank-SKU bzw. SQL Managed Instance-SKU für Ihre lokale Datenbank](/sql/dma/dma-sku-recommend-sql-db/).