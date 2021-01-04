---
title: SKUs für SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: SKUs für SAP HANA in Azure (große Instanzen)
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
keywords: HLI, HANA, SKUs, S896, S224, S448, S672, Optane, SAP
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2732f9fc4b1b9251391180874a055e8ffd8d9e7a
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94985075"
---
# <a name="available-skus-for-hana-large-instances"></a>Verfügbare SKUs für große HANA-Instanzen

Der nur auf Stempeln der Revision 3 basierende Dienst „SAP HANA in Azure (große Instanzen)“ ist in den folgenden Azure-Regionen mit verschiedenen Konfigurationen verfügbar:

- Australien (Osten)
- Australien, Südosten
- Japan, Osten
- Japan, Westen

Der auf Stempeln der Revision 4 basierende Dienst „SAP HANA in Azure (große Instanzen)“ ist in den folgenden Azure-Regionen mit verschiedenen Konfigurationen verfügbar:

- USA, Westen 2
- East US

Für SAP HANA-Workloads zertifizierter, auf Stempeln der Revision 4.2 basierender BareMetal-Infrastrukturdienst. Verfügbar in verschiedenen Konfigurationen in folgenden Azure-Regionen:
- Europa, Westen
- Nordeuropa
- USA (Ost) 2
- USA Süd Mitte




Die Liste der angebotenen großen Azure-Instanzen sieht folgendermaßen aus.

> [!IMPORTANT]
> Beachten Sie die erste Spalte, die den Status der HANA-Zertifizierung für jeden der Typen von großen Instanzen in der Liste enthält. Die Spalte sollte mit dem [SAP HANA-Hardwareverzeichnis](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) für die Azure-SKUs übereinstimmen, die mit dem Buchstaben **S** beginnen.



| SAP HANA-zertifiziert | Modell | Gesamter Speicher | Arbeitsspeicher DRAM | Arbeitsspeicher Optane | Storage | Verfügbarkeit |
| --- | --- | --- | --- | --- | --- | --- |
| YES <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2185), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2265) | SAP HANA in Azure S96<br /> – 2 x Intel® Xeon®-Prozessor E7-8890 v4 <br /> 48 CPU-Kerne und 96 CPU-Threads |  768 GB | 768 GB | --- | 3,0 TB | Verfügbar |
| YES <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2186), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2269) | SAP HANA in Azure S224<br /> – 4 x Intel® Xeon® Platinum 8276-Prozessor <br /> 112 CPU-Kerne und 224 CPU-Threads |  3,0 TB | 3,0 TB | --- | 6,3 TB | Verfügbar |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2297) | SAP HANA in Azure S224m<br /> – 4 x Intel® Xeon® Platinum 8276-Prozessor <br /> 112 CPU-Kerne und 224 CPU-Threads |  6,0 TB | 6,0 TB | --- | 10,5 TB | Verfügbar |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2381) | SAP HANA in Azure S224om<br /> – 4 x Intel® Xeon® Platinum 8276-Prozessor <br /> 112 CPU-Kerne und 224 CPU-Threads | 6,0 TB |  3,0 TB |  3,0 TB | 10,5 TB | Verfügbar |
| Nein | SAP HANA in Azure S224<br /> – 4 x Intel® Xeon® Platinum 8276-Prozessor <br /> 112 CPU-Kerne und 224 CPU-Threads | 4,5 TB |  1,5 TB |  3,0 TB | 8,4 TB | Verfügbar |
| Nein | SAP HANA in Azure S224ooo<br /> – 4 x Intel® Xeon® Platinum 8276-Prozessor <br /> 112 CPU-Kerne und 224 CPU-Threads | 7,5 TB |  1,5 TB |  6,0 TB | 12,7 TB | Verfügbar |
| Nein | SAP HANA in Azure S224oom<br /> – 4 x Intel® Xeon® Platinum 8276-Prozessor <br /> 112 CPU-Kerne und 224 CPU-Threads | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB | Verfügbar |
| YES <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1983), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2268) | SAP HANA in Azure S384<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 CPU-Kerne und 384 CPU-Threads |  4,0 TB | 4,0 TB | --- | 16 TB | Verfügbar |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2080) | SAP HANA in Azure S384m<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 CPU-Kerne und 384 CPU-Threads |  6,0 TB | 6,0 TB | --- | 18 TB |  Verfügbar  |
| YES <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1984), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2267) | SAP HANA in Azure S384xm<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 CPU-Kerne und 384 CPU-Threads |  8,0 TB | 8,0 TB | --- | 22 TB | Verfügbar |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2378) | SAP HANA in Azure S448<br /> – 8 x Intel® Xeon® Platinum 8276-Prozessor <br /> 224 CPU-Kerne und 448 CPU-Threads | 6,0 TB |  6,0 TB |  --- | 10,5 TB | Verfügbar (nur Rev 4) |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2377) | SAP HANA in Azure S448m<br /> – 8 x Intel® Xeon® Platinum 8276-Prozessor <br /> 224 CPU-Kerne und 448 CPU-Threads | 12,0 TB |  12,0 TB |  --- | 18,9 TB | Verfügbar (nur Rev 4) |
| Nein | SAP HANA in Azure S448oo<br /> – 8 x Intel® Xeon® Platinum 8276-Prozessor <br /> 224 CPU-Kerne und 448 CPU-Threads | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB  | Verfügbar (nur Rev 4) |
| Nein | SAP HANA in Azure S448om<br /> – 8 x Intel® Xeon® Platinum 8276-Prozessor <br /> 224 CPU-Kerne und 448 CPU-Threads | 12,0 TB |  6,0 TB |  6,0 TB | 18,9 TB  | Verfügbar (nur Rev 4) |
| Nein | SAP HANA in Azure S448ooo<br /> – 8 x Intel® Xeon® Platinum 8276-Prozessor <br /> 224 CPU-Kerne und 448 CPU-Threads | 15,0 TB |  3,0 TB |  12,0 TB | 23,2 TB  | Verfügbar (nur Rev 4) |
| Nein | SAP HANA in Azure S448oom<br /> – 8 x Intel® Xeon® Platinum 8276-Prozessor <br /> 224 CPU-Kerne und 448 CPU-Threads | 18 TB |  6,0 TB |  12,0 TB | 27,4 TB  | Verfügbar (nur Rev 4) |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2049) | SAP HANA in Azure S576m<br /> – 12 x Intel® Xeon® Processor E7-8890 v4<br /> 288 CPU-Kerne und 576 CPU-Threads |  12,0 TB | 12,0 TB | --- | 28 TB | Verfügbar (nur Rev 4) |
| Nein | SAP HANA in Azure S576xm<br /> – 12 x Intel® Xeon® Processor E7-8890 v4<br /> 288 CPU-Kerne und 576 CPU-Threads |  18 TB | 18.0 | --- |  41 TB | Verfügbar |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2380) | SAP HANA in Azure S672<br /> – 12 x Intel® Xeon® Platinum 8276-Prozessor <br /> 336 CPU-Kerne und 672 CPU-Threads | 9,0 TB |  9,0 TB |  --- | 14,7 TB | Verfügbar (nur Rev 4) |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2379) | SAP HANA in Azure S672m<br /> – 12 x Intel® Xeon® Platinum 8276-Prozessor <br /> 336 CPU-Kerne und 672 CPU-Threads | 18 TB |  18 TB |  --- | 27,4 TB | Verfügbar (nur Rev 4) |
| Nein | SAP HANA in Azure S672oo<br /> – 12 x Intel® Xeon® Platinum 8276-Prozessor <br /> 336 CPU-Kerne und 672 CPU-Threads | 13,5 TB |  4,5 TB |  9,0 TB | 21,1 TB  | Verfügbar (nur Rev 4) |
| Nein | SAP HANA in Azure S672om<br /> – 12 x Intel® Xeon® Platinum 8276-Prozessor <br /> 336 CPU-Kerne und 672 CPU-Threads | 18 TB |  9,0 TB |  9,0 TB | 27,4 TB  | Verfügbar (nur Rev 4) |
| Nein | SAP HANA in Azure S672ooo<br /> – 12 x Intel® Xeon® Platinum 8276-Prozessor <br /> 336 CPU-Kerne und 672 CPU-Threads | 22,5 TB |  4,5 TB |  18 TB | 33,7 TB  | Verfügbar (nur Rev 4) |
| Nein | SAP HANA in Azure S672oom<br /> – 12 x Intel® Xeon® Platinum 8276-Prozessor <br /> 336 CPU-Kerne und 672 CPU-Threads | 27,0 TB |  9,0 TB |  18 TB | 40,0 TB  | Verfügbar (nur Rev 4) |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1985) | SAP HANA in Azure S768m<br /> – 16 x Intel® Xeon® Processor E7-8890 v4<br /> 384 CPU-Kerne und 768 CPU-Threads |  16, 0 TB | 16, 0 TB | -- | 36 TB | Verfügbar |
| Nein | SAP HANA in Azure S768xm<br /> – 16 x Intel® Xeon® Processor E7-8890 v4<br /> 384 CPU-Kerne und 768 CPU-Threads |  24 TB | 24 TB | --- | 56 TB | Verfügbar |
|  YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2376)  | SAP HANA in Azure S896<br /> – 16 x Intel® Xeon® Platinum 8276-Prozessor <br /> 448 CPU-Kerne und 896 CPU-Threads | 12,0 TB |  12,0 TB |  --- | 18,9 TB | Verfügbar (nur Rev 4) |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2328) | SAP HANA in Azure S896m<br /> – 16 x Intel® Xeon® Platinum 8276-Prozessor <br /> 448 CPU-Kerne und 896 CPU-Threads | 24 TB | 24 TB | -- | 35,8 TB | Verfügbar |
| Nein | SAP HANA in Azure S896oo<br /> – 16 x Intel® Xeon® Platinum 8276-Prozessor <br /> 448 CPU-Kerne und 896 CPU-Threads | 18 TB |  6,0 TB |  12,0 TB | 27,4 TB  | Verfügbar (nur Rev 4) |
| Nein | SAP HANA in Azure S896om<br /> – 16 x Intel® Xeon® Platinum 8276-Prozessor <br /> 448 CPU-Kerne und 896 CPU-Threads | 24 TB |  12,0 TB |  12,0 TB | 35,8 TB  | Verfügbar (nur Rev 4) |
| Nein | SAP HANA in Azure S896ooo<br /> – 16 x Intel® Xeon® Platinum 8276-Prozessor <br /> 448 CPU-Kerne und 896 CPU-Threads | 30,0 TB |  6,0 TB |  24 TB | 44,3 TB  | Verfügbar (nur Rev 4) |
| Nein | SAP HANA in Azure S896oom<br /> – 16 x Intel® Xeon® Platinum 8276-Prozessor <br /> 448 CPU-Kerne und 896 CPU-Threads | 36,0 TB |  12,0 TB |  24 TB | 52,7 TB  | Verfügbar (nur Rev 4) |
| YES <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1986) | SAP HANA in Azure S960m<br /> – 20 x Intel® Xeon® Processor E7-8890 v4<br /> 480 CPU-Kerne und 960 CPU-Threads |  20,0 TB | 20,0 TB | -- | 46 TB | Verfügbar (nur Rev 4) |


- CPU-Kerne = Summe der CPU-Kerne ohne Hyperthreading aus der Gesamtsumme der Prozessoren der Servereinheit.
- CPU-Threads = Summe der Computethreads von den Hyperthreading-CPU-Kernen aus der Gesamtsumme der Prozessoren der Servereinheit. Die meisten Einheiten sind standardmäßig für die Verwendung der Hyperthreading-Technologie konfiguriert.
- Basierend auf den Herstellerempfehlungen sind S768m, S768xm und S960m nicht für die Verwendung von Hyperthreading zum Ausführen von SAP HANA konfiguriert.


> [!IMPORTANT]
> Die folgenden SKUs werden zwar weiterhin unterstützt, können jedoch nicht mehr gekauft werden: S72, S72m, S144, S144m, S192, and S192m 

Die jeweils gewählten Konfigurationen hängen von der Workload, den CPU-Ressourcen und dem gewünschten Arbeitsspeicher ab. Die OLTP-Workload kann die SKUs nutzen, die für die OLAP-Workload optimiert sind. 

Die SKUs werden durch zwei unterschiedliche Hardwareklassen wie folgt unterteilt:

- S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, and S224m, S224oo, S224om, S224ooo, S224oom werden als „Typ-I-Klasse“ von SKUs bezeichnet.
- Alle anderen SKUs werden als „Typ-II-Klasse“ von SKUs bezeichnet.
- Wenn Sie an SKUs interessiert sind, die noch nicht im SAP-Hardwareverzeichnis aufgelistet sind, wenden Sie sich an das Microsoft-Konto-Team, um weitere Informationen zu erhalten. 


Ein vollständiges Umfeld von HANA (große Instanz) ist nicht ausschließlich zur Verwendung durch einen einzelnen Kunden zugeordnet. Dies gilt für Racks mit Compute- und Speicherressourcen, die über ein in Azure bereitgestelltes Netzwerkfabric verbunden sind. Die Infrastruktur von HANA (große Instanz) stellt wie Azure verschiedene &quot;Kundenmandanten&quot; bereit, die in den drei folgenden Ebenen voneinander isoliert sind:

- **Netzwerk:** Isolation durch virtuelle Netzwerke innerhalb des Umfelds von HANA (große Instanz).
- **Storage**: Isolation durch Speicher-VMs, denen Speichervolumes zugewiesen sind und die Speichervolumes zwischen Mandanten isolieren.
- **Compute**: Dedizierte Zuweisung von Servereinheiten zu einem einzelnen Mandanten. Keine Hard- oder Softpartitionierung von Servereinheiten. Keine Freigabe einer einzelnen Server- oder Hosteinheit zwischen Mandanten. 

Die Bereitstellungen von Einheiten von HANA (große Instanz) zwischen verschieden Mandanten sind füreinander nicht sichtbar. In verschiedenen Mandanten bereitgestellte Einheiten von HANA (große Instanz) können nicht direkt auf der Ebene des Umfelds von HANA (große Instanz) miteinander kommunizieren. Nur Einheiten von HANA (große Instanz) innerhalb eines Mandanten können auf der Ebene des Umfelds von HANA (große Instanz) miteinander kommunizieren.

Ein bereitgestellter Mandant im Umfeld der großen Instanz wird zur Abrechnung einem Azure-Abonnement zugewiesen. In einem Netzwerk kann über virtuelle Netzwerke anderer Azure-Abonnements in derselben Azure-Registrierung darauf zugegriffen werden. Bei Bedarf können Sie auch einen getrennten HANA-Mandanten (große Instanz) beantragen, wenn Sie mit einem anderen Azure-Abonnement in derselben Azure-Region Bereitstellungen durchführen.

Es gibt jedoch bedeutende Unterschiede zwischen der Ausführung von SAP HANA unter HANA (große Instanz) und der Ausführung von SAP HANA auf in Azure bereitgestellten VMs:

- Es gibt keine Virtualisierungsschicht für SAP HANA in Azure (große Instanzen). Sie profitieren von der Leistung der zugrunde liegenden Bare-Metal-Hardware.
- Im Gegensatz zu Azure ist der Server für SAP HANA in Azure (große Instanzen) für einen bestimmten Kunden vorgesehen. Die Hard- oder Softpartitionierung einer Servereinheit oder eines Hosts ist nicht möglich. Deshalb wird eine Einheit von HANA (große Instanz) als Ganzes einem Mandanten und somit Ihnen zugewiesen und verwendet. Ein Neustart oder das Herunterfahren des Servers führt nicht automatisch dazu, dass das Betriebssystem und SAP HANA auf einem anderen Server bereitgestellt werden. (Die einzige Ausnahme für Typ-I-Klasse-SKUs ist das Auftreten von Problemen auf einem Server, die eine erneute Bereitstellung auf einem anderen Server erfordern.)
- Im Gegensatz zu Azure, wo die Auswahl der Hostprozessortypen nach dem besten Preis-Leistungs-Verhältnis erfolgt, werden für SAP HANA in Azure (große Instanzen) die Prozessortypen mit der höchsten Leistung der Intel E7v3- und E7v4-Prozessorserie ausgewählt.

**Nächste Schritte**
- Lesen Sie [HLI-Größenanpassung](hana-sizing.md).
