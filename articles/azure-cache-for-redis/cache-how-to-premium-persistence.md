---
title: Konfigurieren von Datenpersistenz für Azure Cache for Redis vom Typ „Premium“
description: Erfahren Sie, wie Sie die Datenpersistenz für Ihren Premium Azure Cache for Redis mit Premium-Tarif konfigurieren und verwalten.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: b01cf279-60a0-4711-8c5f-af22d9540d38
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: yegu
ms.openlocfilehash: de0b2e3ef7b0268540ef4896ade132a297ee88ff
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60543442"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>Konfigurieren von Datenpersistenz für Azure Cache for Redis vom Typ „Premium“
Für Azure Cache for Redis stehen verschiedene Cacheangebote bereit, die Flexibilität bei der Auswahl von Cachegröße und -features bieten, einschließlich Features des Premium-Tarifs wie die Unterstützung für Clustering, Persistenz und virtuelle Netzwerke. In diesem Artikel wird erläutert, wie die Persistenz in einer Azure Cache for Redis-Instanz vom Typ „Premium“ konfiguriert wird.

Weitere Informationen zu anderen Premium-Cache-Features finden Sie unter [Einführung in den Premium-Tarif von Azure Cache for Redis](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>Was ist Datenpersistenz?
[Redis-Persistenz](https://redis.io/topics/persistence) ermöglicht die dauerhafte Speicherung von Daten in Redis. Sie können zudem Momentaufnahmen erstellen und die Daten sichern, die Sie dann im Fall eines Hardwarefehlers laden können. Dies ist ein großer Vorteil gegenüber dem Basic- oder Standard-Tarif, bei denen alle Daten im Arbeitsspeicher gespeichert sind, sodass bei einem Fehler, bei dem Cacheknoten ausfallen, möglicherweise Daten verloren gehen. 

Azure Cache for Redis bietet Redis-Persistenz über die folgenden Modelle:

* **RDB-Persistenz:** Wenn RDB-Persistenz (Redis-Datenbank) konfiguriert ist, speichert Azure Cache for Redis basierend auf einer wählbaren Sicherungshäufigkeit eine Momentaufnahme des Azure Cache for Redis in einem binären Redis-Format dauerhaft auf dem Datenträger. Bei einem schwerwiegenden Fehler, bei dem der primäre sowie der Replikatcache deaktiviert werden, wird der Cache mithilfe der neuesten Momentaufnahme wiederhergestellt. Erfahren Sie mehr über die [Vorteile](https://redis.io/topics/persistence#rdb-advantages) und [Nachteile](https://redis.io/topics/persistence#rdb-disadvantages) der RDB-Persistenz.
* **AOF-Persistenz:** Wenn die AOF-Persistenz (Append only file, nur Datei anhängen) konfiguriert ist, speichert Azure Cache for Redis jeden Schreibvorgang in einem Protokoll, das mindestens einmal pro Sekunde in einem Azure Storage-Konto gespeichert wird. Bei einem schwerwiegenden Fehler, bei dem der primäre und der Replikatcache deaktiviert werden, wird der Cache mithilfe der gespeicherten Schreibvorgänge wiederhergestellt. Erfahren Sie mehr über die [Vorteile](https://redis.io/topics/persistence#aof-advantages) und [Nachteile](https://redis.io/topics/persistence#aof-disadvantages) der AOF-Persistenz.

Persistenz wird während der Erstellung des Caches auf dem Blatt **Neuer Azure Cache for Redis** sowie für vorhandene Premium-Caches im **Ressourcenmenü** konfiguriert.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Wenn Sie einen Premium-Tarif ausgewählt haben, klicken Sie auf **Redis persistence**.

![Redis persistence][redis-cache-persistence]

Die Schritte im nächsten Abschnitt beschreiben, wie Sie Redis-Persistenz für Ihren neuen Premium-Cache konfigurieren. Nachdem Redis-Persistenz konfiguriert wurde, klicken Sie auf **Erstellen** , um den neuen Premium-Cache mit Redis-Persistenz zu erstellen.

## <a name="enable-redis-persistence"></a>Aktivieren der Redis-Persistenz

Redis-Persistenz wird auf dem Blatt **Redis-Datenpersistenz** durch Auswählen von **RDB**- oder **AOF**-Persistenz aktiviert. Bei einem neuen Cache wird während der Erstellung des Caches auf dieses Blatt zugegriffen, wie im vorherigen Abschnitt beschrieben. Für vorhandene Caches erfolgt der Zugriff auf das Blatt **Redis-Datenpersistenz** über das **Ressourcenmenü** für Ihren Cache.

![Redis-Einstellungen][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>Konfigurieren der RDB-Persistenz

Klicken Sie zum Aktivieren der RDB-Persistenz auf **RDB**. Um die RDB-Persistenz für einen zuvor aktivierten Premium-Cache zu deaktivieren, klicken Sie auf **Deaktiviert**.

![Redis-RDB-Persistenz][redis-cache-rdb-persistence]

Zum Konfigurieren des Sicherungsintervalls wählen Sie in der Dropdownliste eine **Sicherungshäufigkeit** aus. Zur Auswahl stehen **15 Minuten**, **30 Minuten**, **60 Minuten**, **6 Stunden**, **12 Stunden** und **24 Stunden**. Dieses Intervall wird ab dem Moment rückwärts gezählt, an dem der vorherige Sicherungsvorgang erfolgreich abgeschlossen wird. Wenn das Intervall abgelaufen ist, wird eine neue Sicherung gestartet.

Klicken Sie auf **Speicherkonto**, um das Speicherkonto auszuwählen, und wählen Sie entweder den **primären** oder den **sekundären Schlüssel** aus der Dropdownliste **Speicherschlüssel** aus. Sie müssen ein Speicherkonto auswählen, das aus der gleichen Region wie der Cache stammt, und wir empfehlen ein **Storage Premium** -Konto, da dieses einen höheren Durchsatz aufweist. 

> [!IMPORTANT]
> Wenn der Speicherschlüssel für Ihr Persistenzkonto neu generiert wird, müssen Sie den gewünschten Schlüssel über die Dropdownliste **Speicherschlüssel** neu konfigurieren.
> 
> 

Klicken Sie auf **OK** , um die Persistenzkonfiguration zu speichern.

Die nächste Sicherung (oder erste Sicherung bei neuen Caches) wird gestartet, sobald das Intervall für die Sicherungshäufigkeit abgelaufen ist.

## <a name="configure-aof-persistence"></a>Konfigurieren der AOF-Persistenz

Klicken Sie zum Aktivieren der AOF Persistenz auf **AOF**. Um AOF-Persistenz für einen zuvor aktivierten Premium-Cache zu deaktivieren, klicken Sie auf **Deaktiviert**.

![Redis-AOF-Persistenz][redis-cache-aof-persistence]

Geben Sie zum Konfigurieren der AOF Persistenz ein **Konto für ersten Speicher** an. Dieses Speicherkonto muss aus der gleichen Region wie der Cache stammen. Es wird empfohlen, ein **Storage Premium**-Konto zu verwenden, da dieses einen höheren Durchsatz aufweist. Optional können Sie ein zusätzliches Speicherkonto mit dem Namen **Konto für zweiten Speicher** konfigurieren. Wenn ein zweites Storage-Konto konfiguriert wurde, werden Schreibvorgänge im Replikatcache in dieses zweite Speicherkonto geschrieben. Wählen Sie für jedes konfigurierte Speicherkonto entweder den **primären** oder den **sekundären Schlüssel** in der Dropdownliste **Speicherschlüssel** aus. 

> [!IMPORTANT]
> Wenn der Speicherschlüssel für Ihr Persistenzkonto neu generiert wird, müssen Sie den gewünschten Schlüssel über die Dropdownliste **Speicherschlüssel** neu konfigurieren.
> 
> 

Wenn AOF-Persistenz aktiviert wurde, werden Schreibvorgänge in den Cache im angegebenen Speicherkonto gespeichert (oder in den Konten, wenn Sie ein zweites Speicherkonto konfiguriert haben). Bei einem schwerwiegenden Fehler, der zu einem Ausfall des primären und des Replikatcaches führt, wird das gespeicherte AOF-Protokoll für die Neuerstellung des Caches verwendet.

## <a name="persistence-faq"></a>Persistenz – häufig gestellte Fragen
Die folgende Liste enthält Antworten auf häufig gestellte Fragen zur Persistenz von Azure Cache for Redis-Instanzen.

* [Kann ich die Persistenz für einen bereits erstellten Cache aktivieren?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Kann ich AOF- und RDB-Persistenz gleichzeitig aktivieren?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Welches Persistenzmodell sollte ich auswählen?](#which-persistence-model-should-i-choose)
* [Was geschieht, wenn ich auf eine andere Größe skaliert habe und eine Wiederherstellung aus einer Sicherung vorgenommen wird, die vor der Skalierung erstellt wurde?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>RDB-Persistenz
* [Kann ich die Sicherungshäufigkeit für RDB-Persistenz ändern, nachdem ich den Cache erstellt habe?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Warum verstreichen mehr als 60 Minuten zwischen den RDB-Sicherungen, wenn ich eine Sicherungshäufigkeit von 60 Minuten festgelegt habe?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Was geschieht mit den alten RDB-Sicherungen, wenn eine neue Sicherung durchgeführt wird?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF-Persistenz
* [Wann sollte ich ein zweites Speicherkonto verwenden?](#when-should-i-use-a-second-storage-account)
* [Hat die AOF-Persistenz Auswirkungen auf Durchsatz, Wartezeiten oder Leistung meines Caches?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Wie kann ich das zweite Speicherkonto entfernen?](#how-can-i-remove-the-second-storage-account)
* [Was ist das Neuschreiben, und wie wirkt es sich auf meinen Cache aus?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [Was kann ich bei der Skalierung eines Caches mit aktivierter AOF-Persistenz erwarten?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Wie werden meine AOF-Daten im Speicher organisiert?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Kann ich die Persistenz für einen bereits erstellten Cache aktivieren?
Ja, die Redis-Persistenz kann sowohl bei der Erstellung des Caches als auch für vorhandene Premium-Caches konfiguriert werden.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Kann ich AOF- und RDB-Persistenz gleichzeitig aktivieren?

Nein, Sie können nur RDB oder AOF aktivieren, aber nicht beide Optionen gleichzeitig.

### <a name="which-persistence-model-should-i-choose"></a>Welches Persistenzmodell sollte ich auswählen?

AOF-Persistenz speichert jeden Schreibvorgang in einem Protokoll. Dies hat im Vergleich mit der RDB-Persistenz, bei der Sicherungen basierend auf den konfigurierten Sicherungsintervall gespeichert werden, Auswirkungen auf den Durchsatz und minimale Auswirkung auf die Leistung. Wählen Sie die AOF-Persistenz aus, wenn das primäre Ziel die Minimierung von Datenverlusten ist und die Verringerung des Durchsatzes für Ihren Cache kein Problem darstellt. Wählen Sie die RDB-Persistenz aus, wenn Sie einen optimalen Durchsatz für Ihren Cache wünschen, aber trotzdem einen Mechanismus zur Datenwiederherstellen benötigen.

* Erfahren Sie mehr über die [Vorteile](https://redis.io/topics/persistence#rdb-advantages) und [Nachteile](https://redis.io/topics/persistence#rdb-disadvantages) der RDB-Persistenz.
* Erfahren Sie mehr über die [Vorteile](https://redis.io/topics/persistence#aof-advantages) und [Nachteile](https://redis.io/topics/persistence#aof-disadvantages) der AOF-Persistenz.

Weitere Informationen zur Leistung bei Verwendung der AOF Persistenz finden Sie unter [Hat die AOF-Persistenz Auswirkungen auf Durchsatz, Wartezeiten oder Leistung meines Caches?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache).

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Was geschieht, wenn ich auf eine andere Größe skaliert habe und eine Wiederherstellung aus einer Sicherung vorgenommen wird, die vor der Skalierung erstellt wurde?

Für RDB- und AOF-Persistenz gilt Folgendes:

* Eine Skalierung auf eine größere Größe hat keinerlei Auswirkungen.
* Wenn Sie auf eine kleinere Größe skaliert haben und eine benutzerdefinierte Einstellung für die [Datenbanken](cache-configure.md#databases) verwenden, die größer ist als der [Grenzwert für Datenbanken](cache-configure.md#databases) bei der neuen Größe, werden die Daten in diesen Datenbanken nicht wiederhergestellt. Weitere Informationen finden Sie unter [Hat die Skalierung Auswirkungen auf meine benutzerdefinierte Einstellung für Datenbanken?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Wenn Sie auf eine kleinere Größe skaliert haben und dort nicht genug Platz für alle Daten aus der letzten Sicherung ist, werden beim Wiederherstellungsvorgang Schlüssel entfernt. Diese Entfernung wird in der Regel mithilfe der Entfernungsrichtlinie [allkeys-lru](https://redis.io/topics/lru-cache) vorgenommen.

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Kann ich die Sicherungshäufigkeit für RDB-Persistenz ändern, nachdem ich den Cache erstellt habe?
Ja, Sie können die Sicherungshäufigkeit für die RDB-Persistenz auf dem Blatt **Redis-Datenpersistenz** ändern. Anweisungen dazu finden Sie unter „Konfigurieren von Redis-Persistenz“.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Warum verstreichen mehr als 60 Minuten zwischen den RDB-Sicherungen, wenn ich eine Sicherungshäufigkeit von 60 Minuten festgelegt habe?
Das Intervall für die Sicherungshäufigkeit bei der RDB-Persistenz beginnt erst, nachdem der vorherige Sicherungsvorgang erfolgreich abgeschlossen wurde. Wenn für die Sicherungshäufigkeit 60 Minuten festgelegt sind und der Sicherungsvorgang nach 15 Minuten erfolgreich beendet wird, wird der nächste Sicherungsvorgang 75 Minuten nach der Startzeit des vorherigen Sicherungsvorgangs gestartet.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Was geschieht mit den alten RDB-Sicherungen, wenn eine neue Sicherung durchgeführt wird?
Alle Sicherungen, mit Ausnahme der jeweils letzten Sicherung, werden bei der RDB-Persistenz automatisch gelöscht. Dieser Löschvorgang wird möglicherweise nicht sofort durchgeführt, ältere Sicherungen werden jedoch nicht dauerhaft gespeichert.


### <a name="when-should-i-use-a-second-storage-account"></a>Wann sollte ich ein zweites Speicherkonto verwenden?

Sie sollten bei der AOF-Persistenz ein zweites Speicherkonto verwenden, wenn Sie glauben, dass Sie mehr als die erwarteten Mengenvorgänge im Cache haben.  Durch das Einrichten des sekundären Speicherkontos wird sichergestellt, dass Ihr Cache nicht die Grenzwerte für die Speicherbandbreite erreicht.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>Hat die AOF-Persistenz Auswirkungen auf Durchsatz, Wartezeiten oder Leistung meines Caches?

Die AOF-Persistenz wirkt sich auf den Durchsatz mit ca. 15–20 % aus, wenn der Cache unterhalb der maximalen Auslastung (CPU- und Serverauslastung unter 90 %) verwendet wird. Es sollten keine Latenzprobleme auftreten, wenn der Cache innerhalb dieser Grenzwerte liegt. Der Cache erreicht diese Grenzwerte allerdings mit aktivierter AOF-Persistenz früher.

### <a name="how-can-i-remove-the-second-storage-account"></a>Wie kann ich das zweite Speicherkonto entfernen?

Sie können das sekundäre Speicherkonto für die AOF Persistenz entfernen, indem Sie für das zweite Speicherkonto denselben Wert wie für das erste Speicherkonto festlegen. Anweisungen dazu finden Sie unter [Konfigurieren der AOF-Persistenz](#configure-aof-persistence).

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Was ist das Neuschreiben, und wie wirkt es sich auf meinen Cache aus?

Wenn die AOF-Datei groß genug ist, wird automatisch ein Neuschreibevorgang in die Warteschlange des Caches gestellt. Beim Neuschreiben wird die Größe der AOF-Datei um den minimalen Satz von Vorgängen geändert, die erforderlich sind, um das aktuelle DataSet zu erstellen. Während des Neuschreibens werden die Leistungsgrenzwerte früher erreicht – dies gilt insbesondere bei sehr großen DataSets. Neuschreibevorgänge treten seltener auf, wenn die AOF-Datei größer wird. Die erforderliche Dauer steigt allerdings auch erheblich an.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>Was kann ich bei der Skalierung eines Caches mit aktivierter AOF-Persistenz erwarten?

Wenn die AOF-Datei zum Zeitpunkt der Skalierung sehr groß ist, dauert der Skalierungsvorgang deutlich länger als erwartet, da die Datei nach Abschluss der Skalierung erneut geladen wird.

Weitere Informationen zur Skalierung finden Sie unter [Was geschieht, wenn ich auf eine andere Größe skaliert habe und eine Wiederherstellung aus einer Sicherung vorgenommen wird, die vor der Skalierung erstellt wurde?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation).

### <a name="how-is-my-aof-data-organized-in-storage"></a>Wie werden meine AOF-Daten im Speicher organisiert?

Daten, die in AOF-Dateien gespeichert sind, werden in mehrere Seitenblobs pro Knoten aufgeteilt, um die Leistung beim Speichern der Daten in den Speicher zu erhöhen. Die folgende Tabelle zeigt die Anzahl der Seitenblobs für die einzelnen Tarife:

| Premium-Tarif | Blobs (in englischer Sprache) |
|--------------|-------|
| P1           | 4 pro Shard    |
| P2           | 8 pro Shard    |
| P3           | 16 pro Shard   |
| P4           | 20 pro Shard   |

Nach dem Aktivieren des Clusterings verfügt jeder Shard im Cache über einen eigenen Satz von Seitenblobs, wie in der Tabelle oben ersichtlich. Ein P2-Cache mit drei Shards verteilt seine AOF-Datei z.B. auf 24 Seitenblobs (8 Blobs pro Shard bei 3 Shards).

Nach dem Neuschreiben sind zwei Sätze von AOF-Dateien im Speicher vorhanden. Die Neuschreibvorgänge werden im Hintergrund ausgeführt. Dabei wird der erste Satz von Dateien angefügt, während Mengenvorgänge, die während des Neuschreibens an den Cache gesendet werden, an den zweiten Satz angefügt werden. Während des Neuschreibens wird eine Sicherung temporär gespeichert, falls während des Vorgangs ein Ausfall auftritt. Diese wird aber sofort gelöscht, nachdem das Neuschreiben abgeschlossen wurde.


## <a name="next-steps"></a>Nächste Schritte
Informationen zur Verwendung weiterer Funktionen des Premium-Caches finden Sie in den folgenden Artikeln.

* [Einführung in den Premium-Tarif von Azure Cache for Redis](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
