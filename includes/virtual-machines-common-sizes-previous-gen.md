---
title: include file
description: include file
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 05/16/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 464c7bcb510a2f6ab80fb11d722c241ec51a1b16
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66391434"
---
Dieser Abschnitt enthält Informationen zu den vorherigen Generationen von VM-Größen. Diese Größen können weiterhin verwendet werden, es sind jedoch neuere Generationen verfügbar. 

## <a name="f-series"></a>F-Serie

Die F-Serie basiert auf dem Intel Xeon® E5-2673 v3-Prozessor (Haswell) mit 2,4 GHz, der mit der Intel Turbo Boost Technology 2.0 Taktfrequenzen von 3,1 GHz erreichen kann. Dies ist die gleiche CPU-Leistung wie bei virtuellen Computern (VMs) der Dv2-Serie.  

Virtuelle Computer der F-Serie sind eine hervorragende Wahl für Workloads, die schnellere CPUs erfordern, aber nicht so viel Arbeitsspeicher oder temporären Speicher pro vCPU benötigen.  Bei Arbeitslasten wie Analysen, Gamingservern, Webservern und Batchverarbeitung kommen die Vorteile und der Nutzen der F-Serie besonders gut zum Tragen.

ACU: 210–250

Storage Premium  Nicht unterstützt

Storage Premium-Zwischenspeicherung:  Nicht unterstützt

| Größe         | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Maximaler Durchsatz (temporärer Speicher): IOPS/MBit/s Lesen/MBps Schreiben | Max. Datenträger/Durchsatz: IOPS | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000/46/23                                           | 4/4 x 500                         | 2/750                 |
| Standard_F2  | 2         | 4           | 32             | 6000/93/46                                           | 8/8 x 500                         | 2/1500                     |
| Standard_F4  | 4         | 8           | 64             | 12000/187/93                                         | 16/16 x 500                         | 4/3000                     |
| Standard_F8  | 8         | 16          | 128            | 24000/375/187                                        | 32/32 x 500                       | 8/6000                     |
| Standard_F16 | 16        | 32          | 256            | 48000/750/375                                        | 64/64 x 500                       | 8 / 12000           |

## <a name="fs-series-sup1sup"></a>Fs-Serie <sup>1</sup>

Die Fs-Serie verfügt zusätzlich zum Premium-Speicher über alle Vorteile der F-Serie.

ACU: 210–250

Storage Premium  Unterstützt

Storage Premium-Zwischenspeicherung:  Unterstützt

| Größe | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBps (Cachegröße in GiB) | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBps | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4000/32 (12) |3200/48 |2/750 |
| Standard_F2s |2 |4 |8 |8 |8000/64 (24) |6400/96 |2/1500 |
| Standard_F4s |4 |8 |16 |16 |16000/128 (48) |12800/192 |4/3000 |
| Standard_F8s |8 |16 |32 |32 |32000/256 (96) |25600/384 |8/6000 |
| Standard_F16s |16 |32 |64 |64 |64000/512 (192) |51200/768 |8 / 12000 |

MB/s = 10^6 Bytes pro Sekunde und GB = 1.024^3 Bytes.

<sup>1</sup> Der mit einer VM der Fs-Serie maximal mögliche Datenträgerdurchsatz (IOPS oder MB/s) kann durch Anzahl, Größe und Striping der angefügten Datenträger beschränkt werden.  Details finden Sie unter [Entwerfen für hohe Leistung](../articles/virtual-machines/windows/premium-storage-performance.md).  

## <a name="ls-series"></a>Ls-Serie

Die Ls-Reihe bietet bis zu 32 vCPUs aus der [Intel® Xeon® E5 v3-Prozessorfamilie](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Die Ls-Serie erreicht die gleiche CPU-Leistung wie die G/GS-Serie und bietet 8 GiB Arbeitsspeicher pro vCPU.

Die Ls-Serie unterstützt nicht die Erstellung eines lokalen Caches, um die von dauerhaften Datenträgern erreichbaren IOPS zu erhöhen. Dank des hohen Durchsatzes und der IOPS des lokalen Datenträgers eignen sich VMs der Ls-Reihe ideal für NoSQL-Speicher wie Apache Cassandra und MongoDB, die Daten über mehrere virtuelle Computer replizieren, um Persistenz im Falle eines Ausfalls eines einzelnen virtuellen Computers zu erzielen.

ACU: 180–240

Storage Premium  Unterstützt

Storage Premium-Zwischenspeicherung:  Nicht unterstützt
 
| Größe          | vCPU | Arbeitsspeicher (GiB) | Temporärer Speicher (GiB) | Max. Anzahl Datenträger | Maximaler Durchsatz temporärer Speicher (IOPS/MB/s) | Maximaler Datenträgerdurchsatz ohne Cache: (IOPS/MB/s) | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20000/200 | 5000/125  | 2/4000  | 
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4/8000  | 
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8/16000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5\.630 | 64   | 160000/1600   | 40000/1000     | 8/20000 | 

Der mit einem virtuellen Computer der Ls-Serie maximal mögliche Datenträgerdurchsatz kann durch Anzahl, Größe und Striping angefügter Datenträger beschränkt sein. Details finden Sie unter [Entwerfen für hohe Leistung](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>1</sup> Instanz wird isoliert auf dedizierter Hardware ausgeführt, die für einen einzigen Kunden bereitgestellt wird.

## <a name="nvv2-series-preview"></a>NVv2-Serie (Preview)

**Neuere Größenempfehlung**: [NVv3-Serie (Vorschau)](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nvv2-series-preview)

Die virtuellen Computer der NVv2-Serie verfügen über GPUs vom Typ [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) sowie NVIDIA GRID-Technologie mit Intel Broadwell CPUs. Diese virtuellen Computer wurden für GPU-beschleunigte Grafikanwendungen und virtuelle Desktops entwickelt, um Kunden die Datenvisualisierung, Ergebnissimulation, CAD oder das Rendering und Streaming von Inhalten zu erleichtern. Außerdem können diese virtuellen Computer Workloads mit einfacher Genauigkeit wie Codierung und Rendering ausführen. Virtuelle Computer der NVv2-Serie unterstützen Storage Premium und verfügen im Vergleich zur NV-Vorgängerserie über doppelt so viel Systemspeicher (RAM).  

Alle GPUs in NVv2-Instanzen beinhalten eine GRID-Lizenz. Diese Lizenz bietet Ihnen die erforderliche Flexibilität für die Verwendung einer NV-Instanz als virtuelle Arbeitsstation für einen einzelnen Benutzer. Außerdem besteht für ein Szenario mit einer virtuellen Anwendung die Möglichkeit, dass 25 Benutzer gleichzeitig eine Verbindung mit dem virtuellen Computer herstellen.

| Größe | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | GPU | GPU-Arbeitsspeicher: GiB | Max. Anzahl Datenträger | Maximale Anzahl NICs | Virtuelle Arbeitsstationen | Virtuelle Anwendungen | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6 |112 |320 | 1 | 8 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 16 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1280 | 4 | 32 | 32 | 8 | 4 | 100 |

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard_A0 – A4 mit Befehlszeilenschnittstelle (CLI) und PowerShell

Im klassischen Bereitstellungsmodell unterscheiden sich einige VM-Größennamen in der Befehlszeilenschnittstelle und in PowerShell:

* Standard_A0 ist „Sehr klein“
* Standard_A1 ist „Klein“
* Standard_A2 ist „Mittel“
* Standard_A3 ist „Groß“
* Standard_A4 ist „Extragroß“
