---
title: Erstellen einer Oracle-Datenbank auf einem virtuellen Azure-Computern | Microsoft-Dokumentation
description: Richten Sie in Ihrer Azure-Umgebung schnell eine Oracle Database-12c-Datenbank ein.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: a202c8d176d6b9a8893a7bc5aaad6771942dda04
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063061"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Erstellen einer Oracle-Datenbank auf einem virtuellem Azure-Computer

Diese Anleitung enthält Details zur Verwendung der Azure CLI zum Bereitstellen eines virtuellen Azure-Computers aus einem [Image des Marketplace-Katalogs für Oracle](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview), um eine Oracle 19c-Datenbank zu erstellen. Sobald der Server bereitgestellt wird, werden Sie zum Konfigurieren der Oracle-Datenbank über SSH verbunden. 

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für diesen Schnellstart die Azure CLI-Version 2.0.4 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. 

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *rg-oracle* am Standort *eastus* erstellt.

```azurecli-interactive
az group create --name rg-oracle --location eastus
```

## <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers

Verwenden Sie zum Erstellen eines virtuellen Computers (VM) den Befehl [az vm create](/cli/azure/vm). 

Im folgenden Beispiel wird ein virtueller Computer namens `vmoracle19c` erstellt. Darüber hinaus werden SSH-Schlüssel erstellt, falls sie nicht bereits an einem Standardschlüsselspeicherort vorhanden sind. Um einen bestimmten Satz von Schlüsseln zu verwenden, nutzen Sie die Option `--ssh-key-value`.  

```azurecli-interactive 
az vm create ^
    --resource-group rg-oracle ^
    --name vmoracle19c ^
    --image Oracle:oracle-database-19-3:oracle-database-19-0904:latest ^
    --size Standard_DS2_v2 ^
    --admin-username azureuser ^
    --generate-ssh-keys ^
    --public-ip-address-allocation static ^
    --public-ip-address-dns-name vmoracle19c

```

Nach der Erstellung des virtuellen Computers zeigt die Azure CLI Informationen an, die den Informationen im folgenden Beispiel ähneln. Beachten Sie den Wert für `publicIpAddress`. Diese Adresse wird verwendet, um auf den virtuellen Computer zuzugreifen.

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/rg-oracle/providers/Microsoft.Compute/virtualMachines/vmoracle19c",
  "location": "eastus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "rg-oracle"
}
```
## <a name="create-and-attach-a-new-disk-for-oracle-datafiles-and-fra"></a>Erstellen und Anfügen eines neuen Datenträgers für Oracle-Datendateien und FRA

```bash
az vm disk attach --name oradata01 --new --resource-group rg-oracle --size-gb 128 --sku StandardSSD_LRS --vm-name vmoracle19c
```

## <a name="open-ports-for-connectivity"></a>Öffnen der Ports für Konnektivität
Bei dieser Aufgabe müssen Sie einige externe Endpunkte für den Datenbanklistener und für EM Express konfigurieren, indem Sie die Azure-Netzwerksicherheitsgruppe einrichten, mit der die VM geschützt wird. 

1. Erstellen Sie zum Öffnen des Endpunkts, den Sie für den Remotezugriff auf die Oracle-Datenbank verwenden, wie folgt eine Netzwerksicherheitsgruppen-Regel:
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle ^
       --protocol tcp ^
       --priority 1001 ^
       --destination-port-range 1521
   ```
2. Erstellen Sie zum Öffnen des Endpunkts, den Sie für den Remotezugriff auf Oracle EM Express verwenden, wie folgt mit az network nsg rule create eine Netzwerksicherheitsgruppen-Regel:
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle-EM ^
       --protocol tcp ^
       --priority 1002 ^
       --destination-port-range 5502
   ```
3. Beschaffen Sie bei Bedarf erneut wie folgt die öffentliche IP-Adresse Ihrer VM, indem Sie az network public-ip show verwenden:

   ```bash
   az network public-ip show ^
       --resource-group rg-oracle ^
       --name vmoracle19cPublicIP ^
       --query [ipAddress] ^
       --output tsv
   ```

## <a name="prepare-the-vm-environment"></a>Vorbereiten der VM-Umgebung

1. Herstellen der Verbindung zur VM

   Erstellen Sie mit dem folgenden Befehl eine SSH-Sitzung mit dem virtuellen Computer. Ersetzen Sie die IP-Adresse durch den Wert für `publicIpAddress` Ihres virtuellen Computers.

   ```bash
   ssh azureuser@<publicIpAddress>
   ```

2. Wechseln Sie zum root-Benutzer.

   ```bash
   sudo su -
   ```

3. Überprüfen Sie, welches Datenträgergerät zuletzt erstellt wurde und das von uns für Oracle-Datendateien formatiert werden soll.

   ```bash
   ls -alt /dev/sd*|head -1
   ```

   Es wird in etwa Folgendes ausgegeben:
   ```output
   brw-rw----. 1 root disk 8, 16 Dec  8 22:57 /dev/sdc
   ```

4. Formatieren Sie das Gerät. 
   Führen Sie als „root“-Benutzer den Befehl „parted“ auf dem Gerät aus. 
   
   Erstellen Sie zunächst eine Datenträgerbezeichnung:
   ```bash
   parted /dev/sdc mklabel gpt
   ```
   Erstellen Sie anschließend eine primäre Partition, die den gesamten Datenträger umfasst:
   ```bash
   parted -a optimal /dev/sdc mkpart primary 0GB 64GB   
   ```
   Überprüfen Sie abschließend die Gerätedetails, indem Sie die zugehörigen Metadaten ausgeben:
   ```bash
   parted /dev/sdc print
   ```
   Die Ausgabe sollte in etwa wie folgt aussehen:
   ```bash
   # parted /dev/sdc print
   Model: Msft Virtual Disk (scsi)
   Disk /dev/sdc: 68.7GB
   Sector size (logical/physical): 512B/4096B
   Partition Table: gpt
   Disk Flags:
   Number  Start   End     Size    File system  Name     Flags
    1      1049kB  64.0GB  64.0GB  ext4         primary
   ```

5. Erstellen Sie auf der Gerätepartition ein Dateisystem.

   ```bash
   mkfs -t ext4 /dev/sdc1
   ```

6. Erstellen eines Bereitstellungspunkts
   ```bash
   mkdir /u02
   ```

7. Einbinden des Datenträgers

   ```bash
   mount /dev/sdc1 /u02
   ```

8. Ändern Sie die Berechtigungen auf dem Bereitstellungspunkt.

   ```bash
   chmod 777 /u02
   ```

9. Fügen Sie die Bereitstellung der Datei „/etc/fstab“ hinzu. 

   ```bash
   echo "/dev/sdc1               /u02                    ext4    defaults        0 0" >> /etc/fstab
   ```
   
10. Aktualisieren Sie die Datei * **/etc/hosts** _ mit der öffentlichen IP-Adresse und dem Hostnamen.

    Ändern Sie _*_öffentliche IP-Adresse und VMname_*_ in Ihre Werte:
  
    ```bash
    echo "<Public IP> <VMname>.eastus.cloudapp.azure.com <VMname>" >> /etc/hosts
    ```
11. Aktualisieren Sie die Hostnamendatei.
    
    Verwenden Sie den folgenden Befehl, um den Domänennamen des virtuellen Computers der Datei _ */etc/hostname** hinzuzufügen. Hierbei wird vorausgesetzt, dass Sie Ihre Ressourcengruppe und den virtuellen Computer in der Region **eastus** erstellt haben:
    
    ```bash
    sed -i 's/$/\.eastus\.cloudapp\.azure\.com &/' /etc/hostname
    ```

12. Öffnen Sie die Firewallports.
    
    Da SELinux auf dem Marketplace-Image standardmäßig aktiviert ist, müssen wir die Firewall für Datenverkehr für den Datenbank-Überwachungsport 1521 und den Enterprise Manager Express-Port 5502 öffnen. Führen Sie die folgenden Befehle als „root“-Benutzer aus:

    ```bash
    firewall-cmd --zone=public --add-port=1521/tcp --permanent
    firewall-cmd --zone=public --add-port=5502/tcp --permanent
    firewall-cmd --reload
    ```
   

## <a name="create-the-database"></a>Erstellen der Datenbank

Die Oracle-Software ist bereits im Marketplace-Image installiert. Erstellen Sie wie folgt eine Beispieldatenbank. 

1.  Wechseln Sie zum Benutzer **oracle**:

    ```bash
    $ sudo su - oracle
    ```
2. Starten des Datenbanklisteners

   ```bash
   $ lsnrctl start
   ```
   Die Ausgabe sieht in etwa wie folgt aus:
  
   ```output
   LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 20-OCT-2020 01:58:18

   Copyright (c) 1991, 2019, Oracle.  All rights reserved.

   Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))

   Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
   STATUS of the LISTENER
   ------------------------
   Alias                     LISTENER
   Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Start Date                20-OCT-2020 01:58:18
   Uptime                    0 days 0 hr. 0 min. 0 sec
   Trace Level               off
   Security                  ON: Local OS Authentication
   SNMP                      OFF
   Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
   The listener supports no services
   The command completed successfully
   ```
3. Erstellen Sie ein Datenverzeichnis für die Oracle-Datendateien:

   ```bash
   mkdir /u02/oradata
   ```
    

3.  Führen Sie den Assistenten für die Datenbankerstellung aus:

    ```bash
    dbca -silent \
       -createDatabase \
       -templateName General_Purpose.dbc \
       -gdbname test \
       -sid test \
       -responseFile NO_VALUE \
       -characterSet AL32UTF8 \
       -sysPassword OraPasswd1 \
       -systemPassword OraPasswd1 \
       -createAsContainerDatabase false \
       -databaseType MULTIPURPOSE \
       -automaticMemoryManagement false \
       -storageType FS \
       -datafileDestination "/u02/oradata/" \
       -ignorePreReqs
    ```

    Es dauert einige Minuten, bis die Datenbank erstellt wurde.

    Es wird eine Ausgabe angezeigt, die in etwa wie folgt aussieht:

    ```output
        Prepare for db operation
       10% complete
       Copying database files
       40% complete
       Creating and starting Oracle instance
       42% complete
       46% complete
       50% complete
       54% complete
       60% complete
       Completing Database Creation
       66% complete
       69% complete
       70% complete
       Executing Post Configuration Actions
       100% complete
       Database creation complete. For details check the logfiles at: /u01/app/oracle/cfgtoollogs/dbca/test.
       Database Information:
       Global Database Name:test
       System Identifier(SID):test
       Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/test/test.log" for further details.
    ```

4. Festlegen von Oracle-Variablen

    Bevor Sie eine Verbindung herstellen, müssen Sie die Umgebungsvariable *ORACLE_SID* festlegen:

    ```bash
        export ORACLE_SID=test
    ```

    Außerdem müssen Sie die Variable ORACLE_SID für zukünftige Anmeldungen in der `.bashrc`-Datei für `oracle`-Benutzer hinzufügen, indem Sie den folgenden Befehl verwenden:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```

## <a name="oracle-em-express-connectivity"></a>Konnektivität mit Oracle EM Express

Damit ein GUI-Verwaltungstool verwendet werden kann, um die Datenbank zu untersuchen, müssen Sie Oracle EM Express einrichten. Um eine Verbindung mit Oracle EM Express herstellen zu können, muss zuerst der Port in Oracle konfiguriert werden. 

1. Stellen Sie mit sqlplus eine Verbindung mit Ihrer Datenbank her:

    ```bash
    sqlplus sys as sysdba
    ```

2. Legen Sie nach der Verbindungsherstellung Port 5502 für EM Express fest:

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3.  Stellen Sie eine Verbindung mit EM Express aus Ihrem Browser her. Stellen Sie sicher, dass Ihr Browser mit EM Express kompatibel ist (Eine Flash-Installation ist erforderlich). 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

    Sie können sich mit dem **SYS**-Konto anmelden und das Kontrollkästchen **as sysdba** aktivieren. Verwenden Sie das Kennwort **OraPasswd1**, das Sie während der Installation festgelegt haben. 

    ![Screenshot der Anmeldeseite für Oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="automate-database-startup-and-shutdown"></a>Automatisieren des Startens und Beendens der Datenbank

Die Oracle-Datenbank wird standardmäßig nicht automatisch gestartet, wenn Sie den virtuellen Computer neu starten. Melden Sie sich zuerst als Root-Benutzer an, um für die Oracle-Datenbank das automatische Starten einzurichten. Erstellen und aktualisieren Sie anschließend einige Systemdateien.

1. Anmelden als Root-Benutzer

    ```bash
    sudo su -
    ```

2.  Führen Sie den folgenden Befehl aus, um das Flag für den automatisierten Startvorgang in der Datei `/etc/oratab` von `N` in `Y` zu ändern:

    ```bash
    sed -i 's/:N/:Y/' /etc/oratab
    ```

3.  Erstellen Sie eine Datei namens `/etc/init.d/dbora`, und fügen Sie die folgende Inhalte ein:

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/19.0.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  Ändern Sie die Berechtigungen für Dateien wie folgt mit *chmod*:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Erstellen Sie wie folgt symbolische Verknüpfungen für das Starten und Beenden:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Starten Sie den virtuellen Computer neu, um die Änderungen zu testen:

    ```bash
    reboot
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Untersuchung Ihrer ersten Oracle-Datenbank unter Azure abgeschlossen haben und die VM nicht länger benötigt wird, können Sie den Befehl [az group delete](/cli/azure/group) verwenden, um die Ressourcengruppe, die VM und alle dazugehörigen Ressourcen zu entfernen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich damit vertraut, wie Sie Ihre Datenbank in Azure mit [Oracle-Sicherungsstrategien](./oracle-database-backup-strategies.md) schützen können.

Informieren Sie sich über andere [Oracle-Lösungen in Azure](./oracle-overview.md). 

Probieren Sie das Tutorial [Set up Oracle ASM on an Azure Linux virtual machine](configure-oracle-asm.md) (Einrichten von Oracle ASM auf einem virtuellen Azure-Computer unter Linux) aus.
