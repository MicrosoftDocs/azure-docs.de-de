---
title: Konfigurieren von Agent-Datenquellen in Azure Monitor | Microsoft-Dokumentation
description: Datenquellen definieren die Protokolldaten, die Azure Monitor aus Agents und anderen verbundenen Quellen sammelt.  Dieser Artikel beschreibt das Konzept, nach dem Azure Monitor Datenquellen verwendet, erläutert Details zur Konfiguration der Quellen und bietet eine Übersicht über die verschiedenen verfügbaren Datenquellen.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: d7d4aa89c4dcf2ac9cc0c393e0481cae1f3aeaf2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60776077"
---
# <a name="agent-data-sources-in-azure-monitor"></a>Agent-Datenquellen in Azure Monitor
Welche Daten Azure Monitor von Agents sammelt, wird durch die von Ihnen konfigurierten Datenquellen festgelegt.  Die Daten aus Agents werden als [Protokolldaten](data-platform-logs.md) mit einer Reihe von Datensätzen gespeichert.  Jede Datenquelle erstellt Datensätze eines bestimmten Typs, von denen jeder über einen eigenen Satz von Eigenschaften verfügt.

![Protokolldatensammlung](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>Übersicht über Datenquellen
In der folgenden Tabelle werden die zurzeit in Azure Monitor verfügbaren Agent-Datenquellen aufgeführt.  In den Links zu den Datenquellen finden Sie weitere Informationen zur jeweiligen Datenquelle.   Außerdem finden Sie hier Informationen zur jeweiligen Methode und Häufigkeit der Sammlung. 


| Datenquelle | Plattform | Microsoft Monitoring Agent | Operations Manager-Agent | Azure-Speicher | Operations Manager erforderlich? | Daten vom Operations Manager-Agent über Verwaltungsgruppe gesendet | Sammlungshäufigkeit |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Benutzerdefinierte Protokolle](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | Bei der Ankunft |
| [Benutzerdefinierte Protokolle](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | Bei der Ankunft |
| [IIS-Protokolle](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |richtet sich nach Einstellung „Protokolldateirollover“ |
| [Leistungsindikatoren](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |Gemäß Zeitplan, mindestens 10 Sekunden |
| [Leistungsindikatoren](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |Gemäß Zeitplan, mindestens 10 Sekunden |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |von Azure-Speicher: 10 Minuten; von Agent: bei Ankunft |
| [Windows-Ereignisprotokolle](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | Bei der Ankunft |


## <a name="configuring-data-sources"></a>Konfigurieren von Datenquellen
Sie konfigurieren Datenquellen für den Arbeitsbereich über das Menü **Daten** unter **Erweiterte Einstellungen**.  Jede Konfiguration wird an alle verbundenen Quellen in Ihrem Arbeitsbereich übermittelt.  Sie können zurzeit keine Agents aus dieser Konfiguration ausschließen.

![Windows-Ereignisse konfigurieren](media/agent-data-sources/configure-events.png)

1. Wählen Sie im Azure-Portal die Optionen **Log Analytics-Arbeitsbereiche** > Ihr Arbeitsbereich > **Erweiterte Einstellungen** aus.
2. Wählen Sie **Daten**aus.
3. Klicken Sie auf die Datenquelle, die Sie konfigurieren möchten.
4. Folgen Sie den Links in der oben stehenden Tabelle, um zur Dokumentation für jede Datenquelle zu gelangen und detaillierte Informationen zur jeweiligen Konfiguration zu erhalten.


## <a name="data-collection"></a>Datensammlung
Die Konfigurationen der Datenquellen werden innerhalb weniger Minuten an Agents übermittelt, die direkt mit Azure Monitor verbunden sind.  Die angegebenen Daten werden vom Agent gesammelt und in den für jede Datenquelle spezifischen Intervallen direkt an Azure Monitor übermittelt.  Informationen zu diesen Spezifikationen finden Sie in der Dokumentation zu jeder Datenquelle.

Bei System Center Operations Manager-Agents in einer verbundenen Verwaltungsgruppe werden Datenquellenkonfigurationen in Management Packs übersetzt und standardmäßig alle fünf Minuten an die Verwaltungsgruppe übermittelt.  Der Agent lädt das Management Pack wie jedes andere Paket herunter und sammelt die angegebenen Daten. Je nach Datenquelle werden die Daten entweder an einen Verwaltungsserver gesendet, der die Daten an Azure Monitor weiterleitet, oder der Agent sendet die Daten ohne den Umweg über den Verwaltungsserver direkt an Azure Monitor. Einzelheiten hierzu finden Sie unter [Ausführliche Informationen zu Datensammlungen für Überwachungslösungen in Azure](../insights/solutions-inventory.md).  Informationen zum Verbinden von Operations Manager und Azure Monitor und zum Ändern der Häufigkeit, mit der die Konfiguration übermittelt wird, finden Sie unter [Herstellen einer Verbindung zwischen Operations Manager und Log Analytics](om-agents.md).

Falls der Agent keine Verbindung mit Azure Monitor oder Operations Manager herstellen kann, sammelt er weiter Daten und übermittelt diese, sobald eine Verbindung hergestellt wird.  Daten können verloren, wenn die Datenmenge die maximale Cachegröße für den Client erreicht oder der Agent 24 Stunden lang keine Verbindung herstellen kann.

## <a name="log-records"></a>Protokolldatensätze
Alle von Azure Monitor gesammelten Protokolldaten werden im Arbeitsbereich als Datensätze gespeichert.  Datensätze, die aus verschiedenen Datenquellen gesammelt wurde, verfügen über einen eigenen Eigenschaftensatz und werden über die Eigenschaft **Typ** identifiziert.  Weitere Informationen zu den Datensatztypen finden Sie in der Dokumentation zur jeweiligen Datenquelle und Lösung.

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über [Überwachungslösungen](../insights/solutions.md), die Azure Monitor um zusätzliche Funktionen erweitern und ebenfalls Daten für den Azure Monitor-Arbeitsbereich sammeln.
* Erfahren Sie mehr über [Protokollabfragen](../log-query/log-query-overview.md) zum Analysieren der aus Datenquellen und Überwachungslösungen gesammelten Daten.  
* Konfigurieren Sie [Warnungen](alerts-overview.md), damit Sie bei kritischen Daten, die aus Datenquellen und Überwachungslösungen gesammelt werden, direkt benachrichtigt werden.