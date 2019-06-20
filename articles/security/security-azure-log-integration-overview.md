---
title: Integrieren von Protokollen aus Azure-Ressourcen in Ihre SIEM-Systeme | Microsoft-Dokumentation
description: Informationen zu Azure Log Integration, zu den wichtigsten Funktionen und zur Funktionsweise dieses Diensts.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/28/2019
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 65e256b476c1e459ae937d9f6cbb43e0020fd9fe
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298130"
---
# <a name="introduction-to-azure-log-integration"></a>Einführung in Azure Log Integration

>[!IMPORTANT]
> Das Feature „Azure Log Integration“ wird ab dem 15.06.2019 als veraltet markiert. AzLog-Downloads wurden am 27. Juni 2018 deaktiviert. Um Unterstützung bei der künftigen Vorgehensweise zu erhalten, lesen Sie den Beitrag [Use Azure Monitor to integrate with SIEM tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) (Verwenden von Azure Monitor für die Integration mit SIEM-Tools). 

Azure Log Integration wurde zur Verfügung gestellt, um die Integration von Azure-Protokollen in Ihr lokales SIEM-System (Security Information and Event Management) zu vereinfachen.

 Die empfohlene Methode für die Integration von Azure-Protokollen ist die Verwendung der Connectors Ihres SIEM-Herstellers. Azure Monitor bietet die Möglichkeit, die Protokolle in Event Hubs zu streamen, und SIEM-Hersteller können Connectors schreiben, um Protokolle vom Event Hub in das SIEM-System zu integrieren.  Wenn Sie erfahren möchten, wie dies funktioniert, befolgen Sie die Anweisungen unter [Streamen von Überwachungsdaten an Event Hubs](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). In diesem Artikel werden auch die SIEM-Systeme aufgelistet, für die direkte Azure-Connectors bereits verfügbar sind.  

> [!IMPORTANT]
> Wenn Sie hauptsächlich Protokolle für virtuelle Computer erfassen möchten, wird dies in den Lösungen der meisten SIEM-Anbieter abgedeckt. Das Verwenden des Connectors des SIEM-Anbieters ist immer die bevorzugte Alternative.

Die Dokumentation zum Azure Log Integration-Feature wird weiterhin gepflegt, bis das Feature als veraltet markiert wird.

Lesen Sie weitere Artikel, um mehr über das Azure Log Integration-Feature zu erfahren:

Azure Log Integration erfasst Windows-Ereignisse aus Protokollen der Windows-Ereignisanzeige, aus [Azure-Aktivitätsprotokollen](../azure-monitor/platform/activity-logs-overview.md), aus [Azure Security Center-Warnungen](../security-center/security-center-intro.md) und aus [Azure-Diagnoseprotokollen](../azure-monitor/platform/diagnostic-logs-overview.md) von Azure-Ressourcen. Durch die Integration kann in Ihrer SIEM-Lösung ein einheitliches Dashboard für alle Ihre Objekte – lokal oder in der Cloud – bereitgestellt werden. In einem Dashboard können Sie Warnungen für Sicherheitsereignisse empfangen, aggregieren, korrelieren und analysieren.

> [!NOTE]
> Derzeit unterstützt Azure Log Integration nur Azure Commercial Clouds und Azure Government Clouds. Andere Clouds werden nicht unterstützt.

![Der Azure Log Integration-Prozess][1]

## <a name="what-logs-can-i-integrate"></a>Welche Protokolle kann ich integrieren?

Azure führt für jeden Azure-Dienst eine umfassende Protokollierung durch. Die Protokolle lassen sich in drei Protokolltypen unterteilen:

* **Steuerungs-/Verwaltungsprotokolle**: Ermöglichen Einblicke in CREATE-, UPDATE- und DELETE-Vorgänge in [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Ein Beispiel für diesen Protokolltyp sind Azure-Aktivitätsprotokolle.
* **Datenebenenprotokolle:** Ermöglichen Einblicke in die Ereignisse, die bei Verwendung einer Azure-Ressource ausgelöst werden. Ein Beispiel für diesen Protokolltyp sind die Kanäle **System**, **Sicherheit** und **Anwendung** der Windows-Ereignisanzeige auf einem virtuellen Windows-Computer. Ein weiteres Beispiel ist die Azure-Diagnoseprotokollierung, die über Azure Monitor konfiguriert wird.
* **Verarbeitete Ereignisse:** Bieten analysierte Ereignis- und Warnungsinformationen, die für Sie verarbeitet werden. Ein Beispiel für diesen Ereignistyp sind Azure Security Center-Warnungen. Azure Security Center verarbeitet und analysiert Ihr Abonnement und stellt dann Warnungen bereit, die für den aktuellen Sicherheitsstatus relevant sind.

Die Azure-Protokollintegration unterstützt ArcSight, QRadar und Splunk. Erkundigen Sie sich bei Ihrem SIEM-Anbieter nach einem nativen Connector. Verwenden Sie Azure Log Integration nicht, wenn ein nativer Connector verfügbar ist.

Wenn keine anderen Optionen zur Verfügung stehen, kann Azure Log Integration in Betracht gezogen werden. Die folgende Tabelle enthält unsere Empfehlungen:

|SIEM | Kunde verwendet bereits den Azure Log Integrator | Kunde prüft die SIEM-Integrationsoptionen|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | Migration zum [Azure Monitor-Add-On für Splunk](https://splunkbase.splunk.com/app/3534/) starten | [Splunk-Connector](https://splunkbase.splunk.com/app/3534/) verwenden |
|**QRadar** | Zum QRadar-Connector, der im letzten Abschnitt von [Streamen von Azure-Überwachungsdaten an einen Event Hub für die Verwendung durch ein externes Tool](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) dokumentiert ist, migrieren oder diesen verwenden. | Den QRadar-Connector, der im letzten Abschnitt von [Streamen von Azure-Überwachungsdaten an einen Event Hub für die Verwendung durch ein externes Tool](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) dokumentiert ist, verwenden. |
|**ArcSight** | Weiterhin den Azure Log Integrator verwenden, bis ein Connector verfügbar ist, und dann zur connectorbasierten Lösung migrieren.  | Ziehen Sie die Verwendung von Azure Monitor-Protokollen als Alternative in Betracht. Führen Sie das Onboarding für Azure Log Integration nur aus, wenn Sie bereit sind, die Migration durchzuführen, nachdem der Connector verfügbar ist. |

> [!NOTE]
> Obwohl Azure Log Integration eine kostenlose Lösung ist, entstehen durch Speichern der Protokolldateiinformationen entsprechende Azure-Speicherkosten.

Wenn Sie Unterstützung benötigen, können Sie eine [Supportanfrage](../azure-supportability/how-to-create-azure-support-request.md) erstellen. Wählen Sie für diesen Dienst die Option **Protokollintegration** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wird Azure Log Integration vorgestellt. Weitere Informationen zu Azure Log Integration und den unterstützten Protokolltypen finden Sie in folgenden Artikeln:

* [Erste Schritte mit Azure Log Integration](security-azure-log-integration-get-started.md). In diesem Tutorial wird die Installation von Azure Log Integration Schritt für Schritt erläutert. Zudem wird beschrieben, wie Protokolle aus dem Microsoft Azure-Diagnosespeicher (WAD-Speicher) sowie Azure-Aktivitätsprotokolle, Azure Security Center-Warnungen und Azure Active Directory-Überwachungsprotokolle integriert werden.
* [Azure Log Integration – häufig gestellte Fragen](security-azure-log-integration-faq.md). Hier finden Sie Antworten auf häufig gestellte Fragen zu Azure Log Integration.
* Erhalten Sie weitere Informationen zum [Streamen von Azure-Überwachungsdaten an einen Event Hub für die Verwendung durch ein externes Tool](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
