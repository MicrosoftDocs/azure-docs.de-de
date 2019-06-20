---
title: include file
description: include file
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/16/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 499b5ac77728bc14aadd5a2ef235e28b6ca83516
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133404"
---
Arbeitsspeicheroptimierte VM-Größen bieten ein hohes Arbeitsspeicher-zu-CPU-Verhältnis und eignen sich hervorragend für relationale Datenbankserver, mittelgroße bis große Caches und In-Memory-Analysen. Dieser Artikel enthält Informationen zur Anzahl von vCPUs, Datenträgern und NICs sowie zum Speicherdurchsatz und zur Netzwerkbandbreite der einzelnen Größen in dieser Gruppe. 

* Die Mv2-Serie bietet die höchste vCPU-Anzahl (bis zu 208 vCPUs) und den größten Arbeitsspeicher (bis zu 5,7 TiB) für die virtuellen Computer in der Cloud. Dies ist ideal für extrem große Datenbanken oder andere Anwendungen, für die eine hohe vCPU-Anzahl und große Mengen an Arbeitsspeicher benötigt werden.
 
* Die M-Serie verfügt über eine hohe vCPU-Anzahl (bis zu 128 vCPUs) und eine große Menge von Arbeitsspeicher (bis zu 3,8 TiB). Dies ist auch ideal für extrem große Datenbanken oder andere Anwendungen, für die eine hohe vCPU-Anzahl und große Mengen an Arbeitsspeicher benötigt werden.

* Die Serien Dv2, G und deren DSv2/GS-Entsprechungen eignen sich ideal für Clientanwendungen, die schnellere vCPUs und eine höhere Leistung des temporären Speichers erfordern oder einen höheren Arbeitsspeicherbedarf haben. Sie bieten eine leistungsfähige Kombination für viele Anwendungen für den Unternehmenseinsatz.

* Die Dv2-Serie, eine Nachfolgerin der ursprünglichen D-Serie, hat eine leistungsfähigere CPU. Die CPU der Dv2-Serie ist ca. 35 % schneller als die CPU der D-Serie. Sie basiert auf der neuesten Generation des 2,4-GHz-Intel Xeon® E5-2673 v3-Prozessors (Haswell) oder 2,3-GHz-E5-2673 v4-Prozessors (Broadwell) und kann mit der Intel Turbo Boost Technology 2.0 bis auf 3,1 GHz erhöht werden. Der Dv2-Serie hat die gleichen Arbeitsspeicher- und Datenträgerkonfigurationen wie die D-Serie.

* Die Ev3-Serie bietet den 2,3-GHz-E5-2673 v4-Prozessor (Broadwell) in einer Hyperthreadkonfiguration und somit ein besseres Preis-Leistungs-Verhältnis für die meisten universellen Workloads, und sie bringt den Ev3 mit den allgemeinen VMs der meisten anderen Clouds in Einklang.  Der Speicher wurde erweitert (von 7 GiB/vCPU auf 8 GiB/vCPU), während die Datenträger- und Netzwerkgrenzwerte pro Kern angepasst wurden, um sich für den Übergang zum Hyperthreading anzupassen.  Die Ev3-Serie ist der Nachfolger für die VMs mit großen Arbeitsspeichergrößen der D/Dv2-Familien.

* Azure Compute bietet VM-Größen, die für einen bestimmten Hardwaretyp isoliert und für einen einzelnen Kunden bestimmt sind.  Diese VM-Größen eignen sich am besten für Workloads, die ein hohes Maß an Isolation von anderen Kunden erfordern, wenn es um Workloads mit Elementen wie Konformität und gesetzlichen Anforderungen geht.  Kunden können auch die Ressourcen dieser isolierten virtuellen Computer weiter unterteilen, indem sie die [Azure-Unterstützung für geschachtelte virtuelle Computer](https://azure.microsoft.com/blog/nested-virtualization-in-azure/) verwenden.  In den folgenden Tabellen der VM-Familien finden Sie Ihre Optionen für isolierte VMs.

## <a name="esv3-series"></a>Esv3-Serie 

ACU: 160–190 <sup>1</sup>

Storage Premium  Unterstützt

Storage Premium-Zwischenspeicherung:  Unterstützt

Die Instanzen der ESv3-Serie basieren auf dem Prozessor vom Typ 2,3 GHz Intel XEON ® E5-2673 v4 (Broadwell) und können mit Intel Turbo Boost Technology 2.0 3,5 GHz erreichen und Premium-Speicher nutzen. Die Instanzen der ESv3-Serie eignen sich ideal für speicherintensive Unternehmensanwendungen.


| Größe             | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBps (Cachegröße in GiB) | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBps | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4000/32 (50)                                                       | 3200/48                                | 2/1000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8000/64 (100)                                                      | 6400/96                                | 2/2000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16000/128 (200)                                                    | 12800/192                              | 4/4000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32000/256 (400)                                                    | 25600/384                              | 8 / 8000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 40000/320 (400)                                                    | 32000/480                              | 8 / 10.000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64000/512 (800)                                                    | 51200/768                              | 8/16000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128000/1024 (1600)                                                   | 80000/1200                             | 8 / 30000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128000/1024 (1600)                                                   | 80000/1200                             | 8 / 30000                             |


<sup>1</sup> Virtuelle Computer der Esv3-Serie verfügen über Hyper-Threading-Technologie von Intel®.

<sup>2</sup> Eingeschränkte Kerngrößen verfügbar.

<sup>3</sup> Instanz wird isoliert auf dedizierter Hardware ausgeführt, die für einen einzigen Kunden bereitgestellt wird.


## <a name="ev3-series"></a>Ev3-Serie 

ACU: 160–190 <sup>1</sup>

Storage Premium  Nicht unterstützt

Storage Premium-Zwischenspeicherung:  Nicht unterstützt

Die Instanzen der Ev3-Serie basieren auf dem Prozessor vom Typ 2,3 GHz Intel XEON ® E5-2673 v4 (Broadwell) und können mit Intel Turbo Boost Technology 2.0 3,5 GHz erreichen. Die Instanzen der ESv3-Serie eignen sich ideal für speicherintensive Unternehmensanwendungen.

Datenträgerspeicher wird separat zu virtuellen Computern abgerechnet. Verwenden Sie die ESv3-Größen, um Datenträger mit Premium-Speicher zu nutzen. Die Preis- und Abrechnungskennzahlen für die ESv3-Größen entsprechen denen der Ev3-Serie. 


| Größe            | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (temporärer Speicher): IOPS/MBit/s Lesen/MBit/s Schreiben | Maximale Anzahl NICs/Netzwerkbandbreite |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3000/46/23                                               | 2/1000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6000/93/46                                               | 2/2000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12000/187/93                                             | 4/4000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24000/375/187                                            | 8 / 8000                     |
| Standard_E20_v3 | 20        | 160         | 500            | 32             | 30000/469/234                                            | 8 / 10.000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48000/750/375                                            | 8/16000                 |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |
| Standard_E64i_v3&nbsp;<sup>2,&nbsp;3</sup> | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |

<sup>1</sup> Virtuelle Computer der Ev3-Serie verfügen über Hyper-Threading-Technologie von Intel®.

<sup>2</sup> Eingeschränkte Kerngrößen verfügbar.

<sup>3</sup> Instanz wird isoliert auf dedizierter Hardware ausgeführt, die für einen einzigen Kunden bereitgestellt wird.


## <a name="mv2-series"></a>Mv2-Serie

Storage Premium Unterstützt

Storage Premium-Zwischenspeicherung: Unterstützt

Schreibbeschleunigung: [Unterstützt](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

Die Mv2-Serie verfügt über hohen Durchsatz, geringe Latenz und direkt zugeordneten lokalen NVMe-Speicher, der auf einem Prozessor vom Typ Intel® Xeon® Platinum 8180M 2,5 GHz (Skylake) mit Hyperthreading ausgeführt wird und eine Basistaktfrequenz von 2,5 GHz für alle Kerne und eine maximale Turbotaktfrequenz von 3,8 GHz aufweist. Für alle VM-Größen der Mv2-Serie können sowohl persistente Standard- als auch Premium-Datenträger verwendet werden. Bei Instanzen der Mv2-Serie handelt es sich um arbeitsspeicheroptimierte VM-Größen zur Bereitstellung einer unvergleichlichen Computeleistung, um große In-Memory-Datenbanken und Workloads zu unterstützen. Sie verfügen über ein hohes Arbeitsspeicher/CPU-Verhältnis, das ideal für relationale Datenbankserver, große Caches und In-Memory-Analysen geeignet ist. 

|Größe | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBps (Cachegröße in GiB) | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBps | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M208ms_v2<sup>1, 2</sup> | 208 | 5\.700 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8/16000 |
| Standard_M208s_v2<sup>1, 2</sup> | 208 | 2850 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8/16000 |

Virtuelle Computer der Mv2-Serie verfügen über Hyperthreading-Technologie von Intel®  

<sup>1</sup> Für diese großen VMs ist eines der folgenden Gastbetriebssysteme erforderlich: Windows Server 2016, Windows Server 2019, SLES 12 SP4, SLES 15.

<sup>2</sup> VMs der Mv2-Serie sind nur als „Generation 2“ erhältlich. Bei Verwendung von Linux helfen Ihnen die Informationen zur Ermittlung und Auswahl eines SUSE Linux-Images im folgenden Abschnitt weiter.

#### <a name="find-a-suse-image"></a>Ermitteln eines SUSE-Images

Wählen Sie wie folgt im Azure-Portal ein geeignetes SUSE Linux-Image aus: 

1. Wählen Sie im Azure-Portal die Option **Ressource erstellen**. 
1. Suchen nach „SUSE SAP“ 
1. Images vom Typ „SLES für SAP Generation 2“ sind mit nutzungsbasierter Bezahlung oder als BYOS (Bring Your Own Subscription) erhältlich. Erweitern Sie in den Suchergebnissen die gewünschte Imagekategorie:

    * SUSE Linux Enterprise Server (SLES) für SAP
    * SUSE Linux Enterprise Server (SLES) für SAP (BYOS)
    
1. SUSE-Images, die mit der Mv2-Serie kompatibel sind, ist als Präfix der Name `GEN2:` vorangestellt. Die folgenden SUSE-Images sind für VMs der Mv2-Serie erhältlich:

    * GEN2: SUSE Linux Enterprise Server (SLES) 12 SP4 für SAP-Anwendungen
    * GEN2: SUSE Linux Enterprise Server (SLES) 15 für SAP-Anwendungen
    * GEN2: SUSE Linux Enterprise Server (SLES) 12 SP4 für SAP-Anwendungen (BYOS)
    * GEN2: SUSE Linux Enterprise Server (SLES) 15 für SAP-Anwendungen (BYOS)

#### <a name="select-a-suse-image-via-azure-cli"></a>Auswählen eines SUSE-Images per Azure CLI

Verwenden Sie den folgenden [`az vm image list`](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest#az-vm-image-list)-Befehl, um eine Liste mit dem derzeit verfügbaren SLES für SAP-Image für VMs der Mv2-Serie anzuzeigen:

```azurecli
az vm image list --output table --publisher SUSE --sku gen2 --all
```

Der Befehl gibt die derzeit verfügbaren VMs der Generation 2 aus, die bei SUSE für VMs der Mv2-Serie erhältlich sind. 

Beispielausgabe:

```
Offer          Publisher  Sku          Urn                                        Version
-------------  ---------  -----------  -----------------------------------------  ----------
SLES-SAP       SUSE       gen2-12-sp4  SUSE:SLES-SAP:gen2-12-sp4:2019.05.13       2019.05.13
SLES-SAP       SUSE       gen2-15      SUSE:SLES-SAP:gen2-15:2019.05.13           2019.05.13
SLES-SAP-BYOS  SUSE       gen2-12-sp4  SUSE:SLES-SAP-BYOS:gen2-12-sp4:2019.05.13  2019.05.13
SLES-SAP-BYOS  SUSE       gen2-15      SUSE:SLES-SAP-BYOS:gen2-15:2019.05.13      2019.05.13
```

## <a name="m-series"></a>M-Serie 

ACU: 160–180 <sup>1</sup>

Storage Premium  Unterstützt

Storage Premium-Zwischenspeicherung:  Unterstützt

Schreibbeschleunigung:  [Unterstützt](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Größe            | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBps (Cachegröße in GiB) | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBps | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms&nbsp;<sup>3</sup>    | 8  | 218,75 | 256  | 8  | 10000/100 (793)  | 5000/125 | 4/2000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437,5  | 512  | 16 | 20000/200 (1587) | 10000/250 | 8/4000 |
| Standard_M32ts | 32 | 192    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M32ls | 32 | 256    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M64s  | 64 | 1024   | 2048 | 64 | 80000/800 (6348)| 40000/1000 | 8/16000          |
| Standard_M64ls  | 64 | 512    | 2048 | 64 | 80000/800 (6348) | 40000/1000 | 8/16000 |
| Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1792 | 2048 | 64 | 80000/800 (6348)| 40000/1000 | 8/16000          |
| Standard_M128s&nbsp;<sup>2</sup> | 128  | 2048        | 4096  | 64 | 160000/1600 (12696) | 80000/2000                            | 8 / 30000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3\.892  | 4096 | 64 | 160000/1600 (12696) | 80000/2000                            | 8 / 30000          |
| Standard_M64   | 64  | 1024 | 7168  | 64 | 80000/800 (1228) | 40000/1000 | 8/16000 |
| Standard_M64m  | 64  | 1792 | 7168  | 64 | 80000/800 (1228) | 40000/1000 | 8/16000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2048 | 14336 | 64 | 250000/1600 (2456) | 80000/2000 | 8 / 32.000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3\.892 | 14336 | 64 | 250000/1600 (2456) | 80000/2000 | 8 / 32.000 |



<sup>1</sup> Virtuelle Computer der M-Serie verfügen über Hyper-Threading-Technologie von Intel®

<sup>2</sup> Bei mehr als 64 vCPUs ist eines der folgenden Gastbetriebssysteme erforderlich: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 und Red Hat Enterprise Linux, CentOS 7.3 oder Oracle Linux 7.3 mit LIS 4.2.1.

<sup>3</sup> Eingeschränkte Kerngrößen verfügbar.

<sup>4</sup> Instanz wird isoliert auf dedizierter Hardware ausgeführt, die für einen einzigen Kunden bereitgestellt wird.
<br>


## <a name="dsv2-series-11-15"></a>DSv2-Serie 11-15

ACU: 210–250 <sup>1</sup>

Storage Premium  Unterstützt

Storage Premium-Zwischenspeicherung:  Unterstützt

| Größe | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBps (Cachegröße in GiB) | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBps | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8000/64 (72) |6400/96 |2/1500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16000/128 (144) |12800/192 |4/3000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32000/256 (288) |25600/384 |8/6000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64000/512 (576) |51200/768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80000/640 (720) |64000/960 |8/25000&nbsp;<sup>4</sup>

<sup>1</sup> Der mit einer VM der DSv2-Serie maximal mögliche Datenträgerdurchsatz (IOPS oder MB/s) kann durch Anzahl, Größe und Striping der angefügten Datenträger beschränkt werden.  Details finden Sie unter [Entwerfen für hohe Leistung](../articles/virtual-machines/windows/premium-storage-performance.md).  
<sup>2</sup> Instanz wird isoliert auf dedizierter Hardware ausgeführt, die für einen einzigen Kunden bereitgestellt wird.  
<sup>3</sup> Eingeschränkte Kerngrößen verfügbar.  
<sup>4</sup> 25000 Mbps mit beschleunigtem Netzwerkbetrieb. 

<br>

## <a name="dv2-series-11-15"></a>Dv2-Serie 11-15

ACU: 210–250

Storage Premium  Nicht unterstützt

Storage Premium-Zwischenspeicherung:  Nicht unterstützt

| Größe              | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Maximaler Durchsatz (temporärer Speicher): IOPS/MBit/s Lesen/MBps Schreiben | Max. Datenträger/Durchsatz: IOPS | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000/93/46                                           | 8/8 x 500                         | 2/1500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000/187/93                                         | 16/16 x 500                         | 4/3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000/375/187                                        | 32/32 x 500                       | 8/6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000/750/375                                        | 64/64 x 500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1000          | 60000/937/468                                        | 64/64 x 500                       | 8/25000&nbsp;<sup>2</sup> |

<sup>1</sup> Instanz wird isoliert auf dedizierter Hardware ausgeführt, die für einen einzigen Kunden bereitgestellt wird.  
<sup>2</sup> 25000 Mbps mit beschleunigtem Netzwerkbetrieb. 
