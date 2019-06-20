---
title: Updates für Azure Monitor-Terminologie | Microsoft-Dokumentation
description: Hier werden die aktuellen Terminologieänderungen beschrieben, die an Azure-Überwachungsdiensten vorgenommen wurden.
author: bwren
manager: carmonm
editor: tysonn
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2019
ms.author: bwren
ms.openlocfilehash: 8f645f7d569546a8362d0149806a2b4636567fd0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61086747"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Namens- und Terminologieänderungen bei Azure Monitor
Vor Kurzem wurden wichtige Änderungen bei Azure Monitor vorgenommen und verschiedene Dienste zur vereinfachten Überwachung für Azure-Kunden zusammengefasst. Dieser Artikel beschreibt die neuesten Namens- und Terminologieänderungen in der Azure Monitor-Dokumentation.

## <a name="february-2019---log-analytics-terminology"></a>Februar 2019 – Log Analytics-Terminologie
Nach der Konsolidierung verschiedener Dienste unter Azure Monitor erfolgt nun der nächste Schritt durch Ändern der Terminologie in unserer Dokumentation, damit der Azure Monitor-Dienst und seine verschiedenen Komponenten besser beschrieben werden. 

### <a name="log-analytics"></a>Log Analytics
Azure Monitor-Protokolldaten werden weiterhin in einem Log Analytics-Arbeitsbereich gespeichert und immer noch vom gleichen Log Analytics-Dienst erfasst und analysiert, doch wird der Begriff _Log Analytics_ an vielen Stellen in _Azure Monitor-Protokolle_ geändert. Dieser Begriff gibt dessen Rolle in Azure Monitor besser wieder und sorgt für konsistentere [Metriken in Azure Monitor](platform/data-platform-metrics.md).

Der Begriff _Log Analytics_ wird jetzt in erster Linie für die Seite im Azure-Portal verwendet, die zum Schreiben und Ausführen von Abfragen sowie zum Analysieren von Protokolldaten dient. Dies entspricht von der Funktion her dem [Metrik-Explorer](platform/metrics-charts.md), d.h. der Seite im Azure-Portal, die zum Analysieren von Metrikdaten verwendet wird.

### <a name="log-analytics-workspaces"></a>Log Analytics-Arbeitsbereiche
[Arbeitsbereiche](platform/manage-access.md), die Protokolldaten in Azure Monitor enthalten, werden weiterhin als Log Analytics-Arbeitsbereiche bezeichnet. Das Menü **Log Analytics** im Azure-Portal wurde in **Log Analytics-Arbeitsbereiche** umbenannt. Dort können Sie [neue Arbeitsbereiche erstellen](learn/quick-create-workspace.md) und Datenquellen konfigurieren. Analysieren Sie Ihre Protokolle und andere Überwachungsdaten in **Azure Monitor**, und konfigurieren Sie Ihren Arbeitsbereich in **Log Analytics-Arbeitsbereichen**.

### <a name="management-solutions"></a>Verwaltungslösungen
[Verwaltungslösungen](insights/solutions.md) wurden in _Überwachungslösungen_ umbenannt, da dieser Begriff deren Funktionalität besser beschreibt.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>August 2018 – Konsolidierung der Überwachungsdienste in Azure Monitor
Log Analytics und Application Insights wurden in Azure Monitor zusammengeführt, um eine einzelne, integrierte Benutzeroberfläche für das Überwachen von Azure-Ressourcen und Hybridumgebungen zu schaffen. Es wurde keine Funktionalität entfernt, und die Benutzer können die gleichen Szenarien wie bisher ausführen, ohne Verlust oder Einschränkung bei den Features.

Die Dokumentation für jeden dieser Dienste wurde für Azure Monitor zu einer einzelnen Inhaltssammlung zusammengeführt. Das hilft dem Leser, alle Inhalte für ein bestimmtes Überwachungsszenario an einem einzelnen Ort zu finden, statt mehrere Inhaltssammlungen zu Rate ziehen zu müssen. Mit der Entwicklung des konsolidierten Diensts wird auch die Integration der Inhalte zunehmen.

Andere Features, die wir als Teil von Log Analytics angesehen haben, wie etwa Agents und Ansichten, wurden ebenfalls als Features von Azure Monitor neu positioniert. Ihre Funktionalität hat sich abgesehen von möglichen Verbesserungen der Benutzerfreundlichkeit im Azure-Portal nicht geändert.


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>April 2018 – Einstellung der Marke „Operations Management Suite“
Operations Management Suite (OMS) war eine Bündelung der folgenden Azure-Verwaltungsdienste zu Lizenzzwecken:

- Application Insights
- Azure-Automatisierung
- Azure Backup
- Log Analytics
- Site Recovery

[Für diese Dienste wurde ein neues Preismodell eingeführt](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/), und das OMS-Bündel steht für Neukunden nicht mehr zur Verfügung. Keiner der in OMS enthaltenen Dienste wurde geändert, abgesehen von der oben beschriebenen Konsolidierung in Azure Monitor. 




## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie eine [Übersicht zu Azure Monitor](overview.md), die dessen verschiedene Komponenten und Features beschreibt.
- Erfahren Sie mehr über die [Umstellung vom OMS-Portal](../log-analytics/log-analytics-oms-portal-transition.md).