---
title: Häufig gestellte Fragen zu Azure Monitor für Container | Microsoft-Dokumentation
description: Azure Monitor für Container ist eine Lösung, die die Integrität Ihrer AKS-Cluster und Containerinstanzen in Azure überwacht. Dieser Artikel beantwortet häufig gestellte Fragen.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: afa332b40884a79b5114b3b8093cd27108c39984
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65780006"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Häufig gestellte Fragen zu Azure Monitor für Container

Dieser Microsoft-Artikel enthält eine Liste häufig gestellter Fragen zu Azure Monitor für Container. Wenn Sie weitere Fragen zur Lösung haben, besuchen Sie das [Diskussionsforum](https://feedback.azure.com/forums/34192--general-feedback), und stellen Sie Ihre Fragen. Wenn eine Frage häufiger gestellt wird, fügen wir sie diesem Artikel hinzu, damit sie schnell und einfach gefunden werden kann.

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Warum werden in meinem Log Analytics-Arbeitsbereich keine Daten angezeigt?

Wenn in Ihrem Log Analytics-Arbeitsbereich nicht täglich zu einer bestimmten Zeit Daten angezeigt werden, haben Sie vielleicht den Standardgrenzwert von 500MB oder die tägliche Obergrenze erreicht, die angegeben wird, um die Menge der täglich zu sammelnden Daten zu steuern. Wenn der Grenzwert für den Tag erreicht ist, wird die Datensammlung beendet und erst am nächsten Tag fortgesetzt. Um Ihre Datennutzung zu überprüfen und eine Aktualisierung auf einen anderen Tarif basierend auf Ihren erwarteten Nutzungsmustern vorzunehmen, lesen Sie [Protokollieren von Datennutzung und Kosten](../platform/manage-cost-storage.md). 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Welche Zustände von Containern sind in der Tabelle ContainerInventory angegeben?

Die Tabelle „ContainerInventory“ enthält Informationen zu beendeten und ausgeführten Containern. Die Tabelle wird von einem Workflow innerhalb des Agent ausgefüllt, der den Docker nach allen Containern abfragt (ausgeführte und beendete) und diese Daten an den Log Analytics-Arbeitsbereich weiterleitet.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Wie behebe ich den Fehler **Abonnementregistrierung fehlt**?

Wenn Sie die Fehlermeldung **Abonnementregistrierung für Microsoft.OperationsManagement fehlt** erhalten, können Sie den Fehler beheben, indem Sie den Ressourcenanbieter **Microsoft.OperationsManagement** in dem Abonnement registrieren, in dem der Arbeitsbereich definiert ist. Die Dokumentation für diesen Vorgang finden Sie [hier](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Gibt es Unterstützung für RBAC-fähige AKS-Cluster?

Die Containerüberwachungslösung bietet keine Unterstützung für RBAC, doch ist dies bei Azure Monitor für Container der Fall. Die Seite mit den Lösungsdetails zeigt möglicherweise nicht die richtigen Informationen auf den Blättern an, die Daten für diese Cluster anzeigen.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Wie aktiviere ich die Protokollsammlung für Container im Kube-System-Namespace über Helm?

Die Protokollsammlung von Containern im Kube-System-Namespace ist standardmäßig deaktiviert. Die Protokollsammlung kann durch das Festlegen einer Umgebungsvariablen auf dem Omsagent aktiviert werden. Weitere Informationen finden Sie auf der GitHub-Seite [Azure Monitor für Container](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers). 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Wie aktualisiere ich den Omsagent auf die neueste Version?

Informationen zum Upgrade des Agent, finden Sie unter [Agentverwaltung](container-insights-manage-agent.md).

## <a name="how-do-i-enable-multi-line-logging"></a>Wie aktiviere ich die mehrzeilige Protokollierung?

Derzeit unterstützt Azure Monitor für Container keine mehrzeilige Protokollierung, aber es gibt Workarounds. Sie können alle Dienste so konfigurieren, dass sie im JSON-Format schreiben, und dann schreibt Docker/Moby sie als eine einzelne Zeile.

Beispielsweise können Sie Ihr Protokoll als JSON-Objekt umschließen, wie im folgenden Beispiel für eine Beispiel node.js-Anwendung gezeigt:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Diese Daten sehen in Azure Monitor für Protokolle bei der Abfrage wie im folgenden Beispiel aus:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Eine ausführliche Betrachtung des Problems finden Sie unter folgendem [Github-Link](https://github.com/moby/moby/issues/22920).

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Wie behebe ich Azure AD-Fehler, wenn ich Liveprotokolle aktiviere? 

Es wird möglicherweise der folgende Fehler angezeigt: **The reply url specified in the request does not match the reply urls configured for the application: '<application ID\>'** . (Die in der Anforderung angegebene Antwort-URL entspricht nicht den für die Anwendung konfigurierten Antwort-URLs: <Anwendungs-ID>.) Die Lösung für dieses Problem finden Sie im Artikel [Anzeigen von Containerprotokollen in Echtzeit mit Azure Monitor für Container](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Warum kann ich Cluster nach dem Onboarding nicht aktualisieren?

Wenn Sie nach der Aktivierung von Azure Monitor für Container für einen AKS-Cluster den Log Analytics-Arbeitsbereich löschen, an den der Cluster seine Daten sendete, tritt beim Versuch, den Cluster zu aktualisieren, ein Fehler auf. Um dieses Problem zu umgehen, müssen Sie die Überwachung deaktivieren und anschließend mit Verweis auf einen anderen gültigen Arbeitsbereich in Ihrem Abonnement neu aktivieren. Wenn Sie versuchen, das Clusterupgrade erneut auszuführen, sollte es erfolgreich durchgeführt und abgeschlossen werden.  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Welche Ports und Domänen muss ich für den Agent öffnen bzw. auf die Whitelist setzen?
- *.ods.opinsights.azure.com   443
- *.oms.opinsights.azure.com   443
- *.blob.core.windows.net      443
- dc.services.visualstudio.com 443

## <a name="next-steps"></a>Nächste Schritte

Um in die Überwachung Ihrer AKS-Cluster einzusteigen, lesen Sie [Onboarding von Azure Monitor für Container](container-insights-onboard.md), um ein Verständnis für die Anforderungen und verfügbaren Methoden zum Aktivieren von Überwachung zu entwickeln. 
