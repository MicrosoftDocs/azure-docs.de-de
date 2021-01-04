---
title: Hochverfügbarkeitskonfiguration mit STONITH für SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Herstellen von Hochverfügbarkeit für SAP HANA in Azure (große Instanzen) unter SUSE mit STONITH
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b34a7665770308b45732711f5d8328eb1d0a785f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965067"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Hochverfügbarkeitskonfiguration unter SUSE mit STONITH
Dieses Dokument enthält detaillierte Schritt-für-Schritt-Anweisungen zur Konfiguration der Hochverfügbarkeit unter dem Betriebssystem SUSE mit dem STONITH-Gerät.

**Haftungsausschluss**: *Dieser Leitfaden ist im Rahmen von Tests der Konfiguration in der Microsoft HANA-Umgebung für große Instanzen entstanden, die erfolgreich ausgeführt wird. Da das Microsoft Service Management-Team für große HANA-Instanzen kein Betriebssystem unterstützt, müssen Sie sich möglicherweise an SUSE wenden, um weitere Informationen zur Fehlerbehebung oder Klärung zur Betriebssystemebene zu erhalten. Das Microsoft Service Management-Team konfiguriert das STONITH-Gerät, unterstützt Sie im vollen Umfang bei der Behandlung von Problemen mit dem STONITH-Gerät und kann hierzu zu Rate gezogen werden.*
## <a name="overview"></a>Übersicht
Um mit SUSE-Clustern die Hochverfügbarkeit zu konfigurieren, müssen folgende Voraussetzungen erfüllt sein.
### <a name="pre-requisites"></a>Voraussetzungen
- Große HANA-Instanzen wurden bereitgestellt.
- Das Betriebssystem ist registriert.
- Es besteht eine Verbindung zwischen HANA-Servern (große Instanzen) und dem SMT-Server zum Abrufen von Patches bzw. Paketen.
- Für das Betriebssystem wurden die neusten Patches installiert.
- NTP (Zeitserver) ist eingerichtet.
- Sie haben die aktuelle Version der SUSE-Dokumentation für die Hochverfügbarkeitskonfiguration gelesen und verstanden.

### <a name="setup-details"></a>Details zur Konfiguration
In diesem Leitfaden wird folgendes Setup verwendet:
- Betriebssystem: SLES 12 SP1 für SAP
- Große HANA-Instanzen: 2 x S192 (vier Sockets, 2TB)
- HANA-Version: HANA 2.0 SP1
- Servernamen: „sapprdhdb95“ (node1) und „sapprdhdb96“ (node2)
- STONITH-Gerät: iSCSI-basiertes STONITH-Gerät
- Konfiguration von NTP auf einem der HANA-Knoten (große Instanz)

Wenn Sie große HANA-Instanzen mit HSR einrichten, können Sie beim Microsoft Service Management-Team eine Anfrage zur Konfiguration von STONITH einreichen. Wenn Sie bereits ein Bestandskunde sind, der große HANA-Instanzen bereitgestellt hat, und das STONITH-Gerät für Ihre vorhandenen Blades konfigurieren müssen, müssen Sie für das Microsoft Service Management-Team folgende Informationen im Serviceanforderungsformular (Service Request Form, SRF) angeben. Sie können das SRF-Formular über den Technical Account Manager oder Ihren Microsoft-Kontakt für das Onboarding großer HANA-Instanzen anfordern. Neukunden können STONITH-Geräte bei der Bereitstellung anfordern. Die Eingaben sind im Bereitstellungsanforderungsformular verfügbar.

- Servername und Server-IP-Adresse (z.B. „myhanaserver1“, „10.35.0.1“)
- Standort (z.B. „USA, Osten“)
- Name des Kunden (z.B. „Microsoft“)
- SID – HANA-Systembezeichner (z.B. „H11“)

Sobald das STONITH-Gerät konfiguriert ist, erhalten Sie vom Microsoft Service Management-Team den SBD-Gerätenamen und die IP-Adresse des iSCSI-Speichers, mit denen Sie das STONITH-Gerät konfigurieren können. 

Für die Konfiguration der End-to-End-Hochverfügbarkeit mit STONITH müssen folgende Schritte durchgeführt werden:

1.  Identifizieren des SBD-Geräts
2.  Initialisieren des SBD-Geräts
3.  Konfigurieren des Clusters
4.  Einrichten des Softdog-Watchdogs
5.  Beitreten des Knotens zum Cluster
6.  Überprüfen des Clusters
7.  Konfigurieren der Ressourcen für den Cluster
8.  Testen des Failoverprozesses

## <a name="1---identify-the-sbd-device"></a>1.   Identifizieren des SBD-Geräts
In diesem Abschnitt wird beschrieben, wie das SBD-Gerät für Ihre Konfiguration ermittelt wird, nachdem das Microsoft Service Management-Team das STONITH-Gerät konfiguriert hat. **Dieser Abschnitt gilt nur für Bestandskunden.** Wenn Sie ein Neukunde sind, stellt Ihnen das Microsoft Service Management-Team den SBD-Gerätenamen zur Verfügung. Sie können diesen Abschnitt dann überspringen.

1.1 Ändern Sie */etc/iscsi/initiatorname.isci* in 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Diese Zeichenfolge wird vom Microsoft Service Management-Team bereitgestellt. Ändern Sie die Datei auf **beiden** Knoten, die Knotenanzahl variiert jedoch je nach Knoten.

![Der Screenshot zeigt eine initiatorname-Datei mit InitiatorName-Werten für einen Knoten.](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 Ändern Sie */etc/iscsi/iscsid.conf* in: Legen Sie *node.session.timeo.replacement_timeout=5* und *node.startup = automatic* fest. Ändern Sie die Datei auf **beiden** Knoten.

1.3 Führen Sie den Ermittlungsbefehl aus. Es werden vier Sitzungen angezeigt. Führen Sie ihn auf beiden Knoten aus.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![Der Screenshot zeigt ein Konsolenfenster mit den Ergebnissen des isciadm-Ermittlungsbefehls.](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4 Führen Sie den Befehl zum Anmelden beim iSCSI-Gerät aus. Es werden vier Sitzungen angezeigt. Führen Sie ihn auf **beiden** Knoten aus.

```
iscsiadm -m node -l
```
![Der Screenshot zeigt ein Konsolenfenster mit den Ergebnissen des iscsiadm-Knotenbefehls](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5 Führen Sie das Skript zum erneuten Prüfen aus: *rescan-scsi-bus.sh*.  Dieses Skript zeigt die neuen für Sie erstellten Datenträger an.  Führen Sie ihn auf beiden Knoten aus. Sie sollten eine LUN-Nummer größer als Null sehen (z.B. 1, 2 etc.).

```
rescan-scsi-bus.sh
```
![Der Screenshot zeigt ein Konsolenfenster mit den Ergebnissen des Skripts.](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1.6 Um den Gerätenamen abzurufen, führen Sie den Befehl *fdisk – l* aus. Führen Sie ihn auf beiden Knoten aus. Wählen Sie das Gerät mit der Größe **178 MiB** aus.

```
  fdisk –l
```

![Der Screenshot zeigt ein Konsolenfenster mit den Ergebnissen des fdisk-Befehls.](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2.   Initialisieren des SBD-Geräts

2.1 Initialisieren Sie das SBD-Gerät auf **beiden** Knoten.

```
sbd -d <SBD Device Name> create
```
![Der Screenshot zeigt ein Konsolenfenster mit den Ergebnissen des sbd create-Befehls.](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2 Überprüfen Sie, welche Daten auf das Gerät geschrieben wurden. Führen Sie diesen Schritt auf **beiden** Knoten durch.

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3.   Konfigurieren des Clusters
In diesem Abschnitt werden die Schritte zum Konfigurieren des SUSE-Hochverfügbarkeitsclusters beschrieben.
### <a name="31-package-installation"></a>3.1 Paketinstallation
3.1.1 Überprüfen Sie, ob die Muster „ha_sles“ und „SAPHanaSR-doc“ installiert sind. Wenn sie nicht installiert sein sollten, installieren Sie sie. Installieren Sie sie auf **beiden** Knoten.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![Der Screenshot zeigt ein Konsolenfenster mit den Ergebnissen des Musterbefehls.](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![Der Screenshot zeigt ein Konsolenfenster mit den Ergebnissen des SAPHanaSR-doc-Befehls.](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2 Einrichten des Clusters
3.2.1 Sie können den Cluster entweder mit dem Befehl *ha-cluster-init* oder dem Assistenten yast2 konfigurieren. In diesem Fall wird der Assistent „yast2“ verwendet. Führen Sie diesen Schritt **nur auf dem primären Knoten** durch.

Folgen Sie „yast2“ > „High Availability“ > „Cluster“ ![Der Screenshot zeigt das YaST Control Center mit ausgewählten Optionen „High Availability“ und „Cluster“.](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
![Der Screenshot zeigt ein Dialogfeld mit Optionen zum Installieren und Abbrechen.](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Da das halk2-Paket bereits installiert ist, klicken Sie auf **Abbrechen**.

![Der Screenshot zeigt eine Meldung zu Ihrer Abbrechen-Option.](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Klicken Sie auf **Weiter**.

Erwarteter Wert=Anzahl der bereitgestellten Knoten (in diesem Fall 2) ![Der Screenshot zeigt Clustersicherheit mit einem Kontrollkästchen zum Aktivieren der Sicherheitsauthentifizierung.](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png)
Klicken Sie auf **Weiter**
![Der Screenshot zeigt das Fenster zur Clusterkonfiguration mit den Listen der Synchronisierungshosts und der zu synchronisierenden Dateien.](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png)
Fügen Sie Knotennamen hinzu, und klicken Sie dann auf „Vorgeschlagene Dateien hinzufügen“.

Klicken Sie auf „csync2 aktivieren“.

Klicken Sie auf „Vorinstallierte Schlüssel generieren“. Daraufhin wird das unten dargestellte Popupfenster angezeigt.

![Der Screenshot zeigt eine Meldung, dass Ihr Schlüssel generiert wurde.](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Klicken Sie auf **OK**

Die Authentifizierung erfolgt über die IP-Adressen und vorinstallierten Schlüssel in csync2. Die Schlüsseldatei wird mit „csync2 -k /etc/csync2/key_hagroup“ erzeugt. Die Datei „key_hagroup“ sollte nach der Erstellung manuell in alle Clustermitglieder kopiert. **Achten Sie darauf, die Datei von node1 auf node2 zu kopieren**.

![Der Screenshot zeigt ein Dialogfeld zur Clusterkonfiguration mit den erforderlichen Optionen zum Kopieren des Schlüssels auf alle Mitglieder des Clusters.](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Klicken Sie auf **Weiter**
![Der Screenshot zeigt das Fenster des Clusterdiensts.](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

Standardmäßig ist das Starten deaktiviert. Ändern Sie es in „Ein“, damit Pacemaker beim Systemstart gestartet wird. Sie können die Auswahl gemäß Ihren Konfigurationsanforderungen treffen.
Wenn Sie auf **Weiter** klicken, ist die Clusterkonfiguration abgeschlossen.

## <a name="4---setting-up-the-softdog-watchdog"></a>4.   Einrichten des Softdog-Watchdogs
In diesem Abschnitt wird die Konfiguration des Watchdogs (Softdog) beschrieben.

4.1 Fügen Sie die folgende Zeile zu */etc/init.d/boot.local* auf **beiden** Knoten hinzu.
```
modprobe softdog
```
![Der Screenshot zeigt eine Bootdatei mit hinzugefügter Softdog-Zeile.](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 Aktualisieren Sie die Datei */etc/sysconfig/sbd* wie folgt auf **beiden** Knoten:
```
SBD_DEVICE="<SBD Device Name>"
```
![Der Screenshot zeigt die sbd-Datei mit hinzugefügtem Wert SBD_DEVICE.](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 Laden Sie das Kernelmodul auf **beiden** Knoten, indem Sie den folgenden Befehl ausführen.
```
modprobe softdog
```
![Der Screenshot zeigt einen Teil eines Konsolenfensters mit dem Befehl „modprobe softdog“.](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4 Stellen Sie sicher, dass Softdog wie folgt auf **beiden** Knoten ausgeführt wird:
```
lsmod | grep dog
```
![Der Screenshot zeigt einen Teil eines Konsolenfensters mit dem Ausführungsergebnis des lsmod-Befehls.](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 Starten Sie das SBD-Gerät auf **beiden** Knoten.
```
/usr/share/sbd/sbd.sh start
```
![Der Screenshot zeigt einen Teil eines Konsolenfensters mit dem Startbefehl.](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 Testen Sie den SBD-Deamon auf **beiden** Knoten. Nach der Konfiguration auf **beiden** Knoten werden zwei Einträge angezeigt.
```
sbd -d <SBD Device Name> list
```
![Der Screenshot zeigt einen Teil eines Konsolenfensters, das zwei Einträge anzeigt.](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 Senden Sie eine Testnachricht an **einen** der Knoten.
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![Der Screenshot zeigt einen Teil eines Konsolenfensters, das zwei Einträge anzeigt.](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 Auf dem **zweiten** Knoten (node2) können Sie den Nachrichtenstatus überprüfen.
```
sbd  -d <SBD Device Name> list
```
![Der Screenshot zeigt einen Teil eines Konsolenfensters, in dem eins der Mitglieder einen Testwert für das andere Mitglied anzeigt.](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 Um die SBD-Konfiguration zu übernehmen, aktualisieren Sie die Datei */etc/sysconfig/sbd* wie folgt. Aktualisieren Sie die Datei auf **beiden** Knoten.
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10 Starten Sie den Pacemaker-Dienst auf dem **primären Knoten** (node1).
```
systemctl start pacemaker
```
![Der Screenshot zeigt ein Konsolenfenster, das den Status nach dem Starten von pacemaker zeigt.](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Wenn beim Pacemaker-Dienst ein *Fehler* auftritt, lesen Sie *Szenario 5: Beim Pacemaker-Dienst tritt ein Fehler auf*.

## <a name="5---joining-the-cluster"></a>5.   Beitreten zum Cluster
In diesem Abschnitt wird beschrieben, wie Sie den Knoten zum Cluster hinzuzufügen.

### <a name="51-add-the-node"></a>5.1 Fügen Sie den Knoten hinzu.
Führen Sie den folgenden Befehl auf **node2** aus, damit node2 dem Cluster beitritt.
```
ha-cluster-join
```
Wenn beim Beitritt zum Cluster ein *Fehler* auftritt, lesen Sie *Szenario 6: Node 2 kann nicht dem Cluster beitreten*.

## <a name="6---validating-the-cluster"></a>6.   Überprüfen des Clusters

### <a name="61-start-the-cluster-service"></a>6.1 Starten Sie den Clusterdienst.
So überprüfen und starten Sie den Cluster optional zum ersten Mal auf **beiden** Knoten
```
systemctl status pacemaker
systemctl start pacemaker
```
![Der Screenshot zeigt ein Konsolenfenster mit dem Status von pacemaker.](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6.2 Überwachen Sie den Status.
Führen Sie den Befehl *crm_mon* aus, um sicherzustellen, dass **beide** Knoten online sind. Sie können ihn für **beide Knoten** des Clusters ausführen.
```
crm_mon
```
![Der Screenshot zeigt ein Konsolenfenster mit den Ergebnissen von „crm_mon“.](media/HowToHLI/HASetupWithStonith/crm-mon.png)
Sie können sich auch bei hawk anmelden, um den Clusterstatus zu überprüfen: *https://\<node IP>:7630*. Der Standardbenutzer lautet „hacluster“ und das Kennwort „linux“. Bei Bedarf können Sie das Kennwort mit dem Befehl *passwd* ändern.

## <a name="7-configure-cluster-properties-and-resources"></a>7. Konfigurieren von Clustereigenschaften und -ressourcen 
In diesem Abschnitt werden die Schritte zum Konfigurieren von Clusterressourcen beschrieben.
In diesem Beispiel wurde die folgende Ressource eingerichtet. Der Rest kann (bei Bedarf) anhand des Hochverfügbarkeitsleitfadens für SUSE konfiguriert werden. Führen Sie die Konfiguration nur auf **einem der Knoten** aus. Führen Sie die folgende Konfiguration auf dem primären Knoten aus:

- Cluster-Bootstrap
- STONITH-Gerät
- Virtuelle IP-Adresse


### <a name="71-cluster-bootstrap-and-more"></a>7.1 Cluster-Bootstrap etc.
Fügen Sie ein Cluster-Bootstrap hinzu. Erstellen Sie die Datei, und fügen Sie den Text wie folgt hinzu:
```
sapprdhdb95:~ # vi crm-bs.txt
# enter the following to crm-bs.txt
property $id="cib-bootstrap-options" \
no-quorum-policy="ignore" \
stonith-enabled="true" \
stonith-action="reboot" \
stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
resource-stickiness="1000" \
migration-threshold="5000"
op_defaults $id="op-options" \
timeout="600"
```
Fügen Sie die Konfiguration zum Cluster hinzu.
```
crm configure load update crm-bs.txt
```
![Der Screenshot zeigt einen Teil eines Konsolenfensters, während der crm-Befehl ausgeführt wird.](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7.2 STONITH-Gerät
Fügen Sie das STONITH-Ressourcengerät hinzu. Erstellen Sie die Datei, und fügen Sie den Text wie folgt hinzu.
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15"
```
Fügen Sie die Konfiguration zum Cluster hinzu.
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7.3 Virtuelle IP-Adresse
Fügen Sie die virtuelle IP-Adresse der Ressource hinzu. Erstellen Sie die Datei, und fügen Sie den Text wie folgt hinzu.
```
# vi crm-vip.txt
primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
operations $id="rsc_ip_HA1_HDB10-operations" \
op monitor interval="10s" timeout="20s" \
params ip="10.35.0.197"
```
Fügen Sie die Konfiguration zum Cluster hinzu.
```
crm configure load update crm-vip.txt
```

### <a name="74-validate-the-resources"></a>7.4 Überprüfen Sie die Ressourcen.

Beim Ausführen des Befehls *crm_mon* werden die zwei Ressourcen dort angezeigt.
![Der Screenshot zeigt ein Konsolenfenster mit zwei Ressourcen.](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

Darüber hinaus können Sie den Status unter *https://\<node IP address>:7630/cib/live/state* sehen.

![Der Screenshot zeigt den Status der beiden Ressourcen.](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. Testen des Failoverprozesses
Um den Failoverprozess zu testen, beenden Sie den Pacemaker-Dienst auf node1 und das Ressourcenfailover auf node2.
```
Service pacemaker stop
```
Beenden Sie nun den Pacemaker-Dienst auf **node2** und die Ressourcen, für die ein Failover auf **node1** ausgeführt wurde.

**Vor dem Failover**  
![Der Screenshot zeigt den Status der beiden Ressourcen vor dem Failover.](media/HowToHLI/HASetupWithStonith/Before-failover.png)  

**Nach dem Failover**  
![Der Screenshot zeigt den Status der beiden Ressourcen nach dem Failover.](media/HowToHLI/HASetupWithStonith/after-failover.png)  
![Der Screenshot zeigt ein Konsolenfenster mit dem Status der Ressourcen nach dem Failover.](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  


## <a name="9-troubleshooting"></a>9. Problembehandlung
In diesem Abschnitt werden einige Fehlerzenarien beschrieben, die bei der Einrichtung auftreten können. Diese Probleme müssen nicht zwingend bei Ihnen auftreten.

### <a name="scenario-1-cluster-node-not-online"></a>Szenario 1: Der Clusterknoten ist nicht online.
Wenn der Knoten im Cluster-Manager nicht als online angezeigt wird, können Sie Folgendes versuchen, um ihn wieder online zu schalten.

Starten Sie den iSCSI-Dienst:
```
service iscsid start
```

Nun sollten Sie sich beim iSCSI-Knoten anmelden können.
```
iscsiadm -m node -l
```
Die erwartete Ausgabe sieht wie folgt aus:
```
sapprdhdb45:~ # iscsiadm -m node -l
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
```
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>Szenario 2: yast2 zeigt keine grafische Ansicht an.
In diesem Dokument wurde der grafische yast2-Bildschirm zum Konfigurieren des Hochverfügbarkeitsclusters verwendet. Wenn yast2 nicht wie gezeigt mit dem grafischen Fenster geöffnet und stattdessen ein Qt-Fehler ausgelöst wird, führen Sie die folgenden Schritte durch. Wenn es mit dem grafischen Fenster geöffnet wird, können Sie die Schritte überspringen.

**Fehler**

![Der Screenshot zeigt einen Teil eines Konsolenfensters mit einer Fehlermeldung.](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Erwartete Ausgabe**

![Der Screenshot zeigt das YaST Control Center mit hervorgehobenen Optionen „High Availability“ und „Cluster“.](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Wenn yast2 nicht mit der grafischen Ansicht geöffnet wird, führen Sie die folgenden Schritte durch.

Installieren Sie die erforderlichen Pakete. Sie müssen als „root“-Benutzer angemeldet sein und SMT für den Download bzw. die Installation der Pakete konfiguriert haben.

Navigieren Sie zum Installieren der Pakete zur Option „Empfohlene Pakete installieren...“ unter „yast“ > „Software“ > „Softwareverwaltung“ > „Abhängigkeiten“. Der folgende Screenshot zeigt die erwarteten Bildschirme.
>[!NOTE]
>Damit Sie über beide Knoten auf die grafische Ansicht von yast2 zugreifen können, müssen Sie die Schritte auf beiden Knoten durchführen.

![Der Screenshot zeigt ein Konsolenfenster, in dem das YaST Control Center angezeigt wird.](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

Wählen Sie unter „Abhängigkeiten“ die Option „Empfohlene Pakete installieren“ aus ![Der Screenshot zeigt ein Konsolenfenster mit ausgewählter Option „Empfohlene Pakete installieren“.](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Überprüfen Sie die Änderungen, und klicken Sie auf „OK“.

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

Die Paketinstallation wird fortgesetzt ![Der Screenshot zeigt ein Konsolenfenster, in dem der Status der Installation angezeigt wird.](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Klicken Sie auf „Weiter“.

![Der Screenshot zeigt ein Konsolenfenster mit einer Erfolgsmeldung.](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Klicken Sie auf „Fertig stellen“.

Sie müssen auch die Pakete „libqt4“ und „libyui“ installieren.
```
zypper -n install libqt4
```
![Der Screenshot zeigt ein Konsolenfenster zum Installieren des libqt4-Pakets.](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![Der Screenshot zeigt ein Konsolenfenster zum Installieren des libyui-qt-Pakets.](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![Der Screenshot zeigt die Fortsetzung der Installation des libyui-qt-Pakets.](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png)
YaST2 sollte nun in der Lage sein, die grafische Ansicht zu öffnen, wie hier gezeigt.
![Der Screenshot zeigt das YaST Control Center mit den ausgewählten Optionen „Software“ und „Online-Update“.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Szenario 3: yast2 zeigt keine Hochverfügbarkeitsoption an.
Damit die Hochverfügbarkeitsoption im Steuercenter von yast2 angezeigt wird, müssen Sie die zusätzlichen Pakete installieren.

Wählen Sie unter „Yast2“ > „Software“ > „Softwareverwaltung“ folgende Muster aus:

- SAP HANA-Serverbasis
- C/C++-Compiler und -Tools
- Hochverfügbarkeit
- SAP-Anwendungsserverbasis

Auf dem folgenden Bildschirm werden die Schritte zum Installieren der Muster angezeigt.

Diese finden Sie unter „yast2“ > „Software“ > „Softwareverwaltung“.

![Der Screenshot zeigt das YaST Control Center mit den ausgewählten Optionen „Software“ und „Online-Update“, um die Installation zu beginnen.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

Wählen Sie die Muster aus.

![Der Screenshot zeigt das Auswählen des ersten Musters im Element „C/C++-Compiler und Tools“.](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![Der Screenshot zeigt das Auswählen des zweiten Musters im Element „C/C++-Compiler und Tools“.](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Klicken Sie auf **Annehmen**.

![Der Screenshot zeigt das Dialogfeld „Geänderte Pakete“ mit geänderten Paketen zum Auflösen von Abhängigkeiten.](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Klicken Sie auf **Weiter**.

![Der Screenshot zeigt die Statusseite „Performing Installation“ (Installation wird durchgeführt).](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Klicken Sie nach Abschluss der Installation auf **Weiter**.

![Der Screenshot zeigt den Installationsbericht.](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Szenario 4: Bei der HANA-Installation tritt ein GCC-Assembly-Fehler auf.
Bei der HANA-Installation tritt folgender Fehler auf.

![Der Screenshot zeigt eine Fehlermeldung, dass das Betriebssystem nicht bereit ist, gcc5-Assemblys auszuführen.](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Um das Problem zu beheben, müssen Sie Bibliotheken (libgcc_sl und libstdc++6) wie folgt installieren.

![Der Screenshot zeigt ein Konsolenfenster von der Installation der erforderlichen Bibliotheken.](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Szenario 5: Beim Pacemaker-Dienst tritt ein Fehler auf.

Das folgende Problem tritt beim Start des Pacemaker-Diensts auf.

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: https://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

Um dies zu beheben, löschen Sie die folgende Zeile aus der Datei */usr/lib/systemd/system/fstrim.timer*.

```
Persistent=true
```

![Der Screenshot zeigt die fstrim-Datei mit dem zu löschenden Wert „Persistent=true“.](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Szenario 6: Node 2 kann nicht dem Cluster beitreten.

Wenn node2 über den Befehl *ha-cluster-join* mit dem vorhandenen Cluster verknüpft wird, tritt der folgende Fehler auf.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![Der Screenshot zeigt ein Konsolenfenster mit der Fehlermeldung „Die SSH-Schlüssel können nicht von einer IP-Adresse abgerufen werden“.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Um das Problem zu beheben, führen Sie Folgendes auf beiden Knoten aus.

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![Der Screenshot zeigt einen Teil eines Konsolenfensters während der Ausführung des Befehls auf dem ersten Knoten.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![Der Screenshot zeigt einen Teil eines Konsolenfensters während der Ausführung des Befehls auf dem zweiten Knoten.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Nach der vorangehenden Fehlerbehebung sollte node2 dem Cluster hinzugefügt werden.

![Der Screenshot zeigt ein Konsolenfenster mit einem erfolgreichen ha-cluster-join-Befehl.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Allgemeine Dokumentation
Weitere Informationen zur Hochverfügbarkeitskonfiguration unter SUSE finden Sie in folgenden Artikeln: 

- [SAP HANA SR Performance Optimized Scenario](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf ) (Leistungsoptimiertes SAP HANA-SR-Szenario)
- [Storage based fencing](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html) (Speicherbasierte Umgrenzung)
- [Blog – Using Pacemaker Cluster for SAP HANA- Part 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/) (Blog: Verwenden eines Pacemaker-Clusters für SAP Hana – Teil 1)
- [Blog – Using Pacemaker Cluster for SAP HANA- Part 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/) (Blog: Verwenden eines Pacemaker-Clusters für SAP Hana – Teil 2)
