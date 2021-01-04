---
title: Installieren eines Masterzielservers für ein Linux-VM-Failback mit Azure Site Recovery
description: Hier erfahren Sie, wie Sie einen Linux-Masterzielserver für ein Failback an einem lokalen Standort während der Notfallwiederherstellung virtueller VMware-Computer in Azure mithilfe von Azure Site Recovery einrichten.
author: mayurigupta13
services: site-recovery
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: mayg
ms.openlocfilehash: 9e1008f7acbfe0685b7a171176c7dc54592d1491
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019241"
---
# <a name="install-a-linux-master-target-server-for-failback"></a>Installieren eines Linux-Masterzielservers für Failbacks
Nach dem Failover Ihrer virtuellen Computer zu Azure können Sie für die virtuellen Computer ein Failback zum lokalen Standort durchführen. Für ein Failback müssen Sie den virtuellen Computer von Azure zum lokalen Standort erneut schützen. Für diesen Prozess benötigen Sie einen lokalen Masterzielserver, der den Datenverkehr empfängt. 

Falls es sich bei Ihrem geschützten virtuellen Computer um einen virtuellen Windows-Computer handelt, benötigen Sie ein Windows-Masterziel. Bei einem virtuellen Linux-Computer benötigen Sie ein Linux-Masterziel. Lesen Sie die folgenden Schritte, um Informationen zum Erstellen und Installieren eines Linux-Masterziels zu erhalten.

> [!IMPORTANT]
> Ab Version 9.10.0 des Masterzielservers kann der neueste Masterzielserver nur auf einem Ubuntu 16.04 Server installiert werden. Neue Installationen sind auf CentOS6.6-Servern nicht zugelassen. Sie können Ihre alten Masterzielserver jedoch weiterhin mithilfe der Version 9.10.0 aktualisieren.
> Masterzielserver auf LVMs werden nicht unterstützt.

## <a name="overview"></a>Übersicht
Dieser Artikel enthält Anleitungen zum Installieren eines Linux-Masterziels.

Kommentare oder Fragen können Sie am Ende dieses Artikels oder auf der [Microsoft F&A-Seite für Azure Recovery Services](/answers/topics/azure-site-recovery.html) veröffentlichen.

## <a name="prerequisites"></a>Voraussetzungen

* Zur Auswahl des Hosts, auf dem Sie das Masterziel bereitstellen, ermitteln Sie, ob das Failback zu einem vorhandenen, lokalen virtuellen Computer oder zu einem neuen virtuellen Computer führt. 
    * Bei einem vorhandenen virtuellen Computer muss der Host des Masterziels über Zugriff auf die Datenspeicher des virtuellen Computers verfügen.
    * Falls kein lokaler virtueller Computer vorhanden ist (bei einer Wiederherstellung an einem alternativen Speicherort), wird das Failback des virtuellen Computers auf demselben Host wie das Masterziel erstellt. Sie können einen beliebigen ESXi-Host zum Installieren des Masterziels verwenden.
* Das Masterziel sollte sich in einem Netzwerk befinden, das mit dem Prozessserver und dem Konfigurationsserver kommunizieren kann.
* Die Version des Masterziels darf nicht höher als die des Prozess- und des Konfigurationsservers sein. Beispiel: Bei Version 9.4 des Konfigurationsservers kann die Version des Masterziels 9.4 oder 9.3, aber nicht 9.5 sein.
* Das Masterziel kann nur ein virtueller VMware-Computer und kein physischer Server sein.

> [!NOTE]
> Stellen Sie sicher, dass Sie Storage vMotion nicht auf Verwaltungskomponenten wie dem Masterziel aktivieren. Wenn das Masterziel nach erfolgreichem erneuten Schützen verschoben wird, können die Datenträger des virtuellen Computers nicht getrennt werden. In diesem Fall tritt beim Failback ein Fehler auf.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>Größenrichtlinien für das Erstellen von Masterzielservern

Erstellen Sie das Masterziel gemäß den folgenden Richtlinien zum Festlegen der Größe:
- **RAM**: 6 GB oder mehr
- **Größe des Betriebssystemdatenträgers**: 100 GB oder mehr (für die Betriebssysteminstallation)
- **Zusätzliche Datenträgergröße für Aufbewahrungslaufwerk**: 1 TB
- **CPU-Kerne**: 4 Kerne oder mehr
- **Kernel**: 4.16.*

## <a name="deploy-the-master-target-server"></a>Bereitstellen des Masterzielservers

### <a name="install-ubuntu-16042-minimal"></a>Installieren Sie Ubuntu 16.04.2 Minimal

Führen Sie die folgenden Schritte aus, um das 64-Bit-Betriebssystem Ubuntu 16.04.2 zu installieren:

1.   Rufen Sie den [Downloadlink](http://old-releases.ubuntu.com/releases/16.04.2/ubuntu-16.04.2-server-amd64.iso) auf, wählen Sie den nächstgelegenen Spiegel aus, und laden Sie ein ISO-Image mit Ubuntu 16.04.2 (minimal 64 Bit) herunter.
Legen Sie den Datenträger mit dem ISO-Image für Ubuntu 16.04.2 minimal 64 Bit in das DVD-Laufwerk ein, und starten Sie das System.

1.  Wählen Sie **English** als bevorzugte Sprache aus, und drücken Sie die **EINGABETASTE**.
    
    ![Sprache auswählen](./media/vmware-azure-install-linux-master-target/image1.png)
1. Wählen Sie **Install Ubuntu Server**, und drücken Sie die **EINGABETASTE**.

    ![„Ubuntu Server installieren“ auswählen](./media/vmware-azure-install-linux-master-target/image2.png)

1.  Wählen Sie **English** als bevorzugte Sprache aus, und drücken Sie die **EINGABETASTE**.

    ![Englisch als bevorzugte Sprache auswählen](./media/vmware-azure-install-linux-master-target/image3.png)

1. Wählen Sie unter **Time Zone** die entsprechende Option aus, und drücken Sie die **EINGABETASTE**.

    ![Richtige Zeitzone auswählen](./media/vmware-azure-install-linux-master-target/image4.png)

1. Wählen Sie **No** (die Standardoption) aus, und drücken Sie die **EINGABETASTE**.

     ![Tastatur konfigurieren](./media/vmware-azure-install-linux-master-target/image5.png)
1. Wählen Sie **English (USA)** als Ursprungsland/-region für die Tastatur aus, und drücken Sie die **EINGABETASTE**.

1. Wählen Sie **English (USA)** als Tastaturlayout aus, und drücken Sie die **EINGABETASTE**.

1. Geben Sie im Feld **Hostname** den Hostnamen für Ihren Server ein, und wählen Sie dann **Continue**.

1. Erstellen Sie ein Benutzerkonto, indem Sie den Benutzernamen eingeben, und wählen Sie dann **Continue**.

      ![Erstellen eines Benutzerkontos](./media/vmware-azure-install-linux-master-target/image9.png)

1. Geben Sie das Kennwort für das neue Benutzerkonto ein, und wählen Sie **Continue**.

1.  Bestätigen Sie das Kennwort für den neuen Benutzer, und wählen Sie **Continue**.

    ![Kennwörter bestätigen](./media/vmware-azure-install-linux-master-target/image11.png)

1.  Wählen Sie als Nächstes zur Verschlüsselung Ihres Stammverzeichnisses die Option **No** (Standardoption), und drücken Sie die **EINGABETASTE**.

1. Wenn die richtige Zeitzone angezeigt wird, wählen Sie **Yes** (Standardoption) aus, und drücken Sie dann die **EINGABETASTE**. Wenn Sie die Zeitzone ändern möchten, wählen Sie **Nein** aus.

1. Wählen Sie aus den Optionen zum Partitionierungsverfahren **Guided – use entire disk**, und drücken Sie die **EINGABETASTE**.

     ![Option für Partitionierungsverfahren auswählen](./media/vmware-azure-install-linux-master-target/image14.png)

1.  Wählen Sie den geeigneten Datenträger unter **Select disk to partition** aus, und drücken Sie die **EINGABETASTE**.

    ![Datenträger auswählen](./media/vmware-azure-install-linux-master-target/image15.png)

1.  Wählen Sie **Yes**, um die Änderungen auf den Datenträger zu schreiben, und drücken Sie die **EINGABETASTE**.

    ![Standardoption auswählen](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  Wählen Sie in der Auswahl für die Proxykonfiguration die Standardoption, und wählen Sie dann **Continue**, und drücken Sie die **EINGABETASTE**.
     
     ![Screenshot: Auswählen von „Continue“ und Drücken der EINGABETASTE](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  Wählen Sie in der Auswahl zur Verwaltung von Upgrades auf Ihrem System die Option **No automatic updates**, und drücken Sie die **EINGABETASTE**.

     ![Option zum Verwalten von Upgrades auswählen](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Da der Azure Site Recovery-Masterzielserver eine ganz bestimmte Version von Ubuntu erfordert, müssen Sie sicherstellen, dass die Kernelupgrades für den virtuellen Computer deaktiviert sind. Wenn sie aktiviert sind, verursacht jedes reguläre Upgrade Fehlfunktionen auf dem Masterzielserver. Stellen Sie sicher, dass die Option **Keine automatischen Updates** ausgewählt ist.

1.  Wählen Sie die Standardoptionen aus. Wenn Sie OpenSSH für eine SSH-Verbindung verwenden möchten, wählen Sie die Option **OpenSSH Server** aus, und wählen Sie dann **Weiter** aus.

    ![Software auswählen](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. Wählen Sie in der Auswahl für die Installation des GRUB-Startladeprogramms die Option **Yes** aus, und drücken Sie dann die **EINGABETASTE**.
     
    ![GRUB-Startinstallationsprogramm](./media/vmware-azure-install-linux-master-target/image20.png)


1. Wählen Sie das entsprechende Gerät für die Installation des Startladeprogramms (vorzugsweise **/dev/sda**) aus, und drücken Sie dann die **EINGABETASTE**.
     
    ![Auswählen des entsprechenden Geräts](./media/vmware-azure-install-linux-master-target/image21.png)

1. Wählen Sie **Continue**, und drücken Sie die **EINGABETASTE**, um die Installation abzuschließen.

    ![Installation abschließen](./media/vmware-azure-install-linux-master-target/image22.png)

1. Nach Abschluss der Installation melden Sie sich mit den neuen Benutzeranmeldeinformationen beim virtuellen Computer an. (Weitere Informationen finden Sie in **Schritt 10**.)

1. Verwenden Sie die im folgenden Screenshot dargestellten Schritte, um das ROOT-Benutzerkennwort festzulegen. Melden Sie sich dann als ROOT-Benutzer an.

    ![ROOT-Benutzerkennwort festlegen](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>Konfigurieren des Computers als Masterzielserver

Der Parameter **disk.EnableUUID = TRUE** muss aktiviert werden, um die ID für jede SCSI-Festplatte eines virtuellen Linux-Computers abzurufen. Um diesen Parameter zu aktivieren, führen Sie die folgenden Schritte aus:

1. Fahren Sie den virtuellen Computer herunter.

2. Klicken Sie mit der rechten Maustaste auf den Eintrag des virtuellen Computers im linken Bereich, und wählen Sie dann **Edit Settings** (Einstellungen bearbeiten) aus.

3. Wählen Sie die Registerkarte **Options** (Optionen) aus.

4. Wählen Sie im linken Bereich **Advanced** (Erweitert)  > **General** (Allgemein) aus, und klicken Sie rechts unten in der Anzeige auf die Schaltfläche **Configuration Parameters** (Konfigurationsparameter).

    ![Offener Konfigurationsparameter](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    Die Option **Configuration Parameters** (Konfigurationsparameter) ist nicht verfügbar, wenn der Computer ausgeführt wird. Um diese Registerkarte zu aktivieren, fahren Sie den virtuellen Computer herunter.

5. Prüfen Sie, ob bereits eine Zeile mit **disk.EnableUUID** vorhanden ist.

   - Wenn der Wert vorhanden ist und auf **False** festgelegt ist, ändern Sie den Wert in **True**. (Bei den Werten wird nicht zwischen Groß- und Kleinschreibung unterschieden.)

   - Wenn der Wert vorhanden und auf **True** festgelegt ist, wählen Sie **Cancel** (Abbrechen) aus.

   - Ist der Wert nicht vorhanden, wählen Sie **Add Row** (Zeile hinzufügen) aus.

   - Fügen Sie in der Spalte „Name“ **disk.EnableUUID** hinzu, und legen Sie den Wert auf **TRUE** fest.

     ![Überprüfen, ob disk.EnableUUID bereits vorhanden ist](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>Deaktivieren des Kernelupgrades

Der Azure Site Recovery-Masterzielserver erfordert eine bestimmte Version von Ubuntu. Stellen Sie daher sicher, dass die Kernelupgrades für den virtuellen Computer deaktiviert sind. Wenn Kernelupgrades aktiviert sind, kann dies zu einer Fehlfunktion des Masterzielservers führen.

#### <a name="download-and-install-additional-packages"></a>Herunterladen und Installieren zusätzlicher Pakete

> [!NOTE]
> Stellen Sie sicher, dass Sie über eine Internetverbindung verfügen, um zusätzliche Pakete herunterzuladen und zu installieren. Wenn keine Internetverbindung vorhanden ist, müssen Sie diese Deb-Pakete manuell suchen und installieren.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>Herunterladen des Installationsprogramms zur Einrichtung

Wenn Ihr Masterziel über eine Internetverbindung verfügt, können Sie die folgenden Schritte durchführen, um das Installationsprogramm herunterzuladen. Andernfalls können Sie das Installationsprogramm vom Prozessserver kopieren und anschließend installieren.

#### <a name="download-the-master-target-installation-packages"></a>Herunterladen der Pakete für die Installation des Masterziels

[Laden Sie die neuesten Linux-Masterziel-Installationsbits herunter](https://aka.ms/latestlinuxmobsvc).

Geben Sie zum Herunterladen mit Linux Folgendes ein:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Stellen Sie sicher, dass Sie das Installationsprogramm herunterladen und im Basisverzeichnis entzippen. Wenn Sie es in **/usr/Local** entzippen, schlägt die Installation fehl.


#### <a name="access-the-installer-from-the-process-server"></a>Zugriff auf das Installationsprogramm über den Prozessserver

1. Navigieren Sie auf dem Prozessserver zu **C:\Programme (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

2. Kopieren Sie die erforderliche Installationsdatei vom Prozessserver, und speichern Sie sie unter **latestlinuxmobsvc.tar.gz** in Ihrem Basisverzeichnis.


### <a name="apply-custom-configuration-changes"></a>Anwenden benutzerdefinierter Konfigurationsänderungen

Gehen Sie zum Vornehmen von Änderungen an der benutzerdefinierten Konfiguration folgendermaßen als Root-Benutzer vor:

1. Führen Sie den folgenden Befehl aus, um die Binärdatei zu entpacken.

    `tar -xvf latestlinuxmobsvc.tar.gz`

    ![Screenshot des auszuführenden Befehls](./media/vmware-azure-install-linux-master-target/image16.png)

2. Führen Sie den folgenden Befehl aus, um die Berechtigung zu erteilen.

    `chmod 755 ./ApplyCustomChanges.sh`


3. Führen Sie den folgenden Befehl aus, um das Skript auszuführen.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Führen Sie das Skript nur einmal auf dem Server aus. Fahren Sie anschließend den Server herunter. Starten Sie den Server neu, nachdem Sie einen Datenträger hinzugefügt haben, wie in den nächsten Schritten beschrieben.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Hinzufügen eines Aufbewahrungsdatenträgers mit dem virtuellen Linux-Masterzielcomputer

Führen Sie die folgenden Schritte aus, um einen Aufbewahrungsdatenträger zu erstellen:

1. Fügen Sie einen neuen 1 TB-Datenträger auf dem virtuellen Linux-Masterzielcomputer hinzu, und starten Sie den Computer anschließend.

2. Ermitteln Sie mit dem Befehl **multipath -ll** die Multipfad-ID des Aufbewahrungsdatenträgers: **multipath -ll**

    ![Multipfad-ID](./media/vmware-azure-install-linux-master-target/image27.png)

3. Formatieren Sie das Laufwerk, und erstellen Sie dann ein Dateisystem auf dem neuen Laufwerk: **mkfs.ext4 /dev/mapper/\<Retention disk's multipath id>** .
    
    ![Dateisystem](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. Nach dem Erstellen des Dateisystems stellen Sie den Aufbewahrungsdatenträger bereit.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. Erstellen Sie den **fstab**-Eintrag, um den Aufbewahrungsdatenträger bei jedem Systemstart bereitzustellen.
    
    `vi /etc/fstab`
    
    Drücken Sie **EINFG**, um mit der Bearbeitung der Datei zu beginnen. Erstellen Sie eine neue Zeile, und fügen Sie den folgenden Text ein. Bearbeiten Sie die Multipfad-ID des Datenträgers auf Basis der hervorgehobenen Multipfad-ID aus dem vorherigen Befehl.

    **/dev/mapper/\<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    Drücken Sie **ESC**, und geben Sie **:wq** (Schreiben und Beenden) ein, um das Editor-Fenster zu schließen.

### <a name="install-the-master-target"></a>Installieren des Masterziels

> [!IMPORTANT]
> Die Version des Masterzielservers darf nicht höher als die des Prozess- und des Konfigurationsservers sein. Wenn diese Bedingung nicht erfüllt ist, funktioniert nur das erneute Schützen, die Replikation schlägt jedoch fehl.


> [!NOTE]
> Vergewissern Sie sich vor dem Installieren des Masterzielservers, dass die Datei **/etc/hosts** auf dem virtuellen Computer Einträge enthält, mit denen der lokale Hostname den IP-Adressen der einzelnen Netzwerkkarten zugeordnet wird.

1. Führen Sie den folgenden Befehl aus, um das Masterziel zu installieren.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    ```

2. Kopieren Sie die Passphrase unter **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** auf dem Konfigurationsserver. Speichern Sie diese dann im gleichen Verzeichnis als **passphrase.txt**, indem Sie den folgenden Befehl ausführen:

    `echo <passphrase> >passphrase.txt`

    Beispiel: 

    `echo itUx70I47uxDuUVY >passphrase.txt`
    

3. Notieren Sie sich die IP-Adresse des Konfigurationsservers. Führen Sie den folgenden Befehl aus, um den Server beim Konfigurationsserver zu registrieren.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Beispiel: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Warten Sie, bis das Skript abgeschlossen wurde. Wenn das Masterziel erfolgreich registriert wurde, ist es auf der Seite **Site Recovery-Infrastruktur** im Portal aufgeführt.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Installieren des Masterziels mithilfe der interaktiven Installation

1. Führen Sie den folgenden Befehl aus, um das Masterziel zu installieren. Wählen Sie als Agent-Rolle die Option **Masterziel** aus.

    ```
    ./install
    ```

2. Wählen Sie den Standardspeicherort für die Installation, und drücken Sie die **EINGABETASTE**, um fortzufahren.

    ![Ein Standardverzeichnis für die Installation des Masterziels auswählen](./media/vmware-azure-install-linux-master-target/image17.png)

Nachdem die Installation abgeschlossen ist, registrieren Sie den Konfigurationsserver über die Befehlszeile.

1. Notieren Sie die IP-Adresse des Konfigurationsservers. Sie benötigen sie im nächsten Schritt.

2. Führen Sie den folgenden Befehl aus, um den Server beim Konfigurationsserver zu registrieren.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh
    ```

     Warten Sie, bis das Skript abgeschlossen wurde. Wenn das Masterziel erfolgreich registriert wurde, wird es auf der **Site Recovery-Infrastrukturseite** im Portal aufgeführt.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>Installieren von VMware-Tools/open-vm-tools auf dem Masterzielserver

Installieren Sie VMware-Tools oder open-vm-tools auf dem Masterziel, damit es die Datenspeicher erkennen kann. Wenn die Tools nicht installiert werden, werden die Datenspeicher nicht auf dem Bildschirm für das erneute Schützen angezeigt. Nach der Installation von VMware-Tools müssen Sie einen Neustart durchführen.

### <a name="upgrade-the-master-target-server"></a>Aktualisieren des Masterzielservers

Führen Sie das Installationsprogramm aus. Es erkennt automatisch, dass der Agent auf dem Masterzielserver installiert ist. Wählen Sie zum Aktualisieren **Y** aus.  Nachdem das Setup abgeschlossen wurde, überprüfen Sie die Version des installierten Masterziels mithilfe des folgenden Befehls:

`cat /usr/local/.vx_version`


Sie sehen, dass im Feld **Version** die Versionsnummer des Masterziels angegeben ist.

## <a name="common-issues"></a>Häufige Probleme

* Stellen Sie sicher, dass Sie Storage vMotion nicht auf Verwaltungskomponenten wie dem Masterziel aktivieren. Wenn das Masterziel nach erfolgreichem erneuten Schützen verschoben wird, können die Datenträger des virtuellen Computers nicht getrennt werden. In diesem Fall tritt beim Failback ein Fehler auf.

* Das Masterziel darf keine Momentaufnahmen auf dem virtuellen Computer aufweisen. Wenn Momentaufnahmen vorhanden sind, schlägt das Failback fehl.

* Aufgrund von benutzerdefinierten NIC-Konfigurationen ist die Netzwerkschnittstelle während des Systemstarts deaktiviert, und der Masterziel-Agent kann nicht initialisiert werden. Stellen Sie sicher, dass die folgenden Eigenschaften richtig festgelegt sind. Überprüfen Sie diese Eigenschaften in der Datei der Ethernet-Karte unter „/etc/network/interfaces“.
    * auto eth0
    * iface eth0 inet dhcp <br>

    Starten Sie den Netzwerkdienst mit dem folgenden Befehl neu: <br>

`sudo systemctl restart networking`


## <a name="next-steps"></a>Nächste Schritte
Nach Abschluss der Installation und Registrierung des Masterziels wird es im Abschnitt **Masterziel** der **Site Recovery-Infrastruktur** in der Übersicht für die Konfigurationsserver angezeigt.

Sie können jetzt mit [Reprotection](vmware-azure-reprotect.md) (Erneuter Schutz) und Failback fortfahren.

