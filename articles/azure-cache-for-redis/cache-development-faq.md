---
title: Häufig gestellte Fragen zur Azure Cache for Redis-Entwicklung
description: In diesem Artikel erhalten Sie Antworten auf häufig gestellte Fragen, die Ihnen bei der Entwicklung für Azure Cache for Redis helfen.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: be2e4a002d1daf4da7d042f1fd7d5bf0e9a01377
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92544510"
---
# <a name="azure-cache-for-redis-development-faqs"></a>Häufig gestellte Fragen zur Azure Cache for Redis-Entwicklung

Dieser Artikel bietet Antworten auf häufig gestellte Fragen zur Entwicklung für Azure Cache for Redis.

## <a name="common-questions-and-answers"></a>Häufig gestellte Fragen und deren Antworten
In diesem Abschnitt werden die folgenden häufig gestellten Fragen behandelt:

* [Wie kann ich mit Azure Cache for Redis starten?](#how-can-i-get-started-with-azure-cache-for-redis)
* [Was bewirken die Konfigurationsoptionen für "StackExchange.Redis"?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Welche Azure Cache for Redis-Clients kann ich verwenden?](#what-azure-cache-for-redis-clients-can-i-use)
* [Gibt es einen lokalen Emulator für Azure Cache for Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Wie führe ich Redis-Befehle aus?](#how-can-i-run-redis-commands)
* [Warum gibt es für Azure Cache for Redis keinen MSDN-Klassenbibliothekverweis?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference)
* [Kann ich Azure Cache for Redis als PHP-Sitzungscache verwenden?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Was sind Redis-Datenbanken?](#what-are-redis-databases)

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Wie kann ich mit Azure Cache for Redis starten?
Es gibt verschiedene Möglichkeiten, mit Azure Cache for Redis zu starten.

* Sie können sich eines unserer Tutorials ansehen, verfügbar für [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md) und [Python](cache-python-get-started.md).
* Sie können sich das Video [How to Build High Performance Apps Using Microsoft Azure Cache for Redis](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/) (So erstellen Sie mit Microsoft Azure Cache for Redis hochleistungsfähige Apps) ansehen.
* Sie können sich die Clientdokumentation für die Clients für die jeweilige Entwicklersprache Ihres Projekts ansehen, um weitere Informationen zur Verwendung von Redis zu erhalten. Es gibt viele Redis-Clients, die mit Azure Cache for Redis verwendet werden können. Eine Liste der Redis-Clients finden Sie unter [https://redis.io/clients](https://redis.io/clients).

Wenn Sie noch kein Azure-Konto besitzen, haben Sie folgende Möglichkeiten:

* [Kostenloses Anlegen eines Azure-Kontos](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Sie erhalten ein Guthaben, mit dem Sie andere kostenpflichtige Azure-Dienste ausprobieren können. Auch nachdem Sie das Guthaben aufgebraucht haben, können Sie das Konto behalten und kostenlose Azure-Dienste und -Features nutzen.
* [Aktivieren Sie Visual Studio-Abonnementvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Ihr MSDN-Abonnement beinhaltet ein monatliches Guthaben, das Sie für zahlungspflichtige Azure-Dienste verwenden können.

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Was bewirken die Konfigurationsoptionen für "StackExchange.Redis"?
Für "StackExchange.Redis" stehen zahlreiche Optionen zur Verfügung. In diesem Abschnitt werden einige der gängigsten Optionen erläutert. Ausführlichere Informationen zu den StackExchange.Redis-Optionen finden Sie unter [StackExchange.Redis-Konfiguration](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | BESCHREIBUNG | Empfehlung |
| --- | --- | --- |
| AbortOnConnectFail |Wenn Sie diese Option auf "true" setzen, wird die Verbindung nach einem Netzwerkausfall nicht wiederhergestellt. |Bei Festlegung auf "false" kann "StackExchange.Redis" die Verbindung automatisch wiederherstellen. |
| ConnectRetry |Die Anzahl von Versuchen für die Verbindungsherstellung bei der anfänglichen Verbindung. |Die folgenden Informationen bieten eine Orientierung. |
| ConnectTimeout |Timeout in Millisekunden für Verbindungsvorgänge. |Die folgenden Informationen bieten eine Orientierung. |

In der Regel reichen die Standardwerte des Clients aus. Sie können die Optionen basierend auf Ihrer Workload optimieren.

* **Wiederholungsversuche**
  * Für „ConnectRetry“ und „ConnectTimeout“ lautet die allgemeine Empfehlung, Fail-Fast-fähig zu sein und den Vorgang zu wiederholen. Diese Empfehlung hängt von Ihrer Workload und davon ab, wie lange es auf Ihrem Client durchschnittlich dauert, einen Redis-Befehl auszugeben und eine Antwort zu empfangen.
  * Ermöglichen Sie eine automatische Neuverbindung durch "StackExchange.Redis", statt die Prüfung des Verbindungsstatus und die erneute Verbindungsherstellung selbst zu prüfen. **Vermeiden Sie die Verwendung der ConnectionMultiplexer.IsConnected-Eigenschaft** .
  * Schneeballeffekt: Gelegentlich kann es zu Problemen kommen, bei denen Wiederholungsversuche zu nicht behebbaren, immer wiederkehrenden Problemen führen. Wenn der Schneeballeffekt auftritt, sollten Sie die Verwendung eines exponentiellen Backoff/Retry-Algorithmus in Erwägung ziehen, wie er im Artikel [Allgemeiner Leitfaden zum Wiederholen von Vorgängen](/azure/architecture/best-practices/transient-faults) der Microsoft Patterns & Practices-Gruppe beschrieben wird.
  
* **Timeoutwerte**
  * Berücksichtigen Sie Ihre Workload, und legen Sie die Werte entsprechend fest. Wenn Sie große Werte speichern, legen Sie die Timeouteinstellung auf einen höheren Wert fest.
  * Legen Sie `AbortOnConnectFail` auf „false“ fest, und überlassen Sie StackExchange.Redis die erneute Verbindungsherstellung.
  * Verwenden Sie eine einzelne ConnectionMultiplexer-Instanz für die Anwendung. Sie können "LazyConnection" verwenden, um eine einzelne Instanz zu erstellen, die über eine Connection-Eigenschaft zurückgegeben wird, wie gezeigt in [Verbindungsherstellung mit dem Cache unter Verwendung der ConnectionMultiplexer-Klasse](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Legen Sie die `ConnectionMultiplexer.ClientName` -Eigenschaft zu Diagnosezwecken auf einen eindeutigen App-Instanznamen fest.
  * Verwenden Sie für benutzerdefinierte Workloads mehrere `ConnectionMultiplexer` -Instanzen.
      * Sie können diesem Modell folgen, wenn die Last in Ihrer Anwendung variiert. Beispiel:
      * Sie können einen Multiplexer zur Verarbeitung großer Schlüssel verwenden.
      * Sie können einen Multiplexer zur Verarbeitung kleiner Schlüssel verwenden.
      * Sie können verschiedene Werte für Verbindungstimeouts und eine retry-Logik für jede verwendete ConnectionMultiplexer-Instanz verwenden.
      * Legen Sie die `ClientName` -Eigenschaft für jeden Multiplexer fest, um die Diagnose zu vereinfachen.
      * Diese Empfehlung kann zu einer optimierten Latenz pro `ConnectionMultiplexer` führen.

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Welche Azure Cache for Redis-Clients kann ich verwenden?
Einer der großen Vorteile von Redis ist, dass es viele Clients gibt, die viele verschiedene Programmiersprachen unterstützen. Eine aktuelle Liste von Clients finden Sie unter [Redis-Clients](https://redis.io/clients). Tutorials, in denen mehrere verschiedene Sprachen und Clients behandelt werden, finden Sie unter [Verwenden von Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md) und in den zugehörigen Artikeln im Inhaltsverzeichnis.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Gibt es einen lokalen Emulator für Azure Cache for Redis?
Für Azure Cache for Redis ist kein lokaler Emulator verfügbar. Sie können jedoch die MSOpenTech-Version von „redis-server.exe“ über die [Redis-Befehlszeilentools](https://github.com/MSOpenTech/redis/releases/) auf dem lokalen Computer ausführen und eine Verbindung herstellen, um ein ähnliches Verhalten wie bei einem lokalen Cache-Emulator zu erhalten. Dies wird im folgenden Beispiel gezeigt:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        // Connect to a locally running instance of Redis to simulate
        // a local cache emulator experience.
        return ConnectionMultiplexer.Connect("127.0.0.1:6379");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

Optional können Sie die Datei [redis.conf](https://redis.io/topics/config) konfigurieren, um bei Bedarf eine bessere Abstimmung mit den [standardmäßigen Cacheeinstellungen](cache-configure.md#default-redis-server-configuration) für Ihren Online-Azure Cache for Redis zu erreichen.

### <a name="how-can-i-run-redis-commands"></a>Wie führe ich Redis-Befehle aus?
Sie können alle aufgelisteten [Redis-Befehle](https://redis.io/commands#) mit Ausnahme der Befehle verwenden, die unter [Redis-Befehle, die in Azure Cache for Redis nicht unterstützt werden](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis) aufgeführt sind. Für die Ausführung von Redis-Befehlen haben Sie mehrere Optionen.

* Wenn Sie über einen Standard- oder Premium-Cache verfügen, können Sie Redis-Befehle über die [Redis-Konsole](cache-configure.md#redis-console)ausführen. Die Redis-Konsole ist eine sichere Methode zum Ausführen von Redis-Befehlen im Azure-Portal.
* Sie können auch die Redis-Befehlszeilentools verwenden. Führen Sie hierzu die folgenden Schritte aus:
* Laden Sie die [Redis-Befehlszeilentools](https://github.com/MSOpenTech/redis/releases/) herunter.
* Stellen Sie über `redis-cli.exe`eine Verbindung mit dem Cache her. Übergeben Sie den Cacheendpunkt mithilfe des Schalters „-h“ und dem Schlüssel mit Verwendung von „-a“, wie im folgenden Beispiel gezeigt:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Die Redis-Befehlszeilentools funktionieren nicht mit dem TLS-Port, aber Sie können ein Hilfsprogramm wie `stunnel` verwenden, um eine sichere Verbindung zwischen den Tools und dem TLS-Port herzustellen. Anweisungen hierzu finden Sie unter [Verwenden des Redis-Befehlszeilentools mit Azure Cache for Redis](./cache-how-to-redis-cli-tool.md).
>
>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference"></a>Warum gibt es für Azure Cache for Redis keinen MSDN-Klassenbibliothekverweis ?
Microsoft Azure Cache for Redis basiert auf Redis, dem beliebten Open-Source-basierten In-Memory-Datenspeicher. Der Zugriff kann über eine Vielzahl von [Redis-Clients](https://redis.io/clients) für zahlreiche Programmiersprachen erfolgen. Jeder Client verfügt über eine eigene API, die unter Verwendung von [Redis-Befehlen](https://redis.io/commands) Aufrufe an den Azure Cache for Redis sendet.

Da jeder Client anders ist, gibt es nicht nur einen zentralen Klassenverweis in MSDN, und jeder Client verwaltet seine eigene Verweisdokumentation. Zusätzlich zur Referenzdokumentation gibt es mehrere Tutorials, die Ihnen den Einstieg in die Verwendung von Azure Cache for Redis mit verschiedenen Sprachen und Cacheclients erleichtern. Sie können unter [Verwenden von Azure Cache for Redis](cache-dotnet-how-to-use-azure-redis-cache.md) und in den zugehörigen Artikeln im Inhaltsverzeichnis auf diese Tutorials zugreifen.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Kann ich Azure Cache for Redis als PHP-Sitzungscache verwenden?
Ja. Um Azure Cache for Redis als PHP-Sitzungscache zu verwenden, geben Sie die Verbindungszeichenfolge zu Ihrer Azure Cache for Redis-Instanz in `session.save_path` an.

> [!IMPORTANT]
> Bei der Verwendung von Azure Cache for Redis als PHP-Sitzungscache müssen Sie – wie im folgenden Beispiel dargestellt – eine URL-Codierung des Sicherheitsschlüssels durchführen, der zum Herstellen der Verbindung mit dem Cache verwendet wird:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Wurde keine URL-Codierung des Schlüssels vorgenommen, erhalten Sie ggf. eine Ausnahmemeldung ähnlich der folgenden: `Failed to parse session.save_path`
>

Weitere Informationen zur Nutzung von Azure Cache for Redis als PHP-Sitzungscache mit dem PhpRedis-Client finden Sie unter [PHP Session handler](https://github.com/phpredis/phpredis#php-session-handler)(PHP-Sitzungshandler).

### <a name="what-are-redis-databases"></a>Was sind Redis-Datenbanken?

Redis-Datenbanken sind einfach eine logische Trennung von Daten innerhalb der gleichen Redis-Instanz. Der Cachespeicher wird von allen Datenbanken gemeinsam genutzt, und die tatsächliche Arbeitsspeichernutzung einer bestimmten Datenbank richtet sich nach den in dieser Datenbank gespeicherten Schlüsseln bzw. Werten. Ein Beispiel: Ein C6-Cache verfügt über 53 GB Arbeitsspeicher, ein P5 über 120 GB. Sie können die Gesamtmenge von 53 GB/120 GB einer einzigen Datenbank zuweisen oder das Datenvolumen auf mehrere Datenbanken aufteilen. 

> [!NOTE]
> Wenn Sie einen Premium-Azure Cache for Redis mit aktiviertem Clustering verwenden, ist nur Datenbank 0 verfügbar. Dies ist eine intrinsische Einschränkung von Redis und nicht spezifisch für Azure Cache for Redis. Weitere Informationen finden Sie unter [Muss ich Änderungen an meiner Clientanwendung vornehmen, um Clustering verwenden zu können?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering).
> 
> 

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über weitere [Häufig gestellte Fragen zu Azure Cache for Redis](cache-faq.md).