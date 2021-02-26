---
title: Sichern und Wiederherstellen einer Oracle Database 19c-Datenbank auf einer Azure Linux-VM mithilfe von Azure Backup
description: Erfahren Sie, wie Sie eine Oracle Database 19c-Datenbank mit dem Dienst Azure Backup sichern und wiederherstellen.
author: cro27
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: ac045694e8975509635e03221a8cb9cc84446b55
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806408"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-backup"></a>Sichern und Wiederherstellen einer Oracle Database 19c-Datenbank auf einer Azure Linux-VM mithilfe von Azure Backup

In diesem Artikel wird das Erstellen von Momentaufnahmen der VM-Datenträger, einschließlich Datenbankdateien und Fast Recovery Area (Bereich für schnelle Wiederherstellung), mithilfe von Azure Backup demonstriert. Mit Azure Backup können Sie vollständige Momentaufnahmen von Datenträgern erstellen, die sich als Sicherungen eignen und im [Recovery Services-Tresor](../../../backup/backup-azure-recovery-services-vault-overview.md) gespeichert werden.  Azure Backup ermöglicht auch anwendungskonsistente Sicherungen, sodass sichergestellt ist, dass zum Wiederherstellen der Daten keine zusätzlichen Korrekturen erforderlich sind. Durch die Wiederherstellung von anwendungskonsistenten Daten wird die Wiederherstellungsdauer reduziert, sodass Sie schnell zum Zustand der normalen Ausführung zurückkehren können.

> [!div class="checklist"]
>
> * Sichern der Datenbank mit anwendungskonsistenter Sicherung
> * Wiederherstellen der Datenbank mithilfe eines Wiederherstellungspunkts
> * Wiederherstellen der VM mithilfe eines Wiederherstellungspunkts

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

Sie müssen für den Sicherungs- und Wiederherstellungsprozess zuerst eine Linux-VM erstellen, auf der eine Instanz von Oracle Database 19c installiert ist. Das Marketplace-Image, das derzeit zum Erstellen der VM verwendet wird, heißt **Oracle:oracle-database-19-3:oracle-database-19-0904:latest**. Führen Sie die Schritte im Schnellstart [Erstellen einer Oracle-Datenbank auf einem virtuellem Azure-Computer](./oracle-database-quick-create.md) aus, um eine Oracle-Datenbank zu erstellen, mit der Sie dieses Tutorial absolvieren können.


## <a name="prepare-the-environment"></a>Vorbereiten der Umgebung

Führen Sie die folgenden Schritte aus, um die Umgebung vorzubereiten:

1. Stellen Sie eine Verbindung zur VM her.
1. Bereiten Sie die Datenbank vor.

### <a name="connect-to-the-vm"></a>Herstellen der Verbindung zur VM

1. Erstellen Sie mit dem folgenden Befehl eine SSH-Sitzung (Secure Shell) mit dem virtuellen Computer. Ersetzen Sie die Kombination aus IP-Adresse und Hostname durch den `<publicIpAddress>`-Wert für Ihren virtuellen Computer.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
1. Wechseln Sie zum Benutzer *root*:

   ```bash
   sudo su -
   ```
    
1. Fügen Sie der Datei */etc/sudoers* den Benutzer „oracle“ hinzu:

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

### <a name="prepare-the-database"></a>Vorbereiten der Datenbank

Dieser Schritt setzt voraus, dass Sie über eine Oracle-Instanz (*test*) verfügen, die auf einer VM namens *vmoracle19c* ausgeführt wird.

1. Ändern Sie den Benutzer in den Benutzer *oracle*:
 
   ```bash
    sudo su - oracle
    ```
    
2. Bevor Sie eine Verbindung herstellen, müssen Sie die Umgebungsvariablen ORACLE_SID festlegen:
    
    ```bash
    export ORACLE_SID=test;
    ```

    Sie müssen auch für künftige Anmeldungen die Variable ORACLE_SID der `.bashrc`-Datei für `oracle`-Benutzer mit dem folgenden Befehl hinzufügen:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
3. Starten Sie den Oracle-Listener, sofern noch nicht erfolgt:

    ```output
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

4.  Erstellen Sie den Speicherort für die Fast Recovery Area (FRA):

    ```bash
    mkdir /u02/fast_recovery_area
    ```

5.  Stellen Sie eine Verbindung mit der Datenbank her:

    ```bash
    SQL> sqlplus / as sysdba
    ```

6.  Starten Sie die Datenbank, sofern noch nicht erfolgt:

    ```bash
    SQL> startup
    ```

7.  Legen Sie die Umgebungsvariablen der Datenbank für die Fast Recovery Area fest:

    ```bash
    SQL> alter system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
8.  Stellen Sie sicher, dass sich die Datenbank im ARCHIVELOG-Modus befindet, um Onlinesicherungen zu ermöglichen.

    Überprüfen Sie zuerst den Protokollarchivstatus:

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    Wenn Sie sich im NOARCHIVELOG-Modus befinden, führen Sie die folgenden Befehle aus:

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

9.  Erstellen Sie eine Tabelle zum Testen der Sicherungs- und Wiederherstellungsvorgänge:

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

10. Konfigurieren Sie RMAN für die Sicherung in der Fast Recovery Area auf dem VM-Datenträger:

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/u02/fast_recovery_area/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s'; 
    ```

11. Bestätigen Sie die Details der Konfigurationsänderung:

    ```bash
    RMAN> show all;
    ```    

12.  Führen Sie nun die Sicherung aus. Mit dem folgenden Befehl wird eine vollständige Sicherung der Datenbank, einschließlich der ARCHIVELOG-Dateien, als Backupset (Sicherungssatz) im komprimierten Format erstellt:

     ```bash
     RMAN> backup as compressed backupset database plus archivelog;
     ```

## <a name="using-azure-backup"></a>Verwenden von Azure Backup

Der Azure Backup-Dienst bietet einfache, sichere und kostengünstige Lösungen, um Ihre Daten zu sichern und aus der Microsoft Azure-Cloud wiederherzustellen. Azure Backup bietet unabhängige und isolierte Sicherungen zum Schutz vor dem versehentlichen Löschen von Originaldaten. Sicherungen werden in einem Recovery Services-Tresor mit integrierter Verwaltung von Wiederherstellungspunkten gespeichert. Konfiguration und Skalierung sind unkompliziert. Sicherungen werden außerdem optimiert und können bei Bedarf problemlos wiederhergestellt werden.

Der Dienst Azure Backup bietet ein [Framework](../../../backup/backup-azure-linux-app-consistent.md) zum Erreichen von Anwendungskonsistenz bei Sicherungen von Windows- und Linux-VMs für verschiedene Anwendungen wie Oracle, MySQL, Mongo DB, SAP HANA und PostGreSQL. Dies umfasst das Aufrufen eines Pre-Skripts (zum Stilllegen der Anwendungen), ehe eine Momentaufnahme der Datenträger erstellt wird, und eines Post-Skripts (zum Reaktivieren der Anwendungen) nach Erstellen der Momentaufnahme, um die Anwendungen wieder in den normalen Modus zu versetzen. Obwohl Beispiele für Pre- und Post-Skripts auf GitHub bereitgestellt sind, liegt das Erstellen und Pflegen dieser Skripts in Ihrer Verantwortung. 

Mittlerweile bietet Azure Backup ein erweitertes Framework für Pre- und Post-Skripts, über das der Dienst Azure Backup gepackte Pre- und Post-Skripts für ausgewählte Anwendungen bereitstellt. Benutzer von Azure Backup müssen lediglich die Anwendung angeben, woraufhin bei der Azure-VM-Sicherung automatisch die entsprechenden Pre- und Post-Skripts aufgerufen werden. Für die Pflege der gepackten Pre- und Post-Skripts ist das Azure Backup-Team zuständig, sodass Benutzer sicher sein können, dass diese Skripts unterstützt werden und gültig sind. Derzeit sind die vom verbesserten Framework unterstützten Anwendungen *Oracle* und *MySQL*.

In diesem Abschnitt erstellen Sie mit dem erweiterten Framework von Azure Backup anwendungskonsistente Momentaufnahmen Ihrer laufenden VM und der Oracle-Datenbank. Die Datenbank wird in den Sicherungsmodus versetzt, sodass eine transaktionskonsistente Onlinesicherung erfolgen kann, während Azure Backup eine Momentaufnahme der VM-Datenträger erstellt. Bei der Momentaufnahme handelt es sich um eine vollständige Kopie des Speichers und nicht um eine inkrementelle Momentaufnahme oder eine des Typs „Kopie bei Schreibvorgang“, sodass sie ein effektives Medium zur Wiederherstellung Ihrer Datenbank ist. Der Vorteil anwendungskonsistenter Momentaufnahmen von Azure Backup ist, dass sie unabhängig von der Größe Ihrer Datenbank äußerst schnell erstellt werden können. Eine Momentaufnahme kann für Wiederherstellungsvorgänge verwendet werden, sobald sie erstellt wurde. Sie müssen nicht warten, bis sie in den Recovery Services-Tresor übertragen wurde.

Führen Sie die folgenden Schritte aus, um die Datenbank mit Azure Backup zu sichern:

1. Bereiten Sie die Umgebung für eine anwendungskonsistente Sicherung vor.
1. Richten Sie anwendungskonsistente Sicherungen ein.
1. Lösen Sie eine anwendungskonsistente Sicherung der VM aus.

### <a name="prepare-the-environment-for-an-application-consistent-backup"></a>Vorbereiten der Umgebung für eine anwendungskonsistente Sicherung

1. Wechseln Sie zum Benutzer *root*:

   ```bash
   sudo su -
   ```

1. Erstellen Sie einen neuen Benutzer für die Sicherung:

   ```bash
   useradd -G backupdba azbackup
   ```
   
2. Richten Sie die Benutzerumgebung für die Sicherung ein:

   ```bash
   echo "export ORACLE_SID=test" >> ~azbackup/.bashrc
   echo export ORACLE_HOME=/u01/app/oracle/product/19.0.0/dbhome_1 >> ~azbackup/.bashrc
   echo export PATH='$ORACLE_HOME'/bin:'$PATH' >> ~azbackup/.bashrc
   ```
   
3. Richten Sie die externe Authentifizierung für den neuen Benutzer für die Sicherung ein. Der Benutzer für die Sicherung muss in der Lage sein, über eine externe Authentifizierung auf die Datenbank zuzugreifen, damit kein Kennwort abgefragt wird.

   Wechseln Sie zunächst zurück zum Benutzer *oracle*:

   ```bash
   su - oracle
   ```

   Melden Sie sich mit sqlplus bei der Datenbank an, und überprüfen Sie die Standardeinstellungen für die externe Authentifizierung:
   
   ```bash
   sqlplus / as sysdba
   SQL> show parameter os_authent_prefix
   SQL> show parameter remote_os_authent
   ```
   
   Die Ausgabe sollte wie im folgenden Beispiel aussehen: 

   ```output
   NAME                                 TYPE        VALUE
   ------------------------------------ ----------- ------------------------------
   os_authent_prefix                    string      ops$
   remote_os_authent                    boolean     FALSE
   ```

   Erstellen Sie nun den extern authentifizierten Datenbankbenutzer *azbackup*, und erteilen Sie ihm die Berechtigung „sysbackup“:
   
   ```bash
   SQL> CREATE USER ops$azbackup IDENTIFIED EXTERNALLY;
   SQL> GRANT CREATE SESSION, ALTER SESSION, SYSBACKUP TO ops$azbackup;
   ```

   > [!IMPORTANT] 
   > Wenn Sie beim Ausführen der `GRANT`-Anweisung die Fehlermeldung `ORA-46953: The password file is not in the 12.2 format.` erhalten, führen Sie die folgenden Schritte aus, um die ORAPWD-Datei in das Format 12.2 zu migrieren:
   >
   > 1. Beenden Sie sqlplus.
   > 1. Geben Sie der Kennwortdatei im alten Format einen neuen Namen.
   > 1. Migrieren Sie die Kennwortdatei.
   > 1. Entfernen Sie die alte Datei.
   > 1. Führen Sie den folgenden Befehl aus:
   >
   >    ```bash
   >    mv $ORACLE_HOME/dbs/orapwtest $ORACLE_HOME/dbs/orapwtest.tmp
   >    orapwd file=$ORACLE_HOME/dbs/orapwtest input_file=$ORACLE_HOME/dbs/orapwtest.tmp
   >    rm $ORACLE_HOME/dbs/orapwtest.tmp
   >    ```
   >
   > 1. Führen Sie in sqlplus den Vorgang `GRANT` erneut aus.
   >
   
4. Erstellen Sie eine gespeicherte Prozedur zum Protokollieren von Sicherungsmeldungen im Warnungsprotokoll der Datenbank:

   ```bash
   sqlplus / as sysdba
   SQL> GRANT EXECUTE ON DBMS_SYSTEM TO SYSBACKUP;
   SQL> CREATE PROCEDURE sysbackup.azmessage(in_msg IN VARCHAR2)
   AS
     v_timestamp     VARCHAR2(32);
   BEGIN
     SELECT TO_CHAR(SYSDATE, 'YYYY-MM-DD HH24:MI:SS')
     INTO v_timestamp FROM DUAL;
     DBMS_OUTPUT.PUT_LINE(v_timestamp || ' - ' || in_msg);
     SYS.DBMS_SYSTEM.KSDWRT(SYS.DBMS_SYSTEM.ALERT_FILE, in_msg);
   END azmessage;
   /
   SQL> SHOW ERRORS
   SQL> QUIT
   ```
   
### <a name="set-up-application-consistent-backups"></a>Einrichten anwendungskonsistenter Sicherungen  

1. Wechseln Sie zum Benutzer *root*:

   ```bash
   sudo su -
   ```

2. Erstellen Sie das Arbeitsverzeichnis für die anwendungskonsistente Sicherung:

   ```bash
   if [ ! -d "/etc/azure" ]; then
      sudo mkdir /etc/azure
   fi
   ```

3. Erstellen Sie im Verzeichnis */etc/azure* eine Datei namens *workload.conf* mit folgendem Inhalt, der mit `[workload]` beginnen muss. Der folgende Befehl dient zum Erstellen der Datei und zum Auffüllen des Inhalts:

   ```bash
   echo "[workload]
   workload_name = oracle
   command_path = /u01/app/oracle/product/19.0.0/dbhome_1/bin/
   timeout = 90
   linux_user = azbackup" > /etc/azure/workload.conf
   ```

4. Laden Sie die Skripts „preOracleMaster.sql“ und „postOracleMaster.sql“ aus dem [GitHub-Repository](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts) herunter, und kopieren Sie sie in das Verzeichnis */etc/azure*.

5. Ändern der Dateiberechtigungen

```bash
   chmod 744 workload.conf preOracleMaster.sql postOracleMaster.sql 
   ```

### <a name="trigger-an-application-consistent-backup-of-the-vm"></a>Auslösen einer anwendungskonsistenten Sicherung der VM

# <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Wechseln Sie im Azure-Portal zu Ihrer Ressourcengruppe **rg-oracle**, und klicken Sie auf Ihren virtuellen Computer **vmoracle19c**.

2.  Erstellen Sie auf dem Blatt **Sicherung** in der Ressourcengruppe **rg-oracle** einen neuen **Recovery Services-Tresor** mit dem Namen **myVault**.
    Wählen Sie für **Sicherungsrichtlinie auswählen** die Option **(neu) DailyPolicy**. Wenn Sie die Häufigkeit der Sicherung oder den Aufbewahrungszeitraum ändern möchten, wählen Sie stattdessen **Neue Richtlinie erstellen** aus.

    ![Seite zum Hinzufügen von Recovery Services-Tresoren](./media/oracle-backup-recovery/recovery-service-01.png)

3.  Klicken Sie zum Fortfahren auf **Sicherung aktivieren**.

    > [!IMPORTANT]
    > Nachdem Sie auf **Sicherung aktivieren** geklickt haben, wird der Sicherungsprozess erst gestartet, wenn die geplante Zeit abgelaufen ist. Führen Sie den nächsten Schritt aus, um eine sofortige Sicherung einzurichten.

4. Klicken Sie auf der Seite „Ressourcengruppe“ auf den neu erstellten Recovery Services-Tresor **myVault**. Hinweis: Möglicherweise müssen Sie die Seite aktualisieren, damit er angezeigt wird.

5.  Wählen Sie auf dem Blatt **myVault – Sicherungselemente** unter **Anzahl von Sicherungselementen** die Anzahl der Sicherungselemente aus.

    ![Seite mit myVault-Details zu Recovery Services-Tresoren](./media/oracle-backup-recovery/recovery-service-02.png)

6.  Klicken Sie auf dem Blatt **Sicherungselemente (Virtueller Azure-Computer)** rechts auf die Schaltfläche mit den Auslassungspunkten (**...**) und anschließend auf **Jetzt sichern**.

    ![Befehl „Jetzt sichern“ für Recovery Services-Tresore](./media/oracle-backup-recovery/recovery-service-03.png)

7.  Übernehmen Sie den Standardwert von „Sicherung aufbewahren bis“, und klicken Sie auf die Schaltfläche **OK**. Warten Sie, bis der Sicherungsvorgang abgeschlossen ist. 

    Klicken Sie auf **Sicherungsaufträge**, um den Status des Sicherungsauftrags anzuzeigen.

    ![Seite mit Aufträgen zu Recovery Services-Tresoren](./media/oracle-backup-recovery/recovery-service-04.png)

    Der Status des Sicherungsauftrags wird im folgenden Bild dargestellt:

    ![Seite mit Aufträgen zu Recovery Services-Tresoren mit Status](./media/oracle-backup-recovery/recovery-service-05.png)
    
    Beachten Sie, dass die Ausführung der Momentaufnahme zwar nur Sekunden dauert, die Übertragung in den Tresor jedoch einige Zeit in Anspruch nehmen kann und der Sicherungsauftrag erst nach Abschluss der Übertragung beendet ist.

8. Geben Sie für eine anwendungskonsistente Sicherung alle Fehler in der Protokolldatei an. Die Protokolldatei befindet sich in „/var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/extension.log“.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Erstellen Sie einen Recovery Services-Tresor:

   ```azurecli
   az backup vault create --location eastus --name myVault --resource-group rg-oracle
   ```

2. Aktivieren Sie den Sicherungsschutz für die VM:

   ```azurecli
   az backup protection enable-for-vm \
      --resource-group rg-oracle \
      --vault-name myVault \
      --vm vmoracle19c \
      --policy-name DefaultPolicy
   ```

3. Lösen Sie jetzt eine Sicherung aus, anstatt zu warten, bis die Sicherung gemäß Standardzeitplan (05:00 Uhr UTC) ausgelöst wird: 

   ```azurecli
   az backup protection backup-now \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c 
   ```

   Mit `az backup job list` und `az backup job show` können Sie den Status des Sicherungsauftrags überwachen.

---

## <a name="recovery"></a>Wiederherstellung

Führen Sie die folgenden Schritte aus, um die Datenbank wiederherzustellen:

1. Entfernen Sie die Datenbankdateien.
1. Generieren Sie ein Wiederherstellungsskript aus dem Recovery Services-Tresor.
1. Stellen Sie den Wiederherstellungspunkt bereit.
1. Führen Sie die Wiederherstellung aus.

### <a name="remove-the-database-files"></a>Entfernen der Datenbankdateien 

Weiter unten in diesem Artikel erfahren Sie, wie der Wiederherstellungsprozess getestet werden kann. Bevor Sie den Wiederherstellungsprozess testen können, müssen Sie die Datenbankdateien entfernen.

1.  Fahren Sie die Oracle-Instanz herunter:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  Entfernen Sie die Datendateien und Sicherungen:

    ```bash
    sudo su - oracle
    cd /u02/oradata/TEST
    rm -f *.dbf
    cd /u02/fast_recovery_area
    rm -f *
    ```

### <a name="generate-a-restore-script-from-the-recovery-services-vault"></a>Generieren eines Wiederherstellungsskripts aus dem Recovery Services-Tresor

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wechseln Sie im Azure-Portal im Recovery Services-Tresor zum Element *myVault*, und wählen Sie es aus.

    ![myVault-Sicherungselemente für Recovery Services-Tresore](./media/oracle-backup-recovery/recovery-service-06.png)

2. Wählen Sie auf dem Blatt **Übersicht** die Option **Sicherungselemente** und anschließend die Option **Virtueller Azure-Computer** aus, die für „Anzahl von Sicherungselementen“ nicht 0 enthalten sollte.

    ![Anzahl von Sicherungselementen auf virtuellem Azure-Computer für Recovery Services-Tresore](./media/oracle-backup-recovery/recovery-service-07.png)

3. Auf der Seite „Sicherungselemente (Azure Virtual Machines)“ ist Ihre VM **vmoracle19c** aufgeführt. Klicken Sie rechts auf die Auslassungspunkte, um das Menü einzublenden, und wählen Sie **Dateiwiederherstellung** aus.

    ![Screenshot der Seite zur Dateiwiederherstellung für Recovery Services-Tresore](./media/oracle-backup-recovery/recovery-service-08.png)

4. Klicken Sie im Bereich **Dateiwiederherstellung (Vorschauversion)** auf **Skript herunterladen**. Speichern Sie dann die heruntergeladene Datei (.py) in einem Ordner auf dem Clientcomputer. Zum Ausführen des Skripts wird ein Kennwort generiert. Kopieren Sie das Kennwort zur späteren Verwendung in eine Datei. 

    ![Speichern von Optionen durch Herunterladen der Skriptdatei](./media/oracle-backup-recovery/recovery-service-09.png)

5. Kopieren Sie die PY-Datei auf die VM.

    Im folgenden Beispiel wird dargestellt, wie Sie die Datei mit einem SCP-Befehl (Secure Copy) auf den virtuellen Computer verschieben. Sie können die Inhalte auch in die Zwischenablage kopieren und sie anschließend in eine neue Datei einfügen, die auf dem virtuellen Computer eingerichtet ist.

    > [!IMPORTANT]
    > Stellen Sie im folgenden Beispiel sicher, dass Sie die IP-Adresse und die Ordnerwerte aktualisiert haben. Die Werte müssen dem Ordner zugeordnet werden können, in dem die Datei gespeichert ist.
    >

    ```bash
    $ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
    ```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie zum Auflisten der Wiederherstellungspunkte für Ihre VM „az backup recovery point list“ aus. In diesem Beispiel wird der letzte Wiederherstellungspunkt für den virtuellen Computer mit dem Namen myVM ausgewählt, der in myRecoveryServicesVault geschützt wird:

```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
```

Um das Skript abzurufen, das eine Verbindung des Wiederherstellungspunkts mit Ihrer VM herstellt bzw. ihn dort einbindet, verwenden Sie az backup restore files mount-rp. Im folgenden Beispiel wird das Skript für den virtuellen Computer mit dem Namen myVM abgerufen, der in myRecoveryServicesVault geschützt wird.

Ersetzen Sie myRecoveryPointName mit dem Namen des Wiederherstellungspunkts, den Sie im vorherigen Befehl abgerufen haben:

```azurecli
   az backup restore files mount-rp \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --rp-name myRecoveryPointName
```

Das Skript wird heruntergeladen, und ein Kennwort wird wie im folgenden Beispiel angezeigt:

```bash
   File downloaded: vmoracle19c_eus_4598131610710119312_456133188157_6931c635931f402eb543ee554e1cf06f102c6fc513d933.py. Use password c4487e40c760d29
```

Kopieren Sie die PY-Datei auf die VM.

Im folgenden Beispiel wird dargestellt, wie Sie die Datei mit einem SCP-Befehl (Secure Copy) auf den virtuellen Computer verschieben. Sie können die Inhalte auch in die Zwischenablage kopieren und sie anschließend in eine neue Datei einfügen, die auf dem virtuellen Computer eingerichtet ist.

> [!IMPORTANT]
> Stellen Sie im folgenden Beispiel sicher, dass Sie die IP-Adresse und die Ordnerwerte aktualisiert haben. Die Werte müssen dem Ordner zugeordnet werden können, in dem die Datei gespeichert ist.
>

```bash
$ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
```
---

### <a name="mount-the-restore-point"></a>Bereitstellen des Wiederherstellungspunkts

1. Erstellen Sie einen Bereitstellungspunkt für die Wiederherstellung, und kopieren Sie das Skript in diesen.

    Im folgenden Beispiel erstellen Sie das Verzeichnis */restore*, in dem die Momentaufnahme bereitgestellt werden soll. Verschieben die Datei in das Verzeichnis, und ändern die Datei so, dass sie dem Benutzer „root“ gehört und ausführbar wird.

    ```bash 
    ssh azureuser@<publicIpAddress>
    sudo su -
    mkdir /restore
    chmod 777 /restore
    cd /restore
    cp /tmp/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py /restore
    chmod 755 /restore/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```
    
    Führen Sie nun das Skript aus, um die Sicherung wiederherzustellen. Sie werden aufgefordert, das im Azure-Portal generierte Kennwort anzugeben. 
  
   ```bash
    ./vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```

    Im folgenden Beispiel wird dargestellt, was Ihnen nach der Ausführung des vorherigen Skripts angezeigt werden sollte. Geben Sie **Y** ein, wenn Sie zum Fortsetzen des Vorgangs aufgefordert werden.

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : b1ad68e16dfafc6

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sdc  |  /dev/sdc1  |  /restore/vmoracle19c-20201215123912/Volume1

    2)  | /dev/sdd  |  /dev/sdd1  |  /restore/vmoracle19c-20201215123912/Volume2

    3)  | /dev/sdd  |  /dev/sdd2  |  /restore/vmoracle19c-20201215123912/Volume3

    4)  | /dev/sdd  |  /dev/sdd15  |  /restore/vmoracle19c-20201215123912/Volume5

    The following partitions failed to mount since the OS couldn't identify the filesystem.

    ************ Volumes from unknown filesystem ************

    Sr.No.  |  Disk  |  Volume  |  Partition Type

    1)  | /dev/sdb  |  /dev/sdb14  |  BIOS Boot partition

    Please refer to '/restore/vmoracle19c-2020XXXXXXXXXX/Scripts/MicrosoftAzureBackupILRLogFile.log' for more details.

    ************ Open File Explorer to browse for files. ************

    After recovery, remove the disks and close the connection to the recovery point by clicking the 'Unmount Disks' button from the portal or by using the relevant unmount command in case of powershell or CLI.

    After unmounting disks, run the script with the parameter 'clean' to remove the mount paths of the recovery point from this machine.

    Please enter 'q/Q' to exit...
    ```

2. Der Zugriff auf die bereitgestellten Volumes wurde bestätigt.

    Geben Sie zum Beenden **q** ein, und suchen Sie anschließend nach den bereitgestellten Volumes. Geben Sie **df -h** ein, um an einer Eingabeaufforderung eine Liste mit den hinzugefügten Volumes zu erstellen.
    
    ```
    [root@vmoracle19c restore]# df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        3.8G     0  3.8G   0% /dev
    tmpfs           3.8G     0  3.8G   0% /dev/shm
    tmpfs           3.8G   17M  3.8G   1% /run
    tmpfs           3.8G     0  3.8G   0% /sys/fs/cgroup
    /dev/sdd2        30G  9.6G   18G  36% /
    /dev/sdb1       126G  736M  119G   1% /u02
    /dev/sda1       497M  199M  298M  41% /boot
    /dev/sda15      495M  9.7M  486M   2% /boot/efi
    tmpfs           771M     0  771M   0% /run/user/54322
    /dev/sdc1       126G  2.9G  117G   3% /restore/vmoracle19c-20201215123912/Volume1
    /dev/sdd1       497M  199M  298M  41% /restore/vmoracle19c-20201215123912/Volume2
    /dev/sdd2        30G  9.6G   18G  36% /restore/vmoracle19c-20201215123912/Volume3
    /dev/sdd15      495M  9.7M  486M   2% /restore/vmoracle19c-20201215123912/Volume5
    ```

### <a name="perform-recovery"></a>Ausführen der Wiederherstellung

1. Kopieren Sie die fehlenden Sicherungsdateien zurück in die Fast Recovery Area:

    ```bash
    cd /restore/vmoracle19c-2020XXXXXXXXXX/Volume1/fast_recovery_area/TEST
    cp * /u02/fast_recovery_area/TEST
    cd /u02/fast_recovery_area/TEST
    chown -R oracle:oinstall *
    ```

2. Bei den folgenden Befehlen wird RMAN verwendet, um die fehlenden Datendateien und die Datenbank wiederherzustellen:

    ```bash
    sudo su - oracle
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
3. Prüfen Sie, ob der Datenbankinhalt vollständig wiederhergestellt wurde:

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```

4. Heben Sie die Bereitstellung des Wiederherstellungspunkts auf.

   ```bash
   umount /restore/vmoracle19c-20210107110037/Volume*
   ```

    Klicken Sie im Azure-Portal auf dem Blatt **Dateiwiederherstellung (Vorschauversion)** auf **Bereitstellung der Datenträger aufheben**.

    ![Befehl zum Aufheben der Bereitstellung von Datenträgern](./media/oracle-backup-recovery/recovery-service-10.png)
    
    Sie können die Bereitstellung der Wiederherstellungsvolumes auch aufheben, indem Sie das Python-Skript mit der Option **-clean** erneut ausführen.

## <a name="restore-the-entire-vm"></a>Wiederherstellen des gesamten virtuellen Computers

Statt die aus den Recovery Services-Tresoren gelöschten Dateien wiederherzustellen, können Sie den gesamten virtuellen Computer wiederherstellen.

Führen Sie diese Schritte aus, um die gesamte VM wiederherzustellen:

1. Beenden und löschen Sie vmoracle19c.
1. Stellen Sie die VM wieder her.
1. Legen Sie die öffentliche IP-Adresse fest.
1. Stellen Sie eine Verbindung zur VM her.
1. Starten Sie die Datenbank in der Bereitstellungsphase, und führen Sie die Wiederherstellung aus.

### <a name="stop-and-delete-vmoracle19c"></a>Beenden und löschen von vmoracle19c

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wechseln Sie im Azure-Portal zur VM **vmoracle19c**, und wählen Sie dann **Beenden** aus.

1. Wenn die VM nicht mehr läuft, wählen Sie **Löschen** und dann **Ja** aus.

   ![Befehl „Löschen“ für Tresore](./media/oracle-backup-recovery/recover-vm-01.png)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Beenden Sie die VM, und heben Sie die Zuordnung auf:

    ```azurecli
    az vm deallocate --resource-group rg-oracle --name vmoracle19c
    ```

2. Löschen Sie den virtuellen Computer. Geben Sie bei Aufforderung „y“ ein:

    ```azurecli
    az vm delete --resource-group rg-oracle --name vmoracle19c
    ```

---

### <a name="recover-the-vm"></a>Wiederherstellen des virtuellen Computers

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Erstellen Sie ein Speicherkonto für den Stagingprozess im Azure-Portal.

   1. Wählen Sie im Azure-Portal **+ Ressource erstellen** aus. Suchen Sie nach **Speicherkonto**, und wählen Sie diese Option aus.
    
      ![Screenshot der Erstellung einer Ressource.](./media/oracle-backup-recovery/storage-1.png)
    
    
   1. Wählen Sie auf der Seite „Speicherkonto erstellen“ Ihre vorhandene Ressourcengruppe **rg-oracle** aus. Nennen Sie Ihr Speicherkonto **oracrestore**, und wählen Sie **Storage V2 (generalpurpose v2)** als „Kontotyp“. Ändern Sie „Replikation“ in **Lokal redundanter Speicher (LRS)** , und legen Sie „Leistung“ auf **Standard** fest. Stellen Sie sicher, dass der Standort auf die Region aller anderen Ressourcen in der Ressourcengruppe festgelegt ist. 
    
      ![Seite „Speicherkonto hinzufügen“](./media/oracle-backup-recovery/recovery-storage-1.png)
   
   1. Klicken Sie auf Überprüfen + erstellen und dann auf Erstellen.

2. Wechseln Sie im Azure-Portal im Recovery Services-Tresor zum Element *myVault*, und klicken Sie darauf.

    ![myVault-Sicherungselemente für Recovery Services-Tresore](./media/oracle-backup-recovery/recovery-service-06.png)
    
3.  Wählen Sie auf dem Blatt **Übersicht** die Option **Sicherungselemente** und anschließend die Option **Virtueller Azure-Computer** aus, die für „Anzahl von Sicherungselementen“ nicht 0 enthalten sollte.

    ![Anzahl von Sicherungselementen auf virtuellem Azure-Computer für Recovery Services-Tresore](./media/oracle-backup-recovery/recovery-service-07.png)

4.  Auf der Seite „Sicherungselemente (Azure Virtual Machines)“ ist Ihre VM **vmoracle19c** aufgeführt. Klicken Sie auf den Namen der VM.

    ![Seite zur Wiederherstellung des virtuellen Computers](./media/oracle-backup-recovery/recover-vm-02.png)

5.  Wählen Sie auf dem Blatt **vmoracle19c** einen Wiederherstellungspunkt mit dem Konsistenztyp **Anwendungskonsistent**, und klicken Sie auf die Auslassungspunkte ( **...** ) auf der rechten Seite, um das Menü einzublenden.  Klicken Sie im Menü auf **VM wiederherstellen**.

    ![Befehl „Virtuellen Computer wiederherstellen“](./media/oracle-backup-recovery/recover-vm-03.png)

6.  Wählen Sie auf dem Blatt **Virtuellen Computer wiederherstellen** die Optionen **Neu erstellen** und **Neuen virtuellen Computer erstellen**. Geben Sie den VM-Namen **vmoracle19c** ein, und wählen Sie das VNet **vmoracle19cVNET**. Das Subnetz wird anhand Ihrer VNet-Auswahl automatisch für Sie ausgefüllt. Der Prozess der VM-Wiederherstellung erfordert ein Azure-Speicherkonto in derselben Ressourcengruppe und Region. Sie können das Speicherkonto **orarestore** wählen, das Sie zuvor eingerichtet haben.

    ![Werte der Wiederherstellungskonfiguration](./media/oracle-backup-recovery/recover-vm-04.png)

7.  Klicken Sie auf die Schaltfläche **Wiederherstellen**, um die VM wiederherzustellen.

8.  Klicken Sie auf **Aufträge** und dann auf **Sicherungsaufträge**, um den Status des Wiederherstellungsprozesses anzuzeigen.

    ![Befehl „Status des Sicherungsauftrags“](./media/oracle-backup-recovery/recover-vm-05.png)

    Klicken Sie auf den Wiederherstellungsvorgang **In Bearbeitung**, um den Status des Wiederherstellungsprozesses anzuzeigen:

    ![Status des Wiederherstellungsprozesses](./media/oracle-backup-recovery/recover-vm-06.png)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie zum Einrichten von Speicherkonto und Dateifreigabe die folgenden Befehle in der Azure CLI aus.

1. Erstellen Sie das Speicherkonto entsprechend der Ressourcengruppe und Region Ihrer VM:

   ```azurecli
   az storage account create -n orarestore -g rg-oracle -l eastus --sku Standard_LRS
   ```

2. Rufen Sie die Liste der verfügbaren Wiederherstellungspunkte ab. 

   ```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
   ```

3. Stellen Sie den Wiederherstellungspunkt im Speicherkonto wieder her. Ersetzen Sie `<myRecoveryPointName>` durch einen Wiederherstellungspunkt aus der im vorherigen Schritt generierten Liste:

   ```azurecli
   az backup restore restore-disks \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --storage-account orarestore \
      --rp-name <myRecoveryPointName> \
      --target-resource-group rg-oracle
   ```

4. Rufen Sie die Details des Wiederherstellungsauftrags ab. Der folgende Befehl ruft weitere Details zum ausgelösten Wiederherstellungsauftrag ab, einschließlich des Namens, der zum Abrufen des Vorlagen-URI benötigt wird. 

   ```azurecli
   az backup job list \
       --resource-group rg-oracle \
       --vault-name myVault \
       --output table
   ```

   Die Ausgabe sieht etwa wie `(Note down the name of the restore job)` aus:

   ```output
   Name                                  Operation        Status     Item Name    Start Time UTC                    Duration
   ------------------------------------  ---------------  ---------  -----------  --------------------------------  --------------
   c009747a-0d2e-4ac9-9632-f695bf874693  Restore          Completed  vmoracle19c  2021-01-10T21:46:07.506223+00:00  0:03:06.634177
   6b779c98-f57a-4db1-b829-9e8eab454a52  Backup           Completed  vmoracle19c  2021-01-07T10:11:15.784531+00:00  0:21:13.220616
   502bc7ae-d429-4f0f-b78e-51d41b7582fc  ConfigureBackup  Completed  vmoracle19c  2021-01-07T09:43:55.298755+00:00  0:00:30.839674
   ```

5. Ruft die Details des URI ab, der für die Neuerstellung der VM verwendet werden soll. Ersetzen Sie den Namen des Wiederherstellungsauftrags aus dem vorherigen Schritt durch `<RestoreJobName>`.

    ```azurecli
      az backup job show \
        -v myVault \
        -g rg-oracle \
        -n <RestoreJobName> \
        --query properties.extendedInfo.propertyBag
    ```

   Die Ausgabe ist in etwa wie folgt:

   ```output
   {
   "Config Blob Container Name": "vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2",
   "Config Blob Name": "config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Config Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Job Type": "Recover disks",
   "Recovery point time ": "1/7/2021 10:11:19 AM",
   "Target Storage Account Name": "orarestore",
   "Target resource group": "rg-oracle",
   "Template Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json"
   }
   ```

   Der Name der Vorlage, der sich am Ende des Vorlagenblob-URI befindet und in diesem Beispiel `azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json` lautet, und der Name des Blobcontainers, der `vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2` lautet, werden aufgeführt. 

   Weisen Sie mit diesen Werten im folgenden Befehl Variablen als Vorbereitung auf die Erstellung der VM zu. Ein SAS-Schlüssel mit einer Gültigkeitsdauer von 30 Minuten wird für den Speichercontainer generiert.  


   ```azurecli
   expiretime=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
   connection=$(az storage account show-connection-string \
    --resource-group rg-oracle \
    --name orarestore \
    --query connectionString)
   token=$(az storage blob generate-sas \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
   url=$(az storage blob url \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --connection-string $connection \
    --output tsv)
   ```

   Stellen Sie nun die Vorlage bereit, um die VM zu erstellen.

   ```azurecli
   az deployment group create \
      --resource-group rg-oracle \
      --template-uri $url?$token
   ```

   Sie werden aufgefordert, einen Namen für die VM anzugeben.

---

### <a name="set-the-public-ip-address"></a>Festlegen der öffentlichen IP-Adresse

Nachdem die VM wiederhergestellt wurde, müssen Sie der neuen VM die ursprüngliche IP-Adresse erneut zuweisen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Wechseln Sie im Azure-Portal zu Ihrer VM **vmoracle19c**. Sie werden feststellen, dass ihr eine neue öffentliche IP-Adresse und NIC zugewiesen wurde, ähnlich wie vmoracle19c-nic-XXXXXXXXXXXX. Aber sie hat keine DNS-Adresse. Wenn die ursprüngliche VM gelöscht wurde, bleiben deren öffentliche IP-Adresse und NIC erhalten und werden in den nächsten Schritten der neuen VM wieder zugewiesen.

    ![Liste der öffentlichen IP-Adressen](./media/oracle-backup-recovery/create-ip-01.png)

2.  Beenden des virtuellen Computers

    ![Erstellen der IP-Adresse](./media/oracle-backup-recovery/create-ip-02.png)

3.  Navigieren Sie zu **Netzwerk**.

    ![Zuordnen einer IP-Adresse](./media/oracle-backup-recovery/create-ip-03.png)

4.  Klicken Sie auf **Netzwerkschnittstelle anfügen**. Wählen Sie die ursprüngliche NIC **vmoracle19cVMNic aus, der die ursprüngliche öffentliche IP-Adresse nach wie vor zugeordnet ist, und klicken Sie auf **OK**.

    ![Auswählen von Ressourcentyp und Netzschnittstellenwerten](./media/oracle-backup-recovery/create-ip-04.png)

5.  Nun müssen Sie die beim Wiederherstellungsvorgang der VM erstellte NIC trennen, da sie als primäre Schnittstelle konfiguriert ist. Klicken Sie auf **Netzwerkschnittstelle trennen**. Wählen Sie die neue NIC ähnlich wie **vmoracle19c-NIC-xxxxxxxxxxxx** aus, und klicken Sie dann auf **OK**.

    ![Screenshot, der zeigt, wo „Netzwerkschnittstelle trennen“ ausgewählt werden soll](./media/oracle-backup-recovery/create-ip-05.png)
    
    Ihre neu erstellte VM verfügt nun über die ursprüngliche NIC, die mit der ursprünglichen IP-Adresse und den Regeln der Netzwerksicherheitsgruppe verknüpft ist.
    
    ![Wert der IP-Adresse](./media/oracle-backup-recovery/create-ip-06.png)
    
6.  Kehren Sie zur **Übersicht** zurück, und klicken Sie auf **Starten**. 

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Beenden Sie die VM, und heben Sie die Zuordnung auf:

   ```azurecli
   az vm deallocate --resource-group rg-oracle --name vmoracle19c
   ```

2. Auflisten der aktuellen bei der Wiederherstellung generierten VM-NIC

   ```azurecli
   az vm nic list --resource-group rg-oracle --vm-name vmoracle19c
   ```

   Die Ausgabe sieht ähnlich aus wie diese, die den bei der Wiederherstellung generierten NIC-Namen als `vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf` auflistet.

   ```output
   {
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/rg-oracle/providers/Microsoft.Network/networkInterfaces/vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf",
    "primary": true,
    "resourceGroup": "rg-oracle"
   }
   ```

3. Fügen Sie die ursprüngliche NIC an, die einen Namen des Typs `<VMName>VMNic` haben sollte, in diesem Fall `vmoracle19cVMNic`. Die ursprüngliche öffentliche IP-Adresse ist nach wie vor an diese NIC angefügt und wird für die VM wiederhergestellt, sobald die NIC erneut angefügt wird. 

   ```azurecli
   az vm nic add --nics vmoracle19cVMNic --resource-group rg-oracle --vm-name vmoracle19c
   ```

4. Trennen der bei der Wiederherstellung generierten NIC

   ```azurecli
   az vm nic remove --nics vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf --resource-group rg-oracle --vm-name vmoracle19c
   ```

5. Starten Sie die VM:

   ```azurecli
   az vm start --resource-group rg-oracle --name vmoracle19c
   ```

---

### <a name="connect-to-the-vm"></a>Herstellen der Verbindung zur VM

Verwenden Sie folgendes Skript, um eine Verbindung zum virtuellen Computer herzustellen:

```azurecli
ssh <publicIpAddress>
```

### <a name="start-the-database-to-mount-stage-and-perform-recovery"></a>Starten der Datenbank in der Bereitstellungsphase und Ausführen der Wiederherstellung

1. Es kann sein, dass die Instanz läuft, da beim Autostart versucht wurde, die Datenbank beim Hochfahren der VM zu starten. Da die Datenbank jedoch wiederhergestellt werden muss und sich wahrscheinlich nur in der Bereitstellungsphase befindet, erfolgt zunächst ein vorbereitendes Herunterfahren.

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> shutdown immediate
    SQL> startup mount
    SQL> recover automatic database;
    SQL> alter database open;
    ```
    
1. Prüfen Sie, ob der Datenbankinhalt wiederhergestellt wurde:

    ```bash
    SQL> select * from scott.scott_table;
    ```

Die Sicherung und Wiederherstellung der Oracle Database 19c-Datenbank auf einem virtuellen Azure Linux-Computer ist nun abgeschlossen.

## <a name="delete-the-vm"></a>Löschen der virtuellen Computer

Wenn Sie den virtuellen Computer nicht mehr benötigen, können Sie mit dem folgenden Befehl die Ressourcengruppe, den virtuellen Computer und alle zugehörigen Ressourcen entfernen:

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>Nächste Schritte

[Tutorial: Erstellen von hoch verfügbaren virtuellen Computern](../../linux/create-cli-complete.md)

[Erkunden der Azure CLI-Beispiele für die Bereitstellung virtueller Computer](../../linux/cli-samples.md)
