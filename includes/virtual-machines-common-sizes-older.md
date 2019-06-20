---
title: include file
description: include file
services: virtual-machines-windows, virtual-machines-linux
author: laurenhughes
ms.service: multiple
ms.topic: include
ms.date: 04/11/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 5c35cbfbd2e9d0a1655d05c1116d293fb78c9eb7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133457"
---
Dieser Abschnitt enthält Informationen zu den älteren Generationen von Größen für virtuelle Computer. Diese Größen werden weiterhin unterstützt, erhalten jedoch keine zusätzliche Kapazität. Es gibt neuere oder alternative Größen, die allgemein verfügbar sind. Wählen Sie anhand des Artikels [Größen für virtuelle Windows-Computer in Azure](../articles/virtual-machines/windows/sizes.md) oder [Größen für virtuelle Linux-Computer in Azure](../articles/virtual-machines/linux/sizes.md) die Größen für virtuelle Computer, die am besten zu Ihren Anforderungen passen.  

Weitere Informationen zum Ändern der Größe eines virtuellen Linux-Computers finden Sie unter [Ändern der Größe eines virtuellen Linux-Computers über die Azure CLI](../articles/virtual-machines/linux/change-vm-size.md). Wenn Sie mit virtuellen Windows-Computern arbeiten und PowerShell verwenden möchten, lesen Sie [Ändern der Größe eines virtuellen Windows-Computers](../articles/virtual-machines/windows/resize-vm.md).  

<br>

### <a name="basic-a"></a>Basic A  

**Neuere Größenempfehlung**: [Av2-Serie](../articles/virtual-machines/windows/sizes-general.md#av2-series)

Storage Premium  Nicht unterstützt

Storage Premium-Zwischenspeicherung:  Nicht unterstützt

Die Größen des Basic-Tarifs sind hauptsächlich für Entwicklungsworkloads und andere Anwendungen bestimmt, für die kein Lastenausgleich, keine automatische Skalierung und keine speicherintensiven virtuellen Computer erforderlich sind.

|Größe – Größe\Name | vCPU |Arbeitsspeicher|Netzwerkkarten (max.)|Max. Größe der temporären Datenträger |Maximal Datenträger (jeweils 1023 GB)|Maximal IOPS (300 pro Datenträger)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20 GB|1|1 x 300|
|A1\Basic_A1|1|1,75 GB|2| 40 GB |2|2 x 300|
|A2\Basic_A2|2|3,5 GB|2| 60 GB|4|4 x 300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|4 x 300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16 x 300|

<br>

### <a name="a-series"></a>A-Serie  

**Neuere Größenempfehlung**: [Av2-Serie](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 50–100

Storage Premium  Nicht unterstützt

Storage Premium-Zwischenspeicherung:  Nicht unterstützt

| Größe | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (HDD): GiB | Max. Anzahl Datenträger | Max. Datenträgerdurchsatz: IOPS | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBps)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> |1 |0,768 |20 |1 |1 x 500 |2/100 |
| Standard_A1 |1 |1,75 |70 |2 |2 x 500 |2/500  |
| Standard_A2 |2 |3,5 |135 |4 |4 x 500 |2/500 |
| Standard_A3 |4 |7 |285 |8 |8 x 500 |2/1000 |
| Standard_A4 |8 |14 |605 |16 |16 x 500 |4/2000 |
| Standard_A5 |2 |14 |135 |4 |4 x 500 |2/500 |
| Standard_A6 |4 |28 |285 |8 |8 x 500 |2/1000 |
| Standard_A7 |8 |56 |605 |16 |16 x 500 |4/2000 |

<sup>1</sup> Die Größe „A0“ ist auf der physischen Hardware „überzeichnet“. Nur für diese spezielle Größe kann es dazu kommen, dass sich andere Kundenbereitstellungen negativ auf die Leistung Ihrer aktiven Workload auswirken. Die relative Leistung ist unten als erwartete Baseline beschrieben und unterliegt einer ungefähren Variabilität von 15 Prozent.

<br>

### <a name="a-series---compute-intensive-instances"></a>A-Serie: Rechenintensive Instanzen  

**Neuere Größenempfehlung**: [Av2-Serie](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 225

Storage Premium  Nicht unterstützt

Storage Premium-Zwischenspeicherung:  Nicht unterstützt

Die Größen A8 bis A11 und die Größen der H-Reihe werden auch als *rechenintensive Instanzen* bezeichnet. Die Hardware, auf der diese Größen ausgeführt werden, wurde für rechenintensive Anwendungen mit hoher Netzwerkauslastung konzipiert und optimiert. Hierzu zählen beispielsweise HPC-Clusteranwendungen (High Performance Computing), Modellierung und Simulationen. Die Reihen A8 bis A11 nutzen Intel Xeon E5-2670 mit 2,6 GHZ und die H-Reihe Intel Xeon E5-2667 v3 mit 3,2 GHz.  

| Größe | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (HDD): GiB | Max. Anzahl Datenträger | Max. Datenträgerdurchsatz: IOPS | Maximale Anzahl NICs|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8&nbsp;<sup>1</sup> |8 |56 |382 |32 |32 x 500 |2 |
| Standard_A9&nbsp;<sup>1</sup> |16 |112 |382 |64 |64 x 500 |4 |
| Standard_A10 |8 |56 |382 |32 |32 x 500 |2  |
| Standard_A11 |16 |112 |382 |64 |64 x 500 |4 |

<sup>1</sup> Für MPI-Anwendungen ist ein dediziertes RDMA-Back-End-Netzwerk durch ein FDR InfiniBand-Netzwerk aktiviert, das eine äußerst geringe Latenz und eine hohe Bandbreite ermöglicht.  

<br>

### <a name="d-series"></a>D-Serie  

**Neuere Größenempfehlung**: [Dv3-Serie](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Storage Premium  Nicht unterstützt

Storage Premium-Zwischenspeicherung:  Nicht unterstützt

| Größe         | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Maximaler Durchsatz (temporärer Speicher): IOPS/MBit/s Lesen/MBps Schreiben | Max. Datenträger/Durchsatz: IOPS | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3,5         | 50             | 3000/46/23                                           | 4/4 x 500                         | 2/500                 |
| Standard_D2  | 2         | 7           | 100            | 6000/93/46                                           | 8/8 x 500                         | 2/1000                     |
| Standard_D3  | 4         | 14          | 200            | 12000/187/93                                         | 16/16 x 500                         | 4/2000                     |
| Standard_D4  | 8         | 28          | 400            | 24000/375/187                                        | 32/32 x 500                       | 8/4000                     |

<sup>1</sup> Die VM-Familie kann auf einer der folgenden CPUs ausgeführt werden: Intel Xeon® E5-2660 v2 mit 2,2 GHz, Intel Xeon® E5-2673 v3 mit 2,4 GHz (Haswell) oder Intel XEON® E5-2673 v4 mit 2,3 GHz (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>D-Serie – arbeitsspeicheroptimiert  

**Neuere Größenempfehlung**: [Dv3-Serie](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Storage Premium  Nicht unterstützt

Storage Premium-Zwischenspeicherung:  Nicht unterstützt

| Größe         | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Maximaler Durchsatz (temporärer Speicher): IOPS/MBit/s Lesen/MBps Schreiben | Max. Datenträger/Durchsatz: IOPS | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000/93/46                                           | 8/8 x 500                         | 2/1000                     |
| Standard_D12 | 4         | 28          | 200            | 12000/187/93                                         | 16/16 x 500                         | 4/2000                     |
| Standard_D13 | 8         | 56          | 400            | 24000/375/187                                        | 32/32 x 500                       | 8/4000                     |
| Standard_D14 | 16        | 112         | 800            | 48000/750/375                                        | 64/64 x 500                       | 8 / 8000                |

<sup>1</sup> Die VM-Familie kann auf einer der folgenden CPUs ausgeführt werden: Intel Xeon® E5-2660 v2 mit 2,2 GHz, Intel Xeon® E5-2673 v3 mit 2,4 GHz (Haswell) oder Intel XEON® E5-2673 v4 mit 2,3 GHz (Broadwell)  

<br>

### <a name="ds-series"></a>DS-Serie  

**Neuere Größenempfehlung**: [DSv3-Serie](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1</sup>

Storage Premium  Unterstützt

Storage Premium-Zwischenspeicherung:  Unterstützt

| Größe | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBps (Cachegröße in GiB) | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBps | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3,5 |7 |4 |4\.000/32 (43) |3\.200/32 |2/500 |
| Standard_DS2 |2 |7 |14 |8 |8\.000/64 (86) |6\.400/64 |2/1000 |
| Standard_DS3 |4 |14 |28 |16 |16\.000/128 (172) |12\.800/128 |4/2000 |
| Standard_DS4 |8 |28 |56 |32 |32\.000/256 (344) |25\.600/256 |8/4000 |

<sup>1</sup> Die VM-Familie kann auf einer der folgenden CPUs ausgeführt werden: Intel Xeon® E5-2660 v2 mit 2,2 GHz, Intel Xeon® E5-2673 v3 mit 2,4 GHz (Haswell) oder Intel XEON® E5-2673 v4 mit 2,3 GHz (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>DS-Serie – arbeitsspeicheroptimiert  

**Neuere Größenempfehlung**: [DSv3-Serie](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1,2</sup>

Storage Premium  Unterstützt

Storage Premium-Zwischenspeicherung:  Unterstützt

| Größe | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBps (Cachegröße in GiB) | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBps | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8\.000/64 (72) |6\.400/64 |2/1000 |
| Standard_DS12 |4 |28 |56 |16 |16\.000/128 (144) |12\.800/128 |4/2000 |
| Standard_DS13 |8 |56 |112 |32 |32\.000/256 (288) |25\.600/256 |8/4000 |
| Standard_DS14 |16 |112 |224 |64 |64\.000/512 (576) |51\.200/512 |8 / 8000 |

<sup>1</sup> Der mit einer VM der DS-Serie maximal mögliche Datenträgerdurchsatz (IOPS oder MB/s) kann durch Anzahl, Größe und Striping der angefügten Datenträger beschränkt werden.  Details finden Sie unter [Entwerfen für hohe Leistung](../articles/virtual-machines/windows/premium-storage-performance.md).   
<sup>2</sup> Die VM-Familie kann auf einer der folgenden CPUs ausgeführt werden: Intel Xeon® E5-2660 v2 mit 2,2 GHz, Intel Xeon® E5-2673 v3 mit 2,4 GHz (Haswell) oder Intel XEON® E5-2673 v4 mit 2,3 GHz (Broadwell)  

<br>

### <a name="gs-series"></a>GS-Serie 

ACU: 180–240 <sup>1</sup>

Storage Premium  Unterstützt

Storage Premium-Zwischenspeicherung:  Unterstützt

| Größe | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBps (Cachegröße in GiB) | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBps | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 |2 |28 |56 |8 |10\.000/100 (264) |5\.000 / 125 |2/2000 |
| Standard_GS2 |4 |56 |112 |16 |20\.000/200 (528) |10\.000/250 |2/4000 |
| Standard_GS3 |8 |112 |224 |32 |40\.000/400 (1.056) |20\.000/500 |4/8000 |
| Standard_GS4&nbsp;<sup>3</sup> |16 |224 |448 |64 |80\.000/800 (2.112) |40\.000/1.000 |8/16000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> |32 |448 |896 |64 |160\.000/1.600 (4.224) |80\.000/2.000 |8/20000 |

<sup>1</sup> Der mit einer VM der GS-Serie maximal mögliche Datenträgerdurchsatz (IOPS oder MB/s) kann durch Anzahl, Größe und Striping der angefügten Datenträger beschränkt werden. Details finden Sie unter [Entwerfen für hohe Leistung](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>2</sup> Instanz wird isoliert auf dedizierter Hardware ausgeführt, die für einen einzigen Kunden bereitgestellt wird.

<sup>3</sup> Eingeschränkte Kerngrößen verfügbar.

<br>

### <a name="g-series"></a>G-Serie

ACU: 180–240

Storage Premium  Nicht unterstützt

Storage Premium-Zwischenspeicherung:  Nicht unterstützt

| Größe         | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Maximaler Durchsatz (temporärer Speicher): IOPS/MBit/s Lesen/MBps Schreiben | Max. Datenträger/Durchsatz: IOPS | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000/93/46                                           | 8/8 x 500                       | 2/2000                     |
| Standard_G2  | 4         | 56          | 768            | 12000/187/93                                         | 16/16 x 500                       | 2/4000                     |
| Standard_G3  | 8         | 112         | 1\.536          | 24000/375/187                                        | 32/32 x 500                     | 4/8000                |
| Standard_G4  | 16        | 224         | 3\.072          | 48000/750/375                                        | 64/64 x 500                     | 8/16000          |
| Standard_G5&nbsp;<sup>1</sup> | 32        | 448         | 6\.144          | 96000/1500/750                                       | 64/64 x 500                     | 8/20000           |

<sup>1</sup> Instanz wird isoliert auf dedizierter Hardware ausgeführt, die für einen einzigen Kunden bereitgestellt wird.
<br>
