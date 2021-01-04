---
title: Diagnosetool für die Problembehandlung von StorSimple 8000-Geräten | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Übersicht über das Diagnosetool, und es wird beschrieben, wie das Tool mit einem StorSimple-Gerät verwendet werden kann.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: bf0570cd62a2c329407cfb6dd087020f53cdc991
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954068"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>Verwenden des StorSimple-Diagnosetools für die Behandlung von Problemen mit Geräten der 8000-Serie

## <a name="overview"></a>Übersicht

Mit dem StorSimple-Diagnosetool können Probleme diagnostiziert werden, die sich auf das System, die Leistung, das Netzwerk und die Integrität der Hardwarekomponenten eines StorSimple-Geräts beziehen. Das Diagnosetool kann in verschiedenen Szenarien verwendet werden. Zu diesen Szenarien gehören die Workloadplanung, die Bereitstellung eines StorSimple-Geräts, die Bewertung der Netzwerkumgebung und die Ermittlung der Leistung eines betriebsbereiten Geräts. Dieser Artikel enthält eine Übersicht über das Diagnosetool, und es wird beschrieben, wie das Tool mit einem StorSimple-Gerät verwendet werden kann.

Das Diagnosetool ist hauptsächlich für lokale Geräte (8100 und 8600) der StorSimple 8000-Serie bestimmt.

## <a name="run-diagnostics-tool"></a>Ausführen des Diagnosetools

Dieses Tool kann über die Windows PowerShell-Schnittstelle Ihres StorSimple-Geräts ausgeführt werden. Es gibt zwei Möglichkeiten, auf die lokale Schnittstelle Ihres Geräts zuzugreifen:

* [Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)
* [Remotezugriff auf das Tool über die Windows PowerShell für StorSimple](storsimple-8000-remote-connect.md)

In diesem Artikel wird davon ausgegangen, dass Sie per PuTTY eine Verbindung mit der seriellen Gerätekonsole hergestellt haben.

#### <a name="to-run-the-diagnostics-tool"></a>So führen Sie das Diagnosetool aus

Führen Sie das Cmdlet mit den folgenden Schritten aus, nachdem Sie eine Verbindung mit der Windows PowerShell-Schnittstelle des Geräts hergestellt haben.
1. Melden Sie sich bei der seriellen Gerätekonsole an, indem Sie die Schritte unter [Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)ausführen.

2. Geben Sie folgenden Befehl ein:

    `Invoke-HcsDiagnostics`

    Wenn der Parameter „scope“ nicht angegeben ist, führt das Cmdlet alle Diagnosetests aus. Hierzu gehören Tests zu den Bereichen System, Integrität der Hardwarekomponenten, Netzwerk und Leistung. 
    
    Geben Sie den Parameter „scope“ an, um nur einen bestimmten Test durchzuführen. Geben Sie beispielsweise Folgendes ein, um nur den Netzwerktest durchzuführen:

    `Invoke-HcsDiagnostics -Scope Network`

3. Wählen Sie die Ausgabe des PuTTY-Fensters aus, und kopieren Sie sie für die weitere Analyse in eine Textdatei.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>Szenarien für die Verwendung eines Diagnosetools

Verwenden Sie das Diagnosetool, um die Problembehandlung für die Bereiche Netzwerk, Leistung, System und Hardwareintegrität des Systems durchzuführen. Hier sind einige mögliche Szenarien angegeben:

* **Gerät offline**: Ihr Gerät der StorSimple 8000-Serie ist offline. Über die Windows PowerShell-Schnittstelle sieht es aber so aus, als ob beide Controller betriebsbereit sind und ausgeführt werden.
    * Sie können dieses Tool verwenden, um anschließend den Netzwerkstatus zu ermitteln.
         
         > [!NOTE]
         > Verwenden Sie das Tool nicht, um die Leistung und Netzwerkeinstellungen auf einem Gerät vor der Registrierung zu bewerten (oder der Konfiguration per Setup-Assistent). Eine gültige IP-Adresse wird dem Gerät während des Vorgangs für den Setup-Assistenten und des Registrierungsvorgangs zugewiesen. Sie können dieses Cmdlet für die Hardwareintegrität und das System auf einem Gerät ausführen, das nicht registriert ist. Verwenden Sie den Parameter „scope“ beispielsweise wie folgt:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Dauerhafte Geräteprobleme**: Es kommt zu Geräteproblemen, die sich nicht beheben lassen. Dies kann beispielsweise ein Fehler bei der Registrierung sein. Geräteprobleme können auch auftreten, nachdem das Gerät erfolgreich registriert wurde und bereits ein Zeit lang betriebsbereit war.
    * Verwenden Sie das Tool in diesem Fall für die erste Problembehandlung, bevor Sie eine Serviceanfrage für den Microsoft-Support erstellen. Es ist ratsam, dieses Tool auszuführen und die Ausgabe des Tools zu erfassen. Diese Ausgabe können Sie dann für den Support bereitstellen, um die Problembehandlung zu beschleunigen.
    * Wenn Fehler mit Hardwarekomponenten oder Clustern auftreten, sollten Sie eine Anfrage an den Support stellen.

* **Niedrige Geräteleistung**: Ihr StorSimple-Gerät ist langsam.
    * Führen Sie das Cmdlet in diesem Fall so aus, dass der Parameter „scope“ auf „Performance“ (Leistung) festgelegt ist. Analysieren Sie die Ausgabe. Sie erhalten die Wartezeiten für die Lese- und Schreibvorgänge der Cloud. Verwenden Sie die gemeldeten Wartezeiten als maximal erreichbares Ziel, rechnen Sie etwas Zusatzaufwand für die interne Datenverarbeitung ein, und stellen Sie die Workloads dann im System bereit. Weitere Informationen finden Sie unter [Netzwerktest](#network-test).


## <a name="diagnostics-test-and-sample-outputs"></a>Diagnosetest und Beispielausgaben

### <a name="hardware-test"></a>Hardwaretest

Mit diesem Test wird der Status der Hardwarekomponenten, der USM-Firmware und der Datenträgerfirmware Ihres Systems ermittelt.

* Die gemeldeten Hardwarekomponenten sind die Komponenten, die den Test nicht bestanden haben oder im System nicht vorhanden sind.
* Die Versionen der USM-Firmware und der Datenträgerfirmware werden für Controller 0, Controller 1 und die gemeinsam genutzten Komponenten Ihres Systems gemeldet. Eine vollständige Liste mit den Hardwarekomponenten finden Sie unter:

    * [Komponentenliste für das primäre Gehäuse eines StorSimple-Geräts](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Komponentenliste für das EBOD-Gehäuse eines StorSimple-Geräts](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Falls beim Hardwaretest Komponenten mit Fehlern gemeldet werden, [können Sie sich an den Microsoft-Support für StorSimple](storsimple-8000-contact-microsoft-support.md) wenden.

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>Beispielausgabe des Hardwaretestlaufs auf einem 8100-Gerät

Hier ist eine Beispielausgabe eines StorSimple 8100-Geräts angegeben. Das 8100-Gerät verfügt nicht über das EBOD-Gehäuse. Daher werden die EBOD-Controllerkomponenten nicht gemeldet.

```
Controller0>Invoke-HcsDiagnostics -Scope Hardware
Running hardware diagnostics ...
--------------------------------------------------
Hardware components failed or not present
----------------------

           Type           State      Controller           Index     EnclosureId
           ----           -----      ----------           -----     -----------
...rVipResource      NotPresent            None               1            None
...rVipResource      NotPresent            None               2            None
...rVipResource      NotPresent            None               3            None
...rVipResource      NotPresent            None               4            None
...rVipResource      NotPresent            None               5            None
...rVipResource      NotPresent            None               6            None
...rVipResource      NotPresent            None               7            None
...rVipResource      NotPresent            None               8            None
...rVipResource      NotPresent            None               9            None
...rVipResource      NotPresent            None              10            None
...rVipResource      NotPresent            None              11            None

Firmware information
----------------------
TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

--------------------------------------------------
```

### <a name="system-test"></a>Systemtest

Bei diesem Test werden die Systeminformationen, die verfügbaren Updates, die Clusterinformationen und die Dienstinformationen Ihres Geräts gemeldet.

* Die Systeminformationen umfassen das Modell, die Seriennummer des Geräts, die Zeitzone, den Controllerstatus und die Details zur Softwareversion, die im System ausgeführt wird. Informationen zu den verschiedenen Systemparametern, die als Ausgabe gemeldet werden, finden Sie unter [Interpretieren von Systeminformationen](#appendix-interpreting-system-information).

* Im Abschnitt zur Updateverfügbarkeit wird gemeldet, ob der normale Modus und der Wartungsmodus verfügbar sind und wie die Namen der zugeordneten Pakete lauten. Wenn `RegularUpdates` und `MaintenanceModeUpdates` auf `false` festgelegt sind, weist dies darauf hin, dass die Updates nicht verfügbar sind. Ihr Gerät ist auf dem neuesten Stand.
* Die Clusterinformationen enthalten Informationen zu verschiedenen logischen Komponenten aller HCS-Clustergruppen und des jeweiligen Status. Wenn in diesem Abschnitt des Berichts angezeigt wird, dass eine Clustergruppe offline ist, sollten Sie sich [an den Microsoft-Support wenden](storsimple-8000-contact-microsoft-support.md).
* Die Dienstinformationen enthalten den Namen und die Status aller HCS- und CiS-Dienste, die auf dem Gerät ausgeführt werden. Diese Informationen dienen dem Microsoft-Support als Hilfe beim Behandeln des Geräteproblems.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>Beispielausgabe des Systemtestlaufs auf einem 8100-Gerät

Hier ist eine Beispielausgabe des Systemtestlaufs auf einem 8100-Gerät angegeben.

```
Controller0>Invoke-HcsDiagnostics -Scope System
Running system diagnostics ...
--------------------------------------------------

System information
----------------------
Controller0:

InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                : (UTC-08:00) Pacific Time (US & Canada)
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : Disabled
FipsMode                : Enabled

Controller1:
InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                :
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : HttpsAndHttpEnabled
FipsMode                : Enabled

Update availability
----------------------
RegularUpdates              : False
MaintenanceModeUpdates      : False
RegularUpdatesTitle         : {}
MaintenanceModeUpdatesTitle : {}

Cluster information
----------------------
Name                          State OwnerGroup
----                          ----- ----------
ApplicationHostRLUA           Online HCS Cluster Group
Data0v4                       Online HCS Cluster Group
HCS Vnic Resource             Online HCS Cluster Group
hcs_cloud_connectivity_...    Online HCS Cluster Group
hcs_controller_replacement    Online HCS Cluster Group
hcs_datapath_service          Online HCS Cluster Group
hcs_management_service        Online HCS Cluster Group
hcs_nvram_service             Online HCS Cluster Group
hcs_passive_datapath          Online HCS Passive Cluster Group
hcs_platform_service          Online HCS Cluster Group
hcs_saas_agent_service        Online HCS Cluster Group
HddDataClusterDisk            Online HCS Cluster Group
HddMgmtClusterDisk            Online HCS Cluster Group
HddReplClusterDisk            Online HCS Cluster Group
iSCSI Target Server           Online HCS Cluster Group
NvramClusterDisk              Online HCS Cluster Group
SSAdminRLUA                   Online HCS Cluster Group
SsdDataClusterDisk            Online HCS Cluster Group
SsdNvramClusterDisk           Online HCS Cluster Group

Service information
----------------------
Name                                          Status DisplayName
----                                          ------ -----------
CiSAgentSvc                                   Stopped CiS Service Agent
hcs_cloud_connectivity_...                    Running hcs_cloud_connectivity...
hcs_controller_replacement                    Running HCS Controller Replace...
hcs_datapath_service                          Running HCS Datapath Service
hcs_management_service                        Running HCS Management Service
hcs_minishell                                 Running hcs_minishell
HCS_NVRAM_Service                             Running HCS NVRAM Service
hcs_passive_datapath                          Stopped HCS Passive Datapath S...
hcs_platform_service                          Running HCS Platform Monitor S...
hcs_saas_agent_service                        Running hcs_saas_agent_service
hcs_startup                                   Stopped hcs_startup
--------------------------------------------------
```

### <a name="network-test"></a>Netzwerktest

Bei diesem Test wird Folgendes überprüft: Status der Netzwerkschnittstellen, Ports, DNS- und NTP-Serverkonnektivität, TLS/SSL-Zertifikat, Anmeldeinformationen des Speicherkontos, Konnektivität mit den Updateservern und Webproxykonnektivität auf Ihrem StorSimple-Gerät.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Beispielausgabe des Netzwerktests, wenn nur DATA0 aktiviert ist

Hier ist eine Beispielausgabe des 8100-Geräts angegeben. In der Ausgabe ist Folgendes zu sehen:
* Nur DATA0- und DATA1-Netzwerkschnittstellen sind aktiviert und konfiguriert.
* DATA2 bis DATA5 sind im Portal nicht aktiviert.
* Die DNS-Serverkonfiguration ist gültig, und das Gerät kann über den DNS-Server eine Verbindung herstellen.
* Die NTP-Serverkonnektivität ist ebenfalls in Ordnung.
* Die Ports 80 und 443 sind offen. Port 9354 ist dagegen blockiert. Gemäß den [Netzwerkanforderungen des Systems](./storsimple-8000-system-requirements.md) ist es erforderlich, dass Sie diesen Port für die Service Bus-Kommunikation öffnen.
* Das TLS/SSL-Zertifikat ist gültig.
* Das Gerät kann eine Verbindung mit dem Speicherkonto herstellen: _myss8000storageacct_.
* Die Konnektivität mit den Updateservern ist gültig.
* Der Webproxy ist auf diesem Gerät nicht konfiguriert.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>Beispielausgabe des Netzwerktests bei Aktivierung von DATA0 und DATA1

```
Controller0>Invoke-HcsDiagnostics -Scope Network
Running network diagnostics ....
--------------------------------------------------
Validating networks .....
Name                Entity              Result              Details
----                ------              ------              -------
Network interface   Data0               Valid
Network interface   Data1               Valid
Network interface   Data2               Not enabled
Network interface   Data3               Not enabled
Network interface   Data4               Not enabled
Network interface   Data5               Not enabled
DNS                 10.222.118.154      Valid
NTP                 time.windows.com    Valid
Port                80                  Open
Port                443                 Open
Port                9354                Blocked
SSL certificate     https://myss8000... Valid
Storage account ... myss8000storageacct Valid
URL                 http://download.... Valid
URL                 http://download.... Valid
Web proxy                               Not enabled         Web proxy is not...
--------------------------------------------------
```

### <a name="performance-test"></a>Leistungstest

Bei diesem Test wird die Cloudleistung über die Wartezeit von Lese-/Schreibvorgängen der Cloud für Ihr Gerät gemeldet. Dieses Tool kann zum Einrichten einer „Baseline“ für die Cloudleistung verwendet werden, die Sie mit StorSimple erreichen können. Das Tool meldet die maximale Leistung (bestes Szenario für Lese-/Schreibwartezeiten), die Sie für Ihre Verbindung erzielen können.

Da vom Tool die maximal erreichbare Leistung gemeldet wird, können wir beim Bereitstellen der Workloads die gemeldeten Lese-/Schreibwartezeiten als Ziele verwenden.

Der Test simuliert die Blobgrößen, die den unterschiedlichen Volumentypen auf dem Gerät zugeordnet sind. Für reguläre mehrstufige Volumes und Sicherungen von lokalen Volumes wird eine Blobgröße von 64 KB verwendet. Für mehrstufige Volumes mit aktivierter Archivierungsoption wird eine Blobdatengröße von 512 KB verwendet. Wenn für Ihr Gerät mehrstufige und lokale Volumes konfiguriert sind, wird nur der Test für die Blobdatengröße von 64 KB ausgeführt.

Führen Sie die folgenden Schritte aus, um dieses Tool zu verwenden:

1.  Erstellen Sie zunächst eine Mischung aus mehrstufigen Volumes und mehrstufigen Volumes mit aktivierter Archivierungsoption. Mit dieser Aktion wird sichergestellt, dass das Tool die Tests für die Blobgrößen 64 KB und 512 KB durchführt.

2. Führen Sie das Cmdlet aus, nachdem Sie die Volumes erstellt und konfiguriert haben. Typ:

    `Invoke-HcsDiagnostics -Scope Performance`

3. Notieren Sie sich die vom Tool gemeldeten Lese-/Schreibwartezeiten. Die Ausführung dieses Tests kann mehrere Minuten dauern, bevor die Ergebnisse gemeldet werden.

4. Wenn die Verbindungswartezeiten allesamt unterhalb des erwarteten Bereichs liegen, können die vom Tool gemeldeten Wartezeiten beim Bereitstellen der Workloads als maximal erreichbares Ziel verwendet werden. Rechnen Sie etwas Zusatzaufwand für die interne Datenverarbeitung ein.

    Gehen Sie wie folgt vor, wenn die vom Diagnosetool gemeldeten Lese-/Schreiblatenzen hoch sind:

    1. Konfigurieren Sie Storage Analytics für Blobdienste, und analysieren Sie die Ausgabe, um die Wartezeiten für das Azure-Speicherkonto zu verstehen. Eine ausführliche Anleitung finden Sie unter [Aktivieren der Speichermetriken und Anzeigen von Metrikdaten](../storage/blobs/monitor-blob-storage.md). Falls diese Wartezeiten ebenfalls hoch und mit den Zahlen vergleichbar sind, die Sie über das StorSimple-Diagnosetool erhalten haben, sollten Sie eine Serviceanfrage in Bezug auf den Azure-Speicher stellen.

    2. Wenn die Speicherkonto-Wartezeiten niedrig sind, können Sie sich an Ihren Netzwerkadministrator wenden, um die Wartezeitprobleme in Ihrem Netzwerk untersuchen zu lassen.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>Beispielausgabe eines Leistungstests auf einem 8100-Gerät

```
Controller0>Invoke-HcsDiagnostics -Scope Performance
Running performance diagnostics...
--------------------------------------------------
Cloud performance: writing blobs
Cloud write latency: 194 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: reading blobs..
Cloud read latency: 544 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: writing blobs.
Cloud write latency: 369 ms using credential 'myss8000storageacct', blob size '512KB'
Cloud performance: reading blobs...
Cloud read latency: 4924 ms using credential 'myss8000storageacct', blob size '512KB'
--------------------------------------------------
Controller0>
```

## <a name="appendix-interpreting-system-information"></a>Anhang: Interpretieren von Systeminformationen

In der Tabelle unten wird beschrieben, wie die verschiedenen Windows PowerShell-Parameter in den Systeminformationen zugeordnet werden. 

| PowerShell-Parameter    | BESCHREIBUNG  |
|-------------------------|------------------|
| Instance ID             | Jedem Controller ist ein eindeutiger Bezeichner oder eine GUID zugeordnet.|
| Name                    | Dies ist der Anzeigename des Geräts gemäß Konfiguration über das Azure-Portal während der Gerätebereitstellung. Der Standardanzeigename ist die Seriennummer des Geräts. |
| Modell                   | Das Modell Ihres Geräts der StorSimple 8000-Serie. Die Modellnummer kann 8100 oder 8600 lauten.|
| SerialNumber            | Die Seriennummer des Geräts wird im Werk zugewiesen und ist 15 Zeichen lang. 8600-SHX0991003G44HT bedeutet beispielsweise Folgendes:<br> 8600 – Gerätemodell<br>SHX – Fertigungsstandort<br> 0991003 – Bestimmtes Produkt <br> G44HT– Die letzten 5 Stellen werden inkrementiert, um eindeutige Seriennummern zu erstellen. Unter Umständen ist dies keine sequenzielle Reihe.|
| TimeZone                | Die Zeitzone des Geräts gemäß Konfiguration im Azure-Portal während der Gerätebereitstellung.|
| CurrentController       | Der Controller, mit dem Sie über die Windows PowerShell-Schnittstelle Ihres StorSimple-Geräts verbunden sind.|
| ActiveController        | Der Controller, der auf Ihrem Gerät aktiv ist und alle Netzwerk- und Datenträgervorgänge steuert. Dies kann der Controller 0 oder der Controller 1 sein.  |
| Controller0Status       | Der Status von Controller 0 auf Ihrem Gerät. Der Controller kann folgende Status haben: „Normal“, „Wiederherstellungsmodus“ oder „Nicht erreichbar“.|
| Controller1Status       | Der Status von Controller 1 auf Ihrem Gerät.  Der Controller kann folgende Status haben: „Normal“, „Wiederherstellungsmodus“ oder „Nicht erreichbar“.|
| SystemMode              | Der allgemeine Status Ihres StorSimple-Geräts. Das Gerät kann den Status „Normal“, „Wartung“ oder „Außer Betrieb“ (entspricht „Deaktiviert“ im Azure-Portal) haben.|
| FriendlySoftwareVersion | Die benutzerfreundliche Zeichenfolge, die der Version der Gerätesoftware entspricht. Für ein System mit Update 4 lautet die benutzerfreundliche Softwareversion beispielsweise „StorSimple 8000 Series Update 4.0“ (Update 4.0 der StorSimple 8000-Serie).|
| HcsSoftwareVersion      | Die Version der HCS-Software, die auf Ihrem Gerät ausgeführt wird. Die HCS-Softwareversion für Update 4.0 der StorSimple 8000-Serie lautet beispielsweise 6.3.9600.17820. |
| ApiVersion              | Die Softwareversion der Windows PowerShell-API des HCS-Geräts.|
| VhdVersion              | Die Softwareversion des werkseitigen Image, mit dem das Gerät geliefert wurde. Wenn Sie Ihr Gerät auf die Werkseinstellungen zurücksetzen, wird diese Softwareversion ausgeführt.|
| OSVersion               | Die Softwareversion des Windows Server-Betriebssystems, die auf dem Gerät ausgeführt wird. Das StorSimple-Gerät basiert auf Windows Server 2012 R2. Dies entspricht 6.3.9600.|
| CisAgentVersion         | Die Version des Cis-Agents, der auf Ihrem StorSimple-Gerät ausgeführt wird. Dieser Agent dient als Unterstützung der Kommunikation mit dem StorSimple Manager-Dienst, der in Azure ausgeführt wird.|
| MdsAgentVersion         | Die Version des Mds-Agents, der auf Ihrem StorSimple-Gerät ausgeführt wird. Mit diesem Agent werden die Daten in den Dienst für die Überwachung und Diagnose (Monitoring and Diagnostics Service, MDS) verschoben.|
| Lsisas2Version          | Die Version des LSI-Treibers auf Ihrem StorSimple-Gerät.|
| Capacity                | Die Gesamtkapazität des Geräts in Byte.|
| RemoteManagementMode    | Gibt an, ob das Gerät über die Windows PowerShell-Schnittstelle per Remotezugriff verwaltet werden kann. |
| FipsMode                | Gibt an, ob der US-FIPS-Modus (United States Federal Information Processing Standard) auf Ihrem Gerät aktiviert ist. Der FIPS 140-Standard definiert kryptographische Algorithmen, die zum Schutz vertraulicher Daten in Computersystemen der US-Regierung zugelassen sind. Für Geräte, auf denen Update 4 oder höher ausgeführt wird, ist der FIPS-Modus standardmäßig aktiviert. |

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über die [Syntax des Invoke-HcsDiagnostics-Cmdlets](/previous-versions/windows/powershell-scripting/mt795371(v=wps.630)).

* Lesen Sie sich weitere Informationen dazu durch, wie Sie die [Behandlung von Bereitstellungsproblemen](./storsimple-8000-troubleshoot-deployment.md) auf Ihrem StorSimple-Gerät durchführen.