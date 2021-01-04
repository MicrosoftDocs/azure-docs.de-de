---
title: Konfigurieren von Datenpersistenz – Azure Cache for Redis Premium
description: Erfahren Sie, wie Sie die Datenpersistenz für Ihren Premium Azure Cache for Redis mit Premium-Tarif konfigurieren und verwalten.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: 8ae76ca27c8c6f8fed5692b9a2376fff53a52bb6
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92536571"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>Konfigurieren von Datenpersistenz für Azure Cache for Redis vom Typ „Premium“
In diesem Artikel erfahren Sie, wie Sie über das Azure-Portal Persistenz in einer Azure Cache for Redis-Instanz konfigurieren. Für Azure Cache for Redis stehen verschiedene Cacheangebote bereit, die Flexibilität bei der Auswahl von Cachegröße und -features bieten. Dazu zählen auch Features des Premium-Tarifs wie die Unterstützung für Clustering, Persistenz und virtuelle Netzwerke. 

## <a name="what-is-data-persistence"></a>Was ist Datenpersistenz?
[Redis-Persistenz](https://redis.io/topics/persistence) ermöglicht die dauerhafte Speicherung von Daten in Redis. Sie können zudem Momentaufnahmen erstellen und die Daten sichern, die Sie dann im Fall eines Hardwarefehlers laden können. Dies ist ein großer Vorteil gegenüber dem Basic- oder Standard-Tarif, bei denen alle Daten im Arbeitsspeicher gespeichert sind, sodass bei einem Fehler, bei dem Cacheknoten ausfallen, möglicherweise Daten verloren gehen. 

Azure Cache for Redis bietet Redis-Persistenz über die folgenden Modelle:

* RDB-Persistenz: Wenn RDB-Persistenz (Redis-Datenbank) konfiguriert ist, speichert Azure Cache for Redisbasierend auf einer wählbaren Sicherungshäufigkeit eine Momentaufnahme des Azure Cache for Redis in einem binären Redis-Format dauerhaft auf dem Datenträger. Bei einem schwerwiegenden Fehler, bei dem der primäre sowie der Replikatcache deaktiviert werden, wird der Cache mithilfe der neuesten Momentaufnahme wiederhergestellt. Erfahren Sie mehr über die [Vorteile](https://redis.io/topics/persistence#rdb-advantages) und [Nachteile](https://redis.io/topics/persistence#rdb-disadvantages) der RDB-Persistenz.
* **AOF-Persistenz:** Wenn die AOF-Persistenz (Append only file, nur Datei anhängen) konfiguriert ist, speichert Azure Cache for Redis jeden Schreibvorgang in einem Protokoll, das mindestens einmal pro Sekunde in einem Azure Storage-Konto gespeichert wird. Bei einem schwerwiegenden Fehler, bei dem der primäre und der Replikatcache deaktiviert werden, wird der Cache mithilfe der gespeicherten Schreibvorgänge wiederhergestellt. Erfahren Sie mehr über die [Vorteile](https://redis.io/topics/persistence#aof-advantages) und [Nachteile](https://redis.io/topics/persistence#aof-disadvantages) der AOF-Persistenz.

Durch die Persistenz werden Redis-Daten in ein Azure Storage-Konto geschrieben, das sich in Ihrem Besitz befindet und von Ihnen verwaltet wird. Dies kann bei der Cacheerstellung auf dem Blatt **Neuer Azure Cache for Redis** sowie für vorhandene Premium-Caches im **Ressourcenmenü** konfiguriert werden.

> [!NOTE]
> 
> Dauerhaft gespeicherte Daten werden von Azure Storage automatisch verschlüsselt. Für die Verschlüsselung können Sie Ihre eigenen Schlüssel verwenden. Weitere Informationen finden Sie unter [Von Kunden verwaltete Schlüssel mit Azure Key Vault](../storage/common/storage-service-encryption.md).
> 
> 

1. Melden Sie sich zum Erstellen eines Premium-Caches beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie **Ressource erstellen** aus. Sie können Caches nicht nur über das Azure-Portal, sondern auch mithilfe von Resource Manager-Vorlagen, PowerShell oder der Azure-Befehlszeilenschnittstelle erstellen. Weitere Informationen zum Erstellen einer Azure Cache for Redis-Instanz finden Sie unter [Erstellen eines Caches](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Ressource erstellen.":::
   
2. Wählen Sie auf der Seite **Neu** die Option **Datenbanken** und dann **Azure Cache for Redis** aus.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Ressource erstellen.":::

3. Konfigurieren Sie auf der Seite **Neuer Redis Cache** die Einstellungen für den neuen Premium-Cache.
   
   | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-Name** | Geben Sie einen global eindeutigen Namen ein. | Der Cachename muss zwischen 1 und 63 Zeichen lang sein und darf nur Ziffern, Buchstaben und Bindestriche enthalten. Der Name muss mit einer Zahl oder einem Buchstaben beginnen und enden und darf keine aufeinanderfolgenden Bindestriche enthalten. Der *Hostname* Ihrer Cache-Instanz lautet *\<DNS name>.redis.cache.windows.net* . | 
   | **Abonnement** | Öffnen Sie die Dropdownliste, und wählen Sie Ihr Abonnement aus. | Das Abonnement, unter dem diese neue Azure Cache for Redis-Instanz erstellt wird. | 
   | **Ressourcengruppe** | Öffnen Sie die Dropdownliste, und wählen Sie eine Ressourcengruppe aus. Alternativ dazu wählen Sie **Neu erstellen** aus und geben einen Namen für eine neue Ressourcengruppe ein. | Der Name der Ressourcengruppe, in der Ihr Cache und weitere Ressourcen erstellt werden. Wenn Sie alle Ihre App-Ressourcen in einer Ressourcengruppe zusammenfassen, können Sie sie einfacher gemeinsam verwalten oder löschen. | 
   | **Location** | Öffnen Sie die Dropdownliste, und wählen Sie einen Standort aus. | Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in der Nähe anderer Dienste aus, die Ihren Cache verwenden. |
   | **Cachetyp** | Öffnen Sie die Dropdownliste, und wählen Sie einen Premium-Cache aus, um Premium-Features zu konfigurieren. Weitere Informationen finden Sie unter [Azure Cache for Redis – Preise](https://azure.microsoft.com/pricing/details/cache/). |  Der Tarif bestimmt Größe, Leistung und verfügbare Features für den Cache. Weitere Informationen finden Sie unter [What is Azure Cache for Redis](cache-overview.md) (Was ist Azure Cache for Redis?). |

4. Wählen Sie die Registerkarte **Netzwerk** aus, oder klicken Sie unten auf der Seite auf die Schaltfläche **Netzwerk** .

5. Wählen Sie auf der Registerkarte **Netzwerk** Ihre Konnektivitätsmethode aus. Bei Premium-Cache-Instanzen können Sie die Verbindung entweder öffentlich über öffentliche IP-Adressen oder Dienstendpunkte oder privat über einen privaten Endpunkt herstellen.

6. Wählen Sie unten auf der Seite die Registerkarte **Weiter: Erweitert** aus, oder klicken Sie unten auf der Seite auf die Schaltfläche **Weiter: Erweitert** .

7. Konfigurieren Sie auf der Registerkarte **Erweitert** für eine Premium-Cache-Instanz die Einstellungen für einen Nicht-TLS-Port sowie für Clustering und Datenpersistenz. Für die Datenpersistenz können Sie **RDB** oder **AOF** auswählen. 

8. Zum Aktivieren der RDB-Persistenz klicken Sie auf **RDB** und konfigurieren die Einstellungen. 
   
   | Einstellung      | Vorgeschlagener Wert  | Beschreibung |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Sicherungshäufigkeit** | Öffnen Sie die Dropdownliste, und wählen Sie ein Sicherungsintervall aus. Zur Auswahl stehen **15 Minuten** , **30 Minuten** , **60 Minuten** , **6 Stunden** , **12 Stunden** und **24 Stunden** . | Dieses Intervall wird ab dem Moment rückwärts gezählt, an dem der vorherige Sicherungsvorgang erfolgreich abgeschlossen wird. Wenn das Intervall abgelaufen ist, wird eine neue Sicherung gestartet. | 
   | **Speicherkonto** | Öffnen Sie die Dropdownliste, und wählen Sie Ihr Speicherkonto aus. | Sie müssen ein Speicherkonto auswählen, das aus derselben Region und demselben Abonnement wie der Cache stammt, und wir empfehlen ein **Storage Premium** -Konto, da dieses einen höheren Durchsatz aufweist.  | 
   | **Storage Key (Speicherschlüssel)** | Öffnen Sie die Dropdownliste, und wählen Sie **Primärer Schlüssel** oder **Sekundärer Schlüssel** aus. | Wenn der Speicherschlüssel für Ihr Persistenzkonto neu generiert wird, müssen Sie den gewünschten Schlüssel über die Dropdownliste **Speicherschlüssel** neu konfigurieren. | 

    Die erste Sicherung wird gestartet, sobald das Intervall für die Sicherungshäufigkeit abgelaufen ist.

9. Zum Aktivieren der AOF-Persistenz klicken Sie auf **AOF** und konfigurieren die Einstellungen. 
   
   | Einstellung      | Vorgeschlagener Wert  | Beschreibung |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Erstes Speicherkonto** | Öffnen Sie die Dropdownliste, und wählen Sie Ihr Speicherkonto aus. | Dieses Speicherkonto muss aus derselben Region und demselben Abonnement wie der Cache stammen. Es wird empfohlen, ein **Storage Premium** -Konto zu verwenden, da dieses einen höheren Durchsatz aufweist. | 
   | **Erster Speicherschlüssel** | Öffnen Sie die Dropdownliste, und wählen Sie **Primärer Schlüssel** oder **Sekundärer Schlüssel** aus. | Wenn der Speicherschlüssel für Ihr Persistenzkonto neu generiert wird, müssen Sie den gewünschten Schlüssel über die Dropdownliste **Speicherschlüssel** neu konfigurieren. | 
   | **Zweites Speicherkonto** | (Optional) Öffnen Sie die Dropdownliste, und wählen Sie Ihr sekundäres Speicherkonto aus. | Sie können optional ein zusätzliches Speicherkonto konfigurieren. Wenn ein zweites Storage-Konto konfiguriert wurde, werden Schreibvorgänge im Replikatcache in dieses zweite Speicherkonto geschrieben. | 
   | **Zweiter Speicherschlüssel** | (Optional) Öffnen Sie die Dropdownliste, und wählen Sie **Primärer Schlüssel** oder **Sekundärer Schlüssel** aus. | Wenn der Speicherschlüssel für Ihr Persistenzkonto neu generiert wird, müssen Sie den gewünschten Schlüssel über die Dropdownliste **Speicherschlüssel** neu konfigurieren. | 

    Wenn AOF-Persistenz aktiviert wurde, werden Schreibvorgänge in den Cache im angegebenen Speicherkonto gespeichert (oder in den Konten, wenn Sie ein zweites Speicherkonto konfiguriert haben). Bei einem schwerwiegenden Fehler, der zu einem Ausfall des primären und des Replikatcaches führt, wird das gespeicherte AOF-Protokoll für die Neuerstellung des Caches verwendet.

10. Wählen Sie die Registerkarte **Weiter: Tags** aus, oder klicken Sie unten auf der Seite auf die Schaltfläche **Weiter: Tags** (Weiter: Tags) aus.

11. Geben Sie optional auf der Registerkarte **Tags** den Namen und den Wert ein, wenn Sie die Ressource kategorisieren möchten. 

12. Klicken Sie auf **Überprüfen und erstellen** . Sie werden zur Registerkarte „Überprüfen und erstellen“ weitergeleitet, auf der Azure Ihre Konfiguration überprüft.

13. Wenn die grüne Meldung „Validierung erfolgreich“ angezeigt wird, wählen Sie **Erstellen** aus.

Es dauert eine Weile, bis der Cache erstellt wird. Sie können den Fortschritt auf der Seite **Übersicht** von Azure Cache for Redis überwachen. Wenn **Wird ausgeführt** als **Status** angezeigt wird, ist der Cache einsatzbereit. 

## <a name="persistence-faq"></a>Persistenz – häufig gestellte Fragen
Die folgende Liste enthält Antworten auf häufig gestellte Fragen zur Persistenz von Azure Cache for Redis-Instanzen.

* [Kann ich die Persistenz für einen bereits erstellten Cache aktivieren?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Kann ich AOF- und RDB-Persistenz gleichzeitig aktivieren?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Welches Persistenzmodell sollte ich auswählen?](#which-persistence-model-should-i-choose)
* [Was geschieht, wenn ich auf eine andere Größe skaliert habe und eine Wiederherstellung aus einer Sicherung vorgenommen wird, die vor der Skalierung erstellt wurde?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)
* [Kann ich dasselbe Speicherkonto für Persistenz in zwei verschiedenen Caches verwenden?](#can-i-use-the-same-storage-account-for-persistence-across-two-different-caches)


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

### <a name="can-i-use-the-same-storage-account-for-persistence-across-two-different-caches"></a>Kann ich dasselbe Speicherkonto für Persistenz in zwei verschiedenen Caches verwenden?
Ja, Sie können dasselbe Speicherkonto für Persistenz in zwei Caches verwenden.

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Kann ich die Sicherungshäufigkeit für RDB-Persistenz ändern, nachdem ich den Cache erstellt habe?
Ja. Sie können die Sicherungshäufigkeit für die RDB-Persistenz auf dem Blatt **Datenpersistenz** ändern. Anweisungen dazu finden Sie unter „Konfigurieren von Redis-Persistenz“.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Warum verstreichen mehr als 60 Minuten zwischen den RDB-Sicherungen, wenn ich eine Sicherungshäufigkeit von 60 Minuten festgelegt habe?
Das Intervall für die Sicherungshäufigkeit bei der RDB-Persistenz beginnt erst, nachdem der vorherige Sicherungsvorgang erfolgreich abgeschlossen wurde. Wenn für die Sicherungshäufigkeit 60 Minuten festgelegt sind und der Sicherungsvorgang nach 15 Minuten erfolgreich beendet wird, wird der nächste Sicherungsvorgang 75 Minuten nach der Startzeit des vorherigen Sicherungsvorgangs gestartet.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Was geschieht mit den alten RDB-Sicherungen, wenn eine neue Sicherung durchgeführt wird?
Alle Sicherungen, mit Ausnahme der jeweils letzten Sicherung, werden bei der RDB-Persistenz automatisch gelöscht. Dieser Löschvorgang wird möglicherweise nicht sofort durchgeführt, ältere Sicherungen werden jedoch nicht dauerhaft gespeichert.


### <a name="when-should-i-use-a-second-storage-account"></a>Wann sollte ich ein zweites Speicherkonto verwenden?

Sie sollten bei der AOF-Persistenz ein zweites Speicherkonto verwenden, wenn Sie glauben, dass Sie mehr als die erwarteten Mengenvorgänge im Cache haben.  Durch das Einrichten des sekundären Speicherkontos wird sichergestellt, dass Ihr Cache nicht die Grenzwerte für die Speicherbandbreite erreicht.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>Hat die AOF-Persistenz Auswirkungen auf Durchsatz, Wartezeiten oder Leistung meines Caches?

Die AOF-Persistenz wirkt sich auf den Durchsatz mit ca. 15–20 % aus, wenn der Cache unterhalb der maximalen Auslastung (CPU- und Serverauslastung unter 90 %) verwendet wird. Es sollten keine Latenzprobleme auftreten, wenn der Cache innerhalb dieser Grenzwerte liegt. Der Cache erreicht diese Grenzwerte allerdings mit aktivierter AOF-Persistenz früher.

### <a name="how-can-i-remove-the-second-storage-account"></a>Wie kann ich das zweite Speicherkonto entfernen?

Sie können das sekundäre Speicherkonto für die AOF Persistenz entfernen, indem Sie für das zweite Speicherkonto denselben Wert wie für das erste Speicherkonto festlegen. Bei vorhandenen Caches erfolgt der Zugriff auf das Blatt **Datenpersistenz** über das **Ressourcenmenü** für Ihren Cache. Zum Deaktivieren der AOF-Persistenz klicken Sie auf **Deaktiviert** .

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Was ist das Neuschreiben, und wie wirkt es sich auf meinen Cache aus?

Wenn die AOF-Datei groß genug ist, wird automatisch ein Neuschreibevorgang in die Warteschlange des Caches gestellt. Beim Neuschreiben wird die Größe der AOF-Datei um den minimalen Satz von Vorgängen geändert, die erforderlich sind, um das aktuelle DataSet zu erstellen. Während des Neuschreibens werden die Leistungsgrenzwerte früher erreicht – dies gilt insbesondere bei sehr großen DataSets. Neuschreibevorgänge treten seltener auf, wenn die AOF-Datei größer wird. Die erforderliche Dauer steigt allerdings auch erheblich an.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>Was kann ich bei der Skalierung eines Caches mit aktivierter AOF-Persistenz erwarten?

Wenn die AOF-Datei zum Zeitpunkt der Skalierung sehr groß ist, dauert der Skalierungsvorgang deutlich länger als erwartet, da die Datei nach Abschluss der Skalierung erneut geladen wird.

Weitere Informationen zur Skalierung finden Sie unter [Was geschieht, wenn ich auf eine andere Größe skaliert habe und eine Wiederherstellung aus einer Sicherung vorgenommen wird, die vor der Skalierung erstellt wurde?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation).

### <a name="how-is-my-aof-data-organized-in-storage"></a>Wie werden meine AOF-Daten im Speicher organisiert?

Daten, die in AOF-Dateien gespeichert sind, werden in mehrere Seitenblobs pro Knoten aufgeteilt, um die Leistung beim Speichern der Daten in den Speicher zu erhöhen. Die folgende Tabelle zeigt die Anzahl der Seitenblobs für die einzelnen Tarife:

| Premium-Tarif | BLOBs |
|--------------|-------|
| P1           | 4 pro Shard    |
| P2           | 8 pro Shard    |
| P3           | 16 pro Shard   |
| P4           | 20 pro Shard   |

Nach dem Aktivieren des Clusterings verfügt jeder Shard im Cache über einen eigenen Satz von Seitenblobs, wie in der Tabelle oben ersichtlich. Ein P2-Cache mit drei Shards verteilt seine AOF-Datei z.B. auf 24 Seitenblobs (8 Blobs pro Shard bei 3 Shards).

Nach dem Neuschreiben sind zwei Sätze von AOF-Dateien im Speicher vorhanden. Die Neuschreibvorgänge werden im Hintergrund ausgeführt. Dabei wird der erste Satz von Dateien angefügt, während Mengenvorgänge, die während des Neuschreibens an den Cache gesendet werden, an den zweiten Satz angefügt werden. Während des Neuschreibens wird eine Sicherung temporär gespeichert, falls während des Vorgangs ein Ausfall auftritt. Diese wird aber sofort gelöscht, nachdem das Neuschreiben abgeschlossen wurde.


## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über Azure Cache for Redis-Features.

* [Premium-Dienstebenen für Azure Cache for Redis](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png