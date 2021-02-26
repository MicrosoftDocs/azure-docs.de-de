---
title: Verbinden von Syslog-Daten mit Azure Sentinel | Microsoft-Dokumentation
description: Verbinden Sie einen Computer oder eine Appliance mit Syslog-Unterstützung mit Azure Sentinel, indem Sie einen Agent auf einem Linux-Computer zwischen der Appliance und Azure Sentinel verwenden.
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
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 35c8c2aa31887feb294b04b8a88bbe5478659e5e
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807902"
---
# <a name="collect-data-from-linux-based-sources-using-syslog"></a>Sammeln von Daten aus Linux-basierten Quellen mithilfe von Syslog

Sie können Ereignisse von Linux-basierten Computern oder Appliances, die Syslog unterstützen, in Azure Sentinel streamen, indem Sie den Log Analytics-Agent für Linux (ehemals OMS-Agent) verwenden. Sie können dies für jeden Computer durchführen, auf dem Sie den Log Analytics-Agent direkt auf dem Computer installieren können. Der native Syslog-Daemon des Computers sammelt lokale Ereignisse der angegebenen Typen und leitet diese lokal an den Agent weiter, der sie wiederum in Ihren Log Analytics-Arbeitsbereich streamt.

> [!NOTE]
> - Wenn Ihre Appliance das **Common Event Format (CEF) über Syslog** unterstützt, wird ein umfangreicheres Dataset erfasst, und die Daten werden bei der Erfassung analysiert. Sie sollten diese Option auswählen und die Anleitungen unter [Verbinden Ihrer externen Lösung mithilfe von CEF](connect-common-event-format.md) befolgen.
>
> - Log Analytics unterstützt die Erfassung der von den Daemons **rsyslog** oder **syslog-ng** gesendeten Meldungen, wobei „rsyslog“ der Standarddaemon ist. Der standardmäßige Syslog-Daemon in Version 5 von Red Hat Enterprise Linux (RHEL), CentOS, und in der Oracle Linux-Version (**sysklog**) wird für die Erfassung von Syslog-Ereignissen nicht unterstützt. Der rsyslog-Daemon sollte installiert und so konfiguriert werden, dass er sysklog ersetzt, um syslog-Daten von dieser Version dieser Verteilung zu sammeln.

## <a name="how-it-works"></a>Funktionsweise

**Syslog** ist ein gängiges Protokoll zur Ereignisprotokollierung für Linux. Wenn der **Log Analytics-Agent für Linux** auf Ihrem virtuellen Computer oder Ihrer Appliance installiert ist, konfiguriert die Installationsroutine den lokalen Syslog-Daemon für die Weiterleitung von Meldungen an den Agent an TCP-Port 25224. Der Agent sendet dann die Meldung über HTTPS an Ihren Log Analytics-Arbeitsbereich, wo sie in einen Ereignisprotokolleintrag in der Syslog-Tabelle in **Azure Sentinel > Protokolle** analysiert wird.

Weitere Informationen finden Sie unter [Syslog-Datenquellen in Azure Monitor](../azure-monitor/platform/data-sources-syslog.md).

## <a name="configure-syslog-collection"></a>Konfigurieren der Syslog-Sammlung

### <a name="configure-your-linux-machine-or-appliance"></a>Konfigurieren Ihres Computers oder Ihrer Appliance unter Linux

1. Wählen Sie in Azure Sentinel die Option **Datenconnectors**, und wählen Sie dann den Connector **Syslog** aus.

1. Wählen Sie auf dem Blatt **Syslog** die Option **Connectorseite öffnen** aus.

1. Installieren Sie den Linux-Agent. Unter **Wählen Sie aus, wo der Agent installiert werden soll:**
    
    **Für einen virtuellen Linux-Computer (Azure):**
      
    1. Wählen Sie **Agent auf virtuellem Linux-Computer (Azure) installieren** aus.
    
    1. Klicken Sie auf den Link **Agent für virtuelle Linux-Computer (Azure) herunterladen und installieren**. 
    
    1. Klicken Sie auf dem Blatt **Virtuelle Computer** auf einen virtuellen Computer, auf dem der Agent installiert werden soll, und klicken Sie dann auf **Verbinden**. Wiederholen Sie diesen Schritt für jeden virtuellen Computer, den Sie verbinden möchten.
    
    **Für jeden weiteren Linux-Computer:**

    1. Wählen Sie **Agent auf einem Linux-Computer (kein Azure) installieren** aus.

    1. Klicken Sie auf den Link **Agent für Linux-Computer (kein Azure) herunterladen und installieren**. 

    1. Klicken Sie auf dem Blatt **Agents-Verwaltung** auf die Registerkarte **Linux-Server**, kopieren Sie dann den Befehl für **Agent für Linux herunterladen und Onboarding durchführen**, und führen Sie ihn auf Ihrem Linux-Computer aus. 
    
   > [!NOTE]
   > Konfigurieren Sie die Sicherheitseinstellungen für diese Computer entsprechend der Sicherheitsrichtlinie Ihrer Organisation. Beispielsweise können Sie die Netzwerkeinstellungen so konfigurieren, dass sie der Sicherheitsrichtlinie für das Netzwerk der Organisation entsprechen, und die Ports und Protokolle im Daemon Ihren Sicherheitsanforderungen entsprechend ändern.

### <a name="configure-the-log-analytics-agent"></a>Konfigurieren des Log Analytics-Agent

1. Klicken Sie unten auf dem Blatt des Syslog-Connectors auf den Link **Konfiguration der erweiterten Einstellungen für den Arbeitsbereich öffnen >** .

1. Wählen Sie auf dem Blatt **Erweiterte Einstellungen** die Option **Daten** > **Syslog** aus. Fügen Sie dann die Einrichtungen hinzu, die der Connector erfassen soll.
    
    - Fügen Sie die Einrichtungen hinzu, die Ihre Syslog-Appliance in ihren Protokollheadern enthält. 
    
    - Wenn Sie für die zu sammelnden Daten die Erkennung ungewöhnlicher SSH-Anmeldungen verwenden möchten, fügen Sie **auth** und **authpriv** hinzu. Im [folgenden Abschnitt](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) finden Sie weitere Details.

1. Wenn Sie alle zu überwachenden Einrichtungen hinzugefügt und alle Schweregradoptionen für jede Einrichtung angepasst haben, aktivieren Sie das Kontrollkästchen **Nachstehende Konfiguration auf meine Computer anwenden**.

1. Wählen Sie **Speichern** aus. 

1. Stellen Sie auf Ihrem virtuellen Computer oder der Appliance sicher, dass die von Ihnen angegebenen Einrichtungen gesendet werden.

1. Um die Syslog-Protokolldaten in **Protokolle** abzufragen, geben Sie `Syslog` in das Abfragefenster ein.

1. Sie können mit den in [Verwenden von Funktionen in Azure Monitor-Protokollabfragen](../azure-monitor/log-query/functions.md) beschriebenen Abfrageparametern Ihre Syslog-Meldungen analysieren. Sie können die Abfrage dann als neue Log Analytics-Funktion speichern und als neuen Datentyp verwenden.

> [!NOTE]
> **Verwenden desselben Computers zum Weiterleiten von unformatierten Syslog-Nachrichten *und* CEF-Nachrichten**
>
>
> Sie können Ihren vorhandenen [Computer für die CEF-Protokollweiterleitung](connect-cef-agent.md) verwenden, um Protokolle auch aus einfachen Syslog-Quellen zu erfassen und weiterzuleiten. Sie müssen jedoch die folgenden Schritte ausführen, um zu vermeiden, dass Ereignisse in beiden Formaten an Azure Sentinel gesendet werden, da dies zu einer Duplizierung von Ereignissen führt.
>
>    Wenn Sie die [Datensammlung von Ihren CEF-Quellen](connect-common-event-format.md) bereits eingerichtet und den Log Analytics-Agent wie oben konfiguriert haben:
>
> 1. Sie müssen auf jedem Computer, der Protokolle im CEF-Format sendet, die Syslog-Konfigurationsdatei bearbeiten, um die Einrichtungen zu entfernen, die zum Senden von CEF-Meldungen verwendet werden. Auf diese Weise werden die in CEF gesendeten Einrichtungen nicht auch in Syslog gesendet. Detaillierte Anweisungen hierzu finden Sie unter [Konfigurieren von Syslog auf dem Linux-Agent](../azure-monitor/platform/data-sources-syslog.md#configure-syslog-on-linux-agent).
>
> 1. Sie müssen auf diesen Computern den folgenden Befehl ausführen, um die Synchronisierung des Agents mit der Syslog-Konfiguration in Azure Sentinel zu deaktivieren. Dadurch wird sichergestellt, dass die von Ihnen im vorherigen Schritt vorgenommene Konfigurationsänderung nicht überschrieben wird.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`


### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Konfigurieren des Syslog-Connectors für die Erkennung ungewöhnlicher SSH-Anmeldungen

> [!IMPORTANT]
> Die Erkennung ungewöhnlicher SSH-Anmeldungen befindet sich derzeit in der öffentlichen Vorschauphase.
> Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel kann Machine Learning (ML) auf die Syslog-Daten anwenden, um ungewöhnliche SSH-Anmeldungen (Secure Shell) zu identifizieren. Mögliche Szenarien:

- Unmöglicher Ortswechsel – wenn zwei erfolgreiche Anmeldeereignisse von zwei Standorten aus auftreten, die innerhalb des Zeitraums zwischen den beiden Anmeldeereignissen nicht erreichbar sind.
- Unerwarteter Standort – der Standort, von dem aus eine erfolgreiche Anmeldung durchgeführt wurde, ist verdächtig. Beispielsweise ist der Standort in jüngster Zeit nicht aufgetreten.
 
Diese Erkennung erfordert eine bestimmte Konfiguration des Syslog-Datenconnectors: 

1. Stellen Sie in Schritt 5 des vorherigen Verfahrens sicher, dass sowohl **auth** als auch **authpriv** als zu überwachende Einrichtungen ausgewählt sind. Übernehmen Sie die Standardeinstellungen für die Schweregradoptionen, sodass sie alle ausgewählt sind. Beispiel:
    
    > [!div class="mx-imgBorder"]
    > ![Einrichtungen, die für die Erkennung ungewöhnlicher SSH-Anmeldungen erforderlich sind](./media/connect-syslog/facilities-ssh-detection.png)

2. Lassen Sie ausreichend Zeit zum Sammeln der Syslog-Informationen. Navigieren Sie dann zu **Azure Sentinel – Protokolle**, kopieren Sie die folgende Abfrage, und fügen Sie sie ein:
    
    ```kusto
    Syslog
    | where Facility in ("authpriv","auth")
    | extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)
    | where isnotempty(c)
    | count 
    ```
    
    Ändern Sie bei Bedarf den **Zeitbereich**, und wählen Sie **Ausführen** aus.
    
    Wenn die resultierende Anzahl 0 (null) ist, überprüfen Sie die Konfiguration des Connectors, und überprüfen Sie, ob in dem Zeitraum, den Sie für die Abfrage angegeben haben, auf den überwachten Computern erfolgreiche Anmeldungen durchgeführt wurden.
    
    Wenn die resultierende Anzahl größer als 0 (null) ist, eignen sich die Syslog-Daten für die Erkennung ungewöhnlicher SSH-Anmeldungen. Sie aktivieren diese Erkennung über **Analyse** >  **Rule templates** (Regelvorlagen)  >  **(Preview) Anomalous SSH Login Detection** ((Vorschau) Erkennung ungewöhnlicher SSH-Anmeldungen).

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie gelernt, wie Sie lokale Syslog-Appliances mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](tutorial-monitor-your-data.md), um Ihre Daten zu überwachen.

