---
title: Migrieren zu Azure Cache for Redis
description: Erfahren Sie, wie Sie Ihren vorhandenen Cache zu Azure Cache for Redis migrieren.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: yegu
ms.openlocfilehash: 5de4e1b465cfc3ced59f8fe34a7f397324b4a225
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537625"
---
# <a name="migrate-to-azure-cache-for-redis"></a>Migrieren zu Azure Cache for Redis
In diesem Artikel werden verschiedene Vorgehensweisen zum Migrieren einer vorhandenen, lokal oder in einem anderen Clouddienst ausgeführten Redis Cache-Instanz zu Azure Cache for Redis beschrieben.

## <a name="migration-scenarios"></a>Migrationsszenarios
Open-Source-Redis kann in einer Vielzahl von Compute-Umgebungen ausgeführt werden. Häufige Beispiele sind:

- **Lokal** : Redis Cache-Instanzen, die in privaten Rechenzentren ausgeführt werden.
- **Cloudbasierte VMs** : Redis Cache-Instanzen, die auf Azure-VMs, AWS EC2 usw. ausgeführt werden.
- **Hostingdienste** : Verwaltete Redis-Dienste wie z. B. AWS ElastiCache.
- **Verschiedene Regionen** : Redis Cache-Instanzen, die sich in einer anderen Azure-Region befinden.

Wenn Sie über einen solchen Cache verfügen, können Sie diesen möglicherweise mit minimalen Unterbrechungen oder Ausfallzeiten zu Azure Cache for Redis verlagern.

## <a name="migration-options"></a>Migrationsoptionen

Es gibt verschiedene Möglichkeiten, zwischen verschiedenen Caches zu wechseln. Je nachdem, wo der Cache sich befindet und wie Ihre Anwendung damit interagiert, ist eine Methode vermutlich sinnvoller als die anderen. Im Folgenden werden einige der häufig verwendeten Migrationsstrategien erläutert.

   | Option       | Vorteile | Nachteile |
   | ------------ | ---------- | ------------- |
   | Erstellen eines neuen Caches | Die am einfachsten zu implementierende Methode. | Daten müssen im neuen Cache wieder aufgefüllt werden, was bei vielen Anwendungen möglicherweise nicht funktioniert. |
   | Exportieren und Importieren von Daten über eine RDB-Datei | Im Allgemeinen mit jedem Redis-Cache kompatibel. | Einige Daten könnten verloren gehen, wenn sie nach dem Generieren der RDB-Datei in den vorhandenen Cache geschrieben werden. | 
   | Duale Schreibvorgänge in zwei Caches | Kein Datenverlust und keine Ausfallzeiten Unterbrechungsfreie Vorgänge im vorhandenen Cache. Einfacheres Testen des neuen Caches. | Für einen längeren Zeitraum sind zwei Caches erforderlich. | 
   | Programmgesteuertes Migrieren von Daten | Vollständige Kontrolle über die Verlagerung der Daten. | Erfordert benutzerdefinierten Code. | 

### <a name="create-a-new-azure-cache-for-redis"></a>Erstellen einer neuen Azure Cache for Redis-Instanz

Dieser Ansatz ist technisch gesehen keine Migration. Wenn Datenverluste kein Problem sind, können Sie am einfachsten zu Azure Cache for Redis wechseln, indem Sie eine neue Cache-Instanz erstellen und Ihre Anwendung damit verbinden. Wenn Sie Redis beispielsweise als Suchcache für Datenbank-Datensätze verwenden, können Sie den Cache problemlos von Grund auf neu erstellen.

Hier finden Sie die allgemeinen Schritte zum Implementieren dieser Option:

1. Erstellen Sie eine neue Azure Cache for Redis-Instanz.

2. Aktualisieren Sie Ihre Anwendung, sodass die neue Instanz verwendet wird.

3. Löschen Sie die alte Redis-Instanz.

### <a name="export-data-to-an-rdb-file-and-import-it-into-azure-cache-for-redis"></a>Exportieren von Daten in eine RDB-Datei und Importieren der Datei in Azure Cache for Redis

Open-Source-Redis definiert einen Standardmechanismus, um eine Momentaufnahme des In-Memory-Datasets eines Caches zu erstellen und in einer Datei zu speichern. Diese als RDB bezeichnete Datei kann von einem anderen Redis-Cache gelesen werden. Der [Azure Cache for Redis-Premium-Tarif](cache-overview.md#service-tiers) unterstützt das Importieren von Daten in eine Cache-Instanz über RDB-Dateien. Sie können eine RDB-Datei verwenden, um Daten aus einem vorhandenen Cache nach Azure Cache for Redis zu übertragen.

> [!IMPORTANT]
> Das RDB-Dateiformat kann sich zwischen verschiedenen Redis-Versionen ändern und bietet möglicherweise keine Abwärtskompatibilität. Die Redis-Version des Caches, aus dem Sie die Daten exportieren, muss kleiner oder gleich der von Azure Cache for Redis bereitgestellten Version sein.
>

Hier finden Sie die allgemeinen Schritte zum Implementieren dieser Option:

1. Erstellen Sie eine neue Azure Cache for Redis-Instanz in dem Premium-Tarif, der die gleiche Größe aufweist wie der vorhandene Cache (oder größer ist).

2. Speichern Sie eine Momentaufnahme des vorhandenen Redis-Caches. Sie können [Redis für die regelmäßige Speicherung von Momentaufnahmen konfigurieren](https://redis.io/topics/persistence) oder den Prozess mithilfe der Befehle [SAVE](https://redis.io/commands/save) oder [BGSAVE](https://redis.io/commands/bgsave) manuell ausführen. Die RDB-Datei erhält standardmäßig den Namen „dump.rdb“ und wird in dem Pfad gespeichert, der in der Konfigurationsdatei *redis.conf* angegeben ist.

    > [!NOTE]
    > Wenn Sie Daten innerhalb von Azure Cache for Redis migrieren, lesen Sie [diese Anweisungen zum Exportieren einer RDB-Datei](cache-how-to-import-export-data.md), oder verwenden Sie stattdessen das [PowerShell-Cmdlet für den Export](/powershell/module/azurerm.rediscache/export-azurermrediscache?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.4.0).
    >

3. Kopieren Sie die RDB-Datei in ein Azure-Speicherkonto in der Region, in der sich Ihr neuer Cache befindet. Hierfür können Sie AzCopy verwenden.

4. Importieren Sie die RDB-Datei mit diesen [Importanweisungen](cache-how-to-import-export-data.md) oder dem [PowerShell-Cmdlet für den Import](/powershell/module/azurerm.rediscache/import-azurermrediscache?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.4.0) in den neuen Cache.

5. Aktualisieren Sie Ihre Anwendung, sodass die neue Cache-Instanz verwendet wird.

### <a name="write-to-two-redis-caches-simultaneously-during-migration-period"></a>Gleichzeitiges Schreiben in zwei Redis Cache-Instanzen während der Migrationsphase

Anstatt Daten direkt zwischen Caches zu verschieben, können Sie Ihre Anwendung verwenden, um Daten sowohl in einen vorhandenen als auch in einen neuen, von Ihnen eingerichteten Cache zu schreiben. Die Anwendung liest Daten anfangs weiterhin aus dem vorhandenen Cache. Wenn der neue Cache über alle notwendigen Daten verfügt, konfigurieren Sie Ihre Anwendung für den neuen Cache und nehmen den alten außer Betrieb. Ein Beispiel: Sie verwenden Redis als Sitzungsspeicher, und die Anwendungssitzungen sind sieben Tage lang gültig. Nachdem zwei Wochen lang in beide Caches geschrieben wurde, können Sie sicher sein, dass der neue Cache alle nicht abgelaufenen Sitzungsinformationen enthält. Ab diesem Zeitpunkt können Sie sich auf diesen Cache verlassen, ohne sich Gedanken um Datenverluste machen zu müssen.

Hier finden Sie die allgemeinen Schritte zum Implementieren dieser Option:

1. Erstellen Sie eine neue Azure Cache for Redis-Instanz in dem Premium-Tarif, der die gleiche Größe aufweist wie der vorhandene Cache (oder größer ist).

2. Ändern Sie den Anwendungscode, sodass die Anwendung sowohl in die neue als auch in die ursprüngliche Cache-Instanz schreibt.

3. Verwenden Sie zum Lesen der Daten weiterhin die ursprüngliche Instanz, bis die neue Instanz ausreichend mit Daten aufgefüllt ist.

4. Aktualisieren Sie den Anwendungscode, sodass die Anwendung zum Lesen und Schreiben von Daten ausschließlich die neue Instanz verwendet.

5. Löschen Sie die ursprüngliche Instanz.

### <a name="migrate-programmatically"></a>Programmgesteuertes Migrieren

Sie können einen benutzerdefinierten Migrationsprozess einrichten, indem Sie Daten programmgesteuert aus einem vorhandenen Cache lesen und in Azure Cache for Redis schreiben lassen. Dieses [Open-Source-Tool](https://github.com/deepakverma/redis-copy) kann zum Kopieren von Daten aus einer Azure Cache for Redis-Instanz in eine andere verwendet werden. Dieses Tool ist sehr nützlich, wenn Sie Daten zwischen Cache-Instanzen in verschiedenen Azure Cache-Regionen verschieben müssen. Eine [kompilierte Version](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip) ist ebenfalls verfügbar. Wenn Sie selbst ein Migrationstool schreiben, kann auch der Quellcode ein nützlicher Leitfaden sein.

> [!NOTE]
> Dieses Tool wird von Microsoft nicht offiziell unterstützt. 
>

Hier finden Sie die allgemeinen Schritte zum Implementieren dieser Option:

1. Erstellen Sie eine VM in der Region, in der sich der vorhandene Cache befindet. Wenn Ihr Dataset groß ist, wählen Sie eine relativ leistungsstarke VM aus, um die Kopierdauer zu verkürzen.

2. Erstellen Sie eine neue Azure Cache for Redis-Instanz.

3. Leeren Sie den neuen Cache, um sicherzustellen, dass sich keine Daten darin befinden. Dieser Schritt ist erforderlich, weil das Kopiertool selbst keine vorhandenen Schlüssel im Zielcache überschreibt.

    > [!IMPORTANT]
    > Stellen Sie sicher, dass Sie NICHT den Quellcache leeren.
    >

4. Verwenden Sie eine Anwendung wie z. B. das oben erwähnte Open-Source-Tool, um das Kopieren der Daten vom Quell- zum Zielcache zu automatisieren. Denken Sie daran, dass der Kopiervorgang je nach Größe Ihres Datasets eine Weile dauern kann.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über Azure Cache for Redis-Features.

* [Azure Cache for Redis-Dienstebenen](cache-overview.md#service-tiers)
* [Importieren von Daten](cache-how-to-import-export-data.md#import)