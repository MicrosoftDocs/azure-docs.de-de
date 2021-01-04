---
title: Bereitstellen der Protokollweiterleitung zum Verbinden von CEF-Daten mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Agent zum Verknüpfen von CEF-Daten mit Azure Sentinel bereitstellen.
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
ms.openlocfilehash: ead878daaab977c77b3ab36f42ccfe4d01d7bc03
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548629"
---
# <a name="step-1-deploy-the-log-forwarder"></a>Schritt 1: Bereitstellen der Protokollweiterleitung


In diesem Schritt bestimmen und konfigurieren Sie den Linux-Computer, der die Protokolle von Ihrer Sicherheitslösung an Ihren Azure Sentinel-Arbeitsbereich weiterleiten soll. Bei diesem Computer kann es sich um einen physischen oder virtuellen Computer in Ihrer lokalen Umgebung, eine Azure-VM oder eine VM in einer anderen Cloud handeln. Mithilfe des bereitgestellten Links führen Sie ein Skript auf dem festgelegten Computer aus, das die folgenden Aufgaben ausführt:

- Es installiert den Log Analytics-Agent für Linux (auch als OMS-Agent bezeichnet) und konfiguriert ihn für die folgenden Zwecke:
    - Lauschen auf CEF-Nachrichten vom integrierten Linux-Syslog-Daemon am TCP-Port 25226
    - Sicheres Senden der Nachrichten über TLS an Ihren Azure Sentinel-Arbeitsbereich, wo sie analysiert und ergänzt werden

- Es konfiguriert den integrierten Linux-Syslog-Daemon (rsyslog.d/syslog-ng) für die folgenden Zwecke:
    - Lauschen auf Syslog-Nachrichten von Ihren Sicherheitslösungen an TCP-Port 514
    - Weiterleiten nur der Nachrichten, die mit dem Log Analytics-Agent auf localhost als CEF identifiziert werden, über TCP-Port 25226
 
## <a name="prerequisites"></a>Voraussetzungen

- Sie müssen über erhöhte Berechtigungen (sudo) auf dem festgelegten Linux-Computer verfügen.

- Auf dem Linux-Computer muss **Python 2.7** oder **3** installiert sein.<br>Verwenden Sie den Befehl `python -version` zum Überprüfen dieser Voraussetzung.

- Der Linux-Computer darf nicht mit Azure-Arbeitsbereichen verbunden sein, bevor Sie den Log Analytics-Agent installieren.

- Möglicherweise benötigen Sie während dieses Vorgangs die ID und den Primärschlüssel des Arbeitsbereichs. Sie finden diese in der Arbeitsbereichsressource unter **Agent-Verwaltung**.

## <a name="run-the-deployment-script"></a>Ausführen des Bereitstellungsskripts
 
1. Wählen Sie im Azure Sentinel-Navigationsmenü die Option **Datenconnectors** aus. Klicken Sie in der Liste der Connectors auf die Kachel **Common Event Format (CEF)** und dann rechts unten auf die Schaltfläche **Connectorseite öffnen**. 

1. Kopieren Sie unter **1.2 CEF-Collector auf dem Linux-Computer installieren** den unter **Run the following script to install and apply the CEF collector** (Folgendes Skript zum Installieren und Anwenden des CEF-Collectors ausführen) oder im nachstehenden Text aufgeführten Link (ersetzen Sie die Platzhalter durch die ID und den Primärschlüssel des Arbeitsbereichs):

    ```bash
    sudo wget -O https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
    ```

1. Wenn das Skript ausgeführt wird, stellen Sie sicher, dass keine Fehler- oder Warnmeldungen angezeigt werden.
    - Möglicherweise werden Sie in einer Meldung aufgefordert, einen Befehl auszuführen, um ein Problem mit der Zuordnung im Feld *Computer* zu beheben. Ausführliche Informationen finden Sie in der [Erläuterung im Bereitstellungsskript](#mapping-command).

1. Fahren Sie mit [SCHRITT 2 fort: Konfigurieren Ihrer Sicherheitslösung zum Weiterleiten von CEF-Nachrichten](connect-cef-solution-config.md) .


> [!NOTE]
> **Verwenden desselben Computers zum Weiterleiten von unformatierten Syslog-Nachrichten *und* CEF-Nachrichten**
>
> Wenn Sie beabsichtigen, den Computer mit der Protokollweiterleitung zu verwenden, um [Syslog-Nachrichten](connect-syslog.md) und CEF-Nachrichten weiterzuleiten, gehen Sie folgendermaßen vor, um das Duplizieren von Ereignissen in den Syslog- und CommonSecurityLog-Tabellen zu vermeiden:
>
> 1. Sie müssen auf jedem Quellcomputer, der Protokolle im CEF-Format an die Weiterleitung sendet, die Syslog-Konfigurationsdatei bearbeiten, um die Einrichtungen zu entfernen, die zum Senden von CEF-Nachrichten verwendet werden. Auf diese Weise werden die in CEF gesendeten Einrichtungen nicht auch in Syslog gesendet. Detaillierte Anweisungen hierzu finden Sie unter [Konfigurieren von Syslog auf dem Linux-Agent](../azure-monitor/platform/data-sources-syslog.md#configure-syslog-on-linux-agent).
>
> 1. Sie müssen auf diesen Computern den folgenden Befehl ausführen, um die Synchronisierung des Agents mit der Syslog-Konfiguration in Azure Sentinel zu deaktivieren. Dadurch wird sichergestellt, dass die von Ihnen im vorherigen Schritt vorgenommene Konfigurationsänderung nicht überschrieben wird.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

> [!NOTE]
> **Ändern der Quelle des Felds „TimeGenerated“**
>
> - Standardmäßig füllt der Log Analytics-Agent das Feld *TimeGenerated* im Schema mit der Zeit auf, zur der der Agent das Ereignis vom Syslog-Daemon empfangen hat. Folglich wird die Zeit, zu der das Ereignis im Quellsystem generiert wurde, nicht in Azure Sentinel erfasst.
>
> - Sie können jedoch den folgenden Befehl ausführen, um das Skript `TimeGenerated.py` herunterzuladen und auszuführen. Mit diesem Skript wird der Log Analytics-Agent so konfiguriert, dass das Feld *TimeGenerated* mit der ursprünglichen Zeit des Ereignisses aus dem Quellsystem aufgefüllt wird, und nicht mit der Zeit, zu der es vom Agent empfangen wurde.
>
>    ```bash
>    wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
>    ```

## <a name="deployment-script-explained"></a>Erläuterung des Bereitstellungsskripts

Im Folgenden finden Sie eine Beschreibung der Aktionen des Bereitstellungsskripts in einzelnen Befehlen.

Wählen Sie einen Syslog-Daemon aus, um die entsprechende Beschreibung anzuzeigen.

# <a name="rsyslog-daemon"></a>[rsyslog-Daemon](#tab/rsyslog)

1. **Herunterladen und Installieren des Log Analytics-Agents:**

    - Herunterladen des Installationsskripts für den Log Analytics-Linux-Agent (OMS)

        ```bash
        wget -O https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/
            onboard_agent.sh
        ```

    - Installieren des Log Analytics-Agents
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **Festlegen der Konfiguration des Log Analytics-Agents zum Lauschen an Port 25226 und Weiterleiten von CEF-Nachrichten an Azure Sentinel:**

    - Herunterladen der Konfiguration aus dem GitHub-Repository für den Log Analytics-Agent

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Konfigurieren des Syslog-Daemons:**

    - Öffnen von Port 514 für TCP-Kommunikation mithilfe der Syslog-Konfigurationsdatei `/etc/rsyslog.conf`

    - Konfigurieren des Daemons zum Weiterleiten von CEF-Nachrichten an den Log Analytics-Agent an TCP-Port 25226 durch Einfügen einer speziellen Konfigurationsdatei `security-config-omsagent.conf` in das Verzeichnis `/etc/rsyslog.d/` des Syslog-Daemons

        Inhalt der Datei `security-config-omsagent.conf`:

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. **Neustarten des Syslog-Daemons und des Log Analytics-Agents:**

    - Neustarten des rsyslog-Daemons
    
        ```bash
        service rsyslog restart
        ```

    - Neustarten des Log Analytics-Agents

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **Überprüfen, ob das Feld *Computer* wie erwartet zugeordnet wurde:**

    - Überprüft anhand des folgenden Befehls, ob das Feld *Computer* in der Syslog-Quelle ordnungsgemäß im Log Analytics-Agent zugeordnet ist: 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>Wenn ein Problem mit der Zuordnung vorliegt, generiert das Skript eine Fehlermeldung, in der Sie aufgefordert werden, **den folgenden Befehl manuell auszuführen** (ersetzen Sie den Platzhalter durch die Arbeitsbereichs-ID). Mit dem Befehl wird die korrekte Zuordnung sichergestellt, und der Agent wird neu gestartet.
    
        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

# <a name="syslog-ng-daemon"></a>[syslog-ng daemon](#tab/syslogng)

1. **Herunterladen und Installieren des Log Analytics-Agents:**

    - Herunterladen des Installationsskripts für den Log Analytics-Linux-Agent (OMS)

        ```bash
        wget -O https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/
            onboard_agent.sh
        ```

    - Installieren des Log Analytics-Agents
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **Festlegen der Konfiguration des Log Analytics-Agents zum Lauschen an Port 25226 und Weiterleiten von CEF-Nachrichten an Azure Sentinel:**

    - Herunterladen der Konfiguration aus dem GitHub-Repository für den Log Analytics-Agent

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Konfigurieren des Syslog-Daemons:**

    - Öffnen von Port 514 für TCP-Kommunikation mithilfe der Syslog-Konfigurationsdatei `/etc/syslog-ng/syslog-ng.conf`

    - Konfigurieren des Daemons zum Weiterleiten von CEF-Nachrichten an den Log Analytics-Agent an TCP-Port 25226 durch Einfügen einer speziellen Konfigurationsdatei `security-config-omsagent.conf` in das Verzeichnis `/etc/syslog-ng/conf.d/` des Syslog-Daemons

        Inhalt der Datei `security-config-omsagent.conf`:

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
        destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. **Neustarten des Syslog-Daemons und des Log Analytics-Agents:**

    - Neustarten des syslog-ng-Daemons
    
        ```bash
        service syslog-ng restart
        ```

    - Neustarten des Log Analytics-Agents

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **Überprüfen, ob das Feld *Computer* wie erwartet zugeordnet wurde:**

    - Überprüft anhand des folgenden Befehls, ob das Feld *Computer* in der Syslog-Quelle ordnungsgemäß im Log Analytics-Agent zugeordnet ist: 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>Wenn ein Problem mit der Zuordnung vorliegt, generiert das Skript eine Fehlermeldung, in der Sie aufgefordert werden, **den folgenden Befehl manuell auszuführen** (ersetzen Sie den Platzhalter durch die Arbeitsbereichs-ID). Mit dem Befehl wird die korrekte Zuordnung sichergestellt, und der Agent wird neu gestartet.
    
        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```
---

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie gelernt, wie Sie den Log Analytics-Agent bereitstellen, um CEF-Appliances mit Azure Sentinel zu verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](./tutorial-detect-threats-built-in.md).
