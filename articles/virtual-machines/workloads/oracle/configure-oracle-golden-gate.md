---
title: Implementieren von Oracle Golden Gate in Azure-Linux-VMs | Microsoft-Dokumentation
description: Machen Sie Oracle Golden Gate in Ihrer Azure-Umgebung schnell einsatzbereit.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: 3fff58c240341776a3bb99c059c179cc4f9d96e9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999143"
---
# <a name="implement-oracle-golden-gate-on-an-azure-linux-vm"></a>Implementieren von Oracle Golden Gate in Azure-Linux-VMs 

Die Azure CLI dient zum Erstellen und Verwalten von Azure-Ressourcen über die Befehlszeile oder mit Skripts. In dieser Anleitung wird die Bereitstellung einer Oracle 12c-Datenbank mithilfe des Azure Marketplace-Katalogimages und der Azure CLI beschrieben. 

Dieses Dokument veranschaulicht schrittweise das Erstellen, Installieren und Konfigurieren von Oracle Golden Gate in einer Azure-VM. In diesem Tutorial werden zwei virtuelle Computer in einer Verfügbarkeitsgruppe in einer einzelnen Region eingerichtet. Über dasselbe Tutorial kann Oracle GoldenGate für VMs in verschiedenen Verfügbarkeitszonen in einer einzelnen Azure-Region oder für VMs in zwei verschiedenen Regionen einrichtet werden.

Bevor Sie beginnen, stellen Sie sicher, dass die Azure CLI installiert wurde. Weitere Informationen finden Sie im [Azure CLI-Installationshandbuch](/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Vorbereiten der Umgebung

Für die Installation von Oracle Golden Gate müssen Sie zwei Azure-VMs in derselben Verfügbarkeitsgruppe erstellen. Verwenden Sie zum Erstellen der VMs das Marketplace-Image **Oracle:Oracle-Database-Ee:12.1.0.2:latest**.

Sie müssen zudem mit dem Unix-Editor „vi“ und grundlegend mit X11 (X-Windows) vertraut sein.

Es folgt eine Übersicht über die Konfiguration der Umgebung:
> 
> |  | **Primärer Standort** | **Replikatstandort** |
> | --- | --- | --- |
> | **Oracle Release** |Oracle 12c Release 2 – (12.1.0.2) |Oracle 12c Release 2 – (12.1.0.2)|
> | **Computername** |myVM1 |myVM2 |
> | **Betriebssystem** |Oracle Linux 6.x |Oracle Linux 6.x |
> | **Oracle SID** |CDB1 |CDB1 |
> | **Replikationsschema** |TEST|TEST |
> | **Golden Gate-Besitzer/-Replikat** |C##GGADMIN |REPUSER |
> | **Golden Gate-Prozess** |EXTORA |REPORA|


### <a name="sign-in-to-azure"></a>Anmelden bei Azure 

Melden Sie sich mit dem Befehl [az login](/cli/azure/reference-index) bei Ihrem Azure-Abonnement an. Befolgen Sie dann die Anweisungen auf dem Bildschirm.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. 

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen `myResourceGroup` am Standort `westus` erstellt.

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Verfügbarkeitsgruppe erstellen

Der folgende Schritt ist optional, wird aber empfohlen. Weitere Informationen finden Sie im [Leitfaden zu Azure-Verfügbarkeitsgruppen](/previous-versions/azure/virtual-machines/windows/infrastructure-example).

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie mit dem Befehl [az vm create](/cli/azure/vm) einen virtuellen Computer. 

Im folgenden Beispiel werden zwei VMs (`myVM1` und `myVM2`) erstellt. Erstellen Sie SSH-Schlüssel, falls sie nicht bereits an einem Standardschlüsselspeicherort vorhanden sind. Um einen bestimmten Satz von Schlüsseln zu verwenden, nutzen Sie die Option `--ssh-key-value`.

#### <a name="create-myvm1-primary"></a>Erstellen von myVM1 (primär):

```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Nach dem Erstellen der VM zeigt die Azure CLI ähnliche Informationen wie im folgenden Beispiel an. (Notieren Sie sich den Wert von `publicIpAddress`. Diese Adresse wird verwendet, um auf die VM zuzugreifen.)

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

#### <a name="create-myvm2-replicate"></a>Erstellen von myVM2 (Replikat):

```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Notieren Sie auch den Wert von `publicIpAddress`, nachdem er erstellt wurde.

### <a name="open-the-tcp-port-for-connectivity"></a>Öffnen des TCP-Ports für Verbindungen

Der nächste Schritt besteht im Konfigurieren externer Endpunkte, die den Remotezugriff auf die Oracle-Datenbank ermöglichen. Um die externen Endpunkte zu konfigurieren, führen Sie die folgenden Befehle aus.

#### <a name="open-the-port-for-myvm1"></a>Öffnen des Ports für myVM1:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

Die Ergebnisse sollten etwa wie folgt aussehen:

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

#### <a name="open-the-port-for-myvm2"></a>Öffnen des Ports für myVM2:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>Verbinden mit dem virtuellen Computer

Erstellen Sie mit dem folgenden Befehl eine SSH-Sitzung mit dem virtuellen Computer. Ersetzen Sie die IP-Adresse durch den Wert von `publicIpAddress` Ihres virtuellen Computers.

```bash
ssh <publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>Erstellen der Datenbank in myVM1 (primär)

Die Oracle-Software ist bereits auf dem Marketplace-Image installiert, daher umfasst der nächste Schritt das Installieren der Datenbank. 

Ausführen der Software als Superuser „oracle“

```bash
sudo su - oracle
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
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for more details.
```

Legen Sie die Variablen ORACLE_SID und ORACLE_HOME fest.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Optional können Sie ORACLE_HOME und ORACLE_SID zur BASHRC-Datei hinzufügen, damit diese Einstellungen für künftige Anmeldungen gespeichert werden:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Starten des Oracle-Listeners

```bash
$ lsnrctl start
```

### <a name="create-the-database-on-myvm2-replicate"></a>Erstellen der Datenbank in myVM2 (Replikat)

```bash
sudo su - oracle
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

Legen Sie die Variablen ORACLE_SID und ORACLE_HOME fest.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Optional können Sie ORACLE_HOME und ORACLE_SID zur BASHRC-Datei hinzufügen, damit diese Einstellungen für künftige Anmeldungen gespeichert werden.

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Starten des Oracle-Listeners

```bash
$ sudo su - oracle
$ lsnrctl start
```

## <a name="configure-golden-gate"></a>Konfigurieren von Golden Gate 
Fügen Sie zum Konfigurieren von Golden Gate die Schritte in diesem Abschnitt aus.

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
Erzwingen Sie die Protokollierung, und stellen Sie sicher, dass mindestens eine Protokolldatei vorhanden ist.

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
SQL> ALTER SYSTEM set enable_goldengate_replication=true;
SQL> ALTER PLUGGABLE DATABASE PDB1 OPEN;
SQL> ALTER SESSION SET CONTAINER=PDB1;
SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
SQL> EXIT;
```

### <a name="download-golden-gate-software"></a>Herunterladen der Golden Gate-Software
Führen Sie zum Herunterladen der Oracle Golden Gate-Software die folgenden Schritte aus:

1. Laden Sie die Datei **fbo_ggs_Linux_x64_shiphome.zip** von der [Downloadseite von Oracle Golden Gate](https://www.oracle.com/technetwork/middleware/goldengate/downloads/index.html) herunter. Unter dem Downloadtitel **Oracle GoldenGate 12.x.x.x for Oracle Linux x86-64** sollte eine Gruppe von herunterzuladenden ZIP-Dateien vorhanden sein.

2. Nachdem Sie die ZIP-Dateien auf den Clientcomputer heruntergeladen haben, können Sie die Dateien mit Secure Copy Protocol (SCP) auf den virtuellen Computer kopieren:

   ```bash
   $ scp fbo_ggs_Linux_x64_shiphome.zip <publicIpAddress>:<folder>
   ```

3. Verschieben Sie die ZIP-Dateien in den Ordner **/opt**. Ändern Sie dann den Besitzer der Dateien wie folgt:

   ```bash
   $ sudo su -
   # mv <folder>/*.zip /opt
   ```

4. Entzippen Sie die Dateien (installieren Sie das Linux-Hilfsprogramm zum Entpacken, sofern es noch nicht installiert ist):

   ```bash
   # yum install unzip
   # cd /opt
   # unzip fbo_ggs_Linux_x64_shiphome.zip
   ```

5. Ändern Sie die Berechtigung:

   ```bash
   # chown -R oracle:oinstall /opt/fbo_ggs_Linux_x64_shiphome
   ```

### <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>Vorbereiten des Clients und des virtuellen Computers für die Ausführung von X11 (nur für Windows-Clients)
Dieser Schritt ist optional. Sie können ihn überspringen, wenn Sie einen Linux-Client verwenden oder X11 bereits eingerichtet haben.

1. Laden Sie PuTTY und Xming auf Ihren Windows-Computer herunter:

   * Laden Sie [PuTTY](https://www.putty.org/) herunter.
   * Laden Sie [Xming](https://xming.en.softonic.com/) herunter.

2. Führen Sie nach der Installation von PuTTY im PuTTY-Order (z.B. „C:\Programme\PuTTY“) „puttygen.exe“ (PuTTY-Schlüsselgenerator) aus.

3. Im PuTTY-Schlüsselgenerator:

   - Um einen Schlüssel zu generieren, wählen Sie die Schaltfläche **Generate** (Generieren).
   - Kopieren Sie den Inhalt des Schlüssels (**STRG+C**).
   - Wählen Sie die Schaltfläche **Save private key** (Privaten Schlüssel speichern).
   - Ignorieren Sie die angezeigte Warnung, und wählen Sie dann **OK**.

   ![Screenshot der PuTTY-Schlüsselgeneratorseite](./media/oracle-golden-gate/puttykeygen.png)

4. Führen Sie auf dem virtuellen Computer die folgenden Befehle aus:

   ```bash
   # sudo su - oracle
   $ mkdir .ssh (if not already created)
   $ cd .ssh
   ```

5. Erstellen Sie eine Datei namens **authorized_keys**. Fügen Sie den Inhalt des Schlüssels in dieser Datei ein, und speichern Sie die Datei.

   > [!NOTE]
   > Der Schlüssel muss die Zeichenfolge `ssh-rsa` enthalten. Zudem muss der Inhalt des Schlüssels als einzelne Textzeile eingefügt werden.
   >  

6. Starten Sie PuTTY. Wählen Sie im Bereich **Category** (Kategorie) nacheinander **Connection** > **SSH** > **Auth** (Verbindung > SSH > Authentifizierung) aus. Navigieren Sie im Feld **Private key file for authentication** (Datei mit privatem Schlüssel für die Authentifizierung) zu dem zuvor erstellten Schlüssel.

   ![Screenshot der Seite zum Festlegen des privaten Schlüssels](./media/oracle-golden-gate/setprivatekey.png)

7. Wählen Sie im Bereich **Category** (Kategorie) nacheinander **Connection** > **SSH** > **X11** (Verbindung > SSH > X11) aus. Aktivieren Sie das Kontrollkästchen **Enable X11 forwarding** (X11-Weiterleitung aktivieren).

   ![Screenshot der Seite für die X11-Aktivierung](./media/oracle-golden-gate/enablex11.png)

8. Navigieren Sie im Bereich **Category** (Kategorie) zu **Session** (Sitzung). Geben Sie die Hostinformationen ein, und wählen Sie dann **Open** (Öffnen).

   ![Screenshot der Seite mit der Sitzung](./media/oracle-golden-gate/puttysession.png)

### <a name="install-golden-gate-software"></a>Installieren der Golden Gate-Software

Führen Sie zum Installieren von Oracle Golden Gate die folgenden Schritte aus:

1. Melden Sie sich als „oracle“ an. (Sie sollten sich ohne Aufforderung zur Kennworteingabe anmelden können.) Stellen Sie sicher, dass Xming ausgeführt wird, bevor Sie die Installation starten.

   ```bash
   $ cd /opt/fbo_ggs_Linux_x64_shiphome/Disk1
   $ ./runInstaller
   ```

2. Wählen Sie „Oracle GoldenGate for Oracle Database 12c“ aus. Klicken Sie dann auf **Weiter**, um fortzufahren.

   ![Screenshot der Seite „Select Installation“ (Installation auswählen) des Installationsprogramms](./media/oracle-golden-gate/golden_gate_install_01.png)

3. Ändern Sie den Speicherort der Software. Wählen Sie dann das Feld **Start Manager** (Manager starten) aus, und geben Sie den Speicherort der Datenbank ein. Klicken Sie auf **Weiter**, um fortzufahren.

   ![Screenshot der Seite „Select Installation“ (Installation auswählen)](./media/oracle-golden-gate/golden_gate_install_02.png)

4. Ändern Sie das Inventarverzeichnis, und klicken Sie dann auf **Weiter**, um fortzufahren.

   ![Screenshot: Seite „Select Installation“ (Installation auswählen) mit dem Installationsverzeichnis](./media/oracle-golden-gate/golden_gate_install_03.png)

5. Wählen Sie auf der **Summary** (Zusammenfassung) **Install** (Installieren) aus, um den Vorgang fortzusetzen.

   ![Screenshot: Seite „Select Installation“ (Installation auswählen) und die Schaltfläche „Install“ (Installieren)](./media/oracle-golden-gate/golden_gate_install_04.png)

6. Sie werden möglicherweise aufgefordert, ein Skript als „root“ auszuführen. Wenn dies der Fall ist, stellen Sie in einer separaten Sitzung über SSH eine Verbindung mit der VM her, wechseln Sie per „sudo“ zu „root“, und führen Sie dann das Skript aus. Klicken Sie auf **OK**, um fortzufahren.

   ![Screenshot: Skriptspeicherort und Ausführen des Konfigurationsskripts](./media/oracle-golden-gate/golden_gate_install_05.png)

7. Wenn die Installation abgeschlossen ist, klicken Sie auf **Schließen**, um den Vorgang abzuschließen.

   ![Screenshot: Seite „Select Installation“ (Installation auswählen) mit der Schaltfläche „Close“ (Schließen)](./media/oracle-golden-gate/golden_gate_install_06.png)

### <a name="set-up-service-on-myvm1-primary"></a>Einrichten des Diensts auf myVM1 (primär)

1. Erstellen oder aktualisieren Sie die Datei „tnsnames.ora“:

   ```bash
   $ cd $ORACLE_HOME/network/admin
   $ vi tnsnames.ora

   cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

   pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
   ```

2. Erstellen Sie den Golden Gate-Besitzer und Benutzerkonten.

   > [!NOTE]
   > Das Konto des Besitzers muss das Präfix „C##“ haben.
   >

    ```bash
    $ sqlplus / as sysdba
    SQL> CREATE USER C##GGADMIN identified by ggadmin;
    SQL> EXEC dbms_goldengate_auth.grant_admin_privilege('C##GGADMIN',container=>'ALL');
    SQL> GRANT DBA to C##GGADMIN container=all;
    SQL> connect C##GGADMIN/ggadmin
    SQL> ALTER SESSION SET CONTAINER=PDB1;
    SQL> EXIT;
    ```

3. Erstellen Sie das Golden Gate-Testbenutzerkonto:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ sqlplus system/OraPasswd1@pdb1
   SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
   SQL> GRANT connect, resource, dba TO test;
   SQL> ALTER USER test QUOTA 100M on USERS;
   SQL> connect test/test@pdb1
   SQL> @demo_ora_create
   SQL> @demo_ora_insert
   SQL> EXIT;
   ```

4. Konfigurieren Sie die EXTRACT-Parameterdatei.

   Starten Sie die Golden Gate-Befehlszeilen-Schnittstelle (ggsci):

   ```bash
   $ sudo su - oracle
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> DBLOGIN USERID test@pdb1, PASSWORD test
   Successfully logged into database  pdb1
   GGSCI>  ADD SCHEMATRANDATA pdb1.test
   2017-05-23 15:44:25  INFO    OGG-01788  SCHEMATRANDATA has been added on schema test.
   2017-05-23 15:44:25  INFO    OGG-01976  SCHEMATRANDATA for scheduling columns has been added on schema test.

   GGSCI> EDIT PARAMS EXTORA
   ```

5. Fügen Sie Folgendes (mithilfe von vi-Befehlen) der EXTRACT-Parameterdatei hinzu. Drücken Sie die ESC-Taste, und geben Sie „:wq!“ zum Speichern der Datei ein. 

   ```bash
   EXTRACT EXTORA
   USERID C##GGADMIN, PASSWORD ggadmin
   RMTHOST 10.0.0.5, MGRPORT 7809
   RMTTRAIL ./dirdat/rt  
   DDL INCLUDE MAPPED
   DDLOPTIONS REPORT 
   LOGALLSUPCOLS
   UPDATERECORDFORMAT COMPACT
   TABLE pdb1.test.TCUSTMER;
   TABLE pdb1.test.TCUSTORD;
   ```

6. Registrieren Sie „extract--integrated extract“:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci

   GGSCI> dblogin userid C##GGADMIN, password ggadmin
   Successfully logged into database CDB$ROOT.

   GGSCI> REGISTER EXTRACT EXTORA DATABASE CONTAINER(pdb1)

   2017-05-23 15:58:34  INFO    OGG-02003  Extract EXTORA successfully registered with database at SCN 1821260.

   GGSCI> exit
   ```

7. Richten Sie Extract-Prüfpunkte ein, und starten Sie den Extrahierungsvorgang in Echtzeit:

   ```bash
   $ ./ggsci
   GGSCI>  ADD EXTRACT EXTORA, INTEGRATED TRANLOG, BEGIN NOW
   EXTRACT (Integrated) added.

   GGSCI>  ADD RMTTRAIL ./dirdat/rt, EXTRACT EXTORA, MEGABYTES 10
   RMTTRAIL added.

   GGSCI>  START EXTRACT EXTORA

   Sending START request to MANAGER ...
   EXTRACT EXTORA starting

   GGSCI > info all

   Program     Status      Group       Lag at Chkpt  Time Since Chkpt

   MANAGER     RUNNING
   EXTRACT     RUNNING     EXTORA      00:00:11      00:00:04
   ```

   In diesem Schritt finden Sie den Start-SCN, der später in einem anderen Abschnitt verwendet wird:

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> SELECT current_scn from v$database;
   CURRENT_SCN
   -----------
      1857887
   SQL> EXIT;
   ```

   ```bash
   $ ./ggsci
   GGSCI> EDIT PARAMS INITEXT
   ```

   ```bash
   EXTRACT INITEXT
   USERID C##GGADMIN, PASSWORD ggadmin
   RMTHOST 10.0.0.5, MGRPORT 7809
   RMTTASK REPLICAT, GROUP INITREP
   TABLE pdb1.test.*, SQLPREDICATE 'AS OF SCN 1857887'; 
   ```

   ```bash
   GGSCI> ADD EXTRACT INITEXT, SOURCEISTABLE
   ```

### <a name="set-up-service-on-myvm2-replicate"></a>Einrichten des Diensts auf myVM2 (Replikat)


1. Erstellen oder aktualisieren Sie die Datei „tnsnames.ora“:

   ```bash
   $ cd $ORACLE_HOME/network/admin
   $ vi tnsnames.ora

   cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

   pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
   ```

2. Erstellen Sie ein Replikatkonto:

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> create user repuser identified by rep_pass container=current;
   SQL> grant dba to repuser;
   SQL> exec dbms_goldengate_auth.grant_admin_privilege('REPUSER',container=>'PDB1');
   SQL> connect repuser/rep_pass@pdb1 
   SQL> EXIT;
   ```

3. Erstellen Sie ein Golden Gate-Testbenutzerkonto:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ sqlplus system/OraPasswd1@pdb1
   SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
   SQL> GRANT connect, resource, dba TO test;
   SQL> ALTER USER test QUOTA 100M on USERS;
   SQL> connect test/test@pdb1
   SQL> @demo_ora_create
   SQL> EXIT;
   ```

4. REPLICAT-Parameterdatei zum Replizieren von Änderungen: 

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> EDIT PARAMS REPORA  
   ```

   Inhalt der Parameterdatei REPORA:

   ```bash
   REPLICAT REPORA
   ASSUMETARGETDEFS
   DISCARDFILE ./dirrpt/repora.dsc, PURGE, MEGABYTES 100
   DDL INCLUDE MAPPED
   DDLOPTIONS REPORT
   DBOPTIONS INTEGRATEDPARAMS(parallelism 6)
   USERID repuser@pdb1, PASSWORD rep_pass
   MAP pdb1.test.*, TARGET pdb1.test.*;
   ```

5. Richten Sie einen Replikatprüfpunkt ein:

   ```bash
   GGSCI> ADD REPLICAT REPORA, INTEGRATED, EXTTRAIL ./dirdat/rt
   GGSCI> EDIT PARAMS INITREP

   ```

   ```bash
   REPLICAT INITREP
   ASSUMETARGETDEFS
   DISCARDFILE ./dirrpt/tcustmer.dsc, APPEND
   USERID repuser@pdb1, PASSWORD rep_pass
   MAP pdb1.test.*, TARGET pdb1.test.*;   
   ```

   ```bash
   GGSCI> ADD REPLICAT INITREP, SPECIALRUN
   ```

### <a name="set-up-the-replication-myvm1-and-myvm2"></a>Einrichten der Replikation (myVM1 und myVM2)

#### <a name="1-set-up-the-replication-on-myvm2-replicate"></a>1. Einrichten der Replikation auf myVM2 (Replikat)

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> EDIT PARAMS MGR
  ```

Aktualisieren Sie die Datei mit dem folgenden Inhalt:

  ```bash
  PORT 7809
  ACCESSRULE, PROG *, IPADDR *, ALLOW
  ```

Starten Sie dann den Manager-Dienst neu:

  ```bash
  GGSCI> STOP MGR
  GGSCI> START MGR
  GGSCI> EXIT
  ```

#### <a name="2-set-up-the-replication-on-myvm1-primary"></a>2. Einrichten der Replikation auf myVM1 (primär)

Starten Sie die Anfangslast, und überprüfen Sie auf Fehler:

```bash
$ cd /u01/app/oracle/product/12.1.0/oggcore_1
$ ./ggsci
GGSCI> START EXTRACT INITEXT
GGSCI> VIEW REPORT INITEXT
```

#### <a name="3-set-up-the-replication-on-myvm2-replicate"></a>3. Einrichten der Replikation auf myVM2 (Replikat)

Ändern Sie den SCN-Wert durch den Wert, den Sie zuvor abgerufen haben:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  START REPLICAT REPORA, AFTERCSN 1857887
  ```

Die Replikation wurde gestartet, und Sie können sie testen, indem Sie in die TEST-Tabellen neue Datensätze einfügen.


### <a name="view-job-status-and-troubleshooting"></a>Anzeigen des Auftragsstatus und Problembehandlung

#### <a name="view-reports"></a>Berichte anzeigen
Um Berichte für myVM1 anzuzeigen, führen Sie die folgenden Befehle aus:

  ```bash
  GGSCI> VIEW REPORT EXTORA 
  ```
 
Um Berichte für myVM2 anzuzeigen, führen Sie die folgenden Befehle aus:

  ```bash
  GGSCI> VIEW REPORT REPORA
  ```

#### <a name="view-status-and-history"></a>Anzeigen des Status und Verlaufs
Um den Status und Verlauf für myVM1 anzuzeigen, führen Sie die folgenden Befehle aus:

  ```bash
  GGSCI> dblogin userid c##ggadmin, password ggadmin 
  GGSCI> INFO EXTRACT EXTORA, DETAIL
  ```

Um den Status und Verlauf für myVM2 anzuzeigen, führen Sie die folgenden Befehle aus:

  ```bash
  GGSCI> dblogin userid repuser@pdb1 password rep_pass 
  GGSCI> INFO REP REPORA, DETAIL
  ```
Damit ist die Installation und Konfiguration von Golden Gate unter Oracle Linux abgeschlossen.


## <a name="delete-the-virtual-machine"></a>Löschen des virtuellen Computers

Wenn die Ressourcengruppe, VM und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem folgenden Befehl entfernen.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

[Erstellen einer vollständigen Linux-Umgebung mit der Azure CLI 2.0](../../linux/create-cli-complete.md)

[Azure CLI-Beispiele für Linux-VMs](../../linux/cli-samples.md)
