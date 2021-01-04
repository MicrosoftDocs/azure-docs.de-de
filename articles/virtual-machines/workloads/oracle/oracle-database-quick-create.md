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
ms.openlocfilehash: 6468acb598cee26c46b62d64c748f0e393f27271
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967957"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Erstellen einer Oracle-Datenbank auf einem virtuellem Azure-Computer

Diese Anleitung enthält Details zur Verwendung der Azure CLI zum Bereitstellen eines virtuellen Azure-Computers aus einem [Image des Marketplace-Katalogs für Oracle](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview), um eine Oracle 12c-Datenbank zu erstellen. Sobald der Server bereitgestellt wird, werden Sie zum Konfigurieren der Oracle-Datenbank über SSH verbunden. 

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für diesen Schnellstart die Azure CLI-Version 2.0.4 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. 

Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers

Verwenden Sie zum Erstellen eines virtuellen Computers (VM) den Befehl [az vm create](/cli/azure/vm). 

Im folgenden Beispiel wird ein virtueller Computer namens `myVM` erstellt. Darüber hinaus werden SSH-Schlüssel erstellt, falls sie nicht bereits an einem Standardschlüsselspeicherort vorhanden sind. Um einen bestimmten Satz von Schlüsseln zu verwenden, nutzen Sie die Option `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

Nach der Erstellung des virtuellen Computers zeigt die Azure CLI Informationen an, die den Informationen im folgenden Beispiel ähneln. Beachten Sie den Wert für `publicIpAddress`. Diese Adresse wird verwendet, um auf den virtuellen Computer zuzugreifen.

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-the-vm"></a>Herstellen der Verbindung zur VM

Erstellen Sie mit dem folgenden Befehl eine SSH-Sitzung mit dem virtuellen Computer. Ersetzen Sie die IP-Adresse durch den Wert für `publicIpAddress` Ihres virtuellen Computers.

```bash
ssh azureuser@<publicIpAddress>
```

## <a name="create-the-database"></a>Erstellen der Datenbank

Die Oracle-Software ist bereits im Marketplace-Image installiert. Erstellen Sie wie folgt eine Beispieldatenbank. 

1.  Wechseln Sie zum *oracle*-Benutzer, und starten Sie den Oracle-Listener:

    ```bash
    $ sudo -su oracle
    $ lsnrctl start
    ```

    Die Ausgabe sieht in etwa wie folgt aus:

    ```output
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```
2. Erstellen eines Datenverzeichnisses für die Oracle-Datendateien

    ```bash
        mkdir /u01/app/oracle/oradata
    ```

3.  Erstellen Sie die Datenbank:

    ```bash
    dbca -silent \
           -createDatabase \
           -templateName General_Purpose.dbc \
           -gdbname cdb1 \
           -sid cdb1 \
           -responseFile NO_VALUE \
           -characterSet AL32UTF8 \
           -sysPassword OraPasswd1 \
           -systemPassword OraPasswd1 \
           -createAsContainerDatabase true \
           -numberOfPDBs 1 \
           -pdbName pdb1 \
           -pdbAdminPassword OraPasswd1 \
           -databaseType MULTIPURPOSE \
           -automaticMemoryManagement false \
           -storageType FS \
           -datafileDestination "/u01/app/oracle/oradata/" \
           -ignorePreReqs
    ```

    Es dauert einige Minuten, bis die Datenbank erstellt wurde.

    Es wird eine Ausgabe angezeigt, die in etwa wie folgt aussieht:

    ```output
        Copying database files
        1% complete
        2% complete
        8% complete
        13% complete
        19% complete
        27% complete
        Creating and starting Oracle instance
        29% complete
        32% complete
        33% complete
        34% complete
        38% complete
        42% complete
        43% complete
        45% complete
        Completing Database Creation
        48% complete
        51% complete
        53% complete
        62% complete
        70% complete
        72% complete
        Creating Pluggable Databases
        78% complete
        100% complete
        Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for further details.
    ```

4. Festlegen von Oracle-Variablen

    Bevor Sie eine Verbindung herstellen, müssen Sie zwei Umgebungsvariablen festlegen: *ORACLE_HOME* und *ORACLE_SID*.

    ```bash
        ORACLE_SID=cdb1; export ORACLE_SID
    ```

    Sie können ORACLE_HOME- und ORACLE_SID-Variablen auch der BASHRC-Datei hinzufügen. So werden die Umgebungsvariablen für zukünftige Anmeldungen gespeichert. Bestätigen Sie mithilfe des Editors Ihrer Wahl, dass die folgenden Anweisungen zur `~/.bashrc`-Datei hinzugefügt wurden.

    ```bash
    # Add ORACLE_SID. 
    export ORACLE_SID=cdb1 
    ```

## <a name="oracle-em-express-connectivity"></a>Konnektivität mit Oracle EM Express

Damit ein GUI-Verwaltungstool verwendet werden kann, um die Datenbank zu untersuchen, müssen Sie Oracle EM Express einrichten. Um eine Verbindung mit Oracle EM Express herstellen zu können, muss zuerst der Port in Oracle konfiguriert werden. 

1. Stellen Sie mit sqlplus eine Verbindung mit Ihrer Datenbank her:

    ```bash
    sqlplus / as sysdba
    ```

2. Legen Sie nach der Verbindungsherstellung Port 5502 für EM Express fest:

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. Öffnen Sie den Container PDB1, falls er noch nicht geöffnet wurde, aber überprüfen Sie zuerst den Status:

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    Die Ausgabe sieht in etwa wie folgt aus:

    ```output
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. Führen Sie die folgenden Befehle aus, um PDB1 zu öffnen, wenn der OPEN_MODE für `PDB1` nicht auf READ WRITE festgelegt ist:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

Sie müssen `quit` zum Beenden der sqlplus-Sitzung und `exit` zum Abmelden des Oracle-Benutzers eingeben.

## <a name="automate-database-startup-and-shutdown"></a>Automatisieren des Startens und Beendens der Datenbank

Die Oracle-Datenbank wird standardmäßig nicht automatisch gestartet, wenn Sie den virtuellen Computer neu starten. Melden Sie sich zuerst als Root-Benutzer an, um für die Oracle-Datenbank das automatische Starten einzurichten. Erstellen und aktualisieren Sie anschließend einige Systemdateien.

1. Anmelden als Root-Benutzer

    ```bash
    sudo su -
    ```

2.  Bearbeiten Sie die Datei `/etc/oratab` mit Ihrem bevorzugten Editor, und ändern Sie den Standard `N` in `Y`.

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Erstellen Sie eine Datei namens `/etc/init.d/dbora`, und fügen Sie die folgende Inhalte ein:

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
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

## <a name="open-ports-for-connectivity"></a>Öffnen der Ports für Konnektivität

Abschließend müssen einige externe Endpunkte konfiguriert werden. Beenden Sie zuerst Ihre SSH-Sitzung auf der VM (beim Neustart im vorherigen Schritt sollte eine Beendigung von SSH durchgeführt worden sein), um die Azure-Netzwerksicherheitsgruppe einzurichten, mit der die VM geschützt wird. 

1.  Erstellen Sie zum Öffnen des Endpunkts, den Sie für den Remotezugriff auf die Oracle-Datenbank verwenden, wie folgt mit [az network nsg rule create](/cli/azure/network/nsg/rule) eine Netzwerksicherheitsgruppen-Regel: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Erstellen Sie zum Öffnen des Endpunkts, den Sie für den Remotezugriff auf Oracle EM Express verwenden, wie folgt mit [az network nsg rule create](/cli/azure/network/nsg/rule) eine Netzwerksicherheitsgruppen-Regel:

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. Beschaffen Sie bei Bedarf erneut wie folgt die öffentliche IP-Adresse Ihrer VM, indem Sie [az network public-ip show](/cli/azure/network/public-ip) verwenden:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Stellen Sie eine Verbindung mit EM Express aus Ihrem Browser her. Stellen Sie sicher, dass Ihr Browser mit EM Express kompatibel ist (Eine Flash-Installation ist erforderlich). 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

Sie können sich mit dem **SYS**-Konto anmelden und das Kontrollkästchen **as sysdba** aktivieren. Verwenden Sie das Kennwort **OraPasswd1**, das Sie während der Installation festgelegt haben. 

![Screenshot der Anmeldeseite für Oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Untersuchung Ihrer ersten Oracle-Datenbank unter Azure abgeschlossen haben und die VM nicht länger benötigt wird, können Sie den Befehl [az group delete](/cli/azure/group) verwenden, um die Ressourcengruppe, die VM und alle dazugehörigen Ressourcen zu entfernen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über andere [Oracle-Lösungen in Azure](./oracle-overview.md). 

Probieren Sie das Tutorial [Set up Oracle ASM on an Azure Linux virtual machine](configure-oracle-asm.md) (Einrichten von Oracle ASM auf einem virtuellen Azure-Computer unter Linux) aus.
