---
title: Includedatei
description: Includedatei
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: d2daafa6bf5f9a28ad2b61a97e7a8bd2246ae18d
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66147795"
---
# <a name="what-disk-types-are-available-in-azure"></a>Welche Datenträgertypen stehen in Azure zur Verfügung?

Azure Managed Disks bietet derzeit vier Datenträgertypen, von denen drei allgemein verfügbar sind und eine sich in der Vorschauphase befindet. Für jeden dieser vier Datenträgertypen gelten eigene Kundenszenarien.

## <a name="disk-comparison"></a>Datenträgervergleich

Die folgende Tabelle enthält eine Gegenüberstellung von SSD Ultra- (Vorschau), SSD Premium-, SSD Standard- und HDD Standard-Laufwerken für verwaltete Datenträger, um Ihnen die Entscheidung zu erleichtern. (SSD steht für ein Solid State Drive, HDD für ein Festplattenlaufwerk.)

|   | SSD Ultra (Vorschau)   | SSD Premium   | SSD Standard   | HDD Standard   |
|---------|---------|---------|---------|---------|
|Datenträgertyp   |SSD   |SSD   |SSD   |Festplattenlaufwerk   |
|Szenario   |E/A-intensive Workloads wie SAP HANA, führende Datenbanken (z.B. SQL, Oracle) und andere Workloads mit vielen Transaktionen.   |Produktionsworkloads und leistungsabhängige Workloads   |Webserver, wenig genutzte Unternehmensanwendungen und Dev/Test   |Sicherung, nicht kritisch, sporadischer Zugriff   |
|Datenträgergröße   |65.536 Gibibyte (GiB) (Vorschau)   |32767 GiB    |32767 GiB   |32767 GiB   |
|Max. Durchsatz   |2.000 MiB/s (Vorschau)   |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|Max. IOPS   |160.000 (Vorschau)   |20.000   |6.000   |2.000   |

## <a name="ultra-ssd-preview"></a>SSD Ultra (Vorschau)

Azure SSD Ultra (Vorschau) bietet hohen Durchsatz, hohe IOPS und konsistenten Datenträgerspeicher mit niedrigen Wartezeiten für Azure-IaaS-VMs. Zu den weiteren Vorteilen von SSD Ultra gehört die Möglichkeit, die Datenträgerleistung dynamisch in Abstimmung mit Ihren Workloads ändern zu können, ohne Ihre virtuellen Computer neu starten zu müssen. SSD Ultra eignet sich für datenintensive Workloads wie SAP HANA, führende Datenbanken und Workloads mit vielen Transaktionen. SSD Ultra kann nur als Datenträger für Daten verwendet werden. Als Betriebssystemdatenträger empfehlen wir, SSD Premium-Datenträger zu verwenden.

### <a name="performance"></a>Leistung

Wenn Sie einen Ultra-Datenträger bereitstellen, können Sie die Kapazität und die Leistung des Datenträgers unabhängig voneinander konfigurieren. SSD Ultra-Datenträger sind in mehreren festen Größen von 4 GiB bis 64 TiB erhältlich und bieten ein flexibles Leistungskonfigurationsmodell, durch das Sie IOPS und Durchsatz unabhängig konfigurieren können.

Einige Hauptfunktionen von SSD Ultra-Datenträgern sind:

- Datenträgerkapazität: Die Kapazität von SSD Ultra reicht von 4 GiB bis 64 TiB.
- Datenträger-IOPS: SSD Ultra-Datenträger unterstützen IOPS-Limits von 300 IOPS/GiB bis hin zu maximal 160.000 IOPS pro Datenträger. Um die bereitgestellten IOPS-Werte zu erreichen, stellen Sie sicher, dass der IOPS-Wert für den ausgewählten Datenträger unter dem für den virtuellen Computer liegt. Der Mindestwert für Datenträger-IOPS beträgt 100 IOPS.
- Datenträgerdurchsatz: Mit SSD Ultra beträgt das Durchsatzlimit für einen einzelnen Datenträger 256 KiB/s für jeden bereitgestellten IOPS-Wert bis zu maximal 2000 MB/s pro Datenträger (dabei ist 1 MB/s = 10^6 Byte pro Sekunde). Der Mindest-Datenträgerdurchsatz beträgt 1 MiB.
- SSD Ultra unterstützt die Anpassung der Datenträgerleistungsattribute (IOPS und Durchsatz) zur Laufzeit, ohne den Datenträger vom virtuellen Computer zu trennen. Nachdem ein Vorgang zur Größenänderung der Datenträgerleistung auf einem Datenträger gestartet wurde, kann es bis zu einer Stunde dauern, bis die Änderung tatsächlich wirksam wird.

### <a name="disk-size"></a>Datenträgergröße

|Datenträgergröße (GiB)  |IOPS-Obergrenzen  |Durchsatzlimit (MB/s)  |
|---------|---------|---------|
|4     |1.200         |300         |
|8     |2.400         |600         |
|16     |4.800         |1.200         |
|32     |9.600         |2.000         |
|64     |19.200         |2.000         |
|128     |38.400         |2.000         |
|256     |76.800         |2.000         |
|512     |80.000         |2.000         |
|1.024–65.536 (Größen in diesem Bereich erhöhen sich in Schritten von 1 TiB)     |160.000         |2.000         |

### <a name="transactions"></a>Transaktionen

Für SSD Ultra gilt jeder E/A-Vorgang kleiner oder gleich 256 KiB Durchsatz als einzelner E/A-Vorgang. E/A-Vorgänge mit einem Durchsatz größer als 256 KiB gelten als mehrere Ein-bzw. Ausgabevorgänge der Größe 256 KiB.

### <a name="preview-scope-and-limitations"></a>Umfang und Einschränkungen der Vorschauversion

In der Vorschauversion gilt für SSD Ultra-Datenträger Folgendes:

- Werden in „USA, Osten 2“ in einer einzelnen Verfügbarkeitszone unterstützt.  
- Können nur mit Verfügbarkeitszonen verwendet werden. (Verfügbarkeitsgruppen und einzelne VM-Bereitstellungen außerhalb der Zonen können keine Ultra-Datenträger anfügen.)
- Werden nur auf ES/DS v3 VMs unterstützt.
- Sind nur als Datenträger für Daten verfügbar und unterstützen nur für physische Sektorgröße von 4K.  
- Können nur als leere Datenträger erstellt werden.  
- Können aktuell nur mit Azure Resource Manager-Vorlagen, der CLI und dem Python SDK bereitgestellt werden.
- Unterstützen noch keine Datenträger-Momentaufnahmen, VM-Images, Verfügbarkeitsgruppen, VM-Skalierungsgruppen und Azure Disk Encryption.
- Unterstützen (noch) keine Integration in Azure Backup oder Azure Site Recovery.
- Wie bei den  [meisten Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) sollte dieses Feature bis zum Erreichen der allgemeinen Verfügbarkeit nicht für Produktionsworkloads verwendet werden.
