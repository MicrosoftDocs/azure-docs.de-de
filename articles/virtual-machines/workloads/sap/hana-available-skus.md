---
title: SKUs für SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: SKUs für SAP HANA in Azure (große Instanzen)
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b80f872c82061c0cb87f4f1e2714183e71cf02cd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60794035"
---
# <a name="available-skus-for-hli"></a>Verfügbare SKUs für HLI

Der Dienst „SAP HANA in Azure (große Instanzen)“ ist in verschiedenen Konfigurationen in den folgenden Azure-Regionen verfügbar: „USA, Westen“, „USA, Osten“, „Australien, Osten“, „Australien, Südosten“, „Europa, Westen“, „Europa, Norden“, „Japan, Osten“ und „Japan, Westen“.

[Zertifizierte SAP HANA-SKUs für große HANA-Instanzen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) werden in der folgenden Liste aufgeführt:

| SAP-Lösung | CPU | Arbeitsspeicher | Storage | Verfügbarkeit |
| --- | --- | --- | --- | --- |
| Optimiert für OLAP: SAP BW, BW/4HANA<br /> oder SAP HANA für generische OLAP-Workload | SAP HANA in Azure S72<br /> – 2 x Intel® Xeon®-Prozessor E7-8890 v3<br /> 36 CPU-Kerne und 72 CPU-Threads |  768 GB |  3 TB | Wird nicht mehr angeboten |
| --- | SAP HANA in Azure S144<br /> – 4 x Intel® Xeon®-Prozessor E7-8890 v3<br /> 72 CPU-Kerne und 144 CPU-Threads |  1,5 TB |  6 TB | Wird nicht mehr angeboten |
| --- | SAP HANA in Azure S192<br /> – 4 x Intel® Xeon® Processor E7-8890 v4<br /> 96 CPU-Kerne und 192 CPU-Threads |  2,0 TB |  8 TB | Verfügbar |
| --- | SAP HANA in Azure S384<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 CPU-Kerne und 384 CPU-Threads |  4,0 TB |  16 TB | Verfügbar |
| Optimiert für OLTP: SAP Business Suite<br /> auf SAP HANA oder S/4HANA (OLTP),<br /> generisches OLTP | SAP HANA in Azure S72m<br /> – 2 x Intel® Xeon®-Prozessor E7-8890 v3<br /> 36 CPU-Kerne und 72 CPU-Threads |  1,5 TB |  6 TB | Wird nicht mehr angeboten |
|---| SAP HANA in Azure S144m<br /> – 4 x Intel® Xeon®-Prozessor E7-8890 v3<br /> 72 CPU-Kerne und 144 CPU-Threads |  3,0 TB |  12TB | Wird nicht mehr angeboten |
|---| SAP HANA in Azure S192m<br /> – 4 x Intel® Xeon® Processor E7-8890 v4<br /> 96 CPU-Kerne und 192 CPU-Threads  |  4,0 TB |  16 TB | Verfügbar |
|---| SAP HANA in Azure S384m<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 CPU-Kerne und 384 CPU-Threads |  6,0 TB |  18 TB | Verfügbar |
|---| SAP HANA in Azure S384xm<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 CPU-Kerne und 384 CPU-Threads |  8,0 TB |  22 TB |  Verfügbar |
|---| SAP HANA in Azure S576m<br /> – 12 x Intel® Xeon® Processor E7-8890 v4<br /> 288 CPU-Kerne und 576 CPU-Threads |  12,0 TB |  28 TB | Verfügbar |
|---| SAP HANA in Azure S768m<br /> – 16 x Intel® Xeon® Processor E7-8890 v4<br /> 384 CPU-Kerne und 768 CPU-Threads |  16, 0 TB |  36 TB | Verfügbar |
|---| SAP HANA in Azure S960m<br /> – 20 x Intel® Xeon® Processor E7-8890 v4<br /> 480 CPU-Kerne und 960 CPU-Threads |  20,0 TB |  46 TB | Verfügbar |


Unter SAP HANA TDIv5 ermöglicht SAP die kundenspezifische Größenanpassung und kundenspezifische Projekte, die zu Serverkonfigurationen führen könnten, die nicht als zertifiziert wie in den folgenden Dokumenten aufgeführt werden:

- [Zertifizierte SAP HANA-Appliances](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [Zertifizierte SAP HANA-IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

In vielen Fällen enthalten diese kundenspezifischen Serverkonfigurationen mehr Arbeitsspeicher als die durch SAP zertifizierten Servereinheiten. Bei der Nutzung von SAP besteht für Kunden die Möglichkeit, SAP-Support zu erhalten und sich für ihre kundenspezifische Größe der Serverkonfigurationen zu zertifizieren. In Azure sind die folgenden Standard-SKUs für große HANA-Instanzen verfügbar und werden in der Microsoft-Preisliste für solche TDIv5-Projekte mit kundenspezifischer Größe aufgeführt.

| SKU|CPU | Arbeitsspeicher | Storage | Verfügbarkeit |
| ---| --- | --- | --- | --- |
| S96 | SAP HANA in Azure S96<br /> – 2 x Intel® Xeon® Prozessor E7-8890 v4<br /> 48 CPU-Kerne und 96 CPU-Threads |  768 GB |  3 TB | Verfügbar |


| Ursprüngliche SKU, die im <br /> Arbeitsspeicher erweitert werden kann | CPU | Arbeitsspeicher | Storage | Verfügbarkeit |
| --- | --- | --- | --- | --- |
| S192m kann erweitert werden in | SAP HANA in Azure S192xm<br /> – 4 x Intel® Xeon® Processor E7-8890 v4<br /> 96 CPU-Kerne und 192 CPU-Threads |  6,0 TB |  16 TB | Verfügbar |
| S384xm kann erweitert werden in | SAP HANA in Azure S384xxm<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 CPU-Kerne und 384 CPU-Threads |  12,0 TB |  28 TB | Verfügbar |
| S576m kann erweitert werden in | SAP HANA in Azure S576xm<br /> – 12 x Intel® Xeon® Processor E7-8890 v4<br /> 288 CPU-Kerne und 576 CPU-Threads |  18 TB |  41 TB | Verfügbar |
| S768m kann erweitert werden in | SAP HANA in Azure S768xm<br /> – 16 x Intel® Xeon® Processor E7-8890 v4<br /> 384 CPU-Kerne und 768 CPU-Threads |  24 TB |  56 TB | Verfügbar |

- CPU-Kerne = Summe der CPU-Kerne ohne Hyperthreading aus der Gesamtsumme der Prozessoren der Servereinheit.
- CPU-Threads = Summe der Computethreads von den Hyperthreading-CPU-Kernen aus der Gesamtsumme der Prozessoren der Servereinheit. Die meisten Einheiten sind standardmäßig für die Verwendung der Hyperthreading-Technologie konfiguriert.
- Basierend auf den Herstellerempfehlungen sind S768m, S768xm und S960m nicht für die Verwendung von Hyperthreading zum Ausführen von SAP HANA konfiguriert.


Die jeweils gewählten Konfigurationen hängen von der Workload, den CPU-Ressourcen und dem gewünschten Arbeitsspeicher ab. Die OLTP-Workload kann die SKUs nutzen, die für die OLAP-Workload optimiert sind. 

Die Hardwarebasis für die Angebote (mit Ausnahme von Einheiten für Projekte mit kundenspezifischer Größe) sind SAP HANA TDI-zertifiziert. Die SKUs werden durch zwei unterschiedliche Hardwareklassen wie folgt unterteilt:

- S72, S72m, S96, S144, S144m, S192, S192m und S192xm, die als „Typ-I-Klasse“ von SKUs bezeichnet werden.
- S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm und S960m, die wir als „Typ II-Klasse“ von SKUs bezeichnen.

Ein vollständiges Umfeld von HANA (große Instanz) ist nicht ausschließlich zur Verwendung durch einen einzelnen Kunden zugeordnet. Dies gilt für Racks mit Compute- und Speicherressourcen, die über ein in Azure bereitgestelltes Netzwerkfabric verbunden sind. Die Infrastruktur von HANA (große Instanz) stellt wie Azure verschiedene &quot;Kundenmandanten&quot; bereit, die in den drei folgenden Ebenen voneinander isoliert sind:

- **Netzwerk:** Isolation durch virtuelle Netzwerke innerhalb des Umfelds von HANA (große Instanz).
- **Speicher**: Isolation durch Speicher-VMs, denen Speichervolumes zugewiesen sind und die Speichervolumes zwischen Mandanten isolieren.
- **Compute**: Dedizierte Zuweisung von Servereinheiten zu einem einzelnen Mandanten. Keine Hard- oder Softpartitionierung von Servereinheiten. Keine Freigabe einer einzelnen Server- oder Hosteinheit zwischen Mandanten. 

Die Bereitstellungen von Einheiten von HANA (große Instanz) zwischen verschieden Mandanten sind füreinander nicht sichtbar. In verschiedenen Mandanten bereitgestellte Einheiten von HANA (große Instanz) können nicht direkt auf der Ebene des Umfelds von HANA (große Instanz) miteinander kommunizieren. Nur Einheiten von HANA (große Instanz) innerhalb eines Mandanten können auf der Ebene des Umfelds von HANA (große Instanz) miteinander kommunizieren.

Ein bereitgestellter Mandant im Umfeld der großen Instanz wird zur Abrechnung einem Azure-Abonnement zugewiesen. In einem Netzwerk kann über virtuelle Netzwerke anderer Azure-Abonnements in derselben Azure-Registrierung darauf zugegriffen werden. Bei Bedarf können Sie auch einen getrennten HANA-Mandanten (große Instanz) beantragen, wenn Sie mit einem anderen Azure-Abonnement in derselben Azure-Region Bereitstellungen durchführen.

Es gibt jedoch bedeutende Unterschiede zwischen der Ausführung von SAP HANA unter HANA (große Instanz) und der Ausführung von SAP HANA auf in Azure bereitgestellten VMs:

- Es gibt keine Virtualisierungsschicht für SAP HANA in Azure (große Instanzen). Sie profitieren von der Leistung der zugrunde liegenden Bare-Metal-Hardware.
- Im Gegensatz zu Azure ist der Server für SAP HANA in Azure (große Instanzen) für einen bestimmten Kunden vorgesehen. Die Hard- oder Softpartitionierung einer Servereinheit oder eines Hosts ist nicht möglich. Deshalb wird eine Einheit von HANA (große Instanz) als Ganzes einem Mandanten und somit Ihnen zugewiesen und verwendet. Ein Neustart oder das Herunterfahren des Servers führt nicht automatisch dazu, dass das Betriebssystem und SAP HANA auf einem anderen Server bereitgestellt werden. (Die einzige Ausnahme für Typ-I-Klasse-SKUs ist das Auftreten von Problemen auf einem Server, die eine erneute Bereitstellung auf einem anderen Server erfordern.)
- Im Gegensatz zu Azure, wo die Auswahl der Hostprozessortypen nach dem besten Preis-Leistungs-Verhältnis erfolgt, werden für SAP HANA in Azure (große Instanzen) die Prozessortypen mit der höchsten Leistung der Intel E7v3- und E7v4-Prozessorserie ausgewählt.

**Nächste Schritte**
- Lesen Sie [HLI-Größenanpassung](hana-sizing.md).
