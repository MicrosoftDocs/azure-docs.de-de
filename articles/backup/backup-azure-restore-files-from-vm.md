---
title: 'Azure Backup: Wiederherstellen von Dateien und Ordnern aus einer Azure-VM-Sicherung'
description: Informationen zum Wiederherstellen von Dateien aus einem Wiederherstellungspunkt für virtuelle Azure-Computer
services: backup
author: pvrk
manager: shivamg
keywords: Wiederherstellung auf Elementebene; Wiederherstellung von Dateien aus Azure-VM-Sicherung; Wiederherstellen von Dateien aus Azure-VM
ms.service: backup
ms.topic: conceptual
ms.date: 3/01/2019
ms.author: pullabhk
ms.openlocfilehash: 22ada6f9bb614bdc3698c58c6aa8ec3dd5def868
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60240034"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Wiederherstellen von Dateien aus einer Sicherung von virtuellen Azure-Computern

Azure Backup bietet die Möglichkeit zum Wiederherstellen von [virtuellen Azure-Computern und Datenträgern](./backup-azure-arm-restore-vms.md) aus Azure-VM-Sicherungen, die auch als „Wiederherstellungspunkte“ bezeichnet werden. In diesem Artikel wird die Wiederherstellung von Dateien und Ordnern aus einer Azure-VM-Sicherung erläutert. Das Wiederherstellen von Dateien und Ordnern ist nur für virtuelle Azure-Computer möglich, die mit dem Ressourcen-Manager-Modell bereitgestellt wurden und durch einen Recovery Services-Tresor geschützt werden.

> [!Note]
> Dies ist für Azure-VMs möglich, die mit dem Resource Manager-Modell bereitgestellt wurden und durch einen Recovery Services-Tresor geschützt werden.
> Die Dateiwiederherstellung aus einer verschlüsselten VM-Sicherung wird nicht unterstützt.
>

## <a name="mount-the-volume-and-copy-files"></a>Bereitstellen des Volumes und Kopien von Dateien

Zum Wiederherstellen von Dateien oder Ordnern aus dem Wiederherstellungspunkt wechseln Sie zum virtuellen Computer und wählen den gewünschten Wiederherstellungspunkt aus.

1. Melden Sie sich am [Azure-Portal](https://portal.Azure.com) an, und klicken Sie im linken Bereich auf **Virtuelle Computer**. Wählen Sie in der Liste der virtuellen Computer den gewünschten virtuellen Computer aus, um sein Dashboard zu öffnen.

2. Klicken Sie im Menü des virtuellen Computers auf **Sicherung**, um das Sicherungsdashboard zu öffnen.

    ![Öffnen des Sicherungselements im Recovery Services-Tresors](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. Klicken Sie im Menü des Sicherungsdashboards auf **Dateiwiederherstellung**.

    ![Schaltfläche „Dateiwiederherstellung“](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    Das Menü **Dateiwiederherstellung** wird geöffnet.

    ![Menü „Dateiwiederherstellung“](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. Wählen Sie im Dropdownmenü **Wiederherstellungspunkt auswählen** den Wiederherstellungspunkt mit den gewünschten Dateien aus. Standardmäßig ist der letzte Wiederherstellungspunkt bereits ausgewählt.

5. Klicken Sie auf **Ausführbare Datei herunterladen** (bei einem virtuellen Microsoft Azure-Computer) oder **Skript herunterladen** (bei einem virtuellen Linux-Azure-Computer), um die Software zum Kopieren von Dateien aus dem Wiederherstellungspunkt herunterzuladen.

    ![Generiertes Kennwort](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure lädt die ausführbare Datei bzw. das Skript auf den lokalen Computer herunter.

    ![Downloadmeldung für die ausführbare Datei oder das Skript](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Für die Ausführung der ausführbaren Datei oder des Skripts als Administrator wird empfohlen, die heruntergeladene Datei auf Ihrem Computer zu speichern.

6. Die ausführbare Datei bzw. das Skript ist kennwortgeschützt und erfordert ein Kennwort. Klicken Sie im Menü **Dateiwiederherstellung** auf die Schaltfläche „Kopieren“, um das Kennwort in der Zwischenablage zu speichern.

    ![Generiertes Kennwort](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. Klicken Sie im Downloadverzeichnis (in der Regel der Ordner „Downloads“) mit der rechten Maustaste auf die ausführbare Datei bzw. das Skript, und führen Sie sie bzw. es mit Administratoranmeldeinformationen aus. Wenn Sie dazu aufgefordert werden, geben Sie das Kennwort ein oder fügen es aus der Zwischenablage ein, und drücken Sie die EINGABETASTE. Nach der Eingabe eines gültigen Kennworts stellt das Skript eine Verbindung mit dem Wiederherstellungspunkt her.

    ![Menü „Dateiwiederherstellung“](./media/backup-azure-restore-files-from-vm/executable-output.png)

    Wenn Sie das Skript auf einem Computer mit eingeschränktem Zugriff ausführen, stellen Sie sicher, dass Zugriff auf Folgendes besteht:

    - download.microsoft.com
    - Recovery Service-URLs (Geoname bezieht sich auf die Region, in der sich der Recovery Services-Tresor befindet)
        - https:\//pod01-rec2.geo-name.backup.windowsazure.com (für öffentliche Azure-Regionen)
        - https:\//pod01-rec2.geo-name.backup.windowsazure.cn (für Azure China)
        - https:\//pod01-rec2.geo-name.backup.windowsazure.us (für Azure US Government)
        - https:\//pod01-rec2.geo-name.backup.windowsazure.de (für Azure Deutschland)
    - Ausgehender Port 3260

> [!Note]
> 
> * Für den Namen der heruntergeladenen Skriptdatei wird **geo-name** in die URL eingefügt. Beispiel: Der Name des heruntergeladenen Skripts beginnt mit \'VMname\'\_\'geoname\'_\'GUID\', z. B. ContosoVM_wcus_12345678...<br><br>
> * Die URL wäre: https:\//pod01-rec2.wcus.backup.windowsazure.com


   Für Linux benötigt das Skript zum Herstellen der Verbindung mit dem Wiederherstellungspunkt die Komponenten „open-iscsi“ und „lshw“. Wenn die Komponenten auf dem Computer, auf dem das Skript ausgeführt wird, nicht vorhanden sind, wird um die Erlaubnis zum Installieren der Komponenten gebeten. Geben Sie die Zustimmung zur Installation der erforderlichen Komponenten.

   Der Zugriff auf download.microsoft.com ist erforderlich, um die Komponenten für das Herstellen eines sicheren Kanals zwischen dem Computer, auf dem das Skript ausgeführt wird, und den Daten am Wiederherstellungspunkt herunterzuladen.

   Sie können das Skript auf allen Computern ausführen, die dasselbe (oder ein kompatibles) Betriebssystem wie die gesicherte VM haben. Siehe hierzu die Tabelle [Kompatible Betriebssysteme](backup-azure-restore-files-from-vm.md#system-requirements) mit den kompatiblen Betriebssystemen. Wenn der geschützte virtuelle Azure-Computer das Feature „Windows-Speicherplätze“ (für virtuelle Windows-Computer) oder LVM/RAID-Arrays (für virtuelle Linux-Computer) verwendet, können Sie die ausführbare Datei bzw. das Skript nicht auf diesem virtuellen Computer ausführen. Führen Sie die ausführbare Datei bzw. das Skript stattdessen auf einem beliebigen anderen Computer mit einem kompatiblen Betriebssystem aus.

### <a name="identifying-volumes"></a>Bestimmen von Volumes

#### <a name="for-windows"></a>Für Windows

Wenn Sie die ausführbare Datei ausführen, stellt das Betriebssystem neue Volumes bereit, denen Laufwerkbuchstaben zugewiesen werden. Über den Windows-Explorer oder Datei-Explorer können Sie zu diesen Laufwerken navigieren. Die den Volumes zugewiesenen Laufwerkbuchstaben entsprechen ggf. nicht den Buchstaben des ursprünglichen virtuellen Computers, doch der Volumename wird beibehalten. Beispiel: Wenn das Volume auf dem ursprünglichen virtuellen Computer „Data Disk (E:`\`“ hieß, kann dieses Volume auf dem lokalen Computer als „Data Disk (‚Beliebiger Buchstabe‘:`\`“ angefügt werden. Durchsuchen Sie alle in der Skriptausgabe erwähnten Volumes, bis Sie Ihre Dateien/Ordner gefunden haben.  

   ![Menü „Dateiwiederherstellung“](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>Für Linux

Unter Linux werden die Volumes des Wiederherstellungspunkts im Ordner bereitgestellt, in dem das Skript ausgeführt wird. Die angefügten Datenträger, Volumes und entsprechenden Bereitstellungspfade werden entsprechend angezeigt. Diese Bereitstellungspfade werden Benutzern mit der Berechtigung „root“ angezeigt. Durchsuchen Sie die Volumes in der Ausgabe des Skripts.

  ![Menü „Linux-Dateiwiederherstellung“](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>Schließen der Verbindung

Nachdem die Dateien bestimmt und an den lokalen Speicherort kopiert wurden, können Sie die zusätzlichen Laufwerke entfernen (oder ihre Bereitstellung aufheben). Zum Aufheben der Bereitstellung der Laufwerke klicken Sie im Azure-Portal im Menü **Dateiwiederherstellung** auf **Bereitstellung der Datenträger aufheben**.

![Aufheben der Bereitstellung der Datenträger](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Nachdem die Bereitstellung der Datenträger aufgehoben wurde, erhalten Sie eine Meldung. Die Aktualisierung der Verbindung kann einige Minuten dauern, sodass Sie die Datenträger entfernen können.

Nachdem unter Linux die Verbindung mit dem Wiederherstellungspunkt getrennt wurde, entfernt das Betriebssystem die entsprechenden Bereitstellungspfade nicht automatisch. Diese sind als „verwaiste“ Volumes vorhanden und sichtbar, lösen aber einen Fehler aus, wenn ein Zugriff bzw. Schreibzugriff auf die Dateien erfolgt. Sie können manuell entfernt werden. Das Skript ermittelt bei seiner Ausführung solche Volumes von vorherigen Wiederherstellungspunkten und bereinigt diese nach Zustimmung.

## <a name="special-configurations"></a>Besondere Konfigurationen

### <a name="dynamic-disks"></a>Dynamische Datenträger

Wenn auf dem geschützten virtuellen Azure-Computer Volumes mit einem oder beiden der folgenden Merkmale vorhanden sind, können Sie das ausführbare Skript nicht auf demselben virtuellen Computer ausführen.

- Volumes, die mehrere Datenträger umfassen (übergreifende und Stripesetvolumes)
- Fehlertolerante Volumes (gespiegelte und RAID-5-Volumes) auf dynamischen Datenträgern

Führen Sie das ausführbare Skript stattdessen auf einem beliebigen anderen Computer mit einem kompatiblen Betriebssystem aus.

### <a name="windows-storage-spaces"></a>Windows-Speicherplätze

„Windows-Speicherplätze“ ist eine Windows-Technologie, die Ihnen das Virtualisieren von Speicher ermöglicht. Mit „Windows-Speicherplätze“ können Sie branchenübliche Datenträger in Speicherpools gruppieren. Anschließend erstellen Sie mithilfe des in diesen Speicherpools verfügbaren Speicherplatzes virtuelle Datenträger, die „Speicherplätze“ genannt werden.

Wenn der geschützte virtuelle Azure-Computer „Windows-Speicherplätze“ verwendet, können Sie das ausführbare Skript nicht auf diesem virtuellen Computer ausführen. Führen Sie das ausführbare Skript stattdessen auf einem beliebigen anderen Computer mit einem kompatiblen Betriebssystem aus.

### <a name="lvmraid-arrays"></a>LVM-/RAID-Arrays

Unter Linux werden LVM- (Logical Volume Manager) bzw. softwaregestützte RAID-Arrays zum Verwalten logischer Volumes verwendet, die sich über mehrere Datenträger erstrecken. Wenn der geschützte virtuelle Linux-Computer mit LVM- bzw. RAID-Arrays arbeitet, kann das Skript nicht auf diesem virtuellen Computer ausgeführt werden. Führen Sie das Skript stattdessen auf einem anderen Computer mit kompatiblem Betriebssystem und Unterstützung des Dateisystems des geschützten Computers aus.

Die folgende Ausgabe des Skripts zeigt die Datenträger und Volumes des LVM- bzw. RAID-Arrays samt Partitionstyp.

   ![Menü mit der LVM-Ausgabe unter Linux](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Führen Sie die Befehle in den folgenden Abschnitten aus, um diese Partitionen online zu schalten.

#### <a name="for-lvm-partitions"></a>Für LVM-Partitionen

Hiermit werden die Volumegruppennamen unter einem physischen Volume aufgeführt.

```bash
#!/bin/bash
$ pvs <volume name as shown above in the script output>
```

Hiermit werden alle logischen Volumes mit Name und Pfad in einer Volumegruppe aufgeführt.

```bash
#!/bin/bash
$ lvdisplay <volume-group-name from the pvs command’s results>
```

Hiermit werden die logischen Volumes im Pfad Ihrer Wahl bereitgestellt.

```bash
#!/bin/bash
$ mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>Für RAID-Arrays

Mit dem folgenden Befehl werden Details zu allen RAID-Datenträgern angezeigt.

```bash
#!/bin/bash
$ mdadm –detail –scan
```

 Der entsprechende RAID-Datenträger wird als `/dev/mdm/<RAID array name in the protected VM>` angezeigt.

Verwenden Sie den Befehl „mount“, wenn der RAID-Datenträger physische Volumes hat.

```bash
#!/bin/bash
$ mount [RAID Disk Path] [/mountpath]
```

Wenn auf dem RAID-Laufwerk eine andere LVM konfiguriert ist, führen Sie das vorhergehende Verfahren für LVM-Partitionen durch, verwenden aber der Volumenamen anstelle des Namens des RAID-Datenträgers.

## <a name="system-requirements"></a>Systemanforderungen

### <a name="for-windows-os"></a>Für Windows-Betriebssystem

Die folgende Tabelle zeigt die Kompatibilität zwischen Server- und Clientbetriebssystemen. Dateien können nicht in einem vorhergehenden oder künftigen Betriebssystem wiederhergestellt werden. Beispielsweise kann eine Datei von einem virtuellen Windows Server 2016-Computer nicht auf einem Windows Server 2012- oder Windows 8-Computer wiederhergestellt werden. Sie können Dateien von einem virtuellen Computer im gleichen Serverbetriebssystem oder im kompatiblen Clientbetriebssystem wiederherstellen.

|Serverbetriebssystem | Kompatibles Clientbetriebssystem  |
| --------------- | ---- |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>Für Linux-Betriebssystem

Unter Linux muss das Betriebssystem des Computers zum Wiederherstellen von Dateien das Dateisystem des geschützten virtuellen Computers unterstützen. Achten Sie bei der Auswahl eines Computers zum Ausführen des Skripts darauf, dass der Computer über ein kompatibles Betriebssystem verfügt und eine der in folgenden Tabelle angegebenen Versionen verwendet:

|Linux-Betriebssystem | Versionen  |
| --------------- | ---- |
| Ubuntu | ab 12.04 |
| CentOS | ab 6.5  |
| RHEL | ab 6.7 |
| Debian | ab 7 |
| Oracle Linux | ab 6.4 |
| SLES | ab 12 |
| openSUSE | ab 42.2 |

> [!Note]
> Es wurden einige Probleme mit der Ausführung des Dateiwiederherstellungsskripts auf Computern mit dem Betriebssystem „SLES 12 SP4“ erkannt. Dies wird derzeit in Zusammenarbeit mit dem SLES-Team untersucht.
> Die Ausführung des Dateiwiederherstellungsskripts funktioniert derzeit auf Computern mit den Betriebssystemen SLES 12 SP2 und SP3.
>

Das Skript erfordert auch, dass Python- und Bash-Komponenten ausgeführt werden und eine sichere Verbindung mit dem Wiederherstellungspunkt herstellen.

|Komponente | Version  |
| --------------- | ---- |
| Bash | ab 4 |
| Python | ab 2.6.6  |
| TLS | 1.2 muss unterstützt werden.  |

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie Probleme beim Wiederherstellen von Dateien von den virtuellen Computern haben, überprüfen Sie die folgende Tabelle mit zusätzlichen Informationen.

| Fehlermeldung/Szenario | Mögliche Ursache | Empfohlene Maßnahme |
| ------------------------ | -------------- | ------------------ |
| EXE-Ausgabe: *Ausnahme beim Herstellen einer Verbindung mit dem Ziel* |Skript kann nicht auf den Wiederherstellungspunkt zugreifen.    | Prüfen Sie, ob der Computer die zuvor genannten Zugriffsanforderungen erfüllt. |  
| EXE-Ausgabe: *Das Ziel wurde bereits in einer iSCSI-Sitzung angemeldet.* | Das Skript wurde bereits auf dem gleichen Computer ausgeführt, und die Laufwerke wurden angefügt. | Die Volumes des Wiederherstellungspunkts wurden bereits angefügt. Sie wurden ggf. NICHT mit denselben Laufwerkbuchstaben des ursprünglichen virtuellen Computers bereitgestellt. Suchen Sie im Datei-Explorer auf allen verfügbaren Volumes nach Ihrer Datei. |
| EXE-Ausgabe: *Dieses Skript ist ungültig, da die Bereitstellung der Datenträger über das Portal aufgehoben bzw. das 12-Stunden-Limit überschritten wurde. Laden Sie ein neues Skript aus dem Portal herunter.* |    Die Bereitstellung der Datenträger über das Portal wurde aufgehoben oder das 12-Stunden-Limit überschritten. | Diese bestimmte EXE-Datei ist jetzt ungültig und kann nicht ausgeführt werden. Wenn Sie auf die Dateien dieser zeitpunktbezogenen Wiederherstellung zugreifen möchten, fordern Sie im Portal eine neue EXE-Datei an.|
| Auf dem Computer, auf dem die EXE-Datei ausgeführt wird: Die Bereitstellung der neuen Volumes wird erst aufgehoben, nachdem auf die Schaltfläche „Bereitstellung aufheben“ geklickt wurde. | Der iSCSI-Initiator auf dem Computer reagiert nicht bzw. aktualisiert seine Verbindung mit dem Ziel nicht und verwaltet auch den Cache nicht. |  Warten Sie nach dem Klicken auf **Aufheben der Bereitstellung** einige Minuten. Wenn die Bereitstellung der neuen Volumes nicht aufgehoben wurde, blättern Sie durch alle Volumes. Das Blättern durch alle Volumes zwingt den Initiator zum Aktualisieren der Verbindung, und das Volume wird mit der Fehlermeldung aufgehoben, dass der Datenträger nicht verfügbar ist.|
| EXE-Ausgabe: Das Skript wird erfolgreich ausgeführt, aber „Neue Volumes angefügt“ wird nicht in der Ausgabe des Skripts angezeigt. |    Dies ist ein vorübergehender Fehler.    | Die Volumes wurden bereits angefügt. Öffnen Sie Explorer, um zu ihnen zu navigieren. Wenn Sie zum Ausführen von Skripts jedes Mal den gleichen Computer verwenden, sollten Sie den Computer neu starten, und die Liste sollte in den nachfolgenden Ausführungen der EXE-Datei angezeigt werden. |
| Linux-spezifische Probleme: Die gewünschten Volumes können nicht angezeigt werden. | Das Betriebssystem des Computers, auf dem das Skript ausgeführt wird, erkennt möglicherweise das zugrunde liegende Dateisystem des geschützten virtuellen Computers nicht. | Prüfen Sie, ob der Wiederherstellungspunkt absturz- oder dateikonsistent ist. Falls dateikonsistent, führen Sie das Skript auf einem anderen Computer aus, dessen Betriebssystem das Dateisystem des geschützten virtuellen Computers erkennt. |
| Windows-spezifische Probleme: Die gewünschten Volumes können nicht angezeigt werden. | Möglicherweise wurden die Datenträger angefügt, aber die Volumes wurden nicht konfiguriert. | Suchen Sie auf dem Bildschirm „Datenträgerverwaltung“ die zusätzlichen Datenträger im Zusammenhang mit dem Wiederherstellungspunkt. Wenn sich einer dieser Datenträger im Offlinestatus befindet, versuchen Sie, diesen online zu schalten, indem Sie mit der rechten Maustaste auf den Datenträger und dann auf „Online“ klicken.|

## <a name="security"></a>Sicherheit

In diesem Abschnitt werden die verschiedenen Sicherheitsmaßnahmen für die Implementierung der Dateiwiederherstellung aus Azure-VM-Sicherungen beschrieben, sodass Benutzer sich mit den Sicherheitsaspekten des Features vertraut machen können.

### <a name="feature-flow"></a>Ablauf des Features

Dieses Feature wurde für den Zugriff auf VM-Daten in wenigen Schritten entwickelt, ohne die Wiederherstellung der gesamten VM oder der gesamten VM-Datenträger zu erfordern. Der Zugriff auf die VM-Daten wird mithilfe eines Skripts gewährt (das das Wiederherstellungsvolume wie im Folgenden veranschaulicht einbindet), weshalb dies den Eckpfeiler aller Sicherheitsimplementierungen bildet.

  ![Ablauf des Sicherheitsfeatures](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Sicherheitsimplementierungen

#### <a name="select-recovery-point-who-can-generate-script"></a>Auswählen des Wiederherstellungspunkts (der Skripts generieren kann)

Das Skript stellt den Zugriff auf VM-Daten bereit. Deshalb ist es wichtig, zu regulieren, wer es überhaupt erstellen kann. Zum Erstellen des Skripts ist eine Anmeldung beim Azure-Portal mit einem [RBAC-autorisierten](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) Konto erforderlich.

Für die Dateiwiederherstellung ist die gleiche Autorisierung erforderlich wie für die VM- und Datenträgerwiederherstellung. Das heißt, nur autorisierte Benutzer können die VM-Daten anzeigen und das Skript generieren.

Das generierte Skript wird mit dem offiziellen Microsoft-Zertifikat für den Azure Backup-Dienst signiert. Jede Manipulation des Skripts macht die Signatur ungültig und jeder Versuch, das Skript auszuführen, wird vom Betriebssystem als potenzielles Risiko gemeldet.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Einbinden des Wiederherstellungsvolumes (Wer Skripts ausführen kann)

Nur Administratoren können das Skript ausführen. Sie sollten es im Modus mit erhöhten Rechten ausführen. Das Skript führt nur vorab generierte Schritte aus und akzeptiert keine Eingaben aus externen Quellen.

Zum Ausführen des Skripts ist ein Kennwort erforderlich, das nur dem autorisierten Benutzer angezeigt wird, wenn das Skript im Azure-Portal oder in PowerShell bzw. in der CLI erstellt wird. Hiermit wird sichergestellt, dass der autorisierte Benutzer, der das Skript herunterlädt, auch für das Ausführen des Skripts verantwortlich ist.

#### <a name="browse-files-and-folders"></a>Durchsuchen von Dateien und Ordnern

Das Skript verwendet den iSCSI-Initiator auf dem Computer und stellt eine Verbindung mit dem Wiederherstellungspunkt her, der als iSCSI-Ziel konfiguriert ist, um Dateien und Ordner zu durchsuchen. Hier könnte man von Szenarios ausgehen, in denen jemand versucht eine bzw. alle der Komponenten zu imitieren/fälschen.

Ein Mechanismus für die gegenseitige CHAP-Authentifizierung wird verwendet, damit sich die Komponenten gegenseitig authentifizieren. Aus diesem Grund ist es sehr schwierig, eine Verbindung zwischen einem gefälschten Initiator und dem iSCSI-Ziel oder eine Verbindung zwischen einem gefälschten Ziel und dem Computer herzustellen, auf dem das Skript ausgeführt wird.

Der Datenfluss zwischen dem Wiederherstellungsdienst und dem Computer wird mithilfe eines sicheren SSL-Tunnels über TCP geschützt (auf dem Computer, auf dem das Skript ausgeführt wird, [sollte TLS 1.2 unterstützt werden](#system-requirements)).

Alle Zugriffssteuerungslisten für Dateien, die auf der übergeordneten/gesicherten VM vorhanden sind, werden ebenfalls im eingebundenen Dateisystem gespeichert.

Das Skript erteilt einem Wiederherstellungspunkt schreibgeschützten Zugriff und ist 12 Stunden lang gültig. Wenn der Benutzer den Zugriff früher entfernen möchte, muss er sich beim Azure-Portal, bei PowerShell oder bei der CLI anmelden und die **Einbindung von Datenträgern für diesen Wiederherstellungspunkt aufheben**. Daraufhin wird das Skript sofort ungültig.
