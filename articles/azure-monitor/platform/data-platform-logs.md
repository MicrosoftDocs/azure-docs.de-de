---
title: Azure Monitor-Protokolle
description: Dieser Artikel beschreibt Azure Monitor-Protokolle, die für eine erweiterte Analyse von Überwachungsdaten verwendet werden.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/22/2020
ms.author: bwren
ms.openlocfilehash: 69fda41249ced8518e430af8305c0cb10822b214
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96559064"
---
# <a name="azure-monitor-logs-overview"></a>Übersicht über Azure Monitor-Protokolle
Azure Monitor-Protokolle sind ein Feature von Azure Monitor, mit dem Protokoll- und Leistungsdaten von [überwachten Ressourcen](../monitor-reference.md) gesammelt und organisiert werden. Daten aus unterschiedlichen Quellen, z. B. [Plattformprotokolle](platform-logs-overview.md) von Azure-Diensten, Protokoll- und Leistungsdaten von [Agenten für virtuelle Computer](agents-overview.md) sowie Nutzungs- und Leistungsdaten von [Anwendungen](../app/app-insights-overview.md), können in einem einzigen Arbeitsbereich konsolidiert werden, sodass sie mithilfe einer ausgereiften Abfragesprache, die eine schnelle Analyse von Millionen von Datensätzen ermöglicht, gemeinsam analysiert werden können. Sie können eine einfache Abfrage ausführen, bei der nur eine bestimmte Gruppe von Datensätzen abgerufen wird, oder eine anspruchsvolle Datenanalyse durchführen, um kritische Muster in den Überwachungsdaten zu erkennen. Arbeiten Sie mithilfe von Log Analytics interaktiv mit Protokollabfragen und deren Ergebnissen, verwenden Sie diese in einer Warnungsregel, um proaktiv über Probleme informiert zu werden, oder visualisieren Sie die Ergebnisse in einer Arbeitsmappe oder einem Dashboard.

> [!NOTE]
> Azure Monitor-Protokolle machen die eine Hälfte der Datenplattform aus, die Azure Monitor unterstützt. Die andere sind [Azure Monitor-Metriken](data-platform-metrics.md), die numerische Daten in einer Zeitreihendatenbank speichern. Dadurch sind diese Daten kompakter als Daten in Azure Monitor-Protokollen und können Szenarien nahezu in Echtzeit unterstützen. Dies macht sie besonders nützlich für Warnungen und eine schnelle Erkennung von Problemen. In Metriken können jedoch nur numerische Daten in einer bestimmten Struktur gespeichert werden, während dies in Protokollen für eine Vielzahl verschiedener Datentypen mit jeweils eigener Struktur möglich ist. Sie können auch komplexe Analysen von Protokolldaten mithilfe von Protokollabfragen durchführen, die für die Analyse von Metrikdaten nicht verwendet werden können.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Welche Möglichkeiten bieten Azure Monitor-Protokolle?
In der folgenden Tabelle sind einige der unterschiedlichen Optionen zur Nutzung von Protokollen in Azure Monitor beschrieben:

|  |  |
|:---|:---|
| **Analysieren** | Verwenden von [Log Analytics](../log-query/log-analytics-tutorial.md) im Azure-Portal zum Schreiben von [Protokollabfragen](../log-query/log-query-overview.md) und interaktives Analysieren von Protokolldaten mithilfe einer leistungsstarken Analyse-Engine. |
| **Warnung** | Konfigurieren einer [Protokollwarnungsregel](alerts-log.md), die eine Benachrichtigung sendet oder eine [automatisierte Aktion](action-groups.md) ausführt, wenn die Ergebnisse der Abfrage mit einem bestimmten Ergebnis übereinstimmen. |
| **Visualisieren** | Anheften der als Tabellen oder Diagramme gerenderten Abfrageergebnisse an ein [Azure-Dashboard](../../azure-portal/azure-portal-dashboards.md).<br>Erstellen einer [Arbeitsmappe](./workbooks-overview.md), um mehrere Sätze von Daten in einem interaktiven Bericht zu kombinieren. <br>Exportieren der Ergebnisse einer Abfrage nach [Power BI](powerbi.md), um verschiedene Visualisierungen zu verwenden und sie mit Benutzern außerhalb von Azure zu teilen.<br>Exportieren der Ergebnisse einer Abfrage nach [Grafana](grafana-plugin.md), um dessen Dashboards zu nutzen und sie mit anderen Quellen zu kombinieren.|
| **Erkenntnisse** | Unterstützen von [Erkenntnissen](../monitor-reference.md#insights-and-core-solutions), die eine angepasste Überwachungsoberfläche für bestimmte Anwendungen und Dienste bereitstellen.  |
| **Abrufen** | Zugreifen auf Ergebnisse von Protokollabfragen über eine Befehlszeile mit [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Zugreifen auf Ergebnisse von Protokollabfragen über eine Befehlszeile mit [PowerShell-Cmdlets](/powershell/module/az.operationalinsights).<br>Zugreifen auf Ergebnisse von Protokollabfragen über eine benutzerdefinierte Anwendung mit der [REST-API](https://dev.loganalytics.io/). |
| **Export** | Konfigurieren des [automatisierten Exports von Protokolldaten](logs-data-export.md) in das Azure-Speicherkonto oder in Azure Event Hubs.<br>Erstellen eines Workflows zum Abrufen von Protokolldaten und kopieren der Daten an einen externen Speicherort mithilfe von [Logic Apps](logicapp-flow-connector.md). |

![Übersicht zu Protokollen](media/data-platform-logs/logs-overview.png)


## <a name="data-collection"></a>Datensammlung
Nachdem Sie einen Log Analytics-Arbeitsbereich erstellt haben, müssen Sie verschiedene Quellen zum Senden von Daten konfigurieren. Es werden keine Daten automatisch erfasst. Diese Konfiguration ist je nach Datenquelle unterschiedlich. Erstellen Sie beispielsweise [Diagnoseeinstellungen](diagnostic-settings.md), um Ressourcenprotokolle von Azure-Ressourcen an den Arbeitsbereich zu senden. [Aktivieren Sie Azure Monitor für VMs](../insights/vminsights-enable-overview.md), um Daten von virtuellen Computern zu sammeln. Konfigurieren Sie [Datenquellen im Arbeitsbereich](data-sources.md), um zusätzliche Ereignisse und Leistungsdaten zu sammeln.

- Unter [Was wird von Azure Monitor überwacht?](../monitor-reference.md) finden Sie eine vollständige Liste der Datenquellen, die Sie für das Senden von Daten an Log Analytics-Protokolle konfigurieren können.


## <a name="log-analytics-workspaces"></a>Log Analytics-Arbeitsbereiche
Die von Azure Monitor-Protokollen erfassten Daten werden in einem oder mehreren [Log Analytics-Arbeitsbereichen](./design-logs-deployment.md) gespeichert. Der Arbeitsbereich definiert den geografischen Standort der Daten, Zugriffsrechte zur Festlegung, welche Benutzer auf Daten zugreifen können, und Konfigurationseinstellungen wie Tarif und Datenaufbewahrung.  

Sie müssen mindestens einen Arbeitsbereich erstellen, um Azure Monitor-Protokolle zu verwenden. Ein einziger Arbeitsbereich kann für alle Überwachungsdaten ausreichend sein, oder Sie können je nach Anforderungen mehrere Arbeitsbereiche erstellen. Sie können beispielsweise über einen Arbeitsbereich für die Produktionsdaten und einen anderen für Testzwecke verfügen. 

- Informationen zum Erstellen eines neuen Arbeitsbereichs finden Sie unter [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../learn/quick-create-workspace.md).
- Überlegungen zum Erstellen mehrerer Arbeitsbereiche finden Sie unter [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](design-logs-deployment.md).

## <a name="data-structure"></a>Datenstruktur
Protokollabfragen rufen Daten aus einem Log Analytics-Arbeitsbereich ab. Jeder Arbeitsbereich enthält mehrere Tabellen, die in separaten Spalten mit mehreren Datenzeilen angeordnet sind. Jede Tabelle ist durch einen eindeutigen Satz von Spalten definiert, die von den von der Datenquelle bereitgestellten Datenzeilen gemeinsam genutzt werden. 

[![Struktur von Azure Monitor-Protokollen](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Protokolldaten aus Application Insights werden ebenfalls in Azure Monitor-Protokollen gespeichert, doch erfolgt das Speichern je nach Konfiguration Ihrer Anwendung auf unterschiedliche Weise. Bei einer arbeitsbereichsbasierten Anwendung werden Daten in einem Log Analytics-Arbeitsbereich in einem Standardsatz von Tabellen gespeichert, um Daten wie Anwendungsanforderungen, Ausnahmen und Seitenaufrufe aufzubewahren. Mehrere Anwendungen können denselben Arbeitsbereich verwenden. Bei einer klassischen Anwendung werden die Daten nicht in einem Log Analytics-Arbeitsbereich gespeichert. Es wird die gleiche Abfragesprache verwendet, und Sie können Abfragen mit dem gleichen Log Analytics-Tool im Azure-Portal erstellen und ausführen. Daten für klassische Anwendungen werden jedoch getrennt voneinander gespeichert. Die allgemeine Struktur entspricht arbeitsbereichsbasierten Anwendungen, doch sind die Tabellen- und Spaltennamen unterschiedlich. Einen ausführlichen Vergleich des Schemas für arbeitsbereichsbasierte und klassische Anwendungen finden Sie unter [Änderungen bei arbeitsbereichsbasierten Ressourcen](../app/apm-tables.md).


> [!NOTE]
> Wir stellen auf der Application Insights-Benutzeroberfläche weiterhin vollständige Abwärtskompatibilität für Ihre klassischen Ressourcenabfragen, Arbeitsmappen und protokollbasierten Warnungen von Application Insights bereit. Um die [neue arbeitsbereichsbasierte Tabellenstruktur und das neue Tabellenschema](../app/apm-tables.md) anzuzeigen und abzufragen, müssen Sie zuerst zu Ihrem Log Analytics-Arbeitsbereich navigieren. Während der Vorschau erhalten Sie durch Auswahl der Option **Protokolle** in den Application Insights-Bereichen Zugriff auf die klassische Application Insights-Benutzeroberfläche für Abfragen. Weitere Informationen finden Sie unter [Abfragebereich](../log-query/scope.md).


[![Struktur von Azure Monitor-Protokollen für Application Insights](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)


## <a name="log-queries"></a>Protokollabfragen
Daten werden mithilfe einer Protokollabfrage aus einem Log Analytics-Arbeitsbereich abgerufen. Hierbei handelt es sich um eine schreibgeschützte Anforderung zum Verarbeiten von Daten und Zurückgeben von Ergebnissen. Protokollabfragen werden in der [Kusto-Abfragesprache (KQL)](/azure/data-explorer/kusto/query/) geschrieben – derselben Abfragesprache, die von Azure Data Explorer verwendet wird. Sie können Protokollabfragen in Log Analytics schreiben, um die Ergebnisse interaktiv zu analysieren, sie in Warnungsregeln verwenden, um proaktiv über Probleme informiert zu werden, oder die Ergebnisse in Arbeitsmappen oder Dashboards einschließen. Erkenntnisse umfassen vordefinierte Abfragen zur Unterstützung der Sichten und Arbeitsmappen.

- Eine Liste der Einsatzorte von Protokollabfragen sowie Verweise auf Tutorials und andere Dokumentationen für den Einstieg finden Sie unter [Protokollabfragen in Azure Monitor](log-query/../../log-query/log-query-overview.md).

![Log Analytics](media/data-platform-logs/log-analytics.png)

## <a name="log-analytics"></a>Log Analytics
Verwenden Sie Log Analytics. Dies ist ein Tool im Azure-Portal, das zum Bearbeiten und Ausführen von Protokollabfragen und interaktiven Analysieren von deren Ergebnissen verwendet wird. Anschließend können Sie die von Ihnen erstellten Abfragen zur Unterstützung anderer Funktionen in Azure Monitor wie Protokollabfragewarnungen und Arbeitsmappen verwenden. Greifen Sie über die Option **Protokolle** im Azure Monitor-Menü oder über die meisten anderen Dienste im Azure-Portal auf Log Analytics zu.

- Eine Beschreibung von Log Analytics finden Sie unter [Übersicht über Log Analytics in Azure Monitor](../log-query/log-analytics-overview.md). 
- Verwenden Sie das [Log Analytics-Tutorial](../log-query/log-analytics-tutorial.md), um die Verwendung von Log Analytics-Features zum Erstellen einer einfachen Protokollabfrage und Analysieren der Ergebnisse schrittweise kennenzulernen.



## <a name="relationship-to-azure-data-explorer"></a>Beziehung zu Azure Data Explorer
Azure Monitor-Protokolle basieren auf Azure Data Explorer. Ein Log Analytics-Arbeitsbereich entspricht ungefähr einer Datenbank in Azure Data Explorer, Tabellen sind gleich strukturiert und beide verwenden dieselbe Kusto-Abfragesprache (KQL). Die Verwendung von Log Analytics zum Arbeiten mit Azure Monitor-Abfragen im Azure-Portal ähnelt der Verwendung der Azure Data Explorer-Webbenutzeroberfläche. Sie können sogar [Daten aus einem Log Analytics-Arbeitsbereich in eine Azure Data Explorer-Abfrage einschließen](/azure/data-explorer/query-monitor-data). 


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Protokollabfragen](../log-query/log-query-overview.md) zum Abrufen und Analysieren von Daten aus einem Log Analytics-Arbeitsbereich.
- Informieren Sie sich über [Metriken in Azure Monitor](data-platform-metrics.md).
- Erfahren Sie mehr über die für verschiedene Ressourcen in Azure [verfügbaren Überwachungsdaten](data-sources.md).
