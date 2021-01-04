---
title: Überprüfen der Konnektivität mit Azure Sentinel | Microsoft-Dokumentation
description: Überprüfen Sie die Konnektivität ihrer Sicherheitslösung, um sicherzustellen, dass CEF-Nachrichten an Azure Sentinel weitergeleitet werden.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: 974418a1b3c1e7fe93b2f6839c16169e5bd5abc5
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696998"
---
# <a name="step-3-validate-connectivity"></a>SCHRITT 3: Überprüfen der Konnektivität

Nachdem Sie Ihre Protokollweiterleitung bereitgestellt haben (in Schritt 1) und Ihre Sicherheitslösung für das Senden von CEF-Nachrichten konfiguriert haben (in Schritt 2), befolgen Sie diese Anweisungen, um die Konnektivität zwischen Ihrer Sicherheitslösung und Azure Sentinel zu überprüfen. 

## <a name="prerequisites"></a>Voraussetzungen

- Sie müssen über erhöhte Berechtigungen (sudo) auf dem Computer mit der Protokollweiterleitung verfügen.

- Auf dem Computer mit der Protokollweiterleitung muss **Python 2.7** oder **3** installiert sein.<br>
Verwenden Sie den Befehl `python –version` zum Überprüfen dieser Voraussetzung.

- Möglicherweise benötigen Sie während dieses Vorgangs die ID und den Primärschlüssel des Arbeitsbereichs. Sie finden diese in der Arbeitsbereichsressource unter **Agent-Verwaltung**.

## <a name="how-to-validate-connectivity"></a>Überprüfen der Konnektivität

1. Öffnen Sie im Azure Sentinel-Navigationsmenü die Option **Protokolle**. Führen Sie mithilfe des Schemas **CommonSecurityLog** eine Abfrage aus, um zu überprüfen, ob Sie Protokolle von Ihrer Sicherheitslösung erhalten.<br>
Achten Sie darauf, dass es etwa 20 Minuten dauern kann, bis Ihre Protokolle in **Log Analytics** angezeigt werden. 

1. Wenn keine Ergebnisse der Abfrage angezeigt werden, vergewissern Sie sich, dass von Ihrer Sicherheitslösung Ereignisse generiert werden, oder versuchen Sie, einige Ergebnisse zu generieren, und vergewissern Sie sich, dass sie an den von Ihnen festgelegten Syslog-Weiterleitungscomputer weitergeleitet werden. 

1. Führen Sie das folgende Skript für die Protokollweiterleitung aus (geben Sie die Arbeitsbereichs-ID anstelle des Platzhalters ein), um die Konnektivität zwischen Ihrer Sicherheitslösung, der Protokollweiterleitung und Azure Sentinel zu überprüfen. Das Skript überprüft, ob der Daemon an den richtigen Ports lauscht, die Weiterleitung ordnungsgemäß konfiguriert ist und die Kommunikation zwischen dem Daemon und dem Log Analytics-Agent nicht blockiert wird. Außerdem sendet es auch TestCommonEventFormat-Pseudonachrichten, um die End-to-End-Konnektivität zu überprüfen. <br>

    ```bash
    sudo wget -O https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]` 
    ```

   - Möglicherweise werden Sie in einer Meldung aufgefordert, einen Befehl auszuführen, um ein Problem mit der **Zuordnung im Feld *Computer*** zu beheben. Ausführliche Informationen finden Sie in der [Erläuterung im Validierungsskript](#mapping-command).

    - Möglicherweise werden Sie in einer Meldung aufgefordert, einen Befehl auszuführen, um ein Problem mit der **Analyse von Cisco ASA-Firewallprotokollen** zu beheben. Ausführliche Informationen finden Sie in der [Erläuterung im Validierungsskript](#parsing-command).

## <a name="validation-script-explained"></a>Erläuterung des Überprüfungsskripts

Das Überprüfungsskript führt die folgenden Überprüfungen durch:

# <a name="rsyslog-daemon"></a>[rsyslog-Daemon](#tab/rsyslog)

1. Überprüft, ob die Datei<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    vorhanden und gültig ist.

1. Überprüft, ob die Datei den folgenden Text enthält:

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Überprüft anhand des folgenden Befehls, ob die Analyse für Cisco ASA-Firewallereignisse wie erwartet konfiguriert ist: 

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>Wenn ein Problem mit der Analyse vorliegt, generiert das Skript eine Fehlermeldung, in der Sie aufgefordert werden, **den folgenden Befehl manuell auszuführen** (ersetzen Sie den Platzhalter durch die Arbeitsbereichs-ID). Mit dem Befehl wird die korrekte Analyse sichergestellt, und der Agent wird neu gestartet.
    
        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Überprüft anhand des folgenden Befehls, ob das Feld *Computer* in der Syslog-Quelle im Log Analytics-Agent ordnungsgemäß zugeordnet ist: 

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>Wenn ein Problem mit der Zuordnung vorliegt, generiert das Skript eine Fehlermeldung, in der Sie aufgefordert werden, **den folgenden Befehl manuell auszuführen** (ersetzen Sie den Platzhalter durch die Arbeitsbereichs-ID). Mit dem Befehl wird die korrekte Zuordnung sichergestellt, und der Agent wird neu gestartet.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Überprüft, ob auf dem Computer Sicherheitsverbesserungen vorhanden sind, die möglicherweise den Netzwerkverkehr blockieren (z. B. eine Hostfirewall).

1. Überprüft, ob der Syslog-Daemon (rsyslog) ordnungsgemäß konfiguriert ist und Nachrichten, die er als CEF identifiziert, an den Log Analytics-Agent an TCP-Port 25226 senden kann:

    - Konfigurationsdatei: `/etc/rsyslog.d/security-config-omsagent.conf`

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. Startet den Syslog-Daemon und den Log Analytics-Agent neu:

    ```bash
    service rsyslog restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Überprüft, ob die erforderlichen Verbindungen hergestellt wurden: TCP 514 für das Empfangen von Daten, TCP 25226 für die interne Kommunikation zwischen dem Syslog-Daemon und dem Log Analytics-Agent:

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Überprüft, ob der Syslog-Daemon Daten an Port 514 empfängt und ob der Agent Daten an Port 25226 empfängt:

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. Sendet Pseudodaten an Port 514 von localhost. Diese Daten sollten mithilfe der folgenden Abfrage im Azure Sentinel-Arbeitsbereich zu erkennen sein:

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```

# <a name="syslog-ng-daemon"></a>[syslog-ng daemon](#tab/syslogng)

1. Überprüft, ob die Datei<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    vorhanden und gültig ist.

1. Überprüft, ob die Datei den folgenden Text enthält:

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Überprüft anhand des folgenden Befehls, ob die Analyse für Cisco ASA-Firewallereignisse wie erwartet konfiguriert ist: 

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>Wenn ein Problem mit der Analyse vorliegt, generiert das Skript eine Fehlermeldung, in der Sie aufgefordert werden, **den folgenden Befehl manuell auszuführen** (ersetzen Sie den Platzhalter durch die Arbeitsbereichs-ID). Mit dem Befehl wird die korrekte Analyse sichergestellt, und der Agent wird neu gestartet.
    
        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Überprüft anhand des folgenden Befehls, ob das Feld *Computer* in der Syslog-Quelle im Log Analytics-Agent ordnungsgemäß zugeordnet ist: 

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>Wenn ein Problem mit der Zuordnung vorliegt, generiert das Skript eine Fehlermeldung, in der Sie aufgefordert werden, **den folgenden Befehl manuell auszuführen** (ersetzen Sie den Platzhalter durch die Arbeitsbereichs-ID). Mit dem Befehl wird die korrekte Zuordnung sichergestellt, und der Agent wird neu gestartet.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Überprüft, ob auf dem Computer Sicherheitsverbesserungen vorhanden sind, die möglicherweise den Netzwerkverkehr blockieren (z. B. eine Hostfirewall).

1. Überprüft, ob der Syslog-Daemon (rsyslog-ng) ordnungsgemäß konfiguriert ist und Nachrichten, die er (über einen regulären Ausdruck) als CEF identifiziert, an den Log Analytics-Agent an TCP-Port 25226 senden kann:

    - Konfigurationsdatei: `/etc/syslog-ng/conf.d/security-config-omsagent.conf`

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
        destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. Startet den Syslog-Daemon und den Log Analytics-Agent neu:

    ```bash
    service syslog-ng restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Überprüft, ob die erforderlichen Verbindungen hergestellt wurden: TCP 514 für das Empfangen von Daten, TCP 25226 für die interne Kommunikation zwischen dem Syslog-Daemon und dem Log Analytics-Agent:

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Überprüft, ob der Syslog-Daemon Daten an Port 514 empfängt und ob der Agent Daten an Port 25226 empfängt:

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. Sendet Pseudodaten an Port 514 von localhost. Diese Daten sollten mithilfe der folgenden Abfrage im Azure Sentinel-Arbeitsbereich zu erkennen sein:

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```
---

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie CEF-Appliances mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](./tutorial-detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](tutorial-monitor-your-data.md), um Ihre Daten zu überwachen.
