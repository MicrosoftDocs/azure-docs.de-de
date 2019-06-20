---
title: 'Schnellstart: Manuelle Installation von SAP HANA (Einzelinstanz) auf Azure-VMs | Microsoft-Dokumentation'
description: Schnellstartanleitung für die manuelle Installation von SAP HANA (Einzelinstanz) auf virtuellen Azure-Computern
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: 5091932989849943f00cb71f72378dd17af23a4a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60204821"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-virtual-machines"></a>Schnellstart: Manuelle Installation von SAP HANA (Einzelinstanz) auf Azure-VMs
## <a name="introduction"></a>Einführung
Diese Anleitung hilft Ihnen beim Einrichten eines SAP HANA-Einzelinstanzsystems auf Azure Virtual Machines, wenn Sie SAP NetWeaver 7.5 und SAP HANA 1.0 SP12 manuell installieren. Der Schwerpunkt dieser Anleitung liegt auf der Bereitstellung von SAP HANA in Azure. Sie stellt keinen Ersatz der SAP-Dokumentation dar. 

> [!NOTE]
> In dieser Anleitung werden Bereitstellungen von SAP HANA auf Azure-VMs beschrieben. Informationen zum Bereitstellen von SAP HANA in großen HANA-Instanzen finden Sie unter [Verwenden von SAP auf virtuellen Azure-Computern](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Voraussetzungen
Die Anleitung setzt voraus, dass Sie mit den folgenden Azure IaaS-Grundlagen (Infrastructure-as-a-Service) vertraut sind:
 * Bereitstellen virtueller Computer (VMs) oder virtueller Netzwerke über das Azure-Portal oder PowerShell.
 * Die plattformübergreifende Azure-Befehlszeilenschnittstelle (CLI), einschließlich der Möglichkeit, Vorlagen für JavaScript Object Notation (JSON) zu verwenden.

Darüber hinaus wird in dieser Anleitung vorausgesetzt, dass Sie mit Folgendem vertraut sind:
* SAP HANA und SAP NetWeaver und deren lokale Installation.
* Installieren und Ausführen von SAP HANA und von SAP-Anwendungsinstanzen in Azure.
* Zudem müssen Sie mit folgenden Konzepte und Verfahren vertraut sein:
   * Planen der Bereitstellung von SAP in Azure, einschließlich der Planung für Azure Virtual Network und der Verwendung von Azure Storage. Siehe [SAP NetWeaver in Azure Virtual Machines – Planungs- und Implementierungshandbuch](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Bereitstellungsprinzipien und Möglichkeiten der Bereitstellungen von VMs in Azure. Siehe [Bereitstellung von Azure Virtual Machines für SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Hochverfügbarkeit für SAP NetWeaver ABAP SAP Central Services (ASCS), SAP Central Services (SCS) und Enqueue Replication Server (ERS) in Azure. Siehe [Hochverfügbarkeit von SAP NetWeaver auf virtuellen Azure-Computern](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Einzelheiten zum Steigern der Effizienz in einer Multi-SID-Installation von ASCS/SCS in Azure. Siehe [Erstellen einer SAP NetWeaver Multi-SID-Konfiguration](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Prinzipien der Ausführung von SAP NetWeaver basierend auf Linux-VMs in Azure. Siehe [Ausführen von SAP NetWeaver auf Microsoft Azure-SUSE Linux-VMs](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Dieses Handbuch enthält die spezifischen Einstellungen für Linux auf Azure-VMs. Sie erhalten auch Informationen zum richtigen Anfügen von Azure-Speicherdatenträgern an Linux-VMs.

Die Arten von virtuellen Azure-Computern, die für Produktionsszenarien verwendet werden können, sind in der [SAP-Dokumentation für IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html) aufgeführt. Für Nicht-Produktionsszenarien ist eine größere Auswahl von nativen Azure-VMs verfügbar.
Weitere Informationen zur Konfiguration und dem Betrieb von VMs finden Sie unter [SAP HANA-Infrastrukturkonfigurationen und -Vorgänge in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
Informationen zur SAP HANA-Hochverfügbarkeit finden Sie in [Hochverfügbarkeit von SAP HANA für virtuelle Azure-Computer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).

Wenn Sie eine SAP HANA-Instanz oder ein S/4HANA- oder BW/4HANA-System schnell bereitstellen möchten, sollten Sie die Verwendung von [SAP Cloud Appliance Library](https://cal.sap.com) in Betracht ziehen. Eine Dokumentation zum Bereitstellen z. B. eines S/4HANA-Systems über SAP Cloud Appliance Library finden Sie in [diesem Leitfaden](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Sie benötigen lediglich ein Azure-Abonnement und einen SAP-Benutzer, der bei SAP Cloud Appliance Library registriert werden kann.

## <a name="additional-resources"></a>Zusätzliche Ressourcen
### <a name="sap-hana-backup"></a>SAP HANA-Sicherungen
Informationen zum Sichern von SAP HANA-Datenbanken auf Azure-VMs finden Sie hier:
* [Sicherungsanleitung für SAP HANA in Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [SAP HANA-Sicherung mit Azure Backup auf Dateiebene](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
* [SAP HANA-Sicherung auf der Grundlage von Speichermomentaufnahmen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

### <a name="sap-cloud-appliance-library"></a>SAP Cloud Appliance Library
Weitere Informationen zur Verwendung von SAP Cloud Appliance Library zum Bereitstellen von S/4HANA oder BW/4HANA finden Sie unter [Bereitstellen von SAP S/4HANA oder BW/4HANA in Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>Von SAP HANA unterstützte Betriebssysteme
Informationen zu von SAP HANA unterstützten Betriebssystemen finden Sie unter [SAP-Hinweis 2235581 – SAP HANA: Unterstützte Betriebssysteme](https://launchpad.support.sap.com/#/notes/2235581/E). Azure-VMs unterstützen lediglich einen Teil dieser Betriebssysteme. Für die folgenden Betriebssysteme wird die Bereitstellung von SAP HANA in Azure unterstützt: 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

Eine weitere SAP-Dokumentation zu SAP HANA und anderen Linux-Betriebssystemen finden Sie hier:

* [SAP-Hinweis 171356: SAP-Software unter Linux: Allgemeine Informationen](https://launchpad.support.sap.com/#/notes/1984787).
* [SAP-Hinweis 1944799: SAP HANA-Richtlinien für die Installation des SLES-Betriebssystems](https://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html).
* [SAP-Hinweis 2205917: SAP HANA DB: Empfohlene Betriebssystemeinstellungen für SLES 12 für SAP-Anwendungen](https://launchpad.support.sap.com/#/notes/2205917/E).
* [SAP-Hinweis 1391070: Linux-UUID-Lösungen](https://launchpad.support.sap.com/#/notes/1391070).
* [SAP-Hinweis 2009879: SAP HANA-Richtlinien für das RHEL-Betriebssystem (Red Hat Enterprise Linux)](https://launchpad.support.sap.com/#/notes/2009879).
* [SAP-Hinweis 2292690: SAP HANA DB: Empfohlene Betriebssystemeinstellungen für RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E).

### <a name="sap-monitoring-in-azure"></a>SAP-Überwachung in Azure
Informationen zur SAP-Überwachung in Azure:

* In [SAP-Hinweis 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) wird die erweiterte Überwachung von SAP mit Linux-VMs unter Azure erläutert. 
* [SAP-Hinweis 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) enthält Informationen zu SAPOSCOL unter Linux. 
* [SAP-Hinweis 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) enthält wichtige Überwachungsmetriken für SAP in Microsoft Azure.

### <a name="azure-vm-types"></a>Arten von virtuellen Azure-Computern
Azure-VM-Typen und von SAP unterstützte Workloadszenarien für SAP HANA sind unter [SAP certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html) (IaaS-Plattformen mit SAP-Zertifizierung) dokumentiert. 

Azure-VM-Typen, die von SAP für SAP NetWeaver oder die S/4HANA-Anwendungsschicht zertifiziert wurden, sind in dokumentiert in [SAP-Hinweis 1928533: SAP-Anwendungen in Azure: Supported products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533/E) (SAP-Anwendungen unter Azure: Unterstützte Produkte und Azure-VM-Typen).

> [!NOTE]
> Die SAP-Linux-Azure-Integration wird nur für Azure Resource Manager und nicht für das klassische Bereitstellungsmodell unterstützt. 

## <a name="manual-installation-of-sap-hana"></a>Manuelle Installation von SAP HANA

> [!IMPORTANT]
> Stellen Sie sicher, dass das von Ihnen ausgewählte Betriebssystem auf den spezifischen VM-Typen, die Sie verwenden, für SAP HANA SAP-zertifiziert ist. Die Liste der SAP HANA-zertifizierten VM-Typen und BS-Releases für diese VM-Typen kann unter [Zertifizierte SAP HANA-IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) nachgeschlagen werden. Stellen Sie sicher, dass Sie in die Details des jeweils aufgeführten VM-Typs klicken, um die vollständige Liste der von SAP HANA unterstützten BS-Releases für den spezifischen VM-Typ anzuzeigen. Für das Beispiel in diesem Dokument haben wir ein SUSE Linux Enterprise Server (SLES)-BS-Release verwendet, das von SAP für SAP HANA auf VMs der M-Serie nicht unterstützt wird.
>

In dieser Anleitung werden zwei unterschiedliche Möglichkeiten zum manuellen Installieren von SAP HANA auf Azure-VMs beschrieben:

* Mit dem SAP Software Provisioning Manager (SWPM) im Rahmen einer verteilten NetWeaver-Installation im Schritt „Datenbankinstanzinstallation“
* Mit dem SAP HANA-Datenbank-Lebenszyklusverwaltungstool (HDBLCM), und einer anschließenden Installation von NetWeaver

Sie können auch SWPM verwenden, um alle Komponenten wie SAP HANA, SAP-Anwendungsserver und die ASCS-Instanz auf einer einzelnen VM zu installieren. Die Schritte sind in dieser [SAP HANA-Blogankündigung](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/) beschrieben. Diese Option wird in dieser Schnellstartanleitung nicht beschrieben, es sind jedoch dieselben Aspekte zu berücksichtigen.

Vor dem Starten eines Installationsvorgangs empfiehlt es sich, den Abschnitt „Vorbereiten von Azure-VMs für die manuelle Installation von SAP HANA“ später in dieser Anleitung durchzulesen. Auf diese Weise können mehrere grundlegende Fehler vermieden werden, die auftreten können, wenn Sie nur eine standardmäßige Azure-VM-Konfiguration verwenden.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Wichtige Schritte für die SAP HANA-Installation bei Verwendung von SAP SWPM
Dieser Abschnitt enthält die wichtigsten Schritte für eine manuelle SAP HANA-Einzelinstanzinstallation, wenn Sie SAP SWPM verwenden, um eine verteilte SAP NetWeaver 7.5-Installation durchzuführen. Die einzelnen Schritte werden ausführlicher anhand von Screenshots weiter unten in dieser Anleitung erläutert.

1. Erstellen Sie ein virtuelles Azure-Netzwerk, das die beiden Test-VMs enthält.
2. Stellen Sie die zwei Azure-VMs mit Betriebssystemen über das Azure Resource Manager-Modell bereit. In diesem Beispiel werden SUSE Linux Enterprise Server und SLES for SAP Applications 12 SP1 verwendet. 
3. Fügen Sie zwei Azure Storage Standard- oder Premium-Datenträger (z. B. Datenträger mit 75 oder 500 GB) an die Anwendungsserver-VM an.
4. Fügen Sie Storage Premium-Datenträger an die HANA DB-Server-VM an. Weitere Informationen finden Sie im Abschnitt „Datenträgereinrichtung“ weiter unten in dieser Anleitung.
5. Fügen Sie je nach Größen- oder Durchsatzanforderungen mehrere Datenträger an. Erstellen Sie dann Stripesetvolumes. Verwenden Sie entweder die logische Volumeverwaltung (Logical Volume Management, LVM) oder ein Tool zur Verwaltung mehrerer Geräte (mdadm) auf Betriebssystemebene innerhalb der VM.
6. Erstellen Sie XFS-Dateisysteme auf den angefügten Datenträgern oder logischen Volumes.
7. Stellen Sie die neuen XFS-Dateisysteme auf Betriebssystemebene bereit. Verwenden Sie ein Dateisystem für die gesamte SAP-Software. Verwenden Sie das andere Dateisystem beispielsweise für das Verzeichnis „/sapmnt“ und Sicherungen. Stellen Sie auf dem SAP HANA-DB-Server XFS-Dateisysteme auf den Datenträgern für Storage Premium als „/hana“ und „/usr/sap“ bereit. Dieser Prozess ist erforderlich, um zu verhindern, dass sich das Stammdateisystem füllt. Das Stammdateisystem ist auf Linux-Azure-VMs nicht groß. 
8. Geben Sie die lokalen IP-Adressen der Test-VMs in der Datei „/etc/hosts“ ein.
9. Geben Sie den Parameter **nofail** in der Datei „/etc/fstab“ ein.
10. Legen Sie die Linux-Kernelparameter gemäß der verwendeten Version des Linux-Betriebssystems fest. Weitere Informationen finden Sie in den SAP-Hinweisen, in denen HANA erläutert wird, sowie im Abschnitt „Kernelparameter“ in dieser Anleitung.
11. Fügen Sie den Auslagerungsbereich hinzu.
12. Installieren Sie optional einen grafischen Desktop auf den Test-VMs. Verwenden Sie andernfalls eine SAPinst-Remoteinstallation.
13. Laden Sie die SAP-Software vom SAP Service Marketplace herunter.
14. Installieren Sie die SAP ASCS-Instanz auf der App-Server-VM.
15. Geben Sie das Verzeichnis „/sapmnt“ zwischen den Test-VMs mithilfe von NFS frei. Die Anwendungsserver-VM ist der NFS-Server.
16. Installieren Sie die Datenbankinstanz, einschließlich HANA, mit SWPM auf der DB-Server-VM.
17. Installieren Sie den primären Anwendungsserver (PAS) auf der Anwendungsserver-VM.
18. Starten Sie die SAP Management Console (SAP MC). Stellen Sie eine Verbindung mit beispielsweise SAP GUI oder HANA Studio her.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Wichtige Schritte für die SAP HANA-Installation bei Verwendung von HDBLCM
Dieser Abschnitt enthält die wichtigsten Schritte für eine manuelle SAP HANA-Einzelinstanzinstallation, wenn Sie SAP HDBLCM verwenden, um eine verteilte SAP NetWeaver 7.5-Installation durchzuführen. Die einzelnen Schritte werden ausführlicher anhand von Screenshots in dieser Anleitung erläutert.

1. Erstellen Sie ein virtuelles Azure-Netzwerk, das die beiden Test-VMs enthält.
2. Stellen Sie zwei Azure-VMs mit Betriebssystemen über das Azure Resource Manager-Modell bereit. In diesem Beispiel werden SLES und SLES for SAP Applications 12 SP1 verwendet.
3. Fügen Sie zwei Azure Storage Standard- oder Premium-Datenträger, z. B. Datenträger mit 75 oder 500 GB, an die Anwendungsserver-VM an.
4. Fügen Sie Storage Premium-Datenträger an die HANA DB-Server-VM an. Weitere Informationen finden Sie im Abschnitt „Datenträgereinrichtung“ weiter unten in dieser Anleitung.
5. Fügen Sie je nach Größen- oder Durchsatzanforderungen mehrere Datenträger an. Erstellen Sie Stripesetvolumes, indem Sie entweder logische Volumeverwaltung oder ein mdadm-Tool auf Betriebssystemebene innerhalb der VM verwenden.
6. Erstellen Sie XFS-Dateisysteme auf den angefügten Datenträgern oder logischen Volumes.
7. Stellen Sie die neuen XFS-Dateisysteme auf Betriebssystemebene bereit. Verwenden Sie ein Dateisystem für die gesamte SAP-Software. Verwenden Sie das andere Dateisystem beispielsweise für das Verzeichnis „/sapmnt“ und Sicherungen. Stellen Sie auf dem SAP HANA-DB-Server XFS-Dateisysteme auf den Datenträgern für Storage Premium als „/hana“ und „/usr/sap“ bereit. Dieser Prozess ist erforderlich, um zu verhindern, dass sich das Stammdateisystem füllt. Das Stammdateisystem ist auf Linux-Azure-VMs nicht groß.
8. Geben Sie die lokalen IP-Adressen der Test-VMs in der Datei „/etc/hosts“ ein.
9. Geben Sie den Parameter **nofail** in der Datei „/etc/fstab“ ein.
10. Legen Sie die Kernelparameter gemäß der verwendeten Version des Linux-Betriebssystems fest. Weitere Informationen finden Sie in den SAP-Hinweisen, in denen HANA erläutert wird, sowie im Abschnitt „Kernelparameter“ in dieser Anleitung.
11. Fügen Sie den Auslagerungsbereich hinzu.
12. Installieren Sie optional einen grafischen Desktop auf den Test-VMs. Verwenden Sie andernfalls eine SAPinst-Remoteinstallation.
13. Laden Sie die SAP-Software vom SAP Service Marketplace herunter.
14. Erstellen Sie eine Gruppe „sapsys“ mit der Gruppen-ID 1001 auf der HANA DB-Server-VM.
15. Installieren Sie SAP HANA mithilfe des HANA-Datenbanklebenszyklus-Managers auf der DB-Server-VM.
16. Installieren Sie die SAP ASCS-Instanz auf der App-Server-VM.
17. Geben Sie das Verzeichnis „/sapmnt“ zwischen den Test-VMs mithilfe von NFS frei. Die Anwendungsserver-VM ist der NFS-Server.
18. Installieren Sie die Datenbankinstanz, einschließlich HANA, mit SWPM auf der HANA-DB-Server-VM.
19. Installieren Sie den primären Anwendungsserver auf der Anwendungsserver-VM.
20. Starten Sie die SAP MC. Stellen Sie eine Verbindung über die SAP-GUI oder HANA Studio her.

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>Vorbereiten von Azure-VMs auf eine manuelle Installation von SAP HANA
Dieser Abschnitt enthält die folgenden Themen:

* Betriebssystemupdates
* Datenträgereinrichtung
* Kernelparameter
* Dateisysteme
* Die Datei „/etc/hosts“
* Die Datei „/etc/fstab“

### <a name="os-updates"></a>Betriebssystemupdates
Suchen Sie nach Linux-Betriebssystemupdates und -fixes, bevor Sie weitere Software installieren. Durch Installieren eines Patches können Sie u. U. einen Anruf beim Helpdesk vermeiden.

Achten Sie darauf, Folgendes zu verwenden:
* SUSE Linux Enterprise Server für SAP-Anwendungen.
* Red Hat Enterprise Linux für SAP-Anwendungen oder Red Hat Enterprise Linux für SAP HANA. 

Registrieren Sie die Betriebssystembereitstellung unter Ihrem Linux-Abonnement, das Sie vom Linux-Anbieter erhalten haben (sofern dies noch nicht erfolgt ist). SUSE enthält Betriebssystemimages für SAP-Anwendungen, die bereits Dienste enthalten und automatisch registriert werden.

Dies ist ein Beispiel für die Suche nach verfügbaren Patches für SUSE Linux durch Ausführen des Befehls **zypper**:

 `sudo zypper list-patches`

Je nach Art des Problems sind die Patches nach Kategorie und Schweregrad klassifiziert. Häufig verwendete Werte für die Kategorie sind: 
- Sicherheit
- Empfohlen
- Optional
- Feature
- Dokument
- Yast

Häufig verwendete Werte für den Schweregrad sind:

- Kritisch
- Wichtig
- Moderat
- Niedrig
- Unspecified

Mit dem Befehl **zypper** wird nur nach den Updates gesucht, die für die installierten Pakete benötigt werden. Sie können beispielsweise diesen Befehl verwenden:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Sie können den `--dry-run`-Parameter hinzufügen, um das Update zu testen, ohne dass das System tatsächlich aktualisiert wird.


### <a name="disk-setup"></a>Datenträgereinrichtung
Für das Stammdateisystem einer Linux-VM unter Azure gilt eine Größenbeschränkung. Daher müssen Sie zusätzlichen Festplattenspeicher an eine Azure-VM anfügen, um SAP auszuführen. Für die SAP-Anwendungsserver-Azure-VMs kann die Nutzung von Azure Standard Storage-Datenträgern ausreichend sein. Für die SAP HANA-DBMS-Azure-VMs ist die Nutzung von Azure Storage Premium-Datenträgern für die Implementierung zu Produktions- und anderen Zwecken obligatorisch.

Basierend auf den SAP HANA TDI-Speicheranforderungen ([SAP HANA TDI Storage Requirements](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)) empfehlen wir die folgende Konfiguration von Azure Storage Premium: 

| VM-SKU | RAM |  „/hana/data“ und „/hana/log“ <br /> Striping mit LVM oder mdadm | /hana/shared | /root volume | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

Bei der vorgeschlagenen Datenträgerkonfiguration werden das HANA-Datenvolume und -Protokollvolume auf der gleichen Gruppe von Azure Storage Premium-Datenträgern angeordnet, für die das Striping per LVM oder mdadm durchgeführt wird. Es ist nicht erforderlich, eine RAID-Redundanzebene zu definieren, da bei Azure Storage Premium aus Redundanzgründen drei Images der Datenträger aufbewahrt werden. 

Um sicherzustellen, dass Sie eine ausreichende Speichermenge konfigurieren, beachten Sie die [SAP HANA TDI-Speicheranforderungen](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) und den [SAP HANA-Server-Installations- und Updateleitfaden](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Informieren Sie sich auch über die verschiedenen VHD-Durchsatzvolumes (Virtual Hard Disk) der unterschiedlichen Azure Storage Premium-Datenträger, die unter [Storage Premium-Hochleistungsspeicher und verwaltete Datenträger für VMs](../../windows/disks-types.md) dokumentiert sind. 

Sie können den HANA DBMS-VMs weitere Storage Premium-Datenträger hinzufügen, um Datenbank- oder Transaktionsprotokollsicherungen zu speichern.

Weitere Informationen zu den beiden wichtigsten Tools zum Konfigurieren von Striping finden Sie in den folgenden Artikeln:

* [Konfigurieren von Software-RAID unter Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Konfigurieren von LVM auf einem virtuellen Linux-Computer in Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Weitere Informationen zum Anfügen von Datenträgern an Azure-VMs, auf denen Linux als Gastbetriebssystem ausgeführt wird, finden Sie unter [Hinzufügen eines Datenträgers zu einem virtuellen Linux-Computer](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Mit SSD Premium-Datenträgern von Azure können Sie Zwischenspeicherungsmodi für Datenträger definieren. Deaktivieren Sie für das Stripeset mit „/hana/data“ und „/hana/log“ die Zwischenspeicherung von Datenträgern. Für die anderen Volumes (Datenträger) legen Sie den Zwischenspeicherungsmodus auf **ReadOnly** fest.

JSON-Beispielvorlagen für die Erstellung von VMs finden Sie in den [Azure-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates).
Die Vorlage „vm-simple-sles“ ist eine grundlegende Vorlage. Sie enthält einen Speicherabschnitt mit einem zusätzlichen 100-GB-Datenträger. Verwenden Sie diese Vorlage als Basis. Sie können die Vorlage an Ihre spezifischen Konfiguration anpassen.

> [!NOTE]
> Sie müssen den Azure Storage-Datenträger unbedingt mithilfe von UUID anfügen. Die Vorgehensweise wird in [Ausführen von SAP NetWeaver auf Microsoft Azure SUSE Linux-VMs](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dokumentiert.

In der Testumgebung sind zwei Azure-Standarddatenträger für die Speicherung der SAP-App-Server-VM angefügt, wie im folgenden Screenshot gezeigt. Auf einem Datenträger wurde die gesamte SAP-Software für die Installation gespeichert (z. B. von NetWeaver 7.5, SAP GUI und SAP HANA). Der zweite Datenträger stellt sichert, dass ausreichend freier Speicherplatz für zusätzliche Anforderungen verfügbar ist. Beispielsweise müssen Sicherungs- und Testdaten sowie das Verzeichnis „/sapmnt“, d. h. SAP-Profile, für alle VMs freigegeben werden, die zur gleichen SAP-Landschaft gehören.

![SAP HANA-App-Server-Datenträgerfenster, in dem zwei Datenträger und ihre Größen angezeigt werden](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Kernelparameter
SAP HANA erfordert spezifische Linux-Kerneleinstellungen. Diese Kerneleinstellungen sind nicht Teil des Standardimage des Azure-Katalogs und müssen manuell festgelegt werden. Die Parameter können abweichen, je nachdem, ob SUSE oder Red Hat verwendet wird. Die zuvor aufgeführten SAP-Hinweise enthalten Informationen zu diesen Parametern. In den hier gezeigten Screenshots wurde SUSE Linux 12 SP1 verwendet. 

SLES for SAP Applications 12 mit allgemeiner Verfügbarkeit und SLES for SAP Applications 12 SP1 verfügen über das neue Tool **tuned-adm**, das an Stelle des alten Tools **sapconf** tritt. Ein spezielles SAP HANA-Profil ist für **tuned-adm** verfügbar. Zum Optimieren des Systems für SAP HANA geben Sie das folgende Profil als Root-Benutzer ein:

   `tuned-adm profile sap-hana`

Weitere Informationen zu **tuned-adm** finden Sie in der [SUSE-Dokumentation zu „tuned-adm“](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

Der folgende Screenshot zeigt, wie mit **tuned-adm** die Werte für `transparent_hugepage` und `numa_balancing` gemäß den erforderlichen SAP HANA-Einstellungen geändert wurden:

![Das „tuned-adm“-Tool ändert Werte gemäß der erforderlichen SAP HANA-Einstellungen](./media/hana-get-started/image005.jpg)

Um die SAP HANA-Kerneleinstellungen permanent zu machen, muss **grub2** unter SLES 12 verwendet werden. Weitere Informationen zu **grub2** finden Sie im Abschnitt [Configuration File Structure](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) (Struktur der Konfigurationsdatei) der SUSE-Dokumentation.

Der folgende Screenshot zeigt, wie die Kerneleinstellungen in der Konfigurationsdatei geändert und dann mit **grub2-mkconfig** kompiliert wurden:

![In der Konfigurationsdatei geänderte und mit „grub2-mkconfig“ kompilierte Kerneleinstellungen](./media/hana-get-started/image006.jpg)

Eine andere Möglichkeit ist die Änderung der Einstellungen über YaST und die **Boot Loader** > **Kernel Parameters**-Einstellungen:

![Die Registerkarte "Kernel Parameters" im YaST Boot Loader](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Dateisysteme
Der folgende Screenshot zeigt zwei Dateisysteme, die auf der SAP-App-Server-VM zusätzlich zu den beiden angefügten Azure-Standarddatenträgern für die Speicherung erstellt wurden. Beide Dateisysteme sind vom Typ XFS und werden unter „/sapdata“ und „/sapsoftware“ bereitgestellt.

Es ist nicht erforderlich, Ihre Dateisysteme auf diese Weise zu strukturieren. Sie haben andere Optionen für die Strukturierung des Datenträger-Speicherplatzes. Der wichtigste Aspekt ist, zu verhindern, dass dem Stammdateisystem kein freier Speicherplatz mehr zur Verfügung steht.

![Zwei auf der SAP-App-Server-VM erstellte Dateisysteme](./media/hana-get-started/image008.jpg)

Für die SAP HANA DB-VM wird während einer Datenbankinstallation bei Verwendung von SAPinst mit SWPM und der Installationsoption **Typisch** alles unter „/hana“ und „/usr/sap“ installiert. Der Standardspeicherort für die Sicherung von SAP HANA-Protokollen befindet sich unter „/usr/sap“. Wiederum ist es wichtig zu verhindern, dass dem Stammdateisystem kein freier Speicherplatz mehr zur Verfügung steht. Stellen Sie sicher, dass unter „/hana“ und „/usr/sap“ genügend freier Speicherplatz vorhanden ist, bevor Sie SAP HANA mithilfe von SWPM installieren.

Eine Beschreibung des standardmäßigen Dateisystemlayouts von SAP HANA finden Sie im [Handbuch zu Installation und Update von SAP HANA-Servern](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![Zusätzliche auf der SAP-App-Server-VM erstellte Dateisysteme](./media/hana-get-started/image009.jpg)

Beim Installieren von SAP NetWeaver auf einem Standardimage aus dem SLES/SLES für SAP Applications 12-Azure-Katalog wird eine Meldung angezeigt, dass kein Auslagerungsbereich vorhanden ist (siehe folgender Screenshot). Um diese Meldung zu schließen, können Sie manuell eine Auslagerungsdatei mithilfe von **dd**, **mkswap** und **swapon** hinzufügen. Informationen zur Vorgehensweise erhalten Sie, indem Sie im Abschnitt [Using the YaST Partitioner](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) (Verwenden des YaST-Partitionierers) der SUSE-Dokumentation nach „Adding a Swap File Manually“ (Manuelles Hinzufügen einer Auslagerungsdatei) suchen.

Eine weitere Möglichkeit ist das Konfigurieren des Auslagerungsbereichs über den Linux-VM-Agent. Weitere Informationen erhalten Sie im [Benutzerhandbuch für Azure Linux-Agent](../../extensions/agent-linux.md).

![Popupmeldung, die darüber informiert, dass der Auslagerungsbereich nicht ausreicht](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>Die Datei „/etc/hosts“
Vergewissern Sie sich vor Beginn der Installation von SAP, dass Sie die Hostnamen und IP-Adressen der SAP-VMs in die Datei „/etc/hosts“ einschließen. Stellen Sie alle SAP-VMs in einem virtuellen Azure-Netzwerk bereit. Verwenden Sie dann die internen IP-Adressen wie hier gezeigt:

![In der /etc/hosts-Datei aufgelistete Hostnamen und IP-Adressen der SAP-VMs](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>Die Datei „/etc/fstab“

Es empfiehlt sich, der Datei „fstab“ den Parameter **nofail** hinzuzufügen. Falls ein Problem mit den Datenträgern auftritt, reagiert die VM beim Startprozess dennoch. Bedenken Sie jedoch, dass möglicherweise kein zusätzlicher Speicherplatz zur Verfügung steht und Prozesse das Stammdateisystem auffüllen können. Wenn „/hana“ nicht vorhanden ist, wird SAP HANA nicht gestartet.

![Hinzufügen des Parameters „nofail“ zur Datei „fstab“](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Grafischer GNOME-Desktop unter SLES 12/SLES für SAP Applications 12
In diesem Abschnitt wird Folgendes beschrieben:

* Installieren des GNOME-Desktops und von xrdp unter SLES 12/SLES für SAP Applications 12
* Ausführen der Java-basierten SAP MC mit Firefox unter SLES 12/SLES für SAP Applications 12

Sie können auch Alternativen wie Xterminal oder VNC nutzen (diese werden in der vorliegenden Anleitung nicht beschrieben).

### <a name="install-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Installieren des GNOME-Desktops und von xrdp unter SLES 12/SLES für SAP Applications 12
Wenn Sie einen Windows-Hintergrund haben, können Sie mühelos direkt einen grafischen Desktop in den SAP-Linux-VMs verwenden, um Firefox, SAPinst, SAP GUI, SAP MC oder HANA Studio auszuführen. Anschließend können Sie von einem Windows-Computer aus über das Remotedesktopprotokoll (RDP) eine Verbindung mit dem virtuellen Computer herstellen. Je nach geltenden Unternehmensrichtlinien zum Hinzufügen von GUIs zu Linux-Systemen für Produktions- und andere Zwecke empfiehlt es sich, GNOME auf Ihrem Server zu installieren. Führen Sie diese Schritte aus, um den GNOME-Desktop auf einer Azure-VM unter SLES 12/SLES für SAP Applications 12 zu installieren.

1. Installieren Sie den GNOME-Desktop mit dem folgenden Befehl, z. B. in einem PuTTY-Fenster:

   `zypper in -t pattern gnome-basic`

2. Installieren Sie „xrdp“, um eine Verbindung mit der VM per RDP zu ermöglichen:

   `zypper in xrdp`

3. Bearbeiten Sie „/etc/sysconfig/windowmanager“, und legen Sie den Standard-Manager für Fenster auf „GNOME“ fest:

   `DEFAULT_WM="gnome"`

4. Stellen Sie durch Ausführen von **chkconfig** sicher, dass „xrdp“ nach einem Neustart automatisch gestartet wird:

   `chkconfig -level 3 xrdp on`

5. Falls ein Problem mit der RDP-Verbindung auftritt, können Sie es mit einem Neustart probieren, z. B. über ein PuTTY-Fenster:

   `/etc/xrdp/xrdp.sh restart`

6. Wenn ein Neustart von „xrdp“ wie im vorherigen Schritt nicht möglich ist, suchen Sie nach einer PID-Datei:

   `check /var/run` 

   Suchen Sie nach `xrdp.pid`. Wenn Sie die Datei finden, entfernen Sie sie, und versuchen Sie erneut, einen Neustart auszuführen.

### <a name="start-sap-mc"></a>Starten der SAP MC
Nachdem Sie den GNOME-Desktop installiert haben, starten Sie die grafische Java-basierte SAP MC über Firefox. Wenn sie auf einer Azure-VM unter SLES 12/SLES für SAP Applications 12 ausgeführt wird, wird möglicherweise ein Fehler angezeigt. Der Fehler tritt aufgrund des fehlenden Java-Browser-Plug-Ins auf.

Die URL zum Starten der SAP MC lautet `<server>:5<instance_number>13`.

Weitere Informationen finden Sie unter [Starting the Web-Based SAP Management Console](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm) (Starten der webbasierten SAP Management Console).

Der folgende Screenshot zeigt die Fehlermeldung, die angezeigt wird, wenn das Java-Browser-Plug-In nicht vorhanden ist:

![Fehlermeldung über fehlendes Java-Browser-Plug-In](./media/hana-get-started/image013.jpg)

Eine Möglichkeit zur Behebung des Problems besteht darin, das fehlende-Plug-In mithilfe von YaST zu installieren, wie im folgenden Screenshot veranschaulicht:

![Verwenden von YaST zum Installieren des fehlenden Plug-Ins](./media/hana-get-started/image014.jpg)

Wenn Sie die URL für die SAP Management Console erneut eingeben, werden Sie zum Aktivieren des Plug-Ins aufgefordert:

![Dialogfeld zum Anfordern der Plug-In-Aktivierung](./media/hana-get-started/image015.jpg)

Möglicherweise wird auch eine Fehlermeldung zu einer fehlenden Datei („javafx.properties“) ausgegeben. Dies hängt damit zusammen, dass Oracle Java 1.8 for SAP GUI 7.4 erforderlich ist. Weitere Informationen finden Sie im [SAP-Hinweis 2059429](https://launchpad.support.sap.com/#/notes/2059424).
Weder die IBM Java-Version noch das mit SLES/SLES für SAP Applications 12 gelieferte openjdk-Paket enthalten die benötigte Datei „javafx.properties“. Die Lösung besteht darin, Java SE 8 von Oracle herunterzuladen und zu installieren.

Informationen über ein ähnliches Problem mit „openjdk“ in openSUSE finden Sie im Diskussionsthread [SAPGui 7.4 Java for openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>Manuelle Installation von SAP HANA: SWPM
Die Reihe von Screenshots in diesem Abschnitt zeigt die wichtigsten Schritte zum Installieren von SAP NetWeaver 7.5 und SAP HANA SP12 bei der Verwendung von SWPM mit SAPinst. Als Teil einer NetWeaver 7.5-Installation kann SWPM auch die HANA-Datenbank als Einzelinstanz installieren.

In einer Beispieltestumgebung haben wir einen Advanced Business Application Programming-App-Server (ABAP) installiert. Wie im folgenden Screenshot gezeigt, wurde die **Distributed System**-Option verwendet, um ASCS- und primäre Anwendungsserverinstanzen in einer Azure-VM. SAP HANA wurde als Datenbanksystem auf einer anderen Azure-VM verwendet.

![ASCS- und primäre Anwendungsserverinstanzen, die mithilfe der Option „Distributed System“ installiert werden](./media/hana-get-started/image012.jpg)

Nachdem die ASCS-Instanz auf der App-Server-VM installiert wurde, ist sie in der SAP Management Console durch ein grünes Symbol gekennzeichnet. Das Verzeichnis „/sapmnt“, welches das SAP-Profilverzeichnis enthält, muss für die VM des SAP HANA DB-Servers freigegeben sein. Für den DB-Installationsschritt muss Zugriff auf diese Informationen bestehen. Die beste Möglichkeit, Zugriff zu gewähren, ist die Verwendung von NFS, das für die Nutzung von YaST konfiguriert werden kann.

![SAP Management Console mit der auf der App-Server-VM installierten ASCS-Instanz mit einem grünen Symbol](./media/hana-get-started/image016.jpg)

Auf der App-Server-VM wird das Verzeichnis „/sapmnt“ über NFS freigegeben, indem die Optionen **rw** und **no_root_squash** verwendet werden. Die Standardeinstellungen sind **ro** und **root_squash**. Dies kann beim Installieren der Datenbankinstanz zu Problemen führen.

![Freigabe des Verzeichnisses „/sapmnt“ über NFS mit den Optionen „rw“ und „no_root_squash“](./media/hana-get-started/image017b.jpg)

Wie der nächste Screenshot zeigt, muss die /sapmnt-Freigabe von der App-Server-VM über **NFS Client** und YaST auf der SAP HANA DB-Server-VM konfiguriert werden.

![Die mithilfe von „NFS Client“ konfigurierte /sapmnt-Freigabe](./media/hana-get-started/image018b.jpg)

Zum Ausführen einer verteilten NetWeaver 7.5-Installation, d. h. einer **Datenbankinstanz**, melden Sie sich bei der SAP HANA-DB-Server-VM an, und starten Sie SWPM:

![Installieren einer Datenbankinstanz durch Anmelden bei der SAP HANA-DB-Server-VM und Starten von SWPM](./media/hana-get-started/image019.jpg)

Geben Sie nach der Auswahl einer **typischen** Installation und des Pfads zu den Installationsmedien eine DB-SID, den Hostnamen, die Instanznummer und das DB-Systemadministratorkennwort ein:

![Die Anmeldeseite des SAP HANA-Datenbank-Systemadministrators](./media/hana-get-started/image035b.jpg)

Geben Sie das Kennwort für das DBACOCKPIT-Schema ein:

![Das Kennwort-Eingabefeld für das DBACOCKPIT-Schema](./media/hana-get-started/image036b.jpg)

Geben Sie eine Frage für das SAPABAP1-Schemakennwort ein:

![Eingeben einer Frage für das SAPABAP1-Schemakennwort](./media/hana-get-started/image037b.jpg)

Nachdem die einzelnen Aufgaben abgeschlossen sind, wird neben jeder Phase des DB-Installationsprozesses ein grünes Häkchen angezeigt. Die Meldung „Execution of... Database Instance has completed“ (Ausführung von ... Datenbankinstanz wurde abgeschlossen) wird angezeigt.

![Fenster mit Bestätigungsmeldung zum Abschluss der Aufgabe](./media/hana-get-started/image023.jpg)

Nach erfolgreicher Installation wird auch die DB-Instanz in der SAP Management Console mit einem grünen Symbol angezeigt. Die vollständige Liste der SAP HANA-Prozesse, z.B. „hdbindexserver“ und „hdbcompileserver“, wird angezeigt.

![SAP Management Console-Fenster mit Liste der SAP HANA-Prozesse](./media/hana-get-started/image024.jpg)

Der folgende Screenshot zeigt die Teile der Dateistruktur unter dem Verzeichnis „/hana/shared“, die von SWPM während der HANA-Installation erstellt wurden. Da es keine Option zum Angeben eines anderen Pfads gibt, ist es wichtig, vor der SAP HANA-Installation mit SWPM zusätzlichen Datenträger-Speicherplatz unter „/hana“ bereitzustellen. Mit diesem Schritt wird verhindert, dass dem Stammdateisystem der freie Speicherplatz ausgeht.

![Die während der HANA-Installation erstellte Dateistruktur des Verzeichnisses „/hana/shared“](./media/hana-get-started/image025.jpg)

Dieser Screenshot zeigt die Dateistruktur des Verzeichnisses „/usr/sap“:

![Die Dateistruktur des Verzeichnisses „/usr/sap“](./media/hana-get-started/image026.jpg)

Im letzten Schritt der verteilten ABAP-Installation wird die Instanz des primären Anwendungsservers installiert:

![ABAP-Installation mit der Instanz des primären Anwendungsservers als abschließendem Schritt](./media/hana-get-started/image027b.jpg)

Nach der Installation der primären Instanz des Anwendungsservers und der SAP GUI verwenden Sie die Transaktion **DBA Cockpit**, um zu bestätigen, dass die SAP HANA-Installation ordnungsgemäß fertiggestellt wurde:

![DBA Cockpit-Fenster mit Bestätigung der erfolgreichen Installation](./media/hana-get-started/image028b.jpg)

Als letzten Schritt sollten Sie zuerst HANA Studio auf der SAP-App-Server-VM installieren. Stellen Sie dann eine Verbindung mit der SAP HANA-Instanz her, die auf der DB-Server-VM ausgeführt wird.

![Installieren von SAP HANA Studio auf der SAP-App-Server-VM](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Manuelle Installation von SAP HANA: HDBLCM
Außer der Installation von SAP HANA als Teil einer verteilten Installation mit SWPM können Sie die HANA-Instanz zuerst mithilfe von HDBLCM eigenständig installieren. Anschließend können Sie beispielsweise SAP NetWeaver 7.5 installieren. Die Screenshots in diesem Abschnitt zeigen, wie dieser Prozess funktioniert.

Weitere Informationen zum HANA-HDBLCM-Tool finden Sie in folgenden Ressourcen:

* [Choose the correct SAP HANA HDBLCM for your task (Auswählen des richtigen SAP HANA-HDBLCM-Tools für Ihre Aufgabe)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [SAP HANA Lifecycle Management Tools (SAP HANA-Tools für die Lebenszyklusverwaltung)](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm)
* [SAP HANA Server Installation and Update Guide (SAP HANA-Serverinstallation und Aktualisierungshandbuch)](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

Vermeiden Sie Probleme mit einer Standardeinstellung der Gruppen-ID für das `\<HANA SID\>adm user`-Element, die vom HDBLCM-Tool erstellt wird. Bevor Sie SAP HANA über HDBLCM installieren, definieren Sie eine neue Gruppe namens `sapsys` mit der Gruppen-ID `1001`:

![Neue mit der Gruppen-ID 1001 definierte Gruppe „sapsys“](./media/hana-get-started/image030.jpg)

Wenn Sie HDBLCM zum ersten Mal starten, wird ein einfaches Startmenü angezeigt. Wählen Sie Element 1, **Neues System installieren** aus:

![Option „Install new system“ (Neues System installieren) im HDBLCM-Startfenster](./media/hana-get-started/image031.jpg)

Der folgende Screenshot zeigt die wichtigsten Optionen, die Sie zuvor ausgewählt haben.

> [!IMPORTANT]
> Verzeichnisse, die für HANA-Protokoll- und Datenvolumes benannt sind, sowie der Installationspfad (in diesem Beispiel „/hana/shared“) und „/usr/sap“ dürfen nicht Teil des Stammdateisystems sein. Diese Verzeichnisse gehören zu den Azure-Datenträgern, die der VM angefügt wurden. Weitere Informationen finden Sie im Abschnitt „Datenträgereinrichtung“. 

Durch diesen Ansatz wird verhindert, dass dem Stammdateisystem der freie Speicherplatz ausgeht. Beachten Sie, dass der HANA-Systemadministrator über Benutzer-ID `1005` verfügt und Mitglied der Gruppe `sapsys` mit der ID `1001` ist, die vor der Installation definiert wurde.

![Liste aller zuvor ausgewählten wesentlichen SAP HANA-Komponenten](./media/hana-get-started/image032.jpg)

Überprüfen Sie die Details von `\<HANA SID\>adm user` im Verzeichnis „/etc/passwd“. Suchen Sie nach `azdadm`, wie im folgenden Screenshot dargestellt:

![Im Verzeichnis „/etc/passwd“ aufgelistete HANA-Details für „\<HANA SID\>adm user“](./media/hana-get-started/image033.jpg)

Nach der Installation von SAP HANA mit HDBLCM sehen Sie die Dateistruktur in SAP HANA Studio, wie im folgenden Screenshot veranschaulicht. Das Schema SAPABAP1, in dem alle SAP NetWeaver-Tabellen enthalten sind, ist noch nicht verfügbar.

![SAP HANA-Dateistruktur in SAP HANA Studio](./media/hana-get-started/image034.jpg)

Nach der Installation von SAP HANA können Sie zusätzlich SAP NetWeaver installieren. Wie im folgenden Screenshot gezeigt, wurde die Installation mithilfe von SWPM als verteilte Installation vorgenommen. Dieser Prozess wird im vorherigen Abschnitt beschrieben. Bei der Installation der Datenbankinstanz mit SWPM geben Sie die gleichen Daten mithilfe von HDBLCM ein. Geben Sie z. B. Hostname, HANA-SID und Instanznummer ein. SWPM verwendet anschließend die vorhandene Installation von HANA und fügt weitere Schemas hinzu.

![Eine mithilfe von SWPM ausgeführte verteilte Installation](./media/hana-get-started/image035b.jpg)

Der folgende Screenshot zeigt den SWPM-Installationsschritt, in dem Sie Daten über das DBACOCKPIT-Schema eingeben:

![Der SWPM-Installationsschritt, wo DBACOCKPIT-Schemadaten eingegeben werden](./media/hana-get-started/image036b.jpg)

Geben Sie Daten zum SAPABAP1-Schema ein:

![Eingeben von Daten zum SAPABAP1-Schema](./media/hana-get-started/image037b.jpg)

Nach Abschluss der Installation der SWPM-Datenbankinstanz sehen Sie das SAPABAP1-Schema in SAP HANA Studio:

![Das SAPABAP1-Schema in SAP HANA Studio](./media/hana-get-started/image038b.jpg)

Nach Abschluss der Installation des SAP-App-Servers und der SAP GUI überprüfen Sie die HANA DB-Instanz mit der Transaktion **DBA Cockpit**:

![Die mit der Transaktion „DBA Cockpit“ überprüfte HANA DB-Instanz](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>SAP-Softwaredownloads
Sie können die Software vom SAP Service Marketplace herunterladen, wie in den folgenden Screenshots dargestellt.

NetWeaver 7.5 für Linux/HANA herunterladen:

 ![Installations- und Upgradefenster des SAP Service für das Herunterladen von NetWeaver 7.5](./media/hana-get-started/image001.jpg)

HANA SP12 Platform Edition herunterladen:

 ![Installations- und Upgradefenster des SAP Service für das Herunterladen von HANA SP12 Platform Edition](./media/hana-get-started/image002.jpg)

