---
title: Häufig gestellte Fragen zur Azure Cache for Redis-Verwaltung
description: In diesem Artikel erhalten Sie Antworten auf häufig gestellte Fragen, die Ihnen bei der Verwaltung von Azure Cache for Redis helfen.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: 15c7ed4ca9d04e4bb314eea8b92bef749d2369b1
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537659"
---
# <a name="azure-cache-for-redis-management-faqs"></a>Häufig gestellte Fragen zur Azure Cache for Redis-Verwaltung
Dieser Artikel bietet Antworten auf häufig gestellte Fragen zur Verwaltung von Azure Cache for Redis.

## <a name="common-questions-and-answers"></a>Häufig gestellte Fragen und deren Antworten
In diesem Abschnitt werden die folgenden häufig gestellten Fragen behandelt:

* [Wann sollte ich den nicht für TLS/SSL bestimmten Port für die Verbindungsherstellung mit Redis aktivieren?](#when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis)
* [Welche Best Practices gelten für die Produktion?](#what-are-some-production-best-practices)
* [Was muss bei der Verwendung gängiger Redis-Befehle beachtet werden?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Wie kann ich die Leistung meines Caches messen und testen?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Wichtige Details zum Threadpool-Wachstum](#important-details-about-threadpool-growth)
* [Aktivieren der Garbage Collection auf dem Server, um bei Verwenden von „StackExchange.Redis“ mehr Durchsatz auf dem Client zu erzielen](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Überlegungen zur Leistung im Zusammenhang mit Verbindungen](#performance-considerations-around-connections)

### <a name="when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis"></a>Wann sollte ich den nicht für TLS/SSL bestimmten Port für die Verbindungsherstellung mit Redis aktivieren?
Der Redis-Server bietet keine native TLS-Unterstützung, während dies für Azure Cache for Redis der Fall ist. Wenn Sie eine Verbindung mit Azure Cache for Redis herstellen und Ihr Client TLS unterstützt (z. B. StackExchange.Redis), sollten Sie TLS verwenden.

>[!NOTE]
>Bei neuen Azure Cache for Redis-Instanzen ist der nicht für TLS bestimmte Port standardmäßig deaktiviert. Wenn Ihr Client keine TLS-Unterstützung bietet, müssen Sie den nicht für TLS bestimmten Port gemäß den Anweisungen im Abschnitt [Zugriffsports](cache-configure.md#access-ports) des Artikels [Konfigurieren eines Caches in Azure Cache for Redis](cache-configure.md) aktivieren.
>
>

Redis-Tools wie `redis-cli` funktionieren nicht mit dem TLS-Port, aber Sie können ein Hilfsprogramm wie `stunnel` verwenden, um eine sichere Verbindung zwischen den Tools und dem TLS-Port herzustellen. Anweisungen hierzu finden Sie im Blogbeitrag zur [Ankündigung des ASP.NET-Sitzungsstatusanbieters für Redis-Vorschauversion](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/).

Anweisungen zum Herunterladen der Redis-Tools finden Sie im Abschnitt [Wie führe ich Redis-Befehle aus?](cache-development-faq.md#how-can-i-run-redis-commands) .

### <a name="what-are-some-production-best-practices"></a>Welche Best Practices gelten für die Produktion?
* [Best Practices für StackExchange.Redis](#stackexchangeredis-best-practices)
* [Konfiguration und Konzepte](#configuration-and-concepts)
* [Leistungstests](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Best Practices für StackExchange.Redis
* Legen Sie `AbortConnect` auf „false“ fest, und lassen Sie dann den ConnectionMultiplexer automatisch eine neue Verbindung herstellen. [Ausführliche Informationen finden Sie hier](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Verwenden Sie den ConnectionMultiplexer wieder – erstellen Sie nicht für jede Anforderung einen neuen ConnectionMultiplexer. Sie sollten das [hier gezeigte](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)`Lazy<ConnectionMultiplexer>`-Muster verwenden.
* Redis funktioniert am besten mit kleineren Werten, deshalb sollten Sie die Aufteilung großer Daten in mehrere Schlüssel erwägen. In [dieser Diskussion zu Redis](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) werden 100 KB als groß betrachtet. Lesen Sie [diesen Artikel](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) über ein beispielhaftes Problem, das durch große Werte verursacht werden kann.
* Konfigurieren Sie Ihre [ThreadPool-Einstellungen](#important-details-about-threadpool-growth) , um Timeouts zu verhindern.
* Verwenden Sie mindestens den Standardwert von 5 Sekunden für connectTimeout. Dieses Intervall gibt StackExchange.Redis bei einer Netzwerkunterbrechung genügend Zeit zur erneuten Verbindungsherstellung.
* Berücksichtigen Sie die Leistungskosten für andere Vorgänge, die Sie ausführen. Beispielsweise ist der `KEYS` -Befehl ein O(n)-Vorgang und sollte vermieden werden. Die [redis.io-Website](https://redis.io/commands/) umfasst Details zur Zeitkomplexität für jeden unterstützten Vorgang. Klicken Sie auf jeden Befehl, um die Komplexität für jeden Vorgang anzuzeigen.

#### <a name="configuration-and-concepts"></a>Konfiguration und Konzepte
* Verwenden Sie den Standard- oder Premium-Tarif für Produktionssysteme. Der Basic-Tarif ist ein System mit einem einzelnen Knoten, ohne Datenreplikation und ohne SLA. Verwenden Sie mindestens einen C1-Cache. C0-Caches werden in der Regel für einfache Entwicklungs-/Testszenarien verwendet.
* Beachten Sie, dass Redis ein **In-Memory** -Datenspeicher ist. Lesen Sie [diesen Artikel](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) , um zu erfahren, in welchen Szenarien es zu einem Datenverlust kommen kann.
* Entwickeln Sie Ihr System so, dass es Verbindungsunterbrechungen [aufgrund von Patching- und Failovervorgängen](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md)behandeln kann.

#### <a name="performance-testing"></a>Leistungstests
* Starten Sie mit der Verwendung von `redis-benchmark.exe` , um den möglichen Durchsatz einschätzen zu können, bevor Sie Ihre eigenen Leistungstests schreiben. Weil `redis-benchmark` TLS nicht unterstützt, müssen Sie [den nicht für TLS bestimmten Port über das Azure-Portal aktivieren](cache-configure.md#access-ports), bevor Sie den Test ausführen. Beispiele finden Sie unter [Wie kann ich die Leistung meines Caches messen und testen?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* Die für den Test verwendete Client-VM sollte sich in derselben Region befinden wie Ihre Azure Cache for Redis-Instanz.
* Es wird empfohlen, die Dv2-VM-Serie für Ihren Client zu verwenden, da sie über bessere Hardware verfügt und die besten Ergebnisse liefern dürfte.
* Stellen Sie sicher, dass die von Ihnen ausgewählte Client-VM mindestens über die gleiche Computing- und Bandbreitenkapazität verfügt wie der getestete Cache.
* Aktivieren Sie VRSS auf dem Clientcomputer, wenn Sie unter Windows arbeiten. [Ausführliche Informationen finden Sie hier](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383582(v=ws.11)).
* Redis-Instanzen im Premium-Tarif verfügen über eine bessere Netzwerklatenz und einen höheren Durchsatz, weil sowohl die CPU als auch das Netzwerk auf besserer Hardware ausgeführt werden.

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Was muss bei der Verwendung gängiger Redis-Befehle beachtet werden?

* Vermeiden Sie die Verwendung bestimmter Redis-Befehle mit langer Ausführungszeit, es sei denn, Sie verstehen deren Auswirkungen. Führen Sie beispielsweise nicht den Befehl [KEYS](https://redis.io/commands/keys) in der Produktion aus. Die Rückgabe könnte in Abhängigkeit von der Anzahl von Schlüsseln sehr viel Zeit in Anspruch nehmen. Redis ist ein Server mit Single-Threading, d. h. Befehle werden nacheinander verarbeitet. Wenn Sie nach KEYS weitere Befehle ausgeben, werden diese erst ausgeführt, wenn Redis den KEYS-Befehl verarbeitet hat. Die [redis.io-Website](https://redis.io/commands/) umfasst Details zur Zeitkomplexität für jeden unterstützten Vorgang. Klicken Sie auf jeden Befehl, um die Komplexität für jeden Vorgang anzuzeigen.
* Schlüsselgrößen – sollte ich kleine Schlüssel/Werte oder große Schlüssel/Werte verwenden? Dies hängt vom Szenario ab. Wenn Ihre Szenarios größere Schlüssel erfordern, können Sie den ConnectionTimeout anpassen, dann die Werte erneut ausprobieren und Ihre Logik für erneute Verbindungsversuche anpassen. In Bezug auf den Redis-Server führen kleinere Werte zu einer besseren Leistung.
* Diese Überlegungen bedeuten jedoch nicht, dass Sie in Redis keine größeren Werte speichern können – Sie müssen sich lediglich der folgenden Punkte bewusst sein. Latenzen sind höher. Wenn Sie über einen größeren und einen kleineren Datensatz verfügen, können Sie mehrere ConnectionMultiplexer-Instanzen verwenden und jede mit einem anderen Satz an Werten für Timeout und erneute Verbindungsherstellung konfigurieren – wie im vorangegangenen Abschnitt [Was bewirken die StackExchange.Redis-Konfigurationsoptionen?](cache-development-faq.md#what-do-the-stackexchangeredis-configuration-options-do) beschrieben.

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Wie kann ich die Leistung meines Caches messen und testen?
* [Aktivieren Sie die Cachediagnose](cache-how-to-monitor.md#enable-cache-diagnostics), damit Sie die Integrität Ihres Caches [überwachen](cache-how-to-monitor.md) können. Sie können die Metriken im Azure-Portal anzeigen und anschließend mit einem Tool Ihrer Wahl [herunterladen und prüfen](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) .
* Mit "redis-benchmark.exe" können Sie Auslastungstests für Ihren Redis-Server durchführen.
* Stellen Sie sicher, dass sich Client und Azure Cache for Redis für die Auslastungstests in derselben Region befinden.
* Verwenden Sie "redis-cli.exe", und überwachen Sie den Cache unter Verwendung des INFO-Befehls.
* Wenn Ihre Datenlast zu einer hohen Arbeitsspeicherfragmentierung führt, sollten Sie eine Hochskalierung auf einen größeren Cache durchführen.
* Anweisungen zum Herunterladen der Redis-Tools finden Sie im Abschnitt [Wie führe ich Redis-Befehle aus?](cache-development-faq.md#how-can-i-run-redis-commands) .

Die folgenden Befehle sind ein Beispiel für die Verwendung von „redis-benchmark.exe“. Führen Sie diese Befehle von einem virtuellen Computer in der gleichen Region wie Ihr Cache aus, um genaue Ergebnisse zu erhalten.

* Test von SET-Anforderungen in der Pipeline mithilfe einer 1-K-Nutzlast

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Test von GET-Anforderungen in der Pipeline mithilfe einer 1-K-Nutzlast

>[!NOTE]
> Führen Sie zunächst den oben gezeigten SET-Test aus, um den Cache zu füllen.
>

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

### <a name="important-details-about-threadpool-growth"></a>Wichtige Details zum Threadpool-Wachstum
Der CLR-Threadpool enthält zwei Typen von Threads: Workerthreads und E/A-Abschlussportthreads (IOCP, I/O Completion Port).

* Workerthreads werden für Vorgänge wie das Verarbeiten der Methoden `Task.Run(…)` oder `ThreadPool.QueueUserWorkItem(…)` verwendet. Diese Threads werden auch von verschiedenen Komponenten in der CLR verwendet, wenn Arbeitsvorgänge in einem Hintergrundthread ausgeführt werden müssen.
* IOCP-Threads werden verwendet, wenn asynchrone E/A-Vorgänge ausgeführt werden, z. B. beim Lesen aus dem Netzwerk.

Der Threadpool stellt neue Workerthreads oder E/A-Abschlussthreads nach Bedarf (und ohne Drosselung) bereit, bis die Einstellung für das Minimum des jeweiligen Threadtyps erreicht ist. Standardmäßig entspricht die minimale Anzahl von Threads der Anzahl von Prozessoren in einem System.

Wenn die Anzahl der vorhandenen (ausgelasteten) Threads die „minimale“ Anzahl von Threads erreicht, drosselt der Threadpool die Rate, mit der er neue Threads hinzufügt, auf einen Thread pro 500 Millisekunden. Wenn bei Ihrem System eine Arbeitsspitze eingeht, die einen IOCP-Thread benötigt, werden diese Arbeitsvorgänge in der Regel schnell verarbeitet. Wenn für die Arbeitsspitze jedoch mehr Threads erforderlich sind als in der konfigurierten Einstellung für das Minimum vorgesehen, tritt eine gewisse Verzögerung bei der Verarbeitung einiger Arbeitsvorgänge auf. Der Threadpool wartet darauf, dass zwei Dinge geschehen:

* Ein vorhandener Thread wird frei, um die Arbeitsvorgänge zu verarbeiten.
* Es wird 500 ms lang kein vorhandener Thread frei, weshalb dann ein neuer Thread erstellt wird.

Im Grunde genommen heißt dass: Wenn die Anzahl von ausgelasteten Threads größer ist als die minimale Anzahl von Threads, muss wahrscheinlich eine Verzögerung von 500 ms in Kauf genommen werden, bevor der Netzwerkdatenverkehr von der Anwendung verarbeitet wird. Außerdem gilt es zu beachten, dass ein Thread nach einem längeren Leerlauf als 15 Sekunden (soweit ich mich erinnere) bereinigt wird. Danach kann der Zyklus aus Wachstum und Schrumpfung erneut beginnen.

Betrachten wir eine Beispielfehlermeldung aus StackExchange.Redis (Build 1.0.450 oder höher). Sie sehen, dass nun Threadpool-Statistiken ausgegeben werden (siehe die Details für IOCP und WORKER unten).

```
System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
IOCP: (Busy=6,Free=994,Min=4,Max=1000),
WORKER: (Busy=3,Free=997,Min=4,Max=1000)
```

Im obigen Beispiel sehen Sie, dass bei den IOCP-Threads sechs Threads ausgelastet sind, und dass das System für ein Minimum von vier Threads konfiguriert ist. In diesem Fall wird der Client wahrscheinlich zwei Verzögerungen von 500 ms hingenommen haben, weil 6 größer als 4 ist.

Beachten Sie, dass für StackExchange.Redis ein Timout eintreten kann, wenn IOCP- oder WORKER-Threads gedrosselt werden.

#### <a name="recommendation"></a>Empfehlung

Vor dem Hintergrund dieser Informationen empfehlen wir dringend, als minimalen Wert für IOCP- und WORKER-Threads einen höheren Wert als den Standardwert zu konfigurieren. Wir können keine allgemeingültigen Richtlinien zur Größe dieses Werts geben, weil der richtige Wert für eine Anwendung für eine andere Anwendung wahrscheinlich zu hoch oder zu niedrig sein wird. Diese Einstellung kann sich auch auf die Leistung von anderen Teilen komplexer Anwendungen auswirken. Daher muss jeder Kunde diese Einstellung gemäß seinen eigenen Anforderungen optimieren. Ein guter Ausgangspunkt sind 200 oder 300 Threads. Testen Sie diese Einstellung, und passen Sie sie nach Bedarf an.

So konfigurieren Sie diese Einstellung:

* Wir empfehlen, diese Einstellung programmgesteuert zu ändern, indem Sie die [ThreadPool.SetMinThreads (...) ](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_)-Methode in `global.asax.cs` verwenden. Beispiel:

    ```csharp
    private readonly int minThreads = 200;
    void Application_Start(object sender, EventArgs e)
    {
        // Code that runs on application startup
        AreaRegistration.RegisterAllAreas();
        RouteConfig.RegisterRoutes(RouteTable.Routes);
        BundleConfig.RegisterBundles(BundleTable.Bundles);
        ThreadPool.SetMinThreads(minThreads, minThreads);
    }
    ```

    > [!NOTE]
    > Der von dieser Methode festgelegte Wert ist eine globale Einstellung, die die gesamte AppDomain betrifft. Wenn Sie beispielsweise über einen Computer mit 4 Kernen verfügen und *minWorkerThreads* und *minIOThreads* auf 50 pro CPU während der Laufzeit festlegen möchten, verwenden Sie **ThreadPool.SetMinThreads (200, 200)** .

* Sie können außerdem die Einstellung für die Mindestanzahl von Threads mithilfe der Konfigurationseinstellungen [*minIoThreads* bzw. *minWorkerThreads*](/previous-versions/dotnet/netframework-4.0/7w2sway1(v=vs.100)) unter dem Konfigurationselement `<processModel>` in `Machine.config` angeben, das sich normalerweise in `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\` befindet. **Es wird generell nicht empfohlen, die Mindestanzahl von Threads auf diese Weise festzulegen, weil es sich um eine systemweite Einstellung handelt.**

  > [!NOTE]
  > Der in diesem Konfigurationselement angegebene Wert ist eine Einstellung *pro Kern* . Wenn Ihr Computer z.B. über vier Kerne verfügt und Sie eine *minIoThreads* -Einstellung von 200 zur Laufzeit festlegen möchten, müssen Sie `<processModel minIoThreads="50"/>` verwenden.
  >

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Aktivieren der Garbage Collection auf dem Server-, um bei Verwenden von „StackExchange.Redis“ mehr Durchsatz auf dem Client zu erzielen
Das Aktivieren der Garbage Collection auf dem Server kann den Client optimieren und bei Verwenden von „StackExchange.Redis“ mehr Leistung und Durchsatz ermöglichen. Weitere Informationen zur Garbage Collection auf dem Server und ihrer Aktivierung finden Sie in den folgenden Artikeln:

* [So aktivieren Sie die Garbage Collection auf dem Server](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Grundlagen der Garbage Collection](/dotnet/standard/garbage-collection/fundamentals)
* [Garbage Collection und Leistung](/dotnet/standard/garbage-collection/performance)

### <a name="performance-considerations-around-connections"></a>Überlegungen zur Leistung im Zusammenhang mit Verbindungen

Jeder Tarif hat verschiedene Limits für Clientverbindungen, Speicher und Bandbreite. Obgleich jede Cachegröße eine *bestimmte* Anzahl von Verbindungen zulässt, fällt für jede Verbindung ein Mehraufwand an. Ein Beispiel für einen solchen Aufwand ist die CPU- und Arbeitsspeicherauslastung aufgrund der TLS-/SSL-Verschlüsselung. Das maximale Verbindungslimit für eine angegebene Cachegröße geht von einem geringfügig ausgelasteten Cache aus. Wenn die Last des Verbindungsaufwands *plus* die Last von Clientvorgängen die Systemkapazität überschreiten, können im Cache Kapazitätsprobleme entstehen, auch wenn Sie das Verbindungslimit für die aktuelle Cachegröße nicht überschritten haben.

Weitere Informationen zu den verschiedenen Verbindungsgrenzwerten für die einzelnen Ebenen finden Sie unter [Azure Cache for Redis – Preise](https://azure.microsoft.com/pricing/details/cache/). Weitere Informationen zu Verbindungen und anderen Standardkonfigurationen finden Sie unter [Standardmäßige Redis-Serverkonfiguration](cache-configure.md#default-redis-server-configuration).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über weitere [Häufig gestellte Fragen zu Azure Cache for Redis](cache-faq.md).