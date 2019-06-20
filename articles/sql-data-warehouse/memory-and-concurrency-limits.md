---
title: Speicher- und Parallelitätsgrenzwerte in Azure SQL Data Warehouse | Microsoft-Dokumentation
description: Zeigen Sie die Speicher- und Parallelitätsgrenzwerte an, die den verschiedenen Leistungsstufen und Ressourcenklassen in Azure SQL Data Warehouse zugewiesen sind.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/15/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 3bc1fef1842911e9b2cfb65b3c8cc72e4b615010
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241266"
---
# <a name="memory-and-concurrency-limits-for-azure-sql-data-warehouse"></a>Speicher- und Parallelitätsgrenzwerte – Azure SQL Data Warehouse
Zeigen Sie die Speicher- und Parallelitätsgrenzwerte an, die den verschiedenen Leistungsstufen und Ressourcenklassen in Azure SQL Data Warehouse zugewiesen sind. Weitere Informationen – auch darüber, wie Sie diese Funktionen in Ihren Workloadverwaltungsplan übernehmen – finden Sie unter [Ressourcenklassen für die Workloadverwaltung](resource-classes-for-workload-management.md). 

SQL Data Warehouse ist zurzeit in zwei Generationen verfügbar: Gen1 und Gen2. Es wird empfohlen, Gen2 von SQL Data Warehouse zu verwenden, um von einer optimalen Leistung für Ihre Data Warehouse-Workloads zu profitieren. In Gen2 wird ein neuer NVMe-SSD-Cache (Solid State Drive) eingeführt, der die am häufigsten verwendeten Daten in der Nähe der CPUs beibehält. Dadurch werden Remote-E/A-Vorgänge für Ihre intensivsten und anspruchsvollen Workloads vermieden. Neben Leistungsvorteilen bietet Gen2 mit einer Skalierbarkeit auf bis zu 30.000 Data Warehouse-Einheiten und einer unbegrenzten spaltenorientierten Speicherung das höchste Skalierungsniveau. Für die Vorgängergeneration (Gen1) von SQL Data Warehouse stellen wir zwar nach wie vor Support und die gleichen Features bereit, wir empfehlen jedoch, so bald wie möglich [ein Upgrade auf Gen2](upgrade-to-latest-generation.md) durchzuführen. 

## <a name="data-warehouse-capacity-settings"></a>Data Warehouse-Kapazitätseinstellungen
Die folgenden Tabellen zeigen die maximale Kapazität für das Data Warehouse auf unterschiedlichen Leistungsstufen. Informationen zum Ändern der Leistungsstufe finden Sie unter [Skalieren von Computerressourcen – Portal](quickstart-scale-compute-portal.md).

### <a name="gen2"></a>Gen2

Gen2 stellt 2,5-mal mehr Arbeitsspeicher pro Abfrage bereit als Gen1. Diesem zusätzlichen Speicher verdankt Gen2 seine schnelle Ausführungsleistung.  Die Leistungsstufen für Gen2 erstrecken sich von DW100c bis DW30000c. 

| Leistungsstufe | Serverknoten | Verteilungen pro Serverknoten | Arbeitsspeicher pro Data Warehouse (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100c            | 1             | 60                             |    60                          |
| DW200c            | 1             | 60                             |   120                          |
| DW300c            | 1             | 60                             |   180                          |
| DW400c            | 1             | 60                             |   240                          |
| DW500c            | 1             | 60                             |   300                          |
| DW1000c           | 2             | 30                             |   600                          |
| DW1500c           | 3             | 20                             |   900                          |
| DW2000c           | 4             | 15                             |  1200                          |
| DW2500c           | 5             | 12                             |  1500                          |
| DW3000c           | 6             | 10                             |  1800                          |
| DW5000c           | 10            | 6                              |  3000                          |
| DW6000c           | 12            | 5                              |  3600                          |
| DW7500c           | 15            | 4                              |  4500                          |
| DW10000c          | 20            | 3                              |  6000                          |
| DW15000c          | 30            | 2                              |  9000                          |
| DW30000c          | 60            | 1                              | 18000                          |

Die maximale DWU in Gen2 beträgt DW30000c. Sie bietet 60 Serverknoten und eine Verteilung pro Serverknoten. Beispielsweise verarbeitet ein 600-TB-Data Warehouse mit DW30000c etwa 10 TB pro Serverknoten.

### <a name="gen1"></a>Gen1

Die Servicelevels für Gen1 erstrecken sich von DW100 bis DW6000. 

| Leistungsstufe | Serverknoten | Verteilungen pro Serverknoten | Arbeitsspeicher pro Data Warehouse (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100             | 1             | 60                             |  24                            |
| DW200             | 2             | 30                             |  48                            |
| DW300             | 3             | 20                             |  72                            |
| DW400             | 4             | 15                             |  96                            |
| DW500             | 5             | 12                             | 120                            |
| DW600             | 6             | 10                             | 144                            |
| DW1000            | 10            | 6                              | 240                            |
| DW1200            | 12            | 5                              | 288                            |
| DW1500            | 15            | 4                              | 360                            |
| DW2000            | 20            | 3                              | 480                            |
| DW3000            | 30            | 2                              | 720                            |
| DW6000            | 60            | 1                              | 1.440                           |

## <a name="concurrency-maximums"></a>Parallelitätshöchstwerte
Um sicherzustellen, dass für jede Abfrage genügend Ressourcen zur effizienten Verarbeitung zur Verfügung stehen, verfolgt SQL Data Warehouse die Nutzung von Ressourcen, indem jeder Abfrage Parallelitätsslots zugewiesen werden. Das System stellt Abfragen basierend auf Wichtigkeit und Parallelitätsslots in eine Warteschlange. Abfragen verbleiben in der Warteschlange, bis genügend Parallelitätsslots verfügbar sind. [Wichtigkeit](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-importance) und Parallelitätsslots legen außerdem die CPU-Prioritäten fest. Weitere Informationen finden Sie unter [Analysieren Ihrer Workload](analyze-your-workload.md).

### <a name="gen2"></a>Gen2
 
**Statische Ressourcenklassen**

Die folgende Tabelle zeigt die maximale Anzahl gleichzeitiger Abfragen und die Parallelitätsslots für jede [statische Ressourcenklasse](resource-classes-for-workload-management.md).  

| Service Level | Maximale Anzahl gleichzeitiger Abfragen | Verfügbare Parallelitätsslots | Durch „staticrc10“ verwendete Slots | Durch „staticrc20“ verwendete Slots | Durch „staticrc30“ verwendete Slots | Durch „staticrc40“ verwendete Slots | Durch „staticrc50“ verwendete Slots | Durch „staticrc60“ verwendete Slots | Durch „staticrc70“ verwendete Slots | Durch „staticrc80“ verwendete Slots |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100c        |  4                         |    4                        | 1         | 2          | 4          | 4          | 4         |  4         |  4         |  4         |
| DW200c        |  8                         |    8                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |  8        |
| DW300c        | 12                         |   12                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400c        | 16                         |   16                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500c        | 20                         |   20                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

**Dynamische Ressourcenklassen**

> [!NOTE]
> Die Ressourcenklasse „,smallrc“ in Gen2 fügt mit steigenden Servicelevels dynamisch Arbeitsspeicher hinzu und unterstützt lediglich maximal 32 (DW1000c) bzw. 4 (DW100c) gleichzeitige Abfragen.  Wird die Instanz über DW1500c hinaus skaliert, erhöhen sich die von „smallrc“ verwendeten Parallelitätsslots und Arbeitsspeicher mit steigenden Servicelevels. 
>
>

Die folgende Tabelle zeigt die maximale Anzahl gleichzeitiger Abfragen und die Parallelitätsslots für jede [dynamische Ressourcenklasse](resource-classes-for-workload-management.md). Im Gegensatz zu Gen1 sind dynamische Ressourcenklassen in Gen2 wirklich dynamisch.  Gen2 verwendet in allen Servicelevels eine Speicherbelegung von 3-10-22-70 Prozent für die Ressourcenklassen Klein-Medium-Groß-Extragroß.

| Service Level | Maximale Anzahl gleichzeitiger Abfragen | Verfügbare Parallelitätsslots | Durch „smallrc“ verwendete Slots | Durch „mediumrc“ verwendete Slots | Durch „largerc“ verwendete Slots | Durch „xlargerc“ verwendete Slots |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW100c        |  4                         |    4                        | 1                     |  1                     |  1                    |   2                    |
| DW200c        |  8                         |    8                        | 1                     |  1                     |  1                    |   5                    |
| DW300c        | 12                         |   12                        | 1                     |  1                     |  2                    |   8                    |
| DW400c        | 16                         |   16                        | 1                     |  1                     |  3                    |  11                    |
| DW500c        | 20                         |   20                        | 1                     |  2                     |  4                    |  14                    |
| DW1000c       | 32                         |   40                        | 1                     |  4                     |  8                    |  28                    |
| DW1500c       | 32                         |   60                        | 1                     |  6                     |  13                   |  42                    |
| DW2000c       | 32                         |   80                        | 2                     |  8                     |  17                   |  56                    |
| DW2500c       | 32                         |  100                        | 3                     | 10                     |  22                   |  70                    |
| DW3000c       | 32                         |  120                        | 3                     | 12                     |  26                   |  84                    |
| DW5000c       | 32                         |  200                        | 6                     | 20                     |  44                   | 140                    |
| DW6000c       | 32                         |  240                        | 7                     | 24                     |  52                   | 168                    |
| DW7500c       | 32                         |  300                        | 9                     | 30                     |  66                   | 210                    |
| DW10000c      | 32                         |  400                        | 12                    | 40                     |  88                   | 280                    |
| DW15000c      | 32                         |  600                        | 18                    | 60                     | 132                   | 420                    |
| DW30000c      | 32                         | 1200                        | 36                    | 120                    | 264                   | 840                    |



#### <a name="gen1"></a>Gen1

Statische Ressourcenklassen

Die folgende Tabelle zeigt die maximale Anzahl gleichzeitiger Abfragen und die Parallelitätsslots für jede [statische Ressourcenklasse](resource-classes-for-workload-management.md) in **Gen1**.

| Servicelevel | Maximale Anzahl gleichzeitiger Abfragen | Maximale Anzahl von Parallelitätsslots | Durch „staticrc10“ verwendete Slots | Durch „staticrc20“ verwendete Slots | Durch „staticrc30“ verwendete Slots | Durch „staticrc40“ verwendete Slots | Durch „staticrc50“ verwendete Slots | Durch „staticrc60“ verwendete Slots | Durch „staticrc70“ verwendete Slots | Durch „staticrc80“ verwendete Slots |
|:-------------:|:--------------------------:|:-------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100         | 4                          |   4                       | 1         | 2          | 4          | 4          |  4         |  4         |  4         |   4        |
| DW200         | 8                          |   8                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW300         | 12                         |  12                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400         | 16                         |  16                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500         | 20                         |  20                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW600         | 24                         |  24                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000        | 32                         |  40                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1200        | 32                         |  48                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500        | 32                         |  60                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000        | 48                         |  80                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000        | 64                         | 120                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW6000        | 128                        | 240                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

Dynamische Ressourcenklassen
> [!NOTE]
> Die Ressourcenklasse „smallrc“ in Gen1 teilt eine feste Menge an Arbeitsspeicher pro Abfrage zu, ähnlich wie bei der statische Ressourcenklasse „staticrc10“.  Da „smallrc“ statisch ist, kann diese Ressourcenklasse auf 128 gleichzeitige Abfragen skaliert werden. 
>
>

Die folgende Tabelle zeigt die maximale Anzahl gleichzeitiger Abfragen und die Parallelitätsslots für jede [dynamische Ressourcenklasse](resource-classes-for-workload-management.md) in **Gen1**.

| Servicelevel | Maximale Anzahl gleichzeitiger Abfragen | Verfügbare Parallelitätsslots | Durch „smallrc“ verwendete Slots | Durch „mediumrc“ verwendete Slots | Durch „largerc“ verwendete Slots | Durch „xlargerc“ verwendete Slots |
|:-------------:|:--------------------------:|:---------------------------:|:-------:|:--------:|:-------:|:--------:|
| DW100         |  4                         |   4                         | 1       |  1       |  2      |   4      |
| DW200         |  8                         |   8                         | 1       |  2       |  4      |   8      |
| DW300         | 12                         |  12                         | 1       |  2       |  4      |   8      |
| DW400         | 16                         |  16                         | 1       |  4       |  8      |  16      |
| DW500         | 20                         |  20                         | 1       |  4       |  8      |  16      |
| DW600         | 24                         |  24                         | 1       |  4       |  8      |  16      |
| DW1000        | 32                         |  40                         | 1       |  8       | 16      |  32      |
| DW1200        | 32                         |  48                         | 1       |  8       | 16      |  32      |
| DW1500        | 32                         |  60                         | 1       |  8       | 16      |  32      |
| DW2000        | 48                         |  80                         | 1       | 16       | 32      |  64      |
| DW3000        | 64                         | 120                         | 1       | 16       | 32      |  64      |
| DW6000        | 128                        | 240                         | 1       | 32       | 64      | 128      |


Wenn einer dieser Grenzwerte erreicht wird, werden neue Abfragen in die Warteschlange eingereiht und nach dem Prinzip „First In, First Out“ ausgeführt.  Wenn eine Abfrage abgeschlossen wird und die Anzahl von Abfragen und Slots unter die Grenzwerte sinkt, gibt SQL Data Warehouse Abfragen in der Warteschlange frei. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Verwenden von Ressourcenklassen zum weiteren Optimieren Ihrer Workload finden Sie in den folgenden Artikeln:
* [Ressourcenklassen für die Workloadverwaltung](resource-classes-for-workload-management.md)
* [Analysieren Ihrer Workload](analyze-your-workload.md)

