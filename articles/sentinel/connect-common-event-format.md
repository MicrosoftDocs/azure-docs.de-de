---
title: Verknüpfen von CEF-Daten mit der Vorschauversion von Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie CEF-Daten mit Azure Sentinel verknüpfen.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cbf5003b-76cf-446f-adb6-6d816beca70f
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: rkarlin
ms.openlocfilehash: 8e711c0586ce63d4293e2fb0914bbe884b55971f
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389961"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Verbinden der externen Lösung mithilfe von Common Event Format

> [!IMPORTANT]
> Azure Sentinel befindet sich momentan in der Public Preview.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sie können Azure Sentinel mit einer externen Lösung verbinden, mit der Sie Protokolldateien in Syslog speichern können. Wenn Sie mit Ihrer Appliance Protokolle im Syslog CEF-Format (Common Event Format) speichern können, können Sie nach der Integration in Azure Sentinel ohne großen Aufwand Analysen und Abfragen für die Daten durchführen.

> [!NOTE] 
> Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.

## <a name="how-it-works"></a>So funktioniert's

Die Verbindung zwischen Azure Sentinel und Ihrer CEF-Appliance wird in drei Schritten hergestellt:

1. Auf der Appliance müssen Sie folgende Werte festlegen, damit die erforderlichen Protokolle von der Appliance im erforderlichen Format an den Azure Sentinel Syslog-Agent gesendet werden. Sie können diese Parameter in Ihrer Appliance ändern, sofern Sie diese im Syslog-Daemon auf dem Azure Sentinel-Agent ändern.
    - Protocol = UDP
    - Port = 514
    - Facility = Local-4
    - Format = CEF
2. Der Syslog-Agent sammelt die Daten und sendet sie sicher an Log Analytics. Dort werden sie analysiert und angereichert.
3. Der Agent speichert die Daten in einem Log Analytics-Arbeitsbereich, damit sie bei Bedarf mithilfe von Analysen, Korrelationsregeln und Dashboards abgefragt werden können.

> [!NOTE]
> Der Agent kann Protokolle von mehreren Quellen erfassen, muss aber auf einem dedizierten Proxycomputer installiert sein.

## <a name="step-1-connect-to-your-cef-appliance-via-dedicated-azure-vm"></a>Schritt 1: Herstellen einer Verbindung mit der CEF-Appliance über dedizierte Azure-VM

Sie müssen einen Agent auf einem dedizierten Linux-Computer (VM oder lokal) bereitstellen, um die Kommunikation zwischen der Appliance und Azure Sentinel zu ermöglichen. Sie können den Agent automatisch oder manuell bereitstellen. Die automatische Bereitstellung basiert auf Resource Manager-Vorlagen und kann nur verwendet werden, wenn es sich beim dedizierten Linux-Computer um eine neue VM handelt, die Sie in Azure erstellen.

 ![CEF in Azure](./media/connect-cef/cef-syslog-azure.png)

Alternativ hierzu können Sie den Agent manuell auf einem vorhandenen virtuellen Azure-Computer, einem virtuellen Computer in einer anderen Cloud oder einem lokalen Computer bereitstellen. 

 ![CEF lokal](./media/connect-cef/cef-syslog-onprem.png)

### <a name="deploy-the-agent-in-azure"></a>Bereitstellen des Agents in Azure


1. Klicken Sie im Azure Sentinel-Portal auf **Data connectors** (Datenconnectors), und wählen Sie Ihren Appliancetyp aus. 

1. Gehen Sie unter **Linux Syslog agent configuration** (Linux-Syslog-Agent-Konfiguration) wie folgt vor:
   - Wählen Sie **Automatic deployment** (Automatische Bereitstellung) aus, wenn Sie einen neuen Computer erstellen möchten, auf dem der Azure Sentinel-Agent vorinstalliert ist und der die gesamte erforderliche Konfiguration (siehe Beschreibung weiter oben) enthält. Wählen Sie **Automatic deployment** (Automatische Bereitstellung) aus, und klicken Sie dann auf **Automatic agent deployment** (Automatische Bereitstellung des Agents). Daraufhin wird die Kaufseite für einen dedizierten virtuellen Linux-Computer geöffnet, der automatisch mit Ihrem Arbeitsbereich verbunden wird. Der virtuelle Computer gehört zur Serie **Standard D2s v3 (2 vCPUs, 8 GB Arbeitsspeicher)** und hat eine öffentliche IP-Adresse.
      1. Geben Sie auf der Seite **Benutzerdefinierte Bereitstellung** Ihre Details an, legen Sie einen Benutzernamen und ein Kennwort fest, und erwerben Sie den virtuellen Computer, sofern Sie mit den Nutzungsbedingungen einverstanden sind.
      1. Konfigurieren Sie Ihre Appliance so, dass sie Protokolle mit den Einstellungen sendet, die auf der Verbindungsseite aufgeführt sind. Verwenden Sie für den Connector „Generic Common Event Format“ die folgenden Einstellungen:
         - Protocol = UDP
         - Port = 514
         - Facility = Local-4
         - Format = CEF
   - Wählen Sie **Manual deployment** (Manuelle Bereitstellung) aus, wenn Sie einen vorhandenen virtuellen Computer als dedizierten Linux-Computer verwenden möchten, auf dem der Azure Sentinel-Agent installiert werden soll. 
      1. Wählen Sie unter **Download and install the Syslog agent** (Syslog-Agent herunterladen und installieren) die Option **Azure Linux virtual machine** (Virtueller Azure-Linux-Computer) aus. 
      1. Wählen Sie auf dem daraufhin geöffneten Bildschirm **Virtuelle Computer** den Computer aus, den Sie verwenden möchten, und klicken Sie dann auf **Verbinden**.
      1. Legen Sie auf dem Connector-Bildschirm unter **Configure and forward Syslog** (Syslog konfigurieren und weiterleiten) fest, welchen Syslog-Daemon (**rsyslog.d** oder **syslog-ng**) Sie verwenden. 
      1. Kopieren Sie die folgenden Befehle, und führen Sie sie auf der Appliance aus:
          - Wenn Sie „rsyslog.d“ ausgewählt haben, gehen Sie wie folgt vor:
              
            1. Weisen Sie den Syslog-Daemon an, in der Umgebung „local_4“ zu lauschen und die Syslog-Nachrichten über den Port 25226 an den Azure Sentinel-Agent zu senden. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
            2. Laden Sie die [Konfigurationsdatei „security_events“](https://aka.ms/asi-syslog-config-file-linux) herunter, mit der der Syslog-Agent zum Lauschen am Port 25226 konfiguriert wird, und installieren Sie sie. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Ersetzen Sie hier {0} durch die GUID Ihres Arbeitsbereichs.
            
            1. Starten Sie den Syslog-Daemon neu: `sudo service rsyslog restart`<br> Weitere Informationen finden Sie in der Dokumentation zu [rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html).
           
          - Wenn Sie „syslog-ng“ ausgewählt haben, gehen Sie wie folgt vor:

              1. Weisen Sie den Syslog-Daemon an, in der Umgebung „local_4“ zu lauschen und die Syslog-Nachrichten über den Port 25226 an den Azure Sentinel-Agent zu senden. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. Laden Sie die [Konfigurationsdatei „security_events“](https://aka.ms/asi-syslog-config-file-linux) herunter, mit der der Syslog-Agent zum Lauschen am Port 25226 konfiguriert wird, und installieren Sie sie. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Ersetzen Sie hier {0} durch die GUID Ihres Arbeitsbereichs.

              3. Starten Sie den Syslog-Daemon neu: `sudo service syslog-ng restart` <br>Weitere Informationen finden Sie in der Dokumentation zu [syslog-ng](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/mutual-authentication-using-tls/2).
      2. Starten Sie den Syslog-Agent mit dem folgenden Befehl neu: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Vergewissern Sie sich, dass im Agent-Protokoll keine Fehler enthalten sind, indem Sie den folgenden Befehl ausführen: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

 Um das relevante Schema in Log Analytics für die CEF-Ereignisse zu verwenden, suchen Sie nach `CommonSecurityLog`.


### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>Bereitstellen des Agents auf einem lokalen Linux-Server

Falls Sie nicht Azure verwenden, stellen Sie den auszuführenden Azure Sentinel-Agent manuell auf einem dedizierten Linux-Server bereit.


1. Klicken Sie im Azure Sentinel-Portal auf **Data connectors** (Datenconnectors), und wählen Sie Ihren Appliancetyp aus.
1. Wählen Sie unter **Linux Syslog agent configuration** (Linux-Syslog-Agent-Konfiguration) die Option **Manual deployment** (Manuelle Bereitstellung) aus, um einen dedizierten virtuellen Linux-Computer zu erstellen.
   1. Wählen Sie unter **Download and install the Syslog agent** (Syslog-Agent herunterladen und installieren) die Option **Non-Azure Linux machine** (Nicht von Azure stammender virtueller Computer) aus. 
   1. Wählen Sie auf dem daraufhin geöffneten Bildschirm **Direkt-Agent** die Option **Agent für Linux** aus, um den Agent herunterzuladen, oder führen Sie den folgenden Befehl aus, um den Agent auf Ihren Linux-Computer herunterzuladen: `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`
      1. Legen Sie auf dem Connector-Bildschirm unter **Configure and forward Syslog** (Syslog konfigurieren und weiterleiten) fest, welchen Syslog-Daemon (**rsyslog.d** oder **syslog-ng**) Sie verwenden. 
      1. Kopieren Sie die folgenden Befehle, und führen Sie sie auf der Appliance aus:
         - Wenn Sie „rsyslog“ ausgewählt haben, gehen Sie wie folgt vor:
           1. Weisen Sie den Syslog-Daemon an, in der Umgebung „local_4“ zu lauschen und die Syslog-Nachrichten über den Port 25226 an den Azure Sentinel-Agent zu senden. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
           2. Laden Sie die [Konfigurationsdatei „security_events“](https://aka.ms/asi-syslog-config-file-linux) herunter, mit der der Syslog-Agent zum Lauschen am Port 25226 konfiguriert wird, und installieren Sie sie. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Ersetzen Sie hier {0} durch die GUID Ihres Arbeitsbereichs.
           3. Starten Sie den Syslog-Daemon neu: `sudo service rsyslog restart`
         - Wenn Sie „syslog-ng“ ausgewählt haben, gehen Sie wie folgt vor:
            1. Weisen Sie den Syslog-Daemon an, in der Umgebung „local_4“ zu lauschen und die Syslog-Nachrichten über den Port 25226 an den Azure Sentinel-Agent zu senden. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            2. Laden Sie die [Konfigurationsdatei „security_events“](https://aka.ms/asi-syslog-config-file-linux) herunter, mit der der Syslog-Agent zum Lauschen am Port 25226 konfiguriert wird, und installieren Sie sie. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Ersetzen Sie hier {0} durch die GUID Ihres Arbeitsbereichs.
            3. Starten Sie den Syslog-Daemon neu: `sudo service syslog-ng restart`
      1. Starten Sie den Syslog-Agent mit dem folgenden Befehl neu: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Vergewissern Sie sich, dass im Agent-Protokoll keine Fehler enthalten sind, indem Sie den folgenden Befehl ausführen: `tail /var/opt/microsoft/omsagent/log/omsagent.log`
  
 Um das relevante Schema in Log Analytics für die CEF-Ereignisse zu verwenden, suchen Sie nach `CommonSecurityLog`.

## <a name="step-2-forward-common-event-format-cef-logs-to-syslog-agent"></a>Schritt 2: Weiterleiten von CEF-Protokollen (Common Event Format) an den Syslog-Agent

Stellen Sie Ihre Sicherheitslösung so ein, dass sie Syslog-Nachrichten im CEF-Format an Ihren Syslog-Agent sendet. Stellen Sie sicher, dass Sie dieselben Parameter verwenden, die in Ihrer Agent-Konfiguration verwendet werden. Dies sind normalerweise:

- Port: 514
- Facility: local4

## <a name="step-3-validate-connectivity"></a>Schritt 3: Überprüfen der Konnektivität

Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden. 

1. Stellen Sie sicher, dass Sie die richtige Einrichtung verwenden. Die Einrichtung muss in Ihrem Gerät und in Azure Sentinel identisch sein. Sie können überprüfen, welche Einrichtungsdatei Sie in Azure Sentinel verwenden, und sie in der Datei `security-config-omsagent.conf` ändern. 

2. Stellen Sie sicher, dass für Ihre Protokolle der richtige Port im Syslog-Agent verwendet wird. Führen Sie diesen Befehl auf dem Syslog-Agent-Computer aus: `tcpdump -A -ni any  port 514 -vv` Durch diesen Befehl werden die Protokolle angezeigt, die vom Gerät an den Syslog-Computer übermittelt werden. Stellen Sie sicher, dass Protokolle von der Quellappliance über den richtigen Port und die richtige Einrichtung empfangen werden.

3. Stellen Sie sicher, dass die Protokolle, die Sie senden [RFC 5424](https://tools.ietf.org/html/rfc542) erfüllen.

4. Auf dem Computer, auf dem der Syslog-Agent ausgeführt wird, stellen Sie sicher, dass die Ports 514 und 25226 geöffnet sind und lauschen, indem Sie den Befehl `netstat -a -n:` verwenden. Weitere Informationen zur Verwendung dieses Befehls finden Sie auf der [Linux-Manpage „netstat(8)“](https://linux.die.net/man/8/netstat). Wenn sie ordnungsgemäß lauschen, wird Folgendes angezeigt:

   ![Azure Sentinel-Ports](./media/connect-cef/ports.png) 

5. Stellen Sie sicher, dass der Daemon darauf eingestellt ist, an Port 514 zu lauschen, an dem Sie die Protokolle senden.
    - Für rsyslog:<br>Stellen Sie sicher, dass die Datei `/etc/rsyslog.conf` diese Konfiguration enthält:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Weitere Informationen finden Sie unter [imudp: UDP-Syslog-Eingabemodul](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) und [imtcp: TCP-Syslog-Eingabemodul](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module).

   - Für syslog-ng:<br>Stellen Sie sicher, dass die Datei `/etc/syslog-ng/syslog-ng.conf` diese Konfiguration enthält:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Weitere Informationen finden Sie unter [imudp: UDP-Syslog-Eingabe-Modul] (Weitere Informationen finden Sie im [syslog-ng Open Source-Edition 3.16 – Administratorhandbuch](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Stellen Sie sicher, dass der Syslog-Daemon und der Agent kommunizieren. Führen Sie diesen Befehl auf dem Syslog-Agent-Computer aus: `tcpdump -A -ni any  port 25226 -vv` Durch diesen Befehl werden die Protokolle angezeigt, die vom Gerät an den Syslog-Computer übermittelt werden. Vergewissern Sie sich, dass die Protokolle auch vom Agent empfangen werden.

6. Wenn beide diese Befehle erfolgreiche Ergebnisse zurückgegeben haben, überprüfen Sie in Log Analytics, ob Ihre Protokolle eingehen. Alle Ereignisse, die von diesen Appliances übermittelt werden, werden in Log Analytics unformatiert unter dem Typ `CommonSecurityLog` angezeigt.

7. Hier können Sie überprüfen, ob Fehler aufgetreten sind oder die Protokolle nicht eingehen: `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. Wenn dort Fehler wegen fehlender Protokollformatübereinstimmung aufgeführt werden, wechseln Sie zu `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`, und sehen Sie sich die Datei `security_events.conf` an, und stellen Sie sicher, dass Ihre Protokolle das in dieser Datei angezeigte reguläre Ausdrucksformat einhalten.

8. Vergewissern Sie sich, dass die Standardgröße der Syslog-Nachrichten auf 2.048 Bytes (2 KB) beschränkt ist. Wenn die Protokolle zu lang sind, aktualisieren Sie die Konfigurationsdatei „security_events.conf“ mithilfe des folgenden Befehls: `message_length_limit 4096`


## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie gelernt, wie Sie CEF-Appliances mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats.md).

