---
title: 'SAP HANA: Speicherkonfigurationen für virtuelle Azure-Computer | Microsoft-Dokumentation'
description: Speicherempfehlungen für virtuelle Computer mit SAP HANA-Bereitstellung.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure HANA, Storage Ultra-Datenträger, Storage Premium
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/03/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 48d1c04e59d316ac19000f5b890c0cb1c96fb213
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549438"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA: Speicherkonfigurationen für virtuelle Azure-Computer

Azure bietet verschiedene geeignete Speichertypen für virtuelle Azure-Computer mit SAP HANA. Für **SAP HANA-zertifizierte Azure-Speichertypen** kommen unter anderem folgende Azure-Speichertypen in Frage: 

- Azure SSD Premium oder Storage Premium 
- [Ultra-Datenträger](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Informationen zu diesen Datenträgertypen finden Sie im Artikel [Azure Storage-Typen für die SAP-Workload](./planning-guide-storage.md) und [Auswählen eines Datenträgertyps](../../disks-types.md).

Azure bietet zwei Bereitstellungsmethoden für virtuelle Festplatten (VHDs) in Azure Storage Standard und Premium. Wir erwarten, dass Sie [verwalteter Azure-Datenträger](https://azure.microsoft.com/services/managed-disks/) für Azure Blob Storage-Bereitstellungen nutzen. 

Eine Liste der Speichertypen und deren Vereinbarungen zum Servicelevel für IOPS- und Speicherdurchsatz finden Sie in der [Azure-Dokumentation für verwaltete Datenträger](https://azure.microsoft.com/pricing/details/managed-disks/).

> [!IMPORTANT]
> Unabhängig vom gewählten Azure-Speichertyp muss das Dateisystem, das für diesen Speicher verwendet wird, von SAP für das jeweilige Betriebssystem und DBMS unterstützt werden. [SAP-Supporthinweis 2972496](https://launchpad.support.sap.com/#/notes/2972496) listet die für verschiedene Betriebssysteme und Datenbanken, einschließlich SAP HANA, unterstützten Dateisysteme auf. Dies gilt für alle Volumes, auf die SAP HANA zum Lesen und Schreiben für eine beliebige Aufgabe möglicherweise zugreift. Speziell bei Verwendung von NFS in Azure für SAP HANA gelten, wie weiter unten in diesem Artikel angegeben, zusätzliche Einschränkungen der NFS-Versionen. 


Die minimalen für SAP HANA zertifizierten Bedingungen für die verschiedenen Speichertypen sind: 

- Azure Storage Premium – **/hana/log** ist erforderlich, um von der Azure-[Schreibbeschleunigung](../../how-to-enable-write-accelerator.md) unterstützt zu werden. Das Volume **/hana/data** kann ohne Azure-Schreibbeschleunigung auf Storage Premium oder auf Disk Ultra platziert werden.
- Azure Disk Ultra mindestens für das Volume **/hana/log**. Das Volume **/hana/data** kann entweder ohne Azure-Schreibbeschleunigung auf Storage Premium oder für schnellere Neustartzeiten auf Disk Ultra platziert werden.
- **NFS v4.1**-Volumes basierend auf Azure NetApp Files für **/hana/log und /hana/data**. Das Volume von „/hana/shared“ kann das Protokoll NFS v3 oder NFS v4.1 verwenden.

Einige Speichertypen können kombiniert werden. Es ist z. B. möglich, **/hana/data** unter Storage Premium zu speichern, und **/hana/log** kann unter Disk Storage Ultra gespeichert werden, um die erforderliche geringe Wartezeit zu erzielen. Wenn Sie ein auf ANF basierendes Volume für **/hana/data** verwenden, muss das Volume **/hana/log** zusätzlich zu ANF auch auf NFS basieren. Die Verwendung von NFS über ANF für eines der Volumes (z. B. „/hana/data“) und Azure Storage Premium oder Disk Ultra für das andere Volume (z. B. **/hana/log**) wird **nicht unterstützt**.

In der lokalen Umgebung mussten Sie sich nur selten Gedanken über die E/A-Subsysteme und deren Funktionen machen. Das lag daran, dass der Hersteller des Geräts sicherstellen musste, dass die Mindestspeicheranforderungen für SAP HANA erfüllt sind. Da Sie die Azure-Infrastruktur selbst erstellen, sollten Sie mit einigen dieser SAP-Anforderungen vertraut sein. Einige der von SAP empfohlenen minimalen Durchsatzmerkmale sind:

- Lese-/Schreibaktivität mit 250 MB/s in **/hana/log** bei einer E/A-Größe von 1 MB
- Leseaktivität mit mindestens 400 MB/s in **/hana/data** für E/A-Größen von 16 MB und 64 MB
- Schreibaktivität mit mindestens 250 MB/s in **/hana/data** für E/A-Größen von 16 MB und 64 MB

Da für DBMS-Systeme (und somit auch für SAP HANA) eine geringe Speicherlatenz wichtig ist, behalten sie Daten im Arbeitsspeicher. Der kritische Pfad beim Speichern sind in der Regel die Schreibvorgänge in das Transaktionsprotokoll der DBMS-Systeme. Aber auch Vorgänge wie das Schreiben von Sicherungspunkten oder das Laden von Daten in den Arbeitsspeicher nach der Wiederherstellung nach einem Systemabsturz können wichtig sein. Daher ist für die Volumes **/hana/data** und **/hana/log** die Verwendung von Azure Storage Premium, Disk Ultra oder ANF **zwingend** erforderlich. 


Einige Leitprinzipien bei der Auswahl Ihrer Speicherkonfiguration für HANA können wie folgt aufgeführt werden:

- Entscheiden Sie sich für den Speichertyp auf der Grundlage von [Azure Storage-Typen für die SAP-Workload](./planning-guide-storage.md) und [Auswählen eines Datenträgertyps](../../disks-types.md).
- Beachten Sie den E/A-Gesamtdurchsatz und die IOPS-Grenzwerte eines virtuellen Computers, wenn Sie die Größe festlegen oder sich für einen virtuellen Computer entscheiden. Der VM-Gesamtspeicherdurchsatz ist im Artikel [Arbeitsspeicheroptimierte Größen virtueller Computer](../../sizes-memory.md) beschrieben.
- Versuchen Sie bei der Entscheidung für die Speicherkonfiguration mit Ihrer **/hana/data**-Volumenkonfiguration unter dem Gesamtdurchsatz des virtuellen Computers zu bleiben. Beim Schreiben von Sicherungspunkten kann SAP HANA bei der E/A-Ausgabe aggressiv sein. Es ist leicht möglich, beim Schreiben eines Sicherungspunkts bis an die Durchsatzgrenzen Ihres **/hana/data**-Volumens zu gehen. Wenn Ihre Datenträger, die das **/hana/data**-Volume bilden, einen höheren Durchsatz aufweisen, als Ihre VM zulässt, könnten Sie in Situationen geraten, in denen der vom Sicherungspunkt verwendete Durchsatz die Durchsatzanforderungen der Schreibvorgänge für das Wiederholungsprotokoll stört. Eine Situation, die sich auf den Durchsatz der Anwendung auswirken kann.


> [!IMPORTANT]
> Die Vorschläge für die Speicherkonfigurationen sind als Anleitung für den Einstieg gedacht. Wenn Sie die Workload ausführen und Speicherauslastungsmuster analysieren, stellen Sie möglicherweise fest, dass Sie nicht die gesamte zur Verfügung gestellte Speicherbandbreite oder IOPS nutzen. Sie könnten dann eine Verkleinerung des Speichers in Betracht ziehen. Oder aber Ihre Workload benötigt bei diesen Konfigurationen möglicherweise mehr Speicherdurchsatz als vorgeschlagen. Infolgedessen müssen Sie möglicherweise mehr Kapazität, IOPS oder Durchsatz bereitstellen. Im Spannungsbereich zwischen erforderlicher Speicherkapazität, erforderlicher Speicherlatenz, erforderlichem Speicherdurchsatz und IOPS und der kostengünstigsten Konfiguration bietet Azure genügend verschiedene Speichertypen mit unterschiedlichen Fähigkeiten und Preisen, um den richtigen Kompromiss für Sie und Ihre HANA-Workload zu finden und anzupassen.


## <a name="stripe-sets-versus-sap-hana-data-volume-partitioning"></a>Stripesets im Vergleich zur SAP HANA-Datenvolumepartitionierung
Mit Azure Storage Premium erzielen Sie das beste Preis-/Leistungsverhältnis, wenn Sie das **/hana/data**- und/oder **/hana/log**-Volume über mehrere Azure-Datenträger verteilen. Dies ist die Alternative zum Bereitstellen größerer Datenträgervolumes, die den zusätzlichen Bedarf an IOPS oder Durchsatz decken. Bisher wurde dies mit LVM- und MDADM-Volume-Managern erreicht, die Teil von Linux sind. Die Methode des Datenträgerstripings ist Jahrzehnte alt und wohlbekannt. So vorteilhaft diese Stripesetvolumes sind, um Ihren IOPS- oder Durchsatzbedarf zu erfüllen, sie erhöhen auch die Komplexität der Verwaltung. Dies gilt insbesondere, wenn die Kapazität der Volumes erweitert werden muss. Zumindest für **/hana/data** hat SAP eine alternative Methode eingeführt, mit der Sie das gleiche Ziel erreichen wie mit dem Striping über mehrere Azure-Datenträger. Seit SAP Hana 2.0 SPS03 kann der HANA-Indexserver seine E/A-Aktivität auf mehrere HANA-Datendateien verteilen, die sich auf verschiedenen Azure-Datenträgern befinden. Der Vorteil ist, dass Sie kein Stripesetvolume erstellen und verwalten müssen, dass sich über verschiedene Azure-Datenträger erstreckt. Die SAP HANA-Funktionalität der Datenvolumepartitionierung wird hier ausführlich beschrieben:

- [HANA-Administratorhandbuch](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [Blog zu SAP HANA-Datenvolumepartitionierung](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [SAP Note 2400005](https://launchpad.support.sap.com/#/notes/2400005)
- [SAP Note 2700123](https://launchpad.support.sap.com/#/notes/2700123)

Wenn Sie die Details lesen, wird Ihnen klar, dass die Nutzung dieser Funktionalität der Komplexität von Stripesets auf Volume-Manager-Basis ein Ende bereitet. Sie werden auch feststellen, dass die HANA-Datenvolumepartitionierung nicht nur bei Azure-Blockspeicher wie Azure Storage Premium funktioniert. Sie können diese Funktionalität auch zur Verteilung auf NFS-Freigaben verwenden, falls für diese Freigaben IOPS- oder Durchsatzeinschränkungen bestehen.  


## <a name="linux-io-scheduler-mode"></a>E/A-Scheduler-Modus für Linux
Linux verfügt über mehrere verschiedene E/A-Scheduling-Modi. Linux-Anbieter und SAP empfehlen im Allgemeinen, den E/A-Schedulermodus für Datenträgervolumes von **mq-deadline** oder **kyber** in **noop** (Non-Multiqueue) oder **none** (Multiqueue) zu ändern, sofern dies nicht bereits durch die Saptune-Profile von SLES erfolgt ist. Details finden Sie unter den folgenden Links: 

- [SAP-Hinweis 1984787](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP-Hinweis 2578899](https://launchpad.support.sap.com/#/notes/2578899) 
- [Probleme bei der noop-Einstellung in SLES 12 SP4](https://www.suse.com/support/kb/doc/?id=000019547)

Übernehmen Sie für Red Hat die Einstellungen für die verschiedenen SAP-Anwendungen wie von den spezifischen tune-Profilen festgelegt.


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Lösungen mit Storage Premium und Azure-Schreibbeschleunigung für virtuelle Azure-Computer der M-Serie
Die Azure-Schreibbeschleunigung ist eine Funktion, die ausschließlich für virtuelle Azure-Computer der M-Serie verfügbar ist. Der Name macht bereits deutlich, dass der Zweck der Funktion die Verbesserung der E/A-Wartezeit bei Schreibvorgängen für Azure Storage Premium ist. Für SAP HANA ist die Schreibbeschleunigung nur für das Volume **/hana/log** vorgesehen. **/hana/data** und **/hana/log** sind daher separate Volumes, und die Azure-Schreibbeschleunigung unterstützt nur das Volume **/hana/log**. 

> [!IMPORTANT]
> Bei Verwendung von Azure Storage Premium ist die Nutzung der [Schreibbeschleunigung](../../how-to-enable-write-accelerator.md) von Azure oder des Volumes **/hana/log** obligatorisch. Die Schreibbeschleunigung ist nur für Storage Premium und VMs der Serien M und Mv2 verfügbar. Die Schreibbeschleunigung funktioniert nicht in Kombination mit anderen Azure VM-Familien wie Esv3 oder Edsv4.

Bei den Cacheempfehlungen für Azure Premium-Datenträger unten werden E/A-Merkmale für SAP HANA gemäß der folgenden Liste zugrunde gelegt:

- Es gibt kaum Workload durch Lesezugriffe auf die HANA-Datendateien. Ausnahmen betreffen umfangreiche E/As nach dem Neustart der HANA-Instanz oder wenn Daten in HANA geladen werden. Ein anderer Fall umfangreicherer Lese-E/As für Datendateien sind Sicherungen der HANA-Datenbank. Das hat zur Folge, dass Lesecaches meistens nicht sinnvoll sind, da in den meisten dieser Fälle alle Volumes mit Datendateien vollständig gelesen werden müssen. 
- Das Schreiben in die Datendateien geschieht in Bursts auf der Grundlage der HANA-Sicherungspunkte und der HANA-Absturzwiederherstellung. Das Schreiben von Sicherungspunkten erfolgt asynchron und hält Benutzertransaktionen in keiner Weise auf. Beim Schreiben von Daten während der Wiederherstellung nach Abstürzen ist die Leistung ein kritischer Faktor, um schnell wieder zu einem reaktionsbereiten System zu kommen. Die Wiederherstellung nach Abstürzen dürfte allerdings eher eine Ausnahmesituation darstellen
- Es gibt kaum Lesevorgänge aus den HANA-Wiederholungsdateien. Ausnahmen bilden umfangreiche E/A-Vorgänge beim Ausführen von Sicherungen des Transaktionsprotokolls, der Wiederherstellung nach Abstürzen oder in der Neustartphase einer HANA-Instanz.  
- Der hauptsächliche Workload bei SAP HANA-Wiederholungsprotokolldateien besteht aus Schreibvorgängen. Abhängig von der Art des Workloads kann die Größe von E/As bis zu 4 KB herunter- oder in anderen Fällen bis zu 1 MB oder mehr heraufreichen. Wartezeiten beim Schreiben in das SAP HANA-Wiederholungsprotokoll sind ein kritischer Faktor für die Leistung.
- Alle Schreibvorgänge müssen zuverlässig und dauerhaft auf einem Datenträger gespeichert werden.

**Empfehlung: Aufgrund dieser E/A-Muster von SAP HANA sollte das Caching für die verschiedenen Volumes unter Verwendung von Azure Storage Premium wie folgt festgelegt werden:**

- **/hana/data** – Kein Caching oder Read-Caching
- **/hana/log** – keine Zwischenspeicherung: Ausnahme für VMs der M- und Mv2-Serie, für die Azure-Schreibbeschleunigung aktiviert sein sollte. 
- **/hana/shared** – Read-Caching
- **Betriebssystemdatenträger** – Ändern Sie nicht das Standardcaching, das von Azure zur Erstellungszeit der VM festgelegt wird.


Wenn Sie LVM oder mdadm verwenden, um Stripesets über mehrere Azure Premium-Datenträger zu erstellen, müssen Sie Stripegrößen definieren. Diese Größen unterscheiden sich zwischen **/hana/data** und **/hana/log**. **Empfehlung: Als Stripegrößen empfiehlt sich die Verwendung von:**

- 256 KB für **/hana/log**
- 64 KB für **/hana/log**

> [!NOTE]
> Die Stripegröße für **/hana/data** wurde abweichend von früheren Empfehlungen von 64 KB oder 128 KB auf der Grundlage von Kundenerfahrungen mit neueren Linux-Versionen in 256 KB geändert. Die Größe von 256 KB bietet eine etwas bessere Leistung. Wir haben auch die Empfehlung für Stripegrößen von **/hana/log** von 32 KB auf 64 KB geändert, um genügend Durchsatz mit größeren E/A-Größen zu erreichen.

> [!NOTE]
> Sie müssen keine Redundanzebenen mit RAID-Volumes konfigurieren, da der Azure-Blockspeicher drei Images einer virtuellen Festplatte beibehält. Die Verwendung eines Stripesets mit Azure Premium-Datenträgern dient lediglich dazu, Volumes zu konfigurieren, die einen ausreichenden IOPS- und/oder E/A-Durchsatz bieten.

Die Kumulierung einer Reihe von Azure-VHDs unter einem Stripeset ist für den IOPS- und Speicherdurchsatz kumulativ. Wenn Sie also ein Stripeset über drei Azure Storage Premium-P30-Datenträgern platzieren, erhalten Sie den dreifachen IOPS- und den dreifachen Speicherdurchsatz eines einzelnen Azure Storage Premium-P30-Datenträgers.

> [!IMPORTANT]
> Falls Sie LVM oder mdadm als Volume-Manager verwenden, um Stripesets über mehrere Azure Premium-Datenträger zu erstellen, dürfen die drei SAP HANA FileSystems „/data“, „/log“ und „/shared“ nicht in eine Standard- oder Root-Volumegruppe positioniert werden. Es wird dringend empfohlen, den Anweisungen des Linux-Anbieters zu folgen, die normalerweise besagen, einzelne Volumegruppen für „/data“, „/log“ und „/shared“ zu erstellen.


### <a name="azure-burst-functionality-for-premium-storage"></a>Azure-Burstfunktionalität für Storage Premium
Für Azure Premium-Datenträger mit einer Kapazität bis 512 GiB wird eine Burstfunktionalität angeboten. Die genaue Funktionsweise des Datenträgerbursting wird in dem Artikel [Datenträgerbursting](../../disk-bursting.md) beschrieben. Wenn Sie den Artikel lesen, verstehen Sie das Konzept des Anfallens von IOPS und Durchsatz in den Zeiten, in denen Ihre E/A-Workload unter den nominalen IOPS und unter dem Durchsatz der Datenträger liegt (Einzelheiten zum nominalen Durchsatz finden Sie unter [Verwaltete Datenträger – Preise](https://azure.microsoft.com/pricing/details/managed-disks/)). Sie werden das Delta von IOPS und Durchsatz zwischen Ihrer aktuellen Nutzung und den Nennwerten des Datenträgers ansammeln. Die Bursts sind auf maximal 30 Minuten begrenzt.

Die idealen Fälle, in denen diese Burstfunktionalität eingeplant werden kann, werden wahrscheinlich die Volumes oder Datenträger sein, die Datendateien für die verschiedenen DBMS enthalten. Die für diese Volumen zu erwartende E/A-Workload, insbesondere bei kleinen bis mittleren Systemen, wird voraussichtlich wie folgt aussehen:

- Geringe bis mittlere Leseworkload, da die Daten idealerweise im Arbeitsspeicher zwischengespeichert werden, oder sich wie im Fall von HANA vollständig im Arbeitsspeicher befinden sollten.
- Bursts von Schreibvorgängen, die durch Datenbankprüfpunkte oder Speicherpunkte ausgelöst werden, die regelmäßig ausgegeben werden.
- Sicherungsworkload, die in Fällen, in denen Sicherungen nicht über Speichermomentaufnahmen ausgeführt werden, einen kontinuierlichen Datenstrom einlesen.
- Für SAP HANA: Laden der Daten in den Arbeitsspeicher nach einem Neustart der Instanz.

Insbesondere auf kleineren DBMS-Systemen, auf denen Ihre Workload nur ein paar hundert Transaktionen pro Sekunde verarbeitet, kann eine solche Burstfunktionalität auch für die Datenträger oder Volumes sinnvoll sein, die das Transaktions- oder Wiederholungsprotokoll speichern. Die erwartete Workload für einen solchen Datenträger oder solche Volumes sieht wie folgt aus:

- Reguläre Schreibvorgänge auf dem Datenträger, die von der Workload abhängig sind, und die Art der Workload, da jeder von der Anwendung ausgegebene Commit wahrscheinlich einen E/A-Vorgang auslöst.
- Höhere Workloads beim Durchsatz für Fälle von Betriebsaufgaben, wie das Erstellen oder Wiederherstellen von Indizes.
- Bursts bei Lesevorgängen beim Durchführen der Sicherungen von Transaktions- oder Wiederholungsprotokollen.


### <a name="production-recommended-storage-solution-based-on-azure-premium-storage"></a>Von der Produktion empfohlene Speicherlösung basierend auf Azure Storage Premium.

> [!IMPORTANT]
> Die SAP HANA-Zertifizierung für virtuelle Computer der Azure M-Serie gilt ausschließlich mit der Azure-Schreibbeschleunigung für das Volume **/hana/log**. Folglich müssen SAP HANA-Bereitstellungen in Produktionsszenarien auf virtuellen Computern der Azure M-Serie für das Volume **/hana/log** mit der Azure-Schreibbeschleunigung konfiguriert werden.  

> [!NOTE]
> In Szenarien, die Azure Premium Storage einbeziehen, implementieren wir Burstfunktionen in die Konfiguration. Wenn Sie Speichertesttools in beliebiger Form oder Gestalt verwenden, denken Sie daran, wie das [Azure Premium-Datenträgerbursting](../../disk-bursting.md) funktioniert. Bei der Durchführung der Speichertests, die über das SAP-HWCCT- oder HCMT-Tool geliefert werden, gehen wir nicht davon aus, dass alle Tests die Kriterien erfüllen, da einige der Tests die Bursting-Guthaben überschreiten, die Sie akkumulieren können. Vor allem, wenn alle Tests nacheinander ohne Unterbrechung durchgeführt werden.


> [!NOTE]
> Überprüfen Sie für Produktionsszenarien in der [SAP-Dokumentation für IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html), ob ein bestimmter VM-Typ von SAP für SAP HANA unterstützt wird.

**Empfehlung: Die empfohlenen Konfigurationen mit Azure Storage Premium für Produktionsszenarien sehen wie folgt aus:**

Konfiguration für SAP **/hana/data**-Volume:

| VM-SKU | RAM | Maximal VM-E/A<br /> Throughput | /hana/data | Bereitgestellter Durchsatz | Maximaler Burstdurchsatz | IOPS | Burst-IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MBit/s | 4 x P6 | 200 MBit/s | 680 MBit/s | 960 | 14.000 |
| M32ls | 256 GiB | 500 MBit/s | 4 x P6 | 200 MBit/s | 680 MBit/s | 960 | 14.000 |
| M64ls | 512 GB | 1\.000 MBit/s | 4 x P10 | 400 MBit/s | 680 MBit/s | 2\.000 | 14.000 |
| M64s | 1\.000 GiB | 1\.000 MBit/s | 4 x P15 | 500 MBit/s | 680 MBit/s | 4\.400 | 14.000 |
| M64ms | 1\.750 GiB | 1\.000 MBit/s | 4 x P20 | 600 MBit/s | 680 MBit/s | 9\.200 | 14.000 |  
| M128s | 2\.000 GiB | 2\.000 MBit/s | 4 x P20 | 600 MBit/s | 680 MBit/s | 9\.200| 14.000 | 
| M128ms | 3\.800 GiB | 2\.000 MBit/s | 4 x P30 | 800 MBit/s | kein Bursting | 20.000 | kein Bursting | 
| M208s_v2 | 2\.850 GiB | 1\.000 MBit/s | 4 x P30 | 800 MBit/s | kein Bursting | 20.000| kein Bursting | 
| M208ms_v2 | 5\.700 GiB | 1\.000 MBit/s | 4 x P40 | 1\.000 MBit/s | kein Bursting | 30.000 | kein Bursting |
| M416s_v2 | 5\.700 GiB | 2\.000 MBit/s | 4 x P40 | 1\.000 MBit/s | kein Bursting | 30.000 | kein Bursting |
| M416ms_v2 | 11.400 GiB | 2\.000 MBit/s | 4 x P50 | 2\.000 MBit/s | kein Bursting | 30.000 | kein Bursting |


Für das Volume **/hana/log**. Die Konfiguration würde wie folgt aussehen:

| VM-SKU | RAM | Maximal VM-E/A<br /> Throughput | Volume **/hana/log** | Bereitgestellter Durchsatz | Maximaler Burstdurchsatz | IOPS | Burst-IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MBit/s | 3 x P10 | 300 MBit/s | 510 MBit/s | 1\.500 | 10.500 | 
| M32ls | 256 GiB | 500 MBit/s | 3 x P10 | 300 MBit/s | 510 MBit/s | 1\.500 | 10.500 | 
| M64ls | 512 GB | 1\.000 MBit/s | 3 x P10 | 300 MBit/s | 510 MBit/s | 1\.500 | 10.500 | 
| M64s | 1\.000 GiB | 1\.000 MBit/s | 3 x P15 | 375 MBit/s | 510 MBit/s | 3\.300 | 10.500 | 
| M64ms | 1\.750 GiB | 1\.000 MBit/s | 3 x P15 | 375 MBit/s | 510 MBit/s | 3\.300 | 10.500 |  
| M128s | 2\.000 GiB | 2\.000 MBit/s | 3 x P15 | 375 MBit/s | 510 MBit/s | 3\.300 | 10.500|  
| M128ms | 3\.800 GiB | 2\.000 MBit/s | 3 x P15 | 375 MBit/s | 510 MBit/s | 3\.300 | 10.500 | 
| M208s_v2 | 2\.850 GiB | 1\.000 MBit/s | 3 x P15 | 375 MBit/s | 510 MBit/s | 3\.300 | 10.500 |  
| M208ms_v2 | 5\.700 GiB | 1\.000 MBit/s | 3 x P15 | 375 MBit/s | 510 MBit/s | 3\.300 | 10.500 |  
| M416s_v2 | 5\.700 GiB | 2\.000 MBit/s | 3 x P15 | 375 MBit/s | 510 MBit/s | 3\.300 | 10.500 |  
| M416ms_v2 | 11.400 GiB | 2\.000 MBit/s | 3 x P15 | 375 MBit/s | 510 MBit/s | 3\.300 | 10.500 | 


Für die anderen Volumes würde die Konfiguration wie folgt aussehen:

| VM-SKU | RAM | Maximal VM-E/A<br /> Throughput | /hana/shared | /root volume | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MBit/s | 1 x P15 | 1 x P6 | 1 x P6 |
| M32ls | 256 GiB | 500 MBit/s |  1 x P15 | 1 x P6 | 1 x P6 |
| M64ls | 512 GB | 1\.000 MBit/s | 1 x P20 | 1 x P6 | 1 x P6 |
| M64s | 1\.000 GiB | 1\.000 MBit/s | 1 x P30 | 1 x P6 | 1 x P6 |
| M64ms | 1\.750 GiB | 1\.000 MBit/s | 1 x P30 | 1 x P6 | 1 x P6 | 
| M128s | 2\.000 GiB | 2\.000 MBit/s | 1 x P30 | 1 x P10 | 1 x P6 | 
| M128ms | 3\.800 GiB | 2\.000 MBit/s | 1 x P30 | 1 x P10 | 1 x P6 |
| M208s_v2 | 2\.850 GiB | 1\.000 MBit/s |  1 x P30 | 1 x P10 | 1 x P6 |
| M208ms_v2 | 5\.700 GiB | 1\.000 MBit/s | 1 x P30 | 1 x P10 | 1 x P6 | 
| M416s_v2 | 5\.700 GiB | 2\.000 MBit/s |  1 x P30 | 1 x P10 | 1 x P6 | 
| M416ms_v2 | 11.400 GiB | 2\.000 MBit/s | 1 x P30 | 1 x P10 | 1 x P6 | 


Überprüfen Sie, ob der Speicherdurchsatz für die verschiedenen vorgeschlagenen Volumes für die Workload ausreicht, die Sie ausführen möchten. Wenn die Workload größere Volumes für **/hana/data** und **/hana/log** erfordert, müssen Sie die Anzahl der Azure Storage Premium-VHDs erhöhen. Wenn Sie ein Volume mit mehr VHDs ausstatten als in der Liste angegeben, erhöht sich der IOPS- und E/A-Durchsatz innerhalb der Grenzen des Azure-VM-Typs.

Die Azure-Schreibbeschleunigung funktioniert nur in Verbindung mit [verwalteten Azure-Datenträgern](https://azure.microsoft.com/services/managed-disks/). Daher müssen zumindest die Azure Storage Premium-Datenträger, die das Volume **/hana/log** bilden, als verwaltete Datenträger bereitgestellt werden. Ausführlichere Anweisungen und Einschränkungen der Azure-Schreibbeschleunigung finden Sie im Artikel [Schreibbeschleunigung](../../how-to-enable-write-accelerator.md).

Für die HANA-zertifizierten VMs der Azure [Esv3](../../ev3-esv3-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#esv3-series)- und [Edsv4](../../edv4-edsv4-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#edsv4-series)-Familie müssen Sie für die Volumes **/hana/data** und **/hana/log** eine ANF erstellen. Oder Sie müssen nur für das Volume **/hana/log** Azure Disk Storage Ultra anstelle von Azure Storage Premium verwenden. Infolgedessen könnten die Konfigurationen für das Volume **/hana/data** in Azure Storage Premium wie folgt aussehen:

| VM-SKU | RAM | Maximal VM-E/A<br /> Throughput | /hana/data | Bereitgestellter Durchsatz | Maximaler Burstdurchsatz | IOPS | Burst-IOPS |
| --- | --- | --- | --- | --- | --- | --- |
| E20ds_v4 | 160 GiB | 480 MBit/s | 3 x P10 | 300 MBit/s | 510 MBit/s | 1\.500 | 10.500 |
| E32ds_v4 | 256 GiB | 768 MBit/s | 3 x P10 |  300 MBit/s | 510 MBit/s | 1\.500 | 10.500|
| E48ds_v4 | 384 GiB | 1\.152 MBit/s | 3 x P15 |  375 MBit/s |510 MBit/s | 3\.300  | 10.500 | 
| E64ds_v4 | 504 GiB | 1\.200 MBit/s | 3 x P15 |  375 MBit/s | 510 MBit/s | 3\.300 | 10.500 | 
| E64s_v3 | 432 GiB | 1\.200 MB/s | 3 x P15 |  375 MBit/s | 510 MBit/s | 3\.300 | 10.500 | 

Für die anderen Volumes, einschließlich **/hana/log** auf Disk Ultra, könnte die Konfiguration wie folgt aussehen:

| VM-SKU | RAM | Maximal VM-E/A<br /> Throughput | Volume „/hana/log“ | E/A-Durchsatz für „/hana/log“ | IOPS für „/hana/log“ | /hana/shared | /root volume | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MBit/s | 80 GB | 250 MBit/s | 1\.800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E32ds_v4 | 256 GiB | 768 MBit/s | 128 GB | 250 MBit/s | 1\.800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E48ds_v4 | 384 GiB | 1\.152 MBit/s | 192 GB | 250 MBit/s | 1\.800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64ds_v4 | 504 GiB | 1\.200 MBit/s | 256 GB | 250 MBit/s | 1\.800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64s_v3 | 432 GiB | 1\.200 MBit/s | 220 GB | 250 MBit/s | 1\.800 | 1 x P20 | 1 x P6 | 1 x P6 |


## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Azure Ultra-Datenträgerspeicherkonfiguration für SAP HANA
Ein weiterer Azure-Speichertyp wird [Azure Disk Ultra](../../disks-types.md#ultra-disk) genannt. Der wesentliche Unterschied zwischen dem bisher angebotenen Azure-Speicher und einem Ultra-Datenträger besteht darin, dass die Datenträgermerkmale nicht mehr an die Größe des Datenträgers gebunden sind. Als Kunde können Sie diese Merkmale für einen Ultra-Datenträger definieren:

- Größe eines Datenträgers zwischen 4 GiB und 65.536 GiB
- IOPS-Bereich zwischen 100 IOPS und 160.000 IOPS (Höchstwert abhängig von VM-Typen)
- Speicherdurchsatz zwischen 300 MB/s und 2.000 MB/s

Mit einem Ultra-Datenträger können Sie einen einzelnen Datenträger definieren, der Ihre Anforderungen hinsichtlich Größe, IOPS und Datenträgerdurchsatz erfüllt. Sie müssen also keine Manager für logische Volumes wie LVM oder MDADM zusätzlich zu Azure Storage Premium mehr verwenden, um Volumes zu erstellen, die Ihre Anforderungen an IOPS und Durchsatz erfüllen. Sie können eine Konfigurationsmischung aus Disk Ultra und Storage Premium ausführen. Auf diese Weise können Sie die Verwendung von Disk Ultra auf die leistungskritischen Volumes **/hana/data** und **/hana/log** beschränken und die anderen Volumes mit Azure Storage Premium nutzen.

Ein weiterer Vorteil von Disk Ultra kann auch die bessere Leselatenz im Vergleich mit Storage Premium sein. Eine geringere Leselatenz kann auch Vorteile mit sich bringen, wenn Sie die HANA-Startdauern und das nachfolgende Laden der Daten in den Arbeitsspeicher verkürzen möchten. Weitere Vorteile von Disk Storage Ultra können sich zudem ergeben, wenn von HANA Sicherungspunkte geschrieben werden. 

> [!NOTE]
> Ultra-Datenträger sind noch nicht in allen Azure-Regionen vorhanden und unterstützen noch nicht alle unten aufgeführten VM-Typen. Ausführliche Informationen dazu, wo Ultra-Datenträger verfügbar sind und welche VM-Familien unterstützt werden, finden Sie im Artikel [Welche Datenträgertypen stehen in Azure zur Verfügung?](../../disks-types.md#ultra-disk).

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Für die Produktion empfohlene Speicherlösung mit reiner Ultra-Datenträgerkonfiguration
Bei dieser Konfiguration bleiben die Volumes **/hana/data** und **/hana/log** getrennt. Die vorgeschlagenen Werte werden von den KPIs abgeleitet, die von SAP zur Zertifizierung von VM-Typen für SAP HANA und Speicherkonfigurationen verwendet werden (gemäß Empfehlung im [SAP TDI Storage-Whitepaper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)).

Bei den Empfehlungen werden die Mindestanforderungen für SAP häufig überschritten, wie bereits in diesem Artikel erwähnt. Bei den aufgeführten Empfehlungen handelt es sich um einen Kompromiss zwischen den Größenempfehlungen von SAP und dem maximalen Speicherdurchsatz, der von den unterschiedlichen VM-Typen bereitgestellt wird.

> [!NOTE]
> Azure Ultra-Datenträger erzwingt mindestens 2 IOPS pro Gigabyte eines Datenträgers.


| VM-SKU | RAM | Maximal VM-E/A<br /> Throughput | Volume „/hana/data“ | E/A-Durchsatz für „/hana/data“ | IOPS für „/hana/data“ | Volume „/hana/log“ | E/A-Durchsatz für „/hana/log“ | IOPS für „/hana/log“ |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MB/s | 200 GB | 400 MBit/s | 2\.500 | 80 GB | 250 MB | 1\.800 |
| E32ds_v4 | 256 GiB | 768 MB/s | 300 GB | 400 MBit/s | 2\.500 | 128 GB | 250 MBit/s | 1\.800 |
| E48ds_v4 | 384 GiB | 1152 MB/s | 460 GB | 400 MBit/s | 3,000 | 192 GB | 250 MBit/s | 1\.800 |
| E64ds_v4 | 504 GiB | 1200 MB/s | 610 GB | 400 MBit/s | 3\.500 |  256 GB | 250 MBit/s | 1\.800 |
| E64s_v3 | 432 GiB | 1\.200 MB/s | 610 GB | 400 MBit/s | 3\.500 | 220 GB | 250 MB | 1\.800 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 MBit/s | 2\.500 | 96 GB | 250 MBit/s  | 1\.800 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 MBit/s | 2\.500 | 256 GB | 250 MBit/s  | 1\.800 |
| M64ls | 512 GB | 1\.000 MB/s | 620 GB | 400 MBit/s | 3\.500 | 256 GB | 250 MBit/s  | 1\.800 |
| M64s | 1\.000 GiB | 1\.000 MB/s |  1\.200 GB | 600 MBit/s | 5\.000 | 512 GB | 250 MBit/s  | 2\.500 |
| M64ms | 1\.750 GiB | 1\.000 MB/s | 2\.100 GB | 600 MBit/s | 5\.000 | 512 GB | 250 MBit/s  | 2\.500 |
| M128s | 2\.000 GiB | 2\.000 MB/s |2\.400 GB | 750 MBit/s | 7\.000 | 512 GB | 250 MBit/s  | 2\.500 | 
| M128ms | 3\.800 GiB | 2\.000 MB/s | 4\.800 GB | 750 MBit/s |9\.600 | 512 GB | 250 MBit/s  | 2\.500 | 
| M208s_v2 | 2\.850 GiB | 1\.000 MB/s | 3\.500 GB | 750 MBit/s | 7\.000 | 512 GB | 250 MBit/s  | 2\.500 | 
| M208ms_v2 | 5\.700 GiB | 1\.000 MB/s | 7\.200 GB | 750 MBit/s | 14\.400 | 512 GB | 250 MBit/s  | 2\.500 | 
| M416s_v2 | 5\.700 GiB | 2\.000 MB/s | 7\.200 GB | 1\.000 MBit/s | 14\.400 | 512 GB | 400 MBit/s  | 4\.000 | 
| M416ms_v2 | 11.400 GiB | 2\.000 MB/s | 14.400 GB | 1\.500 MBit/s | 28.800 | 512 GB | 400 MBit/s  | 4\.000 |   

**Die angegebenen Werte sind lediglich als Ausgangspunkt gedacht und müssen auf die tatsächlichen Anforderungen abgestimmt werden.** Der Vorteil eines Azure Ultra-Datenträgers besteht darin, dass die Werte für IOPS und Durchsatz angepasst werden können, ohne den virtuellen Computer herunterzufahren oder die im System verarbeitete Workload anzuhalten.   

> [!NOTE]
> Bisher sind noch keine Speichermomentaufnahmen mit Ultra-Datenträgerspeicher verfügbar. Dadurch wird die Verwendung von VM-Momentaufnahmen mit Azure Backup-Diensten blockiert.


## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>NFS v4.1-Volumes unter Azure NetApp Files
Ausführliche Informationen zu ANF für Hana finden Sie im Dokument [NFS v4.1-Volumes unter Azure NetApp Files für SAP HANA](./hana-vm-operations-netapp.md).




## <a name="cost-conscious-solution-with-azure-premium-storage"></a>Kostengünstige Lösung mit Azure Storage Premium
Bisher wurde die in diesem Dokument im Abschnitt [Lösungen mit Storage Premium und Azure-Schreibbeschleunigung für virtuelle Azure-Computer der M-Serie](#solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines) beschriebene Azure Storage Premium-Lösung für SAP HANA-Szenarien konzipiert, die für die Produktion unterstützt werden. Ein Merkmal der Konfigurationen, die für die Produktion unterstützt werden können, ist die Nutzung von zwei separaten Volumes für SAP HANA-Daten und -Wiederholungsprotokoll. Der Grund für eine solche Trennung ist, dass sich die Workloadmerkmale der Volumes unterscheiden. Mit den empfohlenen Produktionskonfigurationen könnten auch eine andere Art der Zwischenspeicherung oder sogar verschiedene Arten von Azure-Blockspeicher erforderlich sein. In Szenarien, die nicht für die Produktion bestimmt sind, gelten einige der Überlegungen für Produktionssysteme möglicherweise nicht für Low End-Systeme, die nicht für die Produktion bestimmt sind. In der Folge könnten das Daten- und Protokollvolume für HANA kombiniert werden. Wenn auch mit ein paar Schwachstellen, wie z. B. letztendlich einen bestimmten Durchsatz oder Latenz-KPIs nicht zu erreichen, die für Produktionssysteme erforderlich sind. Ein weiterer Aspekt bei der Reduzierung der Kosten in solchen Umgebungen kann die Verwendung von [Azure SSD Standard-Speicher](./planning-guide-storage.md#azure-standard-ssd-storage) sein. Beachten Sie, dass sich die Auswahl von Azure-Speicher vom Typ SSD Standard oder HDD Standard auf Ihre SLAs für einzelne VMs auswirkt. Dies ist im Artikel [SLA für Virtuelle Computer](https://azure.microsoft.com/support/legal/sla/virtual-machines) dokumentiert.

Eine kostengünstigere Alternative für derartige Konfigurationen könnte wie folgt aussehen:


| VM-SKU | RAM | Maximal VM-E/A<br /> Throughput | „/hana/data“ und „/hana/log“<br /> Striping mit LVM oder MDADM | /hana/shared | /root volume | /usr/sap | comments |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | Erzielt keine Speicherlatenz unter 1 ms<sup>1</sup> |
| E16v3 | 128 GB | 384 MB/s | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | VM-Typ nicht HANA-zertifiziert <br /> Erzielt keine Speicherlatenz unter 1 ms<sup>1</sup> |
| M32ts | 192 GiB | 500 MB/s | 3 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Verwendung der Schreibbeschleunigung für kombiniertes Daten- und Protokollvolume beschränkt die IOPS-Rate auf 5.000<sup>2</sup> |
| E20ds_v4 | 160 GiB | 480 MB/s | 4 x P6 | 1 x E15 | 1 x E6 | 1 x E6 | Erzielt keine Speicherlatenz unter 1 ms<sup>1</sup> |
| E32v3 | 256 GiB | 768 MB/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | VM-Typ nicht HANA-zertifiziert <br /> Erzielt keine Speicherlatenz unter 1 ms<sup>1</sup> |
| E32ds_v4 | 256 GiB | 768 MBit/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Erzielt keine Speicherlatenz unter 1 ms<sup>1</sup> |
| M32ls | 256 GiB | 500 MB/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Verwendung der Schreibbeschleunigung für kombiniertes Daten- und Protokollvolume beschränkt die IOPS-Rate auf 5.000<sup>2</sup> |
| E48ds_v4 | 384 GiB | 1\.152 MBit/s | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Erzielt keine Speicherlatenz unter 1 ms<sup>1</sup> |
| E64v3 | 432 GiB | 1\.200 MB/s | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Erzielt keine Speicherlatenz unter 1 ms<sup>1</sup> |
| E64ds_v4 | 504 GiB | 1200 MB/s |  7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Erzielt keine Speicherlatenz unter 1 ms<sup>1</sup> |
| M64ls | 512 GB | 1\.000 MB/s | 7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Verwendung der Schreibbeschleunigung für kombiniertes Daten- und Protokollvolume beschränkt die IOPS-Rate auf 10.000<sup>2</sup> |
| M64s | 1\.000 GiB | 1\.000 MB/s | 7 x P15 | 1 × E30 | 1 x E6 | 1 x E6 | Verwendung der Schreibbeschleunigung für kombiniertes Daten- und Protokollvolume beschränkt die IOPS-Rate auf 10.000<sup>2</sup> |
| M64ms | 1\.750 GiB | 1\.000 MB/s | 6 x P20 | 1 × E30 | 1 x E6 | 1 x E6 | Verwendung der Schreibbeschleunigung für kombiniertes Daten- und Protokollvolume beschränkt die IOPS-Rate auf 10.000<sup>2</sup> |
| M128s | 2\.000 GiB | 2\.000 MB/s |6 x P20 | 1 × E30 | 1 x E10 | 1 x E6 | Verwendung der Schreibbeschleunigung für kombiniertes Daten- und Protokollvolume beschränkt die IOPS-Rate auf 20.000<sup>2</sup> |
| M208s_v2 | 2\.850 GiB | 1\.000 MB/s | 4 x P30 | 1 × E30 | 1 x E10 | 1 x E6 | Verwendung der Schreibbeschleunigung für kombiniertes Daten- und Protokollvolume beschränkt die IOPS-Rate auf 10.000<sup>2</sup> |
| M128ms | 3\.800 GiB | 2\.000 MB/s | 5 x P30 | 1 × E30 | 1 x E10 | 1 x E6 | Verwendung der Schreibbeschleunigung für kombiniertes Daten- und Protokollvolume beschränkt die IOPS-Rate auf 20.000<sup>2</sup> |
| M208ms_v2 | 5\.700 GiB | 1\.000 MB/s | 4 x P40 | 1 × E30 | 1 x E10 | 1 x E6 | Verwendung der Schreibbeschleunigung für kombiniertes Daten- und Protokollvolume beschränkt die IOPS-Rate auf 10.000<sup>2</sup> |
| M416s_v2 | 5\.700 GiB | 2\.000 MB/s | 4 x P40 | 1 × E30 | 1 x E10 | 1 x E6 | Verwendung der Schreibbeschleunigung für kombiniertes Daten- und Protokollvolume beschränkt die IOPS-Rate auf 20.000<sup>2</sup> |
| M416ms_v2 | 11400 GiB | 2\.000 MB/s | 7 x P40 | 1 × E30 | 1 x E10 | 1 x E6 | Verwendung der Schreibbeschleunigung für kombiniertes Daten- und Protokollvolume beschränkt die IOPS-Rate auf 20.000<sup>2</sup> |


<sup>1</sup> [Azure-Schreibbeschleunigung](../../how-to-enable-write-accelerator.md) kann nicht mit den Ev4- und Ev4 VM-Familien verwendet werden. Aufgrund der Verwendung von Azure Storage Premium liegt die E/A-Latenzzeit nicht unter 1 ms.

<sup>2</sup> Die VM-Familie unterstützt die [Azure-Schreibbeschleunigung](../../how-to-enable-write-accelerator.md), aber der IOPS-Grenzwert der Schreibbeschleunigung könnte die IOPS-Fähigkeiten der Datenträgerkonfigurationen einschränken.

Wenn Sie das Daten- und Protokollvolume für SAP Hana kombinieren, sollte für die Datenträger, die das Stripesetvolume bilden, kein Lese- oder Lese-/Schreibcache aktiviert sein.

Es sind VM-Typen aufgelistet, die nicht mit SAP zertifiziert sind und als solche nicht im sogenannten [SAP HANA-Hardwareverzeichnis](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) aufgelistet sind. Laut Kundenfeedback wurden diese nicht aufgelisteten VM-Typen erfolgreich für einige nicht zur Produktion zählenden Aufgaben verwendet.


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter

- [Hochverfügbarkeit von SAP HANA für virtuelle Azure-Computer](./sap-hana-availability-overview.md).