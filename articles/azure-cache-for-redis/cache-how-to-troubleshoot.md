---
title: Problembehandlung für Azure Cache for Redis | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie häufige Probleme bei Azure Cache for Redis beheben.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/15/2019
ms.author: yegu
ms.openlocfilehash: 66361871d365068a90a2eeab70d92adb6b246a83
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60830006"
---
# <a name="how-to-troubleshoot-azure-cache-for-redis"></a>Problembehandlung für Azure Cache for Redis

Dieser Artikel hilft bei der Behandlung verschiedener Kategorien von Problemen, die beim Herstellen einer Verbindung mit Azure Cache for Redis-Instanzen auftreten können.

- [Die Behandlung von clientseitigen Problemen](#client-side-troubleshooting) hilft bei der Identifizierung und Behebung von Problemen in der Anwendung beim Verbinden mit Ihrem Cache.
- [Die Behandlung von serverseitigen Problemen](#server-side-troubleshooting) hilft bei der Identifizierung und Behebung von Problemen, die auf der Seite des Azure Cache for Redis auftreten.
- [Die Problembehandlung für Datenverluste](#data-loss-troubleshooting) hilft bei der Identifizierung und Behebung von Vorfällen, bei denen Schlüssel erwartet, aber in Ihrem Cache nicht gefunden werden.
- [StackExchange.Redis-Timeoutausnahmen](#stackexchangeredis-timeout-exceptions) bietet spezifische Anleitungen zur Behandlung von Problemen mit der StackExchange.Redis-Bibliothek.

> [!NOTE]
> Etliche Problembehandlungsschritte in diesem Leitfaden enthalten Anleitungen zum Ausführen von Redis-Befehlen und zum Überwachen verschiedener Leistungsmetriken. Weitere Informationen und Anleitungen finden Sie in den Artikeln im Abschnitt [Weitere Informationen](#additional-information) .
>
>

## <a name="client-side-troubleshooting"></a>Behandeln von clientseitigen Problemen

In diesem Abschnitt wird das Behandeln von Problemen beschrieben, die wegen eines Zustands in der Clientanwendung auftreten.

- [Hohe Speicherauslastung auf dem Client](#memory-pressure-on-the-client)
- [Datenverkehrsspitzen](#burst-of-traffic)
- [Hohe Auslastung der Client-CPU](#high-client-cpu-usage)
- [Bandbreitenüberschreitung auf der Clientseite](#client-side-bandwidth-exceeded)
- [Große Anforderungen/große Antworten](#large-requestresponse-size)

### <a name="memory-pressure-on-the-client"></a>Hohe Speicherauslastung auf dem Client

Hohe Arbeitsspeicherauslastung auf dem Clientcomputer führt zu Leistungsproblemen aller Art, die die Verarbeitung von Antworten aus dem Cache verzögern können. Wenn es zu hoher Arbeitsspeicherauslastung kommt, kann das System beginnen, Daten auf den Datenträger auszulagern. Diese sogenannten _Seitenfehler_ bewirken eine deutliche Verlangsamung des Systems.

So erkennen sie hohe Speicherauslastung auf dem Client

- Überwachen Sie die Speicherauslastung auf dem Computer und stellen Sie sicher, dass sie den verfügbaren Speicher nicht überschreitet.
- Überwachen Sie den Leistungsindikator `Page Faults/Sec` des Clients. Während des normalen Betriebs kommt es auf den meisten Systeme zu ein paar Seitenfehlern. Auftretende Spitzen bei Seitenfehlern, die mit Anforderungstimeouts korrespondieren, können auf hohe Arbeitsspeicherauslastung hindeuten.

Hoher Arbeitsspeicherauslastung auf dem Client kann auf mehrere Arten gemindert werden:

- Analysieren Sie die Muster Ihrer Arbeitsspeichernutzung, um die Auslastung auf dem Client zu verringern.
- Führen Sie ein Upgrade Ihrer Client-VM auf eine größere VM mit mehr Arbeitsspeicher aus.

### <a name="burst-of-traffic"></a>Datenverkehrsspitzen

Plötzliche Anstiege des Datenverkehrsvolumens in Verbindung mit unzureichenden `ThreadPool` -Einstellungen können zu Verzögerungen beim Verarbeiten der vom Redis-Server bereits gesendeten, aber auf der Clientseite noch nicht genutzten Daten führen.

Überwachen Sie die Änderungen bei den `ThreadPool`-Statistiken im zeitlichen Verlauf unter Verwendung [eines Beispiel-`ThreadPoolLogger`-](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Sie können `TimeoutException`-Meldungen von StackExchange.Redis wie unten gezeigt verwenden, um weitere Untersuchungen anzustellen:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

In der oben aufgeführten Ausnahme zeigen sich mehrere interessante Probleme:

- Beachten Sie, dass in den Abschnitten `IOCP` und `WORKER` der `Busy`-Wert größer als der `Min`-Wert ist. Dieser Unterschied bedeutet, dass die `ThreadPool`-Einstellungen angepasst werden müssen.
- Außerdem wird `in: 64221` angegeben. Dieser Wert bedeutet, dass 64.211 Bytes auf der Kernelsocketebene des Clients empfangen, aber von der Anwendung noch nicht gelesen wurden. Dieser Unterschied bedeutet in der Regel, dass Ihre Anwendung (z. B. StackExchange.Redis) Daten aus dem Netzwerk nicht so schnell liest, wie sie vom Server gesendet werden.

Sie können Ihre [`ThreadPool`-Einstellungen konfigurieren](https://gist.github.com/JonCole/e65411214030f0d823cb), um sicherzustellen, dass Ihr Threadpool bei Datenverkehrsspitzen schnell hochskaliert wird.

### <a name="high-client-cpu-usage"></a>Hohe Auslastung der Client-CPU

Hohe Auslastung der Client-CPU weist darauf hin, dass das System mit der angeforderten Arbeitsmenge nicht Schritt halten kann. Obgleich der Client die Antwort schnell gesendet hat, schafft es der Client eventuell nicht, die Antwort rechtzeitig zu verarbeiten.

Überwachen Sie die systemweite CPU-Auslastung des Clients mithilfe der im Azure-Portal zur Verfügung stehenden Metriken oder mithilfe von Leistungsindikatoren auf dem Computer. Achten Sie darauf, dass Sie nicht die *Prozess*-CPU überwachen. Für einen einzelnen Prozess kann die CPU-Auslastung gering sein, während aber die systemweite CPU-Auslastung hoch sein kann. Beobachten Sie die Spitzen bei der CPU-Auslastung, denn sie korrespondieren mit Timeouts. Eine hohe CPU-Auslastung kann auch hohe Werte für `in: XXX` in den `TimeoutException`-Fehlermeldungen verursachen, wie im Abschnitt [Datenverkehrsspitzen](#burst-of-traffic) beschrieben.

> [!NOTE]
> Ab StackExchange.Redis 1.1.603 ist in den `TimeoutException`-Fehlermeldungen die Metrik `local-cpu` enthalten. Verwenden Sie immer die neueste Version des [NuGet-Pakets für StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Es werden ständig Fehler im Code behoben, um ihn widerstandsfähiger gegen Timeouts zu machen. Das Nutzen der neuesten Version ist also wichtig.
>
>

So mindern Sie eine hohe CPU-Auslastung beim Client

- Untersuchen Sie, was CPU-Spitzen verursacht.
- Führen Sie ein Upgrade Ihres Clients auf eine größere VM mit höherer CPU-Kapazität aus.

### <a name="client-side-bandwidth-exceeded"></a>Bandbreitenüberschreitung auf der Clientseite

Je nach Architektur der Clientcomputer bestehen möglicherweise Einschränkungen im Hinblick auf die verfügbare Netzwerkbandbreite. Wenn auf dem Client durch Überladen der Netzwerkkapazität die verfügbare Bandbreite überschritten wird, werden die Daten auf der Clientseite nicht so schnell verarbeitet, wie sie vom Server gesendet werden. Diese Situation kann zu Timeouts führen.

Überwachen Sie die Änderungen bei der Bandbreitennutzung im zeitlichen Verlauf unter Verwendung eines [Beispiel-`BandwidthLogger`](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Dieser Code wird in einigen Umgebungen mit eingeschränkten Berechtigungen (z.B. Azure-Websites) möglicherweise nicht erfolgreich ausgeführt.

Verringern Sie zur Minderung den Netzwerkbandbreitenverbrauch, oder vergrößern Sie die Client-VM auf eine mit höherer Netzwerkkapazität.

### <a name="large-requestresponse-size"></a>Große Anforderungen/große Antworten

Eine große Anforderung oder eine große Antwort kann Timeouts verursachen. Nehmen Sie beispielsweise an, dass auf Ihrem Client ein Timeoutwert von einer Sekunde konfiguriert ist. Ihre Anwendung fordert (unter Verwendung derselben physischen Verbindung) zwei Schlüssel gleichzeitig an (z.B. „A“ und „B“). Die meisten Clients unterstützen das „Pipelining“ für Anforderungen, wobei bei Anforderungen „A“ und „B“ nacheinander gesendet werden, ohne dass auf ihre Antworten gewartet wird. Der Server sendet die Antworten in der gleichen Reihenfolge zurück. Wenn die Antwort „A“ groß ist, kann sie den Großteil der Timeoutzeit für spätere Anforderungen verbrauchen.

Im folgenden Beispiel werden die Anforderungen „A“ und „B“ schnell an den Server gesendet. Der Server beginnt schnell mit dem Senden der Antworten „A“ und „B“. Aufgrund der Datenübertragungszeiten muss Antwort „B“ hinter Antwort „A“ warten und hat ein Timeout, obwohl der Server schnell geantwortet hat.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

Dieses Anforderungs-/Antwortszenario ist schwer zu messen. Sie könnten Ihren Clientcode für die Nachverfolgung von großen Anforderungen und Antworten instrumentieren.

Lösungen für große Antworten variieren, umfass aber unter anderem:

1. Optimieren Sie Ihre Anwendung für eine große Anzahl von kleinen Werten, statt für wenige große Werte.
    - Die bevorzugte Lösung besteht darin, Ihre Daten in verknüpfte kleinere Werte aufzuteilen.
    - Ausführliche Informationen, warum kleinere Werte empfohlen werden, finden Sie im Beitrag [What is the ideal value size range for redis? Is 100 KB too large?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) (Welche Größe ist für Werte bei Redis ideal? Sind 100 KB zu viel?).
1. Erhöhen Sie die Größe Ihres virtuellen Computers, um höhere Bandbreitenkapazitäten zu erhalten.
    - Mehr Bandbreite auf ihrer Client- oder Server-VM kann die Datenübertragungszeiten für größere Antworten verringern.
    - Vergleichen Sie Ihre aktuelle Netzwerknutzung auf beiden Computern mit den Limits Ihrer aktuellen VM-Größe. Mehr Bandbreite nur auf dem Server oder nur auf dem Client ist möglicherweise nicht ausreichend.
1. Erhöhen Sie die Anzahl von Verbindungsobjekten, die Ihre Anwendung verwendet.
    - Verwenden Sie einen Roundrobinansatz, um Anforderungen über verschiedene Verbindungsobjekte auszuführen.

## <a name="server-side-troubleshooting"></a>Serverseitige Problembehandlung

In diesem Abschnitt wird das Behandeln von Problemen beschrieben, die wegen eines Zustands auf dem Cacheserver auftreten.

- [Hohe Speicherauslastung auf dem Server](#memory-pressure-on-the-server)
- [Hohe CPU-Auslastung/hohe Serverauslastung](#high-cpu-usage--server-load)
- [Bandbreitenüberschreitung auf der Serverseite](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Hohe Speicherauslastung auf dem Server

Wenn der Arbeitsspeicher auf der Serverseite sehr stark ausgelastet ist, führt dies zu Leistungsproblemen aller Art und unter Umständen auch zu einer verzögerten Verarbeitung von Anforderungen. Wenn es zu hoher Arbeitsspeicherauslastung kommt, kann das System beginnen, Daten auf den Datenträger auszulagern. Diese sogenannten _Seitenfehler_ bewirken eine deutliche Verlangsamung des Systems. Eine zu hohe Speicherauslastung kann verschiedene Ursachen haben:

- Der Cache wird bis fast zu seiner maximalen Kapazität mit Daten gefüllt.
- Redis stellt eine hohe Fragmentierung des Arbeitsspeichers fest. Diese Fragmentierung wird am häufigsten durch das Speichern großer Objekte verursacht, da Redis für kleine Objekte optimiert ist.

Redis macht zwei Statistiken durch den Befehl [INFO](https://redis.io/commands/info) verfügbar, der Ihnen bei der Identifizierung des folgenden Problems helfen kann: „used_memory“ und „used_memory_rss“. Sie können mithilfe des Portals [diese Metriken anzeigen](cache-how-to-monitor.md#view-metrics-with-azure-monitor).

Sie können mehrere Änderungen vornehmen, um die Speicherauslastung in einem gesunden Rahmen zu halten:

- [Konfigurieren Sie eine Speicherrichtlinie](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) , und legen Sie Ablauffristen für Ihre Schlüssel fest. Diese Richtlinie reicht möglicherweise nicht aus, wenn Fragmentierung vorliegt.
- [Konfigurieren Sie einen Wert für „maxmemory-reserved“](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) , der groß genug ist, um die Speicherfragmentierung zu kompensieren. Weitere Informationen finden Sie weiter unten in den zusätzlichen [Überlegungen zur Arbeitsspeicherreservierung](#considerations-for-memory-reservations).
- Teilen Sie große zwischengespeicherte Objekte in kleinere verknüpfte Objekte auf.
- [Erstellen von Warnungen](cache-how-to-monitor.md#alerts) bei Metriken wie „verwendeter Arbeitsspeicher“, um frühzeitig über mögliche Beeinträchtigungen benachrichtigt zu werden.
- [Skalieren](cache-how-to-scale.md) Sie Ihr System auf einen größeren Cache mit größerer Arbeitsspeicherkapazität hoch.

#### <a name="considerations-for-memory-reservations"></a>Überlegungen zu Arbeitsspeicherreservierungen

Das Aktualisieren der Reservierungswerte für den Arbeitsspeicher, z. B. „maxmemory-reserved“, kann die Cacheleistung beeinträchtigen. Angenommen, Sie haben einen Cache von 53 GB, der mit 49 GB an Daten gefüllt ist. Wenn Sie den Reservierungswert auf 8 GB ändern, sinkt der maximal verfügbare Arbeitsspeicher des Systems auf 45 GB. Wenn der Wert _used_memory_ oder _used_memory_rss_ höher als 45 GB ist, kann das System Daten entfernen, bis sowohl _used_memory_ als auch _used_memory_rss_ unter 45 GB liegen. Durch die Entfernung können sich Serverauslastung und Arbeitsspeicherfragmentierung erhöhen.

### <a name="high-cpu-usage--server-load"></a>Hohe CPU-Auslastung/hohe Serverauslastung

Eine hohe Serverauslastung oder CPU-Auslastung bedeutet, dass der Server Anforderungen nicht rechtzeitig verarbeiten kann. Der Server weist möglicherweise langsame Reaktionszeiten auf und kann mit den Anforderungsraten nicht Schritt halten.

[Überwachen Sie Metriken](cache-how-to-monitor.md#view-metrics-with-azure-monitor) wie CPU- oder Serverauslastung. Beobachten Sie die Spitzen bei der CPU-Auslastung, denn sie korrespondieren mit Timeouts.

Sie können mehrere Änderungen vornehmen, um hohe Serverauslastung zu mindern:

- Untersuchen Sie, was CPU-Spitzen verursacht, z. B. das Ausführen [speicherintensiver Befehle](#expensive-commands) oder Seitenfehler aufgrund hoher Arbeitsspeicherauslastung.
- [Erstellen Sie Warnungen](cache-how-to-monitor.md#alerts) bei Metriken wie CPU- oder Serverauslastung, um frühzeitig über mögliche Beeinträchtigungen benachrichtigt zu werden.
- [Skalieren](cache-how-to-scale.md) Sie Ihr System auf einen größeren Cache mit größerer CPU-Kapazität hoch.

#### <a name="expensive-commands"></a>Speicherintensive Befehle

Nicht alle Redis-Befehle sind gleich. Die Ausführung mancher ist speicherintensiver als die anderer. In der [Dokumentation zu den Redis-Befehlen](https://redis.io/commands) wird die Zeitkomplexität jedes einzelnen Befehls angegeben. Es wird empfohlen, dass Sie die Befehle überprüfen, die Sie für Ihren Cache ausführen, um die Auswirkungen solcher Befehle auf die Leistung zu verstehen. Beispielsweise wird der Befehl [KEYS](https://redis.io/commands/keys) häufig verwendet, ohne zu wissen, dass es sich um einen O(N)-Vorgang handelt. Sie können KEYSA vermeiden, indem Sie [SCAN](https://redis.io/commands/scan) verwenden, um CPU-Spitzen zu verringern.

Mithilfe des Befehls[SLOWLOG](https://redis.io/commands/slowlog) können Sie speicherintensive Befehle messen, die auf dem Server ausgeführt werden.

### <a name="server-side-bandwidth-exceeded"></a>Bandbreitenüberschreitung auf der Serverseite

Verschiedene Cachegrößen verfügen über unterschiedliche Netzwerkbandbreitenkapazitäten. Wenn der Server die verfügbare Bandbreite überschreitet, werden die Daten nicht so schnell an den Client gesendet wie erwartet. Es könnte bei Anforderungen von Clients zu Timeouts kommen, weil der Server die Daten nicht schnell genug an den Client übertragen kann.

Die Metriken „Cache Read“ (Cachelesevorgänge) und „Cache Write“ (Cacheschreibvorgänge) können verwendet werden, um festzustellen, wie viel Bandbreite auf der Serverseite verwendet wird. Sie können [diese Metriken im Portal anzeigen](cache-how-to-monitor.md#view-metrics-with-azure-monitor).

So mindern Sie Situationen, in denen der Netzwerkbandbreitenverbrauch am Rande der maximalen Kapazität liegt

- Ändern Sie das Aufrufverhalten des Clients, um die Beanspruchung des Netzwerks zu reduzieren.
- [Erstellen Sie Warnungen](cache-how-to-monitor.md#alerts) bei Metriken wie „Cache Read“ oder „Cache Write“, um frühzeitig über mögliche Beeinträchtigungen benachrichtigt zu werden.
- [Skalieren](cache-how-to-scale.md) Sie Ihr System auf einen größeren Cache mit größerer Netzwerkbandbreite hoch.

## <a name="data-loss-troubleshooting"></a>Problembehandlung für Datenverluste

Ich habe erwartet, dass sich bestimmte Daten in meiner Azure Cache for Redis-Instanz befinden, aber sie sind scheinbar nicht dort.

Mögliche Ursachen und Lösungen finden Sie unter [What happened to my data in Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) (Was ist mit meinen Daten in Redis passiert?)

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis-Timeoutausnahmen

StackExchange.Redis verwendet für synchrone Vorgänge eine Konfigurationseinstellung mit dem Namen `synctimeout` mit einem Standardwert von 1000 ms. Wenn ein synchroner Aufruf nicht innerhalb dieses Zeitraums abgeschlossen wird, löst der StackExchange.Redis-Client einen Timeoutfehler ähnlich dem folgenden Beispiel aus:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

Diese Fehlermeldung enthält Metriken, mit deren Hilfe Sie die Ursache und die mögliche Lösung des Problems ermitteln können. In der folgenden Tabelle finden Sie Details zu den Metriken in der Fehlermeldung.

| Metrik in der Fehlermeldung | Details |
| --- | --- |
| inst |In der letzten Zeitscheibe: 0 Befehle wurden ausgegeben. |
| mgr |Der Socket-Manager führt `socket.select` aus, was bedeutet, dass er das Betriebssystem auffordert, einen Socket anzugeben, der beschäftigt ist. Der Reader liest nicht aktiv aus dem Netzwerk, weil er meint, dass nichts zu tun ist |
| queue |Es sind insgesamt 73 Vorgänge in Bearbeitung. |
| qu |6 der in Bearbeitung befindlichen Vorgänge befinden sich in der Warteschlange für „Nicht gesendet“ und wurden noch nicht in das Netzwerk für ausgehenden Datenverkehr geschrieben. |
| qs |67 der in Bearbeitung befindlichen Vorgänge wurden an den Server gesendet, aber es ist noch keine Antwort verfügbar. Als Antwort ist Folgendes möglich: `Not yet sent by the server` oder `sent by the server but not yet processed by the client.`. |
| qc |Für 0 der in Bearbeitung befindlichen Vorgänge liegt eine Antwort vor, aber die Vorgänge wurden noch nicht als abgeschlossen markiert, weil noch auf die Abschlussschleife gewartet wird. |
| wr |Es ist ein aktiver Writer vorhanden (d. h., die 6 noch nicht gesendeten Anforderungen werden nicht ignoriert); Bytes/aktive Writer. |
| in |Es sind keine aktiven Reader vorhanden, und null Bytes sind zum Lesen auf der NIC (Network Interface Card, Netzwerkschnittstellenkarte) vorhanden; Bytes/aktive Reader |

### <a name="steps-to-investigate"></a>Untersuchungsschritte

1. Wenn Sie den StackExchange.Redis-Client verwenden, sollten Sie als bewährte Methode zum Herstellen der Verbindung das folgende Muster verwenden.

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

    Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit dem Cache mithilfe von StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Stellen Sie sicher, dass sich Ihr Server und die Clientanwendung in der gleichen Azure-Region befinden. Wenn Ihr Cache z. B. in der Region „USA, Osten“ und Ihr Client in der Region „USA, Westen“ angesiedelt ist, erhalten Sie möglicherweise Timeouts, und die Anforderung wird nicht innerhalb des `synctimeout`-Intervalls abgeschlossen. Auch beim Debuggen über den lokalen Entwicklungscomputer können Timeouts auftreten. 

    Es wird dringend empfohlen, den Cache und den Client in der gleichen Azure-Region anzusiedeln. Wenn Ihr Szenario regionsübergreifende Aufrufe beinhaltet, sollten Sie das `synctimeout`-Intervall auf einen höheren Wert als die standardmäßigen 1.000 ms festlegen. Nehmen Sie zu diesem Zweck eine `synctimeout`-Eigenschaft in die Verbindungszeichenfolge auf. Das folgende Beispiel zeigt einen Codeausschnitt aus einer Verbindungszeichenfolge für StackExchange.Redis, bereitgestellt von Azure Cache for Redis, mit einem `synctimeout` von 2000 ms.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. Verwenden Sie immer die neueste Version des [NuGet-Pakets für StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Es werden ständig Fehler im Code behoben, um ihn widerstandsfähiger gegen Timeouts zu machen. Das Nutzen der neuesten Version ist also wichtig.
1. Wenn Ihre Anforderungen durch Bandbreiteneinschränkungen auf dem Server oder dem Client eingeschränkt sind, dauert deren Ausführung länger, und es können Timeouts auftreten. Um festzustellen, ob ein Timeout durch die verfügbare Netzwerkbandbreite auf dem Server verursacht wird, befolgen Sie die Anweisungen unter [Bandbreitenüberschreitung auf der Serverseite](#server-side-bandwidth-exceeded). Um festzustellen, ob ein Timeout durch die verfügbare Netzwerkbandbreite auf dem Client verursacht wird, befolgen Sie die Anweisungen unter [Bandbreitenüberschreitung auf der Clientseite](#client-side-bandwidth-exceeded).
1. Werden Sie durch die CPU auf dem Server oder auf dem Client behindert?

   - Überprüfen Sie, ob Sie durch die CPU auf Ihrem Client eingeschränkt werden. Eine hohe CPU-Auslastung kann dazu führen, dass die Anforderung nicht innerhalb des `synctimeout`-Intervalls verarbeitet wird und ein Timeout der Anforderung verursachen. Dieses Problem lässt sich möglicherweise durch Verwendung eines größeren Clients oder durch Verteilung der Last lösen.
   - Überprüfen Sie, ob Sie durch die CPU auf dem Server behindert werden. Überwachen Sie dazu die [Cacheleistungsmetrik](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `CPU`. Wenn Anforderungen eingehen, während Redis durch die CPU behindert wird, können für diese Anforderungen Timeouts auftreten. Sie können Abhilfe schaffen, indem Sie die Last auf mehrere Shards in einem Premium-Cache verteilen. Alternativ können Sie ein Upgrade zu einer größer dimensionierten CPU durchführen oder in einen höheren Tarif wechseln. Weitere Informationen finden Sie unter [Bandbreitenüberschreitung auf der Serverseite](#server-side-bandwidth-exceeded).
1. Dauert die Verarbeitung von Befehlen auf dem Server lange? Wenn die Verarbeitung und Ausführung von Befehlen auf dem Redis-Server lange dauert, kann dies zu Timeouts führen. Weitere Informationen zu zeitintensiven Befehlen finden Sie unter [Speicherintensive Befehle](#expensive-commands). Sie können mithilfe des redis-cli-Clients eine Verbindung mit Ihrer Azure Cache for Redis-Instanz herstellen oder die [Redis-Konsole](cache-configure.md#redis-console) verwenden. Führen Sie dann den Befehl [SLOWLOG](https://redis.io/commands/slowlog) aus, um festzustellen, ob Anforderungen langsamer als erwartet sind. Der Redis-Server und StackExchange.Redis sind für viele kleine Anforderungen optimiert, nicht für wenige große. Durch eine Aufteilung Ihrer Daten in kleinere Blöcke können Sie u.U. Verbesserungen erzielen.

    Informationen zum Herstellen einer Verbindung mit dem SSL-Endpunkt Ihres Caches mithilfe von „redis-cli“ und „stunnel“ finden Sie im Blogbeitrag [Announcing ASP.NET Session State Provider for Redis Preview Release](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) (Ankündigung des ASP.NET-Sitzungszustandsanbieters für Redis – Vorschauversion).
1. Eine hohe Auslastung des Redis-Servers kann zu Timeouts führen. Sie können die Serverauslastung überwachen, indem Sie die [Cacheleistungsmetrik](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Redis Server Load` überwachen. Eine Serverauslastung von 100 (Maximalwert) bedeutet, dass der Redis-Server mit der Verarbeitung von Anforderungen voll ausgelastet war und keine Leerlaufzeiten aufgetreten sind. Um festzustellen, ob bestimmte Anforderungen die gesamte Serverkapazität in Anspruch nehmen, führen Sie den Befehl „SlowLog“ aus, wie im vorherigen Absatz beschrieben. Weitere Informationen finden Sie unter „Hohe CPU-Auslastung/hohe Serverauslastung“.
1. Gab es ein anderes Ereignis auf der Clientseite, das ein Netzwerkflackern verursacht haben könnte? Allgemeine Ereignisse umfassen: zentrales Hoch- oder Herunterskalieren der Anzahl von Clientinstanzen, Bereitstellen einer neuen Version des Clients oder Aktivieren der Autoskalierung. Bei unseren Tests haben wir festgestellt, dass Autoskalierung oder zentrales Hoch- oder Herunterskalieren bei ausgehenden Netzwerkverbindungen zu Unterbrechungen von einigen Sekunden verursachen kann. Der StackExchange.Redis-Code ist unempfindlich gegen solche Ereignisse und stellt die Verbindung anschließend wieder her. Beim Wiederherstellen der Verbindung kann für Anforderungen, die sich in der Warteschlange befinden, ein Timeout auftreten.
1. Gab es vor mehreren kleineren Anforderungen eine große Anforderung an den Cache, für die ein Timeout auftrat? Der Parameter `qs` in der Fehlermeldung teilt Ihnen mit, wie viele Anforderungen vom Client an den Server gesendet wurden, für die aber bisher keine Antwort verarbeitet wurde. Dieser Wert kann fortlaufend wachsen, weil StackExchange.Redis eine einzige TCP-Verbindung nutzt und zu einem Zeitpunkt jeweils immer nur eine Antwort lesen kann. Obwohl es beim ersten Vorgang zu einem Timeout gekommen ist, wird das Senden von weiteren Daten an den oder vom Server dadurch nicht unterbrochen. Andere Anforderungen werden blockiert, bis die umfangreiche Anforderung fertig gestellt ist. Hierbei kann es zu Timeouts kommen. Als mögliche Lösung können Sie die Wahrscheinlichkeit von Timeouts verringern. Stellen Sie zu diesem Zweck sicher, dass Ihr Cache für Ihre Workload groß genug ist, und teilen Sie große Werte in kleinere Blöcke auf. Eine weitere Lösung besteht darin, in Ihrem Client einen Pool von `ConnectionMultiplexer`-Objekten zu verwenden und beim Senden einer neuen Anforderung den am wenigsten ausgelasteten `ConnectionMultiplexer` zu verwenden. Durch das Laden über mehrere Verbindungsobjekte können Sie verhindern, dass ein einzelnes Timeout weitere Timeouts für andere Anforderungen nach sich zieht.
1. Wenn Sie `RedisSessionStateProvider` verwenden, müssen Sie das Timeout für Wiederholungsversuche richtig festlegen. `retryTimeoutInMilliseconds` sollte höher sein als `operationTimeoutInMilliseconds`, andernfalls erfolgen keine Wiederholungsversuche. Im folgenden Beispiel ist `retryTimeoutInMilliseconds` auf 3000 festgelegt. Weitere Informationen finden Sie unter [ASP.NET-Sitzungszustandsanbieter für Azure Cache for Redis](cache-aspnet-session-state-provider.md) und [How to use configuration parameters of Session State Provider and Output Cache Provider](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration) (Verwenden der Konfigurationsparameter des Sitzungszustandsanbieters und des Ausgabecacheanbieters).

    ```xml
    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />
    ```

1. Überprüfen Sie die Speicherauslastung auf dem Azure Cache for Redis-Server, indem Sie `Used Memory RSS` und `Used Memory` [überwachen](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Wenn eine Entfernungsrichtlinie vorhanden ist und `Used_Memory` die Cachegröße erreicht, beginnt Redis mit dem Entfernen von Schlüsseln. Im Idealfall sollte `Used Memory RSS` nur geringfügig höher als `Used memory` sein. Ein großer Unterschied bedeutet, dass eine (interne oder externe) Speicherfragmentierung vorhanden ist. Wenn `Used Memory RSS` niedriger ist als `Used Memory`, bedeutet das, dass ein Teil des Cachespeichers vom Betriebssystem ausgelagert wurde. Im Fall einer solchen Auslagerung müssen Sie mit erheblichen Latenzen rechnen. Da Redis nicht steuern kann, wie seine Belegungen den Speicherseiten zugeordnet werden, ist ein hoher Wert für `Used Memory RSS` oft das Ergebnis einer Speicherauslastungsspitze. Wenn Redis-Server Arbeitsspeicher freigibt, übernimmt der Allocator diesen Speicher, wobei er den Speicher dem System zurückgeben kann, aber nicht muss. Es kann zu einer Diskrepanz zwischen dem Wert für `Used Memory` und der vom Betriebssystem gemeldeten Speichernutzung kommen. Arbeitsspeicher wurde möglicherweise von Redis genutzt und freigegeben, aber nicht an das System zurückgegeben. Um Speicherprobleme möglichst gering zu halten, können Sie die folgenden Schritte ausführen:

   - Führen Sie ein Upgrade auf einen größeren Cache durch, damit in Ihrem System genügend Speicher vorhanden ist und entsprechende Einschränkungen wegfallen.
   - Legen Sie Ablauffristen für die Schlüssel fest, damit ältere Werte proaktiv entfernt werden.
   - Überwachen Sie die Cachemetrik `used_memory_rss`. Wenn sich dieser Wert der Größe Ihres Caches nähert, sind Leistungsprobleme absehbar. Falls Sie einen Premium-Cache nutzen, verteilen Sie die Daten über mehrere Shards. Andernfalls führen Sie ein Upgrade auf einen größeren Cache durch.

   Weitere Informationen finden Sie unter [Hohe Speicherauslastung auf dem Server](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Zusätzliche Informationen

- [Welches Azure Cache for Redis-Angebot und welche Redis Cache-Größe sollte ich verwenden?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Wie kann ich die Leistung meines Caches messen und testen?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Wie führe ich Redis-Befehle aus?](cache-faq.md#how-can-i-run-redis-commands)
- [Überwachen von Azure Cache for Redis](cache-how-to-monitor.md)
