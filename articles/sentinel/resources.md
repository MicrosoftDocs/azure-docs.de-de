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
ms.date: 12/02/2019
ms.author: yelevin
ms.openlocfilehash: 2465cf74dbf4fbf074aed8f8e4097a236e628425
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660643"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Nützliche Ressourcen für das Arbeiten mit Azure Sentinel



Dieser Artikel listet Ressourcen auf, aus denen Sie weitere Informationen zum Arbeiten mit Azure Sentinel abrufen können.

Azure Logic Apps-Connectoren: <https://docs.microsoft.com/connectors/>


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



## <a name="vendor-documentation"></a>Herstellerdokumentation

| **Hersteller**  | **Anwendungsvorfall in Azure Sentinel** | **Link**|
|----|----|----|
| GitHub| Verwendet für Zugriff auf die Community-Seite| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| CEF konfigurieren| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Kurs zur Kusto-Abfragesprache| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Blogs und Foren

Stellen Sie Ihre Fragen im [TechCommunity-Bereich](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) für Azure Sentinel bereit.

Lesen Sie Azure Sentinel-Blogbeiträge aus den Bereichen [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) und [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/).


## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie eine Liste der Ressourcen gefunden, die bei der Arbeit mit Azure Sentinel hilfreich sind. Weitere Informationen zur Azure-Sicherheit und -Compliance finden Sie im [Microsoft Azure Security and Compliance-Blog](/archive/blogs/azuresecurity/).