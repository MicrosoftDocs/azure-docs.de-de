---
title: Häufig gestellte Fragen zur Planung für Azure Cache for Redis
description: In diesem Artikel erhalten Sie Antworten auf häufig gestellte Fragen, die Ihnen bei der Planung für Azure Cache for Redis helfen.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 398b567e173691068e6dd932e075d6598dcfe601
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537404"
---
# <a name="azure-cache-for-redis-planning-faqs"></a>Häufig gestellte Fragen zur Planung für Azure Cache for Redis

Dieser Artikel bietet Antworten auf häufig gestellte Fragen zur Planung für Azure Cache for Redis.

## <a name="common-questions-and-answers"></a>Häufig gestellte Fragen und deren Antworten
In diesem Abschnitt werden die folgenden häufig gestellten Fragen behandelt:

* [Azure Cache for Redis – Leistung](#azure-cache-for-redis-performance)
* [Ich welcher Region sollte ich meinen Cache platzieren?](#in-what-region-should-i-locate-my-cache)
* [Wo befinden sich meine zwischengespeicherten Daten?](#where-do-my-cached-data-reside)
* [Wie wird Azure Cache for Redis abgerechnet?](#how-am-i-billed-for-azure-cache-for-redis)
* [Kann ich Azure Cache for Redis mit der Azure Government Cloud, der Azure China 21Vianet-Cloud oder Microsoft Azure Deutschland verwenden?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany)

### <a name="azure-cache-for-redis-performance"></a>Azure Cache for Redis – Leistung
In der folgenden Tabelle sind die maximalen Bandbreitenwerte beim Testen verschiedener Standard- und Premium-Cachegrößen bei Verwendung von `redis-benchmark.exe` aus einem virtuellen IaaS-Computer mit dem Azure Cache for Redis-Endpunkt angegeben. Für TLS-Durchsatz wird der Redis-Vergleichstest mit „stunnel“ für das Herstellen der Verbindung mit dem Azure Cache for Redis-Endpunkt verwendet.

>[!NOTE] 
>Diese Werte werden nicht garantiert, und es gibt keine SLA für diese Werte; sie sollten jedoch typischerweise erreicht werden. Führen Sie Auslastungstests für Ihre Anwendung durch, um die geeignete Cachegröße für Ihre Anwendung zu ermitteln.
>Diese Zahlen können sich ändern, da wir in regelmäßigen Abständen neuere Ergebnisse veröffentlichen.
>

Aus dieser Tabelle können folgende Schlussfolgerungen gezogen werden:

* Der Durchsatz für Caches gleicher Größe ist im Premium-Tarif besser als im Standard-Tarif. Beispielsweise liegt bei einem Cache mit 6 GB der Durchsatz von P1 bei 180.000 Anforderungen pro Sekunde (RPS) im Vergleich zu 100.000 RPS bei C3.
* Mit dem Redis-Clustering steigt der Durchsatz linear, je mehr Shards (Knoten) Sie im Cluster verwenden. Wenn Sie beispielsweise einen P4-Cluster mit 10 Shards erstellen, beträgt der verfügbare Durchsatz 400.000 * 10 = 4 Millionen RPS.
* Der Durchsatz für größere Schlüsselgrößen ist im Premium-Tarif höher als im Standard-Tarif.

| Tarif | Size | CPU-Kerne | Verfügbare Bandbreite | 1 KB Wertgröße | 1 KB Wertgröße |
| --- | --- | --- | --- | --- | --- |
| **Standard-Cachegröße** | | |**Megabits pro Sekunde (MBit/s)/Megabyte pro Sekunde (MB/s)** |**Anforderungen pro Sekunde (RPS), kein SLL** |**Anforderungen pro Sekunde (RPS), SLL** |
| C0 | 250 MB | Shared | 100 / 12,5  |  15.000 |   7\.500 |
| C1 |   1 GB | 1      | 500 / 62,5  |  38.000 |  20.720 |
| C2 | 2,5 GB | 2      | 500 / 62,5  |  41.000 |  37.000 |
| C3 |   6 GB | 4      | 1\.000 / 125  | 100.000 |  90.000 |
| C4 |  13 GB | 2      | 500 / 62,5  |  60.000 |  55.000 |
| C5 |  26 GB | 4      | 1\.000/125 | 102.000 |  93.000 |
| C6 |  53 GB | 8      | 2\.000/250 | 126.000 | 120.000 |
| **Premium-Cachegröße** | |**CPU-Kerne pro Shard** | **Megabits pro Sekunde (MBit/s)/Megabyte pro Sekunde (MB/s)** |**Anforderungen pro Sekunde (RPS), kein SLL, pro Shard** |**Anforderungen pro Sekunde (RPS), SLL, pro Shard** |
| P1 |   6 GB |  2 | 1\.500/187,5 | 180.000 | 172.000 |
| P2 |  13 GB |  4 | 3\.000/375   | 350.000 | 341.000 |
| P3 |  26 GB |  4 | 3\.000/375   | 350.000 | 341.000 |
| P4 |  53 GB |  8 | 6\.000/750   | 400.000 | 373.000 |
| P5 | 120 GB | 20 | 6\.000/750   | 400.000 | 373.000 |

Anweisungen zum Einrichten von stunnel oder zum Herunterladen von Redis-Tools wie `redis-benchmark.exe` finden Sie unter [Wie führe ich Redis-Befehle aus?](cache-development-faq.md#how-can-i-run-redis-commands).

### <a name="in-what-region-should-i-locate-my-cache"></a>Ich welcher Region sollte ich meinen Cache platzieren?
Um eine optimale Leistung und die niedrigste Latenz zu erzielen, sollten Sie Ihren Azure Cache for Redis in derselben Region platzieren wie Ihre Cacheclientanwendung.

### <a name="where-do-my-cached-data-reside"></a>Wo befinden sich meine zwischengespeicherten Daten?
Azure Cache for Redis speichert Ihre Anwendungsdaten abhängig vom Tarif im RAM der VM oder der VMs, die Ihren Cache hosten. Ihre Daten bleiben strikt in der Azure-Region, die Sie standardmäßig ausgewählt haben. Es gibt zwei Fälle, in denen Ihre Daten eine Region verlassen könnten:
* Wenn Sie die Persistenz im Cache aktivieren, sichert Azure Cache for Redis Ihre Daten in einem Azure Storage-Konto, das Sie besitzen. Wenn sich das von Ihnen bereitgestellte Speicherkonto in einer anderen Region befindet, wird eine Kopie der Daten dort gespeichert.
* Wenn Sie Georeplikation einrichten und sich der sekundäre Cache in einer anderen Region befindet, was normalerweise der Fall ist, werden die Daten in dieser Region repliziert.

Sie müssen Azure Cache for Redis explizit so konfigurieren, um diese Features zu verwenden. Außerdem verfügen Sie über die komplette Kontrolle über die Region, in der sich das Speicherkonto oder der sekundäre Cache befindet.

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Wie wird Azure Cache for Redis abgerechnet?
Die Preise für Azure Cache for Redis finden Sie [hier](https://azure.microsoft.com/pricing/details/cache/). Auf der Seite mit der Preisübersicht sind die Kosten als stündlicher und monatlicher Satz ausgewiesen. Caches werden auf Minutenbasis ab dem Zeitpunkt der Erstellung des Caches bis zu dem Zeitpunkt abgerechnet, an dem ein Cache gelöscht wird. Es gibt keine Möglichkeit, die Abrechnung eines Caches zu beenden oder anzuhalten.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany"></a>Kann ich Azure Cache for Redis mit der Azure Government Cloud, der Azure China 21Vianet-Cloud oder Microsoft Azure Deutschland verwenden?
Ja, Azure Cache for Redis ist in der Azure Government Cloud, der Azure 21Vianet Cloud in China und Microsoft Azure Deutschland verfügbar. Die URLs für den Zugriff auf und die Verwaltung von Azure Cache for Redis unterscheiden sich in diesen Clouds von den URLs der Azure Public Cloud.

| Cloud   | DNS-Suffix für Redis            |
|---------|---------------------------------|
| Öffentlich  | *.redis.cache.windows.net       |
| US Gov  | *.redis.cache.usgovcloudapi.net |
| Deutschland | *.redis.cache.cloudapi.de       |
| China   | *.redis.cache.chinacloudapi.cn  |

Weitere zu berücksichtigende Aspekte bei der Verwendung von Azure Cache for Redis mit anderen Clouds werden unter den folgenden Links beschrieben.

- [Azure Government-Datenbanken – Azure Cache for Redis](../azure-government/compare-azure-government-global-azure.md)
- [Azure 21Vianet Cloud in China – Azure Cache for Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Deutschland](https://azure.microsoft.com/overview/clouds/germany/)

Informationen zur Nutzung von Azure Cache for Redis in Verbindung mit PowerShell in der Azure Government Cloud, in der Azure 21Vianet Cloud in China und in Microsoft Azure Deutschland finden Sie unter [Herstellen einer Verbindung mit anderen Clouds – Azure Cache for Redis PowerShell](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über weitere [häufig gestellte Fragen zu Azure Cache for Redis](cache-faq.md).