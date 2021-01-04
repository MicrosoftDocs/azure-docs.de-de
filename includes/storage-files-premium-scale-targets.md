---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: 8dcb58499113b0b7ae0814419f0a76965a0ed945
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94680937"
---
#### <a name="additional-premium-file-share-level-limits"></a>Zusätzliche Freigabegrenzwerte für Dateien auf Premiumebene

|Bereich  |Ziel  |
|---------|---------|
|Mindestgröße vergrößern/verkleinern    |1 GiB      |
|IOPS-Grundwert    |1 IOPS pro GiB, bis zu 100.000|
|IOPS-Bursting    |3 x IOPS pro GiB, bis zu 100.000|
|Ausgangsrate         |60 MiB/s + 0,06 * bereitgestelltes GiB        |
|Eingangsrate| 40 MiB/s + 0,04 * bereitgestelltes GiB |

#### <a name="file-level-limits"></a>Grenzwerte auf Dateiebene

|Bereich  |Standarddateien  |Premiumdateien  |
|---------|---------|---------|
|Size     |1 TiB         |4 TiB         |
|Max. IOPS pro Datei      |1\.000         |Bis zu 8.000*         |
|Gleichzeitige Handles     |2\.000         |2\.000         |
|Ausgehende Daten     |Siehe: Durchsatzwerte für Standarddateien         |300 MiB/Sek. (bis zu 1 GiB/s bei SMB Multichannel Vorschau)**         |
|Eingehende Daten     |Siehe: Durchsatzwerte für Standarddateien         |200 MiB/Sek. (bis zu 1 GiB/s bei SMB Multichannel Vorschau)**        |
|Throughput     |Bis zu 60 MiB/s         |Siehe: Ein-/Ausgangswerte für Premiumdateien         |

\* <sup> Gilt für Ein-/Ausgaben beim Lesen und Schreiben (normalerweise kleinere E/A-Größen <=64 K). Metadatenvorgänge (außer Lese-und Schreibvorgängen) können niedriger sein. </sup>

\*\* <sup> Unterliegen Netzwerkgrenzwerten für Computer, verfügbarer Bandbreite, E/A-Größen, Warteschlangenlänge und anderen Faktoren. Weitere Informationen finden Sie unter [SMB Multichannel-Leistung](../articles/storage/files/storage-files-smb-multichannel-performance.md). </sup>
