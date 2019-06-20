---
title: include file
description: include file
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 10/08/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 98e0732ece3d046b7c073ac075b7eae89d36c125
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66391346"
---
Universelle VM-Größen zeichnen sich durch ein ausgewogenes Verhältnis zwischen CPU und Arbeitsspeicher aus. Ideal für Tests und Entwicklung, kleine bis mittlere Datenbanken sowie Webserver mit geringer bis mittlerer Auslastung. Dieser Artikel enthält Informationen zur Anzahl von vCPUs, Datenträgern und NICs sowie zum Speicherdurchsatz für die Größen in dieser Gruppe. 

- Die [DC-Serie](#dc-series) ist eine neue Familie virtueller Computer in Azure, beim Schutz der Vertraulichkeit und Integrität Ihrer Daten und Codes helfen kann, wenn diese in der Public Cloud verarbeitet werden. Diese Computer werden von der neuesten Generation von 3,7 GHz Intel XEON E-2176G-Prozessoren mit SGX-Technologie unterstützt. Mit der Intel Turbo Boost Technology können diese Computer bis zu 4,7 GHz erreichen. Instanzen der DC-Serie ermöglichen es Kunden, Secure Enclave-basierte Anwendungen zu erstellen, um ihren Code und Daten zu schützen, während sie verwendet werden.

- Die VMs der Av2-Reihe können auf vielen verschiedenen Hardwaretypen und Prozessoren bereitgestellt werden. Die Konfigurationen für CPU-Leistung und Arbeitsspeicher bei virtuellen Computern der A-Serie eignen sich am besten für Workloads wie Entwicklung und Tests. Die Größe ist basierend auf der Hardware gedrosselt, um eine konsistente Prozessorleistung für die ausgeführte Instanz zu ermöglichen – unabhängig von der Hardware, die für die Bereitstellung gewählt wird. Fragen Sie die virtuelle Hardware über die virtuelle Maschine ab, um die physische Hardware zu ermitteln, auf der diese Größe bereitgestellt wird.

  Mögliche Anwendungsfälle: Entwicklungs- und Testserver, Webserver mit geringem Datenverkehr, kleine bis mittelgroße Datenbanken, Proof of Concept und Coderepositorys.

- Die Dv2-Serie, eine Nachfolgerin der ursprünglichen D-Serie, ist mit schnellen CPUs und einer optimalen CPU-zu-Arbeitsspeicher-Konfiguration ausgestattet und eignet sich daher für die meisten Produktionsworkloads. Die CPU der Dv2-Serie ist ca. 35 % schneller als die CPU der D-Serie. Sie basiert auf der neuesten Generation des 2,4-GHz-Intel Xeon® E5-2673-v3-Prozessors (Haswell) oder 2,3-GHz-E5 2673-v4-Prozessors (Broadwell) und kann mit der Intel Turbo Boost Technology 2.0 bis auf 3,1 GHz erhöht werden. Der Dv2-Serie hat die gleichen Arbeitsspeicher- und Datenträgerkonfigurationen wie die D-Serie.

- Die Dv3-Serie bietet den 2,4-GHz-Intel Xeon® E5-2673-v3-Prozessor (Haswell) oder den aktuellen 2,3-GHz Intel XEON® E5-2673-v4-Prozessor (Broadwell) in einer Hyperthreadkonfiguration und somit ein besseres Preis-Leistungs-Verhältnis für die meisten universellen Workloads.  Der Speicher wurde erweitert (von etwa 3.5 GiB/vCPU auf 4 GiB/vCPU), während die Datenträger- und Netzwerkgrenzwerte pro Kern angepasst wurden, um sich für den Übergang zum Hyperthreading anzupassen.  Die Dv3-Serie hat nicht mehr die hohen Arbeitsspeichergrößen der D/Dv2-Familien, diese sind nun in der neuen Ev3-Familie verfügbar.

  Zu den möglichen Anwendungsfällen der D-Serie zählen Unternehmensanwendungen, relationale Datenbanken, In-Memory-Caching und Analysen. 
  
## <a name="b-series"></a>B-Serie

Storage Premium  Unterstützt

Storage Premium-Zwischenspeicherung:  Nicht unterstützt

Virtuelle Burst-fähige Computer der B-Serie sind ideal für Workloads geeignet, die nicht kontinuierlich die volle Leistung der CPU benötigen. Hierzu zählen beispielsweise Webserver, kleine Datenbanken sowie Entwicklungs- und Testumgebungen. Diese Workloads haben in der Regel kurzfristige Leistungsanforderungen. Mit der B-Serie können diese Kunden eine VM-Größe mit einer preisgünstigen Grundleistung erwerben. Für die VM-Instanz können dann Gutschriften erlangt werden, wenn für die VM weniger als die Grundleistung genutzt wird. Wenn für den virtuellen Computer Guthaben gebildet wurde, kann Leistung genutzt werden, die über die Grundleistung der VM hinausgeht. Dabei kann die CPU zu 100 Prozent verwendet werden, wenn Ihre Anwendung eine solche Leistung benötigt.

Zu den möglichen Anwendungsfällen zählen Entwicklungs- und Testserver, Webserver mit geringem Datenverkehr, kleine Datenbanken, Microservices, Proof of Concept-Server und Buildserver.


| Größe             | vCPU  | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | CPU-Grundleistung des virtuellen Computers | Maximale CPU-Leistung des virtuellen Computers | Anfängliche Guthaben | Guthabenbildung/Stunde | Maximalguthaben | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBps | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBps | Maximale Anzahl NICs |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5 %                   | 100 %                   | 30                   | 3                  | 72            | 2                                      | 200/10                                  | 160/10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 100 %                   | 30                   | 6                  | 144            | 2                        | 400/10                                  | 320/10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 100 %                   | 30                   | 12                 | 288           | 2                         | 800/10                                  | 640/10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200 %                   | 60                   | 24                 | 576            | 4                                      | 1600/15                                 | 1280/15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60 %                   | 200 %                   | 60                   | 36                 | 864            | 4                                      | 2400/22,5                               | 1920/22,5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400 %                   | 120                   | 54                 | 1296           | 8                                      | 3600/35                                 | 2880/35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800 %                   | 240                   | 81                 | 1944           | 16                                     | 4320/50                                 | 4320/50                                 | 4  |

<sup>1</sup> B1ls wird nur unter Linux unterstützt

## <a name="dsv3-series-sup1sup"></a>Dsv3-Serie <sup>1</sup>

ACU: 160–190

Storage Premium  Unterstützt

Storage Premium-Zwischenspeicherung:  Unterstützt

Die Größen der Dsv3-Serie basieren auf dem Prozessor vom Typ 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) oder vom Typ 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell) und können mit Intel Turbo Boost Technology 2.0 3,5 GHz erreichen und Premium-Speicher nutzen. Die Größen der Dsv3-Serie bieten eine Kombination aus vCPU, Arbeitsspeicher und temporärem Speicher für die meisten Produktionsworkloads.


| Größe             | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBps (Cachegröße in GiB) | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBps | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_D2s_v3  | 2      | 8           | 16             | 4              | 4000/32 (50)                                                       | 3200/48                                | 2/1000                                   |
| Standard_D4s_v3  | 4      | 16          | 32             | 8              | 8000/64 (100)                                                      | 6400/96                                | 2/2000                                   |
| Standard_D8s_v3  | 8      | 32          | 64             | 16             | 16000/128 (200)                                                    | 12800/192                              | 4/4000                                      |
| Standard_D16s_v3 | 16     | 64          | 128            | 32             | 32000/256 (400)                                                    | 25600/384                              | 8 / 8000                                      |
| Standard_D32s_v3 | 32     | 128          | 256            | 32             | 64000/512 (800)                                                    | 51200/768                              | 8/16000                                               |
| Standard_D64s_v3 | 64     | 256          | 512            | 32             | 128000/1024 (1600)                                                    | 80000/1200                              | 8 / 30000                                               |

<sup>1</sup> Virtuelle Computer der Dsv3-Serie verfügen über Hyper-Threading-Technologie von Intel®

## <a name="dv3-series-sup1sup"></a>Dv3-Serie <sup>1</sup>

ACU: 160–190

Storage Premium  Nicht unterstützt

Storage Premium-Zwischenspeicherung:  Nicht unterstützt

Die Größen der Dv3-Serie basieren auf dem Prozessor vom Typ 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) oder vom Typ 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell) und können mit Intel Turbo Boost Technology 2.0 3,5 GHz erreichen. Die Größen der Dv3-Serie bieten eine Kombination aus vCPU, Arbeitsspeicher und temporärem Speicher für die meisten Produktionsworkloads.

Datenträgerspeicher wird separat zu virtuellen Computern abgerechnet. Verwenden Sie die Dsv3-Größen, um Datenträger mit Premium-Speicher zu nutzen. Die Preis- und Abrechnungskennzahlen für die Dsv3-Größen entsprechen denen der Dv3-Serie. 


| Größe            | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (temporärer Speicher): IOPS/MBit/s Lesen/MBit/s Schreiben | Maximale Anzahl NICs/Netzwerkbandbreite |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_D2_v3  | 2         | 8           | 50             | 4              | 3000/46/23                                               | 2/1000                    |
| Standard_D4_v3  | 4         | 16          | 100            | 8              | 6000/93/46                                               | 2/2000                    |
| Standard_D8_v3  | 8         | 32          | 200            | 16             | 12000/187/93                                             | 4/4000                    |
| Standard_D16_v3 | 16        | 64          | 400            | 32             | 24000/375/187                                            | 8 / 8000                    |
| Standard_D32_v3 | 32        | 128         | 800            | 32             | 48000/750/375                                            | 8/16000                   |
| Standard_D64_v3 | 64        | 256         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000                   |

<sup>1</sup> Virtuelle Computer der Dv3-Serie verfügen über Hyper-Threading-Technologie von Intel®

## <a name="dsv2-series"></a>DSv2-Serie

ACU: 210–250

Storage Premium  Unterstützt

Storage Premium-Zwischenspeicherung:  Unterstützt

| Größe | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBps (Cachegröße in GiB) | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBps | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3,5 |7 |4 |4000/32 (43) |3200/48 |2/750 |
| Standard_DS2_v2 |2 |7 |14 |8 |8000/64 (86) |6400/96 |2/1500 |
| Standard_DS3_v2 |4 |14 |28 |16 |16000/128 (172) |12800/192 |4/3000 |
| Standard_DS4_v2 |8 |28 |56 |32 |32000/256 (344) |25600/384 |8/6000 |
| Standard_DS5_v2 |16 |56 |112 |64 |64000/512 (688) |51200/768 |8 / 12000 |

## <a name="dv2-series"></a>Dv2-Serie

ACU: 210–250

Storage Premium  Nicht unterstützt

Storage Premium-Zwischenspeicherung:  Nicht unterstützt

| Größe           | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Maximaler Durchsatz (temporärer Speicher): IOPS/MBit/s Lesen/MBps Schreiben | Max. Anzahl Datenträger | Durchsatz: IOPS | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBps) |
|----------------|------|-------------|------------------------|------------------------------------------------------------|----------------|------------------|----------------------------------------------|
| Standard_D1_v2 | 1    | 3,5         | 50                     | 3000/46/23                                             | 4              | 4 x 500            | 2/750                                      |
| Standard_D2_v2 | 2    | 7           | 100                    | 6000/93/46                                             | 8              | 8 x 500            | 2/1500                                     |
| Standard_D3_v2 | 4    | 14          | 200                    | 12000/187/93                                           | 16             | 16 x 500           | 4/3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000/375/187                                          | 32             | 32 x 500           | 8/6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000/750/375                                          | 64             | 64 x 500           | 8 / 12000                                    |

## <a name="av2-series"></a>Av2-Serie

ACU: 100

Storage Premium  Nicht unterstützt

Storage Premium-Zwischenspeicherung:  Nicht unterstützt


| Größe            | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Maximaler Durchsatz (temporärer Speicher): IOPS/MBit/s Lesen/MBps Schreiben | Max. Datenträger/Durchsatz: IOPS | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBps) | 
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000/20/10                                           | 2/2 x 500               | 2/250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000/40/20                                           | 4/4 x 500               | 2/500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000/80/40                                           | 8/8 x 500               | 4/1000                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000/160/80                                          | 16/16 x 500             | 8/2000                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2000/40/20                                           | 4/4 x 500               | 2/500                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000/80/40                                           | 8/8 x 500               | 4/1000                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000/160/80                                          | 16/16 x 500             | 8/2000                     |

## <a name="dc-series"></a>DC-Serie

Storage Premium Unterstützt

Storage Premium-Zwischenspeicherung: Unterstützt



| Größe          | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBps (Cachegröße in GiB) | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBps | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                          | 3200/48                                  | 2/1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                          | 6400/96                                  | 2/3000                                     |