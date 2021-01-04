---
title: Troubleshooting bei Apache HBase-Leistungsproblemen in Azure HDInsight
description: In diesem Artikel finden Sie eine Reihe von Richtlinien für die Apache HBase-Leistungsoptimierung sowie Tipps zur Steigerung der Leistung in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 5be3f02a80524d9c4b633e1e34d581fc26bfd32d
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547893"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Troubleshooting bei Apache HBase-Leistungsproblemen in Azure HDInsight

In diesem Artikel wird eine Reihe von Richtlinien für die Apache HBase-Leistungsoptimierung erläutert, und Sie erhalten Tipps zum Optimieren der Leistung in Azure HDInsight. Viele dieser Tipps sind abhängig von der jeweiligen Arbeitsauslastung und dem Lese-/Schreib-/Scan-Muster. Konfigurationsänderungen müssen sorgfältig getestet werden, bevor sie einer Produktionsumgebung angewendet werden.

## <a name="hbase-performance-insights"></a>Informationen zur HBase-Leistung

Der größte Engpass in den meisten HBase-Workloads ist das Write-Ahead-Protokoll (WAL). Es hat erhebliche Auswirkungen auf die Schreibleistung. HDInsight HBase verfügt über ein separates Speicher-/Computemodell. Daten werden remote in Azure Storage gespeichert, obwohl die Regionsserver von virtuellen Computern gehostet werden. Bis vor Kurzem wurde auch das WAL in Azure Storage geschrieben. In HDInsight hat dieses Verhalten den Engpass noch verstärkt. Das Feature [Beschleunigte Schreibvorgänge](./apache-hbase-accelerated-writes.md) löst dieses Problem. Es schreibt das WAL auf verwaltete Azure-Datenträger vom Typ SSD Premium. Dies verbessert die Schreibleistung erheblich und behebt eine Vielzahl von Problemen im Zusammenhang mit einigen schreibintensiven Workloads.

Verwenden Sie das [Premium-Blockblob-Speicherkonto](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) als Remotespeicher, um deutliche Verbesserungen bei Schreibvorgängen zu erzielen. Diese Option kann nur verwendet werden, wenn das WAL-Feature aktiviert ist.

## <a name="compaction"></a>Komprimierung

Komprimierung ist nach allgemeiner Auffassung in der Community ein weiterer potenzieller Engpass. Standardmäßig ist die umfassende Komprimierung in HDInsight HBase-Clustern deaktiviert, da es sich hierbei um einen ressourcenintensiven Prozess handelt. Kunden können sie jedoch ganz flexibel unter Berücksichtigung ihrer Workloads planen. So kann die Komprimierung beispielsweise außerhalb der Spitzenzeiten geplant werden. Zudem spielt die Datenlokalität keine Rolle, da anstelle einer lokalen HDFS-Instanz (Hadoop Distributed File System) ein Azure Storage-basierter Remotespeicher verwendet wird.

Wir empfehlen Kunden, die umfassende Komprimierung nach Bedarf zu planen. Ohne diese Wartungsmaßnahme wirkt sich die Komprimierung auf lange Sicht nachteilig auf die Leseleistung aus.

Bei Scanvorgängen sollten mittlere Wartezeiten von weit mehr als 100 ms Anlass zur Sorge sein. Überprüfen Sie, ob umfassende Komprimierungsvorgänge ordnungsgemäß geplant wurden.

## <a name="apache-phoenix-workload"></a>Apache Phoenix-Workload

Durch die Beantwortung der folgenden Fragen können Sie Ihre Apache Phoenix-Workload besser verstehen:

* Werden alle Ihre „Lesevorgänge“ in Scans übersetzt?
    * Falls ja, welche Merkmale haben diese Scans?
    * Haben Sie Ihr Phoenix-Tabellenschema für diese Scans optimiert (einschließlich entsprechender Indizierung)?
* Haben Sie mithilfe der Anweisung `EXPLAIN` die Abfragepläne untersucht, die von Ihren Lesevorgängen generiert werden?
* Sind Ihre Schreibvorgänge „upsert-select“-Ausführungen?
    * Wenn dies der Fall ist, führen sie ebenfalls Scans aus. Bei Scans beträgt die erwartete Wartezeit im Schnitt etwa 100 Millisekunden, verglichen mit 10 Millisekunden bei punktuellen Abrufvorgängen in HBase.  

## <a name="test-methodology-and-metrics-monitoring"></a>Testmethode und Metriküberwachung

Wenn Sie Benchmarks wie Yahoo! Cloud Serving Benchmark, JMeter oder Pherf verwenden, um die Leistung zu testen und zu optimieren, vergewissern Sie sich, dass Folgendes erfüllt ist:

- Die Clientcomputer stellen keinen Engpass dar. Überprüfen Sie hierzu die CPU-Auslastung von Clientcomputern.

- Clientseitige Konfigurationen (etwa die Anzahl von Threads) sind entsprechend angepasst, um die gesamte Clientbandbreite zu nutzen.

- Testergebnisse werden genau und systematisch aufgezeichnet.

Sollte sich die Leistung Ihrer Abfragen überraschend deutlich verschlechtern, prüfen Sie Ihren Anwendungscode auf mögliche Fehler. Werden plötzlich große Mengen an ungültigen Daten generiert? Falls ja, können sich dadurch die Wartezeiten bei Schreibvorgänge erhöhen.

## <a name="migration-issues"></a>Migrationsprobleme

Stellen im Falle einer Migration zu Azure HDInsight sicher, dass sie systematisch und korrekt erfolgt (vorzugsweise mittels Automatisierung). Vermeiden Sie eine manuelle Migration. Stellen Sie Folgendes sicher:

- Wurden die Tabellenattribute korrekt migriert? Attribute können Komprimierung, Bloomfilter und Ähnliches enthalten.

- Wurden die Salting-Einstellungen in Phoenix-Tabellen ordnungsgemäß der neuen Clustergröße zugeordnet? Die Anzahl von Salt-Buckets muss beispielsweise ein Vielfaches der Anzahl von Workerknoten im Cluster sein. Verwenden Sie außerdem ein Vielfaches, bei dem es sich um einen Faktor der Hotspotmenge handelt.

## <a name="server-side-configuration-tunings"></a>Serverseitige Konfigurationsoptimierung

In HDInsight HBase werden HFiles in Remotespeicher gespeichert. Im Falle eines Cachefehlers ist der Leseaufwand höher als bei lokalen Systemen, da die Daten lokaler Systeme in einer lokalen HDFS-Instanz vorliegen. In den meisten Szenarien wird dieses Problem durch die intelligente Nutzung von HBase-Caches (Blockcache und Bucketcache) umgangen. In anderen Fällen hilft ggf. die Verwendung eines Premium-Blockblobkontos. Der Windows Azure Storage Blob-Treiber nutzt bestimmte Eigenschaften (beispielsweise `fs.azure.read.request.size`), um Daten in Blöcken abhängig vom ermittelten Lesemodus (sequenziell oder zufällig) abzurufen. Daher sind auch künftig höhere Wartezeiten bei Lesevorgängen nicht auszuschließen. In empirischen Experimenten wurde ermittelt, dass in der Praxis das beste Ergebnis erzielt wird, wenn die Blockgröße für Leseanforderungen (`fs.azure.read.request.size`) auf 512 KB und die Blockgröße der HBase-Tabellen auf den gleichen Wert festgelegt wird.

Bei den meisten Clustern mit großen Knoten stellt HDInsight HBase `bucketcache` als Datei auf einem lokalen SSD Premium-Datenträger bereit, der mit dem virtuellen Computer verbunden ist, auf dem `regionservers`ausgeführt wird. Durch Verwendung eines Off-Heap-Caches lässt sich ggf. eine gewisse Verbesserung erzielen. Da bei dieser Problemumgehung allerdings der verfügbare Arbeitsspeicher verwendet wird und dieser potenziell kleiner ist als ein dateibasierter Cache, ist diese Lösung nicht immer die beste Wahl.

Im Anschluss finden Sie einige der anderen spezifischen Parameter, mit deren Optimierung sich ggf. gewisse Verbesserungen erzielen lassen:

- Erhöhen Sie die Größe von `memstore` auf 256 MB (Standardwert: 128 MB). Diese Einstellung wird in der Regel für schreiblastige Szenarien empfohlen.

- Erhöhen Sie die Anzahl dedizierter Threads für die Komprimierung auf **4** (Standardeinstellung: **1** ). Diese Einstellung ist relevant, wenn häufig geringfügige Komprimierungsvorgänge ausgeführt werden.

- Die `memstore`-Leerung sollte nicht aufgrund von Speicherlimits blockiert werden. Erhöhen Sie zur Bereitstellung dieses Puffers die Einstellung `Hbase.hstore.blockingStoreFiles` auf **100** .

- Verwenden Sie zum Steuern von Leerungen die folgenden Einstellungen:

    - `Hbase.regionserver.maxlogs`: **140** (vermeidet Leerungen aufgrund von WAL-Grenzwerten)

    - `Hbase.regionserver.global.memstore.lowerLimit`: **0,55**

    - `Hbase.regionserver.global.memstore.upperLimit`: **0,60**

- Phoenix-spezifische Konfigurationen für die Optimierung von Threadpools:

    - `Phoenix.query.queuesize`: **10.000**

    - `Phoenix.query.threadpoolsize`: **512**

- Weitere Phoenix-spezifische Konfigurationen:

    - `Phoenix.rpc.index.handler.count`: **50** (im Falle umfangreicher oder zahlreicher Indexsuchvorgänge)

    - `Phoenix.stats.updateFrequency`: **1 Stunde**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`: **1 Stunde**

    - `Phoenix.coprocessor.maxmetadatacachesize`: **50 MB**

- RPC-Timeouts: **Drei Minuten**

   - RPC-Timeouts beinhalten HBase-RPC-Timeout, HBase-Clientscannertimeout und Phoenix-Abfragetimeout. 
   - Achten Sie darauf, dass der Parameter `hbase.client.scanner.caching` sowohl auf der Server- als auch auf der Clientseite auf den gleichen Wert festgelegt ist. Andernfalls treten auf der Clientseite Fehler im Zusammenhang mit `OutOfOrderScannerException` auf. Diese Einstellung sollte für umfassende Scans auf einen niedrigen Wert festgelegt werden. Wir haben diesen Wert auf **100** festgelegt.

## <a name="other-considerations"></a>Weitere Überlegungen

Im Anschluss finden Sie weitere Parameter, die ggf. optimiert werden sollten:

- `Hbase.rs.cacheblocksonwrite`: Diese Einstellung ist für HDI standardmäßig auf **true** festgelegt.

- Einstellungen, mit denen weniger umfassende Komprimierungsvorgänge auf einen späteren Zeitpunkt verschoben werden kann.

- Experimentelle Einstellungen – etwa die Anpassung der Prozentsätze von Warteschlangen, die für Lese- und Schreibanforderungen reserviert sind.

## <a name="next-steps"></a>Nächste Schritte

Sollte Ihr Problem weiterhin bestehen, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

- Nutzen Sie den [Azure-Communitysupport](https://azure.microsoft.com/support/community/), um Antworten von Azure-Experten zu erhalten.

- Wenden Sie sich an [@AzureSupport](https://twitter.com/azuresupport) – das offizielle Microsoft Azure-Konto zur Verbesserung der Benutzerfreundlichkeit. Hierüber hat die Azure-Community Zugriff auf die richtigen Ressourcen: Antworten, Support und Experten.

- Sollten Sie weitere Unterstützung benötigen, senden Sie eine Supportanfrage über das [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wählen Sie dazu auf der Menüleiste die Option **Support** aus, oder öffnen Sie den Hub **Hilfe und Support** . Ausführlichere Informationen hierzu finden Sie unter [Erstellen einer Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md). Ihr Microsoft Azure-Konto beinhaltet den Zugang zu Abonnementverwaltung und Abrechnungssupport. Technischer Support wird über einen [Azure-Supportplan](https://azure.microsoft.com/support/plans/) bereitgestellt.