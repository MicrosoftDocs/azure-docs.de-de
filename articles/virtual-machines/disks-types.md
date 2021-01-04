---
title: 'Auswählen eines Datenträgertyps für virtuelle Azure IaaS-Computer: verwaltete Datenträger'
description: Erhalten Sie Informationen über die verfügbaren Azure-Datenträgertypen für virtuelle Computer, einschließlich Ultra-Datenträgern, SSD Premium, SSD Standard und Standard-HDDs.
author: roygara
ms.author: rogarana
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 1a7e8e71e26af241d16095a5fa1e6a02a7e3d4c2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500766"
---
# <a name="what-disk-types-are-available-in-azure"></a>Welche Datenträgertypen stehen in Azure zur Verfügung?

Azure Managed Disks (verwaltete Azure-Datenträger) stellt derzeit vier Datenträgertypen bereit, wobei jeder Typ auf bestimmte Kundenszenarien ausgerichtet ist.

## <a name="disk-comparison"></a>Datenträgervergleich

Die folgende Tabelle enthält eine Gegenüberstellung von Ultra-Datenträgern, SSD Premium-, SSD Standard- und HDD Standard-Laufwerken für verwaltete Datenträger, um Ihnen die Entscheidung zu erleichtern.

| Detail | Ultra Disks | SSD Premium | SSD Standard | HDD Standard |
| ------ | ---------- | ----------- | ------------ | ------------ |
|Datenträgertyp   |SSD   |SSD   |SSD   |Festplattenlaufwerk   |
|Szenario   |E/A-intensive Workloads wie [SAP HANA](workloads/sap/hana-vm-operations-storage.md), führende Datenbanken (z. B. SQL, Oracle) und andere Workloads mit vielen Transaktionen.   |Produktionsworkloads und leistungsabhängige Workloads   |Webserver, wenig genutzte Unternehmensanwendungen und Dev/Test   |Sicherung, nicht kritisch, sporadischer Zugriff   |
|Maximale Datenträgergröße   |65.536 Gibibyte (GiB)    |32767 GiB    |32767 GiB   |32767 GiB   |
|Max. Durchsatz   |2\.000 MB/s    |900 MB/s   |750 MB/s   |500 MB/s   |
|Max. IOPS   |160.000    |20.000   |6\.000   |2\.000   |

## <a name="ultra-disk"></a>Ultra-Datenträger

Azure Ultra-Datenträger bieten hohen Durchsatz, hohe IOPS und konsistenten Datenträgerspeicher mit niedrigen Wartezeiten für Azure IaaS-VMs. Zu den weiteren Vorteilen von Ultra-Datenträgern gehört die Möglichkeit, die Datenträgerleistung dynamisch in Abstimmung mit Ihren Workloads ändern zu können, ohne Ihre virtuellen Computer (virtual machines, VMs) neu starten zu müssen. Ultra-Datenträger eignen sich für datenintensive Workloads wie SAP HANA, führende Datenbanksysteme und Workloads mit vielen Transaktionen. Ultra-Datenträger können nur als Datenträger für Daten verwendet werden. Als Betriebssystemdatenträger empfehlen wir, SSD Premium-Datenträger zu verwenden.

### <a name="performance"></a>Leistung

Wenn Sie einen Ultra-Datenträger bereitstellen, können Sie die Kapazität und die Leistung des Datenträgers unabhängig voneinander konfigurieren. Ultra-Datenträger sind in mehreren festen Größen von 4 GiB bis 64 TiB erhältlich und bieten ein flexibles Leistungskonfigurationsmodell, durch das Sie IOPS und Durchsatz unabhängig konfigurieren können.

Einige Hauptfunktionen von Ultra-Datenträgern sind:

- Datenträgerkapazität: Die Kapazität von Ultra-Datenträgern reicht von 4 GiB bis 64 TiB.
- Datenträger-IOPS: Ultra-Datenträger unterstützen IOPS-Limits von 300 IOPS/GiB bis hin zu maximal 160.000 IOPS pro Datenträger. Um die bereitgestellten IOPS-Werte zu erreichen, stellen Sie sicher, dass der IOPS-Wert für den ausgewählten Datenträger unter dem IOPS-Limit für den virtuellen Computer liegt. Der kleinste garantierte IOPS-Wert pro Datenträger beträgt 2 IOPS/GiB mit einem Gesamtmindestwert von 100 IOPS. Wenn Sie also beispielsweise über einen 4-GiB-Ultra-Datenträger verfügen, stehen Ihnen anstelle von acht IOPS mindestens 100 IOPS zur Verfügung.
- Datenträgerdurchsatz: Mit Ultra-Datenträgern beträgt das Durchsatzlimit für einen einzelnen Datenträger 256 KiB/s für jeden bereitgestellten IOPS-Wert bis zu maximal 2000 MB/s pro Datenträger (dabei ist 1 MB/s = 10^6 Byte pro Sekunde). Der garantierte Mindestdurchsatz pro Datenträger beträgt 4 KiB/s für jeden bereitgestellten IOPS-Wert mit einem Gesamtmindestwert von 1 MB/s.
- Ultra-Datenträger unterstützen die Anpassung der Datenträgerleistungsattribute (IOPS und Durchsatz) zur Laufzeit, ohne den Datenträger vom virtuellen Computer zu trennen. Nachdem ein Vorgang zur Größenänderung der Datenträgerleistung auf einem Datenträger gestartet wurde, kann es bis zu einer Stunde dauern, bis die Änderung tatsächlich wirksam wird. Innerhalb von 24 Stunden kann die Leistung bis zu viermal angepasst werden. Es kann vorkommen, dass ein Leistungsanpassungsvorgang aufgrund von unzureichender Kapazität der Leistungsbandbreite nicht erfolgreich ist.

### <a name="disk-size"></a>Datenträgergröße

|Datenträgergröße (GiB)  |IOPS-Limit  |Durchsatzlimit (MB/s)  |
|---------|---------|---------|
|4     |1\.200         |300         |
|8     |2\.400         |600         |
|16     |4\.800         |1\.200         |
|32     |9\.600         |2\.000         |
|64     |19.200         |2\.000         |
|128     |38.400         |2\.000         |
|256     |76.800         |2\.000         |
|512     |153.600         |2\.000         |
|1\.024–65.536 (Größen in diesem Bereich erhöhen sich in Schritten von 1 TiB)     |160.000         |2\.000         |

Ultra-Datenträger sind darauf ausgelegt, Wartezeiten von weniger als einer Millisekunde sowie die in der vorherigen Tabelle beschriebenen IOPS- und Durchsatzwerte für 99,99 % der Zeit bereitzustellen.

### <a name="ga-scope-and-limitations"></a>Umfang und Einschränkungen für allgemeine Verfügbarkeit

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]


Wenn Sie Ultra-Datenträger verwenden möchten, finden Sie weitere Informationen in diesem Artikel: [Verwenden von Azure Ultra-Datenträgern](disks-enable-ultra-ssd.md).

## <a name="premium-ssd"></a>SSD Premium

Azure SSD Premium bietet Datenträgerunterstützung mit hoher Leistung und geringer Wartezeit für virtuelle Computer (VMs) mit E/A-intensiven Workloads. Um die Geschwindigkeit und Leistung von Storage Premium-Datenträgern zu nutzen, können Sie vorhandene VM-Datenträger zu SSD Premium-Datenträgern migrieren. SSD Premium ist für unternehmenskritische Produktionsanwendungen geeignet. SSD Premium kann nur mit VM-Serien verwendet werden, die mit Storage Premium kompatibel sind.

Weitere Informationen zu den einzelnen VM-Typen und -Größen in Azure für Windows oder Linux (einschließlich der Größen, die mit Storage Premium kompatibel sind) finden Sie unter [Größen für virtuelle Computer in Azure](sizes.md). Gemäß diesem Artikel müssen Sie jede einzelne VM-Größe überprüfen, um festzustellen, ob sie mit Storage Premium kompatibel ist.

### <a name="disk-size"></a>Datenträgergröße
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

Im Gegensatz zu einem Standard-Speicherdatenträger sind bei der Bereitstellung eines Storage Premium-Datenträgers die Kapazität, die IOPS und der Durchsatz dieses Datenträgers garantiert. Wenn Sie beispielsweise einen P50-Datenträger erstellen, stellt Azure eine Speicherkapazität von 4.095 GB, 7.500 IOPS und einen Durchsatz von 250 MB/s für diesen Datenträger bereit. Die Anwendung kann die Kapazität und Leistung ganz oder teilweise nutzen. SSD Premium-Datenträger sind dafür ausgelegt, Wartezeiten im niedrigen einstelligen Millisekundenbereich sowie die in der vorherigen Tabelle beschriebenen IOPS und Durchsätze 99,9 % der Zeit bereitzustellen.

## <a name="bursting"></a>Bursting

SSD Premium-Größen kleiner als P30 bieten jetzt Datenträgerbursting und können die IOPS pro Datenträger auf bis zu 3.500 und deren Bandbreite auf bis 170 MB/s steigern. Bursting wird automatisiert und funktioniert auf Basis eines Guthabensystems. Guthaben werden automatisch in einem Burst-Bucket akkumuliert, wenn der Datenträgerverkehr unterhalb des bereitgestellten Leistungsziels liegt. Guthaben werden automatisch verbraucht, wenn der Datenverkehr über das Ziel hinausgeht (bis zum maximalen Burst-Limit). Das maximale Burst-Limit definiert die Obergrenze für Datenträger-IOPS und Bandbreite (auch wenn Sie über Burst-Guthaben verfügen, das verbraucht werden kann). Datenträgerbursting bietet eine bessere Toleranz bei unvorhersehbaren Änderungen der E/A-Muster. Es ist am besten für den Start des Betriebssystemdatenträgers und Anwendungen mit extremen Spitzen im Datenverkehr geeignet.    

Die Unterstützung für Datenträgerbursting wird standardmäßig in neuen Bereitstellungen der entsprechenden Datenträgergrößen aktiviert, ohne dass eine Benutzeraktion erforderlich ist. Für vorhandene Datenträger mit den entsprechenden Größen haben Sie zwei Möglichkeiten, um die Burstingfunktion zu aktivieren: Trennen und erneutes Anschließen des Datenträgers oder Beenden und Neustarten der zugehörigen VM. Alle für Bursting infrage kommenden Datenträgergrößen werden mit einem vollen Burst-Guthaben-Bucket gestartet, das eine maximale Dauer von 30 Minuten mit maximalem Burst-Limit unterstützt, wenn der Datenträger an einen virtuellen Computer abgeschlossen wird. Weitere Informationen zur Funktionsweise von Datenträgerbursting auf Azure-Datenträgern finden Sie unter [SSD Premium-Bursting](./disk-bursting.md). 

### <a name="transactions"></a>Transaktionen

Für SSD Premium gilt jeder E/A-Vorgang kleiner oder gleich 256 KiB Durchsatz als einzelner E/A-Vorgang. E/A-Vorgänge mit einem Durchsatz größer als 256 KiB gelten als mehrere Ein-bzw. Ausgabevorgänge der Größe 256 KiB.

## <a name="standard-ssd"></a>SSD Standard

Azure SSD Standard-Datenträger stellen eine kostengünstige Speichermöglichkeit dar, die für Workloads mit konstanter Leistung bei niedrigen IOPS-Werten optimiert ist. Standard-SSDs eignen sich insbesondere als Einstiegslösung zur Verlagerung von Diensten in die Cloud. Dies gilt vor allem dann, wenn bei der Ausführung vieler Workloads auf lokalen HDD-Lösungen Probleme auftreten. Im Vergleich zu HDD Standard bieten SSD Standard-Datenträger eine bessere Verfügbarkeit, Konsistenz, Zuverlässigkeit und Latenz. SSD Standard-Datenträger eignen sich für Webserver, Anwendungsserver mit geringer IOPS-Rate, wenig genutzte Unternehmensanwendungen und Dev/Test-Workloads. Wie HDD Standard-Datenträger sind SSD Standard-Datenträger für alle Azure-VMs verfügbar.

### <a name="disk-size"></a>Datenträgergröße
[!INCLUDE [disk-storage-standard-ssd-sizes](../../includes/disk-storage-standard-ssd-sizes.md)]

Standard-SSDs sind dafür ausgelegt, 99 % der Zeit Wartezeiten im einstelligen Millisekundenbereich sowie IOPS und Durchsätze bis zu den in der vorherigen Tabelle genannten Grenzwerten bereitzustellen. Die Istwerte für IOPS und Durchsätze können in einigen Fällen je nach Datenverkehrsmuster variieren. Standard-SSDs stellen eine konstantere Leistung und geringere Latenzen als HDD-Datenträger bereit.

### <a name="transactions"></a>Transaktionen

Für SSD Standard gilt jeder E/A-Vorgang kleiner oder gleich 256 KiB Durchsatz als ein einzelner E/A-Vorgang. E/A-Vorgänge mit einem Durchsatz größer als 256 KiB gelten als mehrere Ein-bzw. Ausgabevorgänge der Größe 256 KiB. Diese Transaktionen besitzen Auswirkungen auf die Abrechnung.

## <a name="standard-hdd"></a>HDD Standard

Azure HDD Standard bietet zuverlässige, kostengünstige Datenträgerunterstützung für virtuelle Computer mit Workloads, bei denen Wartezeiten eine untergeordnete Rolle spielen. Bei Verwendung von Standardspeicher werden die Daten auf Festplattenlaufwerken (Hard Disk Drives, HDDs) gespeichert. Latenz, IOPS und Durchsatz von HDD Standard-Datenträgern können im Vergleich mit SSD-basierten Datenträgern stärker variieren. HDD Standard-Datenträger sind so konzipiert, dass sie eine Schreiblatenz von unter 10 ms und eine Leselatenzen von unter 20 ms für die meisten E/A-Vorgänge ermöglichen, wobei die tatsächliche Leistung jedoch je nach E/A-Größe und Workloadmuster variieren kann. Wenn Sie mit virtuellen Computern arbeiten, können Sie und HDD Standard-Datenträger für Dev/Test-Szenarien sowie weniger kritische Workloads verwenden. HDD Standard-Datenträger sind in allen Azure-Regionen verfügbar und können mit allen Azure-VMs verwendet werden.

### <a name="disk-size"></a>Datenträgergröße
[!INCLUDE [disk-storage-standard-hdd-sizes](../../includes/disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transaktionen

Für HDD Standard wird jeder E/A-Vorgang als einzelne Transaktion betrachtet, und zwar unabhängig von der E/A-Größe. Diese Transaktionen besitzen Auswirkungen auf die Abrechnung.

## <a name="billing"></a>Abrechnung

Bei der Verwendung verwalteter Datenträger gelten die folgenden Abrechnungsaspekte:

- Datenträgertyp
- Größe des verwalteten Datenträgers
- Momentaufnahmen
- Ausgehende Datenübertragungen
- Anzahl von Transaktionen

**Größe des verwalteten Datenträgers**: Die Abrechnung für verwaltete Datenträger erfolgt nach bereitgestellter Größe. Azure ordnet die bereitgestellte Größe (aufgerundet) der nächsthöheren angebotenen Datenträgergröße zu. Details zu den angebotenen Datenträgergrößen finden Sie in den obigen Tabellen. Jeder Datenträger ist einem Angebot für eine unterstützte bereitgestellte Datenträgergröße zugeordnet und wird entsprechend berechnet. Wenn Sie z.B. einen SSD Standard-Datenträger mit 200 GiB bereitstellen, wird das Datenträgerangebot E15 (256 GiB) zugeordnet. Die Abrechnung für bereitgestellte Datenträger erfolgt anteilig auf Stundenbasis unter Verwendung des monatlichen Preises für das Speicherangebot. Wenn Sie z.B. einen Datenträger des Typs E10 bereitgestellt und diesen nach 20 Stunden gelöscht haben, erfolgt die Abrechnung für das E10-Angebot anteilig für 20 Stunden. Welche Datenmenge tatsächlich auf den Datenträger geschrieben wurde, spielt keine Rolle.

**Momentaufnahmen**: Momentaufnahmen werden auf Basis der verwendeten Größe in Rechnung gestellt. Wenn Sie beispielsweise eine Momentaufnahme eines verwalteten Datenträgers mit einer bereitgestellten Kapazität von 64 GiB und einer tatsächlichen Datengröße von 10 GiB erstellen, wird die Momentaufnahme nur für die in Anspruch genommene Datengröße von 10 GiB in Rechnung gestellt.

Weitere Informationen zu Momentaufnahmen finden Sie im Abschnitt zu Momentaufnahmen in der [Übersicht verwalteter Datenträgern](managed-disks-overview.md).

**Ausgehende Datenübertragungen:**[Ausgehende Datenübertragungen](https://azure.microsoft.com/pricing/details/bandwidth/) (Daten, die von den Azure-Rechenzentren ausgehen) verursachen Kosten bei der Bandbreitenverwendung.

**Transaktionen:** Ihnen wird die Anzahl von Transaktionen berechnet, die Sie für einen verwalteten Standarddatenträger durchführen. Für SSD Standard gilt jeder E/A-Vorgang kleiner oder gleich 256 KiB Durchsatz als ein einzelner E/A-Vorgang. E/A-Vorgänge mit einem Durchsatz größer als 256 KiB gelten als mehrere Ein-bzw. Ausgabevorgänge der Größe 256 KiB. Für HDD Standard wird jeder E/A-Vorgang als einzelne Transaktion betrachtet, und zwar unabhängig von der E/A-Größe.

Ausführliche Informationen zu den Preisen für Managed Disks, einschließlich der Transaktionskosten, finden Sie unter [Verwaltete Datenträger – Preise](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Reservierungsgebühr für virtuellen Computer auf Ultra-Datenträger

Virtuelle Azure-Computer können angeben, ob sie mit Ultra-Datenträgern kompatibel sind. Ein mit Ultra-Datenträgern kompatibler virtueller Computer weist dedizierte Bandbreitenkapazität zwischen der Compute-VM-Instanz und der Blockspeicher-Skalierungseinheit zu, um die Leistung zu optimieren und die Wartezeiten zu verringern. Das Hinzufügen dieser Funktion auf dem virtuellen Computer führt dazu, dass die Reservierungsgebühr nur erhoben wird, wenn Sie die Ultra-Datenträgerfunktion auf dem virtuellen Computer aktiviert haben, ohne ihr einen Ultra-Datenträger anzufügen. Wenn ein Ultra-Datenträger an den mit Ultra-Datenträgern kompatiblen virtuellen Computer angefügt ist, fällt diese Gebühr nicht an. Diese Gebühr fällt pro auf der VM bereitgestellter vCPU an. 

> [!Note]
> Für [VM-Größen mit eingeschränkter Anzahl der Kerne](constrained-vcpu.md) ergibt sich die Reservierungsgebühr anhand der tatsächlichen Anzahl der vCPUs und nicht anhand der eingeschränkten Kerne. Für Standard_E32-8s_v3 basiert die Reservierungsgebühr auf 32 Kernen. 

Preisdetails für Ultra-Datenträger finden Sie auf der [Seite mit der Preisübersicht für Azure-Datenträger](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="azure-disk-reservation"></a>Azure-Datenträgerreservierung

Die Datenträgerreservierung bietet die Möglichkeit, Datenträgerspeicher ein Jahr im Voraus zu Rabattkonditionen zu erwerben und so die Gesamtkosten zu reduzieren. Beim Erwerb einer Datenträgerreservierung wählen Sie eine bestimmte Datenträger-SKU in einer Zielregion aus, z. B. 10 P30 (1 TiB) Premium-SSDs in der Region USA, Osten 2 für eine Laufzeit von einem Jahr. Die Reservierung folgt einem Modell, das dem für reservierte VM-Instanzen ähnelt. Sie können VM- und Datenträgerreservierungen bündeln, um die Einsparungen zu maximieren. Derzeit bietet die Azure-Datenträgerreservierung einen Ein-Jahres-Verpflichtungsplan für Premium-SSD-SKUs von P30 (1 TiB) bis P80 (32 TiB) in allen Produktregionen. Weitere Informationen zu den Preisen für reservierte Datenträger finden Sie auf der [Seite mit der Preisübersicht für Azure-Datenträger](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="next-steps"></a>Nächste Schritte

Als Einstieg sehen Sie sich den Artikel [Verwaltete Datenträger – Preise](https://azure.microsoft.com/pricing/details/managed-disks/) an.