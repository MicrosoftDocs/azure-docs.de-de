---
title: include file
description: include file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7b75e24e5d59206ee8330c3ca9eaf86eacbad13d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178299"
---
### <a name="premium-performance-block-blob-storage"></a>Blockblobspeicher mit Premium-Leistung

Ein Blockblob-Speicherkonto mit Premium-Leistung ist für Anwendungen optimiert, die kleinere Objekte im Kilobytebereich verwenden. Dies ist ideal für Anwendungen, die hohe Transaktionsraten oder Speicher mit gleichbleibend kurzer Wartezeit erfordern. Der Blockblobspeicher mit Premium-Leistung ist zur Skalierung mit Ihren Anwendungen ausgelegt. Wenn Sie Anwendungen bereitstellen möchten, die Hunderttausende von Anforderungen pro Sekunde oder Speicherkapazität im Petabytebereich erfordern, nehmen Sie Kontakt mit uns auf, indem Sie eine Supportanfrage im [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) übermitteln.

### <a name="premium-performance-filestorage-preview"></a>Premiumleistung von FileStorage (Vorschau)

Premiumdateien verwenden ein eindeutiges Speicherkonto namens **FileStorage (Vorschau)** . Dieser Kontotyp ist für Workloads mit hohem IOPS und hohem Durchsatz bei gleichbleibend kurzer Wartezeit konzipiert. Der Speicher für die Premiumdateien skaliert mit der bereitgestellten Freigabegröße.

|Bereich  |Ziel  |
|---------|---------|
|Bereitgestellte maximale Größe     |ca. 100 TiB     |
|Freigaben   |Unbegrenzt  |
|IOPS     |100.000    |
|Eingehende Daten|4\.136 MiB/s     |
|Ausgehende Daten|6\.204 MiB/s |

 Weitere Informationen zu Skalierbarkeitsziele für die Freigabe von Premiumdateien finden Sie im Abschnitt [Premium files scale targets (Skalierbarkeitsziele für Premiumdateien)](../articles/storage/common/storage-scalability-targets.md#premium-files-scale-targets).

### <a name="premium-performance-page-blob-storage"></a>Seitenblobspeicher mit Premium-Leistung

Speicherkonten des Typs „Premium-Leistung“, „Universell V1“ oder „Universell V2“ weisen die folgenden Skalierbarkeitsziele auf:

| Gesamtkapazität des Kontos                            | Gesamtbandbreite für ein lokal redundantes Speicherkonto                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Datenträgerkapazität: 35 TB <br>Kapazität für Momentaufnahmen: 10 TB | Bis zu 50 Gigabit pro Sekunde für eingehenden<sup>1</sup> und ausgehenden<sup>2</sup> Datenverkehr |

<sup>1</sup> Alle Daten (Anforderungen), die an ein Speicherkonto gesendet werden

<sup>2</sup> Alle Daten (Antworten), die von einem Speicherkonto empfangen werden

Wenn Sie Speicherkonten mit Premium-Leistung für nicht verwaltete Datenträger verwenden und Ihre Anwendung die Skalierbarkeitsziele eines einzelnen Speicherkontos überschreitet, sollten Sie die Migration zu verwalteten Datenträgern erwägen. Falls die Migration zu verwalteten Datenträgern für Sie keine Option ist, können Sie Ihre Anwendung für die Nutzung mehrerer Speicherkonten erstellen. Partitionieren Sie Ihre Daten dann basierend auf diesen Speicherkonten. Wenn Sie beispielsweise 51-TB-Datenträger an mehrere VMs anfügen möchten, können Sie sie auf zwei Speicherkonten verteilen. 35 TB ist der Grenzwert pro Storage Premium-Konto. Stellen Sie sicher, dass ein Speicherkonto mit Premium-Leistung stets Datenträger mit einer Größe von maximal 35 TB enthält.