---
title: Verbinden der Imperva WAF Gateway-Appliance mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Imperva WAF Gateway-Connector Imperva WAF-Protokolle in Azure Sentinel pullen. Zeigen Sie Imperva WAF-Daten in Arbeitsmappen an, erstellen Sie Warnungen, und verbessern Sie die Untersuchung.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 3094d20a921f9aa13e111e7af60955ce934b91db
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566577"
---
# <a name="connect-your-imperva-waf-gateway-appliance-to-azure-sentinel"></a>Verbinden der Imperva WAF Gateway-Appliance mit Azure Sentinel

> [!IMPORTANT]
> Der Imperva WAF Gateway-Connector befindet sich derzeit in der **Vorschauversion**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

In diesem Artikel wird erläutert, wie Sie die Imperva WAF Gateway-Appliance mit Azure Sentinel verbinden. Mit dem Imperva WAF Gateway-Datenconnector lassen sich Ihre Imperva WAF Gateway-Protokolle problemlos in Azure Sentinel erfassen, sodass Sie die Daten dann in Arbeitsmappen anzeigen, als Vorlage für benutzerdefinierte Warnungen und zur Verbesserung der Untersuchung von Problemen verwenden können. Die Integration von Imperva WAF Gateway und Azure Sentinel basiert auf CEF-formatierten Syslog-Nachrichten, einem Linux-basierten Server für die Protokollweiterleitung und dem Log Analytics-Agent.

> [!NOTE]
> Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Lese- und Schreibberechtigungen für Ihren Azure Sentinel-Arbeitsbereich.

- Sie müssen über Leseberechtigungen für freigegebene Schlüssel für den Arbeitsbereich verfügen. [Weitere Informationen zu Arbeitsbereichsschlüsseln](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)

## <a name="send-imperva-waf-gateway-logs-to-azure-sentinel"></a>Senden von Imperva WAF Gateway-Protokollen an Azure Sentinel

Um die Protokolle Ihrer Imperva WAF Gateway-Appliance in Azure Sentinel zu erfassen, müssen Sie die Appliance so konfigurieren, dass sie Syslog-Nachrichten im CEF-Format an einen Linux-basierten Protokollweiterleitungsserver (auf dem rsyslog oder syslog-ng ausgeführt wird) sendet. Auf diesem Server ist der Log Analytics-Agent installiert, der die Protokolle an Ihren Azure Sentinel-Arbeitsbereich weiterleitet.

1. Wählen Sie im Azure Sentinel-Navigationsmenü **Datenconnectors** aus.

1. Wählen Sie im Katalog **Datenconnectors** den Eintrag **Imperva WAF Gateway (Vorschau)** und dann **Connectorseite öffnen** aus.

1. Befolgen Sie die Anleitungen auf der Registerkarte **Anweisungen** unter **Konfiguration**:

    1. Unter **1. Konfiguration des Linux-Syslog-Agents**: Führen Sie diesen Schritt aus, wenn Sie noch keinen Protokollweiterleitungsserver ausführen oder einen anderen benötigen. Detaillierte Anleitungen und Erläuterungen finden Sie unter [SCHRITT 1: Bereitstellen des Protokollweiterleitungsservers](connect-cef-agent.md) in der Azure Sentinel-Dokumentation.

    1. Unter **2. CEF-Protokolle (Common Event Format) an den Syslog-Agent weiterleiten**: Für diesen Connector müssen eine **Aktionsschnittstelle** und ein **Aktionssatz** auf der **Imperva SecureSphere MX**-Verwaltungskonsole erstellt werden. Folgen Sie den Anweisungen von Imperva, um die [Imperva WAF Gateway-Warnungsprotokollierung in Azure Sentinel zu aktivieren](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert). Diese Konfiguration sollte die folgenden Elemente enthalten:
        - Protokollziel: Hostname und/oder IP-Adresse Ihres Protokollweiterleitungsservers.
        - Protokoll und Port: TCP 514.
        - Protokollformat: CEF.
        - Protokolltypen: Alle verfügbaren.

    1. Klicken Sie unter **3. Überprüfen der Verbindung**: Überprüfen Sie die Datenerfassung, indem Sie den Befehl von der Connectorseite kopieren und auf Ihrem Protokollweiterleitungsserver ausführen. Detaillierte Anleitungen und Erläuterungen finden Sie unter [SCHRITT 3: Überprüfen der Verbindung](connect-cef-verify.md) in der Azure Sentinel-Dokumentation.

        Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden.

## <a name="find-your-data"></a>Suchen von Daten

Nachdem eine Verbindung hergestellt wurde, werden die Daten in der Tabelle *CommonSecurityLog* im Abschnitt **Azure Sentinel** in **Logs** angezeigt.

Um Imperva WAF Gateway-Daten in Log Analytics abzufragen, kopieren Sie Folgendes in das Abfragefenster, wobei Sie bei der Auswahl andere Filter anwenden:

```kusto
CommonSecurityLog 
| where DeviceVendor == "Imperva Inc." 
| where DeviceProduct == "WAF Gateway" 
| where TimeGenerated == ago(5m)
```

Weitere hilfreiche Beispielabfragen finden Sie auf der Registerkarte **Nächste Schritte** auf der Connectorseite.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument wurde beschrieben, wie Sie Imperva WAF Gateway mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](tutorial-monitor-your-data.md), um Ihre Daten zu überwachen.
