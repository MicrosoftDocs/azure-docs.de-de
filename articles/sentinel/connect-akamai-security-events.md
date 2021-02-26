---
title: Verbinden des Connectors für sicherheitsrelevante Akamai-Ereignisse mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Connector für sicherheitsrelevante Akamai-Ereignisse verwenden, um Sicherheitsprotokolle der Akamai-Lösungen in Azure Sentinel zu pullen. Zeigen Sie Akamai-Daten in Arbeitsmappen an, erstellen Sie Warnungen, und verbessern Sie die Untersuchung.
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
ms.openlocfilehash: c833d87b8d85c75c4f050f0130ddfd74342f4c52
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566610"
---
# <a name="connect-your-akamai-security-events-collector-to-azure-sentinel"></a>Verbinden des Connectors für sicherheitsrelevante Akamai-Ereignisse mit Azure Sentinel

> [!IMPORTANT]
> Der Connector für sicherheitsrelevante Akamai-Ereignisse befindet sich derzeit in der **Vorschauversion**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

In diesem Artikel wird erläutert, wie Sie den Connector für sicherheitsrelevante Akamai-Ereignisse mit Azure Sentinel verbinden. Mit dem Connector für sicherheitsrelevante Akamai-Ereignisse lassen sich Ihre Akamai-Protokolle problemlos mit Azure Sentinel verbinden, sodass Sie die Daten dann in Arbeitsmappen anzeigen, zum Erstellen benutzerdefinierter Warnungen abfragen und zur Verbesserung der Untersuchung von Problemen verwenden können. Die Integration des Connectors für sicherheitsrelevante Akamai-Ereignisse und Azure Sentinel basiert auf CEF-formatierten Syslog-Nachrichten, einem Linux-basierten Server für die Protokollweiterleitung und dem Log Analytics-Agent. Außerdem wird ein benutzerdefinierter Protokollparser verwendet, der auf einer Kusto-Funktion basiert.

> [!NOTE]
> Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Lese- und Schreibberechtigungen für Ihren Azure Sentinel-Arbeitsbereich.

- Sie müssen über Leseberechtigungen für freigegebene Schlüssel für den Arbeitsbereich verfügen. [Weitere Informationen zu Arbeitsbereichsschlüsseln](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)

## <a name="send-akamai-security-events-logs-to-azure-sentinel"></a>Senden von Protokollen der sicherheitsrelevanten Akamai-Ereignisse an Azure Sentinel

Um die Protokolle Ihres Connectors für sicherheitsrelevante Akamai-Ereignisse in Azure Sentinel zu erfassen, müssen Sie den Connector so konfigurieren, dass er Syslog-Nachrichten im CEF-Format an einen Linux-basierten Protokollweiterleitungsserver (auf dem rsyslog oder syslog-ng ausgeführt wird) sendet. Auf diesem Server ist der Log Analytics-Agent installiert, der die Protokolle an Ihren Azure Sentinel-Arbeitsbereich weiterleitet.

1. Wählen Sie im Azure Sentinel-Navigationsmenü **Datenconnectors** aus.

1. Wählen Sie im Katalog **Datenconnectors** den Eintrag **Sicherheitsrelevante Akamai-Ereignisse (Vorschau)** und dann **Connectorseite öffnen** aus.

1. Befolgen Sie die Anleitungen auf der Registerkarte **Anweisungen** unter **Konfiguration**:

    1. Unter **1. Konfiguration des Linux-Syslog-Agents**: Führen Sie diesen Schritt aus, wenn Sie noch keinen Protokollweiterleitungsserver ausführen oder einen anderen benötigen. Informationen zur Größe sowie detaillierte Anleitungen und ausführliche Beschreibungen finden Sie unter [SCHRITT 1: Bereitstellen des Protokollweiterleitungsservers](connect-cef-agent.md) in der Azure Sentinel-Dokumentation.

    1. Unter **2. CEF-Protokolle (Common Event Format) an den Syslog-Agent weiterleiten**: Befolgen Sie die Anweisungen von Akamai zum [Konfigurieren der SIEM-Integration](https://developer.akamai.com/tools/integrations/siem) und [Einrichten eines CEF-Connectors](https://developer.akamai.com/tools/integrations/siem/siem-cef-connector). Dieser Connector empfängt mithilfe der SIEM-OPEN-API Sicherheitsereignisse von den Akamai-Lösungen nahezu in Echtzeit und konvertiert sie vom JSON- in das CEF-Format.
    
        Diese Konfiguration sollte die folgenden Elemente enthalten:
    
        - Protokollziel: Hostname und/oder IP-Adresse Ihres Protokollweiterleitungsservers
        - Protokoll und Port: TCP 514 (Falls etwas anderes empfohlen wird, sollten Sie sicherstellen, dass Sie die entsprechende Änderung parallel auch im syslog-Daemon und auf Ihrem Protokollweiterleitungsserver vornehmen.)
        - Protokollformat: CEF
        - Protokolltypen: Alle verfügbaren.

    1. Unter **3. Verbindung überprüfen**: Überprüfen Sie die Datenerfassung, indem Sie den Befehl von der Connectorseite kopieren und auf Ihrem Protokollweiterleitungsserver ausführen. Detaillierte Anleitungen und Erläuterungen finden Sie unter [SCHRITT 3: Überprüfen der Verbindung](connect-cef-verify.md) in der Azure Sentinel-Dokumentation.

        Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden.

## <a name="find-your-data"></a>Suchen von Daten

Nachdem eine Verbindung hergestellt wurde, werden die Daten in der Tabelle *CommonSecurityLog* im Abschnitt **Azure Sentinel** in **Logs** angezeigt.

Dieser Datenconnector ist von einem Parser abhängig, der auf einer Kusto-Funktion basiert und erwartungsgemäß funktionieren muss. Führen Sie die folgenden Schritte aus, um die Kusto-Funktion **AkamaiSIEMEvent** einzurichten, die in Abfragen und Arbeitsmappen verwendet wird.

1. Wählen Sie im Azure Sentinel-Navigationsmenü die Option **Protokolle** aus.

1. Kopieren Sie die folgende Abfrage, und fügen Sie sie in das Abfragefenster ein.
    ```kusto
    CommonSecurityLog 
    | where DeviceVendor == 'Akamai'
    | where DeviceProduct == 'akamai_siem'
    | extend EventVendor = 'Akamai'
    | extend EventProduct = 'akamai_siem'
    | extend EventProductVersion = '1.0'
    | extend EventId = DeviceEventClassID
    | extend EventCategory = Activity
    | extend EventSeverity = LogSeverity
    | extend DvcAction = DeviceAction
    | extend NetworkApplicationProtocol = ApplicationProtocol
    | extend Ipv6Src = DeviceCustomIPv6Address2
    | extend RuleName = DeviceCustomString1
    | extend RuleMessages = DeviceCustomString2
    | extend RuleData = DeviceCustomString3
    | extend RuleSelectors = DeviceCustomString4
    | extend ClientReputation = DeviceCustomString5
    | extend ApiId = DeviceCustomString6
    | extend RequestId = DevicePayloadId
    | extend DstDvcHostname = DestinationHostName
    | extend DstPortNumber = DestinationPort
    | extend ConfigId = FlexString1
    | extend PolicyId = FlexString2
    | extend NetworkBytes = SentBytes
    | extend UrlOriginal = RequestURL
    | extend HttpRequestMethod = RequestMethod
    | extend SrcIpAddr = SourceIP
    | extend EventStartTime = datetime(1970-01-01) + tolong(extract(@'.*start=(.*?);', 1, AdditionalExtensions)) * 1s 
    | extend SlowPostAction = extract(@'.*AkamaiSiemSlowPostAction=(.*?);', 1, AdditionalExtensions)
    | extend SlowPostRate = extract(@'.*AkamaiSiemSlowPostRate=(.*?);', 1, AdditionalExtensions)
    | extend RuleVersions = extract(@'.*AkamaiSiemRuleVersions=,?(.*?);', 1, AdditionalExtensions)
    | extend RuleTags = extract(@'.*AkamaiSiemRuleTags=(.*?);', 1, AdditionalExtensions)
    | extend ApiKey = extract(@'.*AkamaiSiemApiKey=(.*?);', 1, AdditionalExtensions)
    | extend Tls = extract(@'.*AkamaiSiemTLSVersion=(.*?);', 1, AdditionalExtensions)
    | extend RequestHeaders = extract(@'.*AkamaiSiemRequestHeaders=;?(.*?);', 1, AdditionalExtensions)
    | extend ResponseHeaders = extract(@'.*AkamaiSiemResponseHeaders=(.*?);', 1, AdditionalExtensions)
    | extend HttpStatusCode = extract(@'.*AkamaiSiemResponseStatus=(.*?);', 1, AdditionalExtensions)
    | extend GeoContinent = extract(@'.*AkamaiSiemContinent=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCountry = extract(@'.*AkamaiSiemCountry=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCity = extract(@'.*AkamaiSiemCity=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoRegion = extract(@'.*AkamaiSiemRegion=(.*?);', 1, AdditionalExtensions)
    | extend GeoAsn = extract(@'.*AkamaiSiemASN=(\d+)', 1, AdditionalExtensions)
    | extend Custom = extract(@'.*AkamaiSiemCusomData=(.*?)', 1, AdditionalExtensions)
    | project TimeGenerated
            , EventVendor
            , EventProduct
            , EventProductVersion
            , EventStartTime
            , EventId
            , EventCategory
            , EventSeverity
            , DvcAction
            , NetworkApplicationProtocol
            , Ipv6Src
            , RuleName
            , RuleMessages
            , RuleData
            , RuleSelectors
            , ClientReputation
            , ApiId
            , RequestId
            , DstDvcHostname
            , DstPortNumber
            , ConfigId
            , PolicyId
            , NetworkBytes
            , UrlOriginal
            , HttpRequestMethod
            , SrcIpAddr
            , SlowPostAction
            , SlowPostRate
            , RuleVersions
            , RuleTags
            , ApiKey
            , Tls
            , RequestHeaders
            , ResponseHeaders
            , HttpStatusCode
            , GeoContinent
            , SrcGeoCountry
            , SrcGeoCity
            , SrcGeoRegion
            , GeoAsn
            , Custom
    ```

1. Klicken Sie auf die Dropdownliste **Speichern** und dann auf **Speichern**. Führen Sie im Bereich **Speichern** folgende Aktionen aus:

    1. Geben Sie unter **Name** die Bezeichnung **AkamaiSIEMEvent** ein.

    1. Wählen Sie unter **Speichern unter** die Option **Funktion** aus.

    1. Geben Sie unter **Funktionsalias** die Zeichenfolge **AkamaiSIEMEvent** ein.

    1. Geben Sie unter **Kategorie** die Bezeichnung **Funktionen** ein.

    1. Klicken Sie auf **Speichern**.

    Die Aktivierung von Funktions-Apps dauert in der Regel zwischen 10 und 15 Minuten.

Nun können Sie Akamai-Daten abfragen, indem Sie `AkamaiSIEMEvent` in der obersten Zeile des Abfragefensters eingeben.

Weitere hilfreiche Beispielabfragen finden Sie auf der Registerkarte **Nächste Schritte** auf der Connectorseite.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie sicherheitsrelevante Akamai-Ereignisse mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](tutorial-monitor-your-data.md), um Ihre Daten zu überwachen.
