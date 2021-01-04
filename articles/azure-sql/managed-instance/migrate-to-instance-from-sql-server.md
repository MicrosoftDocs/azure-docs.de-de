---
title: Migrieren von SQL Server zu Azure SQL Managed Instance
description: Erfahren Sie, wie Sie eine Datenbank von SQL Server zu Azure SQL Managed Instance migrieren.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: migration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: ''
ms.date: 07/11/2019
ms.openlocfilehash: 2761b97e595f5e11b00e75cd778ee269b12bfcae
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917799"
---
# <a name="sql-server-instance-migration-to-azure-sql-managed-instance"></a>Migration einer SQL Server-Instanz zu Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Dieser Artikel enthält Informationen zu den Methoden, mit denen eine SQL Server 2005-Instanz (oder eine höhere Version) zu [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) migriert wird. Informationen im Zusammenhang mit der Migration zu einer Einzeldatenbank oder zu einem Pool für elastische Datenbanken finden Sie unter [Migrationsübersicht: SQL Server zu SQL-Datenbank](../migration-guides/database/sql-server-to-sql-database-overview.md). Informationen im Zusammenhang mit der Migration von anderen Plattformen sowie Informationen zu Tools und Optionen finden Sie unter [Migrieren zu Azure SQL](../migration-guides/index.yml).

> [!NOTE]
> Wenn Sie Azure SQL Managed Instance schnell ausprobieren möchten, ist es vielleicht besser, zum [Leitfaden für den Schnellstart](quickstart-content-reference-guide.md) zu wechseln, statt diese Seite zu lesen.

Die Datenbankmigration sieht im Allgemeinen folgendermaßen aus:

![Migrationsprozess](./media/migrate-to-instance-from-sql-server/migration-process.png)

- [Bewerten Sie die Azure SQL Managed Instance-Kompatibilität](#assess-sql-managed-instance-compatibility), wobei Sie sicherstellen sollten, dass es keine blockierenden Probleme gibt, die Ihre Migrationen verhindern können.
  
  Dieser Schritt umfasst auch das Erstellen [einer Leistungsbaseline](#create-a-performance-baseline), um die Ressourcennutzung Ihrer SQL Server-Quellinstanz zu bestimmen. Er ist erforderlich, wenn Sie eine ordnungsgemäß dimensionierte verwaltete Instanz bereitstellen und sicherstellen möchten, dass die Leistung nach der Migration nicht beeinträchtigt wird.
- [Wählen von App-Konnektivitätsoptionen](connect-application-instance.md)
- [Stellen Sie eine verwaltete Instanz mit optimaler Größe bereit](#deploy-to-an-optimally-sized-managed-instance), in der Sie technische Merkmale (Anzahl virtueller Kerne, verfügbarer Arbeitsspeicher) und Leistungsstufe („Unternehmenskritisch“, „Universell“) für Ihre verwaltete Instanz auswählen.
- [Wählen Sie eine Migrationsmethode und den Speicherort aus,](#select-a-migration-method-and-migrate) an den Sie Ihre Datenbanken mithilfe der Offlinemigration (native Sicherung/Wiederherstellung, Datenbankimport/-export) oder der Onlinemigration (Azure Database Migration Service, Transaktionsreplikation) migrieren möchten.
- [Überwachen Sie Anwendungen](#monitor-applications), um sicherzustellen, dass Sie über die erwartete Leistung verfügen.

> [!NOTE]
> Informationen zum Migrieren einer einzelnen Datenbank zu einer Einzeldatenbank oder einem Pool für elastische Datenbanken finden Sie unter [Migrieren einer SQL Server-Datenbank zu Azure SQL-Datenbank in der Cloud](../database/migrate-to-database-from-sql-server.md).

## <a name="assess-sql-managed-instance-compatibility"></a>Bewerten der SQL Managed Instance-Kompatibilität

Stellen Sie zunächst fest, ob SQL Managed Instance mit den Datenbankanforderungen Ihrer Anwendung kompatibel ist. SQL Managed Instance wurde konzipiert, um eine einfache Migration per Lift & Shift für die meisten bestehenden Anwendungen zu ermöglichen, die SQL Server verwenden. Es kann jedoch vorkommen, dass Sie Features oder Funktionen benötigen, die noch nicht unterstützt werden, und die Kosten für die Implementierung eines Workarounds zu hoch sind.

Verwenden Sie den [Datenmigrations-Assistenten](/sql/dma/dma-overview), um mögliche Kompatibilitätsprobleme zu erkennen, die die Datenbankfunktionalität der Azure SQL-Datenbank beeinträchtigen. Wenn einige blockierende Probleme gemeldet werden, müssen Sie möglicherweise eine andere Option in Betracht ziehen, z. B. [SQL Server auf Azure-VMs](https://azure.microsoft.com/services/virtual-machines/sql-server/). Im Folgenden finden Sie einige Beispiele:

- Wenn Sie direkten Zugriff auf das Betriebssystem oder das Dateisystem benötigen, z. B. um Drittanbieter- oder benutzerdefinierte Agents auf demselben virtuellen SQL Server-Computer zu installieren.
- Wenn Sie unbedingt von Features abhängig sind, die noch nicht unterstützt werden, wie z. B. FileStream/FileTable, PolyBase und instanzübergreifende Transaktionen.
- Wenn Sie unbedingt bei einer bestimmten Version von SQL Server (z. B. 2012) bleiben müssen.
- Wenn Ihre Computeanforderungen wesentlich niedriger sind als bei einer verwalteten Instanz (z. B. ein virtueller Kern) und eine Datenbankkonsolidierung nicht in Frage kommt.

Wenn Sie alle erkannten Migrationshindernisse beseitigt haben und die Migration zu SQL Managed Instance fortsetzen, sollten Sie beachten, dass einige der Änderungen die Leistung Ihrer Workload beeinträchtigen können:

- Ein obligatorisches vollständiges Wiederherstellungsmodell und ein geordneter automatisierter Sicherungszeitplan können die Leistung Ihrer Workload- oder Wartungs-/ETL-Aktionen beeinträchtigen, wenn Sie regelmäßig ein einfaches/massenprotokolliertes Modell verwendet oder bedarfsgesteuerte Sicherungen gestoppt haben.
- Unterschiedliche Konfigurationen auf Server- oder Datenbankebene, etwa Ablaufverfolgungsflags oder Kompatibilitätsgrade.
- Neue Features, die Sie verwenden, etwa Transparent Database Encryption (TDE) oder Autofailover-Gruppen, können sich auf die CPU- und E/A-Nutzung-auswirken.

SQL Managed Instance garantiert eine Verfügbarkeit von 99,99 %, und zwar auch in kritischen Szenarien, sodass der durch diese Features verursachte Mehraufwand nicht verhindert werden kann. Weitere Informationen finden Sie in der Beschreibung [der Hauptursachen, die unterschiedliche Leistungen in SQL Server und Azure SQL Managed Instance verursachen können](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

### <a name="create-a-performance-baseline"></a>Erstellen einer Leistungsbaseline

Wenn Sie die Leistung Ihrer Workload in einer verwalteten Instanz mit Ihrer ursprünglichen Workload, die in SQL Server ausgeführt wird, vergleichen möchten, müssen Sie dazu eine Leistungsbaseline erstellen.

Die Leistungsbaseline ist eine Gruppe von Parametern, z. B. durchschnittliche/maximale CPU-Nutzung, durchschnittliche/maximale E/A-Latenz von Datenträgern, Durchsatz, IOPS, durchschnittliche/maximale Seitenlebenserwartung und durchschnittliche/maximale Größe von „tempdb“. Nach der Migration möchten Sie vielleicht ähnliche oder sogar bessere Parameter verwenden. Deshalb ist es wichtig, die Baselinewerte für diese Parameter zu messen und aufzuzeichnen. Zusätzlich zu Systemparametern müssen Sie einen Satz der repräsentativen Abfragen oder der wichtigsten Abfragen in Ihrer Workload auswählen und die minimale/durchschnittliche/maximale Dauer der CPU-Nutzung für die ausgewählten Abfragen messen. Anhand dieser Werte können Sie die Leistung der Workload, die auf der verwalteten Instanz ausgeführt wird, mit den ursprünglichen Werten Ihrer SQL Server-Quellinstanz vergleichen.

Die folgenden Parameter sind einige der Parameter, die Sie in Ihrer SQL Server-Instanz messen müssen:

- [Überwachen der CPU-Nutzung in Ihrer SQL Server-Instanz](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) und Aufzeichnen der durchschnittlichen und der maximalen CPU-Nutzung.
- [Überwachen der Arbeitsspeichernutzung in Ihrer SQL Server-Instanz](/sql/relational-databases/performance-monitor/monitor-memory-usage) und Bestimmen der Menge von Arbeitsspeicher, die von verschiedenen Komponenten, z. B. Pufferpool, Plancache, Columnstorepool, [In-Memory-OLTP](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017) usw., verwendet wird. Darüber hinaus sollten Sie die durchschnittlichen und maximalen Werte des Speicherleistungsindikators „Seitenlebenserwartung“ ermitteln.
- Überwachen der Datenträger-E/A-Nutzung in der SQL Server-Instanz mit der [sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)-Sicht oder mit [Leistungsindikatoren](/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Überwachen der Workload- und Abfrageleistung in Ihrer SQL Server-Instanz durch Auswerten von dynamischen Verwaltungssichten oder „Abfragespeicher“, wenn Sie von SQL Server 2016 oder einer höheren Version migrieren. Ermitteln Sie die durchschnittliche Dauer und CPU-Nutzung für die wichtigsten Abfragen in Ihrer Workload, um die entsprechenden Werte mit denen der Abfragen zu vergleichen, die in der verwalteten Instanz ausgeführt werden.

> [!Note]
> Wenn Sie Probleme mit Ihrer Workload in SQL Server feststellen, etwa hohe CPU-Nutzung, konstant hohe Speicherauslastung, tempdb- oder Parametrisierungsprobleme, sollten Sie diese Probleme in Ihrer SQL-Server-Quellinstanz beheben, bevor Sie die Baseline ermitteln und die Migration vornehmen. Ein Migrieren bekannter Probleme zu einem neuen System kann zu unerwarteten Ergebnissen führen und jeden Leistungsvergleich ungültig machen.

Als Ergebnis dieser Aktivität sollten Sie folgende dokumentierte Werte haben: durchschnittliche und maximale Werte für CPU, Arbeitsspeicher und E/A-Nutzung in Ihrem Quellsystem sowie durchschnittliche und maximale Dauer und CPU-Nutzung der dominierenden und wichtigsten Abfragen in Ihrer Workload. Sie können diese Werte später verwenden, um die Leistung Ihrer Workload in der verwalteten Instanz mit der Baselineleistung der Workload in Ihrem SQL Server-Quellinstanz zu vergleichen.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Bereitstellen für eine verwaltete Instanz mit optimaler Größe

SQL Managed Instance ist auf lokale Workloads zugeschnitten, die für den Umzug in die Cloud vorgesehen sind. Sie führen ein [neues Kaufmodell](../database/service-tiers-vcore.md) ein, das eine größere Flexibilität bei der Auswahl der richtigen Ressourcen für Ihre Workloads bietet. In der lokalen Umgebung sind Sie wahrscheinlich daran gewöhnt, diese Workloads mit physischen Kernen und E/A-Bandbreite zu dimensionieren. Das Kaufmodell für verwaltete Instanzen basiert auf virtuellen Kernen oder „V-Kernen“, wobei zusätzlicher Speicher und EA separat erhältlich sind. Das V-Kern-Modell ist eine einfachere Methode, um die Computeanforderungen in der Cloud mit dem, was Sie heute lokal verwenden, zu vergleichen. Mit diesem neuen Modell können Sie die Größe Ihrer Zielumgebung in der Cloud anpassen. Einige allgemeine Richtlinien, anhand denen Sie die richtige Dienstebene und die richtigen Merkmale auswählen können, sind hier beschrieben:

- Basierend auf der CPU-Nutzung der Baseline können Sie eine verwaltete Instanz mit derselben Anzahl von Kernen bereitstellen, die Sie in SQL Server verwenden. Dabei sollten Sie aber bedenken, dass CPU-Merkmale möglicherweise skaliert werden müssen, damit sie den [Merkmalen des virtuellen Computers entsprechen, auf dem die verwaltete Instanz installiert ist](resource-limits.md#hardware-generation-characteristics).
- Wählen Sie basierend auf der Baseline-Speicherauslastung [die Dienstebene mit übereinstimmendem Speicher](resource-limits.md#hardware-generation-characteristics) aus. Weil die Menge an Arbeitsspeicher nicht direkt ausgewählt werden kann, müssten Sie die verwaltete Instanz mit der Menge an virtuellen Kernen auswählen, die einen übereinstimmenden Arbeitsspeicher aufweisen (z. B. 5,1 GB/vCore in Gen5).
- Wählen Sie basierend auf der Baseline-E/A-Latenz des Dateisubsystems zwischen den Dienstebenen „Universell“ (Latenz größer als 5 ms) und „Unternehmenskritisch“ (Latenz kleiner als 3 ms) aus.
- Ordnen Sie basierend auf dem Baselinedurchsatz die Größe von Daten- oder Protokolldateien vorab zu, um die erwartete E/A-Leistung zu erhalten.

Im [Azure-Portal](../database/scale-resources.md) haben Sie die Möglichkeit, Compute- und Speicherressourcen zum Zeitpunkt der Bereitstellung auszuwählen und anschließend zu ändern, ohne dass es zu Downtime für Ihre Anwendung kommt:

![Dimensionierung einer verwalteten Instanz](./media/migrate-to-instance-from-sql-server/managed-instance-sizing.png)

Informationen zum Erstellen der VNET-Infrastruktur und einer verwalteten Instanz finden Sie unter [Erstellen einer verwalteten Instanz](instance-create-quickstart.md).

> [!IMPORTANT]
> Es ist wichtig, dass Sie Ihr Ziel-VNET und -subnetz in Übereinstimmung mit den [VNET-Anforderungen für verwaltete Instanzen](connectivity-architecture-overview.md#network-requirements) halten. Jede Inkompatibilität kann Sie daran hindern, neue Instanzen anzulegen oder bereits erstellte Instanzen zu verwenden. Erfahren Sie mehr über das [Erstellen neuer](virtual-network-subnet-create-arm-template.md) und [Konfigurieren vorhandener](vnet-existing-add-subnet.md) Netzwerke.

## <a name="select-a-migration-method-and-migrate"></a>Auswählen einer Migrationsmethode und Migration

SQL Managed Instance zielt auf Benutzerszenarien ab, die eine Massenmigration der Datenbank von lokalen oder Azure VM-Datenbankimplementierungen erfordern. Sie sind die optimale Wahl, wenn es darum geht, das Back-End der Anwendungen, die regelmäßig Funktionalitäten auf Instanzebene und/oder über Datenbanken hinweg nutzen, per Lift & Shift zu migrieren. Wenn dies Ihr Szenario ist, können Sie eine ganze Instanz in eine entsprechende Umgebung in Azure verschieben, ohne dass Sie Ihre Anwendungen neu strukturieren müssen.

Zum Verschieben von SQL-Instanzen müssen Sie Folgendes sorgfältig planen:

- Die Migration aller Datenbanken, die zusammengeführt werden müssen (diejenigen, die auf derselben Instanz ausgeführt werden).
- Die Migration von Objekten auf Instanzebene, von denen Ihre Anwendung abhängt, wie Anmeldeinformationen, SQL-Agent-Aufträge und -Operatoren sowie Trigger auf Serverebene.

SQL Managed Instance ist ein verwalteter Dienst, der es Ihnen ermöglicht, einige der regulären DBA-Aktivitäten während deren Einbindung an die Plattform zu delegieren. Daher müssen einige Daten auf Instanzebene nicht migriert werden, wie z.B. Wartungsaufträge für regelmäßige Sicherungen oder Always On-Konfigurationen, da [Hochverfügbarkeit](../database/high-availability-sla.md) integriert ist.

SQL Managed Instance unterstützt die folgenden Datenbankmigrationsoptionen (derzeit sind dies die einzigen unterstützten Migrationsmethoden):

- Azure Database Migration Service: Migrationen nahezu ohne Ausfallzeiten
- Native `RESTORE DATABASE FROM URL`: nutzt native Sicherungen von SQL Server und ist mit einer gewissen Ausfallzeit verbunden

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[Azure Database Migration Service](../../dms/dms-overview.md) ist ein vollständig verwalteter Dienst, der die nahtlose Migration von mehreren Datenbankquellen zu Azure-Datenplattformen bei minimaler Ausfallzeit ermöglicht. Dieser Dienst optimiert die Aufgaben, die erforderlich sind, um bestehende Drittanbieter- und SQL Server-Datenbanken in Azure zu verschieben. Zu den Bereitstellungsoptionen während der öffentlichen Vorschau gehören Datenbanken in Azure SQL-Datenbank und SQL Server-Datenbanken auf einem virtuellen Azure-Computer. Database Migration Service ist die empfohlene Migrationsmethode für Ihre Unternehmensworkloads.

Wenn Sie SQL Server Integration Services (SSIS) in lokalem SQL Server verwenden, unterstützt Database Migration Service noch nicht die Migration des SSIS-Katalogs (SSISDB), in dem SSIS-Pakete gespeichert werden. Sie können jedoch die Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF) bereitstellen, mit der eine neue SSISDB in einer verwalteten Instanz erstellt wird. Anschließend können Sie dort das Paket erneut bereitstellen. Weitere Informationen finden Sie unter [Erstellen von Azure-SSIS IR in Azure Data Factory](../../data-factory/create-azure-ssis-integration-runtime.md).

Weitere Informationen zu diesem Szenario und zu Konfigurationsschritten für Database Migration Service finden Sie unter [Migrieren Ihrer lokalen Datenbank zu einer verwalteten Instanz mit Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Native RESTORE FROM URL-Option

Das Wiederherstellen mit der RESTORE-Option aus nativen Sicherungen (BAK-Dateien), die von einer SQL Server-Instanz erstellt wurden und in [Azure Storage](https://azure.microsoft.com/services/storage/) verfügbar sind, ist eine der wichtigsten Funktionen von SQL Managed Instance, die eine schnelle und einfache Offlinedatenbankmigration ermöglicht.

Das folgende Diagramm bietet einen allgemeinen Überblick über den Prozess:

![Im Diagramm weist ein Pfeil mit der Beschriftung „SICHERN/zu URL hochladen“ zu Azure Storage. Ein weiterer Pfeil mit der Beschriftung „RESTORE FROM URL“ weist von Azure Storage zu einer verwalteten SQL-Instanz.](./media/migrate-to-instance-from-sql-server/migration-flow.png)

In der folgenden Tabelle finden Sie weitere Informationen über die Methoden, die Sie je nach Version der ausgeführten SQL Server-Quellinstanz verwenden können:

|Schritt|SQL-Engine und -Version|Sicherungs- und Wiederherstellungsmethode|
|---|---|---|
|Sicherung auf Azure Storage legen|Vor 2012 SP1 CU2|Direkter Upload der BAK-Datei in Azure Storage|
||2012 SP1 CU2 – 2016|Direkte Sicherung mit veralteter [WITH CREDENTIAL](/sql/t-sql/statements/restore-statements-transact-sql)-Syntax|
||Ab 2016|Direkte Sicherung mit [WITH SAS CREDENTIAL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Wiederherstellung aus Azure Storage in eine verwaltete Instanz|[RESTORE FROM URL mit SAS CREDENTIAL](restore-sample-database-quickstart.md)|

> [!IMPORTANT]
>
> - Beim Migrieren einer Datenbank, die durch [Transparent Data Encryption](../database/transparent-data-encryption-tde-overview.md) geschützt ist, zu einer verwalteten Instanz mithilfe der nativen Wiederherstellungsoption muss das entsprechende Zertifikat von der lokalen oder der Azure VM-SQL Server-Instanz vor der Wiederherstellung der Datenbank migriert werden. Eine ausführliche Anleitung finden Sie unter [Migrieren des Zertifikats einer durch TDE geschützten Datenbank zu einer verwalteten Azure SQL-Instanz](tde-certificate-migrate.md).
> - Das Wiederherstellen von Systemdatenbanken wird nicht unterstützt. Um (in Master- oder msdb-Datenbanken gespeicherte) Objekte auf Instanzebene zu migrieren, wird empfohlen, für diese T-SQL-Skripts zu erstellen und in der Zielinstanz auszuführen.

Einen Schnellstart, der zeigt, wie Sie eine Datenbanksicherung mithilfe von SAS-Anmeldeinformationen in einer verwalteten Instanz wiederherstellen, finden Sie unter [Wiederherstellen einer Datenbanksicherung in einer verwalteten Instanz](restore-sample-database-quickstart.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

## <a name="monitor-applications"></a>Überwachen von Anwendungen

Nachdem Sie die Migration zu einer verwalteten Instanz abgeschlossen haben, sollten Sie das Anwendungsverhalten und die Anwendungsleistung Ihrer Workload nachverfolgen. Dieser Prozess umfasst die folgenden Aktivitäten:

- [Vergleichen der Leistung der Workload, die in der verwalteten Instanz ausgeführt wird,](#compare-performance-with-the-baseline) mit der [Leistungsbaseline, die Sie für die SQL Server-Quellinstanz erstellt haben](#create-a-performance-baseline)
- Kontinuierliches [Überwachen der Leistung Ihrer Workload](#monitor-performance), um mögliche Probleme und Verbesserungen zu erkennen

### <a name="compare-performance-with-the-baseline"></a>Vergleichen der Leistung mit der Baseline

Nach einer erfolgreichen Migration besteht die erste erforderliche Aktivität darin, die Leistung der Workload mit der Baselineleistung der Workload zu vergleichen. Ziel dieser Aktivität ist es, zu bestätigen, dass die Workloadleistung in Ihrer verwalteten Instanz Ihre Anforderungen erfüllt.

Bei einer Datenbankmigration zu einer verwalteten Instanz werden in den meisten Fällen die Einstellungen und der ursprüngliche Kompatibilitätsgrad der Datenbank beibehalten. Die ursprünglichen Einstellungen werden nach Möglichkeit übernommen, um das Risiko einiger Leistungseinbußen im Vergleich zu Ihrer SQL Server-Quellinstanz zu verringern. Wenn der Kompatibilitätsgrad einer Benutzerdatenbank vor der Migration 100 oder höher war, bleibt er auch nach der Migration unverändert. Lag der Kompatibilitätsgrad einer Benutzerdatenbank vor der Migration bei 90, wird er in der aktualisierten Datenbank auf 100 festgelegt, den niedrigsten unterstützten Kompatibilitätsgrad in einer verwalteten Instanz. Der Kompatibilitätsgrad von Systemdatenbanken ist 140. Da eine Migration zu einer verwalteten Instanz tatsächlich einem Migrieren zur neuesten Version der SQL Server-Datenbank-Engine entspricht, sollten Sie sich bewusst sein, dass Sie die Leistung Ihrer Workload erneut testen müssen, um einige überraschende Leistungsprobleme zu vermeiden.

Eine Voraussetzung hierfür ist, dass Sie die folgenden Aktivitäten ausgeführt haben:

- Passen Sie Ihre Einstellungen in der verwalteten Instanz entsprechend den Einstellungen der SQL Server-Quellinstanz an, indem Sie verschiedene Instanz-, Datenbank- und tempdb-Einstellungen sowie Konfigurationen untersuchen. Sie sollten keine Einstellungen, etwa Kompatibilitätsgrade oder Verschlüsselung, ändern, bevor Sie den ersten Leistungsvergleich durchführen. Andernfalls müssen Sie das Risiko akzeptieren, dass sich einige der von Ihnen aktivierten neuen Features auf einige Abfragen auswirken können. Um Migrationsrisiken zu minimieren, sollten Sie den Kompatibilitätsgrad der Datenbank erst nach der Leistungsüberwachung ändern.
- Setzen Sie die [Richtlinien für bewährte Speichermethoden für „Universell“](https://techcommunity.microsoft.com) um, so z. B. Vorabzuordnung der Größe der Dateien, um eine bessere Leistung zu erzielen.
- Erfahren Sie mehr über die [wichtigsten Umgebungsunterschiede, die zu Leistungsunterschieden zwischen einer verwalteten Instanz und SQL Server führen können](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/), und bestimmen Sie die Risiken, die die Leistung beeinträchtigen können.
- Belassen Sie „Abfragespeicher“ und „Automatische Optimierung“ für Ihre verwaltete Instanz aktiviert. Diese Features ermöglichen es Ihnen, die Workloadleistung zu messen und die möglichen Leistungsprobleme automatisch zu beheben. Erfahren Sie, wie Sie „Abfragespeicher“ als optimales Tool verwenden, um Informationen über die Workloadleistung vor und nach einer Änderung des Datenbankkompatibilitätsgrads zu erhalten. Erläuterungen hierzu finden Sie unter [Aufrechterhalten einer stabilen Leistung während des Upgrades auf eine neuere SQL Server-Version](/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
Sobald Sie die Umgebung vorbereitet haben, die so weit wie möglich mit Ihrer lokalen Umgebung vergleichbar ist, können Sie damit beginnen, Ihre Workload auszuführen und die Leistung zu messen. Der Messungsumfang sollte genau die Parameter enthalten, die Sie [während der Erstellung der Leistungsbaseline Ihrer Workload für die SQL Server-Quellinstanz](#create-a-performance-baseline) gemessen haben.
Danach vergleichen Sie die Leistungsparameter mit den Baselinewerten, und ermitteln Sie auffallende Unterschiede.

> [!NOTE]
> In vielen Fällen ist es nicht möglich, eine genau übereinstimmende Leistung in einer verwalteten Instanz und in SQL Server zu erreichen. Azure SQL Managed Instance ist eine SQL Server-Datenbank-Engine, aber die Infrastruktur und die Hochverfügbarkeitskonfiguration einer verwalteten Instanz bedingen möglicherweise einige Unterschiede. Sie erwarten wahrscheinlich, dass einige Abfragen schneller sind, während andere möglicherweise langsamer sind. Das Ziel des Vergleichs ist es, zu überprüfen, ob die Workloadleistung in der verwalteten Instanz mit der Leistung in SQL Server (im Durchschnitt) übereinstimmt, und festzustellen, ob es besonders wichtige Abfragen gibt, deren Leistung nicht der ursprünglichen Leistung entspricht.

Das Ergebnis des Leistungsvergleichs könnte wie folgt aussehen:

- Die Workloadleistung in der verwalteten Instanz ist gleich der oder besser als die Workloadleistung in SQL Server. In diesem Fall haben Sie bestätigt, dass die Migration erfolgreich ist.
- Die meisten Leistungsparameter und die Abfragen in der Workload funktionieren ordnungsgemäß, es gibt aber einige Ausnahmen mit beeinträchtigter Leistung. In diesem Fall müssen Sie die Unterschiede und deren Wichtigkeit ermitteln. Gibt es einige wichtige Abfragen mit verminderter Leistung, sollten Sie untersuchen, ob die zugrunde liegenden SQL-Pläne geändert wurden oder ob die Abfragen an einige Ressourcengrenzen stoßen. Abhilfe könnte in diesem Fall sein, einige Hinweise auf die wichtigen Abfragen (z.B. geänderte Kompatibilitätsstufe, Legacy-Kardinalitätsschätzung) entweder direkt oder über Planhinweislisten anzuwenden, wozu Statistiken und Indizes erstellt oder neu erstellt werden, die sich auf die Pläne auswirken können.
- Die meisten der Abfragen sind in einer verwalteten Instanz im Vergleich zu Ihrer SQL Server-Quellinstanz langsamer. In diesem Fall sollten Sie die Hauptursachen für die Unterschiede ermitteln, etwa das [Erreichen einiger Ressourcengrenzwerte](resource-limits.md#service-tier-characteristics) wie E/A-Grenzwerte, Arbeitsspeichergrenzwert, Grenzwert der Instanzprotokollrate usw. Gibt es keine Ressourcengrenzwerte, die den Unterschied verursachen können, sollten Sie den Kompatibilitätsgrad der Datenbank oder Datenbankeinstellungen (etwa Legacy-Kardinalitätsschätzung) ändern und den Test neu starten. Lesen Sie die Empfehlungen, die von den Sichten „Verwaltete Instanz“ oder „Abfragespeicher“ bereitgestellt werden, um die Abfragen zu bestimmen, für die sich die Leistung verschlechtert hat.

> [!IMPORTANT]
> Azure SQL Managed Instance hat ein integriertes automatische Plankorrekturfeature, das standardmäßig aktiviert ist. Dieses Feature stellt sicher, dass Abfragen, die bisher einwandfrei funktioniert haben, in Zukunft nicht beeinträchtigt werden. Aktivieren Sie dieses Feature, und führen Sie die Workload lange genug mit den alten Einstellungen aus, bevor Sie Einstellungen ändern. So ist sichergestellt, dass die verwaltete Instanz über die Baselineleistung und die Pläne Bescheid weiß.

Nehmen Sie die Änderung der Parameter oder ein Upgrade der Dienstebenen vor, um sich der optimalen Konfiguration zu nähern, bis Sie die Workloadleistung erhalten, die Ihren Anforderungen entspricht.

### <a name="monitor-performance"></a>Überwachen der Leistung

SQL Managed Instance bietet eine Menge an erweiterten Tools zur Überwachung und Problembehandlung, die Sie zur Überwachung der Leistung bei Ihrer Instanz verwenden sollten. Zu den Parametern, die Sie überwachen müssen, gehören die folgenden:

- CPU-Nutzung auf der Instanz, um zu bestimmen, ob die Anzahl der bereitgestellten virtuellen Kerne Ihrer Workload entspricht.
- Seitenlebenserwartung auf Ihrer verwalteten Instanz, um zu bestimmen, [ ob Sie zusätzlichen Speicher benötigen](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Statistiken wie `INSTANCE_LOG_GOVERNOR` oder `PAGEIOLATCH`, die Sie informieren, ob es Speicher-E/A-Probleme gibt – insbesondere auf der Ebene „Universell“, auf der Sie Dateien möglicherweise vorab zuordnen müssen, um eine bessere E/A-Leistung zu erhalten.

## <a name="leverage-advanced-paas-features"></a>Nutzen erweiterter PaaS-Features

Sobald Sie sich auf einer vollständig verwalteten Plattform befinden und sich vergewissert haben, dass die Workloadleistungen mit der SQL Server-Workloadleistung übereinstimmen, können Sie die Vorteile nutzen, die automatisch als Teil des Diensts bereitgestellt werden.

Selbst wenn Sie während der Migration keine Änderungen an der verwalteten Instanz vornehmen, ist es sehr wahrscheinlich, dass Sie im laufenden Betrieb Ihrer Instanz einige der neuen Features aktivieren, um die Vorteile der letzten Verbesserungen der Datenbank-Engine zu nutzen. Einige Änderungen werden erst übernommen, wenn der [Kompatibilitätsgrad der Datenbank geändert wurde](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database).

Beispielsweise müssen Sie keine Sicherungen für den Managed Instance-Dienst erstellen, da dieser automatische Sicherungen für Sie durchführt. Sie müssen sich auch nicht mehr darum kümmern, Sicherungen zu planen, zu erstellen und zu verwalten. SQL Managed Instance bietet Ihnen die Möglichkeit, mithilfe der [Zeitpunktwiederherstellung](../database/recovery-using-backups.md#point-in-time-restore) eine Wiederherstellung gemäß jedem beliebigen Zeitpunkt innerhalb des Aufbewahrungszeitraums durchzuführen. Darüber hinaus müssen Sie sich keine Gedanken über die Einrichtung von [Hochverfügbarkeit](../database/high-availability-sla.md) machen, da diese integriert ist.

Um die Sicherheit zu erhöhen, sollten Sie [Azure Active Directory-Authentifizierung](../database/security-overview.md), [Überwachung](auditing-configure.md), [Bedrohungserkennung](../database/azure-defender-for-sql.md), [Sicherheit auf Zeilenebene](/sql/relational-databases/security/row-level-security) und [dynamische Datenmaskierung](/sql/relational-databases/security/dynamic-data-masking) verwenden.

Zusätzlich zu erweiterten Verwaltungs- und Sicherheitsfeatures stellt die verwaltete Instanz eine Reihe von erweiterten Tools bereit, mit denen Sie Ihre [Workload überwachen und optimieren](../database/monitor-tune-overview.md) können. [Azure SQL-Analyse](../../azure-monitor/insights/azure-sql.md) ermöglicht Ihnen, eine große Menge verwalteter Instanzen zu überwachen und die Überwachung einer großen Anzahl von Instanzen und Datenbanken zu zentralisieren. [Automatische Optimierung](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) in verwalteten Instanzen bewirkt, dass die Ausführungsleistung Ihres SQL-Plans kontinuierlich statistisch überwacht wird und die erkannten Leistungsprobleme automatisch behoben werden.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu Azure SQL Managed Instance finden Sie unter [Was ist Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md).
- Ein Tutorial zu einer Wiederherstellung aus einer Sicherung finden Sie unter [Erstellen einer verwalteten Instanz](instance-create-quickstart.md).
- Ein Tutorial zur Migration mit Database Migration Service finden Sie unter [Migrieren Ihrer lokalen Datenbank zu Azure SQL Managed Instance mit Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).