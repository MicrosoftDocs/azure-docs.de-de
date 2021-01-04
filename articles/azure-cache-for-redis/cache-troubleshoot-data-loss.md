---
title: Problembehandlung bei Datenverlust in Azure Cache for Redis
description: Erfahren Sie, wie Sie Probleme mit Datenverlusten mit Azure Cache for Redis lösen, z. B. den partiellen Verlust von Schlüsseln, den Ablauf von Schlüsseln oder einen vollständigen Schlüsselverlust.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 6db036752bab7b84b72a37b148eaec7aa5765ef3
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538594"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Problembehandlung bei Datenverlust in Azure Cache for Redis

In diesem Artikel wird erläutert, wie Sie tatsächliche oder erkannte Datenverluste, die in Azure Cache for Redis auftreten können, diagnostizieren.

> [!NOTE]
> Etliche Problembehandlungsschritte in diesem Leitfaden enthalten Anleitungen zum Ausführen von Redis-Befehlen und zum Überwachen verschiedener Leistungsmetriken. Weitere Informationen und Anleitungen finden Sie in den Artikeln im Abschnitt [Weitere Informationen](#additional-information) .
>

## <a name="partial-loss-of-keys"></a>Partieller Verlust von Schlüsseln

Azure Cache for Redis löscht Schlüssel nicht nach dem Zufallsprinzip, nachdem sie im Arbeitsspeicher gespeichert wurden. Es löscht Schlüssel jedoch als Reaktion auf Ablauf- oder Entfernungsrichtlinien sowie auf explizite Befehle zum Löschen von Schlüsseln. Auch sind Schlüssel, die in den primären Knoten in einer Azure Cache for Redis-Instanz (Tarif „Standard“ oder „Premium“) geschrieben wurden, möglicherweise nicht sofort auf einem Replikat verfügbar. Daten werden asynchron und auf nicht blockierende Weise vom primären Knoten auf dem Replikat repliziert.

Wenn Sie feststellen, dass Schlüssel aus dem Cache verschwunden sind, sollten Sie auf folgende mögliche Ursachen prüfen:

| Ursache | Beschreibung |
|---|---|
| [Ablauf von Schlüsseln](#key-expiration) | Schlüssel werden entfernt, weil für sie Timeouts festgelegt wurden. |
| [Entfernen von Schlüsseln](#key-eviction) | Schlüssel werden bei hoher Arbeitsspeicherauslastung entfernt. |
| [Löschen von Schlüsseln](#key-deletion) | Schlüssel werden durch explizite Löschbefehle entfernt. |
| [Asynchrone Replikation](#async-replication) | Schlüssel sind aufgrund von Verzögerungen bei der Datenreplikation auf einem Replikat nicht verfügbar. |

### <a name="key-expiration"></a>Ablauf von Schlüsseln

Azure Cache for Redis entfernt einen Schlüssel automatisch, wenn dem Schlüssel ein Timeout zugewiesen und der zugehörige Zeitraum abgelaufen ist. Weitere Informationen zum Ablauf von Redis-Schlüsseln finden Sie in der Dokumentation des Befehls [EXPIRE](https://redis.io/commands/expire). Timeoutwerte können auch über die Befehle [SET](https://redis.io/commands/set), [SETEX](https://redis.io/commands/setex) und [GETSET](https://redis.io/commands/getset) sowie weitere **\*STORE** -Befehle festgelegt werden.

Wenn Sie Statistiken abrufen möchten, wie viele Schlüssel abgelaufen sind, verwenden Sie den Befehl [INFO](https://redis.io/commands/info). Im Abschnitt `Stats` wird die Gesamtzahl abgelaufener Schlüssel angezeigt. Der Abschnitt `Keyspace` enthält weitere Informationen zur Anzahl der Schlüssel mit Timeouts und zum durchschnittlichen Timeoutwert.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

Außerdem können Sie die Diagnosemetriken für Ihren Cache überprüfen, um festzustellen, ob es eine Korrelation zwischen dem fehlendem Schlüssel und einer Spitze bei den abgelaufenen Schlüsseln gibt. Im Anhang von [Debugging Redis Keyspace Misses](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) finden Sie unter „Keyspace Notifications“ Informationen zur Verwendung von Keyspace-Benachrichtigungen sowie unter **MONITOR** Informationen, wie Probleme dieses Typs debuggt werden.

### <a name="key-eviction"></a>Entfernen von Schlüsseln

Azure Cache for Redis benötigt Arbeitsspeicher, um Daten zu speichern. Es löscht bei Bedarf Schlüssel, um verfügbaren Speicher freizugeben. Wenn sich der Wert von **used_memory** oder **used_memory_rss** im Befehl [INFO](https://redis.io/commands/info) dem für die Einstellung **maxmemory** konfigurierten Wert nähert, beginnt Azure Cache for Redis aufgrund der [Cacherichtlinie](https://redis.io/topics/lru-cache) mit dem Entfernen von Schlüsseln.

Mit dem Befehl [INFO](https://redis.io/commands/info) können Sie die Anzahl der entfernten Schlüssel überwachen.

```
# Stats

evicted_keys:13224
```

Außerdem können Sie die Diagnosemetriken für Ihren Cache überprüfen, um festzustellen, ob es eine Korrelation zwischen dem fehlendem Schlüssel und einer Spitze bei den entfernten Schlüsseln gibt. Im Anhang von [Debugging Redis Keyspace Misses](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) finden Sie unter „Keyspace Notifications“ Informationen zur Verwendung von Keyspace-Benachrichtigungen sowie unter **MONITOR** Informationen, wie Probleme dieses Typs debuggt werden.

### <a name="key-deletion"></a>Löschen von Schlüsseln

Redis-Clients können den Befehl [DEL](https://redis.io/commands/del) oder [HDEL](https://redis.io/commands/hdel) ausgeben, um Schlüssel explizit aus Azure Cache for Redis zu entfernen. Mit dem Befehl [INFO](https://redis.io/commands/info) können Sie die Anzahl der Löschvorgänge verfolgen. Aufrufe von **DEL** - oder **HDEL** -Befehlen werden im Abschnitt `Commandstats` aufgelistet.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Asynchrone Replikation

Jede Azure Cache for Redis-Instanz ist sowohl im Tarif „Standard“ als auch im Tarif „Premium“ mit einem primären Knoten und mindestens einem Replikat konfiguriert. Daten werden in einem Hintergrundprozess asynchron vom primären Knoten in ein Replikat kopiert. Auf der Website [redis.io](https://redis.io/topics/replication) wird beschrieben, wie die Redis-Datenreplikation im Allgemeinen funktioniert. In Szenarien, in denen Clients häufig in Redis schreiben, kann ein partieller Datenverlust auftreten, weil nicht garantiert werden kann, dass diese Replikation sofort ausgeführt wird. Wenn beispielsweise der primäre Knoten heruntergefahren wird, *nachdem* ein Client einen Schlüssel in den Knoten geschrieben hat, aber *bevor* der Hintergrundprozess die Möglichkeit hatte, diesen Schlüssel an das Replikat zu senden, geht der Schlüssel verloren, wenn das Replikat als neuer primärer Knoten verwendet wird.

## <a name="major-or-complete-loss-of-keys"></a>Größerer oder kompletter Verlust von Schlüsseln

Sind die meisten oder alle Schlüssel aus dem Cache verschwunden, sollten Sie auf folgende mögliche Ursachen prüfen:

| Ursache | Beschreibung |
|---|---|
| [Leeren von Schlüsseln](#key-flushing) | Schlüssel wurden manuell gelöscht. |
| [Falsche Datenbankauswahl](#incorrect-database-selection) | Azure Cache for Redis ist so festgelegt, dass eine Nicht-Standarddatenbank verwendet wird. |
| [Redis-Instanzausfall](#redis-instance-failure) | Der Redis-Server ist nicht verfügbar. |

### <a name="key-flushing"></a>Leeren von Schlüsseln

Clients können den Befehl [FLUSHDB](https://redis.io/commands/flushdb) aufrufen, um alle Schlüssel in einer *einzelnen* Datenbank zu entfernen. Mit dem Befehl [FLUSHALL](https://redis.io/commands/flushall) werden alle Schlüssel aus *allen* Datenbanken in einem Redis-Cache entfernt. Wenn Sie feststellen möchten, ob Schlüssel geleert (entfernt) wurden, verwenden Sie den Befehl [INFO](https://redis.io/commands/info). Im `Commandstats`-Abschnitt ist zu sehen, ob **FLUSH** -Befehle aufgerufen wurden:

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Falsche Datenbankauswahl

Azure Cache for Redis verwendet standardmäßig die Datenbank **db0** . Wenn Sie zu einer anderen Datenbank wechseln (z. B. **db1** ) und versuchen, Schlüssel aus ihr zu lesen, kann Azure Cache for Redis dort keine Schlüssel finden. Jede Datenbank ist eine logisch separate Einheit und enthält ein anderes Dataset. Wählen Sie mit dem Befehl [SELECT](https://redis.io/commands/select) andere verfügbare Datenbanken aus, und suchen Sie dort jeweils nach Schlüsseln.

### <a name="redis-instance-failure"></a>Redis-Instanzausfall

Redis ist ein In-Memory-Datenspeicher. Daten verbleiben auf dem physischen oder virtuellen Computer, auf dem der Redis-Cache gehostet wird. Eine Azure Cache for Redis-Instanz im Tarif „Basic“ wird nur auf einem einzelnen virtuellen Computer (VM) ausgeführt. Wenn dieser virtuelle Computer ausfällt, gehen alle Daten verloren, die Sie im Cache gespeichert haben. 

Caches der Tarife „Standard“ und „Premium“ bieten eine wesentlich höhere Resilienz vor Datenverlusten, da zwei virtuelle Computer in einer replizierten Konfiguration verwendet werden. Wenn der primäre Knoten für einen solchen Cache ausfällt, übernimmt der Replikatknoten automatisch die Bereitstellung der Daten. Diese virtuellen Computer befinden sich in getrennten Domänen für Ausfälle und Updates, um das Risiko zu minimieren, dass beide gleichzeitig nicht verfügbar sind. Kommt es zu einem größeren Ausfall des Rechenzentrums, können die virtuellen Computer dennoch gemeinsam ausfallen. In diesen seltenen Fällen gehen Ihre Daten verloren.

Um dies zu verhindern, können Sie [Redis-Datenpersistenz](https://redis.io/topics/persistence) und [Georeplikation](./cache-how-to-geo-replication.md) verwenden, um den Schutz Ihrer Daten vor derartigen Infrastrukturausfällen zu erhöhen.

## <a name="additional-information"></a>Zusätzliche Informationen

- [Behandeln von serverseitigen Problemen bei Azure Cache for Redis](cache-troubleshoot-server.md)
- [Auswählen der richtigen Ebene](cache-overview.md#choosing-the-right-tier)
- [Überwachen von Azure Cache for Redis](cache-how-to-monitor.md)
- [Wie führe ich Redis-Befehle aus?](cache-development-faq.md#how-can-i-run-redis-commands)