---
title: Überwachen von Azure Cache for Redis
description: Erfahren Sie, wie Sie die Stabilität und Integrität Ihrer Azure Cache for Redis-Instanzen überwachen.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.openlocfilehash: 3d19d8f1b6a44f32e92f82e861471ca9b5c8fa41
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327337"
---
# <a name="how-to-monitor-azure-cache-for-redis"></a>Überwachen von Azure Cache for Redis

Azure Cache for Redis bietet über [Azure Monitor](../azure-monitor/index.yml) verschiedene Optionen zum Überwachen Ihrer Cache-Instanzen. Sie können Metriken anzeigen, Metrikdiagramme an das Startmenü anheften, Daten- und Zeitbereiche für Überwachungsdiagramme anpassen, Metriken aus Diagrammen hinzufügen und entfernen sowie Warnungen festlegen, die ausgelöst werden, wenn bestimmte Bedingungen erfüllt sind. Diese Tools ermöglichen es Ihnen, die Integrität Ihrer Azure Cache for Redis-Instanzen zu überwachen, und unterstützen Sie beim Verwalten Ihrer Cachinganwendungen.

Metriken für Azure Cache for Redis-Instanzen werden über den Redis-Befehl [INFO](https://redis.io/commands/info) ca. zweimal pro Minute gesammelt und automatisch 30 Tage lang gespeichert, sodass sie in den Metrikdiagrammen angezeigt und durch Warnungsregeln ausgewertet werden können. Informationen zum Konfigurieren einer anderen Aufbewahrungsrichtlinie finden Sie unter [Exportieren von Cachemetriken](#export-cache-metrics). Weitere Informationen zu den verschiedenen für die Cachemetriken verwendeten INFO-Werte finden Sie unter [Verfügbare Metriken und Berichtsintervalle](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Zum Anzeigen von Cachemetriken klicken Sie im [Azure-Portal](https://portal.azure.com) auf [Durchsuchen](cache-configure.md#configure-azure-cache-for-redis-settings), um zu Ihrer Cache-Instanz zu navigieren.  Azure Cache for Redis bietet auf den Blättern **Übersicht** und **Redis-Metriken** einige integrierte Diagramme. Sie können jedes Diagramm benutzerdefiniert anpassen, indem Sie Metriken hinzufügen oder entfernen oder das Berichtsintervall ändern.

![Es werden sechs Graphen angezeigt. Einer von Ihnen ist „Cachetreffer und Cachefehler in der letzten Stunde“.](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Anzeigen von vorkonfigurierten Metrikdiagrammen

Auf dem Blatt **Übersicht** sind die folgenden vorkonfigurierten Überwachungsdiagramme verfügbar.

* [Überwachungsdiagramme](#monitoring-charts)
* [Nutzungsdiagramme](#usage-charts)

### <a name="monitoring-charts"></a>Überwachungsdiagramme

Im Abschnitt **Überwachung** des Blatts **Übersicht** finden Sie die Diagramme **Treffer und Fehler**, **Abrufe und Sets**, **Verbindungen** sowie **Befehle gesamt**.

![Überwachungsdiagramme](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Nutzungsdiagramme

Der Abschnitt **Nutzung** des Blatts **Übersicht** umfasst die Diagramme **Redis-Serverlast**, **Speicherauslastung**, **Netzwerkbandbreite** und **CPU-Auslastung** und zeigt auch den **Tarif** für die Cache-Instanz an.

![Nutzungsdiagramme](./media/cache-how-to-monitor/redis-cache-usage-part.png)

Der Bereich **Tarif** zeigt den Tarif für den Cache an und kann zum [Skalieren](cache-how-to-scale.md) des Caches in einen anderen Tarif verwendet werden.

## <a name="view-metrics-with-azure-monitor"></a>Anzeigen von Metriken mit Azure Monitor

Um Redis-Metriken anzuzeigen und benutzerdefinierte Diagramme mit Azure Monitor zu erstellen, klicken Sie im **Ressourcenmenü** auf **Metriken**, und passen Sie das Diagramm mit den gewünschten Metriken, dem Berichtsintervall, dem Diagrammtyp usw. an.

![Im linken Navigationsbereich von contoso55 ist „Metriken“ eine Option unter „Überwachung“, die hervorgehoben ist. Unter „Metriken“ gibt es eine Liste von Metriken. Cachetreffer und Cachefehler sind ausgewählt.](./media/cache-how-to-monitor/redis-cache-monitor.png)

Weitere Informationen zum Verwenden von Metriken mit Azure Monitor finden Sie unter [Übersicht über Metriken in Microsoft Azure](../azure-monitor/platform/data-platform.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Exportieren von Cachemetriken

Cachemetriken werden in Azure Monitor standardmäßig [30 Tage lang gespeichert](../azure-monitor/platform/data-platform-metrics.md) und anschließend gelöscht. Um Cachemetriken länger als 30 Tage beizubehalten, können Sie [ein Speicherkonto festlegen](../azure-monitor/platform/resource-logs.md#send-to-azure-storage) und eine Richtlinie **Aufbewahrung (Tage)** für die Cachemetriken angeben. 

So konfigurieren Sie ein Speicherkonto für die Cachemetriken

1. Wählen Sie auf der Seite **Azure Cache for Redis** unter der Überschrift **Überwachung** die Option **Diagnose** aus.
2. Wählen Sie **+Diagnoseeinstellung hinzufügen**  aus.
3. Benennen Sie die Einstellungen.
4. Aktivieren Sie **In einem Speicherkonto archivieren**. Ihnen werden normale Datenraten für Speicherung und Transaktionen in Rechnung gestellt, wenn Sie Diagnoseinformationen an ein Speicherkonto senden.
4. Wählen Sie **Konfigurieren** aus, um das Speicherkonto auszuwählen, in dem die Cachemetriken gespeichert werden sollen.
5. Aktivieren Sie unter der Tabellenüberschrift **Metrik** das Kontrollkästchen neben den einzelnen Elementen, die Sie speichern möchten, z. B. **AllMetrics**. Geben Sie eine Richtlinie **Aufbewahrung (Tage)** an. Die maximale Anzahl von Tagen, die Sie für die Aufbewahrung angeben können, beträgt **365 Tage**. Wenn Sie die Metrikdaten aber unbegrenzt aufbewahren möchten, legen Sie die **Aufbewahrung (Tage)** auf **0** fest.
6. Klicken Sie auf **Speichern**.


![Redis-Diagnose](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Neben dem Archivieren der Cachemetriken im Speicher können Sie sie auch [an einen Event Hub streamen oder an Azure Monitor-Protokolle senden](../azure-monitor/platform/rest-api-walkthrough.md#retrieve-metric-values).
>

Um auf die Metriken zuzugreifen, können Sie sie im Azure-Portal anzeigen (wie weiter oben in diesem Artikel beschrieben) oder dazu die [Azure Monitor-REST-API für Metriken](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) verwenden.

> [!NOTE]
> Wenn Sie das Speicherkonto ändern, sind die Daten im zuvor konfigurierten Speicherkonto weiterhin zum Download verfügbar, werden im Azure-Portal jedoch nicht angezeigt.  
> 

## <a name="available-metrics-and-reporting-intervals"></a>Verfügbare Metriken und Berichtsintervalle

Cachemetriken werden in verschiedenen Berichtsintervallen gemeldet, z.B. **Letzte Stunde**, **Heute**, **Letzte Woche** und **Benutzerdefiniert**. Das Blatt **Metrik** für jedes Metrikdiagramm zeigt die Durchschnitts-, Minimal- und Maximalwerte für jede Metrik im Diagramm an. Einige Metriken zeigen einen Gesamtwert für das Berichtsintervall an. 

Jede Metrik umfasst zwei Versionen. Eine Metrik misst die Leistung für den gesamten Cache. Eine zweite Version der Metrik, deren Name `(Shard 0-9)` enthält, misst für Caches, die [Clustering](cache-how-to-premium-clustering.md) verwenden, die Leistung für einen einzelnen Shard in einem Cache. Wenn z. B. ein Cache vier Shards enthält, ist `Cache Hits` der Gesamtanzahl der Treffer für den gesamten Cache, und `Cache Hits (Shard 3)` gibt lediglich die Treffer für dieses Shard des Caches an.

> [!NOTE]
> Selbst wenn keine der mit dem Cache verbundenen Clientanwendungen aktiv ist, wird möglicherweise Cacheaktivität angezeigt, wie z. B. verbundene Clients, Arbeitsspeicherauslastung und Vorgänge, die gerade ausgeführt werden. Diese Aktivität ist beim Betrieb einer Azure Cache for Redis-Instanz normal.
> 
> 

| Metrik | BESCHREIBUNG |
| --- | --- |
| Cachetreffer |Die Anzahl der erfolgreichen Schlüsselsuchen während des angegebenen Berichtsintervalls. Diese Zahl ist `keyspace_hits` aus dem Redis-Befehl [INFO](https://redis.io/commands/info) zugeordnet. |
| Cachelatenz (Vorschau) | Die auf Basis der Latenz des Caches zwischen Knoten berechnete Cachelatenz. Diese Metrik wird in Mikrosekunden gemessen und verfügt über drei Dimensionen: ,`Avg`, `Min` und `Max`, die die durchschnittliche, minimale und maximale Latenz des Caches bzw. während des angegebenen Berichtsintervalls darstellen. |
| Cachefehler |Die Anzahl der fehlerhaften Schlüsselsuchen während des angegebenen Berichtsintervalls. Diese Zahl ist `keyspace_misses` aus dem Redis-Befehl INFO zugeordnet. Cachefehler bedeuten nicht unbedingt, dass ein Problem mit dem Cache vorhanden ist. Bei Verwendung des cachefremden Programmierschemas sucht eine Anwendung zuerst im Cache nach einem Element. Wenn das gesuchte Element nicht vorhanden ist (Cachefehler), wird es aus der Datenbank abgerufen und dem Cache für das nächste Mal hinzugefügt. Cachefehler sind ein normales Verhalten für das cachefremde Programmierschema. Wenn die Anzahl der Cachefehler höher ist als erwartet, untersuchen Sie die Anwendungslogik, die den Cache auffüllt und aus dem Cache liest. Wenn Elemente aufgrund von ungenügendem Arbeitsspeicher aus dem Cache entfernt werden, können Cachefehler auftreten. Zur Überwachung der Arbeitsspeicherauslastung eignen sich jedoch die Metriken `Used Memory` oder `Evicted Keys` besser. |
| Cache-Lesevorgänge |Die Menge an Daten in Megabyte pro Sekunde (MB/s), die während des angegebenen Berichtsintervalls im Cache gelesen wurden. Dieser Wert wird von den Netzwerkschnittstellenkarten für die virtuelle Maschine abgeleitet, die den Cache hostet, und ist nicht Redis-spezifisch. **Dieser Wert entspricht der von diesem Cache verwendeten Netzwerkbandbreite. Wenn Sie Warnungen für serverseitige Grenzwerte bei der Netzwerkbandbreite einrichten möchten, erstellen Sie sie mithilfe des `Cache Read`-Leistungsindikators. Die beobachteten Bandbreitengrenzwerte für verschiedene Cachetarife und -größen finden Sie in [dieser Tabelle](cache-planning-faq.md#azure-cache-for-redis-performance).** |
| Cache-Schreibvorgänge |Die Menge an Daten in Megabyte pro Sekunde (MB/s), die während des angegebenen Berichtsintervalls in den Cache geschrieben wurden. Dieser Wert wird von den Netzwerkschnittstellenkarten für die virtuelle Maschine abgeleitet, die den Cache hostet, und ist nicht Redis-spezifisch. Dieser Wert entspricht der Netzwerkbandbreite der vom Client an den Cache gesendeten Daten. |
| Verbundene Clients |Die Anzahl von Clientverbindungen mit dem Cache während des angegebenen Berichtsintervalls. Diese Zahl ist `connected_clients` aus dem Redis-Befehl INFO zugeordnet. Sobald der [Grenzwert für Verbindungen](cache-configure.md#default-redis-server-configuration) erreicht ist, treten bei weiteren Verbindungsversuchen mit dem Cache Fehler auf. Auch wenn keine Clientanwendungen aktiv sind, können aufgrund interner Prozesse und Verbindungen dennoch einige Instanzen verbundener Clients vorhanden sein. |
| CPU |Die CPU-Auslastung des Azure Cache for Redis-Servers in Prozent während des angegebenen Berichtsintervalls. Dieser Wert ist dem Betriebssystem-Leistungsindikator `\Processor(_Total)\% Processor Time` zugeordnet, |
| Errors | Bestimmte Fehler und Leistungsprobleme, mit denen der Cache während eines angegebenen Berichtsintervalls konfrontiert werden könnte. Diese Metrik hat acht Dimensionen, die verschiedene Fehlertypen darstellen, könnte jedoch zukünftig mit weiteren ergänzt werden. Derzeit werden folgende Fehlertypen dargestellt: <br/><ul><li>**Failover**: Wenn ein Failover für einen Cache erfolgt (untergeordneter Cache wird zum primären Cache heraufgestuft)</li><li>**Dataloss**: Wenn Datenverlust auf dem Cache auftritt</li><li>**UnresponsiveClients**: Wenn die Clients nicht schnell genug Daten vom Server lesen</li><li>**AOF**: Wenn ein Problem im Zusammenhang mit der AOF-Persistenz auftritt</li><li>**RDB**: Wenn ein Problem im Zusammenhang mit der RDB-Persistenz auftritt</li><li>**Import**: Wenn ein Problem im Zusammenhang mit „Import RDB“ auftritt</li><li>**Export**: Wenn ein Problem im Zusammenhang mit „Export RDB“ auftritt</li></ul> |
| Entfernte Schlüssel |Die Anzahl von Elementen, die aufgrund des `maxmemory` -Grenzwerts während des angegebenen Berichtsintervalls aus dem Cache entfernt wurden. Diese Zahl ist `evicted_keys` aus dem Redis-Befehl INFO zugeordnet. |
| Abgelaufene Schlüssel |Die Anzahl von Elementen, die während des angegebenen Berichtsintervalls im Cache abgelaufen sind. Dieser Wert ist `expired_keys` aus dem Redis-Befehl „INFO“ zugeordnet.|
| get-Vorgänge |Die Anzahl von get-Vorgängen im Cache während des angegebenen Berichtsintervalls. Dieser Wert ist die Summe der folgenden Werte aus dem Redis-INFO-Befehl "all": `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit` und `cmdstat_getrange` und entspricht der Summe aller Cachetreffer und Cachefehler während des angegebenen Berichtsintervalls. |
| Vorgänge pro Sekunde | Die Gesamtzahl aller Befehle, die während des angegebenen Berichtsintervalls vom Cacheserver pro Sekunde verarbeitet wurden.  Dieser Wert wird „instantaneous_ops_per_sec“ aus dem Redis-INFO-Befehl zugeordnet. |
| Redis-Serverlast |Der Prozentsatz der Zyklen, in denen der Redis-Server mit der Verarbeitung beschäftigt ist und nicht auf Nachrichten wartet. Wenn dieser Zähler den Wert 100 erreicht, bedeutet dies, dass der Redis-Server die Leistungsobergrenze erreicht hat und die CPU nicht schneller arbeiten kann. Wenn eine hohe Redis-Serverlast angezeigt wird, bedeutet dies auch Timeoutausnahmen auf dem Client. In diesem Fall sollten Sie erwägen, den Cache zentral hochzuskalieren oder Ihre Daten in mehrere Caches zu partitionieren. |
| Sets |Die Anzahl von set-Vorgängen im Cache während des angegebenen Berichtsintervalls. Dieser Wert ist die Summe der folgenden Werte aus dem Redis-INFO-Befehl "all": `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange` und `cmdstat_setnx`. |
| Schlüssel insgesamt  | Die maximale Anzahl der Schlüssel im Cache während des letzten Berichtszeitraums. Diese Zahl ist `keyspace` aus dem Redis-Befehl INFO zugeordnet. Aufgrund einer Einschränkung des zugrunde liegenden Metriksystems gibt „Schlüssel insgesamt“ für Caches mit aktiviertem Clustering die maximale Anzahl der Schlüssel des Shards zurück, der im Berichtsintervall die maximale Anzahl der Schlüssel aufwies.  |
| Vorgänge gesamt |Die Gesamtzahl aller Befehle, die während des angegebenen Berichtsintervalls vom Cacheserver verarbeitet wurden. Dieser Wert ist `total_commands_processed` aus dem Redis-Befehl „INFO“ zugeordnet. Wenn Azure Cache for Redis ausschließlich für pub/sub-Vorgänge verwendet wird, sind keine Metriken für `Cache Hits`, `Cache Misses`, `Gets` oder `Sets` vorhanden. Stattdessen sind `Total Operations`-Metriken vorhanden, die die Cachenutzung für pub/sub-Vorgänge widerspiegeln. |
| Verwendeter Arbeitsspeicher |Der Betrag von Cachearbeitsspeicher in MB, der während des angegebenen Berichtsintervalls für Schlüssel-Wert-Paare im Cache verwendet wird. Dieser Wert ist `used_memory` aus dem Redis-Befehl „INFO“ zugeordnet. Dieser Wert enthält keine Metadaten oder Fragmentierung. |
| Prozentsatz der Arbeitsspeicherverwendung | Der prozentuale Anteil des gesamten Arbeitsspeichers, der während des angegebenen Berichtsintervalls verwendet wird.  Dieser Wert verweist auf den Wert `used_memory` aus dem Redis-INFO-Befehl zum Berechnen des Prozentsatzes. |
| Verwendeter Arbeitsspeicher (RSS) |Die Menge an verwendetem Cachearbeitsspeicher in MB während des angegebenen Berichtsintervalls, einschließlich Fragmentierung und Metadaten. Dieser Wert ist `used_memory_rss` aus dem Redis-Befehl „INFO“ zugeordnet. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Alerts

Sie können die Konfiguration so durchführen, dass Warnungen basierend auf Metriken und Aktivitätsprotokollen empfangen werden. Mit Azure Monitor können Sie eine Warnung so konfigurieren, dass Folgendes erfolgt, wenn sie ausgelöst wird:

* Senden einer E-Mail-Benachrichtigung
* Aufrufen eines Webhooks
* Aufrufen einer Azure-Logik-App

Klicken Sie zum Konfigurieren von Warnungsregeln für den Cache im **Ressourcenmenü** auf **Warnungsregeln**.

![Überwachung](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Weitere Informationen zum Konfigurieren und Verwenden von Warnungen finden Sie unter [Übersicht über Warnungen](../azure-monitor/platform/alerts-classic-portal.md).

## <a name="activity-logs"></a>Aktivitätsprotokolle
Aktivitätsprotokolle geben Einblick in die Vorgänge, die für Ihre Azure Cache for Redis-Instanzen ausgeführt wurden. Sie wurden zuvor als „Überwachungsprotokolle“ oder „Betriebsprotokolle“ bezeichnet. Mit Aktivitätsprotokollen können Sie die Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (PUT, POST, DELETE) ermitteln, die für die Azure Cache for Redis-Instanzen durchgeführt wurden. 

> [!NOTE]
> Aktivitätsprotokolle umfassen keine Lesevorgänge (GET).
>

Klicken Sie zum Anzeigen von Aktivitätsprotokollen für Ihren Cache im **Ressourcenmenü** auf **Aktivitätsprotokolle**.

Weitere Informationen zu Aktivitätsprotokollen finden Sie unter [Übersicht über das Azure-Aktivitätsprotokoll](../azure-monitor/platform/platform-logs-overview.md).