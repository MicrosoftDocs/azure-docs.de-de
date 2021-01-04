---
title: Azure-Speichertypen für SAP-Workloads
description: Planen von Azure-Speichertypen für SAP-Workloads
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/26/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6982b782fdd6b5b269c1562c54be3478c58bbce9
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500996"
---
# <a name="azure-storage-types-for-sap-workload"></a>Azure Storage-Typen für die SAP-Workload
Azure umfasst zahlreiche Speichertypen, die sich in den Funktionen, dem Durchsatz, der Latenz und den Preisen stark unterscheiden. Einige der Speichertypen sind für SAP-Szenarien nicht oder nur eingeschränkt verwendbar. Dagegen sind verschiedene Azure-Speichertypen für spezifische SAP-Workloadszenarien gut geeignet und optimiert. Speziell für SAP HANA wurden einige Azure-Speichertypen für die Verwendung mit SAP HANA zertifiziert. In diesem Dokument werden die verschiedenen Speichertypen erläutert und ihre Funktionen und Verwendbarkeit mit SAP-Workloads und SAP-Komponenten beschrieben.

Anmerkung zu den in diesem Artikel verwendeten Einheiten: Die Anbieter von öffentlichen Clouds sind dazu übergegangen, GiB ([Gibibyte](https://en.wikipedia.org/wiki/Gibibyte)) oder TiB ([Tebibyte](https://en.wikipedia.org/wiki/Tebibyte) anstelle von Gigabyte oder Terabyte als Größeneinheiten zu verwenden. Daher werden diese Einheiten in der gesamten Dokumentation und Preisgestaltung für Azure verwendet.  Im gesamten Dokument wird ausschließlich auf die Größeneinheiten MiB, GiB und TiB Bezug genommen. Möglicherweise möchten Sie mit MB, GB und TB planen. Dann sind einige kleine Unterschiede in den Berechnungen zu beachten, wenn Sie die Größenanpassung für einen Durchsatz von 400 MiB/s anstatt für einen Durchsatz von 250 MiB/s vornehmen möchten.

## <a name="microsoft-azure-storage-resiliency"></a>Microsoft Azure Storage – Resilienz

Bei der Microsoft Azure-Speicherung von HDD Standard, SSD Standard, Azure Storage Premium und Disk Ultra werden die Basis-VHD (mit dem Betriebssystem) und die an VMs angefügten Datenträger oder Daten-VHDs in drei Kopien in drei verschiedenen Speicherknoten aufbewahrt. Das Failover zu einem anderen Replikat und das Seeding eines neuen Replikats bei einem Ausfall eines Speicherknotens erfolgt transparent. Aufgrund dieser Redundanz ist es **NICHT** erforderlich, eine Speicherredundanzschicht für mehrere Azure-Datenträger zu verwenden. Das wird als „lokaler redundanter Speicher „ (LRS) bezeichnet. LRS ist die Standardeinstellung für diese Azure-Speichertypen. [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) bietet ausreichend Redundanz, um die gleichen SLAs wie bei anderen nativen Azure-Speichern zu erreichen.

Es gibt mehrere weitere Redundanzmethoden, die im Artikel [Azure Storage-Replikation](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) beschrieben werden und für einige der verschiedenen Speichertypen von Azure gelten. 

Beachten Sie auch, dass sich unterschiedliche Azure-Speichertypen auf die SLAs zur Verfügbarkeit einzelner VMs auswirken (wie unter [SLA für Virtuelle Computer](https://azure.microsoft.com/support/legal/sla/virtual-machines) beschrieben).

### <a name="azure-managed-disks"></a>Verwaltete Azure-Datenträger

Bei verwalteten Datenträgern handelt es sich um einen Ressourcentyp in Azure Resource Manager, der anstelle von in Azure Storage-Konten gespeicherten VHDs verwendet werden kann. Verwaltete Datenträger können automatisch an die [Verfügbarkeitsgruppe][virtual-machines-manage-availability] des virtuellen Computers, mit dem sie verbunden sind, angepasst werden und erhöhen daher die Verfügbarkeit des virtuellen Computers sowie der Dienste, die auf dem virtuellen Computer ausgeführt werden. Weitere Informationen finden Sie [in diesem allgemeinen Artikel](../../managed-disks-overview.md).

In Bezug auf die Resilienz veranschaulicht folgendes Beispiel den Vorteil von verwalteten Datenträgern:

- Sie stellen zwei virtuelle DBMS-Computer für Ihr SAP-System in einer Azure-Verfügbarkeitsgruppe bereit. 
- Wenn die virtuellen Computer in Azure bereitgestellt werden, wird der Datenträger mit dem Betriebssystemimage in einem anderen Speichercluster platziert. Dadurch wird vermieden, dass beide virtuelle Computer durch ein Problem eines einzelnen Azure-Speicherclusters beeinträchtigt werden.
- Wenn Sie neue verwaltete Datenträger erstellen, die Sie diesen virtuellen Computern zuweisen, um die Daten- und Protokolldateien der Datenbank zu speichern, werden diese neuen Datenträger für die beiden virtuellen Computer ebenfalls in separaten Speicherclustern bereitgestellt. Die Datenträger des ersten virtuellen Computers nutzen somit keine Speichercluster gemeinsam mit den Datenträgern des zweiten virtuellen Computers.

Bei der Bereitstellung ohne verwaltete Datenträger in kundendefinierten Speicherkonten erfolgt die Datenträgerzuordnung willkürlich, und es wird nicht berücksichtigt, dass virtuelle Computer aus Gründen der Resilienz innerhalb einer Verfügbarkeitsgruppe bereitgestellt werden.

> [!NOTE]
> Aus diesem Grund und aufgrund einiger weiterer Verbesserungen, die ausschließlich über verwaltete Datenträger verfügbar sind, müssen bei neuen Bereitstellungen von virtuellen Computern, auf denen Azure-Blockspeicher für die zugehörigen Datenträger verwendet wird (alle Azure-Speicher außer Azure NetApp Files), verwaltete Azure-Datenträger für die Basis-VHD sowie die Betriebssystemdatenträger und Datenträger verwendet werden, die SAP-Datenbankdateien enthalten. Dies gilt unabhängig davon, ob Sie die virtuellen Computer über Verfügbarkeitsgruppen oder Verfügbarkeitszonen bereitstellen, und unabhängig von den verwendeten Gruppen und Zonen. Datenträger zum Speichern von Sicherungen müssen nicht unbedingt als verwaltete Datenträger verwendet werden.

> [!NOTE]
> Verwaltete Azure-Datenträger bieten nur lokale Redundanz (LRS). 


## <a name="storage-scenarios-with-sap-workloads"></a>Speicherszenarien mit SAP-Workloads
Persistente Speicherung wird in der SAP-Workload in verschiedenen Komponenten des Stapels benötigt, den Sie in Azure bereitstellen. Diese Szenarien umfassen mindestens Folgendes:

- Persistente Basis-VHD des virtuellen Computers, die das Betriebssystem und andere Software enthält, die Sie auf diesem Datenträger installieren. Dieser Datenträger bzw. diese VHD ist der Stamm des virtuellen Computers. Alle daran vorgenommenen Änderungen müssen persistent gespeichert werden. Wenn Sie den virtuellen Computer das nächste Mal beenden und neu starten, sind damit alle zuvor vorgenommenen Änderungen weiterhin vorhanden. Dies gilt insbesondere in den Fällen, in denen der virtuelle Computer in Azure auf einem anderen Host als dem Host bereitgestellt wird, auf dem er ursprünglich ausgeführt wurde.
- Persistente Datenträger für Daten. Diese Datenträger sind VHDs, die Sie zum Speichern von Anwendungsdaten anfügen. Bei den Anwendungsdaten kann es sich um Daten- und Protokoll- oder Wiederholungsdateien einer Datenbank, um Sicherungsdateien oder um Softwareinstallationen handeln. Dies sind alle Datenträger außer der Basis-VHD, die das Betriebssystem enthält.
- Dateifreigaben oder freigegebene Datenträger, die das globale Transportverzeichnis für NetWeaver oder S/4HANA enthalten. Der Inhalt dieser Freigaben wird in Software genutzt, die auf mehreren virtuellen Computern ausgeführt wird, oder zum Erstellen von Szenarien mit Hochverfügbarkeits-Failoverclustern.
- Das Verzeichnis „/sapmnt“ oder allgemeine Dateifreigaben für EDI-Prozesse oder ähnliche Prozesse. Der Inhalt dieser Freigaben wird in Software genutzt, die auf mehreren virtuellen Computern ausgeführt wird, oder zum Erstellen von Szenarien mit Hochverfügbarkeits-Failoverclustern.

In den nächsten Abschnitten werden die verschiedenen Azure-Speichertypen und ihre Verwendbarkeit für SAP-Workloads erläutert, die für die vier oben genannten Szenarios gelten. Eine allgemeine Kategorisierung zur Verwendung der verschiedenen Azure-Speichertypen finden Sie im Artikel [Welche Datenträgertypen stehen in Azure zur Verfügung?](../../disks-types.md) Die Empfehlungen für die Verwendung der verschiedenen Azure-Speichertypen für SAP-Workloads unterscheiden sich davon nicht wesentlich.

Informationen zu Einschränkungen der Unterstützung für Azure-Speichertypen für SAP NetWeaver und die Anwendungsschicht von S/4HANA finden Sie im [SAP-Supporthinweis 2015553](https://launchpad.support.sap.com/#/notes/2015553). Informationen zur SAP HANA-Zertifizierung und den unterstützten Azure-Speichertypen finden Sie im Artikel [SAP HANA: Speicherkonfigurationen für virtuelle Azure-Computer](./hana-vm-operations-storage.md).

In den Abschnitten, in denen die verschiedenen Azure-Speichertypen beschrieben werden, erhalten Sie weitere Hintergrundinformationen zu den Einschränkungen und Möglichkeiten der Verwendung der von SAP unterstützten Speicherung. 

## <a name="storage-recommendations-for-sap-storage-scenarios"></a>Speicherempfehlungen für SAP-Speicherszenarien
Bevor auf die Einzelheiten eingegangen wird, werden zunächst die Zusammenfassung und die Empfehlungen vorgestellt. Die Details zu den einzelnen Azure-Speichertypen folgen auf diesen Abschnitt. Die Speicherempfehlungen für die SAP-Speicherszenarien sind in der folgenden Tabelle zusammengefasst:

| Verwendungsszenario | HDD Standard | SSD Standard | Storage Premium | Ultra-Datenträger | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| Betriebssystem-Datenträger | nicht geeignet |  eingeschränkt geeignet (nicht in der Produktion) | empfohlen | nicht möglich | nicht möglich |
| Globales Transportverzeichnis | Nicht unterstützt | Nicht unterstützt | empfohlen | empfohlen | empfohlen |
| /sapmnt | nicht geeignet | eingeschränkt geeignet (nicht in der Produktion) | empfohlen | empfohlen | empfohlen |
| DBMS-Datenvolume, SAP HANA, M/Mv2-VM-Familien | Nicht unterstützt | Nicht unterstützt | empfohlen | empfohlen | empfohlen<sup>2</sup> |
| DBMS-Protokollvolume, SAP HANA, M/Mv2-VM-Familien | Nicht unterstützt | Nicht unterstützt | empfohlen<sup>1</sup> | empfohlen | empfohlen<sup>2</sup> | 
| DBMS-Datenvolume, SAP HANA, Esv3/Edsv4-VM-Familien | Nicht unterstützt | Nicht unterstützt | empfohlen | empfohlen | empfohlen<sup>2</sup> |
| DBMS-Protokollvolume, SAP HANA, Esv3/Edsv4-VM-Familien | Nicht unterstützt | Nicht unterstützt | Nicht unterstützt | empfohlen | empfohlen<sup>2</sup> | 
| DBMS-Datenvolume, Nicht-HANA | Nicht unterstützt | eingeschränkt geeignet (nicht in der Produktion) | empfohlen | empfohlen | Nicht unterstützt |
| DBMS-Protokollvolume, Nicht-HANA, M/Mv2-VM-Familien | Nicht unterstützt | eingeschränkt geeignet (nicht in der Produktion) | empfohlen<sup>1</sup> | empfohlen | Nicht unterstützt |
| DBMS-Protokollvolume, Nicht-HANA, Nicht-M/Mv2-VM-Familien | Nicht unterstützt | eingeschränkt geeignet (nicht in der Produktion) | geeignet für bis zu mittlerer Workload | empfohlen | Nicht unterstützt |


<sup>1</sup> Mit Verwendung der [Azure-Schreibbeschleunigung](../../how-to-enable-write-accelerator.md) für M/Mv2-VM-Familien für Protokoll- und Wiederholungsprotokollvolumes <sup>2</sup> Für die Verwendung von ANF müssen „/hana/data“ und „/hana/log“ in ANF enthalten sein. 

Merkmale der verschiedenen Speichertypen:

| Verwendungsszenario | HDD Standard | SSD Standard | Storage Premium | Ultra-Datenträger | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| Durchsatz/IOPS-SLA | nein | nein | ja | ja | ja |
| Latenz Lesevorgänge | high | mittel bis hoch | niedrig | unter einer Millisekunde | unter einer Millisekunde |
| Latenz Schreibvorgänge | high | mittel bis hoch  | niedrig (unter einer Millisekunde<sup>1</sup>) | unter einer Millisekunde | unter einer Millisekunde |
| Von HANA unterstützt | nein | nein | ja<sup>1</sup> | ja | ja |
| Datenträger-Momentaufnahmen möglich | ja | ja | ja | nein | ja |
| Zuordnung von Datenträgern in verschiedenen Speicherclustern bei Verwendung von Verfügbarkeitsgruppen | über verwaltete Datenträger | über verwaltete Datenträger | über verwaltete Datenträger | Datenträgertyp wird mit über Verfügbarkeitsgruppen bereitgestellten virtuellen Computern nicht unterstützt | nein<sup>3</sup> |
| Angepasst an Verfügbarkeitszonen | ja | ja | ja | ja | erfordert Unterstützung von Microsoft |
| Zonenredundanz | nicht für verwaltete Datenträger | nicht für verwaltete Datenträger | nicht für verwaltete Datenträger | nein | nein |
| Georedundanz | nicht für verwaltete Datenträger | nicht für verwaltete Datenträger | nein | nein | nein |


<sup>1</sup> Mit Verwendung der [Azure-Schreibbeschleunigung](../../how-to-enable-write-accelerator.md) für M/Mv2-VM-Familien für Protokoll- und Wiederholungsprotokollvolumes

<sup>2</sup> Kosten hängen von bereitgestellten IOPS und Durchsatz ab.

<sup>3</sup> Die Erstellung verschiedener ANF-Kapazitätspools garantiert nicht die Bereitstellung von Kapazitätspools in unterschiedlichen Speichereinheiten.


> [!IMPORTANT]
> Um eine E/A-Latenz von weniger als 1 Millisekunde unter Verwendung von Azure NetApp Files (ANF) zu erreichen, müssen Sie sich an Microsoft wenden, um die richtige Platzierung zwischen den virtuellen Computern und den NFS-Freigaben basierend auf ANF zu regeln. Bislang gibt es keinen Mechanismus, der eine automatische Näherung zwischen einem bereitgestellten virtuellen Computer und den in ANF gehosteten NFS-Volumes ermöglicht. Aufgrund der unterschiedlichen Einrichtung der unterschiedlichen Azure-Regionen kann die zusätzliche Netzwerklatenz die E/A-Latenz über 1 Millisekunde erhöhen, wenn der virtuelle Computer und die NFS-Freigabe nicht in der Nähe zugeordnet werden.


> [!IMPORTANT]
> Weder die derzeit angebotenen auf Azure-Blockspeichern basierenden verwalteten Datenträger noch Azure NetApp Files bieten Zonen- oder Georedundanz. Daher müssen Sie sicherstellen, dass sich Ihre Architektur für Hochverfügbarkeit und Notfallwiederherstellung für diese verwalteten Datenträger, NFS- oder SMB-Freigaben nicht auf einen Typ der nativen Azure-Speicherreplikation stützt.


## <a name="azure-premium-storage"></a>Azure Storage Premium
Azure Storage SSD Premium wurde mit dem Ziel eingeführt, Folgendes bereitzustellen:

* Niedrige E/A-Latenz
* SLAs für IOPS und Durchsatz
* Weniger variierende E/A-Latenz

Dieser Speichertyp ist auf DBMS-Workloads, Speicherdatenverkehr, der eine niedrige Latenz im einstelligen Millisekundenbereich erfordert, und SLAs für IOPS und Durchsatz ausgerichtet. Kostenbasis im Fall von Azure Storage Premium ist nicht das tatsächliche auf diesen Datenträgern gespeicherte Datenvolumen, sondern die Größenkategorie des Datenträgers, unabhängig von der auf dem Datenträger gespeicherten Datenmenge. Sie können auch Datenträger in Storage Premium erstellen, die den im Artikel [SSD Premium](../../disks-types.md#premium-ssd) aufgeführten Größenkategorien nicht direkt zugeordnet sind. Aus diesem Artikel ergeben sich folgende Schlussfolgerungen:

- Der Speicher ist in Bereichen organisiert. Datenträger im Kapazitätsbereich von 513 GiB bis 1.024 GiB haben beispielsweise die gleichen Funktionen und die gleichen monatlichen Kosten.
- Die IOPS pro GiB verteilen sich nicht linear über die Größenkategorien. Kleinere Datenträger unter 32 GiB haben höhere IOPS-Raten pro GiB. Bei Datenträgern über 32 GiB bis 1.024 GiB liegt die IOPS-Rate pro GiB zwischen 4–5 IOPS pro GiB. Bei größeren Datenträgern bis zu 32.767 GiB sinkt die IOPS-Rate pro GiB unter 1.
- Der E/A-Durchsatz für diesen Speicher ist nicht linear zur Größe der Datenträgerkategorie. Bei kleineren Datenträgern, z. B. der Kategorie mit einer Kapazität zwischen 65 GiB und 128 GiB, beträgt der Durchsatz ungefähr 780 KB/GiB. Bei den extrem großen Datenträgern, z. B. mit 32.767 GiB, liegt der Durchsatz hingegen bei etwa 28 KB/GiB.
- Die IOPS- und Durchsatz-SLAs können nicht geändert werden, ohne die Kapazität des Datenträgers zu ändern.


Die Funktionsmatrix für die SAP-Workload sieht folgendermaßen aus:

| Funktion| Comment| Hinweise/Links | 
| --- | --- | --- | 
| Basis-VHD für Betriebssystem | geeignet | alle Systeme |
| Datenträger | geeignet | alle Systeme – [speziell für SAP HANA](../../how-to-enable-write-accelerator.md) |
| Globales SAP-Transportverzeichnis | YES | [Unterstützt](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP-Verzeichnis „sapmnt“ | geeignet | alle Systeme |
| Sicherungsspeicher | geeignet | für die kurzfristige Speicherung von Sicherungen |
| Freigaben/freigegebener Datenträger | nicht verfügbar | Azure Files Premium oder Drittanbieter erforderlich |
| Resilienz | LRS | GRS oder ZRS für Datenträger nicht verfügbar |
| Latency | niedrig bis mittel | - |
| IOPS-SLA | YES | - |
| IOPS linear zur Kapazität | halblinear in Klammern  | [Verwaltete Datenträger – Preise](https://azure.microsoft.com/pricing/details/managed-disks/) |
| Maximale Anzahl IOPS pro Datenträger | 20.000, [abhängig von der Größe des Datenträgers](https://azure.microsoft.com/pricing/details/managed-disks/) | Zu berücksichtigen sind auch die [Grenzwerte für virtuelle Computer](../../sizes.md) |
| Durchsatz-SLA | YES | - |
| Durchsatz linear zur Kapazität | halblinear in Klammern | [Verwaltete Datenträger – Preise](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA-zertifiziert | YES | [speziell für SAP HANA](../../how-to-enable-write-accelerator.md) |
| Datenträger-Momentaufnahmen möglich | YES | - |
| Azure Backup-VM-Momentaufnahmen möglich | YES | mit Ausnahme von Cachedatenträgern mit [Schreibbeschleunigung](../../how-to-enable-write-accelerator.md)  |
| Kosten | MEDIUM | - |

Azure Storage Premium erfüllt nicht die Speicherlatenz-KPIs für SAP HANA mit den gängigen Cachetypen, die mit Azure Storage Premium angeboten werden. Um die Speicherlatenz-KPIs für SAP HANA-Protokollschreibvorgänge zu erfüllen, müssen Sie das im Artikel [Aktivieren der Schreibbeschleunigung](../../how-to-enable-write-accelerator.md) beschriebene Caching der Azure-Schreibbeschleunigung verwenden. Die Azure-Schreibbeschleunigung kommt allen anderen DBMS-Systemen bei den zugehörigen Schreibvorgängen für Transaktionsprotokolle und Wiederholungsprotokolle zugute. Daher wird empfohlen, sie in allen SAP-DBMS-Bereitstellungen zu verwenden. Für SAP HANA ist die Verwendung der Azure-Schreibbeschleunigung in Verbindung mit Azure Storage Premium obligatorisch.



**Zusammenfassung:** Azure Storage Premium ist einer der für SAP-Workloads empfohlenen Azure-Speichertypen. Diese Empfehlung gilt für Nicht-Produktionssysteme und für Produktionssysteme. Azure Storage Premium eignet sich für die Verarbeitung von Datenbankworkloads. Durch die Nutzung der Azure-Schreibbeschleunigung wird die Latenz von Schreibvorgängen bei Azure Premium-Datenträgern erheblich verbessert. Bei DBMS-Systemen mit hohen IOPS- und Durchsatzraten müssen Sie jedoch entweder die Speicherkapazität überdimensionieren, oder Sie müssen Funktionen wie Windows-Speicherplätze oder Logical Volume Manager unter Linux verwenden, um Stripesets zu erstellen, die einerseits die gewünschte Kapazität, aber andererseits auch die erforderlichen IOPS oder den erforderlichen Durchsatz bei optimaler Kosteneffizienz bieten.


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


## <a name="azure-ultra-disk"></a>Azure Ultra-Datenträger
Azure Ultra-Datenträger bieten hohen Durchsatz, hohe IOPS und konsistenten Datenträgerspeicher mit niedrigen Wartezeiten für Azure IaaS-VMs. Zu den weiteren Vorteilen von Ultra-Datenträgern gehört die Möglichkeit, die IOPS und den Durchsatz des Datenträgers dynamisch in Abstimmung mit Ihren Workloads ändern zu können, ohne Ihre virtuellen Computer neu starten zu müssen. Ultra-Datenträger eignen sich für datenintensive Workloads wie z. B. eine SAP-DBMS-Workload. Ultra-Datenträger können nur als Datenträger für Daten verwendet werden und nicht als Basis-VHD-Datenträger, auf dem das Betriebssystem gespeichert ist. Wir empfehlen die Verwendung von Azure Storage Premium als Basis-VHD-Datenträger.

Beim Erstellen eines Ultra-Datenträgers können Sie drei Dimensionen definieren:

- Die Kapazität des Datenträgers. Die Bereiche liegen zwischen 4 GiB und 65.536 GiB.
- Bereitgestellte IOPS für den Datenträger. Abhängig von der Kapazität des Datenträgers gelten unterschiedliche Maximalwerte. Weitere Informationen finden Sie unter [Ultra-Datenträger](../../disks-types.md#ultra-disk).
- Bereitgestellte Speicherbandbreite. Abhängig von der Kapazität des Datenträgers gelten unterschiedliche maximale Bandbreiten. Weitere Informationen finden Sie unter [Ultra-Datenträger](../../disks-types.md#ultra-disk).

Die Kosten für einen einzelnen Datenträger werden durch die drei Dimensionen festgelegt, die Sie für die einzelnen Datenträger separat definieren können. 


Die Funktionsmatrix für die SAP-Workload sieht folgendermaßen aus:

| Funktion| Comment| Hinweise/Links | 
| --- | --- | --- | 
| Basis-VHD für Betriebssystem | funktioniert nicht | - |
| Datenträger | geeignet | alle Systeme  |
| Globales SAP-Transportverzeichnis | YES | [Unterstützt](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP-Verzeichnis „sapmnt“ | geeignet | alle Systeme |
| Sicherungsspeicher | geeignet | für die kurzfristige Speicherung von Sicherungen |
| Freigaben/freigegebener Datenträger | nicht verfügbar | Drittanbieter erforderlich |
| Resilienz | LRS | GRS oder ZRS für Datenträger nicht verfügbar |
| Latency | sehr niedrig | - |
| IOPS-SLA | YES | - |
| IOPS linear zur Kapazität | halblinear in Klammern  | [Verwaltete Datenträger – Preise](https://azure.microsoft.com/pricing/details/managed-disks/) |
| Maximale Anzahl IOPS pro Datenträger | 1\.200 bis 160.000 | abhängig von der Datenträgerkapazität |
| Durchsatz-SLA | YES | - |
| Durchsatz linear zur Kapazität | halblinear in Klammern | [Verwaltete Datenträger – Preise](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA-zertifiziert | YES | - |
| Datenträger-Momentaufnahmen möglich | Nein | - |
| Azure Backup-VM-Momentaufnahmen möglich | Nein | - |
| Kosten | höher als bei Storage Premium | - |



**Zusammenfassung:** Azure Ultra-Datenträger sind geeignete Speicher mit niedriger Latenz für alle Arten von SAP-Workloads. Bislang können Ultra-Datenträger nur in Kombination mit virtuellen Computern verwendet werden, die über Verfügbarkeitszonen (Zonenbereitstellung) bereitgestellt wurden. Derzeit werden auf Ultra-Datenträgern keine Speichermomentaufnahmen unterstützt. Im Gegensatz zu allen anderen Speichertypen können Ultra-Datenträger nicht als Basis-VHD-Datenträger verwendet werden. Ultra-Datenträger eignen sich ideal für Fälle, in denen die E/A-Workload sehr stark schwankt und Sie den bereitgestellten Speicherdurchsatz oder die IOPS an die Speicherworkloadmuster anpassen möchten, anstatt die Größe für eine maximale Nutzung von Bandbreite und IOPS zu ändern.


## <a name="azure-netapp-files-anf"></a>Azure NetApp Files (ANF)
[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) ist das Ergebnis einer Kooperation zwischen Microsoft und NetApp mit dem Ziel, hochleistungsfähige native NFS- und SMB-Freigaben in Azure bereitzustellen. Der Schwerpunkt liegt auf der Bereitstellung von Speicher mit hoher Bandbreite und niedriger Latenz, der DBMS-Bereitstellungsszenarien ermöglicht, und der Ermöglichung typischer Betriebsfunktionen des NetApp-Speichers über Azure im Lauf der Zeit. NFS- und SMB-Freigaben werden in drei verschiedenen Dienstebenen angeboten, die sich im Speicherdurchsatz und im Preis unterscheiden. Die Dienstebenen sind im Artikel [Dienstebenen für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md) dokumentiert. Für die unterschiedlichen Typen von SAP-Workloads werden die folgenden Dienstebenen dringend empfohlen:

- SAP-DBMS-Workload:    Leistung, idealerweise Ultra
- Freigabe „sapmnt“:         Leistung, idealerweise Ultra
- Globales Transportverzeichnis: Leistung, idealerweise Ultra

> [!NOTE]
> Die minimale Bereitstellungsgröße ist eine 4-TiB-Einheit, die als Kapazitätspool bezeichnet wird. Sie erstellen dann Volumes aus diesem Kapazitätspool. Das kleinste Volume, das Sie erstellen können, hat eine Kapazität von 100 GiB. Sie können einen Kapazitätspool in TiB-Schritten erweitern. Informationen zu den Preisen finden Sie im Artikel [Preise für Azure NetApp Files](https://azure.microsoft.com/pricing/details/netapp/).

Der ANF-Speicher wird derzeit für verschiedene SAP-Workloadszenarien unterstützt:

- Bereitstellen von SMB- oder NFS-Freigaben für das globale Transportverzeichnis von SAP
- Freigabe „sapmnt“ in Hochverfügbarkeitsszenarien, wie in folgenden Artikeln dokumentiert:
    - [Hochverfügbarkeit von SAP NetWeaver auf virtuellen Azure-Computern unter Windows mit Azure NetApp Files (SMB) für SAP-Anwendungen](./high-availability-guide-windows-netapp-files-smb.md)
    - [Hochverfügbarkeit für SAP NetWeaver auf Azure-VMs unter SUSE Linux Enterprise Server mit Azure NetApp Files für SAP-Anwendungen](./high-availability-guide-suse-netapp-files.md)
    - [Hochverfügbarkeit von Azure Virtual Machines für SAP NetWeaver unter Red Hat Enterprise Linux mit Azure NetApp Files für SAP-Anwendungen](./high-availability-guide-rhel-netapp-files.md)
- SAP HANA-Bereitstellungen mit NFS v4.1-Freigaben für „/hana/data“- und „/hana/log“-Volumes und/oder NFS v4.1- oder NFS v3-Freigaben für „/hana/shared“-Volumes, wie im Artikel [SAP HANA: Speicherkonfigurationen für virtuelle Azure-Computer](./hana-vm-operations-storage.md) beschrieben

> [!NOTE]
> Für Azure NetApp Files-basierte NFS- oder SMB-Freigaben werden keine anderen DBMS-Workloads unterstützt. Wenn sich dies ändert, werden Aktualisierungen und Änderungen zur Verfügung gestellt.

Wie bereits bei Azure Storage Premium kann eine feste oder lineare Durchsatzgröße pro GB ein Problem darstellen, wenn Sie Mindestzahlen beim Durchsatz einhalten müssen. Dies ist auch bei SAP HANA der Fall. Bei ANF kann dieses Problem stärker ausgeprägt sein als bei Azure Premium-Datenträgern. Im Fall von Azure Storage Premium können Sie bei mehreren kleineren Datenträgern mit einem relativ hohen Durchsatz pro GiB ein Stripeset über die Datenträger erstellen, um Kosteneffizienz und einen höheren Durchsatz bei geringerer Kapazität zu erzielen. Diese Art von Striping funktioniert nicht bei NFS- oder SMB-Freigaben, die unter ANF gehostet werden. Diese Einschränkung führt zur Bereitstellung von überdimensionierter Kapazität:

- Um beispielsweise einen Durchsatz von 250 MiB/s auf einem NFS-Volume zu erreichen, das unter ANF gehostet wird, müssen Sie eine Kapazität von 1,95 TiB auf der Dienstebene „Ultra“ bereitstellen. 
- Um 400 MiB/s zu erreichen, müssen Sie eine Kapazität von 3,125 TiB bereitstellen. Möglicherweise benötigen Sie jedoch eine Überdimensionierung der Kapazität, um den erforderlichen Durchsatz für das Volume zu erreichen. Diese Überdimensionierung der Kapazität wirkt sich auf die Preise für kleinere HANA-Instanzen aus. 
- Bei der Verwendung von NFS zusätzlich zum SAP-Verzeichnis „/sapmnt“ kommen Sie mit der Mindestkapazität von 100 GiB bis 150 GiB, die von Azure NetApp Files erzwungen wird, in der Regel sehr weit. Kundenerfahrungen haben jedoch gezeigt, dass der damit verbundene Durchsatz von 12,8 MiB/s (mit der Dienstebene „Ultra“) möglicherweise nicht ausreicht und negative Auswirkungen auf die Stabilität des SAP-Systems haben kann. In solchen Fällen können Kunden Probleme vermeiden, indem sie die Größe des „/sapmnt“-Volumes erhöhen, sodass für dieses Volume mehr Durchsatz zur Verfügung steht.

Die Funktionsmatrix für die SAP-Workload sieht folgendermaßen aus:

| Funktion| Comment| Hinweise/Links | 
| --- | --- | --- | 
| Basis-VHD für Betriebssystem | funktioniert nicht | - |
| Datenträger | geeignet | nur SAP HANA  |
| Globales SAP-Transportverzeichnis | YES | SMB und NFS |
| SAP-Verzeichnis „sapmnt“ | geeignet | alle Systeme – SMB (nur Windows) oder NFS (nur Linux) |
| Sicherungsspeicher | geeignet | - |
| Freigaben/freigegebener Datenträger | YES | SMB 3.0, NFS v3, und NFS v4.1 |
| Resilienz | LRS | GRS oder ZRS für Datenträger nicht verfügbar |
| Latency | sehr niedrig | - |
| IOPS-SLA | YES | - |
| IOPS linear zur Kapazität | streng linear  | abhängig von der [Dienstebene](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| Durchsatz-SLA | YES | - |
| Durchsatz linear zur Kapazität | halblinear in Klammern | abhängig von der [Dienstebene](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| HANA-zertifiziert | YES | - |
| Datenträger-Momentaufnahmen möglich | YES | - |
| Azure Backup-VM-Momentaufnahmen möglich | Nein | - |
| Kosten | höher als bei Storage Premium | - |


Zusätzliche integrierte Funktionalität des ANF-Speichers:

- Möglichkeit zum Erstellen von Momentaufnahmen von Volumes
- Klonen von ANF-Volumes aus Momentaufnahmen
- Wiederherstellen von Volumes aus Momentaufnahmen (snap-revert)

**Zusammenfassung**: Azure NetApp Files ist ein HANA-zertifizierter Speicher mit niedriger Latenz, der die Bereitstellung von NFS- und SMB-Volumes oder NFS- und SMB-Freigaben ermöglicht. Der Speicher wird mit drei verschiedenen Dienstebenen angeboten, über die unterschiedlicher Durchsatz und unterschiedliche IOPS pro GiB Kapazität des Volumes linear bereitgestellt werden. Der ANF-Speicher ermöglicht die Bereitstellung von SAP HANA-Szenarien mit horizontaler Skalierung mit einem Standbyknoten. Der Speicher eignet sich für die Bereitstellung von Dateifreigaben, wie sie für „/sapmnt“ oder das globale SAP-Transportverzeichnis benötigt werden. Der ANF-Speicher umfasst Funktionalitätsverfügbarkeit, die als native NetApp-Funktionalität verfügbar ist.  



## <a name="azure-standard-ssd-storage"></a>Azure SSD Standard-Speicher
Im Vergleich zu Azure HDD Standard-Speichern bieten Azure SSD Standard-Speicher eine bessere Verfügbarkeit, Konsistenz, Zuverlässigkeit und Latenz. Sie sind für Workloads optimiert, die eine konsistente Leistung bei niedrigen IOPS-Werten erfordern. Dieser Speicher ist der Mindestspeicher, der für nicht produktive SAP-Systeme mit niedrigen IOPS- und Durchsatzanforderungen verwendet wird. Die Funktionsmatrix für die SAP-Workload sieht folgendermaßen aus:

| Funktion| Comment| Hinweise/Links | 
| --- | --- | --- | 
| Basis-VHD für Betriebssystem | eingeschränkt geeignet | nicht produktive Systeme |
| Datenträger | eingeschränkt geeignet | einige nicht produktive Systeme mit geringen Anforderungen an IOPS und Latenz |
| Globales SAP-Transportverzeichnis | Nein | [Nicht unterstützt](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP-Verzeichnis „sapmnt“ | eingeschränkt geeignet | nicht produktive Systeme |
| Sicherungsspeicher | geeignet | - |
| Freigaben/freigegebener Datenträger | nicht verfügbar | Drittanbieter erforderlich |
| Resilienz | LRS, GRS | ZRS für Datenträger nicht verfügbar |
| Latency | high | zu hoch für globales SAP-Transportverzeichnis oder Produktionssysteme |
| IOPS-SLA | Nein | - |
| Maximale Anzahl IOPS pro Datenträger | 500 | unabhängig von der Datenträgergröße |
| Durchsatz-SLA | Nein | - |
| HANA-zertifiziert | Nein | - |
| Datenträger-Momentaufnahmen möglich | YES | - |
| Azure Backup-VM-Momentaufnahmen möglich | YES | - |
| Kosten | LOW | - |



**Zusammenfassung:** Der Azure SSD Standard-Speicher ist die Mindestempfehlung für Nicht-Produktions-VMs für Basis-VHD, eventuelle DBMS-Bereitstellungen mit relativer Latenzunempfindlichkeit oder niedrigen IOPS- und Durchsatzraten. Dieser Azure-Speichertyp wird für das Hosten des globalen SAP-Transportverzeichnisses nicht mehr unterstützt. 



## <a name="azure-standard-hdd-storage"></a>Azure HDD Standard-Speicher
Azure HDD Standard war der einzige verfügbare Speichertyp, als die Azure-Infrastruktur im Jahr 2014 für SAP NetWeaver-Workloads zertifiziert wurde. 2014 waren virtuelle Azure-Computer klein und boten einen niedrigen Speicherdurchsatz. Nur deshalb konnte dieser Speichertyp mit den Anforderungen Schritt halten. Der Speicher eignet sich ideal für latenzunempfindliche Workloads, die in SAP-Umgebungen kaum vorkommen. Mit dem steigenden Durchsatz von virtuellen Azure-Computern und der erhöhten Workload, die diese virtuellen Computer generieren, wird dieser Speichertyp für die Verwendung in SAP-Szenarien nicht mehr berücksichtigt. Die Funktionsmatrix für die SAP-Workload sieht folgendermaßen aus:

| Funktion| Comment| Hinweise/Links | 
| --- | --- | --- | 
| Basis-VHD für Betriebssystem | nicht geeignet | - |
| Datenträger | nicht geeignet | - |
| Globales SAP-Transportverzeichnis | Nein | [Nicht unterstützt](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP-Verzeichnis „sapmnt“ | Nein | Nicht unterstützt |
| Sicherungsspeicher | geeignet | - |
| Freigaben/freigegebener Datenträger | nicht verfügbar | Azure Files oder Drittanbieter erforderlich |
| Resilienz | LRS, GRS | ZRS für Datenträger nicht verfügbar |
| Latency | high | zu hoch für DBMS-Verwendung, globales SAP-Transportverzeichnis oder „apmnt/saploc“ |
| IOPS-SLA | Nein | - |
| Maximale Anzahl IOPS pro Datenträger | 500 | unabhängig von der Datenträgergröße |
| Durchsatz-SLA | Nein | - |
| HANA-zertifiziert | Nein | - |
| Datenträger-Momentaufnahmen möglich | YES | - |
| Azure Backup-VM-Momentaufnahmen möglich | YES | - |
| Kosten | LOW | - |



**Zusammenfassung:** HDD Standard ist ein Azure-Speichertyp, der nur zum Speichern von SAP-Sicherungen verwendet werden sollte. Er sollte nur als Basis-VHD für eher inaktive Systeme verwendet werden, z. B. ausgediente Systeme, in denen gelegentlich nach Daten gesucht wird. Aktive Entwicklungs-, QA- oder Produktions-VMs sollten jedoch nicht auf diesem Speicher basieren. Auch Datenbankdateien sollten nicht in diesem Speicher gehostet werden.


## <a name="azure-vm-limits-in-storage-traffic"></a>Azure-VM-Grenzwerte für Speicherdatenverkehr
Im Unterschied zu lokalen Szenarien spielt der einzelne ausgewählte VM-Typ eine entscheidende Rolle für die erzielbare Speicherbandbreite. Bei den verschiedenen Speichertypen ist Folgendes zu beachten:

| Speichertyp| Linux | Windows | Kommentare |
| --- | --- | --- | --- |
| HDD Standard | [Größen für virtuelle Linux-Computer in Azure](../../sizes.md) | [Größen für virtuelle Windows-Computer in Azure](../../sizes.md) | Speichergrenzwerte mittlerer oder großer virtueller Computer wahrscheinlich schwierig erreichbar |
| SSD Standard | [Größen für virtuelle Linux-Computer in Azure](../../sizes.md) | [Größen für virtuelle Windows-Computer in Azure](../../sizes.md) | Speichergrenzwerte mittlerer oder großer virtueller Computer wahrscheinlich schwierig erreichbar |
| Storage Premium | [Größen für virtuelle Linux-Computer in Azure](../../sizes.md) | [Größen für virtuelle Windows-Computer in Azure](../../sizes.md) | VM-Grenzwerte für IOPS oder Speicherdurchsatz mit Speicherkonfiguration einfach zu erreichen |
| Disk Storage Ultra | [Größen für virtuelle Linux-Computer in Azure](../../sizes.md) | [Größen für virtuelle Windows-Computer in Azure](../../sizes.md) | VM-Grenzwerte für IOPS oder Speicherdurchsatz mit Speicherkonfiguration einfach zu erreichen |
| Azure NetApp Files | [Größen für virtuelle Linux-Computer in Azure](../../sizes.md) | [Größen für virtuelle Windows-Computer in Azure](../../sizes.md) | Beim Speicherdatenverkehr wird die Durchsatzbandbreite des Netzwerks und nicht die Speicherbandbreite genutzt! |

Beachten Sie folgende Einschränkungen:

- Je kleiner der virtuelle Computer, desto weniger Datenträger können angefügt werden. Dies gilt nicht für ANF. Da NFS- oder SMB-Freigaben eingebunden werden, besteht keine Einschränkung der Anzahl der anzufügenden freigegebenen Volumes.
- Für virtuelle Computer gelten Grenzwerte für E/A-Durchsatz und IOPS, die mit Storage Premium-Datenträgern und Ultra-Datenträgern schnell überschritten werden können.
- Bei ANF wird beim Datenverkehr zu den freigegebenen Volumes die Netzwerkbandbreite der virtuellen Computer und nicht die Speicherbandbreite genutzt.
- Bei großen NFS-Volumes mit einer Kapazität im zweistelligen TiB-Bereich ist der Durchsatz beim Zugriff auf ein solches Volume über einen einzelnen virtuellen Computer anhand der Grenzwerte von Linux für eine Einzelsitzung in Interaktion mit dem freigegebenen Volume gedeckelt. 

Wenn Sie virtuelle Azure-Computer im Lebenszyklus eines SAP-Systems ausbauen, sollten Sie die Grenzwerte für IOPS und Speicherdurchsatz des neuen und größeren VM-Typs auswerten. In einigen Fällen kann es auch sinnvoll sein, die Speicherkonfiguration an die neuen Funktionen des virtuellen Azure-Computers anzupassen. 


## <a name="striping-or-not-striping"></a>Striping oder kein Striping
Das Erstellen eines Stripesets aus mehreren Azure-Datenträgern in einem größeren Volume ermöglicht es Ihnen, die IOPS und den Durchsatz der einzelnen Datenträger in einem Volume zu kumulieren. Striping wird nur für Azure Storage Standard und Azure Storage Premium verwendet. Bei Azure Disk Ultra können Sie Durchsatz und IOPS unabhängig von der Kapazität eines Datenträgers konfigurieren, sodass die Verwendung von Stripesets nicht erforderlich ist. Für freigegebene auf NFS oder SMB basierende Volumes können keine Stripesets erstellt werden. Da der Durchsatz und die IOPS bei Azure Storage Premium nicht linear sind, können Sie eine geringere Kapazität mit den gleichen IOPS und dem gleichen Durchsatz bereitstellen wie bei großen einzelnen Azure Storage Premium-Datenträgern. Mit dieser Methode können mit Azure Storage Premium ein höherer Durchsatz oder höhere IOPS zu geringeren Kosten erreicht werden. Durch Striping über zwei Storage P15 Premium-Datenträger erreichen Sie beispielsweise einen Durchsatz von: 

- 250 MiB/s. Ein solches Volume verfügt über eine Kapazität von 512 GiB. Für einen einzelnen Datenträger mit einem Durchsatz von 250 MiB/s müssen Sie einen P40-Datenträger mit einer Kapazität von 2 TiB verwenden. 
- 400 MiB/s durch Striping von vier Storage P10 Premium-Datenträgern mit einer Gesamtkapazität von 512 GiB. Für einen einzelnen Datenträger mit einem Durchsatz von mindestens 500 MiB pro Sekunde müssen Sie einen Storage P60 Premium-Datenträger mit einer Kapazität von 8 TiB verwenden. Da die Kosten für Storage Premium nahezu linear zur Kapazität sind, sind die Kosteneinsparungen bei Verwendung von Striping klar erkennbar.

Beim Striping sind folgende Regeln zu beachten:

- Es sollte kein Speicher im virtuellen Computer konfiguriert werden, da der Azure-Speicher die Daten bereits redundant hält.
- Die Datenträger, auf die das Stripeset angewandt wird, müssen die gleiche Größe aufweisen.

Striping über mehrere kleinere Datenträger ist die beste Möglichkeit, um mit Azure Storage Premium ein gutes Preis-Leistungs-Verhältnis zu erzielen. Es ist davon auszugehen, dass Striping einen zusätzlichen Aufwand für die Bereitstellung und Verwaltung mit sich bringt.

Empfehlungen zur spezifischen Stripegröße finden Sie in der Dokumentation der einzelnen DBMS, z. B. unter [SAP HANA: Speicherkonfigurationen für virtuelle Azure-Computer](./hana-vm-operations-storage.md).




## <a name="next-steps"></a>Nächste Schritte
Lesen Sie die folgenden Artikel:

- [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](./dbms_guide_general.md)
- [SAP HANA: Speicherkonfigurationen für virtuelle Azure-Computer](./hana-vm-operations-storage.md)
