---
title: Erstellen einer Verwaltungslösung in Azure | Microsoft-Dokumentation
description: Verwaltungslösungen beinhalten in Azure gebündelte Verwaltungsszenarien, die Kunden zu ihrem Log Analytics-Arbeitsbereich hinzufügen können.  Diese Artikel beschreibt, wie Sie Verwaltungslösungen erstellen, die Sie in Ihrer Umgebung verwenden oder Ihren Kunden zur Verfügung stellen möchten.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ef1af4d3d27bc098341a4de716e293557baa946a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60595801"
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Entwerfen und Erstellen einer Verwaltungslösung in Azure (Vorschau)
> [!NOTE]
> Dies ist die vorläufige Dokumentation für das Erstellen von Verwaltungslösungen in Azure, die sich derzeit in der Vorschau befinden. Jedes unten beschriebene Schema kann sich ändern.

[Verwaltungslösungen]( solutions.md) bieten gebündelte Verwaltungsszenarien, die Kunden zu ihrem Log Analytics-Arbeitsbereich hinzufügen können.  Dieser Artikel stellt ein grundlegendes Verfahren zum Entwerfen und Erstellen einer Verwaltungslösung vor, die die häufigsten Anforderungen erfüllt.  Wenn Sie keine Erfahrung in der Erstellung von Verwaltungslösungen haben, können Sie dieses Verfahren als Ausgangspunkt verwenden und mit steigenden Anforderungen die Konzepte für komplexere Lösungen nutzen.

## <a name="what-is-a-management-solution"></a>Was ist eine Verwaltungslösung?

Verwaltungslösungen enthalten Azure-Ressourcen, die zusammenarbeiten, um ein bestimmtes Verwaltungsszenario zu erreichen.  Sie werden auch als [Ressourcenverwaltungsvorlagen](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) Implementiert, die Details zum Installieren und Konfigurieren der enthaltenen Ressourcen bei Installation der Lösung umfassen.

Die grundlegende Strategie besteht darin, Ihre Verwaltungslösung damit zu beginnen, dass Sie die einzelnen Komponenten in der Azure-Umgebung erstellen.  Sobald die Funktionalität Ihren Vorstellungen entspricht, können Sie damit beginnen, sie in eine [Verwaltungslösungsdatei]( solutions-solution-file.md) zu verpacken. 


## <a name="design-your-solution"></a>Entwerfen der Lösung
Das bekannteste Muster für eine Verwaltungslösung ist im folgenden Diagramm abgebildet.  Die verschiedenen Komponenten in diesem Muster werden weiter unten behandelt.

![Verwaltungslösung – Übersicht](media/solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Datenquellen
Der erste Schritt beim Entwerfen einer Lösung besteht darin, die benötigten Daten aus dem Log Analytics-Repository zu bestimmen.  Diese Daten können durch eine [Datenquelle](../../azure-monitor/platform/agent-data-sources.md) oder [eine andere Lösung]( solutions.md) erfasst werden, oder Ihre Lösung muss ggf. den Prozess zum Sammeln der Daten enthalten.

Es gibt zahlreiche Möglichkeiten zum Erfassen von Datenquellen im Log Analytics-Repository, wie unter [Datenquellen in Log Analytics](../../azure-monitor/platform/agent-data-sources.md) beschrieben.  Neben Leistungsindikatoren für Windows- und Linux-Clients schließt dies im Windows-Ereignisprotokoll enthaltene oder von Syslog generierte Ereignisse ein.  Sie können auch von Azure Monitor erfasste Daten aus Azure-Ressourcen sammeln.  

Können bestimmte benötigte Daten nicht über eine der verfügbaren Datenquellen abgerufen werden, können Sie die [HTTP-Datensammler-API](../../azure-monitor/platform/data-collector-api.md) verwenden, die Ihnen ermöglicht, von jedem beliebigen Client aus, der eine REST-API aufrufen kann, Daten in das Log Analytics-Repository zu schreiben.  Die geläufigste Methode der Sammlung benutzerdefinierter Daten in einer Verwaltungslösung besteht darin, ein [Runbook in Azure Automation](../../automation/automation-runbook-types.md) zu erstellen, mit dem die erforderlichen Daten aus Azure oder externen Ressourcen gesammelt und mithilfe der Datensammler-API in das Repository geschrieben werden.  

### <a name="log-searches"></a>Protokollsuchvorgänge
[Protokollsuchen](../../azure-monitor/log-query/log-query-overview.md) dienen dazu, Daten aus dem Log Analytics-Repository zu extrahieren und zu analysieren.  Sie werden von Ansichten und Warnungen verwendet und ermöglichen Benutzern außerdem, Ad-hoc-Analysen von Daten im Repository auszuführen.  

Sie sollten alle Abfragen definieren, die Sie für den Benutzer als hilfreich erachten, auch wenn sie nicht von Ansichten oder Warnungen verwendet werden.  Sie stehen den Benutzern im Portal als gespeicherte Suchvorgänge zur Verfügung, und Sie können sie auch einer [Visualisierungskomponente „Liste der Abfragen“](../../azure-monitor/platform/view-designer-parts.md#list-of-queries-part) in Ihrer benutzerdefinierte Ansicht hinzufügen.

### <a name="alerts"></a>Alerts
[Warnungen in Log Analytics](../../azure-monitor/platform/alerts-overview.md) identifizieren Probleme über [Protokollsuchvorgänge](#log-searches) in den Daten des Repositorys.  Entweder benachrichtigen sie den Benutzer oder führen automatisch eine Aktion als Antwort aus. Sie sollten verschiedene Warnungsbedingungen für Ihre Anwendung definieren und entsprechende Warnungsregeln zu Ihrer Lösungsdatei hinzufügen.

Wenn ein Problem prinzipiell mit einem automatisierten Prozess behoben werden kann, empfiehlt es sich in der Regel, ein Runbook in Azure Automation zu erstellen, mit dem die entsprechende Korrektur durchgeführt wird.  Die meisten Azure-Dienste lassen sich mit [Cmdlets](/powershell/azure/overview) verwalten, die vom Runbook zum Ausführen solcher Funktionen verwendet werden können.

Wenn Ihre Lösung externe Funktionen als Antwort auf eine Warnung erfordert, können Sie eine [Webhookantwort](../../azure-monitor/platform/alerts-metric.md) verwenden.  Dadurch können Sie einen externen Webdienst aufrufen, der Informationen aus der Warnung sendet.

### <a name="views"></a>Ansichten
Ansichten in Log Analytics dienen zur Visualisierung von Daten aus dem Log Analytics-Repository.  Jede Lösung enthält in der Regel eine einzige Ansicht mit einer [Kachel](../../azure-monitor/platform/view-designer-tiles.md), die auf dem Hauptdashboard des Benutzers angezeigt wird.  Die Ansicht kann eine beliebige Anzahl von [Visualisierungskomponenten](../../azure-monitor/platform/view-designer-parts.md) enthalten, um dem Benutzer unterschiedliche Visualisierungen der gesammelten Daten bereitzustellen.

Sie [Erstellen benutzerdefinierte Ansichten mit dem Ansicht-Designer](../../azure-monitor/platform/view-designer.md). Diese können Sie später exportieren und Ihrer Lösungsdatei hinzufügen.  


## <a name="create-solution-file"></a>Erstellen einer Lösungsdatei
Nachdem Sie die Komponenten, die Teil der Lösung werden sollen, getestet und konfiguriert haben, können Sie [die Lösungsdatei erstellen]( solutions-solution-file.md).  Implementieren Sie die Komponenten der Lösung in eine [Resource Manager-Vorlage](../../azure-resource-manager/resource-group-authoring-templates.md), die eine [Lösungsressource]( solutions-solution-file.md#solution-resource) mit Beziehungen zu den anderen Ressourcen in der Datei umfasst.  


## <a name="test-your-solution"></a>Testen Ihrer Lösung
Während der Entwicklung Ihrer Lösung müssen Sie diese in Ihrem Arbeitsbereich installieren und testen.  Hierzu können Sie jede der verfügbaren Methoden zum [Testen und Installieren der Resource Manager-Vorlagen installiert](../../azure-resource-manager/resource-group-template-deploy.md) verwenden.

## <a name="publish-your-solution"></a>Veröffentlichen Ihrer Lösung
Sobald Sie Ihre Lösung fertig gestellt und getestet haben, können Sie sie Kunden über die folgenden Quellen zur Verfügung stellen.

- **Azure-Schnellstartvorlagen**.  [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/) ist ein Satz aus von der Community über GitHub bereitgestellten Resource Manager-Vorlagen.  Informationen zur Bereitstellung Ihrer Lösung finden Sie im [Contribution Guide](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE).
- **Azure Marketplace**.  Der [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) ermöglicht Ihnen, Ihre Lösung an andere Entwickler, unabhängige Softwareanbieter (ISVs) und IT-Experten zu verteilen und zu verkaufen.  Unter [Veröffentlichen und Verwalten eines Angebots im Azure Marketplace](../../marketplace/marketplace-publishers-guide.md) erfahren Sie, wie Sie Ihre Lösung im Azure Marketplace veröffentlichen können.



## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über das [Erstellen einer Lösungsdatei ]( solutions-solution-file.md) für Ihre Verwaltungslösung.
* Erfahren Sie Näheres zum [Erstellen von Azure Resource Manager-Vorlagen](../../azure-resource-manager/resource-group-authoring-templates.md).
* Durchsuchen Sie die [Azure-Schnellstartvorlagen](https://azure.microsoft.com/documentation/templates), um Beispiele für verschiedene Resource Manager-Vorlagen zu finden.
