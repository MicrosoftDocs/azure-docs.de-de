---
title: Sichern einer Oracle Database 19c-Datenbank auf einer Azure-Linux-VM mithilfe von RMAN und Azure Storage
description: Erfahren Sie, wie Sie eine Oracle Database 19c-Datenbank im Azure-Cloudspeicher sichern.
author: cro27
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: fce947c43e8559f4ea2a65645805e987a9015d3f
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806272"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-storage"></a>Sichern und Wiederherstellen einer Oracle Database 19c-Datenbank auf einer Azure-Linux-VM mithilfe von Azure Storage

In diesem Artikel wird die Verwendung von Azure Storage als Medium zum Sichern und Wiederherstellen einer Oracle-Datenbank erläutert, die auf einer Azure-VM ausgeführt wird. Sie sichern die Datenbank mithilfe von Oracle RMAN in Azure File Storage, der über das SMB-Protokoll auf der VM eingebunden ist. Die Verwendung von Azure Storage als Sicherungsmedium ist äußerst kostengünstig und leistungsstark. Bei sehr umfangreichen Datenbanken ist Azure Backup jedoch die bessere Lösung.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- Sie müssen für den Sicherungs- und Wiederherstellungsprozess zuerst eine Linux-VM erstellen, auf der eine Instanz von Oracle Database 19c installiert ist. Das Marketplace-Image, das derzeit zum Erstellen der VM verwendet wird, heißt **Oracle:oracle-database-19-3:oracle-database-19-0904:latest**. Führen Sie die Schritte im Schnellstart [Erstellen einer Oracle-Datenbank auf einem virtuellen Azure-Computer](./oracle-database-quick-create.md) aus, um eine Oracle-Datenbank zu erstellen, mit der Sie dieses Tutorial absolvieren können.

## <a name="prepare-the-database-environment"></a>Vorbereiten der Datenbankumgebung

1. Erstellen Sie mit dem folgenden Befehl eine SSH-Sitzung (Secure Shell) mit dem virtuellen Computer. Ersetzen Sie die Kombination aus IP-Adresse und Hostname durch den `publicIpAddress`-Wert für Ihren virtuellen Computer.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
2. Wechseln Sie zum Benutzer ***root***:
 
   ```bash
   sudo su -
   ```
    
3. Fügen Sie der Datei ***/etc/sudoers*** den Benutzer „oracle“ hinzu:

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

4. Dieser Schritt setzt voraus, dass Sie über eine Oracle-Instanz (test) verfügen, die auf der VM *vmoracle19c* ausgeführt wird.

   Ändern Sie den Benutzer in den Benutzer *oracle*:

   ```bash
   sudo su - oracle
   ```
    
5. Bevor Sie eine Verbindung herstellen, müssen Sie die Umgebungsvariable ORACLE_SID festlegen:
    
    ```bash
    export ORACLE_SID=test;
    ```
   
   Sie müssen auch für künftige Anmeldungen die Variable ORACLE_SID in der Datei `.bashrc` für `oracle`-Benutzer mit dem folgenden Befehl hinzufügen:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
6. Starten Sie den Oracle-Listener, sofern er noch nicht ausgeführt wird:
    
   ```bash
   $ lsnrctl start
   ```

    Die Ausgabe sollte in etwa wie im folgenden Beispiel aussehen:

    ```bash
    LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 18-SEP-2020 03:23:49

    Copyright (c) 1991, 2019, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    System parameter file is /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))

    Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    Start Date                18-SEP-2020 03:23:49
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Parameter File   /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
    The listener supports no services
    The command completed successfully
    ```

7.  Erstellen Sie den Speicherort für die Fast Recovery Area (FRA):

    ```bash
    mkdir /u02/fast_recovery_area
    ```

8.  Stellen Sie eine Verbindung mit der Datenbank her:

    ```bash
    sqlplus / as sysdba
    ```

9.  Starten Sie die Datenbank, sofern sie nicht bereits ausgeführt wird:

    ```bash
    SQL> startup
    ```

10. Legen Sie die Umgebungsvariablen der Datenbank für die Fast Recovery Area fest:

    ```bash
    SQL>  system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
11. Stellen Sie sicher, dass sich die Datenbank im ARCHIVELOG-Modus befindet, um Onlinesicherungen zu ermöglichen.
    Überprüfen Sie zuerst den Protokollarchivstatus:

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    Wenn der NOARCHIVELOG-Modus festgelegt ist, führen Sie die folgenden Befehle in sqlplus aus:

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

12. Erstellen Sie eine Tabelle zum Testen der Sicherungs- und Wiederherstellungsvorgänge:

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

## <a name="back-up-to-azure-files"></a>Sicherung in Azure Files

Führen Sie für die Sicherung in Azure Files die folgenden Schritte aus:

1. Richten Sie Azure File Storage ein.
1. Binden Sie die Azure Storage-Dateifreigabe auf der VM ein.
1. Sichern Sie die Datenbank.
1. Stellen Sie die Datenbank wieder her.

### <a name="set-up-azure-file-storage"></a>Einrichten von Azure File Storage

In diesem Schritt sichern Sie die Oracle-Datenbank mithilfe von Oracle Recovery Manager (RMAN) in Azure File Storage. Azure-Dateifreigaben sind vollständig verwaltete Dateifreigaben, die in der Cloud gespeichert werden. Der Zugriff erfolgt entweder über das SMB-Protokoll (Server Message Block) oder das NFS-Protokoll (Network File System). In diesem Schritt wird eine Dateifreigabe erstellt, die über das SMB-Protokoll auf der VM eingebunden wird. Informationen zur Einbindung über NFS finden Sie unter [Einbinden von Azure Blob Storage mithilfe des NFS 3.0-Protokolls](../../../storage/blobs/network-file-system-protocol-support-how-to.md).

Beim Einbinden von Azure Files wird mit `cache=none` das Zwischenspeichern von Dateifreigabedaten deaktiviert. Um sicherzustellen, dass die in der Freigabe erstellten Dateien im Besitz des Oracle-Benutzers sind, werden auch die Optionen `uid=oracle` und `gid=oinstall` festgelegt. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Richten Sie zunächst das Speicherkonto ein.

1. Konfigurieren von File Storage im Azure-Portal

    Wählen Sie im Azure-Portal die Option * **+ Ressource erstellen** _ aus. Suchen Sie nach *_Speicherkonto_**, und wählen Sie diese Option aus.
    
    ![Screenshot, der zeigt, wo Sie eine Ressource erstellen und ein Speicherkonto auswählen.](./media/oracle-backup-recovery/storage-1.png)
    
2. Wählen Sie auf der Seite „Speicherkonto erstellen“ Ihre vorhandene Ressourcengruppe ***rg-oracle** _ aus. Benennen Sie das Speicherkonto mit _*_oracbkup1_*_, und wählen Sie _*_Storage V2 (generalpurpose v2)_*_ unter „Kontoart“ aus. Wählen Sie unter „Replikation“ die Option _*_Lokal redundanter Speicher (LRS)_*_ aus, und legen Sie „Leistung“ auf _*_Standard_** fest. Stellen Sie sicher, dass der Standort auf die Region aller anderen Ressourcen in der Ressourcengruppe festgelegt ist. 
    
    ![Screenshot, der zeigt, wo Sie Ihre vorhandene Ressourcengruppe auswählen können.](./media/oracle-backup-recovery/file-storage-1.png)
   
   
3. Klicken Sie auf die Registerkarte ***Erweitert** _, und legen Sie unter „Azure Files“ die Einstellung _*_Große Dateifreigaben_*_ auf *_Aktiviert_** fest. Klicken Sie auf Überprüfen + erstellen und dann auf Erstellen.
    
    ![Screenshot, der zeigt, wo Sie „Große Dateifreigaben“ als „Aktiviert“ festlegen.](./media/oracle-backup-recovery/file-storage-2.png)
    
    
4. Navigieren Sie nach dem Erstellen des Speicherkontos zu der Ressource, und wählen Sie ***Dateifreigaben*** aus.
    
    ![Screenshot, der zeigt, wo Sie die Dateifreigaben auswählen können.](./media/oracle-backup-recovery/file-storage-3.png)
    
5. Klicken Sie auf * **+ Dateifreigabe** _, und geben Sie auf dem Blatt _*_Neue Dateifreigabe_*_ der Dateifreigabe den Namen _*_orabkup1_*_. Legen Sie _*_Kontingent_*_ auf _*_10.240_*_  GiB fest, und wählen Sie _*_Transaktion optimiert_*_ als Ebene aus. Das Kontingent gibt die Obergrenze an, bis zu der die Dateifreigabe erweitert werden kann. Da Storage Standard verwendet wird, werden die Ressourcen nutzungsbasiert abgerechnet und nicht bereitgestellt, sodass durch die Festlegung auf 10 TiB keine Kosten über die Nutzung hinaus entstehen. Wenn Ihre Sicherungsstrategie mehr Speicher erfordert, müssen Sie das Kontingent auf eine entsprechende Ebene festlegen, um alle Sicherungen speichern zu können.   Wenn Sie alle Eingaben auf dem Blatt „Neue Dateifreigabe“ vorgenommen haben, klicken Sie auf _*_Erstellen_**.
    
    ![Screenshot, der zeigt, wo Sie eine neue Dateifreigabe hinzufügen können.](./media/oracle-backup-recovery/file-storage-4.png)
    
    
6. Klicken Sie nach der Erstellung auf der Seite „Dateifreigabeeinstellungen“ auf ***orabkup1***. 
    Klicken Sie auf die Registerkarte ***Verbinden** _, um das Blatt „Verbinden“ zu öffnen. Klicken Sie dann auf die Registerkarte _ *_Linux_**. Kopieren Sie die angegebenen Befehle zum Einbinden der Dateifreigabe über das SMB-Protokoll. 
    
    ![Seite „Speicherkonto hinzufügen“](./media/oracle-backup-recovery/file-storage-5.png)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie zum Einrichten von Speicherkonto und Dateifreigabe die folgenden Befehle in der Azure-Befehlszeilenschnittstelle aus.

1. Erstellen Sie das Speicherkonto entsprechend der Ressourcengruppe und Region Ihrer VM:
   ```azurecli
   az storage account create -n orabackup1 -g rg-oracle -l eastus --sku Standard_LRS --enable-large-file-share
   ```

2. Erstellen Sie im Speicherkonto eine Dateifreigabe mit einem Kontingent von 10 TiB:

   ```azurecli
   az storage share create --account-name orabackup1 --name orabackup --quota 10240
   ```

3. Rufen Sie den Primärschlüssel des Speicherkontos (key1) ab. Er wird benötigt, wenn Sie die Dateifreigabe auf der VM einbinden:

   ```azurecli
   az storage account keys list --resource-group rg-oracle --account-name orabackup1
   ```

---

### <a name="mount-the-azure-storage-file-share-to-your-vm"></a>Einbinden der Azure Storage-Dateifreigabe auf der VM

1. Erstellen Sie den Bereitstellungspunkt:

   ```bash
   sudo mkdir /mnt/orabackup
   ```

2. Richten Sie die Anmeldeinformationen ein:

   ```bash
   if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
   fi
   ```

   Ersetzen Sie `<Your Storage Account Key1>` durch den zuvor abgerufenen Speicherkontoschlüssel, und führen Sie dann den folgenden Befehl aus:
   ```bash
   if [ ! -f "/etc/smbcredentials/orabackup1.cred" ]; then
     sudo bash -c 'echo "username=orabackup1" >> /etc/smbcredentials/orabackup1.cred'
     sudo bash -c 'echo "password=<Your Storage Account Key1>" >> /etc/smbcredentials/orabackup1.cred'
   fi
   ```

3. Ändern Sie die Berechtigungen für die Datei mit den Anmeldeinformationen:

   ```bash
   sudo chmod 600 /etc/smbcredentials/orabackup1.cred
   ```

4. Fügen Sie die Einbindung in „/etc/fstab“ hinzu:

   ```bash
   sudo bash -c 'echo "//orabackup1.file.core.windows.net/orabackup /mnt/orabackup cifs nofail,vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall" >> /etc/fstab'
   ```

5. Führen Sie die Befehle aus, um den Azure Files-Speicher über das SMB-Protokoll einzubinden:

   ```bash
   sudo mount -t cifs //orabackup1.file.core.windows.net/orabackup /mnt/orabackup -o vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall
   ```

   Wenn ein Fehler ähnlich dem folgenden angezeigt wird:

   ```output
   mount: wrong fs type, bad option, bad superblock on //orabackup1.file.core.windows.net/orabackup 
   ```

   ist das CIFS-Paket möglicherweise nicht auf dem Linux-Host installiert. 
   
   Führen Sie den folgenden Befehl aus, um zu überprüfen, ob CIS installiert ist:

   ```bash
   sudo rpm -qa|grep cifs-utils
   ```

   Wenn keine Ausgabe zurückgegeben wird, installieren Sie das CIFS-Paket mit dem folgenden Befehl:

   ```bash 
   sudo yum install cifs-utils
   ```

   Führen Sie den oben angegebenen mount-Befehl erneut aus, um den Azure Files-Speicher einzubinden.

6. Überprüfen Sie mit dem folgenden Befehl, ob die Dateifreigabe ordnungsgemäß eingebunden ist:

   ```bash
   df -h
   ```

   Die Ausgabe sollte in etwa wie folgt aussehen:

   ```output
   $ df -h
   Filesystem                                    Size  Used Avail Use% Mounted on
   devtmpfs                                      3.3G     0  3.3G   0% /dev
   tmpfs                                         3.3G     0  3.3G   0% /dev/shm
   tmpfs                                         3.3G   17M  3.3G   1% /run
   tmpfs                                         3.3G     0  3.3G   0% /sys/fs/cgroup
   /dev/sda2                                      30G  9.1G   19G  34% /
   /dev/sdc1                                      59G  2.7G   53G   5% /u02
   /dev/sda1                                     497M  199M  298M  41% /boot
   /dev/sda15                                    495M  9.7M  486M   2% /boot/efi
   tmpfs                                         671M     0  671M   0% /run/user/54321
   /dev/sdb1                                      14G  2.1G   11G  16% /mnt/resource
   tmpfs                                         671M     0  671M   0% /run/user/54322
   //orabackup1.file.core.windows.net/orabackup   10T     0   10T   0% /mnt/orabackup
   ```

### <a name="back-up-the-database"></a>Sichern der Datenbank

In diesem Abschnitt wird mit Oracle Recovery Manager (RMAN) eine vollständige Sicherung der Datenbank erstellt. Außerdem werden damit Protokolle archiviert und die Sicherung als Sicherungssatz in die zuvor eingebundene Azure-Dateifreigabe geschrieben. 

1. Konfigurieren Sie RMAN für die Sicherung in den Azure Files-Bereitstellungspunkt:

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/mnt/orabkup/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s'; 
    ```

2. Da Azure-Standarddateifreigaben eine maximale Dateigröße von 1 TiB aufweisen, wird die Größe der RMAN-Sicherungen auf 1 TiB eingeschränkt. (Beachten Sie, dass Premium-Dateifreigaben eine maximale Dateigröße von 4 TiB aufweisen. Weitere Informationen finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Files](../../../storage/files/storage-files-scale-targets.md).)

    ```bash
    RMAN> configure channel device type disk maxpiecesize 1000G;
    ```

3. Bestätigen Sie die Details der Konfigurationsänderung:

    ```bash
    RMAN> show all;
    ```

4. Führen Sie nun die Sicherung aus. Mit dem folgenden Befehl wird eine vollständige Sicherung der Datenbank, einschließlich der ARCHIVELOG-Dateien, als Backupset (Sicherungssatz) im komprimierten Format erstellt:   

    ```bash
    RMAN> backup as compressed backupset database plus archivelog;
    ```

Damit haben Sie die Datenbank mithilfe von Oracle RMAN online erstellt, wobei sich die Sicherung in Azure File Storage befindet. Diese Methode hat den Vorteil, dass die Funktionen von RMAN genutzt werden, wenn Sicherungen in Azure File Storage gespeichert werden. Dort kann über andere VMs auf sie zugegriffen werden. Dies ist nützlich, wenn Sie die Datenbank klonen möchten.  
    
Die Verwendung von RMAN und Azure File Storage für die Datenbanksicherung hat zwar viele Vorteile, aber die Sicherungs- und Wiederherstellungszeit hängt mit der Größe der Datenbank zusammen, sodass diese Vorgänge bei sehr umfangreichen Datenbanken sehr lange dauern können.  Bei einem alternativen Sicherungsmechanismus, bei dem anwendungskonsistente VM-Sicherungen von Azure Backup verwendet werden, wird die Momentaufnahmetechnologie zur Ausführung von Sicherungen genutzt. Dies hat den Vorteil, dass die Sicherungen unabhängig von der Datenbankgröße sehr schnell erstellt werden. Die Integration in einen Recovery Services-Tresor ermöglicht eine sichere Speicherung der Oracle Database-Sicherungen im Azure-Cloudspeicher, auf die von anderen VMs und Azure-Regionen zugegriffen werden kann. 

### <a name="restore-and-recover-the-database"></a>Wiederherstellen der Datenbank

1.  Fahren Sie die Oracle-Instanz herunter:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  Entfernen Sie die Datendateien und Sicherungen:

    ```bash
    cd /u02/oradata/TEST
    rm -f *.dbf
    ```

3. Bei den folgenden Befehlen wird RMAN verwendet, um die fehlenden Datendateien und die Datenbank wiederherzustellen:

    ```bash
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
4. Prüfen Sie, ob der Datenbankinhalt vollständig wiederhergestellt wurde:

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```


Die Sicherung und Wiederherstellung der Oracle Database 19c-Datenbank auf einer Azure-Linux-VM ist damit abgeschlossen.

## <a name="delete-the-vm"></a>Löschen der virtuellen Computer

Wenn Sie den virtuellen Computer nicht mehr benötigen, können Sie mit dem folgenden Befehl die Ressourcengruppe, den virtuellen Computer und alle zugehörigen Ressourcen entfernen:

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>Nächste Schritte

[Tutorial: Erstellen von hoch verfügbaren virtuellen Computern](../../linux/create-cli-complete.md)

[Erkunden der Azure CLI-Beispiele für die Bereitstellung virtueller Computer](../../linux/cli-samples.md)
