---
title: 'SQL Server zu SQL Managed Instance: Leistungsanalyse'
description: Hier erfahren Sie, wie Sie eine Leistungsbaseline bei der Migration Ihrer SQL Server-Datenbanken zu Azure SQL Managed Instance erstellen und vergleichen.
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mokabiru
ms.date: 11/06/2020
ms.openlocfilehash: c47e4c1278f222feac35a2c6ab0b067c916c0217
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326844"
---
# <a name="migration-performance-sql-server-to-sql-managed-instance-performance-analysis"></a>Migrationsleistung: Leistungsanalyse für Migrationen von SQL Server zu SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Erstellen Sie eine Leistungsbaseline zum Vergleichen der Leistung Ihrer Workload in einer SQL Managed Instance-Instanz mit Ihrer ursprünglich in SQL Server ausgeführten Workload. 

## <a name="create-a-baseline"></a>Erstellen einer Baseline

Im Idealfall ist die Leistung nach einer Migration ähnlich oder besser. Es ist also wichtig, Baselineleistungswerte für die Quelle zu messen und aufzuzeichnen und diese dann mit der Zielumgebung zu vergleichen. Bei einer Leistungsbaseline handelt es sich um mehrere Parameter, die Sie für Ihre durchschnittliche Workload in Ihrer Quelle definieren. 

Wählen Sie dafür Abfragen aus, die Sie für wichtig erachten, sowie Stellvertreter für Ihre Geschäftsworkloads. Messen und dokumentieren Sie die minimale, durchschnittliche und die maximale Dauer sowie die CPU-Auslastung dieser Abfragen sowie Leistungsmetriken zum Quellserver, z. B. durchschnittliche und maximale CPU-Auslastung, durchschnittliche und maximale EA-Latenz für Datenträger, Durchsatz, durchschnittliche und maximale Seitenlebenserwartung und durchschnittliche und maximale Größe von tempdb. 

Die folgenden Ressourcen unterstützen Sie beim Definieren einer Leistungsbaseline: 

   - [Überwachen der CPU-Auslastung in SQL Server und Azure SQL](https://techcommunity.microsoft.com/t5/azure-sql-database/monitor-cpu-usage-on-sql-server-and-azure-sql/ba-p/680777#M131)
   - [Überwachen der Speicherauslastung](/sql/relational-databases/performance-monitor/monitor-memory-usage)  und Bestimmen der Menge an Arbeitsspeicher, die von verschiedenen Komponenten, z. B. Pufferpool, Plancache, Columnstorepool oder  [In-Memory-OLTP](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage) verwendet wird. Darüber hinaus sollten Sie die durchschnittlichen und maximalen Werte des Speicherleistungsindikators „Seitenlebenserwartung“ ermitteln. 
   - Überwachen der Datenträger-E/A-Nutzung in der SQL Server-Quellinstanz mit der  [sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)-Sicht oder mit  [Leistungsindikatoren](/sql/relational-databases/performance-monitor/monitor-disk-usage) 
   - Überwachen der Workload- und Abfrageleistung durch Auswerten von dynamischen Verwaltungssichten oder Abfragespeicher, wenn Sie von SQL Server 2016 oder höher migrieren. Identifizieren Sie die durchschnittliche Dauer und die CPU-Auslastung der wichtigsten Abfragen Ihrer Workload. 

Sämtliche Leistungsprobleme auf der SQL Server-Quellinstanz sollten vor der Migration beseitigt werden. Ein Migrieren bekannter Probleme zu einem neuen System kann zu unerwarteten Ergebnissen führen und jeden Leistungsvergleich ungültig machen. 


## <a name="compare-performance"></a>Vergleichen der Leistung 

Nachdem Sie eine Baseline definiert haben, können Sie eine ähnliche Workloadleistung auf der SQL Managed Instance-Zielinstanz vergleichen. Aus Gründen der Genauigkeit ist es wichtig, dass die SQL Managed Instance-Umgebung mit der SQL Server-Umgebung möglichst weitgehend vergleichbar ist. 

Es gibt Unterschiede bei der SQL Managed Instance-Infrastruktur, die eine exakt übereinstimmende Leistung unwahrscheinlich machen. Manche Abfragen werden möglicherweise schneller als erwartet ausgeführt, andere dahingegen langsamer. Das Ziel dieses Vergleichs ist es, zu überprüfen, ob die Workloadleistung in der verwalteten Instanz mit der Leistung in SQL Server (im Durchschnitt) übereinstimmt, und festzustellen, ob es besonders wichtige Abfragen gibt, deren Leistung nicht der ursprünglichen Leistung entspricht. 

Ein Vergleich der Leistung führt vermutlich zu den folgenden Ergebnissen: 

- Die Workloadleistung in der verwalteten Instanz ist gleich der oder besser als die Workloadleistung in Ihrer SQL Server-Quellinstanz. In diesem Fall haben Sie bestätigt, dass die Migration erfolgreich ist. 

- Die meisten Leistungsparameter und die Abfragen in der Workload funktionieren ordnungsgemäß, es gibt aber einige Ausnahmen mit beeinträchtigter Leistung. In diesem Fall müssen Sie die Unterschiede und deren Wichtigkeit ermitteln. Gibt es einige wichtige Abfragen mit verminderter Leistung, sollten Sie untersuchen, ob die zugrunde liegenden SQL-Pläne geändert wurden oder ob die Abfragen an Ressourcengrenzen stoßen. Abhilfe schafft die Anwendung einiger Hinweise zu wichtigen Abfragen (z. B. Änderung des Kompatibilitätsgrads, Schätzer für die vorherige Kardinalität), die entweder direkt oder über Planhinweislisten gegeben werden. Sorgen Sie dafür, dass Statistiken und Indizes in beiden Umgebungen aktuell und äquivalent sind. 

- Die meisten der Abfragen sind in einer verwalteten Instanz im Vergleich zu Ihrer SQL Server-Quellinstanz langsamer. In diesem Fall sollten Sie die Hauptursachen für die Unterschiede ermitteln, etwa das  [Erreichen einiger Ressourcengrenzen](../../managed-instance/resource-limits.md#service-tier-characteristics) wie E/A-Grenzwerte, Arbeitsspeichergrenzwerte oder Grenzwerte der Instanzprotokollrate. Gibt es keine Ressourcengrenzwerte, die den Unterschied verursachen können, sollten Sie den Kompatibilitätsgrad der Datenbank oder Datenbankeinstellungen (etwa eine Schätzung der bisherigen Kardinalität) ändern und den Test neu starten. Lesen Sie die Empfehlungen, die von den Sichten „Verwaltete Instanz“ oder „Abfragespeicher“ bereitgestellt werden, um die Abfragen zu bestimmen, für die sich die Leistung verschlechtert hat. 

SQL Managed Instance verfügt über ein integriertes automatisches Plankorrekturfeature, das standardmäßig aktiviert ist. Dieses Feature stellt sicher, dass Abfragen, die bisher einwandfrei funktioniert haben, in Zukunft nicht beeinträchtigt werden. Wenn dieses Feature nicht aktiviert ist, führen Sie die Workload mit den alten Einstellungen aus, sodass SQL Managed Instance Informationen zur Leistungsbaseline erhalten kann. Aktivieren Sie das Feature dann, und führen Sie die Workload noch mal mit den neuen Einstellungen aus. 

Nehmen Sie Änderungen an den Parametern Ihres Tests vor, oder führen Sie ein Upgrade auf höhere Dienstebenen durch, um die optimale Konfiguration für die Workloadleistung nutzen zu können, die Ihren Anforderungen entspricht. 

## <a name="monitor-performance"></a>Überwachen der Leistung 

SQL Managed Instance bietet erweiterte Tools zur Überwachung und Problembehandlung, die Sie zur Überwachung der Leistung bei Ihrer Instanz verwenden sollten. Unten finden Sie einige der wichtigsten zu überwachenden Metriken: 

- CPU-Nutzung auf der Instanz, um zu bestimmen, ob die Anzahl der bereitgestellten virtuellen Kerne Ihrer Workload entspricht. 
- Seitenlebenserwartung für Ihre verwaltete Instanz, um zu bestimmen, ob Sie [zusätzlichen Arbeitsspeicher](https://techcommunity.microsoft.com/t5/azure-sql-database/do-you-need-more-memory-on-azure-sql-managed-instance/ba-p/563444) benötigen
-  Statistiken wie INSTANCE_LOG_GOVERNOR oder PAGEIOLATCH, die Sie informieren, ob es Speicher-E/A-Probleme gibt – insbesondere auf der Ebene „Universell“, auf der Sie Dateien möglicherweise vorab zuordnen müssen, um eine bessere E/A-Leistung zu erhalten 


## <a name="considerations"></a>Überlegungen  

Beachten Sie beim Vergleichen der Leistung Folgendes: 

- Die Einstellungen stimmen zwischen Quelle und Ziel überein. Überprüfen Sie, ob die verschiedenen Einstellungen für Instanz, Datenbank und tempdb zwischen den beiden Umgebungen äquivalent sind. Unterschiede bei den Konfigurationen, Kompatibilitätsgraden, Verschlüsselungseinstellungen oder Ablaufverfolgungsflags können alle die Leistung beeinträchtigen. 

- Der Speicher ist gemäß [Best Practices](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525) konfiguriert. Bei der Dienstebene „Universell“ müssen Sie beispielsweise die Größe der Dateien vorab zuordnen, um die Leistung zu steigern. 

- Es bestehen [wichtige Umgebungsunterschiede](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/), die zu Leistungsunterschieden zwischen einer verwalteten Instanz und SQL Server führen können. Ermitteln Sie Risiken, die für Ihre Umgebung eine Rolle spielen und zu einem Leistungsproblem führen könnten. 

- Abfragespeicher und automatische Anpassungen sollten für Ihre SQL Managed Instance-Instanz aktiviert sein, da sie Sie dabei unterstützen, die Workloadleistung zu messen und mögliche Leistungsprobleme automatisch zu beheben. 



## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Optimieren Ihrer neuen Azure SQL Managed Instance-Umgebung finden Sie in den folgenden Artikeln: 

- [Ermitteln der Gründe für die Unterschiede bei der Workloadleistung zwischen Azure SQL Managed Instance und SQL Server](https://medium.com/azure-sqldb-managed-instance/what-to-do-when-azure-sql-managed-instance-is-slower-than-sql-server-dd39942aaadd)
- [Hauptursache für Leistungsunterschiede zwischen SQL Managed Instance und SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)
- [Best Practices für die Speicherleistung und Überlegungen zu Azure SQL Managed Instance (Universell)](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)
- [Leistungsüberwachung in Echtzeit für verwaltete Azure SQL-Datenbank-Instanzen](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)