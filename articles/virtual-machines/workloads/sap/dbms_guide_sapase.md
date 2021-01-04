---
title: Azure Virtual Machines – SAP ASE-DBMS-Bereitstellung für SAP-Workload| Microsoft-Dokumentation
description: Azure Virtual Machines – SAP ASE-DBMS-Bereitstellung für SAP-Workload
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a3ef3fc2afa07590ff676d57c22f05ed723539f9
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957723"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Azure Virtual Machines – SAP ASE-DBMS-Bereitstellung für SAP-Workload

Dieses Dokument behandelt verschiedene wichtige Themen für die Bereitstellung von SAP ASE in Azure IaaS. Es ist sinnvoll, im Vorfeld das Dokument [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md) und andere Artikel der [Azure-Dokumentation für SAP-Workload](./get-started.md) zu lesen. In diesem Dokument wird SAP ASE behandelt, das unter Linux und unter Windows-Betriebssystemen ausgeführt wird. Das unterstützte Minimalrelease in Azure ist SAP ASE 16.0.02 (Release 16 Support Pack 2). Es wird empfohlen, die neueste Version von SAP und die neueste Patchebene bereitzustellen.  Empfohlen wird mindestens SAP ASE 16.0.03.07 (Release 16 Support Pack 3 Patch Level 7).  Die neueste Version von SAP ist zu finden in [Targeted ASE 16.0 Release Schedule and CR list Information](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information).

Zusätzliche Informationen zum Release-Support bei SAP-Anwendungen oder zum Speicherort für Installationsmedien sind – über die SAP-Produktverfügbarkeitsmatrix hinaus – an folgenden Orten zu finden:

- [SAP-Supporthinweis Nr. 2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [SAP-Supporthinweis Nr. 1941500](https://launchpad.support.sap.com/#/notes/1941500)
- [SAP-Supporthinweis Nr. 1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [SAP-Supporthinweis Nr. 1973241](https://launchpad.support.sap.com/#/notes/1973241)

Hinweis: In der gesamten Dokumentation innerhalb und außerhalb der SAP-Welt wird auf den Namen des Produkts als „Sybase ASE“ oder „SAP ASE“ oder in einigen Fällen auf beide Namen verwiesen. Aus Konsistenzgründen verwenden wir in dieser Dokumentation den Namen **SAP ASE**.

## <a name="operating-system-support"></a>Betriebssystemunterstützung
Die SAP-Produktverfügbarkeitsmatrix enthält die unterstützten Kombinationen aus Betriebssystem und SAP-Kernel für jede SAP-Anwendung.  Die Linux-Distributionen SUSE 12.x, SUSE 15.x, Red Hat 7.x werden vollständig unterstützt.  Oracle Linux als Betriebssystem für SAP ASE wird nicht unterstützt.  Es wird empfohlen, die neuesten verfügbaren Linux-Releases zu verwenden. Windows-Kunden sollten die Releases Windows Server 2016 oder Windows Server 2019 verwenden.  Ältere Releases von Windows wie Windows 2012 werden zwar technisch unterstützt, doch die neueste Windows-Version wird immer empfohlen.


## <a name="specifics-to-sap-ase-on-windows"></a>Besonderheiten bei SAP ASE unter Windows
Ab Microsoft Azure lassen sich bestehende SAP ASE-Anwendungen zu Azure-VMs migrieren. Wenn Sie SAP ASE auf einer Azure-VM betreiben, können Sie die Gesamtkosten für Bereitstellung, Verwaltung und Wartung von unternehmensweiten Anwendungen senken, indem Sie diese Anwendungen einfach zu Microsoft Azure migrieren. Bei SAP ASE auf einem virtuellen Azure-Computer stehen Administratoren und Entwicklern die vertrauten Entwicklungs- und Verwaltungstools aus der lokalen Umgebung zur Verfügung.

Microsoft Azure stellt zahlreiche verschiedene VM-Typen zur Verfügung, mit denen Sie SAP-Systeme und -Landschaften jeder Größe ausführen können: von den kleinsten bis hin zu den großen mit Tausenden von Benutzern. Informationen zur SAP-Größe für SAPS-Zahlen der verschiedenen SAP-zertifizierten VM-SKUs finden Sie im [SAP-Supporthinweis Nr. 1928533](https://launchpad.support.sap.com/#/notes/1928533).

Dokumentation zum Installieren von SAP ASE unter Windows ist zu finden im [SAP ASE Installation Guide for Windows](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html) (SAP ASE-Installationshandbuch für Windows).

„Seiten im Arbeitsspeicher sperren“ ist eine Einstellung, mit der verhindert wird, dass der SAP ASE-Datenbankpuffer ausgelagert wird.  Diese Einstellung ist nützlich für große ausgelastete Systeme mit viel Arbeitsspeicher. Weitere Informationen finden Sie unter „BC-DB-SYB“. 


## <a name="linux-operating-system-specific-settings"></a>Spezifische Einstellungen für das Linux-Betriebssystem
Auf Linux-VMs sollte die Ausführung von `saptune` bei großen SAP-ASE Linux-Profilseiten standardmäßig aktiviert sein und kann mit einem Befehl überprüft werden.  

`cat /proc/meminfo` 

Die Seitengröße ist normalerweise 2.048 KB. Einzelheiten dazu finden Sie im Artikel [Huge Pages on Linux](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html) (Große Seiten unter Linux). 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>Empfehlungen zur VM- und Datenträgerstruktur für SAP ASE-Bereitstellungen

SAP ASE für SAP NetWeaver-Anwendungen wird auf allen VM-Typen unterstützt, die im [SAP-Supporthinweis Nr. 1928533](https://launchpad.support.sap.com/#/notes/1928533) aufgelistet werden. Zu typischen VM-Typen, die für SAP ASE-Datenbankserver mittlerer Größe verwendet werden, gehört Esv3.  Große Datenbanken mit mehreren Terabyte können VM-Typen der M-Serie nutzen. Die Leistung des Datenträgers beim Schreiben des SAP ASE-Transaktionsprotokolls kann durch Aktivieren der Schreibbeschleunigung für die M-Serie verbessert werden. Aufgrund der Art und Weise, wie SAP ASE Protokoll Schreibvorgänge durchführt, sollte Schreibbeschleunigung mit SAP ASE sorgfältig getestet werden.  Lesen Sie den [SAP-Supporthinweis Nr. 2816580](../../how-to-enable-write-accelerator.md), und erwägen Sie die Ausführung eines Leistungstests.  
Schreibbeschleunigung ist nur für den Transaktionsprotokoll-Datenträger vorgesehen. Der Cache auf Datenträgerebene sollte auf NONE festgelegt werden. Seien Sie nicht überrascht, wenn Azure-Schreibbeschleunigung keine ähnlichen Verbesserungen wie bei anderen DBMS zeigt. Basierend auf der Art und Weise, wie SAP ASE in das Transaktionsprotokoll schreibt, könnte es sein, dass es nur eine geringe bis keine Beschleunigung durch Azure-Schreibbeschleunigung gibt.
Für Daten- und Protokollgeräte werden separate Datenträger empfohlen.  Die Systemdatenbanken „sybsecurity“ und `saptools` erfordern keine dedizierten Datenträger und können auf den Datenträgern abgelegt werden, die die SAP-Datenbankdaten und Protokollgeräte enthalten. 

![Speicherkonfiguration für SAP ASE](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>Dateisysteme. Stripegröße und E/A-Lastenausgleich 
SAP ASE schreibt Daten sequenziell in Datenträgerspeichergeräte, sofern sie nicht anders konfiguriert wurden. Dies bedeutet, dass eine leere SAP ASE-Datenbank mit vier Geräten Daten nur in das erste Gerät schreibt.  Auf die anderen Datenträgergeräte wird nur geschrieben, wenn das erste Gerät voll ist.  Die Menge an READ- und WRITE-E/A-Vorgängen für die einzelnen SAP ASE-Geräte ist wahrscheinlich unterschiedlich. Zum Ausgleich von Datenträger-E/A auf allen verfügbaren Azure-Datenträgern müssen entweder Windows-Speicherplätze oder es muss Linux LVM2 verwendet werden. Unter Linux wird die Verwendung des XFS-Dateisystems zum Formatieren der Datenträger empfohlen. Die LVM-Stripegröße sollte mit einem Leistungstest getestet werden. Eine Stripegröße von 128 KB ist ein guter Ausgangspunkt. Unter Windows sollte die NTFS-Größe der Zuordnungseinheiten (Allocation Unit Size, AUS) getestet werden. 64 KB kann als Anfangswert verwendet werden. 

Es wird empfohlen, die automatische Datendankerweiterung so zu konfigurieren, wie im Artikel [Configuring Automatic Database Space Expansion in SAP Adaptive Server Enterprise](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/) (Konfigurieren der automatischen Datenbankspeichererweiterung in SAP Adaptive Server Enterprise) und im [SAP-Supporthinweis Nr. 1815695](https://launchpad.support.sap.com/#/notes/1815695) beschrieben wird. 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Beispielkonfigurationen für SAP ASE auf virtuellen Azure-Computern, Datenträgern und Dateisystemen 
Die nachstehenden Vorlagen zeigen Beispielkonfigurationen für Linux und Windows. Vergewissern Sie sich vor der Bestätigung des virtuellen Computers und der Datenträgerkonfiguration, dass die Netzwerk- und Speicherbandbreitenkontingente der einzelnen virtuellen Computer ausreichen, um die Geschäftsanforderungen zu erfüllen. Beachten Sie außerdem, dass unterschiedliche Azure-VM-Typen eine unterschiedliche maximale Anzahl von Datenträgern aufweisen, die an den virtuellen Computer angefügt werden können. Beispielsweise hat eine E4s_v3-VM einen Speicher-E/A-Durchsatz mit einem Grenzwert von 48 MB/Sek. Wenn der für die Datenbanksicherungsaktivität erforderliche Speicherdurchsatz mehr als 48 MB/Sek. erfordert, ist ein größerer VM-Typ mit mehr Speicherbandbreitendurchsatz unvermeidlich. Beim Konfigurieren von Azure Storage müssen Sie auch bedenken, dass sich vor allem bei [Azure Storage Premium](../../premium-storage-performance.md) der Durchsatz und die IOPS pro GB Kapazität ändern. Weitere Informationen zu diesem Thema finden Sie im Artikel [Welche Datenträgertypen stehen in Azure zur Verfügung?](../../disks-types.md). Die Kontingente für bestimmte Azure-VM-Typen werden im Artikel [Arbeitsspeicheroptimierte Größen virtueller Computer](../../sizes-memory.md) und den damit verlinkten Artikeln dokumentiert. 

> [!NOTE]
>  Wenn ein DBMS-System von einem lokalen Standort nach Azure verschoben wird, wird empfohlen, die Überwachung auf dem virtuellen Computer durchzuführen sowie CPU, Arbeitsspeicher, IOPS und den Speicherdurchsatz zu bewerten. Vergleichen Sie die Spitzenwerte, die bei den in den oben erwähnten Artikeln dokumentierten VM-Kontingentgrenzwerten festgestellt wurden.

Die unten aufgeführten Beispiele dienen zur Veranschaulichung und können basierend auf individuellen Anforderungen geändert werden. Aufgrund des Entwurfs von SAP ASE ist die Anzahl der Datengeräte nicht so wichtig wie bei anderen Datenbanken. Die Anzahl der in diesem Dokument aufgeführten Datengeräte sind nur ein Leitfaden. 

Ein Konfigurationsbeispiel für einen kleinen SAP ASE-DB-Server mit einer Datenbankgröße zwischen 50 GB und 250 GB, z. B. SAP Solution Manager, könnte so aussehen:

| Konfiguration | Windows | Linux | Kommentare |
| --- | --- | --- | --- |
| VM-Typ | E4s_v3 (4 vCPU/32 GB RAM) | E4s_v3 (4 vCPU/32 GB RAM) | --- |
| Beschleunigter Netzwerkbetrieb | Aktivieren | Aktivieren | ---|
| SAP ASE-Version | 16.0.03.07 oder höher | 16.0.03.07 oder höher | --- |
| Anzahl von Datengeräten | 4 | 4 | ---|
| Anzahl von Protokollgeräten | 1 | 1 | --- |
| Anzahl von temporären Geräten | 1 | 1 | mehr für SAP BW-Workload |
| Betriebssystem | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 oder RHEL 7.6 | --- |
| Datenträgeraggregierung | Speicherplätze | LVM2 | --- |
| Dateisystem | NTFS | XFS |
| Blockgröße formatieren | Workloadtests erforderlich | Workloadtests erforderlich | --- |
| Anzahl und Typ von Datenträgern | Storage Premium: 2 x P10 (RAID0) | Storage Premium: 2 x P10 (RAID0)| Cache = Read Only (Schreibgeschützt) |
| Anzahl und Typ von Protokolldatenträgern | Storage Premium: 1 x P20  | Storage Premium: 1 x P20 | Cache = NONE (KEINER) |
| ASE-Parameter „MaxMemory“ | 90 % von physischem RAM | 90 % von physischem RAM | Annahme: einzelne Instanz |
| Anzahl von Sicherungsmedien | 4 | 4| --- |
| Anzahl und Typ von Sicherungsmedien | 1 | 1 | --- |


Ein Konfigurationsbeispiel für einen mittleren SAP ASE-DB-Server mit einer Datenbankgröße zwischen 250 GB und 750 GB, z. B. ein kleineres SAP Business Suite-System, könnte so aussehen:

| Konfiguration | Windows | Linux | Kommentare |
| --- | --- | --- | --- |
| VM-Typ | E16s_v3 (16 vCPU/128 GB RAM) | E16s_v3 (16 vCPU/128 GB RAM) | --- |
| Beschleunigter Netzwerkbetrieb | Aktivieren | Aktivieren | ---|
| SAP ASE-Version | 16.0.03.07 oder höher | 16.0.03.07 oder höher | --- |
| Anzahl von Datengeräten | 8 | 8 | ---|
| Anzahl von Protokollgeräten | 1 | 1 | --- |
| Anzahl von temporären Geräten | 1 | 1 | mehr für SAP BW-Workload |
| Betriebssystem | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 oder RHEL 7.6 | --- |
| Datenträgeraggregierung | Speicherplätze | LVM2 | --- |
| Dateisystem | NTFS | XFS |
| Blockgröße formatieren | Workloadtests erforderlich | Workloadtests erforderlich | --- |
| Anzahl und Typ von Datenträgern | Storage Premium: 4 x P20 (RAID0) | Storage Premium: 4 x P20 (RAID0)| Cache = Read Only (Schreibgeschützt) |
| Anzahl und Typ von Protokolldatenträgern | Storage Premium: 1 x P20  | Storage Premium: 1 x P20 | Cache = NONE (KEINER) |
| ASE-Parameter „MaxMemory“ | 90 % von physischem RAM | 90 % von physischem RAM | Annahme: einzelne Instanz |
| Anzahl von Sicherungsmedien | 4 | 4| --- |
| Anzahl und Typ von Sicherungsmedien | 1 | 1 | --- |

Ein Konfigurationsbeispiel für einen kleinen SAP ASE-DB-Server mit einer Datenbankgröße zwischen 750 GB und 2.000 GB, z. B. ein größeres SAP Business Suite-System, könnte so aussehen:

| Konfiguration | Windows | Linux | Kommentare |
| --- | --- | --- | --- |
| VM-Typ | E64s_v3 (64 vCPU/432 GB RAM) | E64s_v3 (64 vCPU/432 GB RAM) | --- |
| Beschleunigter Netzwerkbetrieb | Aktivieren | Aktivieren | ---|
| SAP ASE-Version | 16.0.03.07 oder höher | 16.0.03.07 oder höher | --- |
| Anzahl von Datengeräten | 16 | 16 | ---|
| Anzahl von Protokollgeräten | 1 | 1 | --- |
| Anzahl von temporären Geräten | 1 | 1 | mehr für SAP BW-Workload |
| Betriebssystem | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 oder RHEL 7.6 | --- |
| Datenträgeraggregierung | Speicherplätze | LVM2 | --- |
| Dateisystem | NTFS | XFS |
| Blockgröße formatieren | Workloadtests erforderlich | Workloadtests erforderlich | --- |
| Anzahl und Typ von Datenträgern | Storage Premium: 4 x P30 (RAID0) | Storage Premium: 4 x P30 (RAID0)| Cache = Read Only (Schreibgeschützt) |
| Anzahl und Typ von Protokolldatenträgern | Storage Premium: 1 x P20  | Storage Premium: 1 x P20 | Cache = NONE (KEINER) |
| ASE-Parameter „MaxMemory“ | 90 % von physischem RAM | 90 % von physischem RAM | Annahme: einzelne Instanz |
| Anzahl von Sicherungsmedien | 4 | 4| --- |
| Anzahl und Typ von Sicherungsmedien | 1 | 1 | --- |


Ein Konfigurationsbeispiel für einen kleinen SAP ASE-DB-Server mit einer Datenbankgröße von 2 TB+, z. B. ein größeres global genutztes SAP Business Suite-System, könnte so aussehen:

| Konfiguration | Windows | Linux | Kommentare |
| --- | --- | --- | --- |
| VM-Typ | M-Series (1,0 bis 4,0 TB RAM)  | M-Series (1,0 bis 4,0 TB RAM) | --- |
| Beschleunigter Netzwerkbetrieb | Aktivieren | Aktivieren | ---|
| SAP ASE-Version | 16.0.03.07 oder höher | 16.0.03.07 oder höher | --- |
| Anzahl von Datengeräten | 32 | 32 | ---|
| Anzahl von Protokollgeräten | 1 | 1 | --- |
| Anzahl von temporären Geräten | 1 | 1 | mehr für SAP BW-Workload |
| Betriebssystem | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 oder RHEL 7.6 | --- |
| Datenträgeraggregierung | Speicherplätze | LVM2 | --- |
| Dateisystem | NTFS | XFS |
| Blockgröße formatieren | Workloadtests erforderlich | Workloadtests erforderlich | --- |
| Anzahl und Typ von Datenträgern | Storage Premium: 4+ x P30 (RAID0) | Storage Premium: 4+ x P30 (RAID0)| Cache = Read Only (Schreibgeschützt); erwägen: Azure Ultra-Datenträger |
| Anzahl und Typ von Protokolldatenträgern | Storage Premium: 1 x P20  | Storage Premium: 1 x P20 | Cache = NONE (KEINER); erwägen: Azure Ultra-Datenträger |
| ASE-Parameter „MaxMemory“ | 90 % von physischem RAM | 90 % von physischem RAM | Annahme: einzelne Instanz |
| Anzahl von Sicherungsmedien | 16 | 16 | --- |
| Anzahl und Typ von Sicherungsmedien | 4 | 4 | Verwenden von LVM2/Speicherplätzen |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>Überlegungen zur Sicherung und Wiederherstellung für SAP ASE in Azure
Durch Erhöhung der Anzahl von Daten und Sicherungsmedien wird die Sicherungs- und Wiederherstellungsleistung erhöht. Es wird empfohlen, die Azure-Datenträger, die als Host für das SAP ASE-Sicherungsmedium dienen, wie in den weiter oben gezeigten Tabellen anzulegen. Achten Sie darauf, die Anzahl von Sicherungsmedien und-Datenträgern auszugleichen, und sorgen Sie dafür, dass der Sicherungsdurchsatz 40 % – 50 % des gesamten VM-Durchsatzkontingents nicht überschreitet. Es wird empfohlen, die SAP-Sicherungskomprimierung als Standard zu verwenden. Weitere Informationen finden Sie in folgenden Artikeln:

- [SAP-Supporthinweis Nr. 1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [SAP-Supporthinweis Nr. 1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [SAP-Supporthinweis Nr. 1585981](https://launchpad.support.sap.com/#/notes/1585981) 

Verwenden Sie das Laufwerk „D:\“ oder „/temp“ nicht als Speicherziel für Datenbank- oder Protokolldateien.

### <a name="impact-of-database-compression"></a>Auswirkungen der Datenbankkomprimierung
In Konfigurationen, bei denen die E/A-Bandbreite zum begrenzenden Faktor werden kann, sollte jede Möglichkeit ergriffen werden, die IOPS zu senken. Dadurch kann die ausführbare Workload in einem IaaS-Szenario wie Azure besser verteilt werden. Es wird daher empfohlen, die SAP ASE-Komprimierung zu verwenden, bevor Sie eine vorhandene SAP-Datenbank in Azure hochladen.

Es ist aus mehreren Gründen ratsam, die Komprimierung vor dem Hochladen in Azure durchzuführen:

* Die in Azure hochzuladende Datenmenge ist geringer.
* Die Ausführungsdauer der Komprimierung ist geringer (unter der Annahme, dass lokal leistungsfähigere Hardware mit mehr CPUs, einer höheren E/A-Bandbreite oder einer geringeren E/A-Latenz zur Verfügung steht).
* Durch kleinere Datenbanken werden u. U. die Kosten für die Datenträgerzuordnung gesenkt.

Die Daten- und LOB-Komprimierung funktioniert auf einer VM, die auf einem virtuellen Azure-Computer gehostet wird, genauso wie in der lokalen Umgebung. Weitere Informationen zur Überprüfung, ob die Komprimierung bei einer bestehenden SAP ASE-Datenbank bereits verwendet wird, finden Sie im [SAP-Supporthinweis Nr. 1750510](https://launchpad.support.sap.com/#/notes/1750510). Weitere Informationen zur SAP ASE-Datenbankkomprimierung finden Sie im [SAP-Supporthinweis Nr. 2121797](https://launchpad.support.sap.com/#/notes/2121797).

## <a name="high-availability-of-sap-ase-on-azure"></a>Hochverfügbarkeit von SAP ASE in Azure 
HADR Users Guide (HADR-Benutzerhandbuch, High Availability And Desaster Recovery, Hochverfügbarkeit und Notfallwiederherstellung) enthält detaillierte Informationen zum Setup und zur Konfiguration einer SAP ASE „Always On“-Lösung mit zwei Knoten.  Außerdem wird ein dritter Knoten für Notfallwiederherstellung ebenfalls unterstützt. SAP ASE unterstützt viele hochverfügbare Konfigurationen, darunter freigegebener Datenträger und natives Betriebssystem-Clustering (Floating IP). Die einzige unterstützte Konfiguration in Azure ist die Verwendung von Fehler-Manager ohne Floating IP.  Die Methode „Floating IP-Adresse“ funktioniert in Azure nicht.  Der SAP-Kernel ist eine "HA-basierte" (High Availability, Hochverfügbarkeit) Anwendung, die die primären und sekundären SAP ASE-Server kennt. Es gibt keine engen Integrationen zwischen SAP ASE und Azure; der interne Azure-Lastenausgleich wird nicht verwendet. Deshalb sollte die SAP ASE-Standarddokumentation befolgt werden, beginnend mit dem [SAP ASE HADR Users Guide](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html). 

> [!NOTE]
> Die einzige unterstützte Konfiguration in Azure ist die Verwendung von Fehler-Manager ohne Floating IP.  Die Methode „Floating IP-Adresse“ funktioniert in Azure nicht. 

### <a name="third-node-for-disaster-recovery"></a>Dritter Knoten für Notfallwiederherstellung
Neben der Verwendung von SAP ASE „Always On“ für lokale Hochverfügbarkeit möchten Sie möglicherweise die Konfiguration auf einen asynchron replizierten Knoten in einer anderen Azure-Region erweitern. Die Dokumentation zu einem solchen Szenario finden Sie [hier](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199).

## <a name="sap-ase-database-encryption--ssl"></a>SAP ASE-Datenbankverschlüsselung und SSL 
Der SAP-Softwarebereitstellungs-Manager (SAP Software Provisioning Manager, SWPM) enthält eine Option zum Verschlüsseln der Datenbank während der Installation.  Wenn Sie Verschlüsselung verwenden möchten, wird die vollständige SAP-Datenbankverschlüsselung empfohlen.  Weitere Informationen finden Sie unter:

- [SAP-Supporthinweis Nr. 2556658](https://launchpad.support.sap.com/#/notes/2556658)
- [SAP-Supporthinweis Nr. 2224138](https://launchpad.support.sap.com/#/notes/2224138)
- [SAP-Supporthinweis Nr. 2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [SAP-Supporthinweis Nr. 2593925](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> Wenn eine SAP ASE-Datenbank verschlüsselt ist, wird die Komprimierung des Sicherungsabbilds nicht funktionieren. Siehe auch [SAP-Supporthinweis Nr. 2680905](https://launchpad.support.sap.com/#/notes/2680905) 

## <a name="sap-ase-on-azure-deployment-checklist"></a>Checkliste zur Bereitstellung von SAP ASE in Azure
 
- Bereitstellen von SAP ASE 16.0.03.07 oder höher
- Aktualisieren auf die neueste Version und Patches von FaultManager und SAPHostAgent
- Bereitstellen auf dem neuesten verfügbaren zertifizierten Betriebssystem, z. B. Windows 2019, SUSE 15.1 oder aber Red Hat 7.6 oder höher
- Zertifizierte SAP-VMs verwenden – empfohlen werden Azure-VM-SKUs, High Memory, z. B. Es_v3, oder für x-large-Systeme der M-Serie VM-SKUs
- Vergleichen Sie die Datenträger-IOPS und das Gesamtdurchsatzkontingent des virtuellen Computers der VM mit dem Datenträgerentwurf.  Eine ausreichende Anzahl von Datenträgern bereitstellen
- Datenträger mithilfe von Windows-Speicherplätzen oder Linux LVM2 mit korrekter Stripegröße und richtigem Dateisystem aggregieren
- Eine ausreichende Anzahl von Geräten für Daten-, Protokoll-, temporäre und Sicherungszwecke erstellen
- Verwendung von UltraDisk für x-large-Systeme in Betracht ziehen 
- `saptune` SAP ASE unter dem Linux-Betriebssystem ausführen 
- Die Datenbank mit Datenbankverschlüsselung sichern – Schlüssel in Azure Key Vault manuell speichern 
- Die [Checkliste „SAP in Azure“](./sap-deployment-checklist.md) vervollständigen 
- Protokollsicherung und vollständige Sicherung konfigurieren 
- HA/DR, Sicherung und Wiederherstellung testen sowie Belastungs- und Volumetests durchführen 
- Bestätigen, dass die automatische Datenbankerweiterung funktioniert 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>Verwenden von DBACockpit zum Überwachen von Datenbankinstanzen
Bei SAP-Systemen, bei denen SAP ASE als Datenbankplattform verwendet wird, erfolgt der Zugriff auf das DBACockpit über eingebettete Browserfenster im Transaktions-DBACockpit oder über Webdynpro. Die vollständige Funktionalität für die Überwachung und Verwaltung der Datenbank steht allerdings nur über die Webdynpro-Implementierung von DBACockpit zur Verfügung.

Wie bei lokalen Systemen sind einige Schritte erforderlich, um die gesamte SAP NetWeaver-Funktionalität zu aktivieren, die durch die Webdynpro-Implementierung von DBACockpit genutzt wird. Führen Sie die Schritte im [SAP-Supporthinweis Nr. 1245200](https://launchpad.support.sap.com/#/notes/1245200) aus, um die Verwendung von Webdynpros zu aktivieren und die erforderlichen Webdynpros zu generieren. Wenn Sie die Anleitungen in den vorstehenden Hinweisen ausführen, konfigurieren Sie auch den Internet Communication Manager (`ICM`) zusammen mit den Ports, die für HTTP- und HTTPS-Verbindungen verwendet werden sollen. Die Standardeinstellung für HTTP sieht wie folgt aus:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

Die im Transaktions-DBACockpit generierten Links ähneln den folgenden Links:

> https:\//\<fullyqualifiedhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullyqualifiedhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Der virtuelle Azure-Computer, auf dem das SAP-System gehostet wird, kann auf verschiedene Arten mit AD und dem DNS verbunden sein. In Abhängigkeit davon muss ICM einen vollqualifizierten Hostnamen verwenden, der von dem Computer aufgelöst werden kann, auf dem DBACockpit geöffnet wird. Im [SAP-Supporthinweis Nr. 773830](https://launchpad.support.sap.com/#/notes/773830) erfahren Sie, wie ICM den vollqualifizierten Hostnamen anhand von Profilparametern ermittelt und wie Sie bei Bedarf den Parameter „icm/host_name_full“ explizit festlegen.

Wenn die VM in einem Nur-Cloud-Szenario ohne standortübergreifende Konnektivität zwischen der lokalen Umgebung und Azure bereitgestellt wird, müssen Sie eine öffentliche IP-Adresse und ein `domainlabel` festlegen. Das Format des öffentlichen DNS-Namens der VM sieht folgendermaßen aus:

> `<custom domainlabel`&gt;.`<azure region`&gt;.cloudapp.azure.com
> 
> 

Weitere Informationen zum DNS-Namen finden Sie [hier][virtual-machines-azurerm-versus-azuresm].

Wenn Sie den SAP-Profilparameter „icm/host_name_full“ auf den DNS-Namen der Azure-VM einstellen, erhalten Sie einen Link, der ähnlich wie dieser aussieht:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

Stellen Sie in diesem Fall Folgendes sicher:

* Fügen Sie der Netzwerksicherheitsgruppe im Azure-Portal für die TCP/IP-Ports, die für die Kommunikation mit ICM verwendet werden, eingehende Regeln hinzu.
* Fügen Sie der Windows-Firewall-Konfiguration für die TCP/IP-Ports, die für die Kommunikation mit ICM verwendet werden, eingehende Regeln hinzu.

Für den automatischen Import aller zur Verfügung stehenden Korrekturen wird empfohlen, regelmäßig den Ihre Version betreffenden SAP-Hinweis zur Korrektursammlung zu konsultieren:

* [SAP-Supporthinweis Nr. 1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [SAP-Supporthinweis Nr. 1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [SAP-Supporthinweis Nr. 1882376](https://launchpad.support.sap.com/#/notes/1882376)

Weitere Informationen zu DBA Cockpit für SAP ASE erhalten Sie in den folgenden SAP-Hinweisen:

* [SAP-Supporthinweis Nr. 1605680](https://launchpad.support.sap.com/#/notes/1605680)
* [SAP-Supporthinweis Nr. 1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [SAP-Supporthinweis Nr. 1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [SAP-Supporthinweis Nr. 1758182](https://launchpad.support.sap.com/#/notes/1758182)
* [SAP-Supporthinweis Nr. 1758496](https://launchpad.support.sap.com/#/notes/1758496)    
* [SAP-Supporthinweis Nr. 1814258](https://launchpad.support.sap.com/#/notes/1814258)
* [SAP-Supporthinweis Nr. 1922555](https://launchpad.support.sap.com/#/notes/1922555)
* [SAP-Supporthinweis Nr. 1956005](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>Nützliche Links, Hinweise und Whitepaper für SAP ASE
Die Startseite für die [Dokumentation zu SAP ASE 16.0.03.07 ](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US) enthält Links zu verschiedenen Dokumenten, darunter die folgenden Dokumente:

- SAP ASE Learning Journey – Administration & Monitoring (SAP ASE Learning Journey – Verwaltung und Überwachung)
- SAP ASE Learning Journey – Installation & Upgrade

hilfreich sind. Ein weiteres nützliches Dokument ist [SAP Applications on SAP Adaptive Server Enterprise Best Practices for Migration and Runtime](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf) (SAP-Anwendungen in SAP Adaptive Server Enterprise – Bewährte Methoden für Migration und Laufzeit).

Weitere hilfreiche SAP-Supporthinweise sind folgende:

- [SAP-Supporthinweis Nr. 2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [SAP-Supporthinweis Nr. 1748888](https://launchpad.support.sap.com/#/notes/1748888) 
- [SAP-Supporthinweis Nr. 2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [SAP-Supporthinweis Nr. 1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [SAP-Supporthinweis Nr. 1724091](https://launchpad.support.sap.com/#/notes/1724091) 
- [SAP-Supporthinweis Nr. 1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [SAP-Supporthinweis Nr. 2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP-Supporthinweis Nr. 1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [SAP-Supporthinweis Nr. 2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [SAP-Supporthinweis Nr. 1750510](https://launchpad.support.sap.com/#/notes/1750510) 
- [SAP-Supporthinweis Nr. 1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [SAP-Supporthinweis Nr. 2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP-Supporthinweis Nr. 1588316](https://launchpad.support.sap.com/#/notes/158831) 

Weitere Informationen werden veröffentlicht unter: 

- [SAP Applications on SAP Adaptive Server Enterprise](https://community.sap.com/topics/applications-on-ase) (SAP-Anwendungen in SAP Adaptive Server Enterprise)
- [SAP ASE-Infocenter](http://infocenter.sybase.com/help/index.jsp) 
- [SAP ASE Always-on with 3rd DR Node Setup](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199) (SAP ASE „Always On“ mit Setup für den dritten Knoten zur Notfallwiederherstellung)

Ein monatlicher Newsletter wird über [SAP-Supporthinweis Nr. 2381575](https://launchpad.support.sap.com/#/notes/2381575) veröffentlicht. 


## <a name="next-steps"></a>Nächste Schritte
Sehen Sie sich den Artikel [Prüfliste für die Planung und Bereitstellung von SAP-Workloads in Azure](./sap-deployment-checklist.md) an.