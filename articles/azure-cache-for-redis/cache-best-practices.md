---
title: Bewährte Methoden für Azure Cache for Redis
description: Erfahren Sie, wie Sie Azure Cache for Redis anhand dieser bewährten Methoden effektiv verwenden.
author: joncole
ms.service: cache
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joncole
ms.openlocfilehash: 47c8096893742a25904f0f7e688af2fc641166d1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004312"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Bewährte Methoden für Azure Cache for Redis 
Durch Befolgen dieser bewährten Methoden sorgen Sie für eine optimale Leistung und kostengünstige Verwendung Ihrer Azure Cache for Redis-Instanz.

## <a name="configuration-and-concepts"></a>Konfiguration und Konzepte
 * **Verwenden Sie den Standard- oder Premium-Tarif für Produktionssysteme.**  Der Basic-Tarif ist ein System mit einem einzelnen Knoten, ohne Datenreplikation und ohne SLA. Verwenden Sie mindestens einen C1-Cache.  C0-Caches sind für einfache Entwicklungs-/Testszenarien vorgesehen, da sie über einen gemeinsamen CPU-Kern und wenig Arbeitsspeicher verfügen und anfällig für Beeinträchtigungen durch andere Dienste („Noisy Neighbors“) sind.

 * **Beachten Sie, dass Redis ein In-Memory -Datenspeicher ist.**  [In diesem Artikel](cache-troubleshoot-data-loss.md) werden einige Szenarien erläutert, in denen Datenverluste auftreten können.

 * **Develop your system such that it can handle connection blips** [because of patching and failover](cache-failover.md).

 * **Konfigurieren Sie Ihre [maxmemory-reserved-Einstellung](cache-configure.md#maxmemory-policy-and-maxmemory-reserved), um die Reaktionsfähigkeit des Systems** unter extremer Speicherauslastung zu verbessern.  Eine ausreichende Reservierungseinstellung ist besonders wichtig für schreibintensive Workloads oder wenn Sie größere Werte (100 KB oder mehr) in Redis speichern. Sie sollten mit 10 % der Größe Ihres Caches beginnen und den Prozentsatz bei schreibintensiven Lasten erhöhen.

 * **Redis funktioniert am besten mit kleineren Werten**, deshalb sollten Sie die Aufteilung großer Daten in mehrere Schlüssel erwägen.  In [dieser Diskussion zu Redis](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/) sind einige Überlegungen aufgelistet, die Sie unbedingt berücksichtigen sollten.  Lesen Sie [diesen Artikel](cache-troubleshoot-client.md#large-request-or-response-size) über ein beispielhaftes Problem, das durch große Werte verursacht werden kann.

 * **Platzieren Sie Ihre Cache-Instanz und Ihre Anwendung in der gleichen Region.**  Eine Verbindung mit einem Cache in einer anderen Region herzustellen, kann zu einer beträchtlichen Latenz führen und die Zuverlässigkeit reduzieren.  Sie können zwar von außerhalb von Azure aus eine Verbindung herstellen, dies ist jedoch nicht empfehlenswert, *vor allem bei Verwendung von Redis als Cache*.  Wenn Sie Redis als nur Schlüssel/Wert-Speicher verwenden, ist Latenz möglicherweise kein Hauptaspekt. 

 * **Wiederverwenden von Verbindungen**.  Das Erstellen neuer Verbindungen ist teuer und erhöht die Latenz, also verwenden Verbindungen weitestgehend wieder. Wenn Sie sich dazu entscheiden, neue Verbindungen zu erstellen, vergewissern Sie sich, dass alte Verbindungen geschlossen sind, bevor Sie sie freigeben (selbst in Sprachen mit verwaltetem Arbeitsspeicher wie .NET oder Java).

 * **Konfigurieren Sie Ihre Clientbibliothek für die Verwendung eines *Verbindungstimeouts* von mindestens 15 Sekunden**, wodurch das System Zeit erhält, die Verbindung auch unter höherer CPU-Auslastung herzustellen.  Bei einem niedrigeren Verbindungstimeoutwert ist nicht sichergestellt, dass die Verbindung in diesem Zeitraum hergestellt werden kann.  Bei Beeinträchtigungen (hohe Client- oder Server-CPU-Auslastung usw.) hat ein niedriger Verbindungstimeoutwert zur Folge, dass der Verbindungsversuch fehlschlägt. Dieses Verhalten macht eine schlechte Situation häufig noch schlimmer.  Statt zu helfen, verschlimmern kürzere Timeouts das Problem, weil sie ein Neustarten des Prozesses der Verbindungsherstellung erzwingen und damit zu einer *Verbinden -> Fehler -> Wiederholen*-Schleife führen können. Im Allgemeinen wird empfohlen, das Verbindungstimeout bei 15 Sekunden oder höher zu lassen. Es ist besser, dass der Verbindungsversuch nach 15 oder 20 Sekunden erfolgreich ist, als dass er schnell fehlschlägt und zu häufigen Wiederholungen führt. Solch eine Wiederholungsschleife kann längere Ausfallzeiten zur Folge haben, als wenn Sie dem System gleich zu Anfang mehr Zeit für die Herstellung der Verbindung einräumen.  
     > [!NOTE]
     > Diese Anleitung bezieht sich speziell auf den *Verbindungsversuch* und nicht auf die Zeit, die Sie auf das Abschließen eines *Vorgangs* wie GET oder SET zu warten bereit sind.
 
 * **Vermeiden Sie speicherintensive Vorgänge**: Einige Redis-Vorgänge (z.B. der [KEYS-Befehl](https://redis.io/commands/keys)) sind *sehr* speicherintensiv und sollten vermieden werden.  Weitere Informationen finden Sie unter [Einige Überlegungen zu Befehlen mit langer Ausführungsdauer](cache-troubleshoot-server.md#long-running-commands).

 * **Verwenden Sie die TLS-Verschlüsselung**: Azure Cache for Redis setzt standardmäßig eine von TLS verschlüsselte Kommunikation voraus.  Derzeit werden die TLS-Versionen 1.0, 1.1 und 1.2 unterstützt.  Die Unterstützung von TLS 1.0 und 1.1 wird jedoch branchenweit eingestellt werden. Verwenden Sie daher nach Möglichkeit TLS 1.2.  Wenn die Clientbibliothek oder das Tool TLS nicht unterstützt, kann die Aktivierung unverschlüsselter Verbindungen [über das Azure-Portal](cache-configure.md#access-ports) oder über [Verwaltungs-APIs](/rest/api/redis/redis/update) vorgenommen werden.  Wenn verschlüsselte Verbindungen nicht möglich sind, empfiehlt es sich, den Cache und die Clientanwendung in ein virtuelles Netzwerk einzubinden.  Weitere Informationen zu den im virtuellen Netzwerkcache-Szenario verwendeten Ports finden Sie in dieser [Tabelle](cache-how-to-premium-vnet.md#outbound-port-requirements).
 
 * **Leerlaufzeitüberschreitung:** Azure Redis weist derzeit ein Leerlauftimeout von 10 Minuten auf. Daher sollte dieser Wert auf weniger als 10 Minuten festgelegt werden.
 
## <a name="memory-management"></a>Speicherverwaltung
Es gibt mehrere Dinge im Zusammenhang mit der Speicherauslastung in Ihrer Redis-Serverinstanz, die Sie ggf. berücksichtigen sollten.  Hier sind einige Beispiele:

 * **Wählen Sie eine [Entfernungsrichtlinie](https://redis.io/topics/lru-cache), die für Ihre Anwendung geeignet ist.**  Die Standardrichtlinie für Azure Redis ist *volatile-lru*. Das bedeutet, dass nur Schlüssel, für die eine Gültigkeitsdauer (TTL) festgelegt ist, entfernt werden können.  Wenn keine Schlüssel über einen TTL-Wert verfügen, werden im System keine Schlüssel entfernt.  Wenn das System bei hoher Speicherauslastung das Entfernen beliebiger Schlüssel erlauben soll, sollten Sie die *allkeys-lru*-Richtlinie in Betracht ziehen.

 * **Legen Sie einen Ablaufwert für Ihre Schlüssel fest.**  Bei einem Ablauf werden Schlüssel proaktiv entfernt, anstatt zu warten, bis die Speicherauslastung hoch ist.  Wird das Entfernung aufgrund hoher Speicherauslastung ausgelöst, kann dies zusätzliche Last auf dem Server verursachen.  Weitere Informationen finden Sie in der Dokumentation für die Befehle [EXPIRE](https://redis.io/commands/expire) und [EXPIREAT](https://redis.io/commands/expireat).
 
## <a name="client-library-specific-guidance"></a>Spezifische Anleitungen für die Clientbibliothek
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java – Welchen Client soll ich verwenden?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Lettuce (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [ASP.NET-Sitzungszustandsanbieter](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Wann ist es sicher ist, eine Wiederholung durchzuführen?
Leider ist gibt es darauf keine einfache Antwort.  Jede Anwendung muss entscheiden, welche Vorgänge wiederholt werden können und welche nicht.  Jeder Vorgang verfügt über unterschiedliche Anforderungen und Abhängigkeiten zwischen Schlüsseln.  Folgende Punkte sollten berücksichtigt werden:

 * Es können clientseitige Fehler auftreten, obwohl Redis den von Ihnen geforderten Befehl erfolgreich ausgeführt hat.  Beispiel:
     - Timeouts sind ein clientseitiges Konzept.  Wenn der Vorgang den Server erreicht, führt der Server den Befehl aus, auch wenn auf dem Client ein Timeout auftritt.  
     - Tritt ein Fehler bei der Socketverbindung auf, kann man unmöglich erkennen, ob der Vorgang auf dem Server tatsächlich ausgeführt wurde.  Der Verbindungsfehler kann beispielsweise auftreten, nachdem der Server die Anforderung verarbeitet hat, aber bevor der Client die Antwort empfangen hat.
 *  Wie reagiert meine Anwendung, wenn ich versehentlich zweimal denselben Vorgang ausführe?  Was geschieht z. B., wenn ich eine ganze Zahl zweimal statt einmal inkrementiere?  Schreibt meine Anwendung von mehreren Stellen in den gleichen Schlüssel?  Was geschieht, wenn meine Wiederholungslogik einen Wert überschreibt, der von einem anderen Teil meiner App festgelegt wurde?

Wenn Sie die Funktionsweise Ihres Codes unter Fehlerbedingungen testen möchten, ziehen Sie die Verwendung des [Neustartfeatures](cache-administration.md#reboot) in Betracht. Ein Neustart ermöglicht Ihnen, zu sehen, wie Verbindungsunterbrechungen sich auf Ihre Anwendung auswirken.

## <a name="performance-testing"></a>Leistungstests
 * **Starten Sie mit der Verwendung von `redis-benchmark.exe`** , um ein Gefühl für den zu erzielenden Durchsatz und die mögliche Latenz zu bekommen, bevor Sie eigene Leistungstests schreiben.  Dokumentation zu „redis-benchmark“ [finden Sie hier](https://redis.io/topics/benchmarks).  Beachten Sie, dass „redis-benchmark“ keine Unterstützung für TLS bietet, Sie müssen deshalb [den Nicht-TLS-Port über das Portal aktivieren](cache-configure.md#access-ports), bevor Sie den Test ausführen.  [Eine mit Windows kompatible Version von „redis-benchmark.exe“ finden Sie hier](https://github.com/MSOpenTech/redis/releases)
 * Die für den Test verwendete Client-VM sollte sich **in derselben Region** befinden wie Ihre Redis Cache-Instanz.
 * **Es wird empfohlen, die Dv2-VM-Serie für Ihren Client zu verwenden**, da sie über bessere Hardware verfügt und die besten Ergebnisse liefert.
 * Stellen Sie sicher, dass die verwendete Client-VM über **mindestens so viel Computeleistung und Bandbreite* wie der getestete Cache verfügt. 
 * **Aktivieren Sie VRSS** auf dem Clientcomputer, wenn Sie unter Windows arbeiten.  [Ausführliche Informationen finden Sie hier](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383582(v=ws.11)).  PowerShell-Beispielskript:
     >PowerShell -ExecutionPolicy Unrestricted Enable-NetAdapterRSS -Name (    Get-NetAdapter).Name 
     
 * **Erwägen Sie die Verwendung von Redis-Instanzen im Premium-Tarif**.  Diese Cachegrößen verfügen über eine bessere Netzwerklatenz und einen höheren Durchsatz, weil sowohl die CPU als auch das Netzwerk auf besserer Hardware ausgeführt werden.
 
     > [!NOTE]
     > Als Referenz haben wir unsere beobachteten Leistungsergebnisse [hier veröffentlicht](cache-planning-faq.md#azure-cache-for-redis-performance).   Bedenken Sie außerdem, dass SSL/TLS einen gewissen Overhead hinzufügt, wodurch Sie bei Verwendung von Transportverschlüsselung abweichende Latenzen und/oder einen anderen Durchsatz erhalten.
 
### <a name="redis-benchmark-examples"></a>Redis-Benchmark-Beispiele
**Vortestsetup**: Bereiten Sie die Cache-Instanz mit Daten vor, die für die unten aufgeführten Befehle zum Testen von Latenz und Durchsatz erforderlich sind.
> redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024 

**So testen Sie die Latenz**: Testen von GET-Anforderungen mithilfe einer 1-K-Nutzlast.
> redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4

**So testen Sie den Durchsatz**: GET-Anforderungen in Pipeline mit einer 1-K-Nutzlast.
> redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t  GET -n 1000000 -d 1024 -P 50  -c 50