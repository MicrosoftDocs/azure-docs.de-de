---
title: Anzeigen und Analysieren von Daten in Azure Log Analytics | Microsoft-Dokumentation
description: Hilfestellung für Benutzer, die von der Log Analytics-Protokollsuche zur Azure Monitor-Protokollabfrage übergehen.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: bwren
ms.openlocfilehash: dc3a754bd1a9f689f4b24b9ee2c1e4cbc0611684
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60519899"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Übergang von der Log Analytics-Protokollsuche zu Azure Monitor-Protokollen
Die Protokollsuche in Log Analytics wurde vor Kurzem durch eine neue Vorgehensweise für das Analysieren von Azure Monitor-Protokollen ersetzt. Die Seite für die Protokollsuche ist derzeit noch über die Menüoption **Protokolle (klassisch)** auf der Seite **Log Analytics-Arbeitsbereiche** im Azure-Portal zugänglich, wird jedoch am 15. Februar 2019 entfernt. Dieser Artikel beschreibt die Unterschiede zwischen den beiden Vorgehensweisen, um Ihnen den Übergang von der Protokollsuche zu erleichtern. 

## <a name="filter-results-of-a-query"></a>Filtern der Ergebnisse einer Abfrage
In der Protokollsuche wird eine Liste der Filter angezeigt, während die Suchergebnisse übermittelt werden. Wählen Sie einen Filter aus, und klicken Sie auf **Übernehmen**, um die Abfrage mit dem ausgewählten Filter auszuführen.

![Filter in der Protokollsuche](media/log-search-transition/filter-log-search.png)

Wählen Sie in Azure Monitor-Protokollen *Filter (Vorschau)* aus, um Filter anzuzeigen. Klicken Sie auf das Filtersymbol, um weitere Filter anzuzeigen. Wählen Sie einen Filter aus, und klicken Sie auf **Apply & Run** (Übernehmen und ausführen), um die Abfrage mit dem ausgewählten Filter auszuführen.

![Filter in Protokollen](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Extrahieren von benutzerdefinierten Feldern 
In der Protokollsuche extrahieren Sie [benutzerdefinierte Felder](../platform/custom-fields.md) aus der Listenansicht, in der das Menü für Felder die Aktion _Extract fields from Table_ (Felder aus Tabelle extrahieren) enthält.

![Extrahieren von Feldern in der Protokollsuche](media/log-search-transition/extract-fields-log-search.png)

In Azure Monitor-Protokollen extrahieren Sie benutzerdefinierte Felder in der Tabellenansicht. Erweitern Sie einen Datensatz, indem Sie auf den Pfeil auf der linken Seite klicken, und klicken Sie dann auf die Auslassungspunkte, um Zugriff auf die Aktion _Felder extrahieren_ zu erhalten.

![Extrahieren von Feldern in Protokollen](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>Funktionen und Computergruppen
Wählen Sie zum Speichern einer Suche in der Protokollsuche **Gespeicherte Suchläufe** und **Hinzufügen** aus, um einen Namen, eine Kategorie und einen Abfragetext anzugeben. Erstellen Sie eine [Computergruppe](../platform/computer-groups.md), indem Sie einen Funktionsalias hinzufügen.

![Speichern der Protokollsuche](media/log-search-transition/save-search-log-search.png)

Wählen Sie zum Speichern der aktuellen Abfrage in Azure Monitor-Protokollen **Speichern** aus. Ändern Sie **Speichern unter** in _Funktion_, und geben Sie einen **Funktionsalias** an, um eine [Funktion](functions.md) zu erstellen. Wählen Sie _Diese Abfrage als Computergruppe speichern_ aus, um den Funktionsalias für eine [Computergruppe](../platform/computer-groups.md) zu verwenden.

![Speichern von Protokollabfragen](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Gespeicherte Abfragen
In der Protokollsuche stehen Ihre gespeicherten Abfragen über das Element **Gespeicherte Suchvorgänge** der Aktionsleiste zur Verfügung. In Azure Monitor-Protokollen können Sie über den [Abfrage-Explorer](../log-query/get-started-portal.md#save-queries) auf gespeicherte Abfragen zugreifen.

![Abfrage-Explorer](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>Drilldown für zusammengefasste Zeilen
In der Protokollsuche können Sie auf eine Zeile in einer zusammengefassten Abfrage klicken, um eine andere Abfrage zu starten, die detaillierte Datensätze in dieser Zeile auflistet.

![Protokollsuchedrilldown](media/log-search-transition/drilldown-search.png)

In Azure Monitor-Protokollen müssen Sie die Abfrage ändern, um diese Datensätze zurückzugeben. Erweitern Sie eine der Zeilen in den Ergebnissen, und klicken Sie auf das Zeichen **+** neben dem Wert, um sie der Abfrage hinzuzufügen. Kommentieren Sie dann den Befehl **summarize** aus, und führen Sie die Abfrage erneut aus.

![Azure Monitor-Protokolldrilldown](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>Ausführen einer Aktion
In der Protokollsuche können Sie aus einem Suchergebnis [ein Runbook starten](take-action.md), indem Sie **Aktion ausführen** auswählen.

![Ausführen einer Aktion](media/log-search-transition/take-action-log-search.png)

In Azure Monitor-Protokollen [erstellen Sie eine Warnung aus der Protokollabfrage](../platform/alerts-log.md). Konfigurieren Sie eine Aktionsgruppe mit Aktionen, die als Reaktion auf die Warnung ausgeführt werden.

![Aktionsgruppe](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die neue [Vorgehensweise für Azure Monitor-Protokolle](get-started-portal.md).