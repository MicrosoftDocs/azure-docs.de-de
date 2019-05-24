---
title: In-Memory-Technologien von Azure SQL-Datenbank | Microsoft-Dokumentation
description: In-Memory-Technologien von Azure SQL-Datenbank verbessern die Leistung von Transaktions- und Analyseworkloads erheblich.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/19/2019
ms.openlocfilehash: 5681b5aa46acc1192675da0b1cceee596dfa0105
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799890"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Optimieren der Leistung mithilfe von In-Memory-Technologien in SQL-Datenbank

In-Memory-Technologien in Azure SQL-Datenbank können zur Verbesserung der Leistung Ihrer Anwendung beitragen und potenziell Kosten für Ihre Datenbank verringern. 

## <a name="when-to-use-in-memory-technologies"></a>Verwenden von In-Memory-Technologien

Durch Verwenden von In-Memory-Technologien in Azure SQL-Datenbank können Sie Leistungsverbesserungen bei verschiedenen Workloads erzielen:

- **Transaktionsverarbeitung** (Online Transactional Processing, OLTP), bei der die meisten Anforderungen einen kleineren Satz Daten lesen oder aktualisieren (z.B. CRUD-Vorgänge).
- **Analytische Verarbeitung** (Online Analytical Processing, OLAP), bei der die meisten Abfragen komplexe Berechnungen für die Berichterstellung umfassen und eine bestimmte Anzahl von Abfragen Daten in vorhandene Tabellen lädt oder daran anfügt (so genanntes Massenladen) oder Daten aus Tabellen löscht. 
- **Gemischte Verarbeitung** (Hybrid Transaction/Analytical Processing, HTAP), bei der sowohl OLTP- als auch OLAP-Abfragen für den gleichen Satz Daten ausgeführt werden.

In-Memory-Technologien können die Leistung dieser Workloads verbessern, indem sie die zu verarbeitenden Daten im Arbeitsspeicher behalten, eine native Kompilierung der Abfragen verwenden oder eine erweiterte Verarbeitung wie z.B. Batchprozesse und SIMD-Anweisungen ausführen, die in der zugrunde liegenden Hardware verfügbar sind. 

## <a name="overview"></a>Übersicht

Azure SQL-Datenbank verfügt über die folgenden In-Memory-Technologien:
- *[In-Memory-OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)* erhöht die Anzahl von Transaktionen pro Sekunde und reduziert die Latenz für die Transaktionsverarbeitung. Szenarien, die von In-Memory-OLTP profitieren sind: hoher Durchsatz bei der Transaktionsverarbeitung z.B. Handel treiben, Spielen, Datenerfassung von Ereignissen oder IoT-Geräten, Zwischenspeichern, Laden von Daten, temporäre Tabellen und Szenarien mit Tabellenvariablen.
- *Gruppierte Columnstore-Indizes* reduzieren den Speicherplatzbedarf (bis um das Zehnfache) und verbessern die Leistung für Berichts- und Analyseabfragen. Verwenden Sie sie mit Faktentabellen in Ihren Data Marts, um mehr Daten in Ihrer Datenbank zu speichern und die Leistung zu verbessern. Sie können sie auch mit Verlaufsdaten in der Betriebsdatenbank verwenden, um bis zu zehnmal mehr Daten zu archivieren und abfragen zu können.
- *Nicht gruppierte Columnstore-Indizes* für HTAP helfen beim Gewinnen von Einblicken in Echtzeit in Ihr Geschäft, indem Sie die Betriebsdatenbank direkt abfragen, ohne einen aufwendigen ETL-Prozess (Extrahieren, Transformieren, Laden) ausführen zu müssen und darauf zu warten, dass das Data Warehouse aufgefüllt wird. Nicht geclusterte Columnstore-Indizes sorgen für eine schnelle Ausführung von Analyseabfragen in der OLTP-Datenbank und reduzieren gleichzeitig die Auswirkungen auf die Betriebsworkload.
- *Speicheroptimierte geclusterte Columnstore-Indizes* für HTAP ermöglichen eine schnelle Transaktionsverarbeitung sowie die sehr schnelle *gleichzeitige* Ausführung von Analyseabfragen derselben Daten.

Sowohl Columnstore-Indizes als auch In-Memory-OLTP gehören seit 2012 bzw. 2014 zum Funktionsumfang von SQL Server. Azure SQL-Datenbank und SQL Server weisen dieselbe Implementierung von In-Memory-Technologien auf. In Zukunft werden neue Funktionen für diese Technologien zunächst in Azure SQL-Datenbank geboten, ehe sie in SQL Server verfügbar sind.

## <a name="benefits-of-in-memory-technology"></a>Vorteile der In-Memory-Technologie

Aufgrund der effizienteren Abfrage- und Transaktionsverarbeitung tragen In-Memory-Technologien auch zur Kostensenkung bei. Sie müssen in der Regel nicht zu einem höheren Datenbanktarif wechseln, um Leistungsvorteile zu erzielen. In einigen Fällen können Sie möglicherweise sogar zu einem niedrigen Tarif wechseln und dennoch in den Genuss von Leistungsverbesserungen durch In-Memory-Technologien kommen.

Hier sind zwei Beispiele dafür, wie In-Memory-OLTP geholfen hat, die Leistung deutlich zu verbessern:

- Mithilfe von In-Memory-OLTP [konnten Quorum Business Solutions ihre Workload verdoppeln und ihre DTUs um 70 % verbessern](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).

  - DTU bedeutet *Database Transaction Unit* (Datenbank-Transaktionseinheit) und schließt eine Messung der Ressourcennutzung ein.
- Das folgende Video veranschaulicht die erhebliche Verbesserung der Ressourcennutzung anhand einer Beispielworkload: [In-Memory OLTP in Azure SQL Database](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (In-Memory OLTP in Azure SQL-Datenbank).
  - Weitere Informationen finden Sie in diesem Blogbeitrag: [Blogbeitrag „In-Memory OLTP in Azure SQL Database“](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/) (In-Memory OLTP in Azure SQL-Datenbank)

> [!NOTE]  
> In-Memory-Technologien sind für Azure SQL-Datenbank in den Tarifen „Premium“ und „Unternehmenskritisch“ sowie in Pools für elastische Datenbanken im Tarif „Premium“ verfügbar.

Im folgende Video werden potenzielle Leistungsvorteile mit In-Memory-Technologien in Azure SQL-Datenbank erklärt. Denken Sie daran, dass der Leistungsvorteil, den Sie erzielen, stets von vielen Faktoren abhängig ist. Dazu gehören die Art der Workload und Daten, die Zugriffsmuster der Datenbank usw.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

In diesem Artikel werden Aspekte von In-Memory-OLTP und Columnstore-Indizes beschrieben, die spezifisch für Azure SQL-Datenbank sind. Außerdem sind Beispiele aufgeführt:

- Sie erfahren etwas über die Auswirkung dieser Technologien auf Speicher und die Grenzwerte für die Datengröße.
- Sie erfahren, wie Sie das Verschieben von Datenbanken, die diese Technologien nutzen, zwischen verschiedenen Tarifen verwalten.
- Sie sehen zwei Beispiele, die die Verwendung von In-Memory-OLTP und Columnstore-Indizes in Azure SQL-Datenbank veranschaulichen.

Weitere Informationen finden Sie unter

- [In-Memory-OLTP: Übersicht und Verwendungsszenarien](https://msdn.microsoft.com/library/mt774593.aspx) (einschließlich Verweisen auf Fallstudien und Informationen für Einsteiger)
- [Dokumentation für In-Memory-OLTP](https://msdn.microsoft.com/library/dn133186.aspx)
- [Beschreibung von Columnstore-Indizes](https://msdn.microsoft.com/library/gg492088.aspx)
- HTAP (Hybrid Transactional/Analytical Processing), auch als [Real-time Operational Analytics](https://msdn.microsoft.com/library/dn817827.aspx) bekannt

## <a name="in-memory-oltp"></a>In-Memory-OLTP

In-Memory-OLTP-Technologie ermöglicht extrem schnelle Datenzugriffsvorgänge, indem sämtliche Daten im Arbeitsspeicher behalten werden. Die Technologie nutzt auch spezialisierte Indizes, die native Kompilierung von Abfragen sowie latchfreien Datenzugriff zur Verbesserung der Leistung der OLTP-Workload. Es gibt zwei Möglichkeiten, die In-Memory-OLTP-Daten zu organisieren:

- **Speicheroptimiertes Rowstoreformat**, in dem jede Zeile ein separates Arbeitsspeicherobjekt darstellt. Dies ist ein klassisches In-Memory-OLTP-Format, das für OLTP-Workloads mit hoher Leistung optimiert ist. Es gibt zwei Arten von speicheroptimierten Tabellen, die im speicheroptimierten Rowstoreformat verwendet werden können:
  - *Dauerhafte Tabellen* (SCHEMA_AND_DATA), bei denen die im Arbeitsspeicher platzierten Zeilen nach einem Serverneustart beibehalten werden. Diese Art von Tabellen verhält sich wie herkömmliche Rowstoretabellen, bietet aber die zusätzlichen Vorteile von speicherinternen Optimierungen.
  - *Nicht dauerhafte Tabellen* (SCHEMA_ONLY), bei denen die Zeilen nach einem Neustart nicht beibehalten werden. Diese Art von Tabelle ist für temporäre Daten (z.B. zum Austausch von temporären Tabellen) oder für Tabellen konzipiert, in die schnell Daten geladen werden müssen, bevor sie in dauerhafte Tabellen verschoben werden (so genannte Stagingtabellen).
- **Speicheroptimiertes Columnstoreformat**, bei dem Daten in Spaltenform organisiert sind. Diese Struktur wurde für HTAP-Szenarien konzipiert, in denen Sie Analyseabfragen in der gleichen Datenstruktur ausführen müssen, in der Ihre OLTP-Workload ausgeführt wird.

> [!Note]
> Die In-Memory-OLTP-Technologie wurde für Datenstrukturen entwickelt, die vollständig im Arbeitsspeicher verbleiben können. Da die In-Memory-Daten nicht auf einen Datenträger ausgelagert werden können, müssen Sie sicherstellen, dass Sie eine Datenbank mit ausreichend Arbeitsspeicher verwenden. Weitere Informationen finden Sie unter [Datengröße und Speicherkapazität für In-Memory-OLTP](#data-size-and-storage-cap-for-in-memory-oltp).

Eine kurze Einführung zu In-Memory-OLTP finden Sie hier: [Schnellstart 1: In-Memory-OLTP-Technologien für höhere T-SQL-Leistung](https://msdn.microsoft.com/library/mt694156.aspx) (Informationen für den Einstieg).

Ausführliche Videos zu den Technologien:

- [In-Memory-OLTP in Azure SQL-Datenbank](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) mit einer Veranschaulichung von Leistungsvorteilen und einer Anleitung, die zeigt, wie Sie diese Ergebnisse selbst reproduzieren können
- [Videos zu In-Memory-OLTP: Was es ist und wann/wie es verwendet wird](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/) (in englischer Sprache)

Es gibt einen programmgesteuerten Weg, um zu verstehen, ob eine vorhandene Datenbank In-Memory-OLTP unterstützt. Sie können die folgende Transact-SQL-Abfrage ausführen:
```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```
Wenn die Abfrage **1** zurückgibt, wird In-Memory-OLTP in dieser Datenbank unterstützt. Die folgenden Abfragen identifizieren alle Objekte, die entfernt werden müssen, damit eine Datenbank auf „Standard“ oder „Basic“ heruntergestuft werden kann:
```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Datengröße und Speicherkapazität für In-Memory-OLTP

In-Memory-OLTP enthält speicheroptimierte Tabellen, die zum Speichern von Benutzerdaten verwendet werden. Diese Tabellen sind müssen in den Arbeitsspeicher passen. Da Sie Arbeitsspeicher direkt im SQL-Datenbank-Dienst verwalten, arbeiten wir mit dem Konzept eines Kontingents für Benutzerdaten. Dieses Konzept wird als *In-Memory-OLTP-Speicher* bezeichnet.

Jeder unterstützte Einzeldatenbank-Tarif und jeder Tarif für Pools für elastische Datenbanken beinhaltet eine bestimmte Menge an In-Memory-OLTP-Speicher. Siehe [DTU-basierte Ressourceneinschränkungen – Einzeldatenbank](sql-database-dtu-resource-limits-single-databases.md), [DTU-basierte Ressourceneinschränkungen – Pool für elastische Datenbanken](sql-database-dtu-resource-limits-elastic-pools.md), [V-Kern-basierte Ressourceneinschränkungen – Einzeldatenbanken](sql-database-vcore-resource-limits-single-databases.md) und [V-Kern-basierte Ressourceneinschränkungen – Pool für elastische Datenbanken](sql-database-vcore-resource-limits-elastic-pools.md).

Die folgenden Elemente werden bis zu Ihrer In-Memory-OLTP-Speicherkapazitätsobergrenze angerechnet:

- Aktive Benutzerdatenzeilen in speicheroptimierten Tabellen und Tabellenvariablen. Beachten Sie, dass alte Zeilenversionen nicht bis zur Kapazitätsobergrenze angerechnet werden.
- Indizes von speicheroptimierten Tabellen.
- Betriebsmehraufwand von ALTER TABLE-Vorgängen.

Wenn Sie die Obergrenze erreichen, erhalten Sie einen Fehler vom Typ „Kontingent aufgebraucht“ und können dann keine Daten mehr einfügen oder aktualisieren. Eine Lösung dieses Fehlers besteht darin, Daten zu löschen oder zu einem höheren Datenbank- oder Pooltarif zu wechseln.

Weitere Informationen zur Überwachung der In-Memory-OLTP-Speicherverwendung und zum Konfigurieren von Benachrichtigungen, wenn die Obergrenze fast erreicht ist, finden Sie unter [Überwachen des In-Memory-OLTP-Speichers](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>Hinweis zu Pools für elastische Datenbanken

Bei Pools für elastische Datenbanken wird der In-Memory-OLTP-Speicher von allen Datenbanken im Pool gemeinsam genutzt. Aus diesem Grund kann die Verwendung in einer Datenbank andere Datenbanken möglicherweise beeinträchtigen. Zwei Lösungen dafür sind:

- Konfigurieren Sie eine `Max-eDTU` oder `MaxvCore` für Datenbanken, die niedriger als die eDTU- oder V-Kern-Anzahl für den ganzen Pool ist. Dieser Maximalwert begrenzt die In-Memory-OLTP-Speichernutzung in jeder Datenbank im Pool auf die Größe, die der eDTU-Anzahl entspricht.
- Konfigurieren Sie eine `Min-eDTU` oder `MinvCore`, die größer als 0 ist. Mit diesem Minimalwert wird sichergestellt, dass jede Datenbank im Pool die Menge des In-Memory-OLTP-Speichers zur Verfügung hat, die der konfigurierten `Min-eDTU` oder `vCore` entspricht.

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>Ändern der Dienstebenen für Datenbanken, die In-Memory-OLTP-Technologien verwenden

Sie können jederzeit ein Upgrade Ihrer Datenbank oder Instanz auf einen höheren Tarif ausführen, z.B. von „Universell“ zu „Unternehmenskritisch“ (oder von „Standard“ zu „Premium“). Es steigen nur die verfügbaren Funktionen und Ressourcen.

Ein Downgrade des Tarifs kann sich jedoch nachteilig auf Ihre Datenbank auswirken. Die Auswirkungen sind besonders deutlich, wenn Sie von „Unternehmenskritisch“ zu „Universell“ (oder von „Premium“ zu „Standard“ oder „Basic“) herabstufen und Ihre Datenbank In-Memory-OLTP-Objekte enthält. Speicheroptimierte Tabellen sind nach dem Downgrade nicht mehr verfügbar (auch wenn sie sichtbar bleiben). Die gleichen Aspekte gelten, wenn Sie den Tarif eines Pools für elastische Datenbanken herabstufen oder mit einer Datenbank mit In-Memory-Technologien zu einem Pool für elastische Datenbanken im Tarif Standard oder Basic wechseln.

> [!Important]
> In-Memory-OLTP wird in den Tarifen „Universell“, „Standard“ und „Basic“ nicht unterstützt. Daher ist es nicht möglich, eine Datenbank mit In-Memory-OLTP-Objekten in die Tarife „Standard“ oder „Basic“ zu verlagern.

Entfernen Sie alle speicheroptimierten Tabellen und Tabellentypen sowie alle nativ kompilierten T-SQL Module, bevor Sie die Datenbank auf den Standard- oder Basic-Tarif herabstufen. 

*Herunterskalieren von Ressourcen im Tarif „Unternehmenskritisch“*: Daten in speicheroptimierten Tabellen müssen in den In-Memory-OLTP-Speicher passen, der dem Tarif der Datenbank oder der verwalteten Instanz zugeordnet oder im Pool für elastische Datenbanken verfügbar ist. Wenn Sie versuchen, in einen niedrigeren Tarif zu wechseln oder die Datenbank in einen Pool zu verschieben, dem nicht genügend In-Memory-OLTP-Speicher zur Verfügung steht, misslingt der Vorgang.

## <a name="in-memory-columnstore"></a>In-Memory-Columnstore

Die In-Memory-Columnstoretechnologie ermöglicht es Ihnen, eine große Datenmenge in Tabellen zu speichern und abzufragen. Die Columnstoretechnologie verwendet ein spaltenbasiertes Datenspeicherformat und Batchabfragen, um eine bis zu 10-mal höhere Abfrageleistung in OLAP-Workloads zu erzielen also herkömmliche zeilenorientierte Speicher. Sie können im Vergleich zur unkomprimierten Datengröße außerdem eine bis zu 10-mal höhere Datenkomprimierung erzielen.
Es gibt zwei Arten von Columnstoremodellen, die Sie zum Organisieren Ihrer Daten verwenden können:

- **Geclusterter Columnstore**, in dem alle Daten in einer Tabelle im Spaltenformat organisiert werden. In diesem Modell werden alle Zeilen einer Tabelle in einem Spaltenformat platziert, das die Daten stark komprimiert und Ihnen eine schnelle Ausführung von Analyseabfragen und -berichten in der Tabelle ermöglicht. Je nach Art Ihrer Daten kann das Datenvolumen um das 10- bis 100-Fache reduziert werden. Das Modell mit Columnstoreclustern ermöglicht auch das schnelle Erfassen großer Datenmengen (Massenladen), da große Datenbatches mit mehr als 100.000 Zeilen vor dem Speichern auf dem Datenträger komprimiert werden. Dieses Modell eignet sich gut für Szenarien mit klassischen Data Warehouses. 
- **Nicht geclusterter Columnstore**, bei dem die Daten in herkömmlichen Rowstoretabellen gespeichert sind und ein Index im Columnstoreformat vorliegt, der für die Analyseabfragen verwendet wird. Dieses Modell ermöglicht Hybrid Transactional-Analytic Processing (HTAP): die Möglichkeit, leistungsfähige Echtzeitanalysen für eine Transaktionsworkload auszuführen. OLTP-Abfragen werden in einer Rowstoretabelle ausgeführt, die für den Zugriff auf eine kleine Menge an Zeilen optimiert ist. OLAP-Abfragen dagegen werden in einem Columnstore-Index ausgeführt, der sich für Überprüfungen und Analysen besser eignet. Der Azure SQL-Datenbank-Abfrageoptimierer wählt das Rowstore- oder Columnstoreformat dynamisch basierend auf der jeweiligen Abfrage aus. Nicht geclusterte Columnstore-Indizes verringern die Größe der Daten nicht, da das ursprüngliche Dataset ohne Änderung in der ursprünglichen Rowstoretabelle beibehalten wird. Der zusätzliche Columnstore-Index sollte sich jedoch in einer Größenordnung befinden, die kleiner ist als der entsprechende B-Struktur-Index.

> [!Note]
> Die In-Memory-Columnstoretechnologie speichert nur die für die Verarbeitung erforderlichen Daten im Arbeitsspeicher. Daten, die nicht in den Arbeitsspeicher passen, werden auf einem Datenträger gespeichert. Aus diesem Grund kann die Datenmenge in In-Memory-Columnstorestrukturen die Größe des verfügbaren Arbeitsspeichers überschreiten. 

Ausführliche Videos zur Technologie:

- [Columnstore-Index: Videos zur In-Memory-Analyse von der Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/) (in englischer Sprache)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Datengröße und Speicher für Columnstore-Indizes

Columnstore-Indizes brauchen nicht in den Arbeitsspeicher zu passen. Deshalb ist die einzige Begrenzung der Indexgröße die maximale Gesamtgröße der Datenbank, was in den Artikeln [DTU-basiertes Kaufmodell](sql-database-service-tiers-dtu.md) und [vCore-basiertes Kaufmodell](sql-database-service-tiers-vcore.md) dokumentiert ist.

Wenn Sie gruppierte Columnstore-Indizes verwenden, wird eine Spaltenkomprimierung für den Basistabellenspeicher verwendet. Durch diese Komprimierung kann der Speicherbedarf Ihrer Benutzerdaten erheblich reduziert werden, d.h., Sie können mehr Daten in der Datenbank speichern. Die Komprimierung kann außerdem mit [spaltenorientierter Archivierungskomprimierung](https://msdn.microsoft.com/library/cc280449.aspx#using-columnstore-and-columnstore-archive-compression) noch weiter erhöht werden. Der Grad der Komprimierung, die Sie erreichen können, hängt von der Art der Daten ab, jedoch ist eine zehnfache Komprimierung nicht ungewöhnlich.

Wenn Sie z.B. eine Datenbank mit einer maximalen Größe von 1 Terabyte (TB) haben und mithilfe von Columnstore-Indizes eine zehnfache Komprimierung erreichen, können Sie insgesamt 10 TB Benutzerdaten in der Datenbank speichern.

Bei Verwendung von nicht gruppierten Columnstore-Indizes wird die Basistabelle weiterhin im herkömmlichen Rowstore-Format gespeichert. Aus diesem Grund sind die Speichereinsparungen nicht so groß wie bei gruppierten Columnstore-Indizes. Wenn Sie jedoch mehrere herkömmliche nicht gruppierte Indizes durch einen einzelnen Columnstore-Index ersetzen, können Sie dennoch allgemeine Einsparungen beim Speicherbedarf für die Tabelle erzielen.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Ändern der Dienstebenen für Datenbanken mit Columnstore-Indizes

Das *Herabstufen einer Einzeldatenbank auf die Tarife „Basic“ oder „Standard“* ist eventuell nicht möglich, wenn der angestrebte Tarif unter S3 liegt. Columnstore-Indizes werden nur in den Tarifen „Unternehmenskritisch“ und „Premium“ sowie in den Standard-Tarifen S3 und höher unterstützt, nicht jedoch im Basic-Tarif. Nach dem Herabstufen Ihrer Datenbank auf einen nicht unterstützten Tarif bzw. auf eine nicht unterstützte Ebene ist der Columnstore-Index nicht mehr verfügbar. Das System verwaltet den Columnstore-Index, nutzt den Index jedoch nie. Wenn Sie später wieder auf einen unterstützten Tarif bzw. auf eine unterstützte Ebene heraufstufen, kann der Columnstore-Index sofort wieder genutzt werden.

Wenn Sie über einen **gruppierten** Columnstore-Index verfügen, ist die gesamte Tabelle nach dem Downgrade nicht mehr verfügbar. Aus diesem Grund wird empfohlen, alle *gruppierten* Columnstore-Indizes vor dem Herabstufen Ihrer Datenbank auf einen nicht unterstützten Tarif bzw. auf eine nicht unterstützte Ebene zu löschen.

> [!Note]
> Verwaltete Azure SQL-Datenbank-Instanz unterstützt Columnstore-Indizes in allen Tarifen.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstart 1: In-Memory-OLTP-Technologien für höhere T-SQL-Leistung](https://msdn.microsoft.com/library/mt694156.aspx)
- [Verwenden von In-Memory-OLTP in einer vorhandenen Azure SQL-Anwendung](sql-database-in-memory-oltp-migration.md)
- [Überwachen des In-Memory-OLTP-Speichers](sql-database-in-memory-oltp-monitoring.md) für In-Memory-OLTP
- [Ausprobieren von In-Memory-Features in Azure SQL-Datenbank](sql-database-in-memory-sample.md)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

### <a name="deeper-information"></a>Weiterführende Informationen

- [Erfahren Sie, wie Quorum die Workload der wichtigen Datenbank verdoppelt, während es die DTU mit In-Memory-OLTP in SQL-Datenbank um 70 % reduziert (in englischer Sprache)](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [Blogbeitrag „In-Memory OLTP in Azure SQL Database“](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/) (In-Memory OLTP in Azure SQL-Datenbank)
- [In-Memory OLTP (Arbeitsspeicheroptimierung)](https://msdn.microsoft.com/library/dn133186.aspx)
- [Beschreibung von Columnstore-Indizes](https://msdn.microsoft.com/library/gg492088.aspx)
- [Erste Schritte mit Columnstore für operative Echtzeitanalyse](https://msdn.microsoft.com/library/dn817827.aspx)
- Das [Whitepaper zu allgemeinen Workloadmustern und Überlegungen zur Migration](https://msdn.microsoft.com/library/dn673538.aspx)beschreibt Workloadmuster, bei denen In-Memory-OLTP im Allgemeinen erhebliche Leistungssteigerungen bietet.

### <a name="application-design"></a>Anwendungsentwurf

- [In-Memory OLTP (In-Memory Optimization)](https://msdn.microsoft.com/library/dn133186.aspx)
- [Verwenden von In-Memory-OLTP in einer vorhandenen Azure SQL-Anwendung](sql-database-in-memory-oltp-migration.md)

### <a name="tools"></a>Tools

- [Azure-Portal](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
