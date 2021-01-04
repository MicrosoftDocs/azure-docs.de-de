---
title: Datenquellen für den Log Analytics-Agent in Azure Monitor
description: Datenquellen definieren die Protokolldaten, die Azure Monitor aus Agents und anderen verbundenen Quellen sammelt.  Dieser Artikel beschreibt das Konzept, nach dem Azure Monitor Datenquellen verwendet, erläutert Details zur Konfiguration der Quellen und bietet eine Übersicht über die verschiedenen verfügbaren Datenquellen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: ad5e91a6dcdb61e09a64e61a27f12148ec28168e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000741"
---
# <a name="log-analytics-agent-data-sources-in-azure-monitor"></a>Datenquellen für den Log Analytics-Agent in Azure Monitor
Die Daten, die Azure Monitor mit dem [Log Analytics](log-analytics-agent.md)-Agent von virtuellen Computern sammelt, werden durch die im [Log Analytics-Arbeitsbereich](data-platform-logs.md) konfigurierten Datenquellen definiert.   Jede Datenquelle erstellt Datensätze eines bestimmten Typs, von denen jeder über einen eigenen Satz von Eigenschaften verfügt.

> [!IMPORTANT]
> In diesem Artikel werden Datenquellen für den [Log Analytics-Agent](log-analytics-agent.md) beschrieben, einen der von Azure Monitor verwendeten Agents. Andere Agents sammeln andere Daten und werden anders konfiguriert. Eine Liste der verfügbaren Agents und der von ihnen gesammelten Daten finden Sie unter [Übersicht über Azure Monitor-Agents](agents-overview.md).

![Protokolldatensammlung](media/agent-data-sources/overview.png)

> [!IMPORTANT]
> Die in diesem Artikel beschriebenen Datenquellen gelten nur für virtuelle Computer, auf denen der Log Analytics-Agent ausgeführt wird. 

## <a name="summary-of-data-sources"></a>Übersicht über Datenquellen
In der folgenden Tabelle werden die zurzeit für den Log Analytics-Agent verfügbaren Datenquellen aufgeführt.  In den Links zu den Datenquellen finden Sie weitere Informationen zur jeweiligen Datenquelle.   Außerdem finden Sie hier Informationen zur jeweiligen Methode und Häufigkeit der Sammlung. 


| Datenquelle | Plattform | Log Analytics-Agent | Operations Manager-Agent | Azure-Speicher | Operations Manager erforderlich? | Daten vom Operations Manager-Agent über Verwaltungsgruppe gesendet | Sammlungshäufigkeit |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Benutzerdefinierte Protokolle](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | Bei der Ankunft |
| [Benutzerdefinierte Protokolle](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | Bei der Ankunft |
| [IIS-Protokolle](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |richtet sich nach Einstellung „Protokolldateirollover“ |
| [Leistungsindikatoren](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |Gemäß Zeitplan, mindestens 10 Sekunden |
| [Leistungsindikatoren](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |Gemäß Zeitplan, mindestens 10 Sekunden |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |von Azure-Speicher: 10 Minuten; von Agent: bei Ankunft |
| [Windows-Ereignisprotokolle](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | Bei der Ankunft |


## <a name="configuring-data-sources"></a>Konfigurieren von Datenquellen
Wechseln Sie zum Konfigurieren von Datenquellen für den Log Analytics-Agent zum Azure-Portal, und wählen Sie im Menü **Log Analytics-Arbeitsbereiche** einen Arbeitsbereich aus. Klicken Sie auf **Erweiterte Einstellungen** und dann auf **Daten**. Wählen Sie die Datenquelle aus, die Sie konfigurieren möchten. Über die Links in der oben stehenden Tabelle gelangen Sie zur Dokumentation für jede Datenquelle und zu ausführlichen Informationen zur jeweiligen Konfiguration.

Jede Konfiguration wird an alle Agents übermittelt, die mit dem Arbeitsbereich verbunden sind.  Sie können keine verbundenen Agents aus dieser Konfiguration ausschließen.

![Windows-Ereignisse konfigurieren](media/agent-data-sources/configure-events.png)



## <a name="data-collection"></a>Datensammlung
Die Konfigurationen der Datenquellen werden innerhalb weniger Minuten an Agents übermittelt, die direkt mit Azure Monitor verbunden sind.  Die angegebenen Daten werden vom Agent gesammelt und in den für jede Datenquelle spezifischen Intervallen direkt an Azure Monitor übermittelt.  Informationen zu diesen Spezifikationen finden Sie in der Dokumentation zu jeder Datenquelle.

Bei System Center Operations Manager-Agents in einer verbundenen Verwaltungsgruppe werden Datenquellenkonfigurationen in Management Packs übersetzt und standardmäßig alle fünf Minuten an die Verwaltungsgruppe übermittelt.  Der Agent lädt das Management Pack wie jedes andere Paket herunter und sammelt die angegebenen Daten. Je nach Datenquelle werden die Daten entweder an einen Verwaltungsserver gesendet, der die Daten an Azure Monitor weiterleitet, oder der Agent sendet die Daten ohne den Umweg über den Verwaltungsserver direkt an Azure Monitor. Einzelheiten hierzu finden Sie unter [Ausführliche Informationen zu Datensammlungen für Überwachungslösungen in Azure](../monitor-reference.md).  Informationen zum Verbinden von Operations Manager und Azure Monitor und zum Ändern der Häufigkeit, mit der die Konfiguration übermittelt wird, finden Sie unter [Herstellen einer Verbindung zwischen Operations Manager und Log Analytics](om-agents.md).

Falls der Agent keine Verbindung mit Azure Monitor oder Operations Manager herstellen kann, sammelt er weiter Daten und übermittelt diese, sobald eine Verbindung hergestellt wird.  Daten können verloren, wenn die Datenmenge die maximale Cachegröße für den Client erreicht oder der Agent 24 Stunden lang keine Verbindung herstellen kann.

## <a name="log-records"></a>Protokolldatensätze
Alle von Azure Monitor gesammelten Protokolldaten werden im Arbeitsbereich als Datensätze gespeichert.  Datensätze, die aus verschiedenen Datenquellen gesammelt wurde, verfügen über einen eigenen Eigenschaftensatz und werden über die Eigenschaft **Typ** identifiziert.  Weitere Informationen zu den Datensatztypen finden Sie in der Dokumentation zur jeweiligen Datenquelle und Lösung.

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über [Überwachungslösungen](../insights/solutions.md), die Azure Monitor um zusätzliche Funktionen erweitern und ebenfalls Daten für den Azure Monitor-Arbeitsbereich sammeln.
* Erfahren Sie mehr über [Protokollabfragen](../log-query/log-query-overview.md) zum Analysieren der aus Datenquellen und Überwachungslösungen gesammelten Daten.  
* Konfigurieren Sie [Warnungen](alerts-overview.md), damit Sie bei kritischen Daten, die aus Datenquellen und Überwachungslösungen gesammelt werden, direkt benachrichtigt werden.
