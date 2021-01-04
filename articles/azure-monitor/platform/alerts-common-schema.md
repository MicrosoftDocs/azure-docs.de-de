---
title: Allgemeines Warnungsschema für Azure Monitor-Warnungen
description: Enthält grundlegende Informationen zum allgemeinen Warnungsschema, zu den Vorteilen der Nutzung und zur Vorgehensweise bei der Aktivierung.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 03/14/2019
ms.openlocfilehash: aa619976c8fa03b925d66e884ad03fc4e385693e
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565601"
---
# <a name="common-alert-schema"></a>Allgemeines Warnungsschema

In diesem Artikel wird beschrieben, was das allgemeine Warnungsschema ist, welche Vorteile sich durch die Nutzung ergeben und wie Sie die Aktivierung durchführen.

## <a name="what-is-the-common-alert-schema"></a>Was ist das allgemeine Warnungsschema?

Mit dem allgemeinen Warnungsschema wird die Benutzeroberfläche für Warnungsbenachrichtigungen in Azure standardisiert. Bisher wurden für die Warnungstypen von Azure (Metrik, Protokoll und Aktivitätsprotokoll) eigene E-Mail-Vorlagen, Webhookschemas usw. verwendet. Mit dem allgemeinen Warnungsschema können Sie Warnungsbenachrichtigungen nun basierend auf einem einheitlichen Schema empfangen.

Mit jeder Warnungsinstanz werden **die betroffene Ressource** und **die Ursache der Warnung** beschrieben. Diese Instanzen werden im allgemeinen Schema in den folgenden Abschnitten beschrieben:
* **Zusammenfassung**: Eine Gruppe **standardisierter Felder** (für alle Warnungstypen gleich), die beschreiben, auf **welcher Ressource** sich die Warnung befindet, sowie zusätzliche allgemeine Warnungsmetadaten (z. B. Schweregrad oder Beschreibung). 
* **Warnungskontext**: Eine Gruppe von Feldern, mit denen die **Ursache der Warnung** beschrieben wird. Die Felder variieren **basierend auf dem Warnungstyp**. Eine Metrikwarnung enthält im Warnungskontext beispielsweise Felder wie den Metriknamen und -wert, während eine Aktivitätsprotokollwarnung Informationen zum Ereignis enthält, von dem die Warnung generiert wurde. 

Die typischen Integrationsszenarien, über die uns von Kunden berichtet wird, umfassen das Routing der Warnungsinstanz an das entsprechende Team anhand eines Verbindungselements (z. B. eine Ressourcengruppe) und die anschließende Bearbeitung durch das zuständige Team. Mit dem allgemeinen Warnungsschema können Sie über Warnungstypen hinweg eine standardisierte Routinglogik verwenden, indem Sie die wichtigen Felder nutzen und die Kontextfelder unverändert lassen, damit diese später von den jeweiligen Teams untersucht werden können.

Dies bedeutet, dass Sie unter Umständen über eine geringere Zahl von Integrationen verfügen, sodass die Verwaltung und Wartung _deutlich_ vereinfacht wird. Darüber hinaus werden zukünftige Warnungsnutzlast-Anreicherungen (z. B. Anpassung, Diagnoseanreicherung usw.) nur im allgemeinen Schema zu finden sein.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Welche Verbesserungen bewirkt das allgemeine Warnungsschema?

Das allgemeine Warnungsschema manifestiert sich hauptsächlich in Ihren Warnungsbenachrichtigungen. Hier sind die vorhandenen Verbesserungen aufgeführt:

| Aktion | Erweiterungen|
|:---|:---|
| sms | Eine einheitliche SMS-Vorlage für alle Warnungstypen. |
| Email | Eine einheitliche und detaillierte E-Mail-Vorlage, mit der Sie Probleme leicht auf einen Blick diagnostizieren können. Mit eingebetteten Deep-Links für die Warnungsinstanz im Portal und die betroffene Ressource wird sichergestellt, dass Sie schnell mit dem Lösungsprozess beginnen können. |
| Webhook/Logik-App/Azure-Funktion/Automation-Runbook | Eine einheitliche JSON-Struktur für alle Warnungstypen, um Ihnen das einfache Entwickeln von Integrationen für die unterschiedlichen Warnungstypen zu ermöglichen. |

Mit dem neuen Schema wird in naher Zukunft auch eine umfassendere Oberfläche für die Nutzung von Warnungen bereitgestellt – sowohl im Azure-Portal als auch in der mobilen Azure-App. 

[Erfahren Sie mehr zu den Schemadefinitionen für Webhooks/Logik-Apps/Azure Functions/Automation-Runbooks.](./alerts-common-schema-definitions.md)

> [!NOTE]
> Für die folgenden Aktionen wird das allgemeine Warnungsschema nicht unterstützt: ITSM-Connector.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Wie kann ich das allgemeine Warnungsschema aktivieren?

Sie können sowohl im Portal als auch in der REST-API über Aktionsgruppen eine Entscheidung für oder gegen das allgemeine Warnungsschema treffen. Der Umschalter für den Wechsel zum neuen Schema befindet sich auf der Aktionsebene. Sie müssen die Entscheidung für eine E-Mail-Aktion und eine Webhookaktion also beispielsweise separat treffen.

> [!NOTE]
> 1. Für die folgenden Warnungstypen wird das allgemeine Schema standardmäßig unterstützt (keine Aktivierung erforderlich):
>     * Warnungen der intelligenten Erkennung
> 1. Von den folgenden Warnungstypen wird das allgemeine Schema derzeit nicht unterstützt:
>     * Von [Azure Monitor für VMs](../insights/vminsights-overview.md) generierte Warnungen
>     * Von [Azure Cost Management](../../cost-management-billing/manage/cost-management-budget-scenario.md) generierte Warnungen

### <a name="through-the-azure-portal"></a>Über das Azure-Portal

![Allgemeines Warnungsschema: Aktivierung](media/alerts-common-schema/portal-opt-in.png)

1. Öffnen Sie in einer Aktionsgruppe eine vorhandene oder eine neue Aktion. 
1. Wählen Sie für den Umschalter die Option „Ja“, um das allgemeine Warnungsschema wie gezeigt zu aktivieren.

### <a name="through-the-action-groups-rest-api"></a>Über die Aktionsgruppen-REST-API

Sie können auch die [Aktionsgruppen-API](/rest/api/monitor/actiongroups) verwenden, um das allgemeine Warnungsschema zu aktivieren. Beim Durchführen des REST-API-Aufrufs zum [Erstellen oder Aktualisieren](/rest/api/monitor/actiongroups/createorupdate) können Sie das Flag „useCommonAlertSchema“ für die folgenden Aktionen auf „true“ (Aktivierung) oder „false“ (keine Aktivierung) festlegen: E-Mail, Webhook, Logik-App, Azure-Funktion oder Automation-Runbook.

Mit dem folgenden Anforderungstext für den REST-API-Vorgang zum [Erstellen oder Aktualisieren](/rest/api/monitor/actiongroups/createorupdate) wird beispielsweise Folgendes durchgeführt:

* Aktivieren des allgemeinen Warnungsschemas für die E-Mail-Aktion „John Doe's email“
* Deaktivieren des allgemeinen Warnungsschemas für die E-Mail-Aktion „Jane Smith's email“
* Deaktivieren des allgemeinen Warnungsschemas für die Webhookaktion „Sample webhook“

```json
{
  "properties": {
    "groupShortName": "sample",
    "enabled": true,
    "emailReceivers": [
      {
        "name": "John Doe's email",
        "emailAddress": "johndoe@email.com",
        "useCommonAlertSchema": true
      },
      {
        "name": "Jane Smith's email",
        "emailAddress": "janesmith@email.com",
        "useCommonAlertSchema": false
      }
    ],
    "smsReceivers": [
      {
        "name": "John Doe's mobile",
        "countryCode": "1",
        "phoneNumber": "1234567890"
      },
      {
        "name": "Jane Smith's mobile",
        "countryCode": "1",
        "phoneNumber": "0987654321"
      }
    ],
    "webhookReceivers": [
      {
        "name": "Sample webhook",
        "serviceUri": "http://www.example.com/webhook",
        "useCommonAlertSchema": true
      }
    ]
  },
  "location": "Global",
  "tags": {}
}
```





## <a name="next-steps"></a>Nächste Schritte

- [Definitionen des allgemeinen Warnungsschemas für Webhooks/Logic Apps/Azure Functions/Automation Runbooks](./alerts-common-schema-definitions.md)
- [Erfahren Sie, wie Sie eine Logik-App erstellen, die das allgemeine Warnungsschema nutzt, um all Ihre Warnungen zu verarbeiten.](./alerts-common-schema-integrations.md)
