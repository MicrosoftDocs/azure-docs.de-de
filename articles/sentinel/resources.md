---
title: Nützliche Ressourcen für das Arbeiten mit Azure Sentinel | Microsoft-Dokumentation
description: Dieses Dokument enthält eine Liste von Ressourcen, die nützlich sind, wenn mit Azure Sentinel gearbeitet wird.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2021
ms.author: yelevin
ms.openlocfilehash: 2a6e31115fb2548f8248f741213970605f230036
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390724"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Nützliche Ressourcen für das Arbeiten mit Azure Sentinel



Dieser Artikel listet Ressourcen auf, aus denen Sie weitere Informationen zum Arbeiten mit Azure Sentinel abrufen können.

- **Azure Logic Apps-Connectors**: <https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Überwachung und Berichterstellung
Überwachungsprotokolle von Azure Sentinel werden in [Azure-Aktivitätsprotokollen](../azure-monitor/platform/platform-logs-overview.md) verwaltet.

Die folgenden unterstützten Vorgänge können überwacht werden.

|Vorgangsname|    Ressourcentyp|
|----|----|
|Arbeitsmappe erstellen oder aktualisieren  |Microsoft.Insights/workbooks|
|Arbeitsmappe löschen    |Microsoft.Insights/workbooks|
|Workflow festlegen   |Microsoft.Logic/workflows|
|Workflow löschen    |Microsoft.Logic/workflows|
|Gespeicherte Suche erstellen    |Microsoft.OperationalInsights/workspaces/savedSearches|
|Gespeicherte Suche löschen    |Microsoft.OperationalInsights/workspaces/savedSearches|
|Warnungsregeln aktualisieren |Microsoft.SecurityInsights/alertRules|
|Warnungsregeln löschen |Microsoft.SecurityInsights/alertRules|
|Antwortaktionen für Warnungsregeln aktualisieren |Microsoft.SecurityInsights/alertRules/actions|
|Antwortaktionen für Warnungsregeln löschen |Microsoft.SecurityInsights/alertRules/actions|
|Lesezeichen aktualisieren   |Microsoft.SecurityInsights/bookmarks|
|Lesezeichen löschen   |Microsoft.SecurityInsights/bookmarks|
|Fälle aktualisieren   |Microsoft.SecurityInsights/Cases|
|Untersuchung von Fällen aktualisieren  |Microsoft.SecurityInsights/Cases/investigations|
|Kommentare zu Fällen erstellen   |Microsoft.SecurityInsights/Cases/comments|
|Datenconnectors aktualisieren |Microsoft.SecurityInsights/dataConnectors|
|Datenconnectors löschen |Microsoft.SecurityInsights/dataConnectors|
|Einstellungen aktualisieren    |Microsoft.SecurityInsights/settings|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Überwachungs- und Berichtserstellungsdaten in Azure Sentinel anzeigen

Sie können diese Daten anzeigen, indem Sie sie aus dem Azure-Aktivitätsprotokoll in Azure Sentinel streamen, wo sie die Daten untersuchen und analysieren können.

1. Stellen Sie eine Verbindung mit der Datenquelle der [Azure-Aktivität](connect-azure-activity.md) her. Anschließend werden Überwachungsereignisse in eine neue Tabelle namens AzureActivity im Bildschirm **Protokolle** gestreamt.

1. Fragen Sie die Daten dann wie jede andere Tabelle mit KQL ab.

    Um beispielsweise herauszufinden, wer der letzte Benutzer war, der eine bestimmte Analyseregel bearbeitet hat, verwenden Sie die folgende Abfrage (ersetzen Sie `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` durch die Regel-ID der Regel, die Sie überprüfen möchten):

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```


## <a name="blogs-and-forums"></a>Blogs und Foren

Wir freuen uns, von unseren Benutzern zu hören!

- **Stellen Sie Ihre Fragen** im [TechCommunity-Bereich](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) für Azure Sentinel. 

- **Senden Sie Verbesserungsvorschläge** über unser [User Voice](https://feedback.azure.com/forums/920458-azure-sentinel)-Programm.

- **Lesen und kommentieren Sie** unsere Azure Sentinel-Blogbeiträge:

    - [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) 
    - [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/)


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Lassen Sie sich zertifizieren!](/learn/paths/security-ops-sentinel/)

> [!div class="nextstepaction"]
> [Informieren Sie sich über Anwendungsfälle unserer Kunden](https://customers.microsoft.com/en-us/search?sq=%22Azure%20Sentinel%20%22&ff=&p=0&so=story_publish_date%20desc)

