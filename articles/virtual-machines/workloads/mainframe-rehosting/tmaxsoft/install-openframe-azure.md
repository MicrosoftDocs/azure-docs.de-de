---
title: Installieren von TmaxSoft OpenFrame auf Azure-VMs
description: Erfahren Sie, wie Sie eine OpenFrame-Umgebung in Azure einrichten, die sich für Entwicklungs-, Demo-, Test- und Produktionsworkloads eignet.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.openlocfilehash: ad3dbd8cdc6fff7eca8c24bb6e5258022d52cb9b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018493"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Install TmaxSoft OpenFrame on Azure (Installieren von TmaxSoft OpenFrame in Azure)

Erfahren Sie, wie Sie eine OpenFrame-Umgebung in Azure einrichten, die sich für Entwicklungs-, Demo-, Test- und Produktionsworkloads eignet. In diesem Tutorial werden die einzelnen Schritte erläutert.

OpenFrame umfasst mehrere Komponenten, die die Mainframeemulationsumgebung in Azure erstellen. OpenFrame-Onlinedienste ersetzen z.B. die Mainframemiddleware wie IBM Customer Information Control System (CICS), und OpenFrame Batch mit der Komponente TJES ersetzt das Job Entry Subsystem (JES) des IBM-Mainframes.

OpenFrame funktioniert mit einer beliebigen relationalen Datenbank einschließlich Oracle Database, Microsoft SQL Server, IBM Db2 und MySQL. Die Installation von OpenFrame verwendet die relationale TmaxSoft Tibero-Datenbank. Sowohl OpenFrame als auch Tibero werden auf dem Linux-Betriebssystem ausgeführt. In diesem Tutorial wird CentOS 7.3 installiert, obwohl Sie andere unterstützte Linux-Distributionen verwenden können. Der OpenFrame-Anwendungsserver und die Tibero-Datenbank werden auf einem virtuellen Computer (VM) installiert.

Das Tutorial führt Sie schrittweise durch die Installation der Komponenten der OpenFrame-Suite. Einiges muss separat installiert werden.

Die wichtigsten OpenFrame-Komponenten:

- Erforderliche Installationspakete.
- Tibero-Datenbank.
- Open Database Connectivity (ODBC) wird von Anwendungen in OpenFrame für die Kommunikation mit der Tibero-Datenbank verwendet.
- OpenFrame Base, die Middleware, die das gesamte System verwaltet.
- OpenFrame Batch, die Lösung, die die Batchsysteme des Mainframes ersetzt.
- TACF, ein Dienstmodul, das den Benutzerzugriff auf Systeme und Ressourcen steuert.
- ProSort, ein Sortiertool für die Batchtransaktionen.
- OFCOBOL, ein Compiler, der die COBOL-Programme des Mainframes übersetzt.
- OFASM, ein Compiler, der die Assembler-Programme des Mainframes übersetzt.
- OpenFrame Server Type C (OSC), die Lösung, die Middleware und IBM CICS des Mainframes ersetzt.
- Java Enterprise User Solution (JEUS), ein Webanwendungsserver, der für Java Enterprise Edition 6 zertifiziert ist.
- OFGW, die OpenFrame-Gatewaykomponente, die einen 3270-Listener enthält.
- OFManager, eine Lösung, die die Betriebs- und Verwaltungsfunktionen von OpenFrame in der Webumgebung bereitstellt.

Andere erforderliche OpenFrame-Komponenten:

- OSI, die Lösung, die die Mainframemiddleware und IMS DC ersetzt.
- TJES, die Lösung, die die JES-Umgebung des Mainframes bereitstellt.
- OFTSAM, die Lösung, die die Verwendung von (V)SAM-Dateien im offenen System ermöglicht.
- OFHiDB, die Lösung, die die IMS DB des Mainframes ersetzt.
- OFPLI, ein Compiler, der die PL/I-Programme des Mainframes übersetzt.
- PROTRIEVE, eine Lösung, die die Mainframesprache CA-Easytrieve ausführt.
- OFMiner, eine Lösung, die die Mainframeassets analysiert und dann in Azure migriert.

## <a name="architecture"></a>Aufbau

Die folgende Abbildung zeigt eine Übersicht über die OpenFrame 7.0-Architekturkomponenten, die in diesem Tutorial installiert werden:

![OpenFrame-Komponenten](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Azure-Systemanforderungen

Die folgende Tabelle enthält die Anforderungen für die Installation in Azure.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Anforderung</th><th>BESCHREIBUNG</th></tr>
</thead>
<tbody>
<tr><td>In Azure unterstützte Linux-Distributionen
</td>
<td>
Linux x86 2.6 (32-Bit, 64-Bit)<br/>
Red Hat 7.x<br/>
CentOS 7.x<br/>
</td>
</tr>
<tr><td>Hardware
</td>
<td>Kerne: 2 (Minimum)<br/>
Memory: 4GB (Minimum)<br/>
Auslagerungsbereich: 1GB (Minimum)<br/>
Festplatte: 100GB (Minimum)<br/>
</td>
</tr>
<tr><td>Optionale Software für Windows-Benutzer
</td>
<td>PuTTY: In diesem Handbuch zum Konfigurieren von VM-Features verwendet<br/>
WinSCP: Ein beliebter SFTP- und FTP-Client, den Sie verwenden können.<br/>
Eclipse für Windows: Eine von TmaxSoft unterstützte Entwicklungsplattform<br/>
(Microsoft Visual Studio wird derzeit nicht unterstützt.)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Voraussetzungen

Planen Sie einige Tage ein, um die gesamte erforderliche Software zusammenzustellen und alle manuellen Prozesse durchzuführen.

Führen Sie folgende Schritte aus, bevor Sie beginnen:

- Beschaffen Sie die OpenFrame-Installationsmedien bei TmaxSoft. Wenn Sie TmaxSoft-Bestandskunde sind, bitten Sie Ihren Ansprechpartner bei TmaxSoft um eine lizenzierte Kopie. Fordern Sie andernfalls eine Testversion bei [TmaxSoft](https://www.tmaxsoft.com/contact/) an.

- Fordern Sie die OpenFrame-Dokumentation an, indem Sie eine E-Mail an <support@tmaxsoft.com> senden.

- Registrieren Sie sich für ein Azure-Abonnement, falls Sie noch keins besitzen. Sie können auch ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

- Optional. Richten Sie einen Site-to-Site-VPN-Tunnel oder eine Jumpbox ein, um den Zugriff auf die Azure-VM auf die zulässigen Benutzer in Ihrer Organisation zu beschränken. Dieser Schritt ist nicht erforderlich, aber eine bewährte Methode.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Einrichten eines virtuellen Computers in Azure für OpenFrame und Tibero

Sie können die OpenFrame-Umgebung über verschiedene Bereitstellungsmuster einrichten, aber das folgende Verfahren zeigt Ihnen, wie Sie den OpenFrame-Anwendungsserver und die Tibero-Datenbank auf einem virtuellen Computer bereitstellen. In größeren Umgebungen und für veränderbare Workloads hat es sich bewährt, die Datenbank separat auf einem eigenen virtuellen Computer bereitzustellen, um eine bessere Leistung zu erzielen.

**So erstellen Sie einen virtuellen Computer**

1. Navigieren Sie unter <https://portal.azure.com> zum Azure-Portal, und melden Sie sich bei Ihrem Konto an.

2. Klicken Sie auf **Virtuelle Computer**.

    ![Ressourcenliste im Azure-Portal](media/vm-01.png)

3. Klicken Sie auf **Hinzufügen**.

    ![Hinzufügen einer Option im Azure-Portal](media/vm-02.png)

4. Klicken Sie rechts neben **Betriebssysteme** auf **Weitere**.

     ![Option „Weitere“ im Azure-Portal](media/vm-03.png)

5. Klicken Sie auf **CentOS-basiertes 7.3**, um diese exemplarische Vorgehensweise genau zu befolgen, oder wählen Sie eine andere unterstützte Linux-Distribution aus.

     ![Betriebssystemoptionen im Azure-Portal](media/vm-04.png)

6. Geben Sie bei den **Grundlagen**-Einstellungen **Name**, **Benutzername**, **Authentifizierungstyp**, **Abonnement** (die nutzungsbasierte Bezahlung ist der AWS-Zahlungsstil) und **Ressourcengruppe** (eine vorhandene Ressourcengruppe verwenden oder eine TmaxSoft-Gruppe erstellen) ein.

7. Klicken Sie nach Abschluss des Vorgangs (einschließlich des öffentlichen/privaten Schlüsselpaars für **Authentifizierungstyp**) auf **Senden**.

> [!NOTE]
> Befolgen Sie bei Verwendung eines öffentlichen SSH-Schlüssels für **Authentifizierungstyp** die Schritte im nächsten Abschnitt, um das öffentliche/private Schlüsselpaar zu generieren, und setzen Sie dann die Schritte hier fort.

### <a name="generate-a-publicprivate-key-pair"></a>Generieren eines öffentlichen/privaten Schlüsselpaars

Wenn Sie ein Windows-Betriebssystem verwenden, benötigen Sie PuTTYgen, um ein öffentliches/privates Schlüsselpaar zu generieren.

Der öffentliche Schlüssel kann ohne Bedenken freigegeben werden, aber der private Schlüssel sollte vollständig geheim gehalten und nie für eine andere Partei freigegeben werden. Nach dem Generieren der Schlüssel müssen Sie den **öffentlichen SSH-Schlüssel** in die Konfiguration einfügen, d.h. zur Linux-VM hochladen. Er wird im \~/.ssh-Verzeichnis des Basisverzeichnisses des Benutzerkontos in den „authorized\_keys“ gespeichert. Die Linux-VM kann dann die Verbindung erkennen und überprüfen, sobald Sie den zugeordneten **privaten SSH-Schlüssel** im SSH-Client (in unserem Fall PuTTY) bereitstellen.

Wenn neue Personen Zugriff auf den virtuellen Computer erhalten: 

- Jede neue Person generiert ihre eigenen öffentlichen/privaten Schlüssel mit PuTTYgen.
- Die Personen speichern ihre eigenen privaten Schlüssel separat und senden die Daten des öffentlichen Schlüssels an den Administrator des virtuellen Computers.
- Der Administrator fügt den Inhalt des öffentlichen Schlüssels in die \~/.ssh/authorized\_-Schlüsseldatei ein.
- Die neue Person stellt eine Verbindung über PuTTY her.

**So generieren Sie ein öffentliches/privates Schlüsselpaar**

1.  Laden Sie PuTTYgen von <https://www.putty.org/> herunter, und installieren Sie PuTTYgen mit allen Standardeinstellungen.

2.  Navigieren Sie zum Öffnen von PuTTYgen zum PuTTY-Installationsverzeichnis „C:\\Programme\\PuTTY“.

    ![PuTTY-Benutzeroberfläche](media/puttygen-01.png)

3.  Klicken Sie auf **Generate (Generieren)** .

    ![Screenshot des Dialogfelds „PuTTY Key Generator“, in dem die Schaltfläche „Generate“ hervorgehoben ist.](media/puttygen-02.png)

4.  Speichern Sie nach der Generierung sowohl den öffentlichen als auch den privaten Schlüssel. Fügen Sie den Inhalt des öffentlichen Schlüssels in den Abschnitt **Öffentlicher SSH-Schlüssel** des Bereichs **Virtuellen Computer erstellen \> Grundlagen** ein (im vorherigen Abschnitt in den Schritten 6 und 7 gezeigt).

    ![Dialogfeld „PuTTY Key Generator“ (PuTTY-Schlüsselgenerator)](media/puttygen-03.png)

### <a name="configure-vm-features"></a>Konfigurieren von VM-Features

1. Wählen Sie im Azure-Portal im Blatt **Größe auswählen** Ihre gewünschten Linux-Computerhardwareeinstellungen aus. Die *minimalen* Anforderungen sowohl für die Installation von Tibero als auch OpenFrame sind 2 CPUs und 4GB RAM, wie in diesem Installationsbeispiel gezeigt:

    ![„Virtuellen Computer erstellen > Grundlagen“](media/create-vm-01.png)

2. Klicken Sie auf **3 Einstellungen**, und verwenden Sie die Standardeinstellungen, um optionale Features zu konfigurieren.
3. Überprüfen Sie Ihre Zahlungsinformationen.

    ![„Virtuellen Computer erstellen > Kaufen“](media/create-vm-02.png)

4. Senden Sie Ihre Auswahl. Azure beginnt, den virtuellen Computer bereitzustellen. Dieser Vorgang dauert in der Regel einige Minuten.

5. Wenn der virtuelle Computer bereitgestellt ist, wird dessen Dashboard mit allen Einstellungen angezeigt, die während der Konfiguration ausgewählt wurden. Notieren Sie sich die **Öffentliche IP-Adresse**.

    ![tmax auf Azure-Dashboard](media/create-vm-03.png)

6. Öffnen Sie PuTTY.

7. Geben Sie für **Host Name** (Hostname) Ihren Benutzernamen und die öffentliche IP-Adresse ein, die Sie kopiert haben. Beispiel: **Benutzername\@publicip**.

    ![Screenshot des Dialogfelds „PuTTY Configuration“, in dem das Feld „Host Name (or IP address)“ hervorgehoben ist.](media/putty-01.png)

8. Klicken Sie im Feld **Category** (Kategorie) auf **Connection (Verbindung) \> SSH \> Auth**. Geben Sie den Pfad zu Ihrer **Private key file** (privaten Schlüsseldatei) an.

    ![Dialogfeld „PuTTY Configuration“ (PuTTY-Konfiguration)](media/putty-02.png)

9. Klicken Sie auf **Open** (Öffnen), um das PuTTY-Fenster zu starten. Bei erfolgreicher Ausführung werden Sie mit Ihrem in Azure ausgeführten neuen virtuellen CentOS-Computer verbunden.

10. Geben Sie zum Anmelden als Root-Benutzer **sudo bash** ein.

    ![Root-Benutzeranmeldung im Befehlsfenster](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>Einrichten von Umgebung und Paketen

Der virtuelle Computer ist nun erstellt, Sie sind angemeldet, und nun müssen Sie einige Setupschritte durchführen und die erforderlichen Pakete zur Vorbereitung der Installation installieren.

1. Ordnen Sie den Namen **ofdemo** der lokalen IP-Adresse zu, wobei Sie mit vi die Datei „hosts“ (`vi /etc/hosts`) bearbeiten. Wenn unsere IP „192.168.96.148 ofdemo“ ist, sieht es vor der Änderung so aus:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Nach der Änderung:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. Erstellen Sie Gruppen und Benutzer:

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. Ändern Sie das Kennwort für Benutzer „oframe7“:

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. Aktualisieren Sie die Kernelparameter in „/etc/sysctl.conf“:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Aktualisieren Sie die Kernelparameter dynamisch ohne Neustart:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Rufen Sie die erforderlichen Pakete ab: Stellen Sie sicher, dass der Server mit dem Internet verbunden ist, laden Sie die folgenden Pakete herunter, und installieren Sie sie:

     - dos2unix
     - glibc
     - glibc.i686 glibc.x86\_64
     - libaio
     - ncurses

          > [!NOTE]
          > Erstellen Sie nach der Installation des ncurses-Pakets die folgenden symbolischen Verknüpfungen:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c++
     - libaio-devel.x86\_64
     - strace
     - ltrace
     - gdb

7. Führen Sie bei einer Java-RPM-Installation folgende Schritte aus:

```
root@ofdemo ~]# rpm -ivh jdk-7u79-linux-x64.rpm
[root@ofdemo ~]# vi .bash_profile

# JAVA ENV
export JAVA_HOME=/usr/java/jdk1.7.0_79/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar

[root@ofdemo ~]# source /etc/profile
[root@ofdemo ~]# java –version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

[root@ofdemo ~]# echo $JAVA_HOME /usr/java/jdk1.7.0_79/
```

## <a name="install-the-tibero-database"></a>Installieren der Tibero-Datenbank

Tibero bietet mehrere der wichtigsten Funktionen in der OpenFrame-Umgebung in Azure:

- Tibero wird als interner OpenFrame-Datenspeicher für verschiedene Systemfunktionen verwendet.
- VSAM-Dateien wie z.B. KSDS, RRDS und ESDS verwenden die Tibero-Datenbank intern zur Datenspeicherung.
- Das TACF-Datenrepository wird in Tibero gespeichert.
- Die OpenFrame-Kataloginformationen werden in Tibero gespeichert.
- Die Tibero-Datenbank kann als Ersatz für IBM Db2 zum Speichern von Anwendungsdaten verwendet werden.

**So installieren Sie Tibero**

1. Stellen Sie sicher, dass die binäre Installationsprogrammdatei für Tibero vorhanden ist, und überprüfen Sie die Versionsnummer.
2. Kopieren Sie die Tibero-Software in das Tibero-Benutzerkonto (oframe). Beispiel:

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Öffnen Sie „.bash\_profile“ in vi (`vi .bash_profile`), und fügen Sie Folgendes ein:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Um das Bashprofil auszuführen, geben Sie an der Eingabeaufforderung Folgendes ein:

    ```
    source .bash_profile
    ```

5. Generieren Sie die tip-Datei (eine Konfigurationsdatei für Tibero), und öffnen Sie sie in vi. Beispiel:

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Ändern Sie „\$TB\_HOME/client/config/tbdsn.tbr“, und fügen Sie wie gezeigt „127.0.0.1“ anstelle von „oflocalhost“ ein:

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Erstellen Sie die Datenbank. Die folgende Ausgabe wird angezeigt:

    ```
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NOMOUNT mode).
     /--------------------- newmount sql ------------------------/
    create database character set MSWIN949 national character set UTF16;
    /-----------------------------------------------------------/
    Database created.
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    /opt/tmaxdb/tibero6/bin/tbsvr
    ………………………..
    Creating agent table...
    Done.
    For details, check /opt/tmaxdb/tibero6/instance/TVSAM/log/system_init.log.
    ************************************************** 
    * Tibero Database TVSAM is created successfully on Fri Aug 12 19:10:43 UTC 2016.
    *     Tibero home directory ($TB_HOME) =
    *         /opt/tmaxdb/tibero6
    *     Tibero service ID ($TB_SID) = TVSAM
    *     Tibero binary path =
    *         /opt/tmaxdb/tibero6/bin:/opt/tmaxdb/tibero6/client/bin
    *     Initialization parameter file =
    *         /opt/tmaxdb/tibero6/config/TVSAM.tip
    * 
    * Make sure that you always set up environment variables $TB_HOME and
    * $TB_SID properly before you run Tibero.
     ******************************************************************************
    ```

8. Um Tibero wieder verwenden zu können, fahren Sie Tibero zuerst mit dem `tbdown`-Befehl herunter. Beispiel:

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Starten Sie Tibero jetzt mit `tbboot`. Beispiel:

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Um einen Tabellenbereich zu erstellen, greifen Sie mit dem SYS-Benutzer (sys/tmax) auf die Datenbank zu, und erstellen Sie dann den erforderlichen Tabellenbereich für das Standardvolume und TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Geben Sie jetzt die folgenden SQL-Befehle ein:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Starten Sie Tibero, und stellen Sie sicher, dass die Tibero-Prozesse ausgeführt werden:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Ausgabe:

![Tibero-Ausgabe](media/tibero-01.png)

## <a name="install-odbc"></a>Installieren von ODBC

Anwendungen in OpenFrame kommunizieren über die ODBC-API, die durch das Open-Source-unixODBC-Projekt bereitgestellt wird, mit der Tibero-Datenbank.

So installieren Sie ODBC:

1. Stellen Sie sicher, dass die unixODBC-2.3.4.tar.gz-Installationsprogrammdatei vorhanden ist, oder verwenden Sie den `wget unixODBC-2.3.4.tar.gz`-Befehl. Beispiel:

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Entzippen Sie die Binärdateien. Beispiel:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Navigieren Sie zum unixODBC-2.3.4-Verzeichnis, und generieren Sie das Makefile mithilfe der durch Überprüfung des Computers gewonnenen Informationen. Beispiel:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     In der Standardeinstellung wird unixODBC in „/usr/local“ installiert, sodass `--prefix` einen Wert zum Ändern des Speicherorts übergibt. Auf ähnliche Weise werden Konfigurationsdateien standardmäßig in „/etc“ installiert, sodass `--sysconfdir` den Wert des gewünschten Speicherorts übergibt.

4. Führen Sie das Makefile aus: `[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Kopieren Sie die ausführbare Datei nach dem Kompilieren in das Programmverzeichnis. Beispiel:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Verwenden Sie vi, um das Bashprofil zu bearbeiten (`vi ~/.bash_profile`), und fügen Sie Folgendes hinzu:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Wenden Sie die ODBC an. Bearbeiten Sie die folgenden Dateien entsprechend. Beispiel:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ source ~/.bash_profile

     [oframe7@ofdemo ~]$ cd

     [oframe7@ofdemo ~]$ odbcinst -j unixODBC 2.3.4
     DRIVERS............: /home/oframe7/odbcinst.ini
     SYSTEM DATA SOURCES: /home/oframe7/odbc.ini
     FILE DATA SOURCES..: /home/oframe7/ODBCDataSources
     USER DATA SOURCES..: /home/oframe7/unixODBC/etc/odbc.ini
     SQLULEN Size.......: 8
     SQLLEN Size........: 8
     SQLSETPOSIROW Size.: 8

     [oframe7@ofdemo ~]$ vi odbcinst.ini

     [Tibero]
     Description = Tibero ODBC driver for Tibero6
     Driver = /opt/tmaxdb/tibero6/client/lib/libtbodbc.so
     Setup = 
     FileUsage = 
     CPTimeout = 
     CPReuse = 
     Driver Logging = 7

     [ODBC]
     Trace = NO 
     TraceFile = /home/oframe7/odbc.log 
     ForceTrace = Yes 
     Pooling = No 
     DEBUG = 1

     [oframe7@ofdemo ~]$ vi odbc.ini

     [TVSAM]
     Description = Tibero ODBC driver for Tibero6 
     Driver = Tibero 
     DSN = TVSAM 
     SID = TVSAM 
     User = tibero 
     password = tmax
     ```

8. Erstellen Sie eine symbolische Verknüpfung, und überprüfen Sie die Verbindung mit der Tibero-Datenbank:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

Die folgende Ausgabe wird angezeigt:

![ODBC-Ausgabe zeigt Verbindung mit SQL an](media/odbc-01.png)

## <a name="install-openframe-base"></a>Installieren von OpenFrame Base

Der Base-Anwendungsserver wird vor den einzelnen Diensten installiert, mit denen OpenFrame das System in Azure einschließlich der Serverprozesse für die Transaktionsverarbeitung verwaltet.

**So installieren Sie OpenFrame Base**

1. Stellen Sie sicher, dass die Tibero-Installation erfolgreich war, und überprüfen Sie dann, ob die folgende OpenFrame\_Base7\_0\_Linux\_x86\_64.bin-Installationsprogrammdatei und die base.properties-Konfigurationsdatei vorhanden sind.

2. Aktualisieren Sie das Bashprofil mit den folgenden Tibero-spezifischen Informationen:

     ```bash
     alias ofhome='cd $OPENFRAME_HOME'
     alias ulog='cd $OPENFRAME_HOME/log/tmax/ulog'
     alias sysjcl='cd $OPENFRAME_HOME/volume_default/SYS1.JCLLIB'
     alias sysload='cd $OPENFRAME_HOME/volume_default/SYS1.LOADLIB'
     alias sysproc='cd $OPENFRAME_HOME/volume_default/SYS1.PROCLIB'
     alias oscsrc='cd $OPENFRAME_HOME/osc/oivp'
     alias osisrc='cd $OPENFRAME_HOME/osi/oivp'
     alias defvol='cd $OPENFRAME_HOME/volume_default'
     ```

3. Führen Sie das Bashprofil aus:`[oframe7@ofdemo ~]$ . .bash_profile`
4. Stellen Sie sicher, dass die Tibero-Prozesse ausgeführt werden. Beispiel:

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Basis](media/base-01.png)

     > [!IMPORTANT]
     > Achten Sie darauf, dass Sie Tibero vor der Installation starten.

5. Generieren Sie die Lizenz unter [technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do), und legen Sie (PUT) die Lizenzen für OpenFrame Base, Batch, TACF und OSC im entsprechenden Ordner ab:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Laden Sie die Binär- und base.properties-Dateien für OpenFrame Base herunter:

     ```
     [oframe7@ofdemo ~]$ vi base.properties
     OPENFRAME_HOME= <appropriate location for installation> ex. /opt/tmaxapp/OpenFrame TP_HOST_NAME=<your IP Hostname> ex. ofdemo 
     TP_HOST_IP=<your IP Address> ex. 192.168.96.148 
     TP_SHMKEY=63481 
     TP_TPORTNO=6623 
     TP_UNBLOCK_PORT=6291 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     MASCAT_NAME=SYS1.MASTER.ICFCAT 
     MASCAT_CREATE=YES 
     DEFAULT_VOLSER=DEFVOL 
     VOLADD_DEFINE=YES TSAM_USERNAME=tibero 
     TSAM_PASSWORD=tmax 
     TSAM_DATABASE=oframe 
     DATASET_SHMKEY=63211 
     DSLOCK_DATA=SYS1.DSLOCK.DATA 
     DSLOCK_LOG=SYS1.DSLOCK.LOG 
     DSLOCK_SEQ=dslock_seq.dat 
     DSLOCK_CREATE=YES 
     OPENFRAME_LICENSE_PATH=/opt/tmaxapp/license/OPENFRAME TMAX_LICENSE_PATH=/opt/tmaxapp/license/TMAX
     ```

7. Führen Sie das Installationsprogramm mithilfe der base.properties-Datei aus. Beispiel:

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    Nach Abschluss des Vorgangs wird die Meldung angezeigt, dass die Installation abgeschlossen ist.

8. Überprüfen Sie die OpenFrame Base-Verzeichnisstruktur mithilfe des `ls -ltr`-Befehls. Beispiel:

     ```
     [oframe7@ofdemo OpenFrame]$ ls -ltr
     total 44

     drwxrwxr-x. 4 oframe7 oframe7 61 Nov 30 16:57 UninstallerData 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 bin 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 cpm drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 data 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 include 
     drwxrwxr-x. 2 oframe7 oframe7 8192 Nov 30 16:57 lib 
     drwxrwxr-x. 6 oframe7 oframe7 48 Nov 30 16:57 log 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 profile 
     drwxrwxr-x. 7 oframe7 oframe7 62 Nov 30 16:57 sample 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 schema 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 temp 
     drwxrwxr-x. 3 oframe7 oframe7 16 Nov 30 16:57 shared 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 license 
     drwxrwxr-x. 23 oframe7 oframe7 4096 Nov 30 16:58 core 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 config 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 scripts 
     drwxrwxr-x. 2 oframe7 oframe7 25 Nov 30 16:58 volume_default
     ```

9. Starten Sie OpenFrame Base:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![tmboot-Befehlsausgabe](media/base-02.png)

10. Stellen Sie mit dem tmadmin-Befehl in si sicher, dass der Prozessstatus „ready“ (bereit) ist. „RDY“ wird für jeden Prozess in der **status**-Spalte angezeigt:

     ![tmadmin-Befehlsausgabe](media/base-03.png)

11. Fahren Sie OpenFrame Base herunter:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(vtammgr:43) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLH downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLL downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TLM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-openframe-batch"></a>Installieren von OpenFrame Batch

OpenFrame Batch besteht aus mehreren Komponenten, die Mainframebatchumgebungen simulieren, und wird verwendet, um Batchaufträge in Azure auszuführen.

**So installieren Sie Batch**

1. Stellen Sie sicher, dass die Base-Installation erfolgreich war, und überprüfen Sie dann, ob die OpenFrame\_Batch7\_0\_Fix2\_MVS\_Linux\_x86\_64.bin-Installationsprogrammdatei und die batch.properties-Konfigurationsdatei vorhanden sind:

2. Geben Sie an der Eingabeaufforderung den Befehl `vi batch.properties` ein, um die batch.properties-Datei mit vi zu bearbeiten.

3. Ändern Sie die Parameter wie folgt:

     ```
     OPENFRAME_HOME = /opt/tmaxapp/OpenFrame
     DEFAULT_VOLSER=DEFVOL 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     RESOURCE_SHMKEY=66991 
     #JOBQ_DATASET_CREATE=YES 
     #OUTPUTQ_DATASET_CREATE=YES 
     DEFAULT_JCLLIB_CREATE=YES 
     DEFAULT_PROCLIB_CREATE=YES 
     DEFAULT_USERLIB_CREATE=YES 
     TJES_USERNAME=tibero 
     TJES_PASSWORD=tmax 
     TJES_DATABASE=oframe 
     BATCH_TABLE_CREATE=YES
     ```

4. Um das Batch-Installationsprogramm auszuführen, geben Sie an der Eingabeaufforderung Folgendes ein:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. Wenn die Installation abgeschlossen ist, starten Sie die installierten OpenFrame-Suites, indem Sie `tmboot` an der Eingabeaufforderung eingeben.

    ![tmboot-Ausgabe](media/tmboot-01.png)

6. Geben Sie `tmadmin` an der Eingabeaufforderung ein, um den OpenFrame-Prozess zu überprüfen.

    ![Tmax Admin-Bildschirm](media/tmadmin-01.png)

7. Führen Sie die folgenden Befehle aus:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Verwenden Sie den `tmdown`-Befehl zum Starten und Herunterfahren von Batch:

     ```
     [oframe7@ofdemo ~]$tmdown
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:44) downed: Wed Sep  7 16:01:46 2016
     TMDOWN: SERVER(vtammgr: 43) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:45) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:46) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjinit:55) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:49) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:51) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:52) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:53) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLL downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TLM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLH downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-tacf"></a>Installieren von TACF

TACF-Manager ist ein OpenFrame-Dienstmodul, das den Benutzerzugriff auf Systeme und Ressourcen über RACF-Sicherheit steuert.

**So installieren Sie TACF**

1. Überprüfen Sie, ob die OpenFrame\_Tacf7\_0\_Fix2\_Linux\_x86\_64.bin-Installationsprogrammdatei und tacf.properties-Konfigurationsdatei vorhanden sind.
2. Stellen Sie sicher, dass die Batch-Installation erfolgreich war, und öffnen Sie dann die tacf.properties-Datei mit vi (`vi tacf.properties`).
3. Ändern Sie die TACF-Parameter:

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. Wenden Sie nach Abschluss des TACF-Installationsprogramms die TACF-Umgebungsvariablen an. Geben Sie an der Eingabeaufforderung Folgendes ein:

     ```
     source \~/.bash\_profile
     ```

5. Führen Sie das TACF-Installationsprogramm aus. Geben Sie an der Eingabeaufforderung Folgendes ein:

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     Die Ausgabe sieht in etwa wie folgt aus:

     ```
     Wed Dec 07 17:36:42 EDT 2016
     Free Memory: 18703 kB 
     Total Memory: 28800 kB

     4 Command Line Args: 
     0:  -f 1:  tacf.properties 
     2:  -m 
     3:  SILENT 
     java.class.path: 
     /tmp/install.dir.41422/InstallerData 
     /tmp/install.dir.41422/InstallerData/installer.zip 
     ZGUtil.CLASS_PATH: 
     /tmp/install.dir.41422/InstallerData 
     tmp/install.dir.41422/InstallerData/installer.zip 
     sun.boot.class.path: 
     /tmp/install.dir.41422/Linux/resource/jre/lib/resources.jar /tmp/install.dir.41422/Linux/resource/jre/lib/rt.jar /tmp/install.dir.41422/Linux/resource/jre/lib/sunrsasign.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jsse.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jce.jar /tmp/install.dir.41422/Linux/resource/jre/lib/charsets.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jfr.jar /tmp/install.dir.41422/Linux/resource/jre/classes
     ```

6. Geben Sie an der Eingabeaufforderung `tmboot` ein, um OpenFrame neu zu starten. Die Ausgabe sieht in etwa wie folgt aus:

     ```
     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/7 
     TMBOOT: TMM is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLL is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLH is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: TLM(tlm) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsasvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrlhsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdsedt) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrcmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofruisvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsmlog) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(vtammgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjschd) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjinit) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjhist) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjspbk) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrpmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmtsmgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(tmsvr) is starting: Wed Sep  7 17:48:53 2016
     ```

7. Stellen Sie mithilfe von `tmadmin` im `si`-Befehl sicher, dass der Prozessstatus „ready“ (bereit) ist. Beispiel:

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     In der **status**-Spalte wird RDY angezeigt:

    ![RDY in der Spalte „status“](media/tmboot-02.png)

8. Führen Sie die folgenden Befehle aus:

     ```
     $$2 NODE1 (tmadm): quit 
     DM quit for node (NODE1)

     [oframe7@ofdemo ~]$ tacfmgr 
     Input USERNAME  : ROOT 
     Input PASSWORD  : SYS1

     TACFMGR: TACF MANAGER START!!!
     QUIT TACFMGR: TACF MANAGER END!!!

     [oframe7@ofdemo ~]$ tmdow
     ```

9. Fahren Sie den Server mithilfe des `tmdown`-Befehls herunter. Die Ausgabe sieht in etwa wie folgt aus:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(tmsvr:60) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLL downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLH downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TLM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-prosort"></a>Installieren von ProSort

ProSort ist ein Hilfsprogramm, das in Batchtransaktionen zum Sortieren von Daten verwendet wird.

**So installieren Sie ProSort**

1. Stellen Sie sicher, dass die Batch-Installation erfolgreich war, und überprüfen Sie, ob die **prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz**-Installationsprogrammdatei vorhanden ist.

2. Führen Sie das Installationsprogramm mithilfe der properties-Datei aus. Geben Sie an der Eingabeaufforderung Folgendes ein:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Verschieben Sie das prosort-Verzeichnis an den Hauptspeicherort. Geben Sie an der Eingabeaufforderung Folgendes ein:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Erstellen Sie ein license-Unterverzeichnis, und kopieren Sie die Lizenzdatei dorthin. Beispiel:

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Öffnen Sie „bash.profile“ in vi (`vi .bash_profile`), und aktualisieren Sie die Datei wie folgt:

     ```bash
     #       PROSORT

     PROSORT_HOME=/opt/tmaxapp/prosort 
     PROSORT_SID=gbg 
     PATH=$PATH:$PROSORT_HOME/bin LD_LIBRARY_PATH=$PROSORT_HOME/lib:$LD_LIBRARY_PATH LIBPATH$PROSORT_HOME/lib:$LIBPATH 
     export PROSORT_HOME PROSORT_SID 
     PATH LD_LIBRARY_PATH LIBPATH 
     PATH=$PATH:$OPENFRAME_HOME/shbin 
     export PATH
     ```

6. Um das Bashprofil auszuführen, geben Sie an der Eingabeaufforderung Folgendes ein: `. .bash_profile`

7. Erstellen Sie die Konfigurationsdatei. Beispiel:

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. Erstellen Sie die symbolische Verknüpfung. Beispiel:

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. Überprüfen Sie die ProSort-Installation durch Ausführen des `prosort -h`-Befehls. Beispiel:

     ```
     oframe@oframe7: prosort -h

     Usage: prosort [options] [sort script files]
     options ------
     -h             Display this information 
     -v             Display version information 
     -s             Display state information 
     -j             Display profile information 
     -x             Use SyncSort compatible mode
     ```

## <a name="install-ofcobol"></a>Installieren von OFCOBOL

OFCOBOL ist der OpenFrame-Compiler, der die COBOL-Programme des Mainframes übersetzt. 

**So installieren Sie OFCOBOL**

1. Stellen Sie sicher, dass die Batch/Online-Installation erfolgreich war, und überprüfen Sie, ob die OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin-Installationsprogrammdatei vorhanden ist.

2. Um das OFCOBOL-Installationsprogramm auszuführen, geben Sie an der Eingabeaufforderung Folgendes ein:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Lesen Sie die Lizenzvereinbarung, und drücken Sie die EINGABETASTE, um den Vorgang fortzusetzen.

4. Akzeptieren Sie die Lizenzbedingungen. Nachdem die Installation abgeschlossen ist, wird Folgendes angezeigt:

     ```
     Choose Install Folder 
     --------------------
     Where would you like to install?
     Default Install Folder: /home/oframe7/OFCOBOL

     ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT : /opt/tmaxapp/OFCOBOL

     INSTALL FOLDER IS: /opt/tmaxapp/OFCOBOL 
     IS THIS CORRECT? (Y/N): Y[oframe7@ofdemo ~]$ vi .bash_profile

     ============================================================================ Installing... 
     ------------
     [==================|==================|==================|==================] 
     [------------------|------------------|------------------|------------------]

     =============================================================================== Installation Complete 
     --------------------
     Congratulations. OpenFrame_COBOL has been successfully installed
     PRESS <ENTER> TO EXIT THE INSTALLER
     ```

5. Öffnen Sie das Bashprofil in vi (`vi .bash_profile`), und stellen Sie sicher, dass es mit OFCOBOL-Variablen aktualisiert wird.
6. Führen Sie das Bashprofil aus. Geben Sie an der Eingabeaufforderung Folgendes ein:

     ```
      source ~/.bash_profile
     ```

7. Kopieren Sie die OFCOBOL-Lizenz in den installierten Ordner. Beispiel:
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Wechseln Sie zur OpenFrame-Konfigurationsdatei „tjclrun.conf“, und öffnen Sie sie in vi. Beispiel:
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   So sieht der SYSLIB-Abschnitt vor der Änderung aus:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   So sieht der SYSLIB-Abschnitt nach der Änderung aus:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Überprüfen Sie die OpenFrame\_COBOL\_InstallLog.log-Datei in vi, und stellen Sie sicher, dass keine Fehler vorliegen. Beispiel:
     ```
     [oframe7@ofdemo ~]$ vi $OFCOB_HOME/UninstallerData/log/OpenFrame_COBOL_InstallLog.log 
     …….. 
     Summary 
     ------
     Installation: Successful. 
     131 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalError
     ```
10. Verwenden Sie den `ofcob --version`-Befehl, und überprüfen Sie die Versionsnummer, um die Installation zu überprüfen. Beispiel:

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Starten Sie OpenFrame mithilfe des `tmdown/tmboot`-Befehls neu.

## <a name="install-ofasm"></a>Installieren von OFASM

OFASM ist der OpenFrame-Compiler, der die Assembler-Programme des Mainframes übersetzt.

**So installieren Sie OFASM**

1. Stellen Sie sicher, dass die Batch/Online-Installation erfolgreich war, und überprüfen Sie, ob die OpenFrame\_ASM3\_0\_Linux\_x86\_64.bin-Installationsprogrammdatei vorhanden ist.

2. Führen Sie das Installationsprogramm aus. Beispiel:

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Lesen Sie die Lizenzvereinbarung, und drücken Sie die EINGABETASTE, um den Vorgang fortzusetzen.
4. Akzeptieren Sie die Lizenzbedingungen.
5. Stellen Sie sicher, dass das Bashprofil mit OFASM-Variablen aktualisiert wird. Beispiel:

     ```
     [oframe7@ofdemo ~]$ source .bash_profile
     [oframe7@ofdemo ~]$ ofasm --version 
     # TmaxSoft OpenFrameAssembler v3 r328 
     (3ff35168d34f6e2046b96415bbe374160fcb3a34)

     [oframe7@ofdemo OFASM]$ vi .bash_profile

     # OFASM ENV 
     export OFASM_HOME=/opt/tmaxapp/OFASM 
     export OFASM_MACLIB=$OFASM_HOME/maclib/free_macro 
     export PATH="${PATH}:$OFASM_HOME/bin:" 
     export LD_LIBRARY_PATH="./:$OFASM_HOME/lib:$LD_LIBRARY_PATH"
     ```

6. Öffnen Sie die OpenFrame-Konfigurationsdatei „tjclrun.conf“ in vi, und bearbeiten Sie sie wie folgt:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     So sieht der Abschnitt [SYSLIB] *vor* der Änderung aus:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     So sieht der Abschnitt [SYSLIB] *nach* der Änderung aus:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Öffnen Sie die OpenFrame\_ASM\_InstallLog.log-Datei in vi, und stellen Sie sicher, dass keine Fehler vorliegen. Beispiel:

     ```
     [oframe7@ofdemo ~]$ vi 
     $OFASM_HOME/UninstallerData/log/OpenFrame_ASM_InstallLog.log 
     …….. 
     Summary 
     ------

     Installation: Successful.

     55 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalErrors
     ```

8. Starten Sie OpenFrame mit einem der folgenden Befehle neu:

     ```
     tmdown / tmboot
     ```

     – oder –

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>Installieren von OSC

OSC ist die IBM CICS ähnliche OpenFrame-Umgebung, die Hochgeschwindigkeits-OLTP-Transaktionen und andere Verwaltungsfunktionen unterstützt.

**So installieren Sie OSC**

1. Stellen Sie sicher, dass die Base-Installation erfolgreich war, und überprüfen Sie dann, ob die OpenFrame\_OSC7\_0\_Fix2\_Linux\_x86\_64.bin-Installationsprogrammdatei und die osc.properties-Konfigurationsdatei vorhanden sind.
2. Bearbeiten Sie die folgenden Parameter in der Datei „osc.properties“:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Führen Sie das Installationsprogramm mithilfe der properties-Datei wie folgt aus:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     Nach Abschluss des Vorgangs wird die Meldung angezeigt, dass die Installation abgeschlossen ist.

4. Stellen Sie sicher, dass das Bashprofil mit OSC-Variablen aktualisiert wird.
5. Überprüfen Sie die OpenFrame\_OSC7\_0\_Fix2\_InstallLog.log-Datei. Der Bericht könnte beispielsweise wie folgt aussehen:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Verwenden Sie vi, um die Konfigurationsdatei „ofsys.seq“ zu öffnen. Beispiel:

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. Bearbeiten Sie die Parameter in den Abschnitten „\#Basis“ und „\#BATCH“ wie gezeigt.

     ```
     Before changes
     #BASE
     ofrsasvr
     ofrlhsvr
     ofrdmsvr
     ofrdsedt
     ofrcmsvr
     ofruisvr
     ofrsmlog
     vtammgr
     TPFMAGENT

     #BATCH 
     #BATCH#obmtsmgr
     #BATCH#ofrpmsvr
     #BATCH#obmjmsvr
     #BATCH#obmjschd
     #BATCH#obmjinit
     #BATCH#obmjhist
     #BATCH#obmjspbk
     #TACF #TACF#tmsvr

     After changes  #BATCH
     #BASE          obmtsmgr 
     ofrsasvr       ofrpmsvr
     ofrlhsvr       obmjmsvr
     ofrdmsvr       obmjschd
     ofrdsedt       obmjinit
     ofrcmsvr       obmjhist
     ofruisvr       obmjspbk
     ofrsmlog
     vtammgr        #TACF
     TPFMAGENT      tmsvr
    ```

8. Kopieren Sie die Lizenzdatei. Beispiel:

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. Initialisieren Sie zum Starten und Herunterfahren von OSC den in der CICS-Region freigegebenen Arbeitsspeicher durch Eingabe von `osctdlinit OSCOIVP1` an der Eingabeaufforderung.

10. Führen Sie `oscboot` aus, um OSC zu starten. Die Ausgabe sieht in etwa wie folgt aus:

     ```
     OSCBOOT : pre-processing       [ OK ]

     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/12 
          TMBOOT: TMM is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLL is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLH is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: TLM(tlm) is starting: Mon Sep 12 01:40:25 2016 
     ```

11. Stellen Sie mithilfe des `tmadmin`-Befehls in si sicher, dass der Prozessstatus „ready“ (bereit) ist. Für alle Prozesse sollte „RDY“ in der **status**-Spalte angezeigt werden.

    ![Prozesse, für die „RDY“ angezeigt wird](media/tmadmin-02.png)

12. Fahren Sie OSC mithilfe des `oscdown`-Befehls herunter.

## <a name="install-jeus"></a>Installieren von JEUS

JEUS (Java Enterprise User Solution) bietet die Darstellungsschicht des OpenFrame-Webanwendungsservers.

Installieren Sie vor JEUS das Apache Ant-Paket, das die zum Installieren von JEUS erforderlichen Bibliotheken und Befehlszeilentools bereitstellt.

**So installieren Sie Apache Ant**

1. Laden Sie die Ant-Binärdatei mithilfe des `wget`-Befehls herunter. Beispiel:

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Verwenden Sie das `tar`-Hilfsprogramm, um die Binärdatei zu extrahieren und an einen geeigneten Speicherort zu verschieben. Beispiel:

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. Erstellen Sie der Effizienz halber eine symbolische Verknüpfung:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Öffnen Sie das Bashprofil in vi (`vi .bash_profile`), und aktualisieren Sie es mit den folgenden Variablen:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Wenden Sie die geänderte Umgebungsvariable an. Beispiel:

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**So installieren Sie JEUS**

1. Extrahieren Sie das Installationsprogramm mit dem `tar`-Hilfsprogramm. Beispiel:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Erstellen Sie einen **jeus**-Ordner (`mkdir jeus7`), und entzippen Sie die Binärdatei.
3. Wechseln Sie zum **setup**-Verzeichnis (oder verwenden Sie den JEUS-Parameter für Ihre eigene Umgebung). Beispiel:

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Führen Sie `ant clean-all` aus, bevor Sie den Build erstellen. Die Ausgabe sieht in etwa wie folgt aus:

     ```
     Buildfile: /home/oframe7jeus7/setup/build.xml

     clean-bin:
     delete-domain:
     [echo] Deleting a domain configuration: domain = jeus_domain
     delete-nodesxml:
     clean-config:
     clean-all:
     BUILD SUCCESSFUL
     Total time: 0 seconds
     ```

5.  Erstellen Sie eine Sicherungskopie der domain-config-template.properties-Datei. Beispiel:

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Öffnen Sie die domain-config-template.properties-Datei in vi:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Ändern Sie `jeus.password=jeusadmin nodename=Tmaxsoft` in `jeus.password=tmax1234 nodename=ofdemo`.

8. Führen Sie den `ant install`-Befehl aus, um JEUS zu erstellen.
9.  Aktualisieren Sie die .bash\_profile-Datei wie gezeigt mit den JEUS-Variablen:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Führen Sie das Bashprofil aus. Beispiel:

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Optional:* Erstellen Sie einen Alias für das einfache Herunterfahren und Starten von JEUS-Komponenten:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Um die Installation zu überprüfen, starten Sie den Domänenadministratorserver wie gezeigt:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Führen Sie zur Überprüfung die Webanmeldung mit folgender Syntax durch:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     Beispiel: <http://192.168.92.133:9736/webadmin/login.>. Der Anmeldebildschirm wird angezeigt:
    
     ![JEUS-WebAdmin-Anmeldebildschirm](media/jeus-01.png)

     > [!NOTE]
     > Wenn Probleme mit der Portsicherheit auftreten, öffnen Sie Port 9736, oder deaktivieren Sie die Firewall (`systemctl stop firewall`).

14. Klicken Sie zum Ändern des Hostnamens für „server1“ auf **Sperren und bearbeiten** und dann auf **server1**. Ändern Sie den Hostnamen im Server-Fenster wie folgt:

    1.  Ändern Sie **Nodename** (Knotennamen) in **ofdemo**.
    2.  Klicken Sie auf der rechten Seite des Fensters auf **OK**.
    3.  Klicken Sie links unten im Fenster auf **Änderungen übernehmen**, und geben Sie als Beschreibung *Hostnamenänderung* ein.

    ![JEUS-WebAdmin-Bildschirm](media/jeus-02.png)

15. Stellen Sie im Bestätigungsbildschirm sicher, dass die Konfiguration erfolgreich war.

    ![jeus_domain-Serverbildschirm](media/jeus-03.png)

16. Starten Sie den verwalteten Serverprozess „server1“ mit dem folgenden Befehl:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>Installieren von OFGW

OFGW ist das OpenFrame-Gateway, das die Kommunikation zwischen dem 3270-Terminalemulator und der OSI-Basis unterstützt und die Sitzungen zwischen dem Terminalemulator und OSI verwaltet.

**So installieren Sie OFGW**

1. Stellen Sie sicher, dass JEUS erfolgreich installiert wurde, und überprüfen Sie, ob die OFGW7\_0\_1\_Generic.bin-Installationsprogrammdatei vorhanden ist.
2. Führen Sie das Installationsprogramm aus. Beispiel:

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Verwenden Sie die folgenden Speicherorte für die entsprechenden Eingabeaufforderungen:
     -   JEUS-Basisverzeichnis
     -   JEUS-Domänenname
     -   JEUS-Servername
     -   Tibero-Treiber
     -   Tmax-Knoten-ID ofdemo

4. Akzeptieren Sie die übrigen Standardeinstellungen, und drücken Sie die EINGABETASTE, um das Installationsprogramm zu beenden.

5. Stellen Sie sicher, dass die URL für OFGW wie erwartet funktioniert:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     Der folgende Bildschirm wird angezeigt:

    ![OpenFrame-Webterminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>Installieren von OFManager

OFManager stellt Betriebs- und Verwaltungsfunktionen für OpenFrame in der Webumgebung bereit.

**So installieren Sie OFManager**

1. Überprüfen Sie, ob die OFManager7\_Generic.bin-Installationsprogrammdatei vorhanden ist.
2. Führen Sie das Installationsprogramm aus. Beispiel:

     ```
     OFManager7_Generic.bin
     ```

3.  Drücken Sie die EINGABETASTE, um fortzufahren, und akzeptieren Sie die Lizenzvereinbarung.
4.  Wählen Sie den Installationsordner aus.
5.  Übernehmen Sie die Standardeinstellungen.
6.  Wählen Sie Tibero als Datenbank aus.
7.  Drücken Sie die EINGABETASTE, um das Installationsprogramm zu beenden.
8.  Stellen Sie sicher, dass die URL für OFManager wie erwartet funktioniert:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

Der Startbildschirm wird angezeigt:

![Tmax-OpenFrame-Manager-Anmeldebildschirm](media/ofmanager-01.png)

Damit ist die Installation der OpenFrame-Komponenten abgeschlossen.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie eine Mainframemigration in Betracht ziehen, steht Ihnen unser expandierendes Partnerökosystem zur Seite. Ausführliche Anleitungen zur Auswahl einer Partnerlösung finden Sie in der [Platform Modernization Alliance](https://datamigration.microsoft.com/).

-   [Erste Schritte mit Azure](../../../../index.yml)
-   [Dokumentation zu Host Integration Server (HIS)](/host-integration-server/)
-   [Virtuelles Azure-Rechenzentrum: Lift-and-Shift-Leitfaden](/archive/blogs/azurecat/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide)
