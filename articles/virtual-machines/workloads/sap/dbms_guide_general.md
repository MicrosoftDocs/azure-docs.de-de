---
title: Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload| Microsoft-Dokumentation
description: Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b19c0fd8af2792a4ffb877e5c6a7fc6b3f94511
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60836116"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload
[1114181]: https://launchpad.support.sap.com/#/notes/1114181
[1409604]: https://launchpad.support.sap.com/#/notes/1409604
[1597355]: https://launchpad.support.sap.com/#/notes/1597355
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2039619]: https://launchpad.support.sap.com/#/notes/2039619
[2069760]: https://launchpad.support.sap.com/#/notes/2069760
[2171857]: https://launchpad.support.sap.com/#/notes/2171857
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Dieses Handbuch ist Teil der Dokumentation zur Implementierung und Bereitstellung von SAP-Software in Microsoft Azure. Lesen Sie vor diesem Handbuch das [Planungs- und Implementierungshandbuch][planning-guide]. Dieses Dokument behandelt die allgemeinen Aspekte der Bereitstellung von DBMS-Systemen für SAP auf Microsoft Azure Virtual Machines (VMs) mit der IaaS-Funktion (Infrastructure-as-a-Service) von Azure.

Das Dokument ergänzt die SAP-Installationsdokumentation und die SAP-Hinweise, die die primäre Ressource für Installationen und Bereitstellungen von SAP-Software auf verschiedenen Plattformen darstellen.

In diesem Dokument werden Überlegungen zum Ausführen von DBMS-Systemen für SAP auf Azure-VMs vorgestellt. Das Kapitel enthält nur wenige Verweise auf spezifische DBMS-Systeme. Stattdessen werden die spezifischen DBMS-Systeme weiter unten in diesem Artikel behandelt.

## <a name="definitions"></a>Definitionen
In diesem Dokument werden die folgenden Begriffe verwendet:

* **IaaS**: Infrastructure-as-a-Service
* **PaaS**: Platform-as-a-Service
* **SaaS**: Software-as-a-Service
* **SAP-Komponente**: Eine einzelne SAP-Anwendung, beispielsweise ERP Central Component (ECC), Business Warehouse (BW), Solution Manager oder Enterprise Portal (EP). SAP-Komponenten können auf herkömmlichen ABAP- oder Java-Technologien oder auf einer Nicht-NetWeaver-basierten Anwendung wie Business Objects basieren.
* **SAP-Umgebung**: Eine oder mehrere SAP-Komponenten, die logisch gruppiert sind, um eine Geschäftsfunktion wie Entwicklung, Qualitätssicherung, Training, Notfallwiederherstellung oder Produktion auszuführen.
* **SAP-Landschaft**: Alle SAP-Ressourcen in der IT-Landschaft eines Kunden. Die SAP-Landschaft umfasst alle Produktions- und anderen Umgebungen.
* **SAP-System**: Die Kombination einer DBMS-Schicht und einer Anwendungsschicht, die beispielsweise aus einem SAP ERP-Entwicklungssystem, einem SAP Business Warehouse-Testsystem oder einem SAP CRM-Produktionssystem bestehen kann. In Azure-Bereitstellungen wird die Aufteilung dieser beiden Schichten zwischen lokalen Systemen und Azure nicht unterstützt. Dies bewirkt, dass ein SAP-System entweder lokal oder in Azure bereitgestellt wird. Sie können die verschiedenen Systeme einer SAP-Landschaft in Azure oder lokal bereitstellen. Beispielsweise könnten Sie die SAP CRM-Entwicklungs- und Testsysteme in Azure, das SAP CRM-Produktionssystem jedoch lokal bereitstellen.
* **Standortübergreifend**: Beschreibt ein Szenario, in dem virtuelle Computer in einem Azure-Abonnement bereitgestellt werden, das Site-to-Site-, Multisite- oder ExpressRoute-Konnektivität zwischen den lokalen Rechenzentren und Azure umfasst. In allgemeinen Azure-Dokumentationen werden diese Arten von Bereitstellungen auch als standortübergreifende Szenarios bezeichnet. 

    Durch die Verbindung sollen lokale Domänen, das lokale Active Directory und das lokale DNS auf Azure ausgeweitet werden. Die lokale Landschaft wird auf die Azure-Ressourcen des Abonnements erweitert. Durch diese Erweiterung können die VMs Teil der lokalen Domäne sein. Domänenbenutzer der lokalen Domäne können auf die Server zugreifen und Dienste auf diesen virtuellen Computern ausführen (z. B. DBMS-Dienste). Die Kommunikation und Namensauflösung zwischen lokal bereitgestellten virtuellen Computern und in Azure bereitgestellten virtuellen Computern ist möglich. Dieses Szenario ist das gängigste für die Bereitstellung von SAP-Ressourcen in Azure. Weitere Informationen finden Sie unter [Planung und Entwurf für VPN-Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Bei standortübergreifenden Bereitstellungen von SAP-Systemen sind virtuelle Azure-Computer mit SAP-Systemen Mitglieder einer lokalen Domäne und werden für SAP-Produktionssysteme unterstützt. Standortübergreifende Konfigurationen werden für die Bereitstellung von Teilen von SAP-Landschaften oder vollständigen SAP-Landschaften in Azure unterstützt. Auch für das Ausführen von vollständigen SAP-Landschaften in Azure müssen diese VMs Teil einer lokalen Domäne und von Active Directory/LDAP sein. 
>
> In früheren Versionen der Dokumentation wurden Hybrid-IT-Szenarios erwähnt. Der Begriff *Hybrid* wurzelt in der Tatsache, dass es eine standortübergreifende Konnektivität zwischen der lokalen Umgebung und Azure besteht. In diesem Fall bedeutet „hybrid“ auch, dass die virtuellen Computer in Azure Teil der lokalen Active Directory-Instanz sind.
>
>

In einigen Microsoft-Dokumentationen werden standortübergreifende Szenarios etwas anders beschrieben, insbesondere bei DBMS-Hochverfügbarkeitskonfigurationen. In Dokumenten zu SAP bezieht sich ein standortübergreifendes Szenario nur auf Site-to-Site- oder private [ExpressRoute](https://azure.microsoft.com/services/expressroute/)-Konnektivität und eine SAP-Landschaft, die zwischen lokalen Systemen und Azure verteilt ist.

## <a name="resources"></a>Ressourcen
Zur SAP-Workload in Azure sind weitere Artikel verfügbar. Beginnen Sie mit [Erste Schritte mit SAP in Azure-VMs](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started), und wählen Sie dann den gewünschten Bereich.

Die folgenden SAP-Hinweise beziehen sich auf SAP in Azure und die in diesem Dokument behandelten Themen:

| Hinweisnummer | Titel |
| --- | --- |
| [1928533] |SAP-Anwendungen in Azure: Unterstützte Produkte und Azure-VM-Typen |
| [2015553] |SAP auf Microsoft Azure: Supportvoraussetzungen |
| [1999351] |Problembehandlung für die erweiterte Azure-Überwachung für SAP |
| [2178632] |Wichtige Überwachungsmetriken für SAP in Microsoft Azure |
| [1409604] |Virtualisierung unter Windows: Erweiterte Überwachung |
| [2191498] |SAP unter Linux mit Azure: Erweiterte Überwachung |
| [2039619] |SAP-Anwendungen auf Microsoft Azure mit der Oracle-Datenbank: Unterstützte Produkte und Versionen |
| [2233094] |DB6: SAP-Anwendungen in Azure mit IBM DB2 für Linux, UNIX und Windows: Zusätzliche Informationen |
| [2243692] |Microsoft Azure (IaaS)-VM: SAP-Lizenzprobleme |
| [1984787] |SUSE LINUX Enterprise Server 12: Installationshinweise |
| [2002167] |Red Hat Enterprise Linux 7.x: Installation und Upgrade |
| [2069760] |Oracle Linux 7.x SAP: Installation und Upgrade |
| [1597355] |Empfehlung zu Auslagerungsbereichen für Linux |
| [2171857] |Oracle Database 12c: Dateisystemunterstützung unter Linux |
| [1114181] |Oracle Database 11g: Dateisystemunterstützung unter Linux |


Informationen für alle SAP-Hinweise für Linux finden Sie im [Wiki der SAP-Community](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

Sie benötigen Kenntnisse der Microsoft Azure-Architektur sowie der Bereitstellung und Verwendung von virtuellen Microsoft Azure-Computern. Weitere Informationen finden Sie in der [Azure-Dokumentation](https://docs.microsoft.com/azure/).

Installation und Konfiguration von Windows, Linux und DBMS sind generell weitgehend identisch mit allen VMs oder Bare-Metal-Computern, die Sie lokal installieren. Bei der Implementierung der Architektur und Systemverwaltung gibt es jedoch einige Entscheidungen, die bei der Nutzung von Azure IaaS anders sind. Dieses Dokument erläutert die spezifischen Unterschiede bei Architektur und Systemverwaltung, die Sie bei der Verwendung von Azure IaaS kennen sollten.


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Speicherstruktur einer VM für RDBMS-Bereitstellungen
Damit Sie diesem Kapitel folgen können, sollten Sie die Informationen [diesem Kapitel][deployment-guide-3] des [Bereitstellungshandbuch][deployment-guide] gelesen und verstanden haben. Sie müssen die verschiedenen VM-Serien und die Unterschiede zwischen Standard- und Premium-Speicher verstehen und kennen, bevor Sie dieses Kapitel lesen. 

Informationen zu Azure Storage für Azure-VMs finden Sie unter:

- [Einführung in verwaltete Azure-Datenträger für Azure Windows-VMs](../../windows/managed-disks-overview.md)
- [Einführung in verwaltete Azure-Datenträger für Azure Linux-VMs](../../linux/managed-disks-overview.md)

In einer Basiskonfiguration eignet sich eine Bereitstellungsstruktur, bei der das Betriebssystem, das DBMS und eventuelle SAP-Binärdateien von den Datenbankdateien getrennt sind. Wird empfehlen, dass bei SAP-Systemen, die auf Azure-VMs ausgeführt werden, das Basis-VHD – oder der Datenträger – mit dem Betriebssystem, den ausführbaren Dateien für das Datenbankverwaltungssystem und den ausführbaren SAP-Dateien installiert wird. 

Die DBMS-Daten und -Protokolldateien werden in Storage Standard oder Storage Premium gespeichert. Sie werden auf separaten Datenträgern gespeichert und als logische Datenträger an die ursprüngliche Azure-VM mit dem Betriebssystemimage angefügt. Für Linux-Bereitstellungen sind speziell für SAP HANA verschiedene Empfehlungen dokumentiert.

Ermitteln Sie beim Planen Ihres Festplattenlayouts die beste Balance zwischen diesen Elementen:

* Die Anzahl der Datendateien.
* Die Anzahl von Datenträgern, die die Dateien enthalten
* Die IOPS-Kontingente eines einzelnen Datenträgers
* Den Datendurchsatz pro Datenträger
* Die Anzahl der für die jeweilige VM-Größe möglichen zusätzlichen Datenträger
* Der Gesamtspeicherdurchsatz, den ein virtueller Computer bereitstellen kann.
* Die Latenz verschiedener Azure Storage-Typen.
* SLAs für VMs.

Azure erzwingt ein IOPS-Kontingent pro Datenträger. Diese Kontingente sind für Datenträger, die in Storage Standard und Storage Premium gehostet werden, unterschiedlich. Auch die E/A-Latenz unterscheidet sich zwischen den beiden Speichertypen. Storage Premium bietet eine bessere E/A-Latenz. 

Für jeden der verschiedenen VM-Typen können Sie eine begrenzte Anzahl von Datenträgern anfügen. Eine weitere Einschränkung besteht darin, dass nur bestimmte VM-Typen Storage Premium nutzen können. In der Regel entscheiden Sie sich basierend auf CPU- und Arbeitsspeicheranforderungen für einen bestimmten VM-Typ. Sie können auch die Anforderungen an IOPS, Latenz und Festplattendurchsatz berücksichtigen, die normalerweise mit der Anzahl der Festplatten oder dem Typ der Storage Premium-Datenträger skaliert werden. Die Festplattengröße kann insbesondere bei Storage Premium durch die Anzahl der IOPS und den Durchsatz, den jeder Datenträger erreichen muss, bestimmt werden.

> [!NOTE]
> Für DBMS-Bereitstellungen empfehlen wir die Verwendung von Storage Premium für alle Daten, Transaktionsprotokolle und Wiederholungsdateien. Dabei spielt es keine Rolle, ob Sie Produktionssysteme bereitstellen möchten oder nicht.

> [!NOTE]
> Um die einzigartigen Azure-Vorteile der [SLA für einzelne VMs](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) zu nutzen, müssen alle angefügten Datenträger vom Typ Storage Premium sein, einschließlich der Basis-VHD.

> [!NOTE]
> Das Hosten von Hauptdatenbankdateien wie Daten- und Protokolldateien von SAP-Datenbanken auf Speicherhardware, die sich in gemeinsam untergebrachten Drittanbieter-Datencentern neben Azure-Datencentern befindet, wird nicht unterstützt. Für SAP-Workloads wird für Daten- und Transaktionsprotokolldateien von SAP-Datenbanken nur Speicher unterstützt, der als nativer Azure-Dienst umgesetzt ist.

Die Platzierung der Datenbank-, der Protokoll- und der Wiederholungsdateien sowie der verwendete Azure Storage-Typ müssen durch die Anforderungen an IOPS, Latenz und Durchsatz bestimmt werden. Damit ausreichend IOPS verfügbar ist, müssen Sie möglicherweise mehrere Datenträger oder einen größeren Storage Premium-Datenträger verwenden. Falls Sie mehrere Datenträger verwenden, erstellen Sie einen Softwarestripe über die Datenträger hinweg, die die Datendateien oder die Protokoll- und Wiederholungsdateien enthalten. In solchen Fällen sind die IOPS- und die Datenträgerdurchsatz-SLAs der zugrunde liegenden Storage Premium-Datenträger oder der maximal erzielbare IOPS der Storage Standard-Datenträger kumulativ für das resultierende Stripeset.

Wenn Ihre IOPS-Anforderungen das übersteigen, was eine einzelne VHD bereitstellen kann, müssen Sie den für die Datenbankdateien benötigten IOPS über die Anzahl von VHDs ausgleichen. Die einfachste Möglichkeit, die IOPS-Last auf verschiedene Datenträger zu verteilen, ist das Erstellen eines Softwarestripes über die verschiedenen Datenträger hinweg. Fügen Sie dann den LUNs im Softwarestripe eine Anzahl von Datendateien des SAP-DBMS hinzu. Die Anzahl von Datenträgern im Stripe wird von den Anforderungen an IOPS, Datenträgerdurchsatz und Volumes bestimmt.


- - -
> ![Windows][Logo_Windows] Windows
>
> Es wird empfohlen, Windows-Speicherplätze zum Erstellen von Stripesets über mehrere Azure-VHDs hinweg zu verwenden. Verwenden Sie mindestens Windows Server 2012 R2 oder Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> Nur MDADM und Logical Volume Manager (LVM) werden unterstützt, um ein Software-RAID unter Linux zu erstellen. Weitere Informationen finden Sie unter
>
> - [Konfigurieren von Software-RAID unter Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) mithilfe von MDADM
> - [Konfigurieren von LVM auf einem virtuellen Linux-Computer in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) (LVM)
>
>

- - -

> [!NOTE]
> Da Azure Storage drei Images der VHDs speichert, ist es nicht ratsam, beim Striping eine Redundanz zu konfigurieren. Sie müssen nur das Striping so konfigurieren, dass die E/A-Vorgänge auf die verschiedenen VHDs verteilt werden.
>

### <a name="managed-or-nonmanaged-disks"></a>Verwaltete oder nicht verwaltete Datenträger
Ein Azure-Speicherkonto ist nicht nur ein Konzept für Verwaltungsaufgaben, sondern stellt auch eine Gruppe von Einschränkungen dar. Die Einschränkungen unterscheiden sich zwischen Storage Standard und Storage Premium-Konten. Informationen zu Funktionen und Einschränkungen finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

Denken Sie daran, dass bei Storage Standard eine Begrenzung der IOPS pro Speicherkonto gilt. Finden Sie die Zeile mit der **Gesamtanforderungsrate** im Artikel [Skalierbarkeits- und Leistungsziele für Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets). Für Speicherkonten gilt pro Azure-Abonnement außerdem eine anfängliche Einschränkung. Verteilen Sie daher VHDs für die größeren SAP-Landschaften auf verschiedene Speicherkonten, um die Grenzen dieser Speicherkonten nicht zu überschreiten. Dies ist sehr aufwändig, wenn Sie einige hundert VMs mit über tausend VHDs haben.

Da die Verwendung von Standard-Speicher für DBMS-Bereitstellungen in Verbindung mit SAP-Workload nicht empfohlen wird, sind die entsprechenden Verweise und Empfehlungen auf diesen kurzen [Artikel](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx) beschränkt.

Damit keine Verwaltung der Planung und Bereitstellung von VHDs über verschiedene Azure-Speicherkonten hinweg erforderlich ist, führte Microsoft 2017 den Dienst [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) ein. Verwaltete Datenträger sind für Storage Standard und Storage Premium verfügbar. Hauptvorteile von verwalteten Datenträgern im Vergleich zu nicht verwalteten Datenträgern:

- Für verwaltete Datenträger verteilt Azure die verschiedenen VHDs zum Zeitpunkt der Bereitstellung automatisch auf verschiedene Speicherkonten. Auf diese Weise werden die Grenzwerte des Speicherkontos für Datenvolumen, E/A-Durchsatz und IOPS nicht überschritten.
- Mit verwalteten Datenträgern berücksichtigt Azure Storage die Konzepte von Azure-Verfügbarkeitsgruppen. Wenn die VM Teil einer Azure-Verfügbarkeitsgruppe ist, werden die Basis-VHD und die angefügte Festplatte einer VM in verschiedenen Fehler- und Update-Domänen bereitgestellt.


> [!IMPORTANT]
> Angesichts der Vorteile wird empfohlen, Azure Managed Disks für Ihre DBMS-Bereitstellungen und SAP-Bereitstellungen im Allgemeinen zu verwenden.
>

Informationen zum Konvertieren nicht verwalteter Datenträger in verwaltete Datenträger finden Sie unter:

- [Konvertieren einer Windows-VM von nicht verwalteten Datenträgern in verwaltete Datenträger](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)
- [Konvertieren einer Linux-VM von nicht verwalteten Datenträgern in verwaltete Datenträger](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks)


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Zwischenspeichern für VMs und Datenträger
Wenn Sie Datenträger auf VMs bereitstellen, können Sie auswählen, ob der E/A-Datenverkehr zwischen der VM und diesen Datenträgern in Azure Storage zwischengespeichert wird. Storage Standard und Premium verwenden zwei unterschiedliche Technologien für diese Art von Cache.

Bei den folgenden Empfehlungen werden die E/A-Merkmale für Standard-DBMS zugrunde gelegt:

- Es handelt sich hauptsächlich um Lesevorgänge für Datendateien einer Datenbank. Diese Lesevorgänge sind leistungskritisch für das DBMS-System.
- Das Schreiben in die Datendateien erfolgt in Bursts, die auf Prüfpunkten oder einem konstanten Datenstrom basieren. Pro Tag erfolgen im Durchschnitt weniger Schreib- als Lesezugriffe. Im Gegensatz zum Lesen von Datendateien sind diese Schreibvorgänge asynchron und verzögern keine Benutzertransaktionen.
- Es gibt kaum Lesevorgänge aus den Transaktionsprotokollen oder Wiederholungsdateien. Ausnahmen sind große E/A-Vorgänge beim Ausführen von Transaktionsprotokollsicherungen.
- Bei Transaktions- und Wiederholungsprotokolldateien stellen Schreibvorgänge die Hauptlast dar. Je nach Art der Workload kann die Größe von E/As bis zu 4 KB herunter- oder in anderen Fällen bis zu 1 MB oder mehr heraufreichen.
- Alle Schreibvorgänge müssen zuverlässig und dauerhaft auf einem Datenträger gespeichert werden.

Für Storage Standard sind folgende Cachetypen möglich:

* Keine
* Lesen
* Lesen/Schreiben

Um eine konsistente und deterministische Leistung zu erzielen, legen Sie die Zwischenspeicherung in Storage Standard für alle Datenträger mit DBMS-bezogenen Datendateien, Protokoll- und Wiederholungsdateien und Tabellenspeicherplatz auf **KEINE** fest. Für das Zwischenspeichern der Basis-VHD kann die Standardeinstellung übernommen werden.

Storage Premium hat die folgenden Optionen zum Zwischenspeichern:

* Keine
* Lesen
* Lesen/Schreiben
* Keine + Schreibbeschleunigung (nur für Azure-VMs der M-Serie)
* Lesen + Schreibbeschleunigung (nur für Azure-VMs der M-Serie)

Für Storage Premium wird empfohlen, das **Zwischenspeichern von Lesevorgängen für Datendateien** in der SAP-Datenbank zu nutzen und **Kein Zwischenspeichern für die Datenträger von Protokolldateien** festzulegen.

Für Bereitstellungen der M-Serie empfehlen wir, die Azure-Schreibbeschleunigung für die DBMS-Bereitstellung zu verwenden. Weitere Informationen, Einschränkungen und Bereitstellungsdetails für die Azure-Schreibbeschleunigung finden Sie im Dokument [Schreibbeschleunigung](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


### <a name="azure-nonpersistent-disks"></a>Nicht beständige Azure-Datenträger
Azure-VMs bieten Datenträger mit flüchtigem Speicher, nachdem eine VM bereitgestellt wurde. Bei einem Neustart der VM werden alle Inhalte auf diesen Laufwerken gelöscht. Daher sollten Datendateien und Protokoll- bzw. Wiederholungsdateien von Datenbanken unter keinen Umständen auf diesen nicht permanenten Laufwerken gespeichert werden. Möglicherweise gibt es Ausnahmen für einige Datenbanken, in denen diese nicht permanenten Laufwerke für die Tabellenbereiche „tempdb“ und „temp“ geeignet sind. Verwenden Sie diese Laufwerke nicht für VMs der A-Serie, da diese nicht permanenten Laufwerke mit diesen VMs nur einen begrenzten Durchsatz ermöglichen. 

Weitere Informationen finden Sie unter [Understand the temporary drive on Windows VMs in Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/) (Grundlegendes zum temporären Laufwerk auf Windows-VMs in Azure).

- - -
> ![Windows][Logo_Windows] Windows
>
> Laufwerk D auf einer Azure-VM ist ein nicht permanentes Laufwerk, das durch einige lokale Datenträger im Azure-Serverknoten gesichert wird. Da es nicht permanent ist, gehen alle auf Laufwerk D vorgenommenen Änderungen am Inhalt verloren, wenn die VM neu gestartet wird. Änderungen umfassen gespeicherte Dateien, erstellte Verzeichnisse und installierte Anwendungen.
>
> ![Linux][Logo_Linux] Linux
>
> Azure-VMs unter Linux stellen ein Laufwerk automatisch unter „/mnt/resource“ bereit. Dies ist ein nicht permanentes Laufwerk, das durch lokale Datenträger auf dem Azure-Serverknoten gesichert wird. Da es nicht permanent ist, gehen alle Änderungen am Inhalt unter „/mnt/resource“ verloren, wenn die VM neu gestartet wird. Änderungen umfassen gespeicherte Dateien, erstellte Verzeichnisse und installierte Anwendungen.
>
>

- - -



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure Storage – Resilienz
Microsoft Azure Storage speichert die Basis-VM (mit Betriebssystem) und angefügte Datenträger oder Blobs in mindestens drei getrennten Speicherknoten. Diese Art von Speicher wird als „lokal redundanter Speicher“ (LRS) bezeichnet. LRS ist die Standardeinstellung für alle Azure-Speichertypen.

Es gibt andere Redundanzmethoden. Weitere Informationen finden Sie unter [Azure Storage-Replikation](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>Storage Premium ist der empfohlene Speichertyp für DBMS-VMs und Datenträger, die Datenbank- sowie Protokoll- und Wiederholungsdateien speichern. Die einzige verfügbare Redundanzmethode für Storage Premium ist LRS. Daher müssen Sie Datenbankmethoden konfigurieren, um die Replikation von Datenbankdaten in eine andere Azure-Region oder Verfügbarkeitszone zu ermöglichen. Datenbankmethoden sind beispielsweise SQL Server Always On, Oracle Data Guard und HANA-Systemreplikation.


> [!NOTE]
> Bei DBMS-Bereitstellungen wird die Verwendung von georedundantem Speicher (GRS) für Storage Standard nicht empfohlen. GRS beeinträchtigt die Leistung erheblich und berücksichtigt außerdem nicht die Schreibreihenfolge über verschiedene VHDs hinweg, die an eine VM angeschlossen sind. Die Nichtberücksichtigung der Schreibreihenfolge über verschiedene VHDs hinweg kann zu inkonsistenten Datenbanken auf der Zielseite der Replikation führen. Diese Situation tritt auf, wenn Datenbank- sowie Protokoll- und Wiederholungsdateien auf mehrere VHDs verteilt sind, wie es aufseiten der Quell-VM üblich ist.



## <a name="vm-node-resiliency"></a>VM-Knoten – Resilienz
Azure bietet verschiedene SLAs für VMs. Weitere Informationen finden Sie im letzten Release der [SLA für Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Da die DBMS-Schicht in der Regel kritisch für die Verfügbarkeit in einem SAP-System ist, müssen Sie mit Verfügbarkeitsgruppen, Verfügbarkeitszonen und Wartungsereignissen vertraut sein. Weitere Informationen zu diesen Konzepten finden Sie unter [Verwalten der Verfügbarkeit virtueller Windows-Computer in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) und [Verwalten der Verfügbarkeit virtueller Linux-Computer](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

Mindestempfehlung für DBMS-Produktionsszenarien mit einer SAP-Workload:

- Bereitstellen von zwei VMs in einer separaten Verfügbarkeitsgruppe in derselben Azure-Region.
- Führen Sie diese beiden VMs im gleichen virtuellen Azure-Netzwerk aus, und fügen Sie NICs aus den gleichen Subnetzen an.
- Verwenden Sie Datenbankmethoden, um einen unmittelbar betriebsbereiten Standbyserver mit der zweiten VM beizubehalten. Mögliche Methoden sind SQL Server Always On, Oracle Data Guard oder HANA-Systemreplikation.

Sie können auch eine dritte VM in einer anderen Azure-Region bereitstellen und die gleichen Datenbankmethoden verwenden, um ein asynchrones Replikat in einer anderen Azure-Region anzugeben.

Informationen zum Einrichten von Azure-Verfügbarkeitsgruppen finden Sie in [diesem Tutorial](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Überlegungen zum Azure-Netzwerk
Verwenden Sie in großen SAP-Bereitstellungen die Blaupause des [virtuellen Azure-Rechenzentrums](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter). Verwenden Sie sie für die Konfiguration des virtuellen Netzwerks sowie für Berechtigungen und Rollenzuweisungen zu verschiedenen Teilen Ihrer Organisation.

Diese bewährten Methoden sind das Ergebnis von Hunderten von Kundenbereitstellungen:

- Die virtuellen Netzwerke, in denen die SAP-Anwendung bereitgestellt wird, haben keinen Zugriff auf das Internet.
- Die Datenbank-VMs werden im selben virtuellen Netzwerk ausgeführt wie die Anwendungsschicht.
- Die VMs im virtuellen Netzwerk haben eine statische Zuteilung der privaten IP-Adresse. Weitere Informationen finden Sie unter [IP-Adresstypen und Zuordnungsmethoden in Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).
- Routingeinschränkungen in und aus DBMS-VMs werden *nicht* mit Firewalls festgelegt, die auf den lokalen DBMS-VMs installiert sind. Stattdessen wird Routing mit [Netzwerksicherheitsgruppen (NSGs)](https://docs.microsoft.com/azure/virtual-network/security-overview) definiert.
- Weisen Sie der VM verschiedene NICs zu, um den Datenverkehr zu den DBMS-VMs zu trennen und zu isolieren. Jede NIC erhält eine andere IP-Adresse und ist einem anderen Subnetz des virtuellen Netzwerks zugeordnet. Für jedes Subnetz gelten verschiedene NSG-Regeln. Die Isolation oder die Trennung des Netzwerkverkehrs ist eine Routingmaßnahme. Sie wird nicht zum Festlegen von Kontingenten zur Steigerung des Netzwerkdurchsatzes verwendet.

> [!NOTE]
> Statische IP-Adressen über Azure zuzuweisen bedeutet, sie einzelnen virtuellen NICs zuzuweisen. Weisen Sie statische IP-Adressen nicht innerhalb des Gastbetriebssystems einer virtuellen NIC zu. Einige Azure-Dienste (beispielsweise Azure Backup) sind darauf angewiesen, dass mindestens die primäre virtuelle NIC auf DHCP festgelegt ist und nicht auf statische IP-Adressen. Weitere Informationen finden Sie unter [Problembehandlung bei der Sicherung virtueller Azure-Computer](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Wenn Sie einem virtuellen Computer mehrere statische IP-Adressen zuweisen möchten, müssen Sie ihm mehrere virtuelle NICs zuweisen.
>


> [!IMPORTANT]
> Das Konfigurieren [virtueller Netzwerkgeräte](https://azure.microsoft.com/solutions/network-appliances/) im Kommunikationspfad zwischen der SAP-Anwendung und der DBMS-Schicht eines SAP NetWeaver-, Hybris- oder S/4HANA-basierten SAP-Systems wird nicht unterstützt. Diese Einschränkung gilt aus Funktions- und Leistungsgründen. Der Kommunikationspfad zwischen der SAP-Anwendungsschicht und der DBMS-Schicht muss ein direkter Pfad sein. Die Einschränkung gilt nicht für [Regeln für Anwendungssicherheitsgruppen (ASGs) und NSGs](https://docs.microsoft.com/azure/virtual-network/security-overview), wenn diese ASG- und NSG-Regeln einen direkten Kommunikationspfad zulassen. 
>
> Weitere Szenarien, in denen virtuelle Netzwerkgeräte nicht unterstützt werden:
>
> * Kommunikationspfade zwischen virtuellen Azure-Computern, die Linux Pacemaker-Clusterknoten und SBD-Geräte darstellen. Dies ist unter [Hochverfügbarkeit für SAP NetWeaver auf Azure-VMs auf dem SUSE Linux Enterprise Server for SAP Applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) beschrieben.
> * Kommunikationspfade zwischen virtuellen Azure-Computern und einem Windows Server-Dateiserver mit horizontaler Skalierung (SOFS), die wie unter [Gruppieren einer SAP ASCS/SCS-Instanz in einem Windows-Failovercluster per Dateifreigabe in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share) beschrieben eingerichtet wurden. 
>
> Virtuelle Netzwerkgeräte in Kommunikationspfaden können die Netzwerklatenz zwischen zwei Kommunikationspartnern locker verdoppeln. Außerdem können sie den Durchsatz in kritischen Pfaden zwischen der SAP-Anwendungsschicht und der DBMS-Schicht einschränken. In einigen Kundenszenarien können virtuelle Netzwerkgeräte fehlerhafte Linux Pacemaker-Clustern zur Folge zu haben. In diesen Fällen erfolgt die Kommunikation zwischen den Linux Pacemaker-Clusterknoten und dem SBD-Gerät über ein virtuelles Netzwerkgerät.
>

> [!IMPORTANT]
> Ein weiterer *nicht* unterstützter Entwurf ist die Aufteilung der SAP-Anwendungsschicht und der DBMS-Schicht auf verschiedene virtuelle Azure-Netzwerke, für die kein [Peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) konfiguriert ist. Es wird empfohlen, die SAP-Anwendungsschicht und die DBMS-Schicht durch Subnetze innerhalb eines virtuellen Azure-Netzwerks zu trennen, anstatt verschiedene virtuelle Azure-Netzwerke zu verwenden. 
>
> Wenn Sie sich dafür entscheiden, der Empfehlung nicht zu folgen und stattdessen die beiden Schichten auf verschiedene virtuelle Netzwerke aufzuteilen, muss für die beiden virtuellen Netzwerke ein [Peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) konfiguriert werden. 
>
> Beachten Sie, dass für den Netzwerkdatenverkehr zwischen zwei virtuellen Azure-Netzwerken mit [Peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Übertragungskosten anfallen. Zwischen der SAP-Anwendungsschicht und der DBMS-Schicht werden riesige Datenmengen im Terabytebereich ausgetauscht. Somit können erhebliche Kosten entstehen, wenn die SAP-Anwendungsschicht und die DBMS-Schicht auf zwei mittels Peering verknüpfte virtuelle Azure-Netzwerke aufgeteilt sind.

Verwenden Sie zwei VMs für Ihre DBMS-Produktionsbereitstellung innerhalb einer Azure-Verfügbarkeitsgruppe. Verwenden Sie auch separates Routing für die SAP-Anwendungsschicht sowie den Datenverkehr für die Verwaltung und Vorgänge zu den beiden DBMS-VMs. Sehen Sie sich die folgende Abbildung an:

![Diagramm: zwei VMs in zwei Subnetzen](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Verwenden von Azure Load Balancer zum Umleiten von Datenverkehr
Die Verwendung von privaten virtuellen IP-Adressen, die in Funktionen wie SQL Server Always On oder HANA-Systemreplikation verwendet werden, erfordert die Konfiguration einer Azure Load Balancer-Instanz. Der Lastenausgleich verwendet Testports, um den aktiven DBMS-Knoten zu ermitteln und den Datenverkehr ausschließlich an diesen aktiven Datenbankknoten weiterzuleiten. 

Bei einem Failover des Datenbankknotens muss die SAP-Anwendung nicht erneut konfiguriert werden. Stattdessen werden die gängigsten SAP-Anwendungsarchitekturen wieder mit der privaten virtuellen IP-Adresse verbunden. Inzwischen reagiert der Lastenausgleich auf das Failover des Knotens, indem er den Datenverkehr an die private virtuelle IP-Adresse auf dem zweiten Knoten umleitet.

Azure bietet zwei verschiedene [Load Balancer-SKUs](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview): Basic und Standard. Wenn Sie nicht über Azure-Verfügbarkeitszonen hinweg bereitstellen möchten, ist die Load-Balancer-SKU „Basic“ vollkommen ausreichend.

Wird der Datenverkehr zwischen den DBMS-VMs und der SAP-Anwendungsschicht immer über den Lastenausgleich weitergeleitet? Die Antwort hängt von der Load Balancer-Konfiguration ab. 

Zu diesem Zeitpunkt wird der eingehende Datenverkehr an die DBMS-VM immer über den Lastenausgleich geleitet. Der ausgehende Datenverkehr von der DBMS-VM zur Anwendungsschicht-VM hängt von der Konfiguration des Lastenausgleichs ab. 

Die Load Balancer-Instanz bietet eine Möglichkeit, DirectServerReturn. Wenn diese Option konfiguriert ist, wird der Datenverkehr von der DBMS-VM zur SAP-Anwendungsschicht *nicht* über den Lastenausgleich weitergeleitet. Stattdessen geht er direkt zur Anwendungsschicht. Wenn „DirectServerReturn“ nicht konfiguriert ist, wird der zur SAP-Anwendungsschicht zurückkommende Datenverkehr über den Lastenausgleich weitergeleitet.

Wir empfehlen, „DirectServerReturn“ in Kombination mit Lastenausgleichen zu konfigurieren, die zwischen der SAP-Anwendungsschicht und der DBMS-Schicht positioniert sind. Diese Konfiguration verringert die Netzwerklatenz zwischen den zwei Schichten.

Ein Beispiel für die Einrichtung dieser Konfiguration mit SQL Server Always On finden Sie unter [Konfigurieren eines ILB-Listeners für Always On-Verfügbarkeitsgruppen in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Wenn Sie veröffentlichte GitHub-JSON-Vorlagen als Referenz für Ihre SAP-Infrastrukturbereitstellungen in Azure verwenden, sehen Sie sich diese [Vorlage für ein dreischichtiges SAP-System](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md) an. In dieser Vorlage finden Sie auch die korrekten Einstellungen für den Lastenausgleich.

### <a name="azure-accelerated-networking"></a>Beschleunigter Netzwerkbetrieb in Azure
Um die Netzwerklatenz zwischen Azure-VMs weiter zu reduzieren, empfehlen wir Ihnen, [Beschleunigter Netzwerkbetrieb in Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) zu wählen. Verwenden Sie diese Option, wenn Sie Azure-VMs für eine SAP-Workload bereitstellen, insbesondere für die SAP-Anwendungsschicht und die SAP DBMS-Schicht.

> [!NOTE]
> Nicht alle VM-Typen unterstützen den beschleunigten Netzwerkbetrieb. Der vorherige Artikel führt die VM-Typen auf, die den beschleunigten Netzwerkbetrieb unterstützen.
>

- - -
> ![Windows][Logo_Windows] Windows
>
> Unter [Erstellen eines virtuellen Windows-Computers mit beschleunigtem Netzwerkbetrieb](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) erfahren Sie, wie VMs mit beschleunigtem Netzwerkbetrieb für Windows bereitgestellt werden.
>
> ![Linux][Logo_Linux] Linux
>
> Weitere Informationen zur Linux-Distribution finden Sie unter [Erstellen eines virtuellen Linux-Computers mit beschleunigtem Netzwerkbetrieb](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).
>
>

- - -

> [!NOTE]
> Im Falle von SUSE, Red Hat und Oracle Linux wird der beschleunigte Netzwerkbetrieb von den aktuellen Releases unterstützt. Ältere Releases wie SLES 12 SP2 oder RHEL 7.2 unterstützen den beschleunigten Netzwerkbetrieb in Azure nicht.
>


## <a name="deployment-of-host-monitoring"></a>Bereitstellen der Hostüberwachung
Für die Verwendung von SAP-Anwendungen auf Azure-VMs in der Produktion benötigt SAP die Möglichkeit, Hostüberwachungsdaten von den physischen Hosts abzurufen, auf denen die Azure-VMs ausgeführt werden. Es wird eine bestimmte SAP-Host-Agent-Patchebene benötigt, die diese Funktion in SAPOSCOL und SAP-Host-Agent ermöglicht. Die Patchebenen sind in SAP-Hinweis [1409604]genau dokumentiert.

Weitere Informationen zur Bereitstellung von Komponenten, die Hostdaten für SAPOSCOL und den SAP-Host-Agent liefern, sowie zur Verwaltung des Lebenszyklus dieser Komponenten finden Sie im [Bereitstellungshandbuch][deployment-guide].


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu einem bestimmten DBMS finden Sie unter:

- [Azure Virtual Machines – SQL Server-DBMS-Bereitstellung für SAP-Workload](dbms_guide_sqlserver.md)
- [Azure Virtual Machines – Oracle-DBMS-Bereitstellung für SAP-Workload](dbms_guide_oracle.md)
- [Azure Virtual Machines – IBM DB2-DBMS-Bereitstellung für SAP-Workload](dbms_guide_ibm.md)
- [Azure Virtual Machines – SAP ASE-DBMS-Bereitstellung für SAP-Workload](dbms_guide_sapase.md)
- [Bereitstellung von SAP MaxDB, SAP liveCache und SAP Content Server in Azure](dbms_guide_maxdb.md)
- [SAP HANA in Azure – Betriebshandbuch](hana-vm-operations.md)
- [Hochverfügbarkeit von SAP HANA für virtuelle Azure-Computer](sap-hana-availability-overview.md)
- [Sicherungsanleitung für SAP HANA in Azure Virtual Machines](sap-hana-backup-guide.md)

