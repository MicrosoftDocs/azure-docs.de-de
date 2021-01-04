---
title: 'Burstable-Computer der B-Serie: Azure Virtual Machines'
description: In diesem Artikel werden die Größen von Burstable Azure-VMs der B-Serie beschrieben.
services: virtual-machines
ms.subservice: sizes
author: styli365
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: sttsinar
ms.openlocfilehash: 819654ef88584cb91d6032e46256258aaed524fd
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500307"
---
# <a name="b-series-burstable-virtual-machine-sizes"></a>Größen von virtuellen Burstable-Computern der B-Serie

Virtuelle Computer der B-Serie sind ideal für Workloads, die nicht kontinuierlich die volle Leistung der CPU benötigen. Hierzu zählen beispielsweise Webserver, Proofs of Concept, kleine Datenbanken sowie Entwicklungsbuildumgebungen. Diese Workloads haben in der Regel kurzfristige Leistungsanforderungen. Mit der B-Serie können Sie eine VM-Größe mit einer Grundleistung erwerben, die Guthaben erstellen kann, wenn sie weniger als die Grundleistung beansprucht. Wenn für den virtuellen Computer Guthaben gebildet wurde, kann Leistung genutzt werden, die über die Grundleistung hinausgeht. Dabei können bis zu 100 % der vCPU verwendet werden, wenn Ihre Anwendung mehr CPU-Leistung benötigt.

Die B-Serie ist in den folgenden VM-Größen erhältlich:

[Azure-Compute-Einheit (ACU)](./acu.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): Variiert*<br>
[Storage Premium:](premium-storage-performance.md) Unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Nicht unterstützt<br>
[Livemigration:](maintenance-and-updates.md) Unterstützt<br>
[Updates mit Speicherbeibehaltung:](maintenance-and-updates.md) Unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2<br>
<br>
*VMs der B-Serie sind burstfähig, daher variieren die ACU-Nummern je nach Workloads und Kernnutzung.

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | CPU-Grundleistung des virtuellen Computers | Maximale CPU-Leistung des virtuellen Computers | Anfängliche Guthaben | Guthabenbildung/Stunde | Maximalguthaben | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBit/s | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBit/s | Maximale Anzahl NICs |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Standard_B1ls<sup>1</sup> | 1  | 0.5 | 4   | 5 %   | 100 %  | 30  | 3   | 72   | 2  | 200/10    | 160/10    | 2  |
| Standard_B1s              | 1  | 1   | 4   | 10 %  | 100 %  | 30  | 6   | 144  | 2  | 400/10    | 320/10    | 2  |
| Standard_B1ms             | 1  | 2   | 4   | 20%  | 100 %  | 30  | 12  | 288  | 2  | 800/10    | 640/10    | 2  |
| Standard_B2s              | 2  | 4   | 8   | 40%  | 200 %  | 60  | 24  | 576  | 4  | 1\.600/15   | 1\.280/15   | 3  |
| Standard_B2ms             | 2  | 8   | 16  | 60%  | 200 %  | 60  | 36  | 864  | 4  | 2\.400/22,5 | 1\.920/22,5 | 3  |
| Standard_B4ms             | 4  | 16  | 32  | 90%  | 400 %  | 120 | 54  | 1296 | 8  | 3\.600/35   | 2\.880/35   | 4  |
| Standard_B8ms             | 8  | 32  | 64  | 135% | 800 %  | 240 | 81  | 1944 | 16 | 4\.320/50   | 4\.320/50   | 4  |
| Standard_B12ms            | 12 | 48  | 96  | 202 % | 1200 % | 360 | 121 | 2909 | 16 | 6\.480/75   | 4\.320/50   | 6  |
| Standard_B16ms            | 16 | 64  | 128 | 270 % | 1600 % | 480 | 162 | 3888 | 32 | 8\.640/100  | 4\.320/50   | 8  |
| Standard_B20ms            | 20 | 80  | 160 | 337 % | 2000 % | 600 | 203 | 4860 | 32 | 10.800/125 | 4\.320/50   | 8  |

<sup>1</sup> B1ls wird nur unter Linux unterstützt

## <a name="workload-example"></a>Workloadbeispiel

Stellen Sie sich eine Zeiterfassungsanwendung für ein Büro vor. Die Anwendung benötigt eine hohe CPU-Leistung innerhalb der Geschäftszeiten, aber wenig Rechenleistung außerhalb der Geschäftszeiten. In diesem Beispiel erfordert die Workload einen virtuellen Computer mit 16 vCPUs und 64 GiB RAM, um effizient arbeiten zu können.

In der Tabelle werden die stündlichen Datenverkehrsdaten angezeigt, und das Diagramm ist eine visuelle Darstellung dieses Datenverkehrs.

B16-Merkmale:

Maximale CPU-Leistung: 16 vCPU * 100 % = 1600 %

Baseline: 270 %

![Diagramm der stündlichen Datenverkehrsdaten](./media/b-series-burstable/office-workload.png)

| Szenario | Time | CPU-Auslastung (%) | Aufgelaufenes Guthaben<sup>1</sup> | Verfügbares Guthaben |
| --- | --- | --- | --- | --- |
| B16ms-Bereitstellung | Bereitstellung | Bereitstellung  | 480 (Anfängliches Guthaben) | 480 |
| Kein Datenverkehr | 0:00 | 0 | 162 | 642 |
| Kein Datenverkehr | 1:00 | 0 | 162 | 804 |
| Kein Datenverkehr | 2:00 | 0 | 162 | 966 |
| Kein Datenverkehr | 3:00 | 0 | 162 | 1128 |
| Kein Datenverkehr | 4:00 | 0 | 162 | 1290 |
| Kein Datenverkehr | 5:00 | 0 | 162 | 1452 |
| Geringer Datenverkehr | 6:00 | 270 | 0 | 1452 |
| Mitarbeiter kommen ins Büro (App benötigt 80 % vCPU) | 7:00 | 1280 | -606 | 846 |
| Weitere Mitarbeiter kommen ins Büro (App benötigt 80 % vCPU) | 8:00 | 1280 | -606 | 240 |
| Geringer Datenverkehr | 9:00 | 270 | 0 | 240 |
| Geringer Datenverkehr | 10:00 | 100 | 102 | 342 |
| Geringer Datenverkehr | 11:00 | 50 | 132 | 474 |
| Geringer Datenverkehr | 12:00 | 100 | 102 | 576 |
| Geringer Datenverkehr | 13:00 | 100 | 102 | 678 |
| Geringer Datenverkehr | 14:00 | 50 | 132 | 810 |
| Geringer Datenverkehr | 15:00 | 100 | 102 | 912 |
| Geringer Datenverkehr | 16:00 | 100 | 102 | 1014 |
| Mitarbeiter checken aus (App-Anforderungen 100 % vCPU) | 17:00 | 1600 | -798 | 216 |
| Geringer Datenverkehr | 18:00 | 270 | 0 | 216 |
| Geringer Datenverkehr | 19:00 | 270 | 0 | 216 |
| Geringer Datenverkehr | 20:00 | 50 | 132 | 348 |
| Geringer Datenverkehr | 21:00 | 50 | 132 | 480 |
| Kein Datenverkehr | 22:00 | 0 | 162 | 642 |
| Kein Datenverkehr | 23:00 | 0 | 162 | 804 |

<sup>1</sup> Aufgelaufenes Guthaben/Guthaben, das in einer Stunde verwendet wird, entspricht: `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes`.  

Bei einer D16s_v3 mit 16 vCPUs und 64 GiB Arbeitsspeicher beträgt der Stundensatz 0,936 $ pro Stunde (monatlich 673,92 $), und für B16ms mit 16 vCPUs und 64 GiB Arbeitsspeicher beträgt die Rate 0,794 $pro Stunde (monatlich 547,86 $). <b> Dies führt zu einer Einsparung von 15 %!</b>

## <a name="q--a"></a>Fragen und Antworten

### <a name="q-what-happens-when-my-credits-run-out"></a>F: Was passiert, wenn mein Guthaben erschöpft ist?
**A:** Wenn das Guthaben erschöpft ist, wird die VM auf die Baselineleistung zurückgestuft.

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>F: Wie erhalte ich einen virtuellen Computer mit einer Grundleistung von 135 Prozent?

**A:** Die 135 Prozent verteilen sich auf die acht vCPUs, die der VM-Größe zugrunde liegen. Wenn Ihre Anwendung also beispielsweise vier der acht Kerne für die Batchverarbeitung nutzt und jede dieser vier vCPUs zu 30 Prozent ausgelastet ist, beläuft sich die CPU-Leistung des virtuellen Computers auf insgesamt 120 Prozent.  Das bedeutet, dass der virtuelle Computer ein Zeitguthaben auf der Grundlage des 15-prozentigen Deltas (im Vergleich zu Ihrer Grundleistung) bildet.  Es bedeutet aber auch Folgendes: Wenn Sie über Guthaben verfügen, kann der gleiche virtuelle Computer 100 Prozent aller acht vCPUs nutzen, wodurch sich eine maximale CPU-Leistung von 800 Prozent ergibt.

### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>F: Wie kann ich mein Guthaben und den Verbrauch überwachen?

**A:** Mit der Metrik **Credit** gibt Aufschluss darüber, wie viel Guthaben Ihr virtueller Computer verdient hat, und die Metrik **ConsumedCredit** zeigt an, wie viel CPU-Guthaben Ihr virtueller Computer aus dem Vorrat verbraucht hat.    Diese Metriken können Sie im Metrikbereich des Portals oder programmgesteuert über die Azure Monitor-APIs anzeigen.

Weitere Informationen zum Zugreifen auf die Metrikdaten für Azure finden Sie in der [Übersicht über Metriken in Microsoft Azure](../azure-monitor/platform/data-platform.md).

### <a name="q-how-are-credits-accumulated-and-consumed"></a>F: Wie wird Guthaben hinzugefügt und verbraucht?

**A:** Bildungs- und Verbrauchsrate des virtuellen Computers sind so gestaltet, dass es bei einem virtuellen Computer, der exakt auf dem Grundleistungsniveau betrieben wird, weder zu einer Nettobildung noch zu einem Verbrauch von Burst-Guthaben kommt.  Eine Nettoerhöhung des Guthabens erfolgt, wenn der virtuelle Computer unter seiner Grundleistung betrieben wird. Bei einer CPU-Nutzung, die über das Grundleistungsniveau hinausgeht, kommt es hingegen zu einer Nettoverringerung des Guthabens.

**Beispiel:** Angenommen, ich stelle für meine kleine Zeit- und Anwesenheitsdatenbank einen virtuellen Computer der Größe „B1ms“ bereit. Bei dieser Größe kann meine Anwendung bis zu 20 % einer vCPU als Grundleistung nutzen. Das entspricht 0,2 Guthaben pro Minute, die ich entweder nutzen oder sammeln kann.

Meine Anwendung ist jeweils am Anfang und Ende des Arbeitstags meiner Mitarbeiter aktiv (also zwischen 7:00 und 9:00 Uhr sowie zwischen 16:00 und 18:00 Uhr). Während der restlichen 20 Stunden des Tages befindet sich die Anwendung in der Regel im Leerlauf und nutzt lediglich zehn Prozent der vCPU. In der Nebenzeit erhalte ich pro Minute 0,2 Guthaben, verbrauche aber nur 0,1 Guthaben pro Minute. Der virtuelle Computer bildet also pro Stunde ein Guthaben von sechs Einheiten (0,1 x 60).  Für die 20 Stunden, die nicht als Spitzenzeit gelten, entsteht somit ein Guthaben von 120 Einheiten.  

Während der Spitzenzeit beansprucht meine Anwendung im Schnitt 60 % der vCPU. Ich erhalte zwar weiterhin 0,2 Guthaben pro Minute, verbrauche nun aber 0,6 Guthaben pro Minute. Die Minute kostet also netto 0,4 Guthaben. In der Stunde sind das 24 Guthaben (0,4 x 60). Die Spitzenauslastung dauert pro Tag vier Stunden und kostet somit 96 Guthaben (4 x 24).

Wenn ich nun die 120 Guthaben, die ich während der Nebenzeit gebildet habe, mit den 96 Guthaben verrechne, die für meine Spitzenzeit anfallen, bleiben täglich 24 Guthaben übrig, die ich für andere Aktivitäten mit höheren Anforderungen nutzen kann.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>F: Wie kann ich das aufgelaufene und das genutzte Guthaben berechnen?

**A:** Sie können die folgende Formel verwenden:

(Basis-CPU-Leistung der VM – CPU-Auslastung)/100 = Erworbenes Guthaben oder Nutzung pro Minute

In der obigen Instanz ist Ihre Baseline z. B. 20 %. Wenn Sie 10 % der CPU nutzen, ergibt sich ein Guthaben von (20 % – 10 %)/100 = 0,1 pro Minute.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>F: Unterstützt die B-Serie Storage Premium-Datenträger?

**A:** Ja. Storage Premium-Datenträger werden von allen Größen der B-Serie unterstützt.

### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>F: Warum wird mein Restguthaben nach dem erneuten Bereitstellen oder Beenden bzw. Starten auf 0 gesetzt?

**A:** Wenn eine VM „ERNEUT BEREITGESTELLT“ ist und die VM auf einen anderen Knoten verschoben wird, geht das gesammelte Guthaben verloren. Wenn die VM beendet/gestartet wird, aber auf dem gleichen Knoten verbleibt, behält die VM das gesammelte Guthaben. Immer wenn die VM auf einem Knoten neu startet, erhält sie ein Anfangsguthaben. Bei Standard_B8ms beträgt dieser Wert 240.

### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>F: Was geschieht, wenn ein nicht unterstütztes Betriebssystemimage auf B1ls bereitgestellt wird?

**A**: B1ls unterstützt nur Linux-Images, und wenn Sie ein Image eines anderen Betriebssystems bereitstellen, erhalten Sie möglicherweise keine optimale Benutzererfahrung.

## <a name="other-sizes-and-information"></a>Weitere Größen und Informationen

- [Allgemeiner Zweck](sizes-general.md)
- [Computeoptimiert](sizes-compute.md)
- [Arbeitsspeicheroptimiert](sizes-memory.md)
- [Speicheroptimiert](sizes-storage.md)
- [GPU-optimiert](sizes-gpu.md)
- [High Performance Computing](sizes-hpc.md)

Preisrechner: [Preisrechner](https://azure.microsoft.com/pricing/calculator/)

Weitere Informationen zu Datenträgertypen: [Datenträgertypen](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen dazu, wie Sie mit [Azure-Computeeinheiten (ACU)](acu.md) die Computeleistung von Azure-SKUs vergleichen können.
