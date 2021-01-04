---
title: Azure VMware Solution by CloudSimple – Optimieren der privaten CloudSimple-Cloud für Oracle RAC
description: Erfahren Sie, wie Sie einen neuen Cluster bereitstellen und einen virtuellen Computer für die Installation und Konfiguration von Oracle Real Application Clusters (RAC) optimieren.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 980ba86a9916e13dd2ac7639bd06d3ab8546d2f1
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424696"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>Optimieren der privaten CloudSimple-Cloud für die Installation von Oracle RAC

Sie können Oracle Real Application Clusters (RAC) in Ihrer privaten CloudSimple-Cloudumgebung bereitstellen. In dieser Anleitung wird beschrieben, wie Sie einen neuen Cluster bereitstellen und einen virtuellen Computer für die Oracle RAC-Lösung optimieren. Nachdem Sie die Schritte in diesem Thema ausgeführt haben, können Sie Oracle RAC installieren und konfigurieren.

## <a name="storage-policy"></a>Speicherrichtlinie

Die erfolgreiche Implementierung von Oracle RAC erfordert eine ausreichende Anzahl von Knoten im Cluster.  In der vSAN-Speicherrichtlinie wird eine Fehlertoleranz (Failures To Tolerate, FTT) auf Datenträger angewandt, die zum Speichern von Datenbank-, Protokoll- und Wiederholungsdatenträgern verwendet werden.  Die erforderliche Anzahl von Knoten zum effektiven Tolerieren von Fehlern ist 2N + 1, wobei N der FTT-Wert ist.

Beispiel: Wenn der gewünschte FTT-Wert „2“ ist, muss die Gesamtanzahl der Knoten im Cluster 2 × 2 + 1 = 5 betragen.

## <a name="overview-of-deployment"></a>Übersicht über die Bereitstellung

In den folgenden Abschnitten wird beschrieben, wie Sie Ihre private CloudSimple-Cloudumgebung für Oracle RAC einrichten.

1. Bewährte Methoden für die Datenträgerkonfiguration
2. Bereitstellen des vSphere-Clusters für die private CloudSimple-Cloud
3. Einrichten von Netzwerken für Oracle RAC
4. Einrichten von vSAN-Speicherrichtlinien
5. Erstellen von Oracle-VMs und freigegebenen VM-Datenträgern
6. Einrichten von VM-zu-Host-Affinitätsregeln

## <a name="best-practices-for-disk-configuration"></a>Bewährte Methoden für die Datenträgerkonfiguration

Virtuelle Oracle RAC-Computer verfügen über mehrere Datenträger, die jeweils für eine bestimmte Funktion verwendet werden.  Freigegebene Datenträger werden auf allen virtuellen Computern eingebunden, die vom Oracle RAC-Cluster verwendet werden.  Datenträger zur Betriebssystem- und Softwareinstallation werden nur auf den einzelnen virtuellen Computern eingebunden.  

![Übersicht über Datenträger virtueller Oracle RAC-Computer](media/oracle-vm-disks-overview.png)

Im folgenden Beispiel werden die in der folgenden Tabelle definierten Datenträger verwendet.

| Datenträger                                      | Zweck                                       | Freigegebener Datenträger |
|-------------------------------------------|-----------------------------------------------|-------------|
| OS                                        | Betriebssystem-Datenträger                         | Nein          |
| GRID                                      | Installationsort für Oracle Grid-Software     | Nein          |
| DATABASE                                  | Installationsort für Oracle-Datenbanksoftware | Nein          |
| ORAHOME                                   | Basisspeicherort für Binärdateien der Oracle-Datenbank    | Nein          |
| DATA1, DATA2, DATA3, DATA4                | Datenträger für die Speicherung der Oracle-Datenbankdateien   | Ja         |
| REDO1, REDO2, REDO3, REDO4, REDO5, REDO6  | Datenträger für Wiederholungsprotokolle                                | Ja         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | Abstimmungsdatenträger                                  | Ja         |
| FRA1, FRA2                                | Datenträger für den Bereich für die schnelle Wiederherstellung                      | Ja         |

![Datenträgerkonfiguration für virtuelle Oracle-Computer](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>Konfiguration des virtuellen Computers

* Jeder virtuelle Computer wird mit vier SCSI-Controllern konfiguriert.
* Der SCSI-Controllertyp ist auf „VMware paravirtual“ festgelegt.
* Es werden mehrere virtuelle Datenträger (VMDK-Dateien) erstellt.
* Datenträger werden auf verschiedenen SCSI-Controllern eingebunden.
* Der Freigabetyp für Mehrfachschreiben ist für freigegebene Clusterdatenträger festgelegt.
* Die vSAN-Speicherrichtlinie wird definiert, um Hochverfügbarkeit von Datenträgern zu gewährleisten.

### <a name="operating-system-and-software-disk-configuration"></a>Konfiguration von Betriebssystem- und Softwaredatenträgern

Jeder virtuelle Oracle-Computer wird mit mehreren Datenträgern für Hostbetriebssystem, Auslagerung, Softwareinstallation und andere Betriebssystemfunktionen konfiguriert.  Diese Datenträger werden nicht von mehreren virtuellen Computer gemeinsam genutzt.  

* Drei Datenträger werden für jeden virtuellen Computer als virtuelle Datenträger konfiguriert und auf virtuellen Oracle RAC-Computern eingebunden.
    * Betriebssystemdatenträger
    * Datenträger zum Speichern von Installationsdateien für Oracle Grid
    * Datenträger zum Speichern von Installationsdateien für die Oracle-Datenbank
* Datenträger können als **Für schlanke Speicherzuweisung geeignet** konfiguriert werden.
* Jeder Datenträger wird auf dem ersten SCSI-Controller (SCSI0) eingebunden.  
* Die Freigabe wird auf **Keine Freigabe** festgelegt.
* Die Redundanz wird im Speicher mithilfe von vSAN-Richtlinien definiert.  

![Diagramm: Physische Konfiguration des Oracle RAC-Betriebssystemdatenträgers](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>Konfiguration der Datenträger für Daten

Datenträger für Daten werden hauptsächlich zum Speichern von Datenbankdateien verwendet.  

* Vier Datenträger werden für jeden virtuellen Computer als virtuelle Datenträger konfiguriert und auf virtuellen Oracle RAC-Computern eingebunden.
* Jeder Datenträger wird auf einem anderen SCSI-Controller eingebunden.
* Jeder virtuelle Datenträger ist als **Thick Provision Eager Zeroed** (Komplette Bereitstellung mit Null-Reservierung) konfiguriert.  
* Die Freigabe ist auf **Multi-writer** (Mehrfachschreibvorgänge) festgelegt.  
* Die Datenträger müssen als ASM-Datenträgergruppe (automatische Speicherverwaltung) konfiguriert werden.  
* Die Redundanz wird im Speicher mithilfe von vSAN-Richtlinien definiert.  
* ASM-Redundanz ist auf **externe Redundanz** festgelegt.

![Konfiguration der Oracle RAC-Datenträgergruppe für Daten](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>Datenträgerkonfiguration für Wiederholungsprotokolle

In Wiederholungspotokolldateien wird eine Kopie der Änderungen an der Datenbank gespeichert.  Die Protokolldateien werden verwendet, wenn Daten nach Fehlern wiederhergestellt werden müssen.

* Die Datenträger für die Wiederholungsprotokolle müssen als mehrere Datenträgergruppen konfiguriert werden.  
* Sechs Datenträger werden auf allen virtuellen Oracle RAC-Computern erstellt und eingebunden.
* Datenträger werden auf verschiedenen SCSI-Controllern eingebunden.
* Jeder virtuelle Datenträger ist als **Thick Provision Eager Zeroed** (Komplette Bereitstellung mit Null-Reservierung) konfiguriert.
* Die Freigabe ist auf **Multi-writer** (Mehrfachschreibvorgänge) festgelegt.  
* Die Datenträger müssen als zwei ASM-Datenträgergruppen konfiguriert werden.
* Jede ASM-Datenträgergruppe enthält drei Datenträger auf unterschiedlichen SCSI-Controllern.  
* ASM-Redundanz ist auf **normale Redundanz** festgelegt.
* Fünf Wiederholungsprotokolldateien werden in beiden ASM-Gruppen für die Wiederholungsprotokolle erstellt.

```
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
```

![Konfiguration der Oracle RAC-Datenträgergruppe für Wiederholungsprotokolle](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Konfiguration der Oracle-Abstimmungsdatenträger

Abstimmungsdatenträger stellen die Funktion für Quorumdatenträger als zusätzlichen Kommunikationskanal bereit, um eine „Split-Brain“-Situation zu vermeiden.

* Fünf Datenträger werden auf allen virtuellen Oracle RAC-Computern erstellt und eingebunden.
* Datenträger werden auf einem SCSI-Controller eingebunden.
* Jeder virtuelle Datenträger ist als **Thick Provision Eager Zeroed** (Komplette Bereitstellung mit Null-Reservierung) konfiguriert.
* Die Freigabe ist auf **Multi-writer** (Mehrfachschreibvorgänge) festgelegt.  
* Die Datenträger müssen als eine ASM-Datenträgergruppe konfiguriert werden.  
* ASM-Redundanz ist auf **hohe Redundanz** festgelegt.

![Konfiguration der Oracle RAC-Gruppe mit Abstimmungsdatenträgern](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Konfiguration der Oracle-Datenträger für den Bereich für schnelle Wiederherstellung (optional)

Der Bereich für schnelle Wiederherstellung (Fast Recovery Area, FRA) ist ein Dateisystem, das von der Oracle ASM-Datenträgergruppe verwaltet wird.  Der Bereich für schnelle Wiederherstellung stellt einen freigegebenen Speicherort für Sicherungs- und Wiederherstellungsdateien bereit. Oracle erstellt archivierte Protokolle und Zurückverfolgungsprotokolle im Bereich für schnelle Wiederherstellung. Oracle Recovery Manager (RMAN) kann seine Sicherungssätze und Imagekopien optional im Bereich für schnelle Wiederherstellung speichern. Außerdem nutzt er diesen beim Wiederherstellen von Dateien während der Medienwiederherstellung.

* Zwei Datenträger werden auf allen virtuellen Oracle RAC-Computern erstellt und eingebunden.
* Datenträger werden auf verschiedenen SCSI-Controllern eingebunden.
* Jeder virtuelle Datenträger ist als **Thick Provision Eager Zeroed** (Komplette Bereitstellung mit Null-Reservierung) konfiguriert.
* Die Freigabe ist auf **Multi-writer** (Mehrfachschreibvorgänge) festgelegt.  
* Die Datenträger müssen als eine ASM-Datenträgergruppe konfiguriert werden.  
* ASM-Redundanz ist auf **externe Redundanz** festgelegt.

![Diagramm: Konfiguration der Oracle RAC-Gruppe mit Abstimmungsdatenträgern](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>Bereitstellen des vSphere-Clusters für die private CloudSimple-Cloud

Gehen Sie folgendermaßen vor, um einen vSphere-Cluster in der privaten Cloud bereitzustellen:

1. Erstellen Sie im CloudSimple-Portal eine [private Cloud](create-private-cloud.md). CloudSimple erstellt in der neu erstellten privaten Cloud einen vCenter-Standardbenutzer mit dem Namen „cloudowner“. Ausführliche Informationen zum Standardbenutzer und Berechtigungsmodell für die private Cloud finden Sie unter [Kennenlernen des Berechtigungsmodells für private Clouds](learn-private-cloud-permissions.md).  In diesem Schritt wird der primäre Verwaltungscluster für die private Cloud erstellt.

2. Erweitern Sie die [private Cloud](expand-private-cloud.md) im CloudSimple-Portal mit einem neuen Cluster.  Dieser Cluster wird für die Bereitstellung von Oracle RAC verwendet.  Wählen Sie die Anzahl der Knoten basierend auf der gewünschten Fehlertoleranz aus (mindestens drei Knoten).

## <a name="set-up-networking-for-oracle-rac"></a>Einrichten von Netzwerken für Oracle RAC

1. Erstellen Sie in Ihrer privaten Cloud [zwei VLANs](create-vlan-subnet.md) – eines für das öffentliche Oracle-Netzwerk und eines für das private Oracle-Netzwerk –, und weisen Sie ihnen entsprechende Subnetz-CIDR-Werte zu.
2. Nachdem die VLANs erstellt wurden, erstellen Sie die [verteilten Portgruppen im vCenter der privaten Cloud](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere).
3. Richten Sie einen [virtuellen Computer für DHCP- und DNS-Server](dns-dhcp-setup.md) in Ihrem Verwaltungscluster für die Oracle-Umgebung ein.
4. [Konfigurieren Sie die DNS-Weiterleitung](on-premises-dns-setup.md#create-a-conditional-forwarder) auf dem in der privaten Cloud installierten DNS-Server.

## <a name="set-up-vsan-storage-policies"></a>Einrichten von vSAN-Speicherrichtlinien

vSAN-Richtlinien definieren Fehlertoleranz und Datenträgerstriping für die auf den VM-Datenträgern gespeicherten Daten.  Die erstellte Speicherrichtlinie muss beim Erstellen des virtuellen Computers auf die VM-Datenträger angewandt werden.

1. [Melden Sie sich beim vSphere-Client](./vcenter-access.md) Ihrer privaten Cloud an.
2. Wählen Sie im Menü oben die Option **Policies and Profiles** (Richtlinien und Profile) aus.
3. Wählen Sie im Menü auf der linken Seite **VM Storage Policies** (VM-Speicherrichtlinien) und dann **Create a VM storage Policy** (VM-Speicherrichtlinie erstellen) aus.
4. Geben Sie einen aussagekräftigen Namen für die Richtlinie ein, und klicken Sie auf **NEXT** (Weiter).
5. Wählen Sie im Abschnitt **Policy structure** (Richtlinienstruktur) die Option **Enable rules for vSAN storage** (Regeln für vSAN-Speicher aktivieren) aus, und klicken Sie auf **NEXT** (Weiter).
6. Wählen Sie im Abschnitt **vSAN** > **Availability** (Verfügbarkeit) als Notfalltoleranz für die Site **None** (Keine) aus. Wählen Sie als Fehlertoleranz die Option **RAID – Mirroring** (RAID – Spiegelung) für die gewünschte FTT aus.
    ![vSAN-Einstellungen](media/oracle-rac-storage-wizard-vsan.png)
7. Wählen Sie im Abschnitt **Advanced** (Erweitert) die Anzahl der Datenträgerstripes pro Objekt aus. Wählen Sie als Objektbereichsreservierung die Option **Thick Provisioned** (Komplette Bereitstellung) aus. Wählen Sie **Disable object checksum** (Objektprüfsumme deaktivieren) aus. Klicken Sie auf **NEXT** (Weiter).
8. Befolgen Sie die Anweisungen auf dem Bildschirm, um die Liste der kompatiblen vSAN-Datenspeicher anzuzeigen, überprüfen Sie die Einstellungen, und schließen Sie die Einrichtung ab.

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>Erstellen von Oracle-VMs und freigegebenen VM-Datenträgern für Oracle

Zum Erstellen eines virtuellen Computers für Oracle klonen Sie eine vorhandene VM oder erstellen eine neue VM.  In diesem Abschnitt wird beschrieben, wie Sie einen neuen virtuellen Computer erstellen und anschließend klonen, um nach der Installation des Basisbetriebssystems einen zweiten virtuellen Computer zu erstellen.  Nachdem die virtuellen Computer erstellt wurden, können Sie ihnen Datenträger hinzufügen.  Oracle-Cluster verwenden freigegebene Datenträger zum Speichern von Daten, Protokollen und Wiederholungsprotokollen.

### <a name="create-vms"></a>Virtuelle Computer erstellen

1. Klicken Sie in vCenter auf das Symbol **Hosts und Cluster**. Wählen Sie den Cluster aus, den Sie für Oracle erstellt haben.
2. Klicken Sie mit der rechten Maustaste auf den Cluster, und wählen Sie **Neuer virtueller Computer** aus.
3. Wählen Sie **Neuen virtuellen Computer erstellen** aus, und klicken Sie auf **Weiter**.
4. Benennen Sie den Computer, wählen Sie den Speicherort der Oracle-VM aus, und klicken Sie auf **Weiter**.
5. Wählen Sie die Clusterressource aus, und klicken Sie auf **Weiter**.
6. Wählen Sie den vSAN-Datenspeicher für den Cluster aus, und klicken Sie auf **Weiter**.
7. Behalten Sie die Standardauswahl für ESXi 6.5-Kompatibilität bei, und klicken Sie auf **Weiter**.
8. Wählen Sie das Gastbetriebssystem der ISO-Datei für den virtuellen Computer aus, den Sie erstellen, und klicken Sie auf **Weiter**.
9. Wählen Sie die Festplattengröße aus, die für die Installation des Betriebssystems erforderlich ist.
10. Wenn Sie die Anwendung auf einem anderen Gerät installieren möchten, klicken Sie auf **Neues Gerät hinzufügen**.
11. Wählen Sie Netzwerkoptionen aus, und weisen Sie die für das öffentliche Netzwerk erstellte verteilte Portgruppe zu.
12. Wenn Sie zusätzliche Netzwerkschnittstellen hinzufügen möchten, klicken Sie auf **Neues Gerät hinzufügen** , und wählen Sie die für das private Netzwerk erstellte verteilte Portgruppe aus.
13. Wählen Sie für das neue CD/DVD-Laufwerk die Datenspeicher-ISO-Datei aus, die die ISO-Datei für die Installation des bevorzugten Betriebssystems enthält. Wählen Sie die Datei aus, die Sie zuvor in den Ordner „ISO-Dateien und Vorlagen“ hochgeladen haben, und klicken Sie auf **OK**.
14. Überprüfen Sie die Einstellungen, und klicken Sie auf **OK** , um den neuen virtuellen Computer zu erstellen.
15. Schalten Sie die VM ein. Installieren des Betriebssystems und aller erforderlichen Updates

Nachdem das Betriebssystem installiert wurde, können Sie einen zweiten virtuellen Computer klonen. Klicken Sie mit der rechten Maustaste auf den Eintrag der VM, und wählen Sie die Option zum Klonen aus.

### <a name="create-shared-disks-for-vms"></a>Erstellen freigegebener Datenträger für virtuelle Computer

Oracle verwendet freigegebene Datenträger zum Speichern der Daten-, Protokoll- und Wiederholungsprotokolldateien.  Sie können einen freigegebenen Datenträger in vCenter erstellen und auf beiden VMs einbinden.  Um die Leistung zu erhöhen, platzieren Sie die Datenträger auf unterschiedlichen SCSI-Controllern. Die folgenden Schritte zeigen, wie Sie einen freigegebenen Datenträger in vCenter erstellen und dann an einen virtuellen Computer anfügen. Der vCenter Flash-Client wird zum Ändern der VM-Eigenschaften verwendet.

#### <a name="create-disks-on-the-first-vm"></a>Erstellen von Datenträgern auf der ersten VM

1. Klicken Sie in vCenter mit der rechten Maustaste auf eine der Oracle-VMs, und wählen Sie **Edit settings** (Einstellungen bearbeiten) aus.
2. Wählen Sie im Abschnitt des neuen Geräts **SCSI controller** aus, und klicken Sie auf **Add** (Hinzufügen).
3. Wählen Sie im Abschnitt des neuen Geräts **New Hard disk** (Neue Festplatte) aus, und klicken Sie auf **Add** (Hinzufügen).
4. Erweitern Sie die Eigenschaften der neuen Festplatte.
5. Geben Sie die Größe der Festplatte an.
6. Geben Sie als VM-Speicherrichtlinie die vSAN-Speicherrichtlinie an, die Sie zuvor definiert haben.
7. Wählen Sie als Speicherort einen Ordner im vSAN-Datenspeicher aus. Der Speicherort hilft beim Durchsuchen und Anfügen der Datenträger an einen zweiten virtuellen Computer.
8. Wählen Sie für die Datenträgerbereitstellung die Option **Thick provision eager zeroed** (Komplette Bereitstellung mit Null-Reservierung) aus.
9. Geben Sie für die Freigabe **Multi-writer** (Mehrfachschreibvorgänge) an.
10. Wählen Sie für den Knoten virtueller Gerät den neuen SCSI-Controller aus, der in Schritt 2 erstellt wurde.

    ![Screenshot, auf dem die erforderlichen Felder zum Erstellen von Datenträgern auf dem ersten virtuellen Computer hervorgehoben sind](media/oracle-rac-new-hard-disk.png)

Wiederholen Sie die Schritte 2 bis 10 für alle neuen Datenträger, die für die Oracle-Dateien für Daten, Protokolle und Wiederholungsprotokolle erforderlich sind.

#### <a name="attach-disks-to-second-vm"></a>Anfügen von Datenträgern an den zweiten virtuellen Computer

1. Klicken Sie in vCenter mit der rechten Maustaste auf eine der Oracle-VMs, und wählen Sie **Edit settings** (Einstellungen bearbeiten) aus.
2. Wählen Sie im Abschnitt des neuen Geräts **SCSI controller** aus, und klicken Sie auf **Add** (Hinzufügen).
3. Wählen Sie im Abschnitt des neuen Geräts **Existing Hard disk** (Vorhandene Festplatte) aus, und klicken Sie auf **Add** (Hinzufügen).
4. Navigieren Sie zu dem Speicherort, an dem der Datenträger für die erste VM erstellt wurde, und wählen Sie die VMDK-Datei aus.
5. Geben Sie als VM-Speicherrichtlinie die vSAN-Speicherrichtlinie an, die Sie zuvor definiert haben.
6. Wählen Sie für die Datenträgerbereitstellung die Option **Thick provision eager zeroed** (Komplette Bereitstellung mit Null-Reservierung) aus.
7. Geben Sie für die Freigabe **Multi-writer** (Mehrfachschreibvorgänge) an.
8. Wählen Sie für den Knoten virtueller Gerät den neuen SCSI-Controller aus, der in Schritt 2 erstellt wurde.

    ![Erstellen von Datenträgern auf der ersten VM](media/oracle-rac-existing-hard-disk.png)

Wiederholen Sie die Schritte 2–7 für alle neuen Datenträger, die für die Oracle-Dateien für Daten, Protokolle und Wiederholungsprotokolle erforderlich sind.

## <a name="set-up-vm-host-affinity-rules"></a>Einrichten von VM-zu-Host-Affinitätsregeln

Durch VM-zu-Host-Affinitätsregeln wird sichergestellt, dass die VM auf dem gewünschten Host ausgeführt wird.  Sie können Regeln in vCenter definieren, um sicherzustellen, dass der virtuelle Oracle-Computer auf dem Host mit ausreichenden Ressourcen ausgeführt wird und alle Lizenzierungsanforderungen erfüllt.

1. Im CloudSimple-Portal können Sie die Berechtigungen des Benutzers „cloudowner“ [ausweiten](escalate-private-cloud-privileges.md).
2. Melden Sie sich beim vSphere-Client Ihrer privaten Cloud an.
3. Wählen Sie im vSphere-Client den Cluster aus, auf dem Oracle-VMs bereitgestellt werden, und klicken Sie auf **Configure** (Konfigurieren).
4. Wählen Sie unter „Configure“ (Konfigurieren) die Option **VM/Host Groups** (VM-/Hostgruppen) aus.
5. Klicken Sie unten auf der Seite auf **+** .
6. Fügen Sie eine VM-Gruppe hinzu. Wählen Sie als Typ **VM group** (VM-Gruppe) aus. Geben Sie den Namen der Gruppe ein. Wählen Sie die VMs aus, und klicken Sie auf **OK** , um die Gruppe zu erstellen.
6. Fügen Sie eine Hostgruppe hinzu. Wählen Sie als Typ **Host Group** (Hostgruppe) aus. Geben Sie den Namen der Gruppe ein. Wählen Sie die Hosts aus, auf denen die VMs ausgeführt werden, und klicken Sie auf **OK** , um die Gruppe zu erstellen.
7. Um eine Regel zu erstellen, klicken Sie auf **VM/Host rules** (VM-/Hostregeln).
8. Klicken Sie unten auf der Seite auf **+** .
9. Geben Sie einen Namen für die Regel ein, und wählen Sie **Enable** (Aktivieren) aus.
10. Wählen Sie als Regeltyp **Virtual Machines to Host** (VMs zu Host) aus.
11. Wählen Sie die VM-Gruppe aus, die die Oracle-VMs enthält.
12. Wählen Sie **Must run on hosts in this group** (Muss auf Hosts in dieser Gruppe ausgeführt werden) aus.
13. Wählen Sie die Hostgruppe aus, die Sie erstellt haben.
14. Klicken Sie auf **OK** , um die Regel zu erstellen.

## <a name="references"></a>References

* [Informationen zu vSAN-Richtlinien](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [VMware-Multi-Writer-Attribut für freigegebene VMDKs](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
