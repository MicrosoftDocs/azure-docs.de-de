---
title: Installieren von SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Vorgehensweise zum Installieren von SAP HANA in Azure (große Instanzen).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a1430b32c0e74be7a0e50fa4c5c183018b2b55e0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006301"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Installieren und Konfigurieren von SAP HANA (große Instanzen) in Azure

Machen Sie sich vor dem Lesen dieses Artikels mit [allgemeinen Begriffen zu HANA (große Instanzen)](hana-know-terms.md) und [SKUs für HANA (große Instanzen)](hana-available-skus.md) vertraut.

Für die Installation von SAP HANA sind Sie zuständig. Sie können mit dem Installieren eines neuen Servers für SAP HANA in Azure (große Instanzen) beginnen, nachdem Sie die Konnektivität zwischen Ihren virtuellen Azure-Netzwerken und den HANA-Einheiten (große Instanzen) eingerichtet haben. 

> [!Note]
> Gemäß einer SAP-Richtlinie muss die Installation von SAP HANA durch eine Person durchgeführt werden, die die Prüfung „Certified SAP Technology Associate“ oder die Zertifizierungsprüfung „SAP HANA Installation“ bestanden hat oder ein von SAP zertifizierter Systemintegrator (SI) ist.

Wenn Sie planen, HANA 2.0 zu installieren, lesen Sie [SAP Support Note #2235581 – SAP HANA: Supported Operating Systems](https://launchpad.support.sap.com/#/notes/2235581/E) (SAP-Supporthinweis #2235581 – SAP HANA: Unterstützte Betriebssysteme), um sicherzustellen, dass das Betriebssystem von der SAP HANA-Version unterstützt wird, die Sie installieren. Das unterstützte Betriebssystem für HANA 2.0 ist stärker eingeschränkt als das Betriebssystem, das für HANA 1.0 unterstützt wird. Außerdem müssen Sie überprüfen, ob die gewünschte Version des Betriebssystems für die jeweilige HLI-Einheit in dieser veröffentlichten [Liste](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) aufgeführt wird. Klicken Sie auf die Einheit, um die vollständigen Details mit der Liste der unterstützten Betriebssysteme dieser Einheit abzurufen. 

Überprüfen Sie Folgendes, bevor Sie mit der Installation von HANA beginnen:
- [HLI-Einheit(en)](#validate-the-hana-large-instance-units)
- [Betriebssystemkonfiguration](#operating-system)
- [Network Configuration](#networking)
- [Speicherkonfiguration](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Validieren der HANA-Einheiten (große Instanzen)

Nachdem Sie die HANA-Einheiten (große Instanzen) von Microsoft erhalten haben, überprüfen Sie die folgenden Einstellungen und passen sie nach Bedarf an.

Nachdem Sie SAP HANA (große Instanzen) erhalten und den Zugriff auf die Instanzen und die Konnektivität mit ihnen hergestellt haben, ist der **erste Schritt**, im Azure-Portal zu überprüfen, ob die Instanzen dort mit den richtigen SKUs und Betriebssystemen angezeigt werden. Lesen Sie [Steuerung von HANA in Azure (große Instanzen) über das Azure-Portal](./hana-li-portal.md), um sich über die erforderlichen Schritte zum Durchführen der Überprüfungen zu informieren.

Der **zweite Schritt** nach dem Erhalt der SAP HANA-Einheiten (große Instanzen) und der Einrichtung des Zugriffs und der Konnektivität besteht darin, das Betriebssystem der Instanz bei Ihrem Betriebssystemanbieter zu registrieren. Zu diesem Schritt gehört das Registrieren Ihres SUSE Linux-Betriebssystems in einer Instanz von SUSE SMT, die auf einer VM in Azure bereitgestellt ist. 

Die HANA-Einheit (große Instanzen) kann eine Verbindung mit dieser SMT-Instanz herstellen. (Weitere Informationen finden Sie unter [Einrichten eines SMT-Servers für SUSE Linux](hana-setup-smt.md).) Alternativ dazu muss Ihr Red Hat-Betriebssystem beim Red Hat Subscription Manager registriert werden, mit dem Sie eine Verbindung herstellen müssen. Weitere Informationen finden Sie unter [Was ist SAP HANA in Azure (große Instanzen)?](./hana-overview-architecture.md?toc=/azure/virtual-machines/linux/toc.json). 

Dieser Schritt ist auch zum Patchen des Betriebssystems notwendig – dies liegt in der Zuständigkeit des Kunden. Für SUSE finden Sie die Dokumentation zur Installation und Konfiguration von SMT auf der Seite zur [SMT-Installation](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

Der **dritte Schritt** besteht darin, nach neuen Patches und Fixes der speziellen Betriebssystemversion zu suchen. Überprüfen Sie, ob die Patchebene der HANA-Einheit (große Instanzen) auf dem neuesten Stand ist. Es kann Fälle geben, in denen die neuesten Patches nicht enthalten sind. Nach der Übernahme einer HANA-Einheit (große Instanzen) muss unbedingt geprüft werden, ob Patches angewendet werden müssen.

Der **vierte Schritt** besteht darin, die zutreffenden SAP-Hinweise (SAP Notes) zum Installieren und Konfigurieren von SAP HANA in der spezifischen Betriebssystemversion zu überprüfen. Aufgrund sich ändernder Empfehlungen oder Änderungen an SAP-Hinweisen oder -Konfigurationen, die von einzelnen Installationsszenarien abhängen, ist Microsoft nicht immer in der Lage, eine HANA-Einheit (große Instanzen) perfekt zu konfigurieren. 

Für Sie als Kunde ist es daher obligatorisch, dass Sie die SAP-Hinweise zu SAP HANA für Ihre genaue Linux-Version lesen. Prüfen Sie außerdem die Konfigurationen der Betriebssystemversion, und wenden Sie die Konfigurationseinstellungen an, falls dies nicht bereits geschehen ist.

Prüfen Sie insbesondere die folgenden Parameter, und passen Sie diese ggf. an:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

Seit SLES12 SP1 und RHEL 7.2 müssen diese Parameter in einer Konfigurationsdatei festgelegt sein, die sich im Verzeichnis „/etc/sysctl.d“ befindet. Beispielsweise muss eine Konfigurationsdatei namens „91-NetApp-HANA.conf“ erstellt werden. Für ältere SLES- und RHEL-Versionen müssen diese Parameter in „/etc/sysctl.conf“ festgelegt sein.

Beachten Sie bei allen RHEL-Versionen ab RHEL 6.3 Folgendes: 
- Der Parameter „sunrpc.tcp_slot_table_entries = 128“ muss auf „in/etc/modprobe.d/sunrpc-local.conf“ festgelegt sein. Wenn die Datei nicht vorhanden ist, müssen Sie diese zunächst erstellen, indem Sie folgenden Eintrag hinzufügen: 
    - options sunrpc tcp_max_slot_table_entries=128

Der **fünfte Schritt** besteht darin, die Systemzeit Ihrer SAP HANA-Einheit (große Instanzen) zu prüfen. Die Instanzen werden mit einer Systemzeitzone bereitgestellt. Diese Zeitzone repräsentiert den Standort der Azure-Region, in der sich der Stempel für die große HANA-Instanz befindet. Sie können die Systemzeit oder Zeitzone der Instanzen ändern, deren Besitzer Sie sind. 

Wenn Sie weitere Instanzen in Ihrem Mandanten bestellen, müssen Sie die Zeitzone der neu bereitgestellten Instanzen anpassen. Microsoft hat keinen Einblick in die Systemzeitzone, die Sie nach der Übergabe für die Instanzen einrichten. Daher kann es sein, dass neu bereitgestellte Instanzen nicht mit der Zeitzone eingerichtet sind, zu der Sie gewechselt haben. Es liegt in Ihrer Verantwortung als Kunde, die Zeitzone der übergebenen Instanzen ggf. anzupassen. 

Der **sechste Schritt** besteht darin, „etc/hosts“ zu prüfen. Wenn die Blades übergeben werden, haben sie unterschiedliche IP-Adressen, die für unterschiedliche Zwecke zugeordnet sind. Überprüfen Sie die Datei „etc/hosts“. Wenn Einheiten in einem vorhandenen Mandanten hinzugefügt werden, dürfen Sie nicht davon ausgehen, dass „etc/hosts“ der neu bereitgestellten Systeme den IP-Adressen von früher bereitgestellten Systemen entspricht. Es liegt in Ihrer Verantwortung als Kunde, sicherzustellen, dass eine neu bereitgestellte Instanz interagieren und die Namen von früher in Ihrem Mandanten bereitgestellten Einheiten auflösen kann. 


## <a name="operating-system"></a>Betriebssystem

Der Auslagerungsbereich des bereitgestellten Betriebssystemimage ist gemäß [SAP Support Note #1999997 – FAQ: SAP HANA Memory](https://launchpad.support.sap.com/#/notes/1999997/E) (SAP-Supporthinweis #1999997 – FAQ: SAP HANA-Arbeitsspeicher) auf 2 GB festgelegt. Wenn Sie als Kunde eine andere Einstellung wünschen, müssen Sie diese selbst festlegen.

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/download/) ist die Distribution von Linux, die für SAP HANA in Azure (große Instanzen) installiert wird. Diese spezielle Distribution bietet sofort einsatzbereite SAP-spezifische Funktionen (einschließlich vorab festgelegter Parameter für die effektive Ausführung von SAP unter SLES).

Auf der SUSE-Website unter [Resource Library/White Papers](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) und unter [SAP on SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) im SAP Community Network (SCN) finden Sie verschiedene nützliche Ressourcen in Zusammenhang mit der Bereitstellung von SAP HANA unter SLES (einschließlich der Einrichtung für Hochverfügbarkeit und Verstärkung der Sicherheit speziell für den SAP-Betrieb).

Zusätzliche nützliche Links zu SAP unter SUSE:

- [Website zu SAP HANA unter SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) (in englischer Sprache)
- [Best practices for SAP: Enqueue replication – SAP NetWeaver on SUSE Linux Enterprise 12](https://www.suse.com/media/guide/SLES4SAP-NetWeaver-ha-guide-EnqRepl-12_color_en.pdf)
- [ClamSAP – SLES Virus Protection for SAP](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (einschließlich SLES 12 für SAP-Anwendungen)

SAP-Supporthinweise zur Implementierung von SAP HANA unter SLES 12:

- [SAP Support Note #1944799 – SAP HANA Guidelines for SLES Operating System Installation](http://service.sap.com/sap/support/notes/1944799) (SAP-Supporthinweis 1944799 – SAP HANA-Richtlinien für die Installation des SLES-Betriebssystems)
- [SAP Support Note #2205917 – SAP HANA DB Recommended OS Settings for SLES 12 for SAP Applications](https://launchpad.support.sap.com/#/notes/2205917/E) (SAP-Supporthinweis 2205917 – SAP HANA DB: Empfohlene Betriebssystemeinstellungen für SLES 12 für SAP-Anwendungen)
- [SAP Support Note #1984787 – SUSE Linux Enterprise Server 12: Installation Notes](https://launchpad.support.sap.com/#/notes/1984787) (SAP-Supporthinweis 1984787 – SUSE Linux Enterprise Server 12: Installationshinweise)
- [SAP Support Note #171356 – SAP Software on Linux:  General Information](https://launchpad.support.sap.com/#/notes/1984787) (SAP-Supporthinweis 171356 – SAP-Software unter Linux: allgemeine Informationen)
- [SAP Support Note #1391070 – Linux UUID Solutions](https://launchpad.support.sap.com/#/notes/1391070) (SAP-Supporthinweis 1391070 – Linux UUID-Lösungen)

[Red Hat Enterprise Linux für SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) ist ein weiteres Angebot zum Ausführen von SAP HANA auf großen SAP HANA-Instanzen. Releases von RHEL 7.2 und 7.3 sind verfügbar und werden unterstützt. 

Zusätzliche nützliche Links zu SAP unter Red Hat:
- [Website zu SAP HANA unter Red Hat](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat) (in englischer Sprache)

SAP-Supporthinweise zur Implementierung von SAP HANA unter Red Hat:

- [SAP Support Note #2009879 – SAP HANA Guidelines for Red Hat Enterprise Linux (RHEL) Operating System](https://launchpad.support.sap.com/#/notes/2009879/E) (SAP-Supporthinweis 2009879 – SAP HANA-Richtlinien für das RHEL-Betriebssystem [Red Hat Enterprise Linux])
- [SAP support note #2292690 - SAP HANA DB: Recommended OS Settings for RHEL 7](https://launchpad.support.sap.com/#/notes/2292690) (2292690 – SAP HANA DB: empfohlene Betriebssystemeinstellungen für RHEL 7)
- [SAP Support Note #1391070 – Linux UUID Solutions](https://launchpad.support.sap.com/#/notes/1391070) (SAP-Supporthinweis 1391070 – Linux UUID-Lösungen)
- [SAP support note #2228351 - Linux: SAP HANA Database SPS 110 revision 6 (or higher) on RHEL 11 or SLES 11](https://launchpad.support.sap.com/#/notes/2228351) (SAP-Supporthinweis 2228351 – Linux: SAP HANA-Datenbank SPS 11 Revision 110 (oder höher) unter RHEL 6 oder SLES 11)
- [SAP support note #2397039 - FAQ: SAP on RHEL](https://launchpad.support.sap.com/#/notes/2397039) (SAP-Supporthinweis 2397039 – Häufig gestellte Fragen: SAP unter RHEL)
- [SAP support note #2002167 - Red Hat Enterprise Linux 7.x: Installation and upgrade](https://launchpad.support.sap.com/#/notes/2002167) (SAP-Supporthinweis 2002167 – Red Hat Enterprise Linux 7.x: Installation und Upgrade)

### <a name="time-synchronization"></a>Zeitsynchronisierung

SAP-Anwendungen, die auf der SAP NetWeaver-Architektur basieren, reagieren sehr empfindlich auf Zeitunterschiede bei den verschiedenen Komponenten, aus denen das SAP-System besteht. SAP ABAP-Kurzdumps mit der Fehlerbezeichnung „ZDATE\_LARGE\_TIME\_DIFF“ sind Ihnen möglicherweise bekannt. Das liegt daran, dass diese Kurzdumps angezeigt werden, wenn die Systemzeiten verschiedener Server oder VMs zu weit voneinander abweichen.

Bei SAP HANA in Azure (große Instanzen) gilt die in Azure erfolgende Zeitsynchronisierung nicht für die Compute-Einheiten in den Stempeln für große Instanzen. Diese Synchronisierung gilt nicht für die Ausführung von SAP-Anwendungen auf nativen Azure-VMs, da Azure sicherstellt, dass die Uhrzeit eines Systems richtig synchronisiert wird. 

Deshalb müssen Sie einen separaten Zeitserver einrichten, der von den auf Azure-VMs ausgeführten SAP-Anwendungsservern und von den in HANA (große Instanzen) ausgeführten SAP HANA-Datenbankinstanzen verwendet werden kann. Der Speicherinfrastruktur in Stempeln für große Instanzen wird mit NTP-Servern zeitlich synchronisiert.


## <a name="networking"></a>Netzwerk
Es wird davon ausgegangen, dass Sie beim Entwerfen Ihrer virtuellen Azure-Netzwerke und Verbinden dieser Netzwerke mit den großen HANA-Instanzen den Empfehlungen in den folgenden Dokumenten gefolgt sind:

- [Übersicht und Architektur von SAP HANA in Azure (große Instanzen)](./hana-overview-architecture.md)
- [Infrastruktur und Verbindungen mit SAP HANA in Azure (große Instanzen)](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Es gibt einige Details zum Netzwerkbetrieb der einzelnen Einheiten, die Sie kennen sollten. Jede HANA-Einheit (große Instanzen) wird mit zwei oder drei IP-Adressen geliefert, die zwei oder drei Netzwerkkartenports zugewiesen sind. Drei IP-Adressen werden in HANA-Konfigurationen für die horizontale Skalierung und im Szenario für die HANA-Systemreplikation verwendet. Eine der IP-Adressen, die der Netzwerkkarte der Einheit zugewiesen sind, stammt aus dem Server-IP-Pool, der in [Übersicht und Architektur von SAP HANA in Azure (große Instanzen)](./hana-overview-architecture.md) beschrieben ist.

Weitere Informationen zu den Ethernet-Daten für Ihre Architektur finden Sie unter [Unterstützte Szenarien für große HANA-Instanzen](hana-supported-scenario.md).

## <a name="storage"></a>Storage

Das Speicherlayout für SAP HANA in Azure (große Instanzen) wird von SAP HANA im Azure-`service management` basierend auf von SAP empfohlenen Richtlinien konfiguriert. Diese Richtlinien sind im Whitepaper [SAP HANA Storage Requirements](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (SAP HANA-Speicheranforderungen) dokumentiert. 

Die ungefähren Größen der verschiedenen Volumes mit den unterschiedlichen SKUs für HANA (große Instanzen) sind unter [Übersicht und Architektur von SAP HANA in Azure (große Instanzen)](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dokumentiert.

Die Benennungskonventionen der Speichervolumes sind in der folgenden Tabelle aufgeführt:

| Speicherverwendung | Mountname | Volumename | 
| --- | --- | ---|
| HANA-Daten | /hana/data/SID/mnt0000\<m> | Speicher-IP: /hana_data_SID_mnt00001_tenant_vol |
| HANA-Protokoll | /hana/log/SID/mnt0000\<m> | Speicher-IP: /hana_log_SID_mnt00001_tenant_vol |
| HANA-Protokollsicherung | /hana/log/backups | Speicher-IP: /hana_log_backups_SID_mnt00001_tenant_vol |
| HANA-Freigabe | /hana/shared/SID | Speicher-IP: /hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | Speicher-IP: /hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*SID* ist die System-ID der HANA-Instanz. 

*Tenant* ist eine interne Enumeration von Vorgängen beim Bereitstellen eines Mandanten.

„usr/sap“ in HANA nutzt das gleiche Volume. Die Nomenklatur der Mountpunkte enthält sowohl die System-ID der HANA-Instanzen als auch die Mountnummer. Bei Bereitstellungen für zentrales Skalieren ist nur ein Mount vorhanden, z.B. „mnt00001“. Bei Bereitstellungen für horizontales Skalieren werden so viele Mounts verwendet, wie Worker und Masterknoten vorhanden sind. 

Für Umgebungen für horizontales Skalieren werden Daten-, Protokoll- und Protokollsicherungsvolumes gemeinsam genutzt und an jeden Knoten in der Konfiguration für horizontales Skalieren angefügt. Für Konfigurationen mit mehreren SAP-Instanzen wird ein anderer Satz mit Volumes erstellt und an die HANA-Einheit (große Instanzen) angefügt. Speicherlayoutdetails für Ihr Szenario finden Sie unter [Unterstützte HLI-Szenarien](hana-supported-scenario.md).

Wenn Sie sich eine HANA-Einheit (große Instanzen) ansehen, werden Sie feststellen, dass die Einheiten mit einem großzügigen Datenträgervolume für „HANA/data“ bereitgestellt werden und dass es das Volume „HANA/log/backup“ gibt. Wir haben „HANA/data“ so großzügig dimensioniert, weil für die Speichermomentaufnahmen, die Ihnen als Kunde angeboten werden, dasselbe Datenträgervolume verwendet wird. Je mehr Speichermomentaufnahmen Sie erstellen, desto mehr Speicherplatz wird von Momentaufnahmen in Ihren zugewiesenen Speichervolumes verbraucht. 

Das Volume „HANA/log/backup“ ist nicht für die Speicherung von Datenbanksicherungen vorgesehen. Seine Größe ist so ausgelegt, dass es als Sicherungsvolume für Sicherungen von HANA-Transaktionsprotokollen verwendet werden kann. Weitere Informationen finden Sie unter [Hochverfügbarkeit und Notfallwiederherstellung für SAP HANA in Azure (große Instanzen)](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Zusätzlich zu dem bereitgestellten Speicher können Sie weitere Speicherkapazität in Schritten von 1 TB erwerben. Dieser zusätzliche Speicher kann einer großen HANA-Instanz in Form von neuen Volumes hinzugefügt werden.

Während des Onboardings mit SAP HANA beim Azure-`service management` gibt der Kunde eine Benutzer-ID (UID) und eine Gruppen-ID (GID) für den sidadm-Benutzer und die sapsys-Gruppe an (Beispiel: 1000,500). Während der Installation des SAP HANA-Systems müssen Sie genau diese Werte verwenden. Da Sie mehrere HANA-Instanzen in einer Einheit bereitstellen möchten, erhalten Sie mehrere Gruppen von Volumes (eine Gruppe für jede Instanz). Daher müssen Sie zur Bereitstellungszeit Folgendes definieren:

- Die SID der verschiedenen HANA-Instanzen („sidadm“ wird daraus abgeleitet).
- Die Arbeitsspeichergrößen der verschiedenen HANA-Instanzen. Die Arbeitsspeichergröße pro Instanz definiert die Größe der Volumes in den einzelnen Volumesätzen.

Basierend auf den Empfehlungen von Speicheranbietern werden für alle Mountvolumes (mit Ausnahme der Start-LUN) die folgenden Mountoptionen konfiguriert:

- nfs    rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

Diese Mountpunkte werden in „/etc/fstab“ konfiguriert, wie in den folgenden Abbildungen dargestellt:

![fstab von bereitgestellten Volumes in Einheit von HANA (große Instanzen)](./media/hana-installation/image1_fstab.PNG)

Die Ausgabe des Befehls „df -h“ in einer HANA-Einheit S72m (große Instanzen) sieht wie folgt aus:

![Der Screenshot zeigt die Ausgabe des Befehls für eine Einheit von HANA (große Instanzen).](./media/hana-installation/image2_df_output.PNG)


Der Speichercontroller und die Knoten in den „Große Instanz“-Stapeln werden mit NTP-Servern synchronisiert. Beim Synchronisieren der SAP HANA-Einheiten in Azure (große Instanzen) und Azure-VMs mit einem NTP-Server darf es zwischen der Infrastruktur und den Compute-Einheiten in Azure bzw. Stempeln für große Instanzen keine signifikante Zeitabweichung geben.

Um SAP HANA für den zugrunde liegenden Speicher zu optimieren, legen Sie die folgenden SAP HANA-Konfigurationsparameter fest:

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
Für SAP HANA 1.0-Versionen bis SPS12 können diese Parameter während der Installation der SAP HANA-Datenbank festgelegt werden, wie in [SAP Note #2267798 – Configuration of the SAP HANA Database](https://launchpad.support.sap.com/#/notes/2267798) (SAP-Hinweis 2267798 – Konfiguration der SAP HANA-Datenbank) beschrieben.

Sie können die Parameter auch nach der Installation der SAP HANA-Datenbank konfigurieren, indem Sie das hdbparam-Framework verwenden. 

Der in HANA (große Instanzen) genutzte Speicher weist eine Dateigrößenbeschränkung auf. Die [Größenbeschränkung beträgt 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) pro Datei. Anders als bei den Einschränkungen der Dateigröße in EXT3-Dateisystemen wird die durch HANA (große Instanzen) erzwungene Speichereinschränkung in HANA nicht implizit behandelt. Daher erstellt HANA nicht automatisch eine neue Datendatei, wenn die maximale Dateigröße von 16 TB erreicht wird. Während HANA versucht, die Datei über 16 TB hinaus zu vergrößern, werden Fehler gemeldet, und der Indexserver stürzt am Ende ab.

> [!IMPORTANT]
> Um zu verhindern, dass HANA versucht, Datendateien über die Dateigrößenbeschränkung von 16 TB für den Speicher in HANA (große Instanzen) hinaus zu vergrößern, müssen Sie in der Konfigurationsdatei „global.ini“ von SAP HANA die folgenden Parameter festlegen
> 
> - datavolume_striping=true
> - datavolume_striping_size_gb = 15000
> - Siehe auch SAP-Hinweis [2400005](https://launchpad.support.sap.com/#/notes/2400005).
> - Achten Sie auf SAP-Hinweis [2631285](https://launchpad.support.sap.com/#/notes/2631285).


Seit SAP HANA Version 2.0 ist das hdbparam-Framework veraltet. Daher müssen die Parameter mithilfe von SQL-Befehlen festgelegt werden. Weitere Informationen finden Sie in [SAP-Hinweis 2399079: Beseitigung von hdbparam in HANA 2](https://launchpad.support.sap.com/#/notes/2399079).

Informationen zum Speicherlayout für Ihre Architektur finden Sie unter [Unterstützte HLI-Szenarien](hana-supported-scenario.md).


**Nächste Schritte**

- Lesen Sie [HANA-Installation auf HLI](hana-example-installation.md).










































 







 
