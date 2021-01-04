---
title: Schemas für Azure Security Center-Warnungen
description: In diesem Artikel werden die verschiedenen Schemas beschrieben, die von Azure Security Center für Sicherheitswarnungen verwendet werden.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2020
ms.author: memildin
ms.openlocfilehash: f9b3be69ab57c0abf7523169303def899f325229
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789215"
---
# <a name="security-alerts-schemas"></a>Schemas für Sicherheitswarnungen

Wenn für Ihr Abonnement Azure Defender aktiviert ist, erhalten Sie Sicherheitswarnungen, wenn Security Center Bedrohungen für Ressourcen erkennt.

Sie können diese Sicherheitswarnungen auf der Seite **Bedrohungsschutz** von Azure Security Center oder mithilfe externer Tools anzeigen, wie z. B.:

- [Azure Sentinel](../sentinel/index.yml): die cloudnative SIEM-Lösung von Microsoft. Der Sentinel-Connector erhält Warnungen von Azure Security Center und sendet diese an den [Log Analytics-Arbeitsbereich](../azure-monitor/learn/quick-create-workspace.md) für Azure Sentinel.
- SIEMs von Drittanbietern: Senden von Daten an [Azure Event Hubs](../event-hubs/index.yml). Integrieren Sie anschließend Ihre Event Hub-Daten in eine SIEM-Lösung eines Drittanbieters. Weitere Informationen finden Sie unter [Streamen von Warnungen in eine SIEM-, SOAR- oder IT-Service-Management-Lösung](export-to-siem.md).
- [Die REST-API](/rest/api/securitycenter/): Wenn Sie die REST-API für den Zugriff auf Warnungen verwenden, finden Sie weitere Informationen in der [Online-API-Dokumentation zu Warnungen](/rest/api/securitycenter/alerts).

Wenn Sie programmgesteuerte Methoden verwenden, um die Warnungen zu verarbeiten, benötigen Sie das richtige Schema, um die für Sie relevanten Felder zu finden. Wenn Sie in einen Event Hub exportieren oder die Workflowautomatisierung mit generischen HTTP-Connectors auslösen möchten, verwenden Sie die Schemas, um die JSON-Objekte ordnungsgemäß zu analysieren.

>[!IMPORTANT]
> Das Schema ist für jedes dieser Szenarien etwas anders. Stellen Sie daher sicher, dass Sie die entsprechende Registerkarte auswählen.


## <a name="the-schemas"></a>Die Schemas 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[Workflowautomatisierung und fortlaufender Export in Event Hub](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>JSON-Beispiel für Warnungen, die an Logic Apps, Event Hub und SIEM-Lösungen von Drittanbietern gesendet werden

Im Folgenden finden Sie das Schema der Warnungsereignisse, die übergeben werden an:

- Azure Logic App-Instanzen, die in der Workflowautomatisierung von Security Center konfiguriert wurden
- Azure Event Hub mithilfe des Security Center-Features für den fortlaufenden Export

Weitere Informationen zum Feature Workflowautomatisierung finden Sie unter [Automatisieren von Reaktionen auf Security Center-Trigger](workflow-automation.md).

Weitere Informationen zum fortlaufenden Export finden Sie unter [Fortlaufender Export von Security Center-Daten](continuous-export.md).

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Azure Sentinel und Log Analytics-Arbeitsbereiche](#tab/schema-sentinel)

Der Sentinel-Connector erhält Warnungen von Azure Security Center und sendet diese an den Log Analytics-Arbeitsbereich für Azure Sentinel. 

Zum Erstellen eines Sentinel-Falls oder -Vorfalls mithilfe von Security Center-Warnungen benötigen Sie das Schema für die unten angezeigten Warnungen. 

Weitere Informationen finden Sie in der [Dokumentation zu Azure Sentinel](../sentinel/index.yml).

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Azure-Aktivitätsprotokoll](#tab/schema-activitylog)

Azure Security Center überwacht generierte Sicherheitswarnungen als Ereignisse im Azure-Aktivitätsprotokoll.

Sie können die Sicherheitswarnungen im Aktivitätsprotokoll anzeigen, indem Sie folgendermaßen nach dem Ereignis „Activate Alert“ suchen:

[![Durchsuchen des Aktivitätsprotokolls nach dem Ereignis „Activate Alert“](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>JSON-Beispiel für Warnungen, die an das Azure-Aktivitätsprotokoll gesendet werden

```json
{
    "channels": "Operation",
    "correlationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "description": "PREVIEW - Role binding to the cluster-admin role detected. Kubernetes audit log analysis detected a new binding to the cluster-admin role which gives administrator privileges.\r\nUnnecessary administrator privileges might cause privilege escalation in the cluster.",
    "eventDataId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "eventName": {
        "value": "PREVIEW - Role binding to the cluster-admin role detected",
        "localizedValue": "PREVIEW - Role binding to the cluster-admin role detected"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2019-12-25T18:52:36.801035Z",
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/events/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/ticks/637128967568010350",
    "level": "Informational",
    "operationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Activate Alert"
    },
    "resourceGroupName": "RESOURCE_GROUP_NAME",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-12-25T19:14:03.5507487Z",
    "subscriptionId": "SUBSCRIPTION_ID",
    "properties": {
        "clusterRoleBindingName": "cluster-admin-binding",
        "subjectName": "for-binding-test",
        "subjectKind": "ServiceAccount",
        "username": "masterclient",
        "actionTaken": "Detected",
        "resourceType": "Kubernetes Service",
        "severity": "Low",
        "intent": "[\"Persistence\"]",
        "compromisedEntity": "ASC-IGNITE-DEMO",
        "remediationSteps": "[\"Review the user in the alert details. If cluster-admin is unnecessary for this user, consider granting lower privileges to the user.\"]",
        "attackedResourceType": "Kubernetes Service"
    },
    "relatedEvents": []
}
```

### <a name="the-data-model-of-the-schema"></a>Das Datenmodell des Schemas

|Feld|BESCHREIBUNG|
|----|----|
|**channels**|Konstant („Operation“)|
|**correlationId**|Die Azure Security Center-Warnungs-ID|
|**description**|Beschreibung der Warnung|
|**eventDataId**|Siehe correlationId|
|**eventName**|Die Unterfelder „value“ und „localizedValue“ enthalten den Anzeigenamen der Warnung.|
|**category**|Die Unterfelder „value“ und „localizedValue“ sind konstant („Security“).|
|**eventTimestamp**|UTC-Zeitstempel, wann die Warnung generiert wurde|
|**id**|Die vollqualifizierte Warnungs-ID|
|**level**|Konstant („Informational“)|
|**operationId**|Siehe „correlationId“|
|**operationName**|Das Feld „value“ ist konstant („Microsoft.Security/locations/alerts/activate/action“), und „localized value“ ist „Activate Alert“ (wird möglicherweise dem Gebietsschema des Benutzers entsprechend lokalisiert).|
|**resourceGroupName**|Enthält den Namen der Ressourcengruppe.|
|**resourceProviderName**|Die Unterfelder „value“ und „localizedValue“ sind konstant („Microsoft.Security“).|
|**resourceType**|Die Unterfelder „value“ und „localizedValue“ sind konstant („Microsoft.Security/locations/alerts“).|
|**Ressourcen-ID**|Die vollqualifizierte Azure-Ressourcen-ID|
|**status**|Die Unterfelder „value“ und „localizedValue“ sind konstant („Active“).|
|**subStatus**|Die Unterfelder „value“ und „localizedValue“ sind leer.|
|**submissionTimestamp**|Der UTC-Zeitstempel der Ereignisübermittlung an das Aktivitätsprotokoll|
|**subscriptionId**|Die Abonnement-ID der kompromittierten Ressource|
|**properties**|Ein JSON-Behälter mit zusätzlichen Eigenschaften, die sich auf die Warnung beziehen. Diese können sich je nach Warnung ändern. Die folgenden Felder werden jedoch in allen Warnungen angezeigt:<br>- severity: Der Schweregrad des Angriffs<br>- compromisedEntity: Der Name der kompromittierten Ressource<br>- remediationSteps: Array von Problembehandlungsschritten, die durchgeführt werden müssen<br>- intent: Absicht des Angriffs. Mögliche Absichten finden Sie in der Tabelle mit den [Absichten](alerts-reference.md#intentions).|
|**relatedEvents**|Konstant, leeres Array|
|||





### <a name="ms-graph-api"></a>[MS Graph-API](#tab/schema-graphapi)

Microsoft Graph ist das Gateway zu Daten und Intelligence in Microsoft 365. Microsoft Graph bietet ein einheitliches Programmierbarkeitsmodell, mit dem Sie auf die enormen Datenmengen in Microsoft 365, Windows 10 und Enterprise Mobility + Security zugreifen können. Verwenden Sie die Vielzahl von Daten in Microsoft Graph, um Apps für Organisationen und Consumer zu erstellen, die mit Millionen von Benutzern interagieren.

Das Schema und eine JSON-Darstellung für Sicherheitswarnungen, die an MS Graph gesendet werden, sind in der [Microsoft Graph-Dokumentation](/graph/api/resources/alert?preserve-view=true&view=graph-rest-1.0) verfügbar.

---


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden die Schemas beschrieben, die von den Tools zum Bedrohungsschutz von Azure Security Center beim Senden von Informationen zu Sicherheitswarnungen verwendet werden.

Weitere Informationen zu den Möglichkeiten, wie Sie auf Sicherheitswarnungen außerhalb von Security Center zugreifen können, finden Sie auf den folgenden Seiten:

- [Azure Sentinel](../sentinel/index.yml): die cloudnative SIEM-Lösung von Microsoft
- [Azure Event Hubs](../event-hubs/index.yml): der vollständig verwaltete Microsoft-Dienst für die Datenerfassung in Echtzeit
- [Fortlaufendes Exportieren von Security Center-Daten](continuous-export.md)
- [Log Analytics-Arbeitsbereiche](../azure-monitor/learn/quick-create-workspace.md): Azure Monitor speichert die Protokolldaten in einem Log Analytics-Arbeitsbereich, einem Container, der Daten und Konfigurationsinformationen enthält.