---
title: Einrichten von Oracle ASM auf einem virtuellen Azure Linux-Computer | Microsoft-Dokumentation
description: Richten Sie in Ihrer Azure-Umgebung schnell Oracle ASM ein.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: 6f0b58374ae3eb972993b544117d8cbb98371ce5
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602613"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Einrichten von Oracle ASM auf einem virtuellen Azure Linux-Computer  

Virtuelle Azure-Computer bieten eine vollständig konfigurierbare und flexible Computerumgebung. In diesem Tutorial wird die grundlegende Bereitstellung von virtuellen Azure-Computern in Kombination mit der Installation und Konfiguration von Oracle Automated Storage Management (ASM) behandelt.  Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen eines virtuellen Oracle Database-Computers und Herstellen einer Verbindung
> * Installieren und Konfigurieren von Oracle Automated Storage Management
> * Installieren und Konfigurieren von Oracle Grid Infrastructure
> * Initialisieren der Oracle ASM-Installation
> * Erstellen einer mit ASM verwalteten Oracle DB


Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial die Azure CLI-Version 2.0.4 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>Vorbereiten der Umgebung

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Verwenden Sie zum Erstellen einer Ressourcengruppe den Befehl [az group create](/cli/azure/group). Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. In diesem Beispiel befindet sich eine Ressourcengruppe mit dem Namen *myResourceGroup* in der Region *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Erstellen einer VM

Verwenden Sie den Befehl [az vm create](/cli/azure/vm), um einen virtuellen Computer auf der Grundlage des Oracle Database-Images zu erstellen und diesen für Oracle ASM zu konfigurieren. 

Im folgenden Beispiel wird ein virtueller Computer mit dem Namen „myVM“ erstellt, die die Größe „Standard_DS2_v2“ mit vier angefügten Datenträgern mit jeweils 50 GB aufweist. Darüber hinaus werden SSH-Schlüssel erstellt, falls diese nicht bereits am Standardschlüsselspeicherort vorhanden sind.  Um einen bestimmten Satz von Schlüsseln zu verwenden, nutzen Sie die Option `--ssh-key-value`.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

Nach der Erstellung des virtuellen Computers zeigt die Azure CLI Informationen an, die den Informationen im folgenden Beispiel ähneln. Beachten Sie den Wert für `publicIpAddress`. Diese Adresse wird verwendet, um auf den virtuellen Computer zuzugreifen.

   ```output
   {
     "fqdns": "",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
     "location": "eastus",
     "macAddress": "00-0D-3A-36-2F-56",
     "powerState": "VM running",
     "privateIpAddress": "10.0.0.4",
     "publicIpAddress": "13.64.104.241",
     "resourceGroup": "myResourceGroup"
   }
   ```

### <a name="connect-to-the-vm"></a>Herstellen der Verbindung zur VM

Mit dem folgenden Befehl erstellen Sie eine SSH-Sitzung mit dem virtuellen Computer und konfigurieren weitere Einstellungen. Ersetzen Sie die IP-Adresse durch den Wert für `publicIpAddress` Ihres virtuellen Computers.

```bash
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Installieren von Oracle ASM

Führen Sie zum Installieren von Oracle ASM die folgenden Schritte aus. 

Weitere Informationen zum Installieren von Oracle ASM finden Sie unter [Oracle ASMLib Downloads für Oracle Linux 6](https://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html) (Oracle ASMLib-Downloads für Oracle Linux 6).  

1. Sie müssen sich als „root“ anmelden, um die ASM-Installation fortsetzen:

   ```bash
   sudo su -
   ```
   
2. Führen Sie diese zusätzliche Befehle aus, um Oracle ASM-Komponenten zu installieren:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget https://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Stellen Sie sicher, dass Oracle ASM installiert ist:

   ```bash
   rpm -qa |grep oracleasm
   ```

    In der Ausgabe dieses Befehls sollten die folgenden Komponenten aufgeführt werden:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. ASM erfordert bestimmte Benutzer und Rollen, damit es ordnungsgemäß funktioniert. Mit den folgenden Befehlen erstellen Sie die erforderlichen Benutzerkonten und Gruppen: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Vergewissern Sie sich, dass Benutzer und Gruppen korrekt erstellt wurden:

   ```bash
   id grid
   ```

    In der Ausgabe dieses Befehls sollten die folgenden Benutzer und Gruppen aufgeführt sein:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Erstellen Sie einen Ordner für den Benutzer *grid*, und ändern Sie den Besitzer:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Einrichten von Oracle ASM

In diesem Tutorial wird als Standardbenutzer *grid* und als Standardgruppe *asmadmin* verwendet. Stellen Sie sicher, dass der *oracle*-Benutzer Mitglied der Gruppe „asmadmin“ ist. Führen Sie zum Einrichten der Oracle ASM-Installation die folgenden Schritte aus:

1. Die Einrichtung des Oracle ASM-Bibliothekstreibers umfasst die Definition des Standardbenutzers (grid) und der Standardgruppe (asmadmin) sowie die Konfiguration des Datenträgers, der beim Systemstart gestartet werden soll (wählen Sie „y“) und der Suche nach Datenträgern beim Systemstart (wählen Sie „y“). Sie müssen die Eingabeaufforderungen beim folgenden Befehl beantworten:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   Die Ausgabe dieses Befehls, die durch zu beantwortende Eingabeaufforderungen unterbrochen wird, sollte ungefähr wie folgt aussehen:

    ```output
   Configuring the Oracle ASM library driver.

   This will configure the on-boot properties of the Oracle ASM library
   driver. The following questions will determine whether the driver is
   loaded on boot and what permissions it will have. The current values
   will be shown in brackets ('[]'). Hitting <ENTER> without typing an
   answer will keep that current value. Ctrl-C will abort.

   Default user to own the driver interface []: grid
   Default group to own the driver interface []: asmadmin
   Start Oracle ASM library driver on boot (y/n) [n]: y
   Scan for Oracle ASM disks on boot (y/n) [y]: y
   Writing Oracle ASM library driver configuration: done
   ```

2. Zeigen Sie die Datenträgerkonfiguration an:

   ```bash
   cat /proc/partitions
   ```

   Die Ausgabe dieses Befehls sollte ungefähr wie die folgende Liste der verfügbaren Datenträger aussehen:

   ```output
   8       16   14680064 sdb
   8       17   14678976 sdb1
   8        0   52428800 sda
   8        1     512000 sda1
   8        2   51915776 sda2
   8       48   52428800 sdd
   8       64   52428800 sde
   8       80   52428800 sdf
   8       32   52428800 sdc
   11       0       1152 sr0
   ```

3. Formatieren Sie den Datenträger */dev/sdc*, indem Sie den folgenden Befehl ausführen und die Eingabeaufforderungen beantworten:
   - *n* für eine neue Partition
   - *p* für die primäre Partition
   - *1* zum Auswählen der ersten Partition
   - `enter` drücken für den ersten Standardzylinder
   - `enter` drücken für den letzten Standardzylinder
   - *w* drücken, um die Änderungen in die Partitionstabelle zu schreiben  

   ```bash
   fdisk /dev/sdc
   ```
   
   Bei Verwendung der oben genannten Antworten sollte die Ausgabe des `fdisk`-Befehls wie folgt aussehen:

   ```output
   Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
   Building a new DOS disklabel with disk identifier 0xf865c6ca.
   Changes will remain in memory only, until you decide to write them.
   After that, of course, the previous content won't be recoverable.

   Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

   The device presents a logical sector size that is smaller than
   the physical sector size. Aligning to a physical sector (or optimal
   I/O) size boundary is recommended, or performance may be impacted.

   WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
           switch off the mode (command 'c') and change display units to
           sectors (command 'u').

   Command (m for help): n
   Command action
     e   extended
     p   primary partition (1-4)
   p
   Partition number (1-4): 1
   First cylinder (1-6527, default 1):
   Using default value 1
   Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
   Using default value 6527

   Command (m for help): w
   The partition table has been altered!

   Calling ioctl() to re-read partition table.
   Syncing disks.
   ```

4. Wiederholen Sie den oben beschriebenen `fdisk`-Befehl für `/dev/sdd`, `/dev/sde` und `/dev/sdf`.

5. Überprüfen Sie die Datenträgerkonfiguration:

   ```bash
   cat /proc/partitions
   ```

   Die Ausgabe des Befehls sollte ungefähr wie folgt aussehen:

   ```output
   major minor  #blocks  name

     8       16   14680064 sdb
     8       17   14678976 sdb1
     8       32   52428800 sdc
     8       33   52428096 sdc1
     8       48   52428800 sdd
     8       49   52428096 sdd1
     8       64   52428800 sde
     8       65   52428096 sde1
     8       80   52428800 sdf
     8       81   52428096 sdf1
     8        0   52428800 sda
     8        1     512000 sda1
     8        2   51915776 sda2
     11       0    1048575 sr0
   ```

6. Überprüfen Sie den Oracle ASM-Dienststatus, und starten Sie den Oracle ASM-Dienst:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   Die Ausgabe des Befehls sollte ungefähr wie folgt aussehen:

   ```output
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. Erstellen Sie die Oracle ASM-Datenträger:

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```

   Die Ausgabe des Befehls sollte ungefähr wie folgt aussehen:

   ```output
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Listen Sie die Oracle ASM-Datenträger auf:

   ```bash
   service oracleasm listdisks
   ```

   In der Ausgabe dieses Befehls sollten die folgenden Oracle ASM-Datenträger aufgeführt werden:

   ```output
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Ändern Sie die Kennwörter für die Root-, Oracle- und Grid-Benutzer. **Notieren Sie sich diese neuen Kennwörter**, da Sie diese später bei der Installation benötigen.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. Ändern Sie die Ordnerberechtigung:

    ```bash
    chmod -R 775 /opt 
    chown grid:oinstall /opt 
    chown oracle:oinstall /dev/sdc1 
    chown oracle:oinstall /dev/sdd1 
    chown oracle:oinstall /dev/sde1 
    chown oracle:oinstall /dev/sdf1 
    chmod 600 /dev/sdc1 
    chmod 600 /dev/sdd1 
    chmod 600 /dev/sde1 
    chmod 600 /dev/sdf1
    ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Herunterladen und Vorbereiten von Oracle Grid Infrastructure

Führen Sie zum Herunterladen der Oracle Grid Infrastructure-Software die folgenden Schritte aus:

1. Laden Sie Oracle Grid Infrastructure von der [Oracle ASM-Downloadseite](https://www.oracle.com/database/technologies/oracle19c-linux-downloads.html) herunter. 

   Laden Sie mit dem Download **Oracle Database 12c Release 1 Grid Infrastructure (12.1.0.2.0) for Linux x86-64** zwei ZIP-Dateien herunter.

2. Nachdem Sie die ZIP-Dateien auf den Clientcomputer heruntergeladen haben, können Sie mit Secure Copy Protocol (SCP) die Dateien auf Ihre VM kopieren:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. Melden Sie sich wieder über SSH beim virtuellen Oracle-Computer in Azure an, um die ZIP-Dateien in den Ordner „/opt“ zu verschieben. Ändern Sie dann den Besitzer der Dateien:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Entzippen Sie die Dateien. (Installieren Sie das Linux-Tool zum Entzippen, sofern es noch nicht installiert ist.)

   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Ändern Sie die Berechtigung:

   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Aktualisieren Sie den konfigurierten Auslagerungsbereich. Für Oracle Grid-Komponenten ist ein Auslagerungsbereich von mindestens 6,8 GB erforderlich, um Grid zu installieren. Die Standardgröße des Auslagerungsbereichs für Oracle Linux-Images in Azure beträgt nur 2048 MB. Sie müssen `ResourceDisk.SwapSizeMB` in der Datei `/etc/waagent.conf` erhöhen und den WALinuxAgent-Dienst neu starten, damit die aktualisierten Einstellungen wirksam werden. Da es sich um eine schreibgeschützte Datei handelt, müssen Sie die Dateiberechtigungen so ändern, dass Sie Schreibzugriff haben.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```

   Suchen Sie nach `ResourceDisk.SwapSizeMB`, und ändern Sie den Wert in **8192**. Sie müssen `insert` drücken, um in den Einfügemodus zu wechseln. Geben Sie den Wert **8192** ein, und drücken Sie dann `esc`, um zum Befehlsmodus zurückzukehren. Schreiben Sie die Änderungen in die Datei, und schließen Sie die Datei. Geben Sie `:wq` ein, und drücken Sie `enter`.
   
   > [!NOTE]
   > Es wird dringend empfohlen, den Auslagerungsbereich immer mit `WALinuxAgent` zu konfigurieren, damit er immer auf dem lokalen temporären Datenträger erstellt wird und eine optimale Leistung sichergestellt ist. Weitere Informationen hierzu finden Sie unter [Vorgehensweise zum Hinzufügen einer Auslagerungsdatei auf virtuellen Linux-Azure-Computern](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Vorbereiten des lokalen Clients und des virtuellen Computers für die Ausführung von X11
Die Konfiguration von Oracle ASM erfordert eine grafische Benutzeroberfläche, um die Installation und die Konfiguration abschließen zu können. Im Folgenden wird das X11-Protokoll verwendet, um diese Installation zu ermöglichen. Wenn Sie ein Clientsystem (Mac oder Linux) verwenden, das X11-Funktionen bereits unterstützt und entsprechend konfiguriert ist, können Sie diese Konfiguration und Einrichtung überspringen, die sich ausschließlich auf Windows-Computer bezieht. 

1. Laden Sie [PuTTY](https://www.putty.org/) und [Xming](https://xming.en.softonic.com/) auf Ihren Windows-Computer herunter. Sie müssen die Installation dieser beiden Anwendungen mit den Standardwerten ausführen, bevor Sie fortfahren.

2. Nachdem Sie PuTTY installiert haben, öffnen Sie eine Eingabeaufforderung, wechseln Sie zum PuTTY-Ordner (z.B. C:\Programme\PuTTY), und führen Sie `puttygen.exe` aus, um einen Schlüssel zu generieren.

3. Im PuTTY-Schlüsselgenerator:
   
   1. Generieren Sie einen Schlüssel, indem Sie die Schaltfläche `Generate` auswählen.
   2. Kopieren Sie den Inhalt des Schlüssels (STRG+C).
   3. Wählen Sie die Schaltfläche `Save private key` aus.
   4. Ignorieren Sie die Warnung zum Schützen des Schlüssels mit einer Passphrase, und wählen Sie dann `OK` aus.

   ![Screenshot des PuTTY Key Generator](./media/oracle-asm/puttykeygen.png)

4. Führen Sie auf dem virtuellen Computer die folgenden Befehle aus:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Erstellen Sie eine Datei mit dem Namen `authorized_keys`. Fügen Sie den Inhalt des Schlüssels in dieser Datei ein, und speichern Sie die Datei.

   > [!NOTE]
   > Der Schlüssel muss die Zeichenfolge `ssh-rsa` enthalten. Zudem muss der Inhalt des Schlüssels als einzelne Textzeile eingefügt werden.
   >  

6. Starten Sie PuTTY auf dem Clientsystem. Navigieren Sie im Bereich **Category** (Kategorie) zu **Connection** > **SSH** > **Auth** (Verbindung > SSH > Authentifizierung). Navigieren Sie im Feld **Private key file for authentication** (Datei mit privatem Schlüssel für die Authentifizierung) zu dem zuvor erstellten Schlüssel.

   ![Screenshot der SSH-Authentifizierungsoptionen](./media/oracle-asm/setprivatekey.png)

7. Navigieren Sie im Bereich **Category** (Kategorie) zu **Connection** > **SSH** > **X11** (Verbindung > SSH > X11). Aktivieren Sie das Kontrollkästchen **X11-Weiterleitung aktivieren**.

   ![Screenshot der SSH-X11-Weiterleitungsoptionen](./media/oracle-asm/enablex11.png)

8. Navigieren Sie im Bereich **Category** (Kategorie) zu **Session** (Sitzung). Geben Sie im Dialogfeld für den Hostnamen die `<publicIPaddress>` für den virtuellen Oracle ASM-Computer ein, tragen Sie einen neuen Namen für `Saved Session` (Gespeicherte Sitzung) ein, und klicken Sie dann auf `Save` (Speichern).  Sobald der Speichervorgang abgeschlossen ist, klicken Sie auf `open` (Öffnen), um eine Verbindung mit dem virtuellen Oracle ASM-Computer herzustellen.  Wenn Sie zum ersten Mal eine Verbindung herstellen, erhalten Sie eine Warnmeldung, dass das Remotesystem nicht in der Registrierung zwischengespeichert ist. Klicken Sie auf `yes` (Ja), um das System hinzuzufügen und um den Vorgang fortzusetzen.

   ![Screenshot der PuTTY-Sitzungsoptionen](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Installieren von Oracle Grid Infrastructure

Führen Sie zum Installieren von Oracle Grid Infrastructure die folgenden Schritte aus:

1. Melden Sie sich als **grid** an. (Sie sollten sich ohne Aufforderung zur Kennworteingabe anmelden können.) 

   > [!NOTE]
   > Wenn auf Ihrem Computer Windows ausgeführt wird, vergewissern Sie sich, dass Xming gestartet wurde, bevor Sie mit der Installation beginnen.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Das Installationsprogramm für Oracle Grid Infrastructure 12c Release 1 wird geöffnet. (Es kann einen Moment, bis das Installationsprogramm gestartet wird.)

2. Wählen Sie auf der Seite **Select Installation Option** (Installationsoption auswählen) die Option **Install and Configure Oracle Grid Infrastructure for a Standalone Server** (Oracle Grid Infrastructure für einen eigenständigen Server installieren und konfigurieren).

   ![Screenshot der Seite „Installationsoption auswählen“ des Installationsprogramms](./media/oracle-asm/install01.png)

3. Vergewissern Sie sich auf der Seite **Select Product Languages** (Produktsprache auswählen), dass die Sprache **English** (Englisch) oder die gewünschte Sprache ausgewählt ist.  Klicken Sie auf `next`.

4. Auf der Seite **Create ASM Disk Group** (ASM-Datenträgergruppe erstellen):
   - Geben Sie einen Namen für die Datenträgergruppe ein.
   - Wählen Sie unter **Redundancy** (Redundanz) die Option **External** (Extern).
   - Wählen Sie unter **Allocation Unit Size** (Größe der Zuordnungseinheit) die Größe **4**.
   - Wählen Sie unter **Add Disks** (Datenträger hinzufügen) die Option **ORCLASMSP**.
   - Klicken Sie auf `next`.

5. Wählen Sie auf der Seite **Specify ASM Password** (ASM-Kennwort angeben) die Option **Use same passwords for these accounts** (Gleiche Kennwörter für diese Konten verwenden), und geben Sie ein Kennwort ein.

   ![Screenshot der Seite „ASM-Kennwort angeben“ des Installationsprogramms](./media/oracle-asm/install04.png)

6. Auf der Seite **Specify Management Options** (Verwaltungsoptionen angeben) haben Sie die Möglichkeit, EM Cloud Control zu konfigurieren. In diesem Fall wird dieser Schritt übersprungen. Klicken Sie auf `next` (Weiter), um den Vorgang fortzusetzen. 

7. Verwenden Sie auf der Seite **Privileged Operating System Groups** (Privilegierte Betriebssystemgruppen) die Standardeinstellungen. Klicken Sie auf `next` (Ja), um den Vorgang fortzusetzen.

8. Verwenden Sie auf der Seite **Specify Installation Location** (Installationsort angeben) die Standardeinstellungen. Klicken Sie auf `next` (Ja), um den Vorgang fortzusetzen.

9. Ändern Sie auf der Seite **Create Inventory** (Bestand erstellen) das Bestandsverzeichnis in `/u01/app/grid/oraInventory`. Klicken Sie auf `next` (Ja), um den Vorgang fortzusetzen.

   ![Screenshot der Seite „Bestand erstellen“ des Installationsprogramms](./media/oracle-asm/install08.png)

10. Aktivieren Sie auf der Seite **Konfiguration der Rootskriptausführung** das Kontrollkästchen **Konfigurationsskripts automatisch ausführen**. Wählen Sie dann die Option **Use "root" user credential** (Anmeldeinformationen des root-Benutzers verwenden), und geben Sie das Kennwort des root-Benutzers ein.

    ![Screenshot der Seite „Konfiguration der Rootskriptausführung“ des Installationsprogramms](./media/oracle-asm/install09.png)

11. Auf der Seite **Perform Prerequisite Checks** (Überprüfungen auf erforderliche Komponenten ausführen) werden Fehler für das aktuelle Setup ausgegeben. Dies entspricht dem erwarteten Verhalten. Wählen Sie `Fix & Check Again`aus.

12. Wählen Sie im Dialogfeld **Fixup Script** (Fixupskript) die Option `OK` aus.

13. Überprüfen Sie auf der Seite **Summary** (Zusammenfassung) Ihre ausgewählten Einstellungen, und klicken Sie dann auf `Install` (Installieren).

    ![Screenshot der Seite „Zusammenfassung“ des Installationsprogramms](./media/oracle-asm/install12.png)

14. Es wird eine Warnmeldung angezeigt, in der Sie darauf hingewiesen werden, dass Konfigurationsskripts als berechtigter Benutzer ausgeführt werden müssen. Klicken Sie auf `Yes` (Ja), um den Vorgang fortzusetzen.

15. Klicken Sie auf der Seite **Finish** (Fertig stellen) auf `Close` (Schließen), um die Installation abzuschließen.

## <a name="set-up-your-oracle-asm-installation"></a>Einrichten der Oracle ASM-Installation

Führen Sie zum Einrichten der Oracle ASM-Installation die folgenden Schritte aus:

1. Vergewissern Sie sich, dass Sie in Ihrer X11-Sitzung weiterhin als **grid** angemeldet sind. Möglicherweise müssen Sie `enter` drücken, um das Terminal wieder zu aktivieren. Starten Sie den Oracle Automated Storage Management Configuration Assistant:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Oracle ASM Configuration Assistant wird geöffnet.

2. Klicken Sie im Dialogfeld **Configure ASM: Disk Groups** (ASM konfigurieren: Datenträgergruppen) auf die Schaltfläche `Create` (Erstellen) und dann auf `Show Advanced Options` (Erweiterte Optionen anzeigen).

3. Führen Sie im Dialogfeld **Datenträgergruppe erstellen** Folgendes durch:

   - Geben Sie als Namen der Datenträgergruppe **DATA** ein.
   - Wählen Sie unter **Select Member Disks** (Mitgliedsdatenträger auswählen) die Optionen **ORCL_DATA** und **ORCL_DATA1**.
   - Wählen Sie unter **Allocation Unit Size** (Größe der Zuordnungseinheit) die Größe **4**.
   - Klicken Sie auf `ok`, um die Datenträgergruppe zu erstellen.
   - Klicken Sie auf `ok`, um das Bestätigungsfenster zu schließen.

   ![Screenshot des Dialogfelds „Datenträgergruppe erstellen“](./media/oracle-asm/asm02.png)

4. Klicken Sie im Dialogfeld **Configure ASM: Disk Groups** (ASM konfigurieren: Datenträgergruppen) auf die Schaltfläche `Create` (Erstellen) und dann auf `Show Advanced Options` (Erweiterte Optionen anzeigen).

5. Führen Sie im Dialogfeld **Datenträgergruppe erstellen** Folgendes durch:

   - Geben Sie als Namen der Datenträgergruppe **FRA** ein.
   - Wählen Sie unter **Redundancy** (Redundanz) die Option **External (none)** (Extern – keine) aus.
   - Wählen Sie unter **Select Member Disks** (Mitgliedsdatenträger auswählen) die Option **FRA**.
   - Wählen Sie unter **Allocation Unit Size** (Größe der Zuordnungseinheit) die Größe **4**.
   - Klicken Sie auf `ok`, um die Datenträgergruppe zu erstellen.
   - Klicken Sie auf `ok`, um das Bestätigungsfenster zu schließen.

   ![Screenshot des Dialogfelds zum Erstellen der Datenträgergruppe und der Option „External (none)“ (Extern (keine))](./media/oracle-asm/asm04.png)

6. Wählen Sie **Exit** (Beenden), um ASM Configuration Assistant zu schließen.

   ![Screenshot des Dialogfelds „Configure ASM: Disk Groups“ (ASM konfigurieren: Datenträgergruppen) mit der Schaltfläche „Exit“ (Beenden)](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Erstellen der Datenbank

Die Oracle Database-Software ist bereits im Azure Marketplace-Image installiert. Gehen Sie zum Erstellen einer Datenbank wie folgt vor:

1. Wechseln Sie die Benutzer zum Oracle-Superbenutzer, und initialisieren Sie dann den Listener für die Protokollierung:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```

   Database Configuration Assistant wird geöffnet.

2. Klicken Sie auf der Seite **Database Operation** (Datenbankvorgang) auf `Create Database` (Datenbank erstellen).

3. Auf der Seite **Creation Mode** (Erstellungsmodus):

   - Geben Sie einen Namen für die Datenbank ein.
   - Vergewissern Sie sich, dass für **Storage Type** (Speichertyp) die Option **Automatic Storage Management (ASM)** (Automatische Speicherverwaltung) ausgewählt ist.
   - Verwenden Sie für **Database Files Location** (Speicherort der Datenbankdateien) den von ASM vorgeschlagenen Standardspeicherort.
   - Verwenden Sie für **Fast Recovery Area** (Bereich für die schnelle Wiederherstellung) den von ASM vorgeschlagenen Standardspeicherort.
   - Geben Sie ein Kennwort in **Administrative Password** (Administratorkennwort) und **confirm password** (Kennwort bestätigen) ein.
   - Vergewissern Sie sich, dass `create as container database` (Als Containerdatenbank erstellen) aktiviert ist.
   - Geben Sie in einen Wert für `pluggable database name` (Name der austauschbaren Datenbank) ein.

4. Überprüfen Sie auf der Seite **Summary** (Zusammenfassung) Ihre ausgewählten Einstellungen, und wählen Sie dann `Finish` (Fertig stellen), um die Datenbank zu erstellen.

   ![Screenshot der Seite „Summary“ (Zusammenfassung)](./media/oracle-asm/createdb03.png)

5. Die Datenbank wurde erstellt. Auf der Seite **Finish** (Fertig stellen) haben Sie die Möglichkeit, weitere Konten für die Verwendung dieser Datenbank zu entsperren und die Kennwörter zu ändern. Wählen Sie hierzu **Password Management** (Kennwortverwaltung) aus. Andernfalls klicken Sie auf `close` (Schließen).

## <a name="delete-the-vm"></a>Löschen der virtuellen Computer

Sie haben Oracle Automated Storage Management erfolgreich für Oracle DB-Images aus dem Azure Marketplace konfiguriert.  Wenn Sie den virtuellen Computer nicht mehr benötigen, können Sie die Ressourcengruppe, den virtuellen Computer und alle zugehörigen Ressourcen mit dem folgenden Befehl entfernen:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

[Tutorial: Konfigurieren von Oracle DataGuard](configure-oracle-dataguard.md)

[Tutorial: Konfigurieren von Oracle GoldenGate](Configure-oracle-golden-gate.md)

Informationen zum [Zusammenstellen einer Oracle-Datenbank](oracle-design.md)
