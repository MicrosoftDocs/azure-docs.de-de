---
title: Erweitern (Kopieren) von Log Analytics-Warnungen in die Azure Government-Cloud
description: Übersicht über den Prozess zum Kopieren von Warnungen aus Log Analytics im OMS-Portal nach Azure Alerts mit Details zur Berücksichtigung häufiger Bedenken auf Kundenseite.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: efd2fc9d164564126b7b641ef35bbb10c4834f49
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60996399"
---
# <a name="extend-log-analytics-alerts-to-azure-alerts"></a>Erweitern von Log Analytics-Warnungen nach Azure Alerts

> [!NOTE]
> Microsoft hat den in diesem Artikel beschriebenen Prozess für die öffentlichen Versionen von Azure bereits abgeschlossen. Er gilt jedoch weiterhin für US Government-Versionen.  

Bis vor Kurzem enthielt Azure Log Analytics eigene Warnfunktionen, die Sie auf der Grundlage von Log Analytics-Daten proaktiv über Bedingungen benachrichtigen konnten. Sie haben im Microsoft Operations Management Suite-Portal Warnungsregeln verwaltet. Die neue Oberfläche für Warnungen bietet nun die übergreifende Warnungsanzeige für verschiedene Dienste in Microsoft Azure. Sie ist im Azure-Portal unter Azure Monitor als **Warnungen** verfügbar und unterstützt die Anzeige von Warnungen aus Aktivitätsprotokollen, Metriken und Protokollen von Log Analytics ebenso wie von Azure Application Insights.

Kunden der Azure Government-Cloud, die das OMS-Portal verwenden, können ab **1. Februar 2019** [ihre Warnungsregeln freiwillig auf Azure ausdehnen](alerts-extend-tool.md). Ab **1. März 2019** führt Microsoft systematisch die automatische Erweiterung aller im OMS-Portal für Azure Government vorhandenen Warnungsregeln in Azure durch – ohne Ausfallzeiten oder Unterbrechung der Überwachung. Alle am oder nach dem **1. März 2019** im OMS-Portal der Azure Government-Cloud erstellten Arbeitsbereiche werden automatisch auf Azure erweitert.

## <a name="benefits-of-extending-your-alerts"></a>Vorteile des Erweiterns von Warnungen
Das Erstellen und Verwalten von Warnungen im Azure-Portal bietet eine Reihe von Vorteilen, wie z. B.:

- Anders als das Portal der Operations Management Suite, wo nur 250 Warnungen erstellt und angezeigt werden können, weist Azure Alerts diese Einschränkung nicht auf.
- In Azure Alerts können Sie alle Ihre Warnungstypen verwalten, durchlaufen und anzeigen. Bisher war das nur bei Log Analytics-Warnungen möglich.
- Sie können den Zugriff für Benutzer mithilfe der [Azure Monitor-Rolle](../../azure-monitor/platform/roles-permissions-security.md) auf die Überwachung und Anzeige von Warnungen einschränken.
- In Azure Alerts können [Aktionsgruppen](../../azure-monitor/platform/action-groups.md) verwendet werden. Dadurch können Sie für jede Warnung mit mehr als einer Aktion arbeiten. Sie können eine SMS oder einen Sprachanruf senden, ein Azure Automation-Runbook oder einen Webhook aufrufen und einen ITSM-Connector (IT-Service-Management) konfigurieren. 

## <a name="process-of-extending-your-alerts"></a>Prozess zum Erweitern von Warnungen
Der Prozess des Verschiebens von Warnungen aus Log Analytics nach Azure Alerts bringt keinerlei Änderung Ihrer Warnungsdefinition, Abfrage oder Konfiguration mit sich. Die einzige erforderliche Änderung besteht darin, dass in Azure alle Aktionen mithilfe von Aktionsgruppen ausgeführt werden. Wenn Ihrer Warnung bereits Aktionsgruppen zugeordnet sind, sind diese bei der Erweiterung nach Azure eingeschlossen.

Wenn Sie die Erweiterung von Warnungen nach Azure in einem Log Analytics-Arbeitsbereich planen, funktionieren sie weiterhin und beschädigen Ihre Konfiguration in keiner Weise. Warnungen, für die Erweiterung geplant ist, stehen möglicherweise vorübergehend nicht zur Änderung zur Verfügung, jedoch können Sie während dieser Zeit weiterhin neue Azure-Warnungen erstellen. Wenn Sie versuchen, Warnungen im Operations Management Suite-Portal zu bearbeiten oder zu erstellen, bietet sich Ihnen die Option, sie weiterhin in Ihrem Log Analytics-Arbeitsbereich zu erstellen. Alternativ können Sie sich entscheiden, sie im Azure-Portal in Azure Alerts zu erstellen.

 ![Screenshot der Option zum Erstellen von Warnungen in Log Analytics oder Azure Alerts](media/alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Das Erweitern von Warnungen von Log Analytics nach Azure führt nicht zu Belastungen Ihres Kontos. Die Verwendung von Azure Alerts für abfragebasierte Log Analytics-Warnungen wird nicht in Rechnung gestellt, sofern die Grenzwerte und Bedingungen eingehalten werden, die in der [Azure Monitor-Preisrichtlinie](https://azure.microsoft.com/pricing/details/monitor/) festgelegt sind.  


### <a name="how-to-extend-your-alerts-voluntarily"></a>Freiwilliges Erweitern Ihrer Warnungen
Sie können Ihre Warnungen nach Azure Alerts erweitern, indem Sie einen Assistenten im Operations Management Suite-Portal verwenden oder eine API programmgesteuert ansprechen. Weitere Informationen finden Sie unter [Erweitern von Warnungen in Azure mithilfe von Operations Management Suite-Portal und API](alerts-extend-tool.md).

## <a name="experience-after-extending-your-alerts"></a>Benutzeroberfläche nach dem Erweitern Ihrer Warnungen
Nachdem Sie Ihre Warnungen nach Azure Alerts erweitert haben, stehen diese weiterhin im Operations Management Suite-Portal zur Verwaltung zur Verfügung, nicht anders als zuvor.

![Screenshot des Operations Management Suite-Portals mit aufgelisteten Warnungen](media/alerts-extend/PostExtendList.png)

Wenn Sie im Operations Management Suite-Portal versuchen, eine vorhandene Warnung zu bearbeiten oder eine neue Warnung zu erstellen, werden Sie automatisch zu Azure Alerts umgeleitet.  

> [!NOTE]
> Achten Sie darauf, dass die Berechtigungen der Personen, die Warnungen bearbeiten oder erstellen müssen, in Azure ordnungsgemäß zugewiesen sind. Informationen zum Verständnis, welche Berechtigungen Sie erteilen müssen, finden Sie unter [Berechtigungen für die Verwendung von Azure Monitor und Alerts](../../azure-monitor/platform/roles-permissions-security.md).  
> 

Sie können Warnungen weiterhin über die [Log Analytics-API](../../azure-monitor/platform/api-alerts.md) und die [Log Analytics-Ressourcenvorlage](../../azure-monitor/insights/solutions-resources-searches-alerts.md) erstellen. Wenn Sie sich dafür entscheiden, müssen Sie Aktionsgruppen einschließen.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über die Tools zum [Initiieren der Erweiterung von Warnungen aus Log Analytics nach Azure](alerts-extend-tool.md).
* Erfahren Sie mehr über die neue [Azure Alerts-Benutzeroberfläche](../../azure-monitor/platform/alerts-overview.md).
* Informationen zum Erstellen von [Protokollwarnungen in Azure Alerts](alerts-unified-log.md).

