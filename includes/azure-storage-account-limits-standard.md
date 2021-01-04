---
title: include file
description: include file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ef9efe389894af7c792e980922ca422e9d05929b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026616"
---
In der folgenden Tabelle werden die für Azure GPv1, GPv2, Blobspeicher und Blockblob-Speicherkonten geltenden Standardgrenzwerte beschrieben. Der Grenzwert für *eingehend* bezieht sich auf alle Daten, die an ein Speicherkonto gesendet werden. Der Grenzwert für *ausgehend* bezieht sich auf alle Daten, die von einem Speicherkonto empfangen werden.

> [!NOTE]
> Sie können höhere Grenzwerte für die Kapazität und für eingehende Daten anfordern. Wenden Sie sich dazu an den [Azure-Support](https://azure.microsoft.com/support/faq/).

| Resource | Begrenzung |
| --- | --- |
| Anzahl von Speicherkonten pro Region und Abonnement, einschließlich Standard- und Premium-Speicherkonten.| 250 |
| Maximale Speicherkontokapazität | 5 PiB<sup>1</sup>|
| Maximale Anzahl an Blobcontainern, Blobs, Dateifreigaben, Tabellen, Warteschlangen, Entitäten oder Meldungen pro Speicherkonto | Keine Begrenzung |
| Maximale Anforderungsrate<sup>1</sup> pro Speicherkonto | 20.000 Anforderungen pro Sekunde |
| Maximaler Eingang<sup>1</sup> pro Speicherkonto (Regionen US, Europa) | 10 GBit/s |
| Maximaler Eingang<sup>1</sup> pro Speicherkonto (Regionen außerhalb von USA und Europa) | 5 GBit/s bei aktiviertem RA-GRS/GRS, 10 GBit/s für LRS/ZRS<sup>2</sup> |
| Maximaler Ausgang für universelle v2-Speicherkonten und Blobspeicherkonten (alle Regionen) | 50 GBit/s |
| Maximaler Ausgang für universelle v1-Speicherkonten (US-Regionen) | 20 GBit/s bei aktiviertem RA-GRS/GRS, 30 GBit/s für LRS/ZRS<sup>2</sup> |
| Maximaler Ausgang für universelle v1-Speicherkonten (US-fremde Regionen) | 10 GBit/s bei aktiviertem RA-GRS/GRS, 15 GBit/s für LRS/ZRS<sup>2</sup> |
| Maximale Anzahl von VNET-Regeln pro Speicherkonto | 200 |
| Maximale Anzahl von Regeln für IP-Adressen pro Speicherkonto | 200 |

<sup>1</sup> Azure Storage Standard-Konten unterstützen höhere Grenzwerte für Kapazität und Eingang auf Anforderung. Wenden Sie sich an den [Azure-Support](https://azure.microsoft.com/support/faq/), um eine Erhöhung der Kontogrenzwerte anzufordern.

<sup>2</sup> Wenn für Ihr Speicherkonto Lesezugriff mit georedundantem Speicher (RA-GRS) oder geozonenredundantem Speicher (RA-GZRS) aktiviert ist, sind die Ausgangsziele für den sekundären Standort mit denen des primären Standorts identisch. Weitere Informationen finden Sie unter [Azure Storage-Replikation](../articles/storage/common/storage-redundancy.md).

> [!NOTE]
> Microsoft empfiehlt, für die meisten Szenarien Speicherkonten vom Typ „Allgemein v2“ zu verwenden. Sie können ganz einfach ein Upgrade von einem Konto vom Typ „Allgemein v1“ oder einem Blob Storage-Konto auf ein Konto vom Typ „Allgemein v2“ durchführen. Dabei treten keine Ausfallzeiten auf, und Sie müssen keine Daten kopieren. Weitere Informationen finden Sie unter [Durchführen eines Upgrades auf ein Speicherkonto vom Typ „Allgemein v2“](../articles/storage/common/storage-account-upgrade.md).

Alle Speicherkonten werden unabhängig von ihrem Erstellungszeitpunkt in einer flachen Netzwerktopologie ausgeführt. Weitere Informationen zur flachen Netzwerkarchitektur von Azure Storage sowie zur Skalierbarkeit finden Sie unter [Microsoft Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets) (Hochverfügbarer Cloud-Speicherdienst mit starker Konsistenz).