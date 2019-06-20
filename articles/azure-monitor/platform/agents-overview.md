---
title: Übersicht über die Azure-Überwachungs-Agents | Microsoft-Dokumentation
description: Dieser Artikel bietet eine ausführliche Übersicht über die verfügbaren Azure-Agents, die die Überwachung virtueller Computer unterstützen, die in Azure oder einer Hybridumgebung gehostet werden.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: magoedte
ms.openlocfilehash: 12eea032c37c8d737ae004d622b72536195c4444
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65977585"
---
# <a name="overview-of-the-azure-monitoring-agents"></a>Übersicht über die Azure-Überwachungs-Agents 
Microsoft Azure bietet mehrere Möglichkeiten zum Erfassen unterschiedlicher Datentypen von virtuellen Computern unter Microsoft Windows und Linux, die in Azure, in Ihrem Rechenzentrum oder von anderen Cloudanbietern gehostet werden. Folgende drei Agent-Typen stehen zum Überwachen eines virtuellen Computers zur Verfügung:

* Azure-Diagnoseerweiterungen
* Log Analytics-Agent für Linux und Windows
* Abhängigkeits-Agent

In diesem Artikel werden die Unterschiede und die jeweiligen Funktionen beschrieben, damit Sie ermitteln können, welcher Agent Ihre IT-Dienstverwaltung oder Ihre allgemeinen Überwachungsanforderungen unterstützt.  

## <a name="azure-diagnostic-extension"></a>Azure-Diagnoseerweiterung
Die [Azure-Diagnoseerweiterung](../../azure-monitor/platform/diagnostics-extension-overview.md) (allgemein als Microsoft Azure-Diagnose- bzw. Linux Azure-Diagnoseerweiterung bezeichnet) wurde seit ihrer allgemeinen Verfügbarkeit 2010 zunächst für Azure Cloud Services bereitgestellt und ist ein Agent, der die einfache Sammlung von Diagnosedaten von einer Azure-Computeressource, z.B. einem virtuellen Computer, und die Speicherung der Daten in Azure Storage ermöglicht. Die Daten im Speicher können Sie mit einem von mehreren verfügbaren Tools anzeigen, z.B. mit [Server-Explorer in Visual Studio](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) oder [Azure Storage-Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).

Sie können auswählen, dass Folgendes gesammelt wird:

* Eine vordefinierte Gruppe von Betriebssystem-Leistungsindikatoren und Ereignisprotokollen (oder Sie können die einzelnen zu sammelnden Daten angeben). 
* Alle Anforderungen und/oder fehlerhaften Anforderungen an einen IIS-Webserver
* Ausgabeprotokolle zur Ablaufverfolgung von .NET-Apps
* Ereignisablaufverfolgung für Windows-Ereignisse (ETW) 
* Sammeln von Protokollereignissen aus Syslog  
* Absturzabbilder 

Der Azure-Diagnose-Agent sollte zu folgenden Zwecken verwendet werden:

* Archivieren von Protokollen und Metriken im Azure-Speicher.
* Integrieren von Überwachungsdaten in Drittanbietertools. Diese Tools verwenden eine Vielzahl von Methoden, darunter die Abfrage des Speicherkontos, die Weiterleitung an [Event Hubs](../../event-hubs/event-hubs-about.md) oder die Abfrage mit der [Azure-Überwachungs-REST-API](../../azure-monitor/platform/rest-api-walkthrough.md).
* Hochladen von Daten in Azure Monitor zum Erstellen von Metrikdiagrammen im Azure-Portal oder Erstellen von [Metrikwarnungen](../../azure-monitor/platform/alerts-metric-overview.md) nahezu in Echtzeit. 
* Autoskalierung von VM-Skalierungsgruppen und klassischen Cloud Services basierend auf Metriken für Gastbetriebssysteme.
* Untersuchen von VM-Startfehlern mit der [Startdiagnose](../../virtual-machines/troubleshooting/boot-diagnostics.md).
* Nachvollziehen der Leistung Ihrer Anwendungen und proaktives Erkennen von Problemen, die sich auf die Anwendungen auswirken, mithilfe von [Application Insights](../../azure-monitor/overview.md).
* Konfigurieren von Azure Monitor für den Import von Metriken und Protokolldaten, die von Cloud Services, klassischen VMs und Service Fabric-Knoten erfasst werden, die in einem Azure-Speicherkonto gespeichert sind.

## <a name="log-analytics-agent"></a>Log Analytics-Agent
Für die erweiterte Überwachung, bei der nicht nur Metriken und einige Protokolle gesammelt werden sollen, ist der Log Analytics-Agent für Windows (auch als Microsoft Monitoring Agent, MMA, bezeichnet) und Linux erforderlich. Der Log Analytics-Agent wurde für eine umfassende Verwaltung von lokalen physischen und virtuellen Computern, mit System Center Operations Manager überwachten Computern und in anderen Clouds gehosteten virtuellen Computern entwickelt. Die Windows- und Linux-Agents stellen eine Verbindung mit einem Log Analytics-Arbeitsbereich in Azure Monitor her, um sowohl auf einer Überwachungslösung basierende Daten als auch von Ihnen konfigurierte benutzerdefinierte Datenquellen zu erfassen.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Der Log Analytics-Agent sollte zu folgenden Zwecken verwendet werden:

* Erfassen von Daten aus einer Vielzahl von Quellen, sowohl innerhalb von Azure als auch von anderen Cloudanbietern und aus lokalen Ressourcen. 
* Verwenden einer der Azure Monitor-Überwachungslösungen wie z.B. [Azure Monitor für VMs](../insights/vminsights-overview.md), [Azure Monitor für Container](../insights/container-insights-overview.md) usw.  
* Verwenden eines der anderen Azure-Verwaltungsdienste wie z.B. [Azure Security Center](../../security-center/security-center-intro.md), [Azure Automation](../../automation/automation-intro.md) usw.

Zuvor wurden mehrere Azure-Dienste in der *Operations Management Suite* zusammengefasst, daher wird der Log Analytics-Agent auch in Diensten wie Azure Security Center und Azure Automation verwendet.  Dazu gehört die vollständige von diesen angebotene Featurereihe, die eine umfassende Verwaltung Ihrer Azure-VMs während ihres gesamten Lebenszyklus bereitstellen.  Einige Beispiele:

* [Azure Automation-Updateverwaltung](../../automation/automation-update-management.md) von Betriebssystemupdates.
* [Azure Automation Desired State Configuration](../../automation/automation-dsc-overview.md) zur Beibehaltung eines konsistenten Konfigurationsstatus.
* Nachverfolgen von Konfigurationsänderungen mit [Azure Automation-Änderungsnachverfolgung und Bestand](../../automation/change-tracking.md).
* Azure-Dienste, z.B. [Application Insights](https://docs.microsoft.com/azure/application-insights/) und [Azure Security Center](https://docs.microsoft.com/azure/security-center/), die ihre Daten nativ direkt in Log Analytics speichern.  

## <a name="dependency-agent"></a>Abhängigkeits-Agent
Der Dependency-Agent wurde als Teil der Dienstzuordnungslösung entwickelt, die ursprünglich nicht von Microsoft entwickelt wurde. Für die [Dienstzuordnung](../insights/service-map.md) und [Azure Monitor für VMs](../insights/vminsights-overview.md) ist ein Dependency-Agent unter Windows- und Linux-VMs erforderlich, der in den Log Analytics-Agent integriert wird, um ermittelte Daten zu Prozessen zu sammeln, die auf dem virtuellen Computer und externen Prozessabhängigkeiten ausgeführt werden. Diese Daten werden in einem Log Analytics-Arbeitsbereich gespeichert, und die ermittelten verbundenen Komponenten werden visualisiert.

Möglicherweise brauchen Sie eine Kombination aus diesen Agents, um Ihren virtuellen Computer zu überwachen. Die Agents können als Azure-Erweiterungen nebeneinander installiert werden, unter Linux *muss* der Log Analytics-Agent jedoch zuerst installiert werden, da ansonsten ein Installationsfehler auftritt. 

## <a name="next-steps"></a>Nächste Schritte

- In der [Übersicht zum Log Analytics-Agent](../../azure-monitor/platform/log-analytics-agent.md) finden Sie die Anforderungen und unterstützten Methoden zum Bereitstellen des Agents auf Computern, die in Azure, in Ihrem Rechenzentrum oder in einer anderen Cloudumgebung gehostet werden.

