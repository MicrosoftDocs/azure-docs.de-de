---
title: Installieren des Log Analytics-Agents auf Linux-Computern
description: In diesem Artikel wird beschrieben, wie Linux-Computer, die in anderen Clouds oder lokal gehostet werden, über den Log Analytics-Agent für Linux mit Azure Monitor verbunden werden können.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: e1dbf5e20aa206189397cab26e9b867f4942e1d5
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886837"
---
# <a name="install-log-analytics-agent-on-linux-computers"></a>Installieren des Log Analytics-Agents auf Linux-Computern
Dieser Artikel enthält ausführliche Informationen zum Installieren des Log Analytics-Agents auf Linux-Computern mithilfe der folgenden Methoden:

* [Installieren Sie den Agent für Linux mithilfe eines Wrapperskripts](#install-the-agent-using-wrapper-script), das auf GitHub gehostet wird. Dies ist die empfohlene Methode für Installation und Upgrade des Agents, wenn der Computer direkt oder über einen Proxyserver mit dem Internet verbunden ist.
* [Laden Sie den Agent manuell herunter, und installieren Sie ihn](#install-the-agent-manually). Dies ist erforderlich, wenn der Linux-Computer keinen Zugriff auf das Internet hat und über das [Log Analytics-Gateway](gateway.md) mit Azure Monitor oder Azure Automation kommuniziert. 

>[!IMPORTANT]
> Die in diesem Artikel beschriebenen Installationsmethoden werden in der Regel für virtuelle Computer in der lokalen Umgebung oder in anderen Clouds verwendet. Effizientere Optionen für virtuelle Azure-Computer finden Sie unter [Installationsoptionen](log-analytics-agent.md#installation-options).



## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

Eine Liste der Linux-Distributionen, die vom Log Analytics-Agent unterstützt werden, finden Sie unter [Übersicht über Azure Monitor-Agents](agents-overview.md#supported-operating-systems).

>[!NOTE]
>OpenSSL 1.1.0 wird nur auf x86_x64-Plattformen (64 Bit) unterstützt. OpenSSL vor Version 1.x wird auf keiner Plattform unterstützt.

>[!NOTE]
>Das Ausführen des Log Analytics-Linux-Agents in Containern wird nicht unterstützt. Verwenden Sie zur Überwachung von Containern die [Containerüberwachungslösung](../insights/containers.md) (für Docker-Hosts) oder [Azure Monitor für Container](../insights/container-insights-overview.md) (für Kubernetes).

Beginnend mit den nach August 2018 veröffentlichten Versionen gelten folgende Änderungen für unser Supportmodell:  

* Es werden nur die Serverversionen und keine Clientversionen unterstützt.  
* Die Unterstützung konzentriert sich auf die [von Azure unterstützten Linux-Distributionen](../../virtual-machines/linux/endorsed-distros.md). Beachten Sie, dass möglicherweise eine Verzögerung zwischen einer neuen von Azure unterstützten Linux-Distribution/Version und deren Unterstützung für den Log Analytics Linux-Agent besteht.
* Alle Nebenversionen werden für jede aufgeführte Hauptversion unterstützt.
* Versionen, für die der Support des Herstellers abgelaufen ist, werden nicht unterstützt.
* Es werden nur VM-Images unterstützt. Container werden nicht unterstützt (auch nicht, wenn sie auf Images von offiziellen Distributionsherausgebern basieren).
* Neue Versionen von AMI werden nicht unterstützt.  
* Nur Versionen, die standardmäßig SSL 1.x ausführen, werden unterstützt.

>[!NOTE]
>Wenn Sie eine Distribution oder eine Version verwenden, die derzeit nicht unterstützt wird und nicht auf unser Supportmodell abgestimmt ist, wird empfohlen, dieses Repository zu forken und dabei anzuerkennen, dass der Microsoft-Support bei geforkten Agent-Versionen keine Unterstützung bietet.

### <a name="python-requirement"></a>Python-Anforderung

Ab Agent-Version 1.13.27 unterstützt der Linux-Agent sowohl Python 2 als auch Python 3. Es wird immer empfohlen, den neuesten Agent zu verwenden. 

Wenn Sie eine ältere Version des Agents nutzen, muss der virtuelle Computer standardmäßig Python 2 verwenden. Wenn Ihr virtueller Computer eine Distribution verwendet, in der Python 2 nicht standardmäßig enthalten ist, müssen Sie die Sprache installieren. Mithilfe der folgenden Beispielbefehle wird Python 2 auf verschiedenen Distributionen installiert.

 - Red Hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - SUSE: `zypper install -y python2`

Die ausführbare python2-Datei muss dem Alias *python* zugewiesen werden. Mit der folgenden Methode können Sie diesen Alias festlegen:

1. Führen Sie den folgenden Befehl aus, um eventuell vorhandene Aliase zu entfernen.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Führen Sie den folgenden Befehl aus, um den Alias zu erstellen.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

## <a name="supported-linux-hardening"></a>Unterstützte Linux-Härtung
Der OMS-Agent verfügt über eingeschränkte Anpassungsunterstützung für Linux. 

Folgendes wird derzeit unterstützt: 
- FIPs

Folgendes wird in Erwägung gezogen, aber noch nicht unterstützt:
- CIS
- SELINUX

Andere Härtungs- und Anpassungsmethoden werden für den OMS-Agent weder unterstützt noch geplant.  

## <a name="agent-prerequisites"></a>Agent-Voraussetzungen

In der folgenden Tabelle sind die erforderlichen Pakete für [unterstützte Linux-Distributionen](#supported-operating-systems), auf denen der Agent installiert wird, hervorgehoben.

|Erforderliches Paket |BESCHREIBUNG |Mindestversion |
|-----------------|------------|----------------|
|Glibc |    GNU C-Bibliothek | 2.5-12 
|Openssl    | OpenSSL-Bibliotheken | 1.0.x oder 1.1.x |
|Curl | cURL-Webclient | 7.15.5 |
|Python | | 2.6+ oder 3.3+
|Python-ctypes | | 
|PAM | Module für austauschbare Authentifizierung | | 

>[!NOTE]
>Zum Sammeln von syslog-Nachrichten sind entweder rsyslog oder syslog-ng erforderlich. Der Standard-syslog-Daemon in Version 5 von Red Hat Enterprise Linux, CentOS und Oracle Linux-Version (sysklog) wird für die syslog-Ereigniserfassung nicht unterstützt. Der rsyslog-Daemon sollte installiert und so konfiguriert werden, dass er sysklog ersetzt, um syslog-Daten von dieser Version dieser Verteilung zu sammeln.

## <a name="network-requirements"></a>Netzwerkanforderungen
Die Netzwerkanforderungen für den Linux-Agent finden Sie unter [Übersicht über den Log Analytics-Agent](log-analytics-agent.md#network-requirements).

## <a name="agent-install-package"></a>Agent-Installationspaket

Der Log Analytics-Agent für Linux besteht aus mehreren Paketen. Die Releasedatei enthält die folgenden Pakete, die durch Ausführen des Shell-Pakets mit dem Parameter `--extract` verfügbar sind:

**Paket** | **Version** | **Beschreibung**
----------- | ----------- | --------------
omsagent | 1.13.9 | Der Log Analytics-Agent für Linux
omsconfig | 1.1.1 | Konfigurations-Agent für den Log Analytics-Agent
omi | 1.6.4 | Open Management Infrastructure (OMI) – ein kompakter CIM-Server. *Beachten Sie, dass OMI Stammzugriff erfordert, um einen Cron-Auftrag auszuführen, der für das Funktionieren des Diensts erforderlich ist.*
scx | 1.6.4 | OMI-CIM-Anbieter für Leistungsmetriken für das Betriebssystem
apache-cimprov | 1.0.1 | Apache HTTP Server-Anbieter für die Leistungsüberwachung für OMI Nur installiert, wenn Apache HTTP Server erkannt wird.
mysql-cimprov | 1.0.1 | MySQL Server-Anbieter für die Leistungsüberwachung für OMI Nur installiert, wenn MySQL/MariaDB-Server erkannt wird.
docker-cimprov | 1.0.0 | Docker-Anbieter für OMI. Nur installiert, wenn Docker erkannt wird.

### <a name="agent-installation-details"></a>Agent-Installationsdetails

Nach der Installation der Pakete für den Log Analytics-Agent für Linux werden die folgenden zusätzlichen systemweiten Konfigurationsänderungen angewendet. Diese Artefakte werden entfernt, wenn das „omsagent“-Paket deinstalliert wird.

* Ein Benutzer ohne Berechtigungen mit dem Namen `omsagent` wird erstellt. Der Daemon wird unter diesen Anmeldeinformationen ausgeführt. 
* Eine sudoers-*include*-Datei wird in `/etc/sudoers.d/omsagent` erstellt. Diese autorisiert `omsagent` dazu, die syslog- und omsagent-Daemons neu zu starten. Falls sudo-*include*-Direktiven in der installierten sudo-Version nicht unterstützt werden, werden diese Einträge in `/etc/sudoers` geschrieben.
* Die syslog-Konfiguration wird geändert, um eine Teilmenge von Ereignissen an den Agent weiterzuleiten. Weitere Informationen finden Sie unter [Konfigurieren der Syslog-Datensammlung](data-sources-syslog.md).

Auf einem überwachten Linux-Computer wird der Agent als `omsagent` aufgeführt. `omsconfig` ist der Konfigurations-Agent für den Log Analytics-Agent für Linux, der alle fünf Minuten nach einer neuen im Portal erstellten Konfiguration sucht. Die neue und aktualisierte Konfiguration wird auf die Agent-Konfigurationsdateien unter `/etc/opt/microsoft/omsagent/conf/omsagent.conf` angewendet.

## <a name="install-the-agent-using-wrapper-script"></a>Installieren des Agents mithilfe eines Wrapperskripts

Mit den folgenden Schritten konfigurieren Sie die Einrichtung des Agents für Log Analytics in Azure und Azure Government Cloud mithilfe des Wrapperskripts für Linux-Computer, die direkt oder über einen Proxyserver kommunizieren können, um den auf GitHub gehosteten Agent herunterzuladen und zu installieren.  

Wenn Ihr Linux-Computer über einen Proxyserver mit Log Analytics kommunizieren muss, kann diese Konfiguration in der Befehlszeile durch den Zusatz `-p [protocol://][user:password@]proxyhost[:port]` angegeben werden. Für die *protocol*-Eigenschaft kann `http` oder `https` und für die *proxyhost*-Eigenschaft kann ein vollqualifizierter Domänenname oder eine IP-Adresse des Proxyservers verwendet werden. 

Beispiel: `https://proxy01.contoso.com:30443`

Wenn in beiden Fällen eine Authentifizierung erforderlich ist, müssen Sie den Benutzernamen und das Kennwort angeben. Beispiel: `https://user01:password@proxy01.contoso.com:30443`

1. Führen Sie den folgenden Befehl aus, und stellen Sie die Arbeitsbereichs-ID sowie den Primärschlüssel bereit, um den Linux-Computer so zu konfigurieren, dass er eine Verbindung mit einem Log Analytics-Arbeitsbereich herstellt. Mit dem folgenden Befehl wird der Agent heruntergeladen, die Prüfsumme des Agents überprüft und anschließend der Agent installiert.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Der folgende Befehl enthält den `-p`-Proxyparameter und die Beispielsyntax für den Fall, dass die Authentifizierung für Ihren Proxyserver erforderlich ist:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Führen Sie den folgenden Befehl aus, und stellen Sie die zuvor kopierte Arbeitsbereichs-ID sowie den Primärschlüssel bereit, um den Linux-Computer so zu konfigurieren, dass er eine Verbindung mit einem Log Analytics-Arbeitsbereich in Azure Government Cloud herstellt. Mit dem folgenden Befehl wird der Agent heruntergeladen, die Prüfsumme des Agents überprüft und anschließend der Agent installiert. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Der folgende Befehl enthält den `-p`-Proxyparameter und die Beispielsyntax für den Fall, dass die Authentifizierung für Ihren Proxyserver erforderlich ist:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Starten Sie den Agent neu, indem Sie den folgenden Befehl ausführen: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 


## <a name="install-the-agent-manually"></a>Manuelles Installieren des Agents

Der Log Analytics-Agent für Linux wird in einem selbstextrahierenden und installierbaren Shell-Skriptpaket bereitgestellt. Dieses Paket enthält Debian- und RPM-Pakete für jede Komponente des Agents und kann direkt installiert oder zum Abrufen der einzelnen Pakete extrahiert werden. Es wird ein Paket für x64-Architekturen und eines für x86-Architekturen bereitgestellt. 

> [!NOTE]
> Bei Azure-VMs wird empfohlen, den Agent mithilfe der [Azure Log Analytics VM-Erweiterung](../../virtual-machines/extensions/oms-linux.md) für Linux auf den Computern zu installieren. 

1. [Laden Sie das entsprechende Paket (x64 oder x86) herunter](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide), und übertragen Sie es mithilfe von scp/sftp auf Ihren virtuellen oder physischen Linux-Computer.

2. Installieren Sie das Paket mit dem Argument `--install`. Wenn Sie während der Installation die Integration in einen Log Analytics-Arbeitsbereich vornehmen möchten, geben Sie die zuvor kopierten Parameter `-w <WorkspaceID>` und `-s <workspaceKey>` an.

    >[!NOTE]
    >Sie müssen das Argument `--upgrade` verwenden, falls abhängige Pakete wie omi, scx, omsconfig oder deren ältere Versionen installiert sind. Dies wäre dann der Fall, wenn der System Center Operations Manager-Agent für Linux bereits installiert ist. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Wenn Sie den Linux-Agent so konfigurieren möchten, dass er über ein Log Analytics-Gateway installiert wird und eine Verbindung mit einem Log Analytics-Arbeitsbereich herstellt, führen Sie den folgenden Befehl unter Angabe der Parameter für Proxy, Arbeitsbereichs-ID und Arbeitsbereichsschlüssel aus. Diese Konfiguration kann in der Befehlszeile durch den Zusatz `-p [protocol://][user:password@]proxyhost[:port]` angegeben werden. Für die *proxyhost*-Eigenschaft kann ein vollqualifizierter Domänenname oder eine IP-Adresse des Log Analytics-Gatewayservers verwendet werden.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Wenn eine Authentifizierung erforderlich ist, müssen Sie den Benutzernamen und das Kennwort angeben. Beispiel: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Führen Sie den folgenden Befehl aus, und stellen Sie die zuvor kopierte Arbeitsbereichs-ID sowie den Primärschlüssel bereit, um den Linux-Computer so zu konfigurieren, dass er eine Verbindung mit einem Log Analytics-Arbeitsbereich in Azure Government Cloud herstellt.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Wenn Sie die Agent-Pakete installieren und so konfigurieren möchten, dass Berichte zu einem späteren Zeitpunkt an einen bestimmten Log Analytics-Arbeitsbereich übermittelt werden, führen Sie den folgenden Befehl aus:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Wenn Sie die Agent-Pakete aus dem Paket extrahieren möchten, ohne den Agent zu installieren, führen Sie den folgenden Befehl aus:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="upgrade-from-a-previous-release"></a>Upgrade von einem früheren Release

Das Upgrade von einer früheren Version wird ab Version 1.0.0-47 bei jedem Release unterstützt. Führen Sie die Installation mit dem Parameter `--upgrade` aus, um ein Upgrade aller Komponenten des Agents auf die neueste Version vorzunehmen.

## <a name="cache-information"></a>Informationen zum Cache
Daten vom Log Analytics-Agent für Linux werden auf dem lokalen Computer unter „ *%STATE_DIR_WS%/out_oms_common*.buffer*“ zwischengespeichert, bevor sie an Azure Monitor gesendet werden. Benutzerdefinierte Protokolldaten werden unter „ *%STATE_DIR_WS%/out_oms_blob*.buffer*“ gepuffert. Der Pfad unterscheidet sich möglicherweise für einige [Lösungen und Datentypen](https://github.com/microsoft/OMS-Agent-for-Linux/search?utf8=%E2%9C%93&q=+buffer_path&type=).

Der Agent versucht den Upload alle 20 Sekunden. Wenn ein Fehler auftritt, wird bis zum erfolgreichen Abschluss jeweils eine exponentiell zunehmende Zeitspanne gewartet: 30 Sekunden vor dem zweiten Versuch, 60 Sekunden vor dem dritten Versuch, 120 Sekunden ... usw. bis maximal 16 Minuten zwischen Wiederholungen, bis wieder eine Verbindung hergestellt wurde. Der Agent führt für einen bestimmten Datenblock bis zu sechs Wiederholungsversuche aus, bevor er ihn verwirft und zum nächsten wechselt. Dies wird fortgesetzt, bis der Agent wieder erfolgreich einen Upload ausführen kann. Das bedeutet, dass Daten bis zu etwa 30 Minuten gepuffert werden können, bevor sie verworfen werden.

Die Standardcachegröße beträgt 10 MB, dies kann aber in der Datei [omsagent.conf](https://github.com/microsoft/OMS-Agent-for-Linux/blob/e2239a0714ae5ab5feddcc48aa7a4c4f971417d4/installer/conf/omsagent.conf) geändert werden.


## <a name="next-steps"></a>Nächste Schritte

- Informationen zu Neukonfiguration, Upgrade oder Entfernen des Agents auf dem virtuellen Computer finden Sie unter [Verwalten und Warten des Log Analytics-Agents für Windows und Linux](agent-manage.md).

- Lesen Sie [Problembehandlung für den Linux-Agent](agent-linux-troubleshoot.md), wenn bei der Installation oder Verwaltung des Agent Probleme auftreten.
