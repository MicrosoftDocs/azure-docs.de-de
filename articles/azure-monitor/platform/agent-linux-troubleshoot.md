---
title: Problembehandlung für den Azure Log Analytics-Linux-Agent | Microsoft-Dokumentation
description: Beschreibt die Symptome, Ursachen und Lösungen für die häufigsten Probleme mit dem Log Analytics-Agent für Linux in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 13959c4a3c798656efdc72b5c8e5f96e4fb2392a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011896"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Behandeln von Problemen mit dem Log Analytics-Agent für Linux 

Dieser Artikel bietet Unterstützung bei der Problembehandlung von Fehlern, die beim Log Analytics-Agent für Linux in Azure Monitor auftreten können, und enthält mögliche Lösungen zum Beheben dieser Fehler.

Falls sich Ihr Problem durch keinen dieser Schritte beheben lässt, stehen Ihnen auch die folgenden Supportkanäle zur Verfügung:

* Kunden mit Premier Support-Vorteilen können eine Supportanfrage mit [Premier](https://premier.microsoft.com/) stellen.
* Kunden mit Azure-Supportvereinbarungen können eine Supportanfrage [im Azure-Portal](https://manage.windowsazure.com/?getsupport=true) stellen.
* Verwenden Sie zum Diagnostizieren von OMI-Problemen den [OMI troubleshooting guide](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md) (Leitfaden zur OMI-Problembehandlung).
* Melden Sie ein [GitHub-Problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Besuchen Sie die Log Analytics-Feedbackseite unter [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback), um bereits eingereichte Vorschläge und gemeldete Fehler einzusehen oder ein neues Problem zu melden. 

## <a name="log-analytics-troubleshooting-tool"></a>Log Analytics-Problembehandlungstool

Das Problembehandlungstool für den Log Analytics-Agent für Linux ist ein Skript, das für die Ermittlung und Diagnose von Problemen mit dem Log Analytics-Agent konzipiert ist. Es ist bei der Installation des Agents automatisch enthalten. Das Ausführen des Tools sollte der erste Schritt bei der Diagnose eines Problems sein.

### <a name="how-to-use"></a>Verwendung
Das Problembehandlungstool kann ausgeführt werden, indem Sie auf einem Computer mit dem Log Analytics-Agent den folgenden Befehl in ein Terminalfenster einfügen: `sudo /opt/microsoft/omsagent/bin/troubleshooter`

### <a name="manual-installation"></a>Manuelle Installation
Das Problembehandlungstool ist bei der Installation des Log Analytics-Agents automatisch enthalten. Wenn die Installation jedoch in irgendeiner Weise fehlschlägt, kann es mithilfe der folgenden Schritte auch manuell installiert werden.

1. Kopieren Sie das Problembehandlungspaket auf Ihren Computer: `wget https://raw.github.com/microsoft/OMS-Agent-for-Linux/master/source/code/troubleshooter/omsagent_tst.tar.gz`
2. Entpacken Sie das Paket: `tar -xzvf omsagent_tst.tar.gz`
3. Führen Sie die manuelle Installation aus: `sudo ./install_tst`

### <a name="scenarios-covered"></a>Abgedeckte Szenarien
Es folgt eine Liste von Szenarien, die vom Problembehandlungstool geprüft werden:

1. Agent ist fehlerhaft, Takt funktioniert nicht ordnungsgemäß
2. Agent startet nicht, keine Verbindung mit Log Analytics-Diensten
3. Agent-Syslog funktioniert nicht
4. Agent weist hohe CPU-/Arbeitsspeicherauslastung auf
5. Agent weist Installationsprobleme auf
6. Benutzerdefinierte Agent-Protokolle funktionieren nicht
7. Sammeln von Agent-Protokollen

Weitere Informationen finden Sie in der [GitHub-Dokumentation](https://github.com/microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting-Tool.md).

 >[!NOTE]
 >Führen Sie das Protokollsammler-Tool aus, wenn ein Problem auftritt. Mithilfe der anfänglichen Protokolle kann unser Supportteam das Problem schneller beheben.

## <a name="purge-and-re-install-the-linux-agent"></a>Vollständiges Löschen und erneutes Installieren des Linux-Agents

Wir haben festgestellt, dass eine saubere Neuinstallation des Agents die meisten Probleme behebt. Dies ist möglicherweise die erste Empfehlung des Supportteams, um den Agent wieder in einen fehlerfreien Zustand zu versetzen. Das Ausführen der Problembehandlung, die Erfassung von Protokollen und der Versuch einer sauberen Neuinstallation tragen zu einer schnelleren Behebung von Problemen bei.

1. Laden Sie das Skript zur endgültigen Löschung herunter:
- `$ wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/purge_omsagent.sh`
2. Führen Sie das Skript zur endgültigen Löschung (mit sudo-Berechtigungen) aus:
- `$ sudo sh purge_omsagent.sh`

## <a name="important-log-locations-and-log-collector-tool"></a>Wichtige Protokollspeicherorte und das Protokollsammler-Tool

 Datei | `Path`
 ---- | -----
 Protokolldatei des Log Analytics-Agents für Linux | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Konfigurationsprotokolldatei des Log Analytics-Agents für Linux | `/var/opt/microsoft/omsconfig/omsconfig.log`

 Wir empfehlen, für die Problembehandlung oder vor dem Übermitteln eines GitHub-Problems wichtige Protokolle mit unserem Protokollsammler-Tool abzurufen. Weitere Informationen zum Tool und zu seiner Ausführung finden Sie [hier](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md).

## <a name="important-configuration-files"></a>Wichtige Konfigurationsdateien

 Category | Dateispeicherort
 ----- | -----
 syslog | `/etc/syslog-ng/syslog-ng.conf` oder `/etc/rsyslog.conf` oder `/etc/rsyslog.d/95-omsagent.conf`
 Leistung, Nagios, Zabbix, Log Analytics-Ausgabe und allgemeiner Agent | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Zusätzliche Konfigurationen | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >Änderungen an den Konfigurationsdateien für Leistungsindikatoren und Syslog werden überschrieben, wenn die Sammlung für Ihren Arbeitsbereich im Azure-Portal über das [Menü „Daten“ in den erweiterten Einstellungen von Log Analytics](./agent-data-sources.md#configuring-data-sources) konfiguriert wird. Um die Konfiguration für alle Agents zu deaktivieren, deaktivieren Sie die Sammlung auf dem Blatt **Erweiterte Einstellungen** für Log Analytics. Wenn Sie die Konfiguration für einen einzelnen Agent deaktivieren möchten, führen Sie den folgenden Befehl aus:  
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="installation-error-codes"></a>Codes für Installationsfehler

| Fehlercode | Bedeutung |
| --- | --- |
| NOT_DEFINED | Da die erforderlichen Abhängigkeiten nicht installiert sind, wird das Plug-In „auoms auditd“ nicht installiert. | Bei der Installation von auoms ist ein Fehler aufgetreten. Installieren Sie das auditd-Paket. |
| 2 | Für das Shellbündel wurde eine ungültige Option angegeben. Führen Sie `sudo sh ./omsagent-*.universal*.sh --help` aus, um Informationen zur Verwendung anzuzeigen. |
| 3 | Für das Shellbündel wurde keine Option angegeben. Führen Sie `sudo sh ./omsagent-*.universal*.sh --help` aus, um Informationen zur Verwendung anzuzeigen. |
| 4 | Ungültiger Pakettyp ODER ungültige Proxyeinstellungen. omsagent-*rpm*.sh-Pakete können nur auf RPM-basierten Systemen installiert werden, und omsagent-*deb*.sh-Pakete können nur auf Debian-basierten Systemen installiert werden. Es wird empfohlen, den universellen Installer der [aktuellen Version](../learn/quick-collect-linux-computer.md#install-the-agent-for-linux) zu verwenden. Überprüfen Sie auch Ihre Proxyeinstellungen. |
| 5 | Das Shellbündel muss als Root-Benutzer ausgeführt werden, ODER während des Onboardings wurde ein Fehler 403 zurückgegeben. Führen Sie den Befehl mit `sudo` aus. |
| 6 | Ungültige Paketarchitektur ODER während des Onboardings wurde ein Fehler 200 zurückgegeben. omsagent-*x64.sh-Pakete können nur auf 64-Bit-Systemen installiert werden, und omsagent-* x86.sh-Pakete können nur auf 32-Bit-Systemen installiert werden. Laden Sie das richtige Paket für Ihre Architektur aus der [aktuellen Version](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest) herunter. |
| 17 | Fehler bei der Installation des OMS-Pakets. Suchen Sie in der Ausgabe des Befehls nach der Grundursache des Fehlers. |
| 19 | Fehler bei der Installation des OMI-Pakets. Suchen Sie in der Ausgabe des Befehls nach der Grundursache des Fehlers. |
| 20 | Fehler bei der Installation des SCX-Pakets. Suchen Sie in der Ausgabe des Befehls nach der Grundursache des Fehlers. |
| 21 | Fehler bei der Installation von Anbieterkits. Suchen Sie in der Ausgabe des Befehls nach der Grundursache des Fehlers. |
| 22 | Fehler bei der Installation des gebündelten Pakets. Suchen Sie in der Ausgabe des Befehls nach der Grundursache des Fehlers. |
| 23 | Das SCX- oder OMI-Paket ist bereits installiert. Verwenden Sie `--upgrade` anstelle von `--install`, um das Shellbündel zu installieren. |
| 30 | Interner Bündelfehler. Melden Sie ein [GitHub-Problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) mit Details aus der Ausgabe. |
| 55 | Nicht unterstützte OpenSSL-Version ODER die Verbindung mit Azure Monitor kann nicht hergestellt werden, ODER dpkg ist gesperrt, ODER das Programm cURL fehlt. |
| 61 | Die Python-ctypes-Bibliothek fehlt. Installieren Sie die Python-Bibliothek bzw. das Python-Paket ctypes (python-ctypes). |
| 62 | Das Programm tar fehlt. Installieren Sie tar. |
| 63 | Das Programm sed fehlt. Installieren Sie sed. |
| 64 | Das Programm cURL fehlt. Installieren Sie cURL. |
| 65 | Das Programm GPG fehlt. Installieren Sie GPG. |

## <a name="onboarding-error-codes"></a>Codes für Onboardingfehler

| Fehlercode | Bedeutung |
| --- | --- |
| 2 | Im omsadmin-Skript wurde eine ungültige Option angegeben. Führen Sie `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` aus, um Informationen zur Verwendung anzuzeigen. |
| 3 | Im omsadmin-Skript wurde eine ungültige Konfiguration angegeben. Führen Sie `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` aus, um Informationen zur Verwendung anzuzeigen. |
| 4 | Im omsadmin-Skript wurde ein ungültiger Proxy angegeben. Überprüfen Sie den Proxy, und lesen Sie die [Dokumentation zur Verwendung eines HTTP-Proxys](log-analytics-agent.md#firewall-requirements). |
| 5 | HTTP-Fehler 403 von Azure Monitor empfangen. Details finden Sie in der vollständigen Ausgabe des omsadmin-Skripts. |
| 6 | Von 200 abweichender HTTP-Fehler von Azure Monitor empfangen. Details finden Sie in der vollständigen Ausgabe des omsadmin-Skripts. |
| 7 | Verbindung mit Azure Monitor nicht möglich. Details finden Sie in der vollständigen Ausgabe des omsadmin-Skripts. |
| 8 | Fehler beim Onboarding im Log Analytics-Arbeitsbereich. Details finden Sie in der vollständigen Ausgabe des omsadmin-Skripts. |
| 30 | Interner Skriptfehler. Melden Sie ein [GitHub-Problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) mit Details aus der Ausgabe. |
| 31 | Fehler beim Generieren der Agent-ID. Melden Sie ein [GitHub-Problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) mit Details aus der Ausgabe. |
| 32 | Fehler beim Generieren von Zertifikaten. Details finden Sie in der vollständigen Ausgabe des omsadmin-Skripts. |
| 33 | Fehler beim Generieren der Metakonfiguration für omsconfig. Melden Sie ein [GitHub-Problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) mit Details aus der Ausgabe. |
| 34 | Das Skript zum Generieren der Metakonfiguration ist nicht vorhanden. Wiederholen Sie das Onboarding mit `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>`. |

## <a name="enable-debug-logging"></a>Debugprotokollierung aktivieren
### <a name="oms-output-plugin-debug"></a>Debuggen mit dem OMS-Ausgabe-Plug-In
 FluentD ermöglicht die Verwendung von Plug-In-spezifischen Protokolliergraden, sodass Sie verschiedene Protokollebenen für Ein- und Ausgaben festlegen können. Bearbeiten Sie zum Festlegen einer anderen Protokollebene für die OMS-Ausgabe die allgemeine Agent-Konfiguration unter `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.  

 Ändern Sie im OMS-Ausgabe-Plug-In vor dem Ende der Konfigurationsdatei die `log_level`-Eigenschaft von `info` in `debug`:

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

Mithilfe der Debugprotokollierung sehen Sie Batch-Uploads an Azure Monitor, getrennt nach Typ, Anzahl von Datenelementen und benötigter Zeit zum Senden:

*Beispiel für Debugprotokoll:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Ausführliche Ausgabe
Statt das OMS-Ausgabe-Plug-In zu verwenden, können Sie Datenelemente auch direkt an das `stdout`-Element ausgeben, das in der Protokolldatei des Log Analytics-Agents für Linux sichtbar ist.

Kommentieren Sie in der allgemeinen Agent-Konfigurationsdatei von Log Analytics unter `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` das OMS-Ausgabe-Plug-In aus, indem Sie vor jeder Zeile ein `#`-Zeichen hinzufügen:

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

Heben Sie unter dem Ausgabe-Plug-In die Auskommentierung des folgenden Abschnitts auf, indem Sie das `#`-Zeichen vor jeder Zeile entfernen:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Problem:  Es ist keine Verbindung über den Proxy mit Azure Monitor möglich.

### <a name="probable-causes"></a>Mögliche Ursachen
* Der während der Integration angegebene Proxy war falsch.
* Die Dienstendpunkte für Azure Monitor und Azure Automation sind nicht in der genehmigten Liste in Ihrem Rechenzentrum enthalten. 

### <a name="resolution"></a>Lösung
1. Führen Sie das Onboarding in Azure Monitor mit dem Log Analytics-Agent für Linux erneut durch, indem Sie den folgenden Befehl mit aktivierter Option `-v` ausführen. Dies ermöglicht die ausführliche Ausgabe des Agents, der die Verbindung über den Proxy herstellt, an Azure Monitor. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Lesen Sie den Abschnitt [Aktualisieren der Proxyeinstellungen](agent-manage.md#update-proxy-settings), um sicherzustellen, dass der Agent ordnungsgemäß für die Kommunikation über einen Proxyserver konfiguriert wurde.    

3. Vergewissern Sie sich, dass die in der Liste der [Netzwerkfirewallanforderungen](log-analytics-agent.md#firewall-requirements) für Azure Monitor aufgeführten Endpunkte ordnungsgemäß zu einer Zulassungsliste hinzugefügt wurden. Wenn Sie Azure Automation verwenden, sind oben auch Links zu den erforderlichen Schritten für die Netzwerkkonfiguration angegeben.

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problem: Beim Onboardingversuch erhalten Sie einen 403-Fehler.

### <a name="probable-causes"></a>Mögliche Ursachen
* Das Datum und die Uhrzeit auf dem Linux-Server sind falsch. 
* Die Arbeitsbereichs-ID und der Arbeitsbereichsschlüssel sind falsch.

### <a name="resolution"></a>Lösung

1. Überprüfen Sie die Uhrzeit auf dem Linux-Server mit dem Befehl „date“. Wenn die Zeit um +/-15 Minuten von der aktuellen Uhrzeit abweicht ist, tritt bei der Integration ein Fehler auf. Aktualisieren Sie zur Behebung dieses Problems das Datum und/oder die Zeitzone des Linux-Servers. 
2. Überprüfen Sie, ob die aktuelle Version des Log Analytics-Agents für Linux installiert ist.  Die neueste Version benachrichtigt Sie nun darüber, ob der Onboardingfehler durch die Zeitabweichung verursacht wird.
3. Führen Sie gemäß den Installationsanweisungen weiter oben in diesem Artikel das Onboarding erneut mit korrekten Daten für die Arbeitsbereichs-ID und den Arbeitsbereichsschlüssel durch.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problem: In der Protokolldatei sind direkt nach der Integration die Fehler 500 und 404 enthalten.
Dies ist ein bekanntes Problem, das beim ersten Hochladen von Linux-Daten in einen Log Analytics-Arbeitsbereich auftritt. Gesendete Daten und die Ausführung des Diensts sind davon nicht betroffen.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Problem: OMI-Agent verwendet 100 % der CPU

### <a name="probable-causes"></a>Mögliche Ursachen
Eine Regression im nss-pem-Paket [v1.0.3-5.el7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) verursachte ein schwerwiegendes Leistungsproblem, dass in Redhat/Centos 7.x-Distributionen sehr häufig vorkommt. Weitere Informationen zu diesem Problem finden Sie in dieser Dokumentation: Fehler [1667121 Leistungsregression in libcurl](https://bugzilla.redhat.com/show_bug.cgi?id=1667121).

Leistungsbezogene Fehler kommen nicht immer vor und sind nur sehr schwer zu reproduzieren. Wenn ein solches Problem mit OMI-Agent auftritt, verwenden Sie das Skript „omiHighCPUDiagnostics.sh“, das bei Überschreitung eines bestimmten Schwellenwerts die Stapelüberwachung des OMI-Agents erfasst.

1. Herunterladen des Skripts <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Für 24 Stunden Diagnose mit einem CPU-Schwellenwert von 30 % ausführen <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. Der Rückruf wird in die Ablaufverfolgungsdatei „omiagent_“ ausgegeben. Wenn Sie viele Curl- und NSS-Funktionsaufrufe feststellen, führen Sie die Lösungsschritte unten aus.

### <a name="resolution-step-by-step"></a>Lösung (Schritt für Schritt)

1. Aktualisieren Sie das nss-pem-Paket auf [v1.0.3-5.el7_6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html). <br/>
`sudo yum upgrade nss-pem`

2. Wenn nss-perm nicht für das Upgrade verfügbar ist (tritt größtenteils unter Centos auf), stufen Sie Curl auf 7.29.0-46 herab. Wenn Sie versehentlich „yum-update“ ausführen, wird Curl auf 7.29.0-51 aktualisiert, und das Problem wird erneut auftreten. <br/>
`sudo yum downgrade curl libcurl`

3. OMI neu starten: <br/>
`sudo scxadmin -restart`

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problem: Im Azure-Portal werden keine Daten angezeigt.

### <a name="probable-causes"></a>Mögliche Ursachen

- Fehler beim Onboarding in Azure Monitor
- Die Verbindung mit Azure Monitor ist blockiert.
- Daten des Log Analytics-Agents für Linux werden gesichert.

### <a name="resolution"></a>Lösung
1. Vergewissern Sie sich, dass das Onboarding in Azure Monitor erfolgreich war, indem Sie überprüfen, ob die folgende Datei vorhanden ist: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`.
2. Erneutes Onboarding mithilfe der Befehlszeilenanweisungen `omsadmin.sh`
3. Wenn Sie einen Proxy verwenden, lesen Sie die Proxylösungsschritte weiter oben.
4. In einigen Fällen, wenn der Log Analytics-Agent für Linux nicht mit dem Dienst kommunizieren kann, werden Daten auf dem Agent bis zur vollständigen Puffergröße von 50 MB in eine Warteschlange gestellt. Der Agent sollte mit dem folgenden Befehl neu gestartet werden: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Dieses Problem wurde ab der Agent-Version 1.1.0-28 behoben.


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Problem: Weitergeleitete Syslog-Nachrichten werden nicht angezeigt. 

### <a name="probable-causes"></a>Mögliche Ursachen
* Die auf den Linux-Server angewendete Konfiguration ermöglicht keine Sammlung der gesendeten Facilities und/oder Protokollebenen.
* Syslog wird nicht ordnungsgemäß zum Linux-Server weitergeleitet.
* Die Anzahl pro Sekunde weitergeleiteter Nachrichten ist zu groß für die Basiskonfiguration des Log Analytics-Agents für Linux.

### <a name="resolution"></a>Lösung
* Überprüfen Sie, ob die Konfiguration im Log Analytics-Arbeitsbereich für Syslog alle Facilities und die richtigen Protokollebenen enthält. Lesen Sie die Informationen zum [Konfigurieren der Syslog-Sammlung im Azure-Portal](./data-sources-syslog.md#configure-syslog-in-the-azure-portal).
* Stellen Sie sicher, dass native Syslog-Messagingdaemons (`rsyslog`, `syslog-ng`) die weitergeleiteten Nachrichten empfangen können.
* Überprüfen Sie die Firewall-Einstellungen auf dem Syslog-Server, um sicherzustellen, dass Nachrichten nicht blockiert werden.
* Simulieren Sie mit dem Befehl `logger` eine Syslog-Nachricht an Log Analytics.
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Problem: Sie empfangen eine bereits verwendete Errno-Adresse in der omsagent-Protokolldatei.
Der Fehler `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` wird in der Datei „omsagent.log“ angezeigt.

### <a name="probable-causes"></a>Mögliche Ursachen
Dieser Fehler weist darauf hin, dass die Linux-Diagnoseerweiterung (LAD) parallel zur Log Analytics-Linux-VM-Erweiterung installiert ist und denselben Port für die Syslog-Datensammlung verwendet wie omsagent.

### <a name="resolution"></a>Lösung
1. Führen Sie die folgenden Befehle als Root-Benutzer aus (beachten Sie, dass 25224 ein Beispiel ist und in Ihrer Umgebung möglicherweise eine andere von LAD verwendete Portnummer angezeigt wird):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Anschließend müssen Sie die richtige `rsyslogd`- oder `syslog_ng`-Konfigurationsdatei bearbeiten und die LAD-bezogene Konfiguration ändern, um die Ausgabe an Port 25229 zu senden.

2. Wenn die VM `rsyslogd` ausführt, muss die folgende Datei geändert werden: `/etc/rsyslog.d/95-omsagent.conf` (sofern diese vorhanden ist, andernfalls `/etc/rsyslog`). Wenn die VM `syslog_ng` ausführt, muss die folgende Datei geändert werden: `/etc/syslog-ng/syslog-ng.conf`.
3. Starten Sie omsagent mit `sudo /opt/microsoft/omsagent/bin/service_control restart` neu.
4. Starten Sie den Syslog-Dienst neu.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Problem: Sie können omsagent nicht mit der purge-Option deinstallieren.

### <a name="probable-causes"></a>Mögliche Ursachen

* Die Linux-Diagnoseerweiterung ist installiert.
* Die Linux-Diagnoseerweiterung wurde installiert und deinstalliert, es wird aber weiterhin ein Fehler angezeigt, dass omsagent von mdsd verwendet wird und nicht entfernt werden kann.

### <a name="resolution"></a>Lösung
1. Deinstallieren Sie die Linux-Diagnoseerweiterung (LAD).
2. Entfernen Sie die Dateien der Linux-Diagnoseerweiterung vom Computer, sofern sie am folgenden Speicherort vorhanden sind: `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` und `/var/opt/microsoft/omsagent/LAD/`.

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Problem: Es werden keine Nagios-Daten angezeigt. 

### <a name="probable-causes"></a>Mögliche Ursachen
* Der omsagent-Benutzer besitzt keine Berechtigungen zum Lesen aus der Nagios-Protokolldatei.
* Die Auskommentierung der Nagios-Quelle und des Nagios-Filters wurde in der Datei „omsagent.conf“ nicht aufgehoben.

### <a name="resolution"></a>Lösung
1. Fügen Sie wie in diesen [Anweisungen](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts) beschrieben einen omsagent-Benutzer mit der Berechtigung zum Lesen der Nagios-Datei hinzu.
2. Vergewissern Sie sich in der allgemeinen Konfigurationsdatei des Log Analytics-Agents für Linux unter `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`, dass die Auskommentierung der Nagios-Quelle **und** des Nagios-Filters aufgehoben wurde.

    ```
    <source>
      type tail
      path /var/log/nagios/nagios.log
      format none
      tag oms.nagios
    </source>

    <filter oms.nagios>
      type filter_nagios_log
    </filter>
    ```

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Problem: Es werden keine Linux-Daten angezeigt. 

### <a name="probable-causes"></a>Mögliche Ursachen
* Fehler beim Onboarding in Azure Monitor
* Die Verbindung mit Azure Monitor ist blockiert.
* Die VM wurde neu gestartet.
* Das OMI-Paket wurde manuell auf eine Version aktualisiert, die neuer ist als die vom Log Analytics-Agent für Linux-Paket installierte Version.
* Die DSC-Ressource protokolliert einen Fehler vom Typ *Klasse nicht gefunden* in der Protokolldatei `omsconfig.log`.
* Daten des Log Analytics-Agents werden gesichert.
* DSC protokolliert die Meldung *Current configuration does not exist. Execute Start-DscConfiguration command with -Path parameter to specify a configuration file and create a current configuration first.* (Die aktuelle Konfiguration ist nicht vorhanden. Führen Sie zunächst den Befehl „Start-DscConfiguration“ mit dem -Path-Parameter aus, um eine Konfigurationsdatei anzugeben und eine aktuelle Konfiguration zu erstellen.) in der Protokolldatei `omsconfig.log`, es ist jedoch keine Protokollmeldung zu `PerformRequiredConfigurationChecks`-Vorgängen vorhanden.

### <a name="resolution"></a>Lösung
1. Installieren Sie alle Abhängigkeiten (z. B. das auditd-Paket).
2. Vergewissern Sie sich, dass das Onboarding in Azure Monitor erfolgreich war, indem Sie überprüfen, ob die folgende Datei vorhanden ist: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`.  Falls dies nicht der Fall ist, wiederholen Sie das Onboarding mithilfe der [Anweisungen](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) der Befehlszeile „omsadmin.sh“.
4. Gehen Sie bei der Verwendung eines Proxys gemäß den oben aufgeführten Schritten zur Proxyproblembehandlung vor.
5. In einigen Azure-Verteilungssystemen wird der omid-OMI-Serverdaemon nach dem Neustart der VM nicht gestartet. Dies führt dazu, dass keine Daten im Zusammenhang mit Audit-, ChangeTracking- oder UpdateManagement-Lösungen angezeigt werden. Die Problemumgehung besteht darin, den OMI-Server durch Ausführen von `sudo /opt/omi/bin/service_control restart`manuell zu starten.
6. Nachdem das OMI-Paket manuell auf eine neuere Version aktualisiert wurde, muss es manuell neu gestartet werden, damit der Log Analytics-Agent weiterhin funktioniert. Dieser Schritt ist für einige Distributionen erforderlich, bei denen der OMI-Server nach einem Upgrade nicht automatisch gestartet wird. Führen Sie `sudo /opt/omi/bin/service_control restart` aus, um OMI neu zu starten.
7. Falls ein DSC-Ressourcenfehler vom Typ *Klasse nicht gefunden* in der Datei „omsconfig.log“ angezeigt wird, führen Sie `sudo /opt/omi/bin/service_control restart` aus.
8. In einigen Fällen, wenn der Log Analytics-Agent für Linux nicht mit Azure Monitor kommunizieren kann, werden Daten auf dem Agent bis zur vollständigen Puffergröße gesichert: 50 MB. Der Agent sollte mit dem folgenden Befehl neu gestartet werden: `/opt/microsoft/omsagent/bin/service_control restart`.

    >[!NOTE]
    >Dieses Problem wurde ab der Agent-Version 1.1.0-28 behoben.
    >

* Wenn in der Protokolldatei `omsconfig.log` nicht angezeigt wird, dass regelmäßig `PerformRequiredConfigurationChecks`-Vorgänge auf dem System ausgeführt werden, liegt möglicherweise ein Problem mit dem Cron-Auftrag/-Dienst vor. Stellen Sie sicher, dass der Cron-Auftrag unter `/etc/cron.d/OMSConsistencyInvoker` vorhanden ist. Führen Sie ggf. die folgenden Befehle aus, um den Cron-Auftrag zu erstellen:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Stellen Sie außerdem sicher, dass der Cron-Dienst ausgeführt wird. Sie können `service cron status` unter Debian, Ubuntu und SUSE oder `service crond status` unter RHEL, CentOS und Oracle Linux verwenden, um den Status dieses Diensts zu überprüfen. Falls der Dienst nicht vorhanden ist, können Sie mit den folgenden Befehlen die Binärdateien installieren und den Dienst starten:

    **Ubuntu/Debian**

    ```
    # To Install the service binaries
    sudo apt-get install -y cron
    # To start the service
    sudo service cron start
    ```

    **SUSE**

    ```
    # To Install the service binaries
    sudo zypper in cron -y
    # To start the service
    sudo systemctl enable cron
    sudo systemctl start cron
    ```

    **RHEL/CentOS**

    ```
    # To Install the service binaries
    sudo yum install -y crond
    # To start the service
    sudo service crond start
    ```

    **Oracle Linux**

    ```
    # To Install the service binaries
    sudo yum install -y cronie
    # To start the service
    sudo service crond start
    ```

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Problem: Wenn die Sammlung über das Portal für Syslog oder Linux-Leistungsindikatoren konfiguriert wird, werden die Einstellungen nicht angewendet.

### <a name="probable-causes"></a>Mögliche Ursachen
* Der Log Analytics-Agent für Linux hat nicht die aktuelle Konfiguration übernommen.
* Die geänderten Einstellungen im Portal wurden nicht angewendet.

### <a name="resolution"></a>Lösung
**Hintergrund:** `omsconfig` ist der Konfigurations-Agent für den Log Analytics-Agent für Linux, der alle fünf Minuten nach einer neuen im Portal erstellten Konfiguration sucht. Diese Konfiguration wird dann auf die Konfigurationsdateien des Log Analytics-Agents für Linux unter „/etc/opt/microsoft/omsagent/conf/omsagent.conf“ angewendet.

* In einigen Fällen kann der Konfigurations-Agent für den Log Analytics-Agent für Linux möglicherweise nicht mit dem Portalkonfigurationsdienst kommunizieren, was dazu führt, dass die aktuelle Konfiguration nicht angewendet wird.
  1. Überprüfen Sie, ob der `omsconfig`-Agent installiert ist, indem Sie `dpkg --list omsconfig` oder `rpm -qi omsconfig` ausführen.  Falls der Agent nicht installiert ist, installieren Sie die aktuelle Version des Log Analytics-Agents für Linux.

  2. Überprüfen Sie, ob der `omsconfig`-Agent mit Azure Monitor kommunizieren kann, indem Sie den Befehl `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` ausführen. Dieser Befehl gibt die Konfiguration zurück, die der Agent vom Dienst empfängt (einschließlich der Syslog-Einstellungen, Linux-Leistungsindikatoren und benutzerdefinierten Protokolle). Falls bei diesem Befehl ein Fehler auftritt, führen Sie den folgenden Befehl aus: `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Dieser Befehl erzwingt die Kommunikation des omsconfig-Agents mit Azure Monitor und das Abrufen der aktuellen Konfiguration.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Problem: Es werden keine benutzerdefinierten Protokolldaten angezeigt. 

### <a name="probable-causes"></a>Mögliche Ursachen
* Fehler beim Onboarding in Azure Monitor.
* Die Einstellung **Apply the following configuration to my Linux Servers** (Die nachstehende Konfiguration auf meine Linux-Server anwenden) wurde nicht ausgewählt.
* omsconfig hat nicht die aktuelle benutzerdefinierte Protokollkonfiguration aus dem Dienst übernommen.
* Der Benutzer `omsagent` des Log Analytics-Agents für Linux kann nicht auf das benutzerdefinierte Protokoll zugreifen, weil seine Berechtigungen nicht ausreichen oder das Protokoll nicht gefunden wurde.  Die folgenden Fehler werden möglicherweise angezeigt:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Bekanntes Problem mit einer Racebedingung, das in Log Analytics-Agent für Linux Version 1.1.0-217 behoben wurde.

### <a name="resolution"></a>Lösung
1. Vergewissern Sie sich, dass das Onboarding in Azure Monitor erfolgreich war, indem Sie überprüfen, ob die folgende Datei vorhanden ist: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`. Falls dies nicht der Fall ist, führen Sie einen der folgenden Schritte aus:  

  1. Wiederholen Sie das Onboarding mithilfe der [Anweisungen](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) der Befehlszeile „omsadmin.sh“.
  2. Vergewissern Sie sich im Azure-Portal unter **Erweiterte Einstellungen**, dass die Einstellung **Apply the following configuration to my Linux Servers** (Die nachstehende Konfiguration auf meine Linux-Server anwenden) aktiviert ist.  

2. Überprüfen Sie, ob der `omsconfig`-Agent mit Azure Monitor kommunizieren kann, indem Sie den Befehl `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` ausführen.  Dieser Befehl gibt die Konfiguration zurück, die der Agent vom Dienst empfängt (einschließlich der Syslog-Einstellungen, Linux-Leistungsindikatoren und benutzerdefinierten Protokolle). Falls bei diesem Befehl ein Fehler auftritt, führen Sie den folgenden Befehl aus: `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Dieser Befehl erzwingt die Kommunikation des omsconfig-Agents mit Azure Monitor und das Abrufen der aktuellen Konfiguration.

**Hintergrund:** Der Log Analytics-Agent für Linux wird nicht als privilegierter Benutzer `root` ausgeführt, sondern als Benutzer `omsagent`. In den meisten Fällen muss diesem Benutzer eine explizite Berechtigung erteilt werden, damit bestimmte Dateien gelesen werden können. Damit Benutzer `omsagent` die erforderliche Berechtigung bekommt, führen Sie die folgenden Befehle aus:

1. Fügen Sie den Benutzer `omsagent` einer bestimmten Gruppe hinzu: `sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Gewähren Sie universellen Lesezugriff auf die erforderliche Datei: `sudo chmod -R ugo+rx <FILE DIRECTORY>`

Im Log Analytics-Agent für Linux vor Version 1.1.0-217 besteht ein bekanntes Problem mit einer Racebedingung. Führen Sie nach der Aktualisierung auf den aktuellen Agent den folgenden Befehl aus, um die aktuelle Version des Ausgabe-Plug-Ins `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` abzurufen.

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Problem: Sie versuchen, ein erneutes Onboarding in einem neuen Arbeitsbereich durchzuführen.
Wenn Sie versuchen, für einen Agent ein erneutes Onboarding in einem neuen Arbeitsbereich durchzuführen, muss die Konfiguration des Log Analytics-Agents vor dem erneuten Onboarding bereinigt werden. Führen Sie zum Bereinigen der alten Konfiguration des Agents das Shellbündel mit `--purge` aus.

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
oder

```
sudo sh ./onboard_agent.sh --purge
```

Sie können das erneute Onboarding nach der Verwendung der Option `--purge` fortsetzen.

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Die Log Analytics-Agent-Erweiterung im Azure-Portal ist mit einem Fehlerstatus markiert: Fehler bei der Bereitstellung.

### <a name="probable-causes"></a>Mögliche Ursachen
* Der Log Analytics-Agent wurde aus dem Betriebssystem entfernt.
* Der Log Analytics-Agent-Dienst ist nicht verfügbar, deaktiviert oder nicht konfiguriert.

### <a name="resolution"></a>Lösung 
Führen Sie die folgenden Schritte aus, um das Problem zu beheben.
1. Entfernen Sie die Erweiterung aus dem Azure-Portal.
2. Installieren Sie den Agent gemäß den [Anweisungen](../learn/quick-collect-linux-computer.md).
3. Starten Sie den Agent neu, indem Sie den folgenden Befehl ausführen: `sudo /opt/microsoft/omsagent/bin/service_control restart`.
* Warten Sie einige Minuten. Der Bereitstellungsstatus ändert sich dann in **Bereitstellung erfolgreich**.


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Problem: On-Demand-Upgrade des Log Analytics-Agents

### <a name="probable-causes"></a>Mögliche Ursachen

Die Log Analytics-Agent-Pakete auf dem Host sind veraltet.

### <a name="resolution"></a>Lösung 
Führen Sie die folgenden Schritte aus, um das Problem zu beheben.

1. Die aktuelle Version finden Sie [hier](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Laden Sie das Installationsskript herunter (1.4.2-124 als Beispielversion):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Aktualisieren Sie die Pakete, indem Sie `sudo sh ./omsagent-*.universal.x64.sh --upgrade` ausführen.
