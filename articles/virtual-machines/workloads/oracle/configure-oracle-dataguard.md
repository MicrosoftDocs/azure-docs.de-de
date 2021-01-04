---
title: Implementieren von Oracle Data Guard auf einem virtuellen Azure-Linux-Computer | Microsoft-Dokumentation
description: Richten Sie in Ihrer Azure-Umgebung schnell Oracle Data Guard ein.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: cc7579b48307325e25d8914ea4c722a9641883f3
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952147"
---
# <a name="implement-oracle-data-guard-on-an-azure-linux-virtual-machine"></a>Implementieren von Oracle Data Guard auf einem virtuellen Azure-Linux-Computer 

Die Azure CLI dient zum Erstellen und Verwalten von Azure-Ressourcen über die Befehlszeile oder mit Skripts. In diesem Artikel wird beschrieben, wie Sie Skripts in der Azure CLI verwenden, um eine Oracle Database 12c-Datenbank aus einem Azure Marketplace-Image bereitzustellen. Anschließend wird in diesem Artikel Schritt für Schritt gezeigt, wie Sie Data Guard auf einem virtuellen Azure-Computer (VM) installieren und konfigurieren.

Stellen Sie sicher, dass die Azure CLI installiert ist, bevor Sie beginnen. Weitere Informationen finden Sie im [Azure CLI-Installationshandbuch](/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Vorbereiten der Umgebung
### <a name="assumptions"></a>Annahmen

Für die Installation von Oracle Data Guard müssen Sie zwei Azure-VMs in derselben Verfügbarkeitsgruppe erstellen:

- Der primäre virtuelle Computer („myVM1“) verfügt über eine ausgeführte Oracle-Instanz.
- Auf der Standby-VM („myVM2“) ist nur die Oracle-Software installiert.

Verwenden Sie zum Erstellen der VMs das Marketplace-Image „Oracle:Oracle-Database-Ee:12.1.0.2:latest“.

### <a name="sign-in-to-azure"></a>Anmelden bei Azure 

Melden Sie sich mit dem Befehl [az login](/cli/azure/reference-index) bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl [az group create](/cli/azure/group) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. 

Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen `myResourceGroup` am Standort `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Verfügbarkeitsgruppe erstellen

Die Erstellung einer Verfügbarkeitsgruppe ist optional, wird jedoch empfohlen. Weitere Informationen finden Sie im [Leitfaden zu Azure-Verfügbarkeitsgruppen](/previous-versions/azure/virtual-machines/windows/infrastructure-example).

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie mit dem Befehl [az vm create](/cli/azure/vm) eine VM. 

Im folgenden Beispiel werden zwei VMs (`myVM1` und `myVM2`) erstellt. Darüber hinaus werden SSH-Schlüssel erstellt, falls sie nicht bereits an einem Standardschlüsselspeicherort vorhanden sind. Um einen bestimmten Satz von Schlüsseln zu verwenden, nutzen Sie die Option `--ssh-key-value`.

Erstellen von myVM1 (primär):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --admin-username azureuser \
     --generate-ssh-keys \
```

Nach der Erstellung der VM zeigt die Azure CLI Informationen an, die den Informationen im folgenden Beispiel ähneln. Beachten Sie den Wert von `publicIpAddress`. Diese Adresse wird verwendet, um auf den virtuellen Computer zuzugreifen.

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

Erstellen Sie „myVM2“ (Standby):

```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --admin-username azureuser \
     --generate-ssh-keys \
```

Beachten Sie den Wert von `publicIpAddress`, nachdem Sie „myVM2“ erstellt haben.

### <a name="open-the-tcp-port-for-connectivity"></a>Öffnen des TCP-Ports für Verbindungen

Bei diesem Schritt werden externe Endpunkte konfiguriert, die Remotezugriff auf die Oracle-Datenbank ermöglichen.

Öffnen des Ports für myVM1:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVM1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

Das Ergebnis sollte etwa wie die folgende Antwort aussehen:

```output
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "1521",
  "direction": "Inbound",
  "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
  "name": "allow-oracle",
  "priority": 999,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

Öffnen des Ports für myVM2:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVM2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>Verbinden mit dem virtuellen Computer

Erstellen Sie mit dem folgenden Befehl eine SSH-Sitzung mit dem virtuellen Computer. Ersetzen Sie die IP-Adresse durch den Wert von `publicIpAddress` für Ihren virtuellen Computer.

```bash 
$ ssh azureuser@<publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>Erstellen der Datenbank in myVM1 (primär)

Die Oracle-Software ist bereits auf dem Marketplace-Image installiert, daher umfasst der nächste Schritt das Installieren der Datenbank. 

Wechseln Sie zum Oracle-Superbenutzer:

```bash
$ sudo su - oracle
```

Erstellen Sie die Datenbank:

```bash
$ dbca -silent \
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
   -ignorePreReqs
```

Die Ausgaben sollten etwa wie folgt aussehen:

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

Legen Sie die Variablen „ORACLE_SID“ und „ORACLE_HOME“ fest:

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
```

Optional können Sie „ORACLE_HOME“ und „ORACLE_SID“ zur Datei „/home/oracle/.bashrc“ hinzufügen, damit diese Einstellungen für zukünftige Anmeldungen gespeichert werden:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

## <a name="configure-data-guard"></a>Konfigurieren von Data Guard

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>Aktivieren des Protokollmodus ARCHIVELOG in myVM1 (primär)

```bash
$ sqlplus / as sysdba
SQL> SELECT log_mode FROM v$database;

LOG_MODE
------------
NOARCHIVELOG

SQL> SHUTDOWN IMMEDIATE;
SQL> STARTUP MOUNT;
SQL> ALTER DATABASE ARCHIVELOG;
SQL> ALTER DATABASE OPEN;
```

Aktivieren Sie die Erzwingung der Protokollierung, und stellen Sie sicher, dass mindestens eine Protokolldatei vorhanden ist:

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
```

Erstellen Sie die Standbywiederholungsprotokolle:

```bash
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo01.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo02.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo03.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo04.log') SIZE 50M;
```

Aktivieren Sie Flashback (um die Wiederherstellung deutlich zu vereinfachen), und legen Sie „STANDBY\_FILE\_MANAGEMENT“ auf „Auto“ fest. Beenden Sie danach SQL*Plus.

```bash
SQL> ALTER DATABASE FLASHBACK ON;
SQL> ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=AUTO;
SQL> EXIT;
```

### <a name="set-up-service-on-myvm1-primary"></a>Einrichten des Diensts auf myVM1 (primär)

Bearbeiten oder erstellen Sie die Datei „tnsnames.ora“ im Ordner „$ORACLE_HOME\network\admin“.

Fügen Sie die folgenden Einträge hinzu:

```bash
cdb1 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )

cdb1_stby =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )
```

Bearbeiten oder erstellen Sie die Datei „listener.ora“ im Ordner „$ORACLE_HOME\network\admin“.

Fügen Sie die folgenden Einträge hinzu:

```bash
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = cdb1_DGMGRL)
      (ORACLE_HOME = /u01/app/oracle/product/12.1.0/dbhome_1)
      (SID_NAME = cdb1)
    )
  )

ADR_BASE_LISTENER = /u01/app/oracle
```

Aktivieren Sie Data Guard Broker:

```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```

Starten Sie den Listener:

```bash
$ lsnrctl stop
$ lsnrctl start
```

### <a name="set-up-service-on-myvm2-standby"></a>Einrichten des Diensts auf „myVM2“ (Standby)

SSH-Verbindung mit „myVM2“:

```bash 
$ ssh azureuser@<publicIpAddress>
```

Melden Sie sich als „Oracle“ an:

```bash
$ sudo su - oracle
```

Bearbeiten oder erstellen Sie die Datei „tnsnames.ora“ im Ordner „$ORACLE_HOME\network\admin“.

Fügen Sie die folgenden Einträge hinzu:

```bash
cdb1 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )

cdb1_stby =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )
```

Bearbeiten oder erstellen Sie die Datei „listener.ora“ im Ordner „$ORACLE_HOME\network\admin“.

Fügen Sie die folgenden Einträge hinzu:

```bash
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = cdb1_DGMGRL)
      (ORACLE_HOME = /u01/app/oracle/product/12.1.0/dbhome_1)
      (SID_NAME = cdb1)
    )
  )

ADR_BASE_LISTENER = /u01/app/oracle
```

Starten Sie den Listener:

```bash
$ lsnrctl stop
$ lsnrctl start
```


### <a name="restore-the-database-to-myvm2-standby"></a>Wiederherstellen der Datenbank auf „myVM2“ (Standby)

Erstellen Sie die Parameterdatei „/tmp/initcdb1_stby.ora“ mit folgenden Inhalten:

```bash
*.db_name='cdb1'
```

Erstellen Sie die Ordner:

```bash
mkdir -p /u01/app/oracle/oradata/cdb1/pdbseed
mkdir -p /u01/app/oracle/oradata/cdb1/pdb1
mkdir -p /u01/app/oracle/fast_recovery_area/cdb1
mkdir -p /u01/app/oracle/admin/cdb1/adump
```

Erstellen Sie eine Kennwortdatei:

```bash
$ orapwd file=/u01/app/oracle/product/12.1.0/dbhome_1/dbs/orapwcdb1 password=OraPasswd1 entries=10
```

Starten Sie die Datenbank auf „myVM2“:

```bash
$ export ORACLE_SID=cdb1
$ sqlplus / as sysdba

SQL> STARTUP NOMOUNT PFILE='/tmp/initcdb1_stby.ora';
SQL> EXIT;
```

Stellen Sie die Datenbank mit dem RMAN-Tool wieder her:

```bash
$ rman TARGET sys/OraPasswd1@cdb1 AUXILIARY sys/OraPasswd1@cdb1_stby
```

Führen Sie die folgenden Befehle in RMAN aus:

```bash
DUPLICATE TARGET DATABASE
  FOR STANDBY
  FROM ACTIVE DATABASE
  DORECOVER
  SPFILE
    SET db_unique_name='CDB1_STBY' COMMENT 'Is standby'
  NOFILENAMECHECK;
```

Wenn der Befehl durchgeführt wird, sollten Sie Nachrichten sehen, die ähnlich wie die Folgenden aussehen. Beenden Sie RMAN.

```output
media recovery complete, elapsed time: 00:00:00
Finished recover at 29-JUN-17
Finished Duplicate Db at 29-JUN-17
```

```bash
RMAN> EXIT;
```

Optional können Sie „ORACLE_HOME“ und „ORACLE_SID“ zur Datei „/home/oracle/.bashrc“ hinzufügen, damit diese Einstellungen für zukünftige Anmeldungen gespeichert werden:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

Aktivieren Sie Data Guard Broker:
```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```

### <a name="configure-data-guard-broker-on-myvm1-primary"></a>Konfigurieren von Data Guard Broker auf „myVM1“ (primär)

Starten Sie Data Guard Manager, und melden Sie sich mithilfe von SYS und einem Kennwort an. (Verwenden Sie nicht die BS-Authentifizierung.) Führen Sie Folgendes aus:

```bash
$ dgmgrl sys/OraPasswd1@cdb1
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> CREATE CONFIGURATION my_dg_config AS PRIMARY DATABASE IS cdb1 CONNECT IDENTIFIER IS cdb1;
Configuration "my_dg_config" created with primary database "cdb1"
DGMGRL> ADD DATABASE cdb1_stby AS CONNECT IDENTIFIER IS cdb1_stby MAINTAINED AS PHYSICAL;
Database "cdb1_stby" added
DGMGRL> ENABLE CONFIGURATION;
Enabled.
```

Überprüfen Sie die Konfiguration:

```bash
DGMGRL> SHOW CONFIGURATION;

Configuration - my_dg_config

  Protection Mode: MaxPerformance
  Members:
  cdb1      - Primary database
    cdb1_stby - Physical standby database

Fast-Start Failover: DISABLED

Configuration Status:
SUCCESS   (status updated 26 seconds ago)
```

Sie haben die Einrichtung von Oracle Data Guard abgeschlossen. Im nächsten Abschnitt erfahren Sie, wie Sie die Konnektivität testen und wechseln.

### <a name="connect-the-database-from-the-client-machine"></a>Herstellen einer Verbindung mit der Datenbank über den Clientcomputer

Aktualisieren oder erstellen Sie auf Ihrem Clientcomputer die Datei „tnsnames.ora“. Diese Datei wird in der Regel im Verzeichnis „$ORACLE_HOME\network\admin“ gespeichert.

Ersetzen Sie die IP-Adressen durch Ihre `publicIpAddress`-Werte für „myVM1“ und „myVM2“:

```bash
cdb1=
  (DESCRIPTION=
    (ADDRESS=
      (PROTOCOL=TCP)
      (HOST=<myVM1 IP address>)
      (PORT=1521)
    )
    (CONNECT_DATA=
      (SERVER=dedicated)
      (SERVICE_NAME=cdb1)
    )
  )

cdb1_stby=
  (DESCRIPTION=
    (ADDRESS=
      (PROTOCOL=TCP)
      (HOST=<myVM2 IP address>)
      (PORT=1521)
    )
    (CONNECT_DATA=
      (SERVER=dedicated)
      (SERVICE_NAME=cdb1_stby)
    )
  )
```

Starten Sie SQL*Plus:

```bash
$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

## <a name="test-the-data-guard-configuration"></a>Testen der Data Guard-Konfiguration

### <a name="switch-over-the-database-on-myvm1-primary"></a>Wechseln der Datenbank auf „myVM1“ (primär)

So wechseln Sie von der primären zur Standby-Datenbank („cdb1“ zu „cdb1_stby“):

```bash
$ dgmgrl sys/OraPasswd1@cdb1
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> SWITCHOVER TO cdb1_stby;
Performing switchover NOW, please wait...
Operation requires a connection to instance "cdb1" on database "cdb1_stby"
Connecting to instance "cdb1"...
Connected as SYSDBA.
New primary database "cdb1_stby" is opening...
Operation requires start up of instance "cdb1" on database "cdb1"
Starting instance "cdb1"...
ORACLE instance started.
Database mounted.
Switchover succeeded, new primary is "cdb1_stby"
DGMGRL>
```

Sie können nun eine Verbindung mit der Standbydatenbank herstellen.

Starten Sie SQL*Plus:

```bash

$ sqlplus sys/OraPasswd1@cdb1_stby
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

### <a name="switch-over-the-database-on-myvm2-standby"></a>Wechseln der Datenbank auf „myVM2“ (Standby)

Führen Sie zum Wechseln Folgendes auf „myVM2“ aus:

```bash
$ dgmgrl sys/OraPasswd1@cdb1_stby
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> SWITCHOVER TO cdb1;
Performing switchover NOW, please wait...
Operation requires a connection to instance "cdb1" on database "cdb1"
Connecting to instance "cdb1"...
Connected as SYSDBA.
New primary database "cdb1" is opening...
Operation requires start up of instance "cdb1" on database "cdb1_stby"
Starting instance "cdb1"...
ORACLE instance started.
Database mounted.
Switchover succeeded, new primary is "cdb1"
```

Sie sollten nun wieder eine Verbindung mit der primären Datenbank herstellen können.

Starten Sie SQL*Plus:

```bash

$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

Sie haben die Installation und Konfiguration von Data Guard unter Oracle Linux beendet.


## <a name="delete-the-virtual-machine"></a>Löschen des virtuellen Computers

Wenn Sie den virtuellen Computer nicht mehr benötigen, können Sie mit dem folgenden Befehl die Ressourcengruppe, den virtuellen Computer und alle zugehörigen Ressourcen entfernen:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

[Tutorial: Erstellen hoch verfügbarer VMs](../../linux/create-cli-complete.md)

[Erkunden der Azure CLI-Beispiele für die Bereitstellung virtueller Computer](../../linux/cli-samples.md)
