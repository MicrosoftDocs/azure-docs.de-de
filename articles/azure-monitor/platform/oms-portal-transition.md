---
title: Wechsel des OMS-Portals zu Azure | Microsoft-Dokumentation
description: Das OMS-Portal hat das Ende seines Lebenszyklus erreicht und wechselt mit seiner gesamten Funktionalität zum Azure-Portal. Dieser Artikel enthält Details zu diesem Übergang.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: ba81e0bd74390d8b20803f8f9cf6e9f2ae9f0eab
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184517"
---
# <a name="oms-portal-moving-to-azure"></a>Wechsel des OMS-Portals zu Azure

> [!NOTE]
> Dieser Artikel gilt sowohl für die öffentliche Azure-Cloud als auch für die Government-Cloud, sofern nichts anderes angegeben ist.

**Das OMS-Portal für die öffentliche Azure-Cloud wurde offiziell eingestellt. Das OMS-Portal für die Azure US Government-Cloud wurde offiziell am 15. Mai 2019 eingestellt.** Wir freuen uns auf den Wechsel zum Azure-Portal und gehen davon aus, dass der Übergang reibungslos abläuft. Wir wissen aber, dass Änderungen schwierig sind und Störungen verursachen können. Im weiteren Verlauf dieses Artikels werden die wichtigsten Szenarien sowie die Roadmap für diesen Übergang beschrieben.

Das Azure-Portal dient als Hub für alle Azure-Dienste und bietet eine umfassende Verwaltungsoberfläche mit Funktionen wie Dashboards für das Anheften von Ressourcen, einer intelligenten Suche nach Ressourcen und Tagging für die Ressourcenverwaltung. Zum Konsolidieren und Optimieren des Überwachungs- und Verwaltungsworkflows haben wir damit begonnen, die Funktionen des OMS-Portals in das Azure-Portal einzubinden. Alle Features des OMS-Portals sind nun auch Bestandteil des Azure-Portals. Tatsächlich sind einige der neuen Features, wie z.B. Traffic Analytics, nur im Azure-Portal verfügbar. Sie können alle Aufgaben, die Sie bisher im OMS-Portal erledigt haben, nun im Azure-Portal ausführen – und dazu noch vieles mehr. Wenn Sie das Azure-Portal noch nicht verwenden – fangen Sie am besten heute noch damit an!

## <a name="what-is-changing"></a>Was ändert sich? 
Die folgenden Änderungen werden mit der Einstellung des OMS-Portals angekündigt. Die einzelnen Änderungen werden in den nachstehenden Abschnitten ausführlicher erläutert.

- Sie können nur im Azure-Portal [neue Arbeitsbereiche](#new-workspaces) erstellt.
- Die neue Oberfläche für die Warnungsverwaltung [ersetzt die Warnungsverwaltungslösung](#changes-to-alerts).
- Die [Benutzerzugriffsverwaltung](#user-access-and-role-migration) erfolgt jetzt im Azure-Portal mithilfe der rollenbasierten Zugriffssteuerung von Azure.
- Der [Application Insights-Connector ist nicht mehr erforderlich](#application-insights-connector-and-solution), da die gleiche Funktionalität über arbeitsbereichsübergreifende Abfragen ermöglicht wird.
- Die [mobile OMS-App](#oms-mobile-app) wird eingestellt. 
- Die [NSG-Lösung wird durch eine verbesserte Funktionalität ersetzt](#azure-network-security-group-analytics), die über die Traffic Analytics-Lösung bereitsteht.
- Neue Verbindungen zwischen System Center Operations Manager und Log Analytics erfordern [aktualisierte Management Packs](#system-center-operations-manager).
- Ausführliche Informationen zu den Änderungen bei der [Updateverwaltung](../../automation/update-management/overview.md) finden Sie unter [Migrieren von OMS-Updatebereitstellungen zu Azure](../../automation/migrate-oms-update-deployments.md).


## <a name="what-should-i-do-now"></a>Wie soll ich jetzt vorgehen?
Zwar funktionieren die meisten Features weiterhin ohne Migration, müssen Sie folgende Aufgaben ausführen:

- Sie müssen die [Benutzerberechtigungen zum Azure-Portal migrieren](#user-access-and-role-migration).
- Ausführliche Informationen zur Umstellung auf die Lösung für die Updateverwaltung finden Sie unter [Migrieren von OMS-Updatebereitstellungen zu Azure](../../automation/migrate-oms-update-deployments.md).

Informationen zum Übergang zum Azure-Portal finden Sie in den [häufig gestellten Fragen zum Übergang vom OMS-Portal zum Azure-Portal für Log Analytics-Benutzer](../overview.md). 

## <a name="user-access-and-role-migration"></a>Benutzerzugriff und Rollenmigration
Die Zugriffsverwaltung im Azure-Portal ist umfangreicher und leistungsfähiger als die Zugriffsverwaltung im OMS-Portal. Ausführliche Informationen zur Zugriffsverwaltung in Log Analytics finden Sie unter [Entwerfen Ihres Azure Monitor-Protokollarbeitsbereichs](design-logs-deployment.md).

> [!NOTE]
> In vorherigen Versionen dieses Artikels wurde darüber informiert, dass die Berechtigungen automatisch vom OMS-Portal zum Azure-Portal konvertiert würden. Diese automatische Konvertierung ist nicht mehr geplant, Sie müssen die Konvertierung selbst durchführen.

Möglicherweise verfügen Sie bereits über den geeigneten Zugriff im Azure-Portal – in diesem Fall müssen Sie keine Änderungen vornehmen. Wenn Sie nicht über den geeigneten Zugriff verfügen, muss Ihr Administrator Ihnen die benötigten Berechtigungen zuweisen.

- Sie besitzen im OMS-Portal Benutzerberechtigungen für den schreibgeschützten Zugriff, aber keine Berechtigungen im Azure-Portal. 
- Sie besitzen im OMS-Portal Berechtigungen als Mitwirkender, im Azure-Portal aber nur Lesezugriff.
 
In beiden Fällen muss Ihr Administrator Ihnen die geeignete Rolle aus der folgenden Tabelle manuell zuweisen. Es wird empfohlen, diese Rolle auf Ebene der Ressourcengruppe oder des Abonnements zuzuweisen.  Für beide Fälle wird in Kürze eine ausführlichere Beschreibung verfügbar sein.

| OMS-Portalberechtigung | Azure-Rolle |
|:---|:---|
| ReadOnly | Log Analytics-Leser |
| Mitwirkender | Log Analytics-Mitwirkender |
| Administrator | Besitzer | 
 

## <a name="new-workspaces"></a>Neue Arbeitsbereiche
Sie können im OMS-Portal keine neuen Arbeitsbereiche mehr erstellen. Folgen Sie den Anweisungen unter [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../learn/quick-create-workspace.md), um einen neuen Arbeitsbereich im Azure-Portal zu erstellen.

## <a name="changes-to-alerts"></a>Änderungen bei Warnungen

### <a name="alert-extension"></a>Warnungserweiterung  

Warnungen wurden [in das Azure-Portal erweitert](./alerts-unified-log.md). Vorhandene Warnungen werden weiterhin im OMS-Portal aufgelistet, aber Sie können sie nur im Azure-Portal verwalten. Wenn Sie programmgesteuert mithilfe der Log Analytics-REST-API für Warnungen oder der Log Analytics-Warnungsressourcenvorlage auf Warnungen zugreifen, müssen Sie in Ihren API-Aufrufen, Azure Resource Manager-Vorlagen und PowerShell-Befehlen anstelle von Aktionen nun Aktionsgruppen verwenden.

### <a name="alert-management-solution"></a>Lösung „Alert Management“
Die [Warnungsverwaltungslösung](alert-management-solution.md) ist entgegen einer früheren Ankündigung weiterhin im Azure-Portal verfügbar und wird weiterhin vollständig unterstützt. Die Lösung kann weiterhin über den Azure Marketplace installiert werden.

Auch wenn die Warnungsverwaltungslösung weiterhin verfügbar ist, empfehlen wir die Verwendung der [einheitlichen Warnungsoberfläche von Azure Monitor](alerts-overview.md), um alle Warnungen in Azure zu visualisieren und zu verwalten. Diese neue Oberfläche aggregiert Warnungen aus mehreren Quellen nativ in Azure (einschließlich Protokollwarnungen aus Log Analytics). Wenn Sie die einheitliche Warnungsoberfläche von Azure Monitor verwenden, wird die Warnungsverwaltungslösung nur noch für die Integration von Warnungen aus System Center Operation Manager in Azure benötigt. Auf der einheitlichen Warnungsoberfläche von Azure Monitor können Sie sich Verteilungen Ihrer Warnungen ansehen, die automatisierte Gruppierung zusammengehöriger Warnungen mithilfe intelligenter Gruppen nutzen und Warnungen für mehrere Abonnements unter Verwendung umfangreicher Filter anzeigen. Zukünftige Verbesserungen bei der Warnungsverwaltung werden in erster Linie in dieser neuen Umgebung bereitgestellt. 

Die von der Warnungsverwaltungslösung gesammelten Daten (Datensätze vom Typ „Warnung“) sind weiterhin in Log Analytics verfügbar, solange die Lösung für den Arbeitsbereich installiert ist. 

## <a name="oms-mobile-app"></a>Mobile OMS-App
Der Lebenszyklus der mobilen OMS-App endet zusammen mit dem OMS-Portal. Statt die mobile OMS-App zu verwenden, können Sie für den Zugriff auf Informationen über Ihre IT-Infrastruktur, Dashboards und gespeicherte Abfragen direkt über den Browser auf Ihrem mobilen Gerät auf das Azure-Portal zugreifen. Um Warnungen zu erhalten, sollten Sie [Azure-Aktionsgruppen](action-groups.md) konfigurieren, damit Sie Benachrichtigungen in Form einer SMS oder eines Sprachanrufs empfangen.

## <a name="application-insights-connector-and-solution"></a>Application Insights-Connector und -Lösung
[Application Insights-Connector](app-insights-connector.md) bietet eine Möglichkeit, Application Insights-Daten in einen Log Analytics-Arbeitsbereich einzuschließen. Diese Datenduplizierung war für die Sichtbarkeit in der gesamten Infrastruktur und für alle Anwendungsdaten erforderlich. Dank der Unterstützung der erweiterten Datenaufbewahrung von Application Insights (März 2019) und der Möglichkeit zum Ausführen [ressourcenübergreifender Abfragen](../log-query/cross-workspace-query.md) sowie dem [Anzeigen mehrerer Azure Monitor Application Insights-Ressourcen](../log-query/unify-app-resource-data.md) müssen keine Daten aus Ihren Application Insights-Ressourcen dupliziert und an Log Analytics gesendet werden. Darüber hinaus sendet der Connector einen Teil der Anwendungseigenschaften an Log Analytics, und die ressourcenübergreifenden Abfragen sorgen für mehr Flexibilität.  

Die Unterstützung für den Application Insights-Connector wurde eingestellt. Am 30. März 2019 wurde dieser parallel zur Einstellung des OMS-Portals aus dem Azure Marketplace entfernt. Vorhandene Verbindungen sind weiterhin bis zum 30 Juni 2019 einsetzbar. Aufgrund der Veraltung des OMS-Portals können vorhandene Verbindungen nicht mehr über das Portal konfiguriert oder entfernt werden. Dies wird über die REST-API unterstützt, die im Januar 2019 bereitgestellt wird, und eine entsprechende Benachrichtigung wird unter [Azure-Updates](https://azure.microsoft.com/updates/) veröffentlicht. 

## <a name="azure-network-security-group-analytics"></a>Azure-Netzwerksicherheitsgruppen-Analyse
Die [Analyselösung der Azure-Netzwerksicherheitsgruppe](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) wird durch die kürzlich gestartete [Traffic Analytics](https://azure.microsoft.com/blog/traffic-analytics-in-preview/)-Lösung ersetzt, die Einblicke in die Benutzer- und Anwendungsaktivität in Cloudnetzwerken bietet. Traffic Analytics erleichtert es Ihnen, die Netzwerkaktivität Ihres Unternehmens zu überwachen, Anwendungen und Daten zu schützen, die Arbeitsauslastungsleistung zu optimieren und die Kompatibilität aufrechtzuerhalten. 

Diese Lösung analysiert NSG-Datenflussprotokolle und bietet folgende Einblicke.

- Datenverkehrsflüsse über Ihre Netzwerke zwischen Azure und Internet, öffentliche Cloudregionen, VNETs und Subnetze
- Anwendungen und Protokolle in Ihrem Netzwerk, ohne die Notwendigkeit, Sniffer oder dedizierte Datenflusssammlung-Appliances einzusetzen
- Spitzennutzer, „geschwätzige“ Anwendungen, VM-Konversationen in der Cloud, Datenverkehrhotspots
- Quellen und Ziele des Datenverkehrs über VNETs, Beziehungen zwischen geschäftskritischen Diensten und Anwendungen
- Sicherheit, einschließlich schädlichen Datenverkehrs, zum Internet offenen Ports, Anwendungen oder VMs, die versuchen, auf das Internet zuzugreifen
- Kapazitätsauslastung, wodurch Sie Probleme einer Überbereitstellung oder Unterauslastung vermeiden

Sie können weiterhin Diagnoseeinstellungen verwenden, um NSG-Protokolle an Log Analytics zu senden, damit Ihre vorhandenen gespeicherten Suchen, Warnungen und Dashboards weiterhin funktionieren. Kunden, bei denen die Lösung bereits installiert ist, können diese bis auf Weiteres verwenden. Ab dem 5. September wird die Netzwerksicherheitsgruppen-Analyselösung aus dem Marketplace entfernt und über die Community als [Azure-Schnellstartvorlage](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights) zur Verfügung gestellt.

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Wenn Sie [Ihre Operations Manager-Verwaltungsgruppe mit Log Analytics verbunden haben](om-agents.md), funktioniert sie weiterhin ohne Änderungen. Für neue Verbindungen müssen Sie jedoch die Anweisungen in [Microsoft System Center Operations Manager Management Pack to configure Operations Management Suite](https://techcommunity.microsoft.com/t5/system-center-blog/bg-p/SystemCenterBlog) (Microsoft System Center Operations Manager Management Pack zum Konfigurieren der Operations Management Suite) befolgen.

## <a name="next-steps"></a>Nächste Schritte
- Einen Leitfaden für den Wechsel vom OMS-Portal zum Azure-Portal finden Sie unter [Häufig gestellte Fragen zum Übergang vom OMS-Portal zum Azure-Portal für Log Analytics-Benutzer](../overview.md).