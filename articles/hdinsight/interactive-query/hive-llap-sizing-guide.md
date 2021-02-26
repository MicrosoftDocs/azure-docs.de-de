---
title: Leitfaden zur Größenanpassung des HDInsight Interactive Query-Clusters (LLAP)
description: Leitfaden zur LLAP-Größenanpassung
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: 7df75077785c66215008e045ef0b1e451ba29f57
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931109"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Leitfaden zur Größenanpassung des Azure HDInsight Interactive Query-Clusters (Hive LLAP)

In diesem Dokument wird die Größenanpassung des HDInsight Interactive Query-Clusters (Hive LLAP-Clusters) für eine typische Workload beschrieben, um eine angemessene Leistung zu erzielen. Beachten Sie, dass die Empfehlungen in diesem Dokument allgemeine Richtlinien sind und bestimmte Workloadseine spezifische Optimierung erfordern.

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**Standardmäßige Azure-VM-Typen für HDInsight Interactive Query-Cluster (LLAP)**

| Knotentyp      | Instanz | Size     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 vCPUs, 56 GB RAM, SSD mit 400 GB   |
| Worker   | **D14 v2**        | **16 vCPUs, 112 GB RAM, SSD mit 800 GB**       |
| ZooKeeper   | A4 v2        | 4 vCPUs, 8 GB RAM, SSD mit 40 GB       |

**_Hinweis: Alle empfohlenen Konfigurationswerte basieren auf dem Workerknoten des Typs D14 v2._* _  

### <a name="_configuration"></a>_ *Konfiguration:* *    
| Konfigurationsschlüssel      | Empfohlener Wert  | BESCHREIBUNG |
| :---        |    :----:   | :---     |
| yarn.nodemanager.resource.memory-mb | 102400 (MB) | Gesamter festgelegter Speicher in MB für alle YARN-Container in einem Knoten | 
| yarn.scheduler.maximum-allocation-mb | 102400 (MB) | Maximale Zuordnung für jede Containeranforderung in Resource Manager in MB. Speicheranforderungen, die diesen Wert übersteigen, werden nicht wirksam. |
| yarn.scheduler.maximum-allocation-vcores | 12 |Maximale Anzahl der CPU-Kerne für jede Containeranforderung in Resource Manager. Anforderungen, die diesen Wert übersteigen, werden nicht wirksam. |
| yarn.nodemanager.resource.cpu-vcores | 12 | Anzahl der CPU-Kerne pro NodeManager, die für Container zugeordnet werden können |
| yarn.scheduler.capacity.root.llap.capacity | 85 (%) | YARN-Kapazitätszuordnung für LLAP-Warteschlange  |
| tez.am.resource.memory.mb | 4096 (MB) | Der von Tez-AppMaster zu verwendende Speicher in MB |
| hive.server2.tez.sessions.per.default.queue | <Anzahl_der_Workerknoten> |Anzahl der Sitzungen für jede Warteschlange, die in „hive.server2.tez.default.queues“ benannt wird. Diese Anzahl entspricht der Anzahl der Abfragekoordinatoren (Tez-AMs). |
| hive.tez.container.size | 4096 (MB) | Angegebene Tez-Containergröße in MB |
| hive.llap.daemon.num.executors | 19 | Anzahl der Executors pro LLAP-Daemon | 
| hive.llap.io.threadpool.size | 19 | Threadpoolgröße für Executors |
| hive.llap.daemon.yarn.container.mb | 81920 (MB) | Gesamter Speicher in MB, der von einzelnen LLAP-Daemons verwendet wird (Speicher pro Daemon)
| hive.llap.io.memory.size | 242688 (MB) | Cachegröße in MB pro LLAP-Daemon, bereitgestellter SSD-Cache ist aktiviert |
| hive.auto.convert.join.noconditionaltask.size | 2048 (MB) | Speichergröße in MB für Map Join |

### <a name="llap-architecturecomponents"></a>**LLAP-Architektur/Komponenten:**  

![`LLAP-Architektur/Komponenten`](./media/hive-llap-sizing-guide/LLAP_architecture_sizing_guide.png "LLAP-Architektur/Komponenten")

### <a name="llap-daemon-size-estimations"></a>**Größenschätzungen für LLAP-Daemon:** 

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1. Festlegen der gesamten YARN-Speicherbelegung für alle Container in einem Knoten**    
Konfiguration: **_yarn.nodemanager.resource.memory-mb_* _  

Dieser Wert gibt eine maximale Speichermenge in MB an, die in den YARN-Containern in jedem Knoten verwendet werden kann. Der angegebene Wert muss kleiner sein als die Gesamtmenge des physischen Speichers im jeweiligen Knoten.   
Gesamtspeicher für alle YARN-Container in einem Knoten = (Gesamter physischer Arbeitsspeicher – Arbeitsspeicher für Betriebssystem und andere Dienste)  
Legen Sie diesen Wert auf ca. 90 % der verfügbaren RAM-Größe fest.  
Für D14 v2 wird der Wert _*102400 MB** empfohlen. 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2. Festlegen der maximalen Speichermenge pro YARN-Containeranforderung**  
Konfiguration: **_yarn.scheduler.maximum-allocation-mb_* _

Dieser Wert gibt die maximale Zuordnung für jede Containeranforderung in Resource Manager in MB an. Speicheranforderungen, die den angegebenen Wert übersteigen, werden nicht wirksam. In Resource Manager kann Containern Speicher in Schritten von _yarn.scheduler.minimum-allocation-mb* zugeordnet werden. Dabei darf die durch *yarn.scheduler.maximum-allocation-mb* angegebene Größe nicht überschritten werden. Der angegebene Wert darf nicht größer sein als der gesamte angegebene Speicher für alle Container im Knoten, der durch *yarn.nodemanager.resource.memory-mb* angegeben wird.    
Für D14 v2-Workerknoten wird der Wert **102400 MB** empfohlen.

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3. Festlegen der maximalen Anzahl der virtuellen Kerne pro YARN-Containeranforderung**  
Konfiguration: **_yarn.scheduler.maximum-allocation-vcores_* _  

Dieser Wert gibt die maximale Anzahl der virtuellen CPU-Kerne für jede Containeranforderung in Resource Manager an. Die Anforderung einer höheren Anzahl von virtuellen Kernen als dieser Wert wird nicht wirksam. Dabei handelt es sich um eine globale Eigenschaft des YARN-Schedulers. Für den LLAP-Daemon-Container kann dieser Wert auf 75 % der insgesamt verfügbaren virtuellen Kerne festgelegt werden. Die restlichen 25 % müssen für NodeManager, DataNode und andere in den Workerknoten ausgeführte Dienste reserviert werden.  
Bei virtuellen D14 v2-Computern stehen 16 virtuelle Kerne zur Verfügung. 75 % der insgesamt 16 virtuellen Kerne können vom LLAP-Daemon-Container verwendet werden.  
Für D14 v2 wird der Wert _*12** empfohlen.  

#### <a name="4-number-of-concurrent-queries"></a>**4. Anzahl gleichzeitiger Abfragen**  
Konfiguration: **_hive.server2.tez.sessions.per.default.queue_* _

Dieser Konfigurationswert legt die Anzahl der Tez-Sitzungen fest, die parallel gestartet werden können. Diese Tez-Sitzungen werden für die einzelnen Warteschlangen gestartet, die durch „hive.server2.tez.default.queues“ angegeben werden. Dies entspricht der Anzahl der Tez-AMs (Abfragekoordinatoren). Es wird empfohlen, dass diese Anzahl der Anzahl der Workerknoten entspricht. Die Anzahl der Tez-AMs kann die Anzahl der LLAP-Daemon-Knoten übersteigen. Die primäre Zuständigkeit des Tez-AM besteht darin, die Abfrageausführung zu koordinieren und Abfrageplanfragmente den entsprechenden LLAP-Daemons zur Ausführung zuzuweisen. Behalten Sie diesen Wert als Vielfaches der Anzahl von LLAP-Daemon-Knoten bei, um einen höheren Durchsatz zu erzielen.  

Da im HDInsight-Standardcluster vier LLAP-Daemons auf vier Workerknoten ausgeführt werden, wird als Wert _*4** empfohlen.  

**Schieberegler der Ambari-Benutzeroberfläche für Hive-Konfigurationsvariable `hive.server2.tez.sessions.per.default.queue`:**

![`LLAP: Maximale Anzahl gleichzeitiger Abfragen`](./media/hive-llap-sizing-guide/LLAP_sizing_guide_max_concurrent_queries.png "LLAP: Maximale Zahl gleichzeitiger Abfragen")

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5. Größe des Tez-Containers und des Tez-Application Master**    
Konfiguration: **_tez.am.resource.memory.mb, hive.tez.container.size_* _  

_tez.am.resource.memory.mb* definiert die Größe des Tez-Anwendungsmasters.  
Es wird der Wert **4096 MB** empfohlen.
   
*hive.tez.container.size:* definiert die vorgegebene Speichermenge für Tez-Container. Dieser Wert muss zwischen der minimalen YARN-Containergröße (*yarn.scheduler.minimum-allocation-mb*) und der maximalen YARN-Containergröße (*yarn.scheduler.maximum-allocation-mb*) festgelegt werden. Die LLAP-Daemon-Executors verwenden diesen Wert, um die Speicherauslastung pro Executor einzuschränken.  
Es wird der Wert **4096 MB** empfohlen.  

#### <a name="6-llap-queue-capacity-allocation"></a>**6. Kapazitätszuordnung für LLAP-Warteschlange**   
Konfiguration: **_yarn.scheduler.capacity.root.llap.capacity_* _  

Dieser Wert gibt einen Prozentsatz der für die LLAP-Warteschlange festgelegten Kapazität an. Je nach Konfiguration der YARN-Warteschlangen können die Kapazitätszuordnungen unterschiedliche Werte für verschiedene Workloads aufweisen. Wenn es sich bei der Workload um schreibgeschützte Vorgänge handelt, eignet sich eine Einstellung von bis zu 90 % der Kapazität. Wenn die Workload jedoch eine Mischung aus Aktualisierungs-, Lösch- und Zusammenführungsvorgängen unter Verwendung verwalteter Tabellen umfasst, empfiehlt es sich, 85 % der Kapazität für die LLAP-Warteschlange anzugeben. Die verbleibende Kapazität von 15 % kann für andere Aufgaben verwendet werden, z. B. Komprimierung, um Container aus der Standardwarteschlange zuzuordnen. Auf diese Weise gehen den Tasks in der Standardwarteschlange nicht die YARN-Ressourcen aus.    

Für D14 v2-Workerknoten wird der Wert _*85** für die LLAP-Warteschlange empfohlen.     
(Bei schreibgeschützten Workloads kann er je nach Bedarf auf bis zu 90 erhöht werden.)  

#### <a name="7-llap-daemon-container-size"></a>**7. Größe des LLAP-Daemon-Containers**    
Konfiguration: **_hive.llap.daemon.yarn.container.mb_* _  
   
Der LLAP-Daemon wird in jedem Workerknoten als YARN-Container ausgeführt. Die Gesamtspeichergröße des LLAP-Daemon-Containers hängt von folgenden Faktoren ab:    
_ Konfigurationen der YARN-Containergröße (yarn.scheduler.minimum-allocation-mb, yarn.scheduler.maximum-allocation-mb, yarn.nodemanager.resource.memory-mb)
*  Anzahl der Tez-AMs in einem Knoten
*  Konfigurierter Gesamtspeicher für alle Container in einem Knoten und Kapazität der LLAP-Warteschlange  

Der von Tez-Application Masters (Tez-AMs) benötigte Speicher kann wie folgt berechnet werden.  
Tez AM fungiert als Abfragekoordinator, und die Anzahl der Tez-AMs sollte ausgehend von einer Reihe zu verarbeitender gleichzeitiger Abfragen konfiguriert werden. Theoretisch können wir von einem Tez AM pro Workerknoten ausgehen. Es ist aber möglich, dass Sie mehr als einen Tez AM auf einem Workerknoten finden werden. Zum Zweck der Berechnung wird von einer einheitlichen Verteilung von Tez AMs über alle LLAP Daemonknoten/Workerknoten ausgegangen.
Es wird empfohlen, pro Tez-AM 4 GB Speicher bereitzustellen.  

Anzahl der Tez Ams = in der Hive-Konfiguration angegebener Wert ***hive.server2.tez.sessions.per.default.queue** _.  
Anzahl der LLAP-Daemonknoten = angeben durch die env-Variable _*_num_llap_nodes_for_llap_daemons_*_ in der Ambari-Benutzeroberfläche.  
Tez AM-Containergröße = in der Tez-Konfiguration angegebener Wert _*_tez.am.resource.memory.mb_*_.  

Speicher für Tez AM pro Knoten = _ *(* * ceil **(** Anzahl der Tez AMs **/** Anzahl der LLAP-Daemonknoten **)** **x** Tez AM-Containergröße **)**  
Für D14 v2 umfasst die Standardkonfiguration vier Tez-AMs und vier LLAP-Daemon-Knoten.  
Speicher für Tez-AM pro Knoten = (ceil(4/4) × 4 GB) = 4 GB

Der verfügbare Gesamtspeicher für die LLAP-Warteschlange pro Workerknoten kann wie folgt berechnet werden:  
Dieser Wert hängt von der Gesamtmenge des verfügbaren Speichers für alle YARN-Container in einem Knoten (*yarn.nodemanager.resource.memory-mb*) und dem Prozentsatz der für die LLAP-Warteschlange konfigurierten Kapazität (*yarn.scheduler.capacity.root.llap.capacity*) ab.  
Gesamtspeicher für die LLAP-Warteschlange im Workerknoten = Verfügbarer Gesamtspeicher für alle YARN-Container in einem Knoten × Prozentsatz der Kapazität für die LLAP-Warteschlange  
Für D14 v2 beträgt dieser Wert (100 GB x 0,85) = 85 GB.

Die Größe des LLAP-Daemon-Containers wird wie folgt berechnet:

**LLAP-Daemon-Containergröße =  (Arbeitsspeicher gesamt für LLAP-Warteschlange auf einem Workerknoten) – (Tez AM-Arbeitsspeicher pro Knoten) - (Service Master-Containergröße)**  
Es gibt nur einen Service Master (Application Master für den LLAP-Dienst) auf dem Cluster, der auf einem der Workerknoten erzeugt wird. Zu Berechnungszwecken gehen wir von einem Service Master pro Workerknoten aus.  
Für D14 v2-Workerknoten mit HDI 4.0 wird der Wert (85 GB - 4 GB - 1 GB)) = **80 GB**  empfohlen.  
(Für HDI 3.6 wird der Wert **79 GB** empfohlen, da ca. 2 GB zusätzlich für den Slider-AM reserviert werden sollten.)  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8. Festlegen der Anzahl der Executors pro LLAP-Daemon**  
Konfiguration: **_hive.llap.daemon.num.executors_* _, _*_hive.llap.io.threadpool.size_*_

_*_hive.llap.daemon.num.executors:_*_   
Diese Konfiguration steuert die Anzahl der Executors pro LLAP-Daemon, die Tasks parallel ausführen können. Dieser Wert richtet sich nach der Anzahl der virtuellen Kerne, der pro Executor genutzten Speichermenge und der für den LLAP-Daemoncontainer verfügbaren Gesamtspeichermenge.    Die Anzahl der Executors kann bis zu 120 % der verfügbaren virtuellen Kerne pro Workerknoten überbucht werden. Sie sollte jedoch angepasst werden, wenn sie die Anforderungen an den Arbeitsspeicher nicht erfüllt, ausgehend vom pro Executor benötigten Arbeitsspeicher und der Größe des LLAP-Containers.

Jeder Executor entspricht einem Tez-Container und kann bis zu 4 GB (Tez-Containergröße) Arbeitsspeicher belegen. Alle Executors im LLAP-Daemon nutzen denselben Heap-Speicher. Ausgehend von der Annahme, dass nicht alle Executors zur gleichen Zeit speicherintensive Vorgänge ausführen, können Sie 75 % der Tez-Containergröße (4 GB) pro Executor veranschlagen. Auf diese Weise können Sie die Anzahl der Executors heraufsetzen und so eine höhere Parallelität erreichen, indem Sie jedem Executor weniger Arbeitsspeicher zuweisen (z B. 3 GB). Es wird jedoch empfohlen, diese Einstellung für Ihre Zielworkload zu optimieren.

Bei virtuellen D14 v2-Computern stehen 16 virtuelle Kerne zur Verfügung.
Bei D14 v2 lautet der empfohlene Wert für die Anzahl von Executors (16 virtuelle Kerne x 120 %) ~ = _ *19** auf jedem Workerknoten, wenn von 3 GB pro Executor ausgegangen wird.

**_hive.llap.io.threadpool.size:_ *_ Dieser Wert gibt die Threadpoolgröße für Executors an. Da Executors wie angegeben festgelegt sind, entspricht er der Anzahl von Executors pro LLAP-Daemon. Für D14 v2 wird der Wert _* 19** empfohlen.

#### <a name="9-determining-llap-daemon-cache-size"></a>**9. Festlegen der LLAP-Daemon-Cachegröße**  
Konfiguration: **_hive.llap.io.memory.size_* _

Der Speicher des LLAP-Daemon-Containers besteht aus folgenden Komponenten: _ Toleranzbereich
*  Von Executors verwendeter Heapspeicher (Xmx)
*  In-Memory-Cache pro Daemon (Größe des Off-Heapspeichers; nicht zutreffend, wenn der SSD-Cache aktiviert ist)
*  Metadatengröße des In-Memory-Caches (nur zutreffend, wenn der SSD-Cache aktiviert ist)

**Größe des Toleranzbereichs:** Diese Größe gibt einen Teil des Off-Heapspeichers an, der für den Java-VM-Overhead (Metaspace, Threadstapel, GC-Datenstrukturen usw.) verwendet wird. Im Allgemeinen beträgt dieser Overhead etwa 6 % der Heapgröße (Xmx). Sicherheitshalber kann der Wert als 6 % der gesamten LLAP-Daemon-Speichergröße berechnet werden.  
Für D14 v2 wird ein Wert von ca. **4 GB** (ceil(80 GB · 0,06)) empfohlen.  

**Heapgröße (Xmx):** Menge des für alle Executors verfügbaren Heapspeichers.
Gesamte Heapgröße = Anzahl der Executors × 3 GB  
Für D14 v2 beträgt dieser Wert 19 × 3 GB = **57 GB**.  

`Ambari environment variable for LLAP heap size:`

![`LLAP-Heapgröße`](./media/hive-llap-sizing-guide/LLAP_sizing_guide_llap_heap_size.png "LLAP-Heapgröße")

Wenn der SSD-Cache deaktiviert ist, entspricht der In-Memory-Cache der Speichermenge, die nach Abzug der Größe des Toleranzbereichs und der Heapgröße von der Größe des LLAP-Daemon-Containers verbleibt.

Die Berechnung der Cachegröße unterscheidet sich, wenn der SSD-Cache aktiviert ist.
Durch Festlegen von *hive.llap.io.allocator.mmap* auf TRUE wird SSD-Caching aktiviert.
Wenn der SSD-Cache aktiviert ist, wird ein Teil des Speichers zum Speichern der Metadaten für den SSD-Cache verwendet. Die Metadaten werden im Speicher gespeichert. Ihre Größe beläuft sich erwartungsgemäß auf ca. 8 % der SSD-Cachegröße.   
Größe der In-Memory-Metadaten des SSD-Caches = Größe des LLAP-Daemon-Containers – (Toleranzbereich + Heapgröße)  
Für D14 v2 mit HDI 4.0 beträgt die Größe der In-Memory-Metadaten des SSD-Caches 80 GB – (4 GB + 57 GB) = **19 GB**.  
Für D14 v2 mit HDI 3.6 beträgt die Größe der In-Memory-Metadaten des SSD-Caches 79 GB – (4 GB + 57 GB) = **18 GB**.

Angesichts der Größe des verfügbaren Speichers zum Speichern der Metadaten des SSD-Caches kann die unterstützbare Größe des SSD-Caches berechnet werden.  
Größe der In-Memory-Metadaten des SSD-Caches = Größe des LLAP-Daemon-Containers – (Toleranzbereich + Heapgröße) = 19 GB     
Größe des SSD-Caches = Größe der In-Memory-Metadaten für den SSD-Cache (19 GB) / 0,08 (8 Prozent)  

Für D14 v2 und HDI 4.0 werden 19 GB / 0,08 = **237 GB** als SSD-Cachegröße empfohlen.  
Für D14 v2 und HDI 3.6 werden 18 GB / 0,08 = **225 GB** als SSD-Cachegröße empfohlen.

#### <a name="10-adjusting-map-join-memory"></a>**10. Anpassen des Map Join-Speichers**   
Konfiguration: **_hive.auto.convert.join.noconditionaltask.size_* _

Achten Sie darauf, dass _hive.auto.convert.join.noconditionaltask* aktiviert ist, damit dieser Parameter wirksam wird.
Mit dieser Konfiguration wird der Schwellenwert für die MapJoin-Auswahl durch den Hive-Optimierer festgelegt, der die Überbuchung von Arbeitsspeicher durch die anderen Executors berücksichtigt, um mehr Platz für in-Memory-Hash Tabellen zu bieten und so mehr MapJoin-Konvertierungen zu ermöglichen. Wenn Sie 3 GB pro Executor zugrunde legen, kann diese Größe bis auf 3 GB überbucht werden, es kann aber auch ein Teil des Heap-Speichers für Sortierpuffer, Shuffle-Puffer usw. von anderen Vorgängen verwendet werden.   
Für D14 v2 mit 3 GB Speicher pro Executor empfiehlt es sich, diesen Wert auf **2048 MB** festzulegen.  

(Hinweis: Dieser Wert muss möglicherweise entsprechend der Workload angepasst werden. Wenn dieser Wert zu niedrig festgelegt wird, kann die Funktion für die automatische Konvertierung nicht verwendet werden. Wenn er zu hoch festgelegt wird, kann dies Ausnahmen wegen unzureichenden Speichers oder GC-Pausen zur Folge haben und so zu Leistungsbeeinträchtigungen führen.)  

#### <a name="11-number-of-llap-daemons"></a>**11. Anzahl von LLAP-Daemons**
Ambari-Umgebungsvariablen: **_num_llap_nodes, num_llap_nodes_for_llap_daemons_* _  

_ *num_llap_nodes**: Gibt die Anzahl der vom Hive-LLAP-Dienst verwendeten Knoten an. Diese schließt Knoten ein, die den LLAP-Daemon, LLAP Service Master und Tez Application Master(Tez AM) ausführen.  

![`Anzahl der Knoten für den LLAP-Dienst`](./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes.png "Anzahl der Knoten für den LLAP-Dienst")  

**num_llap_nodes_for_llap_daemons**: angegebene Anzahl der Knoten, die nur für LLAP-Daemons verwendet werden. Die Containergrößen für den LLAP-Daemon sind auf den maximalen Wert pro Knoten festgelegt, das Ergebnis ist also ein LLAP-Daemon pro Knoten.

![`Anzahl der Knoten für LLAP-Daemons`](./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes_for_llap_daemons.png "Anzahl der Knoten für LLAP-Daemons")

Es wird empfohlen, beide Werte gleich der Anzahl der Workerknoten im Interactive Query-Cluster zu halten.

### <a name="considerations-for-workload-management"></a>**Überlegungen zur Workloadverwaltung**  
Wenn Sie die Workloadverwaltung für LLAP aktivieren möchten, sollten Sie sicherstellen, dass Sie ausreichend Kapazität für die Workloadverwaltung reservieren, damit diese erwartungsgemäß funktioniert. Die Workloadverwaltung erfordert die Konfiguration einer benutzerdefinierten YARN-Warteschlange, zusätzlich zur `llap`-Warteschlange. Achten Sie darauf, die gesamte Ressourcenkapazität des Clusters gemäß Ihren Workloadanforderungen zwischen der LLAP-Warteschlange und der Warteschlange der Workloadverwaltung aufzuteilen.
Bei der Workloadverwaltung werden Tez Application Master (TEZ AMs) erzeugt, wenn ein Ressourcenplan aktiviert wird.
Hinweis:

* TEZ AMs, die durch Aktivieren eines Ressourcenplans erzeugt werden, beanspruchen Ressourcen aus der Warteschlange zur Workloadverwaltung, wie in `hive.server2.tez.interactive.queue`angegeben.  
* Die Anzahl der Tez AMs hängt dann vom Wert von `QUERY_PARALLELISM` ab, der im Ressourcenplan angegeben ist.  
* Wenn die Workloadverwaltung aktiv ist, werden Tez AMs in der LLAP-Warteschlange nicht verwendet. Nur TEZ AMs aus der Warteschlange der Workloadverwaltung-werden für die Abfragekoordinierung verwendet. TEZ AMs in der `llap`-Warteschlange werden verwendet, wenn die Workloadverwaltung deaktiviert ist.
 
Beispiel: Cluster-Gesamtkapazität = 100 GB Arbeitsspeicher, wie folgt aufgeteilt auf LLAP, Workloadverwaltung und Standardwarteschlangen:
 - Kapazität der LLAP-Warteschlange = 70 GB
 - Kapazität der Warteschlange der Workloadverwaltung = 20 GB
 - Kapazität der Standardwarteschlange = 10 GB

Bei einer Kapazität der Warteschlange der Workloadverwaltung von 20 GB kann ein Ressourcenplan den Wert für `QUERY_PARALLELISM` mit fünf angeben, was bedeutet, dass die Workloadverwaltung fünf Tez AMs mit einer Containergröße von jeweils 4 GB starten kann. Wenn `QUERY_PARALLELISM` höher als die Kapazität ist, kann es vorkommen, dass einige Tez AMs im Status `ACCEPTED` nicht mehr reagieren. Der Hiveserver2 Interactive kann keine Abfragefragmente an die Tez AMs senden, die sich nicht im Status `RUNNING` befinden.


#### <a name="next-steps"></a>**Next Steps**
Wenn sich das Problem durch Festlegen dieser Werte nicht beheben lässt, haben Sie folgende Möglichkeiten:

* Nutzen Sie den [Azure-Communitysupport](https://azure.microsoft.com/support/community/), um Antworten von Azure-Experten zu erhalten.

* Nutzen Sie [@AzureSupport](https://twitter.com/azuresupport) – das offizielle Microsoft Azure-Konto zur Verbesserung der Benutzerfreundlichkeit. Hierüber hat die Azure-Community Zugriff auf die richtigen Ressourcen: Antworten, Support und Experten.

* Sollten Sie weitere Unterstützung benötigen, senden Sie eine Supportanfrage über das [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wählen Sie dazu auf der Menüleiste die Option **Support** aus, oder öffnen Sie den Hub **Hilfe und Support**. Ausführlichere Informationen hierzu finden Sie unter [Erstellen einer Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md). Zugang zu Abonnementverwaltung und Abrechnungssupport ist in Ihrem Microsoft Azure-Abonnement enthalten. Technischer Support wird über einen [Azure-Supportplan](https://azure.microsoft.com/support/plans/) bereitgestellt.  

* ##### <a name="other-references"></a>**Andere Referenzen:**
  * [Configure other LLAP properties](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_setup_llap.html) (Konfigurieren anderer LLAP-Eigenschaften)  
  * [Configure the HiveServer heap size](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_hiveserver_heap_sizing.html) (Konfigurieren der HiveServer-Heapgröße)  
  * [Map Join Memory Sizing for LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462) (Map Join-Speicheranpassung für LLAP)  
  * [Tez Execution Engine Properties](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_tez_engine_properties.html) (Eigenschaften der Tez-Ausführungs-Engine)  
  * [Hive LLAP deep dive](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893) (Tieferer Einblick in Hive LLAP)