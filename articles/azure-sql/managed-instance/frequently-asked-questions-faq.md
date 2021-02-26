---
title: Häufig gestellte Fragen (FAQ)
titleSuffix: Azure SQL Managed Instance
description: Häufig gestellte Fragen (FAQ) zu Azure SQL Managed Instance
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 09/21/2020
ms.openlocfilehash: f58811411a447eae4ff9a5c0fd2a9888b4a39a34
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428672"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Häufig gestellte Fragen (FAQ) zu Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Dieser Artikel enthält die am häufigsten gestellten Fragen zu [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md).

## <a name="supported-features"></a>Unterstützte Features

**Wo finde ich eine Liste der unterstützten Funktionen in SQL Managed Instance?**

Eine Liste der in SQL Managed Instance unterstützten Funktionen finden Sie unter [Azure SQL Managed Instance: Features](../database/features-comparison.md).

Informationen zu den Unterschieden in Syntax und Verhalten zwischen Azure SQL Managed Instance und SQL Server finden Sie unter [T-SQL-Unterschiede zu SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="technical-specification-resource-limits-and-other-limitations"></a>Technische Spezifikation, Ressourceneinschränkungen und andere Einschränkungen
 
**Wo finde ich technische Merkmale und Ressourceneinschränkungen für SQL Managed Instance?**

Informationen zu den verfügbaren Merkmalen der Hardwaregenerationen finden Sie unter [Technische Unterschiede zwischen Hardwaregenerationen](resource-limits.md#hardware-generation-characteristics).
Informationen zu den verfügbaren Dienstebenen und ihren Merkmalen finden Sie unter [Technische Unterschiede zwischen Dienstebenen](resource-limits.md#service-tier-characteristics).

**Für welche Dienstebene bin ich qualifiziert?**

Jeder Kunde ist für alle Dienstebenen qualifiziert. Falls Sie aber Ihre vorhandenen Lizenzen gegen vergünstigte Tarife in Azure SQL Managed Instance über den [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) eintauschen möchten, sollten Sie Folgendes beachten: SQL Server Enterprise Edition-Kunden mit Software Assurance sind für die Leistungsstufen [Universell](../database/service-tier-general-purpose.md) oder [Unternehmenskritisch](../database/service-tier-business-critical.md) qualifiziert, und SQL Server Standard Edition-Kunden mit Software Assurance sind nur für die Leistungsstufe „Universell“ qualifiziert. Weitere Informationen finden Sie unter [Welche spezifischen Rechte gelten für den Azure-Hybridvorteil für SQL Server?](../azure-hybrid-benefit.md?tabs=azure-powershell#what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server).

**Welche Abonnementtypen werden für SQL Managed Instance unterstützt?**

Eine Liste mit den unterstützten Abonnementtypen finden Sie unter [Unterstützte Abonnementtypen](resource-limits.md#supported-subscription-types). 

**Welche Azure-Regionen werden unterstützt?**

Verwaltete Instanzen können in den meisten Azure-Regionen erstellt werden. Weitere Informationen finden Sie auf der Seite mit den [unterstützten Regionen für SQL Managed Instance](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). [Senden Sie eine Supportanfrage über das Azure-Portal](../database/quota-increase-request.md), wenn Sie eine verwaltete Instanz in einer Region benötigen, die derzeit nicht unterstützt wird.

**Gelten für SQL Managed Instance-Bereitstellungen Kontingenteinschränkungen?**

Die verwaltete Instanz verfügt über zwei Standardeinschränkungen: eine Einschränkung der Anzahl von Subnetzen, die Sie verwenden können, und eine Einschränkung der Anzahl von virtuellen Kernen, die Sie bereitstellen können. Die Einschränkungen variieren je nach Abonnementtyp und Region. Eine Liste mit den Einschränkungen für regionale Ressourcen nach Abonnementtyp finden Sie in der Tabelle unter [Regionale Ressourcenbeschränkungen](resource-limits.md#regional-resource-limitations). Dies sind weiche Einschränkungen, die bei Bedarf erweitert werden können. Senden Sie eine Supportanfrage zum Erhöhen des Kontingents über das Azure-Portal, wenn Sie mehr verwaltete Instanzen in Ihren aktuellen Regionen bereitstellen müssen. Weitere Informationen finden Sie unter [Anfordern von Kontingenterhöhungen für Azure SQL-Datenbank](../database/quota-increase-request.md).

**Kann ich die maximale Anzahl von Datenbanken (100) auf meiner verwalteten Instanz bedarfsgesteuert erhöhen?**

Nein. Derzeit ist nicht geplant, die Anzahl von Datenbanken für eine SQL Managed Instance zu erhöhen. 

**Wohin kann ich eine Migration durchführen, wenn ich über mehr als 8 TB an Daten verfüge?**
Sie können eine Migration zu anderen Azure-Varianten erwägen, die zu Ihrer Workload passen: [Hyperscale in Azure SQL-Datenbank](../database/service-tier-hyperscale.md) oder [SQL Server auf virtuellen Azure-Computern](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

**Wohin kann ich eine Migration durchführen, wenn ich über bestimmte Hardwareanforderungen verfüge, z. B. ein höheres Verhältnis von Arbeitsspeicher zu virtuellen Kernen oder weitere CPUs?**
Sie können erwägen, eine Migration zu [SQL Server auf virtuellen Azure-Computern](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) oder zu [Azure SQL-Datenbank](../database/sql-database-paas-overview.md) mit Arbeitsspeicher- bzw. CPU-Optimierung durchzuführen.

## <a name="known-issues-and-defects"></a>Bekannte Probleme und Fehler

**Wo finde ich bekannte Probleme und Fehler?**

Informationen zu Produktfehlern und bekannten Problemen finden Sie unter [Bekannte Probleme](../database/doc-changes-updates-release-notes.md#known-issues).

## <a name="new-features"></a>Neue Funktionen

**Wo finde ich die neuesten Features sowie die Features in der öffentlichen Vorschau?**

Neue Features und Previewfunktionen finden Sie in den [Versionshinweisen](../database/doc-changes-updates-release-notes.md?tabs=managed-instance).

## <a name="create-update-delete-or-move-sql-managed-instance"></a>Erstellen, Aktualisieren, Löschen oder Verschieben von SQL Managed Instance

**Wie kann ich SQL Managed Instance bereitstellen?**

Sie können eine Instanz per [Azure-Portal](instance-create-quickstart.md), [PowerShell](scripts/create-configure-managed-instance-powershell.md), [Azure CLI](https://techcommunity.microsoft.com/t5/azure-sql-database/create-azure-sql-managed-instance-using-azure-cli/ba-p/386281) und mit [ARM-Vorlagen](/archive/blogs/sqlserverstorageengine/creating-azure-sql-managed-instance-using-arm-templates) bereitstellen.

**Kann ich verwaltete Instanzen in einem vorhandenen Abonnement bereitstellen?**

Ja. Sie können eine verwaltete Instanz in einem vorhandenen Abonnement bereitstellen, wenn es zu den [unterstützten Abonnementtypen](resource-limits.md#supported-subscription-types) gehört.

**Warum kann ich keine verwaltete Instanz in einem Subnetz bereitstellen, dessen Name mit einer Ziffer beginnt?**

Dies ist eine aktuelle Einschränkung der zugrunde liegenden Komponente, bei der der Subnetzname anhand regulärer Ausdrücke überprüft wird: ^[a-zA-Z_][^\\\/\:\*\?\"\<\>\|\`\'\^]*(?<![\.\s])$. Alle Namen, die die Überprüfung mit regulären Ausdrücken bestehen und gültige Subnetznamen sind, werden derzeit unterstützt.

**Wie kann ich meine verwaltete Instanz skalieren?**

Sie können Ihre verwaltete Instanz per [Azure-Portal](../database/service-tiers-vcore.md?tabs=azure-portal#selecting-a-hardware-generation), [PowerShell](/archive/blogs/sqlserverstorageengine/change-size-azure-sql-managed-instance-using-powershell), [Azure CLI](/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-update&preserve-view=true) oder mit [ARM-Vorlagen](/archive/blogs/sqlserverstorageengine/updating-azure-sql-managed-instance-properties-using-arm-templates) skalieren.

**Kann ich meine verwaltete Instanz aus einer Region in eine andere verschieben?**

Ja, das ist möglich. Eine Anleitung finden Sie unter [Verschieben von Ressourcen in eine neue Region](../database/move-resources-across-regions.md).

**Wie kann ich meine verwaltete Instanz löschen?**

Sie können verwaltete Instanzen per Azure-Portal, [PowerShell](/powershell/module/az.sql/remove-azsqlinstance?preserve-view=true&view=azps-4.3.0), [Azure CLI](/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-delete&preserve-view=true) oder mit [Resource Manager-REST-APIs](/rest/api/sql/managedinstances/delete) löschen.

**Wie lange dauert es, eine Instanz zu erstellen oder zu aktualisieren oder eine Datenbank wiederherzustellen?**

Die erwartete Dauer für die Erstellung einer neuen verwalteten Instanz oder die Änderung von Dienstebenen (virtuelle Kerne, Speicher) hängt von mehreren Faktoren ab. Weitere Informationen finden Sie unter [Verwaltungsvorgänge](sql-managed-instance-paas-overview.md#management-operations).
 
## <a name="naming-conventions"></a>Benennungskonventionen

**Kann eine verwaltete Instanz über den gleichen Namen wie eine lokale SQL Server-Instanz verfügen?**

Die Namensänderung für eine verwaltete Instanz wird nicht unterstützt.

**Kann ich das Präfix der DNS-Zone ändern?**

Ja. Die DNS-Standardzone *.database.windows.net* einer verwalteten Instanz kann geändert werden. 

So verwenden Sie statt der Standardzone eine andere DNS-Zone, z. B. *.contoso.com*: 
- Verwenden Sie „CliConfig“ zum Definieren eines Alias. Weil das Tool nur ein Wrapper für Registrierungseinstellungen ist, kann es auch mithilfe einer Gruppenrichtlinie oder eines Skripts ausgeführt werden.
- Verwenden Sie *CNAME* mit der Option *TrustServerCertificate=true*.

## <a name="migration-options"></a>Migrationsoptionen

**Wie kann ich eine Azure SQL-Einzeldatenbank oder einen Azure SQL-Pool für elastische Datenbanken zu einer verwalteten Instanz von SQL Managed Instance migrieren?**

Die verwaltete Instanz bietet die gleichen Leistungsstufen pro Compute- und Speichergröße wie andere Bereitstellungsoptionen von Azure SQL-Datenbank. Wenn Sie Daten in einer einzelnen Instanz konsolidieren möchten oder einfach eine Funktion ausschließlich in der verwalteten Instanz unterstützt werden soll, können Sie die Daten mithilfe der Export-/Import-Funktion (BACPAC) migrieren. Hier finden Sie weitere Optionen, wofür die SQL-Datenbankmigration zu SQL Managed Instance geeignet wäre: 
- Verwenden der [externen Datenquelle](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-129-using-data-source-external-from-azure-sql/ba-p/1443210)
- Verwenden von [SQLPackage](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-migrate-azure-sql-database-to-azure-sql-managed-instance/ba-p/369182)
- Verwenden von [BCP](https://medium.com/azure-sqldb-managed-instance/migrate-from-azure-sql-managed-instance-using-bcp-674c92efdca7)

**Wie kann ich meine Instanzdatenbank zu einer einzelnen Azure SQL-Datenbank migrieren?**

Eine Option ist das [Exportieren einer Datenbank in eine BACPAC-Datei](../database/database-export.md) und das anschließende [Importieren der BACPAC-Datei](../database/database-import.md). Dieser Ansatz wird empfohlen, wenn die Datenbank kleiner als 100 GB ist.

[Transaktionsreplikation](replication-two-instances-and-sql-server-configure-tutorial.md?view=sql-server-2017&preserve-view=true) kann verwendet werden, wenn alle Tabellen in der Datenbank *Primärschlüssel* aufweisen und in der Datenbank keine In-Memory-OLTP-Objekte vorhanden sind.

Native COPY_ONLY-Sicherungen einer verwalteten Instanz können nicht in SQL Server wiederhergestellt werden, da eine verwaltete Instanz eine höhere Datenbankversion als SQL Server aufweist. Weitere Informationen finden Sie unter [Kopiesicherung](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15).

**Wie kann ich meine SQL Server-Instanz zu SQL Managed Instance migrieren?**

Informationen zum Migrieren Ihrer SQL Server-Instanz finden Sie unter [Migration einer SQL Server-Instanz zu Azure SQL Managed Instance](migrate-to-instance-from-sql-server.md).

**Wie kann ich von anderen Plattformen zu SQL Managed Instance migrieren?**

Weitere Informationen zum Migrieren von anderen Plattformen finden Sie im [Leitfaden zur Azure-Datenbankmigration](https://datamigration.microsoft.com/).

## <a name="switch-hardware-generation"></a>Wechseln der Hardwaregeneration 

**Kann ich für meine verwaltete Instanz online zwischen der Hardwaregeneration Gen 4 und Gen 5 wechseln?**

Der automatische Onlinewechsel von Gen4 zu Gen5 ist möglich, wenn die Gen5-Hardware in der Region verfügbar ist, in der Ihre verwaltete Instanz bereitgestellt wurde. In diesem Fall können Sie auf der [Seite mit der Übersicht über das V-Kern-Modell](../database/service-tiers-vcore.md) nachsehen, wie Sie zwischen den Hardwaregenerationen wechseln können.

Dabei handelt es sich um einen Vorgang mit langer Ausführungszeit, da eine neue verwaltete Instanz im Hintergrund bereitgestellt wird und Datenbanken automatisch zwischen der alten und neuen Instanz mit einem schnellen Failover am Ende des Prozesses übertragen werden.

Hinweis: Die Gen4-Hardware wird eingestellt und steht für neue Bereitstellungen nicht mehr zur Verfügung. Alle neuen Datenbanken müssen auf Gen5-Hardware bereitgestellt werden. Der Wechsel von Gen5 zu Gen4 ist ebenfalls nicht möglich.

## <a name="performance"></a>Leistung 

**Wie kann ich die Managed Instance-Leistung mit der SQL Server-Leistung vergleichen?**

Als Ausgangspunkt für einen Leistungsvergleich zwischen der verwalteten Instanz und SQL Server empfiehlt sich der Artikel [Best practices for performance comparison between Azure SQL Managed Instance and SQL Server](https://techcommunity.microsoft.com/t5/azure-sql-database/the-best-practices-for-performance-comparison-between-azure-sql/ba-p/683210) (Bewährte Methoden für den Leistungsvergleich zwischen der verwalteten Azure-SQL-Instanz und SQL Server, in englischer Sprache).

**Wodurch entstehen Leistungsunterschiede zwischen Managed Instance und SQL Server?**

Weitere Informationen finden Sie unter [Hauptursache für Leistungsunterschiede zwischen SQL Managed Instance und SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/). Weitere Informationen zum Einfluss der Protokolldateigröße auf die Leistung einer universell verwalteten Instanz finden Sie unter [Auswirkung der Protokolldateigröße auf die Leistung einer universell verwalteten Instanz](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

**Wie optimiere ich die Leistung meiner verwalteten Instanz?**

Sie können die Leistung Ihrer verwalteten Instanz wie folgt optimieren:
- [Die automatische Optimierung](../database/automatic-tuning-overview.md), die aufgrund einer kontinuierlichen Leistungsoptimierung, die auf künstlicher Intelligenz und maschinellem Lernen basiert, für hohe Leistung und stabile Workloads sorgt.
-   [In-Memory-OLTP](../in-memory-oltp-overview.md), das den Durchsatz und die Latenz bei transaktionalen Verarbeitungsworkloads verbessert und schnellere geschäftliche Erkenntnisse liefert. 

Wenn Sie die Leistung noch weiter optimieren möchten, wenden Sie einige der *Best Practices* für die [Anwendungs- und Datenbankoptimierung](../database/performance-guidance.md#tune-your-database) an.
Wenn Ihre Workload aus vielen kleinen Transaktionen besteht, sollten Sie den [Verbindungstyp von Proxymodus in Umleitungsmodus ändern](connection-types-overview.md#changing-connection-type), um eine niedrigere Latenz und höheren Durchsatz zu erreichen.

## <a name="monitoring-metrics-and-alerts"></a>Überwachung, Metriken und Warnungen

**Welche Optionen sind für die Überwachung und Warnungen für meine verwaltete Instanz verfügbar?**

Alle möglichen Optionen zur Überwachung und Warnung bei der Nutzung und Leistung von SQL Managed Instance finden Sie im [Blogbeitrag zu Überwachungsoptionen für Azure SQL Managed Instance](https://techcommunity.microsoft.com/t5/azure-sql-database/monitoring-options-available-for-azure-sql-managed-instance/ba-p/1065416). Die Leistungsüberwachung in Echtzeit für SQL Managed Instance finden Sie unter [Leistungsüberwachung in Echtzeit für verwaltete Azure SQL-Datenbank-Instanzen](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance).

**Kann ich SQL Profiler für die Leistungsüberwachung verwenden?**

Ja, jeweils SQL Profiler oder SQL Managed Instance werden unterstützt. Weitere Informationen finden Sie unter [SQL Profiler](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15).

**Werden Database Advisor und Query Performance Insight für verwaltete Instanzdatenbanken unterstützt?**

Nein, sie werden nicht unterstützt. Sie können [DMVs](../database/monitoring-with-dmvs.md) und den [Abfragedatenspeicher](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?preserve-view=true&view=sql-server-ver15) zusammen mit [SQL Profiler](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15) und [XEvents](/sql/relational-databases/extended-events/extended-events?preserve-view=true&view=sql-server-ver15) verwenden, um Ihre Datenbanken zu überwachen.

**Kann ich Metrikwarnungen für SQL Managed Instance erstellen?**

Ja. Anweisungen finden Sie unter [Erstellen von Warnungen für SQL Managed Instance](alerts-create.md).

**Kann ich Warnungsmetriken für eine Datenbank in SQL Managed Instance erstellen?**

Nein, Warnungsmetriken sind nur für eine verwaltete Instanz verfügbar. Warnungsmetriken für einzelne Datenbanken in einer verwalteten Instanz sind nicht verfügbar.

## <a name="storage-size"></a>Speichergröße

**Was ist die maximale Speichergröße für SQL Managed Instance?**

Die Speichergröße für SQL Managed Instance hängt von der ausgewählten Dienstebene („Universell“ oder „Unternehmenskritisch“) ab. Informationen zu den Speichereinschränkungen dieser Dienstebenen finden Sie unter [Dienstebenen für Azure SQL-Datenbank und Azure SQL Managed Instance](../database/service-tiers-general-purpose-business-critical.md).

**Was ist die minimale Speichergröße, die für eine verwaltete Instanz verfügbar ist?**

Die Mindestmenge an verfügbarem Speicherplatz in einer Instanz beträgt 32 GB. Der Speicher kann in Inkrementen von 32 GB bis zur maximalen Speichergröße hinzugefügt werden. Die ersten 32 GB sind kostenlos.

**Kann ich den Speicherplatz, der einer Instanz zugewiesen ist, unabhängig von den Computeressourcen erhöhen?**

Ja, Sie können Add-On-Speicher unabhängig von Computeressourcen in gewissem Umfang erwerben. Weitere Informationen finden Sie unter *Maximal reservierter Instanzspeicher* in der [Tabelle](resource-limits.md#hardware-generation-characteristics).

**Wie kann ich die Speicherleistung auf der universellen Dienstebene optimieren?**

Informationen zur Optimierung der Speicherleistung finden Sie unter [Best Practices für die Speicherleistung auf universeller Dienstebene](https://techcommunity.microsoft.com).

## <a name="backup-and-restore"></a>Sichern und Wiederherstellen

**Wird der Sicherungsspeicher vom Speicher meiner verwalteten Instanz abgezogen?**

Nein, der Sicherungsspeicher wird nicht vom Speicher Ihrer verwalteten Instanz abgezogen. Der Sicherungsspeicher ist unabhängig vom Instanzspeicherplatz, und seine Größe ist nicht begrenzt. Der Sicherungsspeicher wird durch den Zeitraum zum Beibehalten der Sicherung Ihrer Instanzdatenbanken begrenzt, der bis zu 35 Tagen festgelegt werden kann. Weitere Informationen finden Sie unter [Automatisierte Sicherungen](../database/automated-backups-overview.md).

**Wie kann ich feststellen, wann automatisierte Sicherungen für meine verwaltete Instanz durchgeführt werden?**

Informationen zum Nachverfolgen, wann automatisierte Sicherungen auf einer SQL Managed Instance-Instanz ausgeführt wurden, finden Sie unter [So werden automatische Sicherungen für Azure SQL Managed Instance nachverfolgt](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-128-how-to-track-the-automated-backup-for-an/ba-p/1442355).

**Wird eine bedarfsgesteuerte Sicherung unterstützt?**

Ja, Sie können im Azure Blob Storage eine vollständige Kopiesicherung erstellen. Diese kann jedoch nur in Managed Instance wieder hergestellt werden. Weitere Informationen finden Sie unter [Kopiesicherung](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15). Allerdings ist die Kopiesicherung nicht möglich, wenn die Datenbank von einer dienstverwalteten TDE-Technologie verschlüsselt wird, da das für die Verschlüsselung verwendete Zertifikat nicht zugänglich ist. Verwenden Sie in diesem Fall die Point-in-Time-Wiederherstellung, um diese Datenbank auf eine andere Instanz von SQL Managed Instance zu verschieben, oder wechseln Sie zu einem kundenseitig verwalteten Schlüssel.

**Wird die native Wiederherstellung (von BAK-Dateien) in Managed Instance unterstützt?**

Ja, sie wird unterstützt und ist für SQL Server 2005 und höhere Versionen verfügbar.  Wenn Sie die native Wiederherstellung verwenden möchten, laden Sie die BAK-Datei in Azure Blob Storage hoch, und führen Sie die T-SQL-Befehle aus. Weitere Informationen finden Sie unter [Native RESTORE FROM URL-Option](./migrate-to-instance-from-sql-server.md#native-restore-from-url).

## <a name="business-continuity"></a>Geschäftskontinuität

**Werden meine Systemdatenbanken auf die sekundäre Instanz in einer Failovergruppe repliziert?**

Systemdatenbanken werden nicht in die sekundäre Instanz in einer Failovergruppe repliziert. Daher sind Szenarien, die von Objekten aus den Systemdatenbanken abhängen, in der sekundären Instanz nicht möglich, es sei denn, die Objekte werden manuell in der sekundären Instanz erstellt. Ein Problemumgehung finden Sie unter [Aktivieren von Szenarios in Abhängigkeit von Objekten in den Systemdatenbanken](../database/auto-failover-group-overview.md?tabs=azure-powershell#enable-scenarios-dependent-on-objects-from-the-system-databases).
 
## <a name="networking-requirements"></a>Netzwerkanforderungen 

**Welche Einschränkungen in Bezug auf den ein- und ausgehenden NSG-Datenverkehr gelten derzeit für Subnetze von verwalteten Instanzen?**

Die erforderlichen NSG- und UDR-Regeln sind [hier](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) dokumentiert und werden vom Dienst automatisch festgelegt.
Beachten Sie, dass es sich bei diesen Regeln lediglich um die Regeln handelt, die wir für die Wartung des Diensts benötigen. Für die Verbindungsherstellung mit der verwalteten Instanz und die Verwendung anderer Features müssen Sie weitere featurespezifische Regeln festlegen und pflegen.

**Wie kann ich NSG-Regeln für eingehenden Datenverkehr an Verwaltungsports festlegen?**

SQL Managed Instance ist für das Festlegen von Regeln auf Verwaltungsports zuständig. Dies wird mit einer Funktion mit dem Namen [dienstgestützte Subnetzkonfiguration](connectivity-architecture-overview.md#service-aided-subnet-configuration) erreicht.
Hiermit wird der unterbrechungsfreie Fluss von Verwaltungsdatenverkehr sichergestellt, um eine SLA zu erfüllen.

**Kann ich die Quell-IP-Adressbereiche abrufen, die für den eingehenden Verwaltungsdatenverkehr verwendet werden?**

Ja. Sie können Datenverkehr analysieren, der über Ihre Netzwerksicherheitsgruppen eingeht, indem Sie [Network Watcher-Flussprotokolle konfigurieren](../../network-watcher/network-watcher-monitoring-overview.md#analyze-traffic-to-or-from-a-network-security-group).

**Kann ich die NSG so festlegen, dass der Zugriff auf den Datenendpunkt (Port 1433) gesteuert wird?**

Ja. Nachdem eine verwaltete Instanz bereitgestellt wurde, können Sie eine NSG festlegen, die den Zugriff in Eingangsrichtung auf Port 1433 steuert. Wir empfehlen Ihnen, den zugehörigen IP-Adressbereich so weit wie möglich zu begrenzen.

**Kann ich das virtuelle Netzwerkgerät oder die lokale Firewall so festlegen, dass der ausgehende Verwaltungsdatenverkehr basierend auf FQDNs gefiltert wird?**

Nein. Dies wird aus verschiedenen Gründen nicht unterstützt:
-   Das Routing von Datenverkehr als Antwort auf eine eingehende Verwaltungsanforderung wäre asymmetrisch und würde nicht funktionieren.
-   Das Routing von Datenverkehr in den Speicher würde durch Durchsatzeinschränkungen und Latenz beeinträchtigt werden. Auf diese Weise ist es also nicht möglich, die erwartete Dienstqualität und -verfügbarkeit bereitzustellen.
-   Die Erfahrung hat gezeigt, dass diese Konfigurationen fehleranfällig sind und nicht unterstützt werden.

**Kann ich das virtuelle Netzwerkgerät oder die Firewall für ausgehenden Datenverkehr festlegen, bei dem es sich nicht um Verwaltungsdatenverkehr handelt?**

Ja. Dies lässt sich am einfachsten erreichen, indem eine 0/0-Regel einer UDR hinzugefügt wird, die dem Subnetz einer verwalteten Instanz zugeordnet ist. So kann Datenverkehr über das virtuelle Netzwerkgerät geleitet werden.
 
**Wie viele IP-Adressen benötige ich für eine verwaltete Instanz?**

Das Subnetz muss eine ausreichende Anzahl verfügbarer [IP-Adressen](connectivity-architecture-overview.md#network-requirements) aufweisen. Informationen zur Ermittlung der Größe von VNET-Subnetzen für SQL Managed Instance finden Sie unter [Bestimmen von Subnetzgröße und -bereich für Azure SQL Managed Instance](./vnet-subnet-determine-size.md). 

**Was geschieht, wenn nicht genügend IP-Adressen zum Ausführen des Updatevorgangs für Instanzen vorhanden sind?**

Falls nicht genügend [IP-Adressen](connectivity-architecture-overview.md#network-requirements) im Subnetz vorhanden sind, in dem die verwaltete Instanz bereitgestellt wird, müssen Sie ein neues Subnetz und darin eine neue verwaltete Instanz erstellen. Wir empfehlen Ihnen auch, das neue Subnetz mit mehr zugeordneten IP-Adressen zu erstellen, damit ähnliche Situationen bei zukünftigen Updatevorgängen vermieden werden können. Nachdem die neue Instanz bereitgestellt wurde, können Sie Daten zwischen der alten und der neuen Instanz manuell sichern und wiederherstellen. Sie können auch eine instanzübergreifende [Point-in-Time-Wiederherstellung](point-in-time-restore.md?tabs=azure-powershell) durchführen.

**Benötige ich zum Erstellen einer verwalteten Instanz ein leeres Subnetz?**

Nein. Sie können entweder ein leeres Subnetz oder ein Subnetz verwenden, das bereits ein oder mehrere verwaltete Instanzen enthält. 

**Kann ich den Adressbereich des Subnetzes ändern?**

Dies ist nicht möglich, wenn darin verwaltete Instanzen enthalten sind. Dies ist eine Einschränkung der Azure-Netzwerkinfrastruktur. Es ist für Sie nur zulässig, [einem leeren Subnetz weiteren Adressraum hinzuzufügen](../../virtual-network/virtual-network-manage-subnet.md#change-subnet-settings). 

**Kann ich meine verwaltete Instanz in ein anderes Subnetz verschieben?**

Nein. Dies ist eine derzeit geltende Einschränkung für das Entwerfen von verwalteten Instanzen. Sie können aber eine neue Instanz in einem anderen Subnetz bereitstellen und Daten zwischen der alten und der neuen Instanz manuell sichern und wiederherstellen. Darüber hinaus können Sie auch eine instanzübergreifende [Point-in-Time-Wiederherstellung](point-in-time-restore.md?tabs=azure-powershell) durchführen.

**Benötige ich zum Erstellen einer verwalteten Instanz ein leeres virtuelles Netzwerk?**

Dies ist nicht erforderlich. Sie können entweder [ein virtuelles Netzwerk für Azure SQL Managed Instance erstellen](./virtual-network-subnet-create-arm-template.md) oder [ein vorhandenes virtuelles Netzwerk für Azure SQL Managed Instance konfigurieren](./vnet-existing-add-subnet.md).

**Kann ich eine verwaltete Instanz zusammen mit anderen Diensten in einem Subnetz anordnen?**

Nein. Derzeit wird das Anordnen einer verwalteten Instanz in einem Subnetz, das bereits andere Ressourcentypen enthält, nicht unterstützt.

## <a name="connectivity"></a>Konnektivität 

**Kann ich mithilfe der IP-Adresse eine Verbindung mit einer verwalteten Instanz herstellen?**

Nein, dies wird nicht unterstützt. Der Hostname der Instanz von Managed Instance wird dem Lastenausgleich (Load Balancer, LB) vor dem virtuellen Cluster von Managed Instance zugeordnet. Da ein einziger virtueller Cluster mehrere verwaltete Instanzen hosten könnte, könnte die Verbindung nicht an eine ordnungsgemäße verwaltete Instanz weitergeleitet werden, ohne deren Namen anzugeben.
Weitere Informationen zur Architektur des virtuellen Clusters für SQL Managed Instances finden Sie unter [Verbindungsarchitektur für virtuelle Cluster](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Kann eine verwaltete Instanz eine statische IP-Adresse aufweisen?**

Dies wird derzeit nicht unterstützt.

In seltenen, jedoch erforderlichen Situationen müssen Sie eine Onlinemigration einer verwalteten Instanz zu einem neuen virtuellen Cluster durchführen. Gegebenenfalls ist diese Migration aufgrund von Änderungen in unserem Technologiestapel erforderlich, mit denen die Sicherheit und Zuverlässigkeit des Diensts verbessert werden sollen. Die Migration zu einem neuen virtuellen Cluster führt zum Andern der IP-Adresse, die dem Hostnamen der verwalteten Instanz zugeordnet ist. Der Dienst für die verwaltete Instanz beansprucht keine Unterstützung statischer IP-Adressen und behält sich das Recht vor, die IP-Adresse im Rahmen regulärer Wartungszyklen zu ändern.

Aus diesem Grund wird dringend empfohlen, keine statische IP-Adresse zu verwenden, da dies zu unnötigen Ausfallzeiten führen kann.

**Verfügt Managed Instance über einen öffentlichen Endpunkt?**

Ja. Managed Instance verfügt über einen öffentlichen Endpunkt, der standardmäßig nur für die Dienstverwaltung verwendet wird. Ein Kunde kann diesen jedoch auch für den Datenzugriff aktivieren. Weitere Informationen finden Sie unter [Sicheres Verwenden von Azure SQL Managed Instance mit öffentlichen Endpunkten](./public-endpoint-overview.md). Wechseln Sie zum Konfigurieren des öffentlichen Endpunkts zu [Konfigurieren eines öffentlichen Endpunkts in Azure SQL Managed Instance](public-endpoint-configure.md).

**Wie wird der Zugriff auf den öffentlichen Endpunkt von Managed Instance gesteuert?**

Managed Instance steuert den Zugriff auf den öffentlichen Endpunkt sowohl auf Netzwerk- als auch auf Anwendungsebene.

Verwaltungs- und Bereitstellungsdienste stellen über einen [Verwaltungsendpunkt](./connectivity-architecture-overview.md#management-endpoint), der einem externen Lastenausgleich zugeordnet ist, eine Verbindung mit einer verwalteten Instanz her. Der Datenverkehr wird nur an die Knoten weitergeleitet, wenn er an einer vordefinierten Gruppe von Ports empfangen wird, die ausschließlich die Verwaltungskomponenten der verwalteten Instanz verwenden. Eine integrierte Firewall auf den Knoten ist so eingerichtet, dass nur Datenverkehr von Microsoft-IP-Adressbereichen zulässig ist. Zertifikate authentifizieren gegenseitig die gesamte Kommunikation zwischen den Verwaltungskomponenten und der Verwaltungsebene. Weitere Informationen finden Sie unter [Verbindungsarchitektur für SQL Managed Instance](./connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Kann ich den öffentlichen Endpunkt verwenden, um auf die Daten in Managed Instance-Datenbanken zuzugreifen?**

Ja. Der Kunde muss den Datenzugriff für öffentliche Endpunkte über das [Azure-Portal](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal) / [PowerShell](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-using-powershell)/ARM aktivieren und die NSG so konfigurieren, dass der Zugriff auf den Datenport (Portnummer 3342) gesperrt wird. Weitere Informationen finden Sie unter [Konfigurieren eines öffentlichen Endpunkts in Azure SQL Managed Instance](public-endpoint-configure.md) und [Sicheres Verwenden von Azure SQL Managed Instance mit öffentlichen Endpunkten](public-endpoint-overview.md). 

**Kann ich einen benutzerdefinierten Port für einen oder mehrere SQL-Datenendpunkte angeben?**

Nein, diese Option ist nicht verfügbar.  Für den privaten Datenendpunkt verwendet Managed Instance die Standardportnummer 1433 und für den öffentlichen Datenendpunkt die Standardportnummer 3342.

**Was ist die empfohlene Vorgehensweise Verbinden von verwalteten Instanzen in unterschiedlichen Regionen?**

Das Express Route-Leitungspeering ist die bevorzugte Methode. Globales Peering virtueller Netzwerke wird unterstützt. Die einzige Einschränkung ist im folgenden Hinweis beschrieben.  

> [!IMPORTANT]
> [Am 22.09.2020 haben wir globales Peering virtueller Netzwerke für neu erstellte virtuelle Cluster angekündigt](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Dies bedeutet, dass globales Peering virtueller Netzwerke sowohl für SQL Managed Instances, die nach dem Ankündigungsdatum in leeren Subnetzen erstellt wurden, als auch für alle späteren verwalteten Instanzen, die in diesen Subnetzen erstellt werden, unterstützt wird. Für alle anderen SQL Managed Instances ist die Peeringunterstützung aufgrund der [Einschränkungen beim globalen Peering virtueller Netzwerke](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) auf die Netzwerke in derselben Region beschränkt. Ausführliche Informationen finden Sie im entsprechenden Abschnitt des Artikels [Azure Virtual Network – häufig gestellte Fragen](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). 

Wenn Express Route-Leitungspeering und globales Peering virtueller Netzwerke nicht möglich sind, besteht die einzige Option darin, eine Site-to-Site-VPN-Verbindung zu erstellen ([Azure-Portal](../../vpn-gateway/tutorial-site-to-site-portal.md), [PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)).

## <a name="mitigate-data-exfiltration-risks"></a>Mindern von Risiken bei der Datenexfiltration  

**Wie kann ich Risiken bei der Datenexfiltration mindern?**

Kunden wird empfohlen, zum Mindern von Risiken bei der Datenexfiltration eine Reihe von Sicherheitseinstellungen und -kontrollen anzuwenden:

- Aktivieren Sie [Transparent Data Encryption (TDE)](../database/transparent-data-encryption-tde-overview.md) für alle Datenbanken.
- Deaktivieren Sie die Common Language Runtime (CLR). Dies wird auch für lokale Umgebungen empfohlen.
- Verwenden Sie nur die Azure AD-Authentifizierung (Azure Active Directory).
- Greifen Sie auf die Instanz mit einem DBA-Konto mit geringen Rechten zu.
- Konfigurieren Sie den Zugriff auf die JIT-Jumpbox für das Systemadministratorkonto.
- Aktivieren Sie [SQL-Überwachung](/sql/relational-databases/security/auditing/sql-server-audit-database-engine), und integrieren Sie sie in Warnungsmechanismen.
- Aktivieren Sie [Bedrohungserkennung](../database/threat-detection-configure.md) in der [Azure Defender für SQL](../database/azure-defender-for-sql.md)-Suite.

## <a name="dns"></a>DNS

**Kann ich ein benutzerdefiniertes DNS für SQL Managed Instance konfigurieren?**

Ja. Informationen hierzu finden Sie unter [Konfigurieren eines benutzerdefinierten DNS für Azure SQL Managed Instance](./custom-dns-configure.md).

**Kann ich eine DNS-Aktualisierung ausführen?**

Ja. Lesen Sie dazu [Synchronisieren der Einstellung für DNS-Server virtueller Netzwerke in einem virtuellen SQL Managed Instance-Cluster](./synchronize-vnet-dns-servers-setting-on-virtual-cluster.md).

## <a name="change-time-zone"></a>Ändern der Zeitzone

**Kann ich die Zeitzone für eine vorhandene verwaltete Instanz ändern?**

Die Zeitzonenkonfiguration kann festgelegt werden, wenn eine verwaltete Instanz zum ersten Mal bereitgestellt wird. Das Ändern der Zeitzone einer vorhandenen verwalteten Instanz wird nicht unterstützt. Weitere Informationen finden Sie unter [Zeitzonenbeschränkungen](timezones-overview.md#limitations).

Als Problemumgehung können Sie eine neue verwaltete Instanz mit der richtigen Zeitzone erstellen und dann entweder eine Sicherung und Wiederherstellung oder eine [instanzübergreifende Point-in-Time-Wiederherstellung](/archive/blogs/sqlserverstorageengine/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance) durchführen (unsere Empfehlung).


## <a name="security-and-database-encryption"></a>Sicherheit und Datenbankverschlüsselung

**Ist die sysadmin-Serverrolle für SQL Managed Instance verfügbar?**

Ja. Kunden können Anmeldungen erstellen, bei denen es sich um Mitglieder der sysadmin-Rolle handelt.  Kunden, die über die sysadmin-Berechtigung verfügen, sind auch für den Betrieb der Instanz verantwortlich. Dies kann negative Auswirkungen auf die Erfüllung der SLA-Anforderungen haben. Informationen zum Hinzufügen der Anmeldung zur sysadmin-Serverrolle finden Sie unter [Azure AD-Authentifizierung](./aad-security-configure-tutorial.md#azure-ad-authentication).

**Wird Transparent Data Encryption für SQL Managed Instance unterstützt?**

Ja. Transparent Data Encryption wird für SQL Managed Instance unterstützt. Weitere Informationen finden Sie unter [TDE (Transparent Data Encryption) für SQL-Datenbank, SQL Managed Instance und Azure Synapse Analytics](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal).

**Kann ich das Modell „Bring Your Own Key“ für TDE nutzen?**

Ja. Das Szenario „Azure Key Vault für BYOK“ ist für Azure SQL Managed Instance verfügbar. Weitere Informationen finden Sie unter [Azure SQL Transparent Data Encryption mithilfe eines kundenseitig verwalteten Schlüssels](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key).

**Kann ich eine verschlüsselte SQL Server-Datenbank migrieren?**

Ja, das ist möglich. Um eine verschlüsselte SQL Server-Datenbank zu migrieren, müssen Sie Ihre vorhandenen Zertifikate exportieren und in die verwaltete Instanz importieren und anschließend eine vollständige Datenbanksicherung erstellen und auf der verwalteten Instanz wiederherstellen. 

Sie können auch den [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) nutzen, um die per TDE verschlüsselten Datenbanken zu migrieren.

**Wie kann ich die Rotation der TDE-Schutzvorrichtung für SQL Managed Instance konfigurieren?**

Sie können die TDE-Schutzvorrichtung für SQL Managed Instance per Azure Cloud Shell rotieren. Eine Anleitung finden Sie unter [Transparent Data Encryption in SQL Managed Instance mithilfe eines eigenen Schlüssels aus dem Azure Key Vault](scripts/transparent-data-encryption-byok-powershell.md).

**Kann ich meine verschlüsselte Datenbank in SQL Managed Instance wiederherstellen?**

Ja. Sie müssen die Datenbank nicht entschlüsseln, um sie in SQL Managed Instance wiederherzustellen. Sie müssen ein Zertifikat bzw. einen Schlüssel als Verschlüsselungsschlüssel-Schutzvorrichtung im Quellsystem für SQL Managed Instance bereitstellen, um Daten aus der verschlüsselten Sicherungsdatei lesen zu können. Dies kann auf zwei Arten erreicht werden:

- *Laden Sie die Zertifikatschutzvorrichtung in SQL Managed Instance hoch*. Das kann nur mithilfe von PowerShell geschehen. Im [Beispielskript](./tde-certificate-migrate.md) wird der gesamte Prozess beschrieben.
- *Laden Sie eine asymmetrische Schlüsselschutzvorrichtung in Azure Key Vault hoch, und verweisen Sie in SQL Managed Instance darauf*. Dieser Ansatz ähnelt dem TDE-Anwendungsfall „Bring-Your-Own-Key“ (BYOK), in dem ebenfalls die Key Vault-Integration zum Speichern des Verschlüsselungsschlüssels verwendet wird. Wenn Sie den Schlüssel nicht als Schutzvorrichtung für den Verschlüsselungsschlüssel verwenden, sondern nur für SQL Managed Instance zum Wiederherstellen verschlüsselter Datenbanken zur Verfügung stellen möchten, befolgen Sie die Anweisungen zum [Einrichten von BYOK-TDE](../database/transparent-data-encryption-tde-overview.md#manage-transparent-data-encryption), und aktivieren Sie nicht das Kontrollkästchen **Legen Sie den ausgewählten Schlüssel als TDE-Standardschutzvorrichtung fest**.

Nachdem Sie die Verschlüsselungsschutzvorrichtung für SQL Managed Instance verfügbar gemacht haben, können Sie mit dem Standardverfahren für die Datenbankwiederherstellung fortfahren.

## <a name="purchasing-models-and-benefits"></a>Kaufmodelle und Vorteile

**Welche Kaufmodelle sind für SQL Managed Instance verfügbar?**

Für SQL Managed Instance ist das [vCore-basierte Kaufmodell](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model) verfügbar.

**Welche Kostenvorteile sind für SQL Managed Instance verfügbar?**

Sie können mit den Azure SQL-Vorteilen wie folgt Kosten sparen:
-   Maximieren Sie die vorhandenen Investitionen in lokale Lizenzen, um mit dem [Azure-Hybridvorteil](../azure-hybrid-benefit.md?tabs=azure-powershell) bis zu 55 Prozent an Kosten zu sparen. 
-   Gehen Sie eine Verpflichtung zur Reservierung von Computeressourcen ein, um mit dem [Vorteil für reservierte Instanzen](../database/reserved-capacity-overview.md) bis zu 33 Prozent der Kosten zu sparen. Kombinieren Sie dies mit dem Azure-Hybridvorteil, um Kosten in Höhe von bis zu 82 Prozent zu sparen. 
-   Sparen Sie bis zu 55 Prozent gegenüber den Listenpreisen, indem Sie den [Vorteil in Bezug auf Preise für Azure Dev/Test](https://azure.microsoft.com/pricing/dev-test/) nutzen. Hierbei erhalten Sie vergünstigte Preise für Ihre laufenden Entwicklungs- und Testworkloads.

**Wer ist für den Vorteil für reservierte Instanzen berechtigt?**

Um sich für den Vorteil für reservierte Instanzen zu qualifizieren, muss Ihr Abonnementtyp ein Enterprise Agreement (Angebotsnummern: MS-AZR-0017P oder MS-AZR-0148P) oder eine einzelne Vereinbarung mit Preisen für nutzungsbasierte Bezahlung (Angebotsnummern: MS-AZR-0003P oder MS-AZR-0023P) sein. Weitere Informationen zu Reservierungen finden Sie unter [Sparen von Kosten für Ressourcen mit reservierter Kapazität – Azure SQL-Datenbank und SQL Managed Instance](../database/reserved-capacity-overview.md). 

**Können Reservierungen storniert, umgetauscht oder rückerstattet werden?**

Reservierungen können mit bestimmten Einschränkungen storniert, umgetauscht oder rückerstattet werden. Weitere Informationen finden Sie unter [Self-Service-Umtausch und -Rückerstattungen für Azure-Reservierungen](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="billing-for-managed-instance-and-backup-storage"></a>Abrechnung für verwaltete Instanzen und Sicherungsspeicher

**Welche Preisoptionen gibt es für SQL Managed Instance?**

Informationen zu den Preisoptionen für SQL Managed Instance finden Sie auf der [Seite mit den Preisen](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

**Wie kann ich die Abrechnungskosten für meine verwaltete Instanz nachverfolgen?**

Sie können zu diesem Zweck die [Azure Cost Management-Lösung](../../cost-management-billing/index.yml) verwenden. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu **Abonnements**, und wählen Sie **Kostenanalyse** aus. 

Verwenden Sie die Option **Kumulierte Kosten**, und filtern Sie dann nach dem **Ressourcentyp** als `microsoft.sql/managedinstances`.

**Welche Kosten fallen für automatisierte Sicherungen an?**

Die Menge an freiem Speicherplatz für Sicherungen entspricht der Menge an reserviertem Datenspeicher, den Sie erworben haben. Die gilt unabhängig vom festgelegten Aufbewahrungszeitraum für die Sicherungen. Wenn sich der Verbrauch Ihres Sicherungsspeichers innerhalb der zugeordneten Menge an freiem Speicher für Sicherungen bewegt, fallen für Sie für automatisierte Sicherungen auf der verwalteten Instanz keine weiteren Kosten an. Sie sind also kostenlos. Eine Überschreitung des kostenlosen Sicherungsspeichers führt in Regionen in den USA zu monatlichen Kosten von ca. 0,20 bis 0,24 US-Dollar pro GB. Informationen zu den Preisdetails für Ihre Region finden Sie auf der Seite mit den Preisen. Ausführlichere Informationen finden Sie auf der Seite mit der [Beschreibung der Nutzung von Sicherungsspeicher](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923).

**Wie kann ich die Abrechnungskosten für meinen Verbrauch des Sicherungsspeichers überwachen?**

Sie können die Kosten für den Sicherungsspeicher über das Azure-Portal überwachen. Eine Anleitung finden Sie unter [Überwachen der Kosten](../database/automated-backups-overview.md?tabs=managed-instance#monitor-costs). 

**Wie kann ich die Kosten für Sicherungsspeicher auf der verwalteten Instanz optimieren?**

Informationen zur Optimierung Ihrer Kosten für Sicherungsspeicher finden Sie unter [Optimieren von Sicherungen für SQL Managed Instance](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935).

## <a name="cost-saving-use-cases"></a>Anwendungsfälle für Kosteneinsparungen

**Wo finde ich Anwendungsfälle und entsprechende Kosteneinsparungen für SQL Managed Instance?**

Fallstudien zu SQL Managed Instance:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

Es gibt auch eine Forrester-Studie, die ein besseres Verständnis der Vorteile, Kosten und Risiken im Zusammenhang mit der Bereitstellung von Azure SQL Managed Instance vermittelt: [The Total Economic Impact™ von Microsoft Azure SQL Database Managed Instance](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).

## <a name="password-policy"></a>Kennwortrichtlinie 

**Welche Kennwortrichtlinien gelten für SQL-Anmeldungen in SQL Managed Instance?**

Die SQL Managed Instance-Kennwortrichtlinie für SQL-Anmeldungen erbt Azure-Plattformrichtlinien, die auf die VMs angewendet werden, die einen virtuellen Cluster bilden, der die verwaltete Instanz enthält. Zurzeit ist es nicht möglich, irgendeine dieser Einstellungen zu ändern, da diese Einstellungen durch Azure definiert und von der verwalteten Instanz geerbt sind.

 > [!IMPORTANT]
 > Die Azure-Plattform kann die Richtlinienanforderungen ändern, ohne Dienste zu benachrichtigen, die von diesen Richtlinien abhängen.

**Was sind die aktuellen Azure Platform-Richtlinien?**

Für jeden Anmeldenamen muss das Kennwort bei der Anmeldung festgelegt und nach dem Erreichen des maximalen Alters geändert werden.

| **Richtlinie** | **Sicherheitseinstellung** |
| --- | --- |
| Maximales Kennwortalter | 42 Tage |
| Minimales Kennwortalter | 1 Tag |
| Minimale Kennwortlänge | 10 Zeichen |
| Das Kennwort muss den Komplexitätsanforderungen entsprechen | Aktiviert |

**Ist es möglich, die Kennwortkomplexität und den Ablauf in SQL Managed Instance auf Anmeldeebene zu deaktivieren?**

Ja, es ist möglich, die Felder CHECK_POLICY und CHECK_EXPIRATION auf der Anmeldeebene zu steuern. Sie können die aktuellen Einstellungen überprüfen, indem Sie den folgenden T-SQL-Befehl ausführen:

```sql
SELECT *
FROM sys.sql_logins
```

Anschließend können Sie die angegebenen Anmeldeeinstellungen ändern, indem Sie Folgendes ausführen:

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(ersetzen Sie ‚test‘ durch den gewünschten Anmeldenamen, und passen Sie Werte für Richtlinie und Ablauf an).


## <a name="service-updates"></a>Dienstupdates

**Wie sieht die Änderung der Stammzertifizierungsstelle für Azure SQL-Datenbank und SQL Managed Instance aus?**

Weitere Informationen finden Sie unter [Grundlegendes zu den Änderungen im Zusammenhang mit der Stammzertifizierungsstelle für Azure SQL-Datenbank und SQL Managed Instance](../updates/ssl-root-certificate-expiring.md). 

**Was ist ein geplantes Wartungsereignis für SQL Managed Instance?**

Weitere Informationen finden Sie unter [Planen von Azure-Wartungsereignissen in SQL Managed Instance](../database/planned-maintenance.md). 


## <a name="azure-feedback-and-support"></a>Azure-Feedback und -Support

**Wohin kann ich meine Ideen für Verbesserungen von SQL Managed Instance senden?**

Sie können Ihre Stimme für ein neues Feature von SQL Managed Instance abgeben oder eine neue Idee für eine Verbesserung im [Feedbackforum von SQL Managed Instance](https://feedback.azure.com/forums/915676-sql-managed-instance) zur Abstimmung bereitstellen. Auf diese Weise können Sie an der Produktentwicklung mitwirken und uns bei der Priorisierung von potenziellen Verbesserungen unterstützen.

**Wie kann ich eine Azure-Supportanfrage erstellen?**

Informationen zum Erstellen einer Azure-Supportanfrage finden Sie unter [Erstellen einer Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md).
