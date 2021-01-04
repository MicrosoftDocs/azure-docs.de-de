---
title: Erläuterungen zu dem in Aktivitätsprotokollwarnungen verwendeten Webhookschema
description: Lernen Sie das Schema des JSON-Codes kennen, der beim Aktivieren einer Aktivitätsprotokollwarnung an einen Webhook-URL gesendet wird.
ms.topic: conceptual
ms.date: 03/31/2017
ms.subservice: alerts
ms.openlocfilehash: 026613c3f5710137fb110153b34f9ed74bbf8a7b
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522786"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Webhooks für Azure-Aktivitätsprotokollwarnungen
Als Teil der Definition einer Aktionsgruppe können Sie Webhookendpunkte für den Empfang von Aktivitätsprotokollwarnungs-Benachrichtigungen konfigurieren. Mithilfe von Webhooks können Sie diese Benachrichtigung zur Nachbearbeitung oder Ausführung benutzerdefinierter Aktionen an andere Systeme weiterleiten. In diesem Artikel erfahren Sie, wie die Nutzlast für die HTTP POST-Methode für einen Webhook aussieht.

Weitere Informationen zu Aktivitätsprotokollwarnungen finden Sie unter [Erstellen von Aktivitätsprotokollwarnungen](activity-log-alerts.md).

Weitere Informationen zu Aktionsgruppen, finden Sie unter [Erstellen und Verwalten von Aktionsgruppen im Azure-Portal](./action-groups.md).

> [!NOTE]
> Für Ihre Webhook-Integrationen können Sie auch das [allgemeine Warnungsschema](./alerts-common-schema.md) verwenden, das den Vorteil einer einzelnen erweiterbaren und einheitlichen Warnungsnutzlast für alle Benachrichtigungsdienste in Azure Monitor bietet. [Hier finden Sie Informationen zu den Definitionen des allgemeinen Warnungsschemas](./alerts-common-schema-definitions.md).


## <a name="authenticate-the-webhook"></a>Authentifizieren des Webhooks
Der Webhook kann optional tokenbasierte Autorisierung zur Authentifizierung verwenden. Der Webhook-URI wird mit einer Token-ID gespeichert, z.B. `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`.

## <a name="payload-schema"></a>Nutzlast und Schema
Die JSON-Nutzlast des POST-Vorgangs variiert in Abhängigkeit vom Feld „data.context.activityLog.eventSource“ der Nutzlast.

### <a name="common"></a>Allgemein

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```

### <a name="administrative"></a>Administrative

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}
```

### <a name="security"></a>Sicherheit

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{"status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "correlationId":"2518408115673929999",
                "description":"Failed SSH brute force attack. Failed brute force attacks were detected from the following attackers: [\"IP Address: 01.02.03.04\"].  Attackers were trying to access the host with the following user names: [\"root\"].",
                "eventSource":"Security",
                "eventTimestamp":"2017-06-25T19:00:32.607+00:00",
                "eventDataId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "level":"Informational",
                "operationName":"Microsoft.Security/locations/alerts/activate/action",
                "operationId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "properties":{
                    "attackers":"[\"IP Address: 01.02.03.04\"]",
                    "numberOfFailedAuthenticationAttemptsToHost":"456",
                    "accountsUsedOnFailedSignInToHostAttempts":"[\"root\"]",
                    "wasSSHSessionInitiated":"No","endTimeUTC":"06/25/2017 19:59:39",
                    "actionTaken":"Detected",
                    "resourceType":"Virtual Machine",
                    "severity":"Medium",
                    "compromisedEntity":"LinuxVM1",
                    "remediationSteps":"[In case this is an Azure virtual machine, add the source IP to NSG block list for 24 hours (see https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)]",
                    "attackedResourceType":"Virtual Machine"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/Microsoft.Security/locations/centralus/alerts/Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "resourceGroupName":"contoso",
                "resourceProviderName":"Microsoft.Security",
                "status":"Active",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-25T20:23:04.9743772+00:00",
                "resourceType":"MICROSOFT.SECURITY/LOCATIONS/ALERTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="recommendation"></a>Empfehlung

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{
        "status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
                "caller":"Microsoft.Advisor",
                "correlationId":"123b4c54-11bb-3d65-89f1-0678da7891bd",
                "description":"A new recommendation is available.",
                "eventSource":"Recommendation",
                "eventTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
                "eventDataId":"1bf234ef-e45f-4567-8bba-fb9b0ee1dbcb",
                "level":"Informational",
                "operationName":"Microsoft.Advisor/recommendations/available/action",
                "properties":{
                    "recommendationSchemaVersion":"1.0",
                    "recommendationCategory":"HighAvailability",
                    "recommendationImpact":"Medium",
                    "recommendationName":"Enable Soft Delete to protect your blob data",
                    "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/recommendationTypeId/12dbf883-5e4b-4f56-7da8-123b45c4b6e6",
                    "recommendationType":"12dbf883-5e4b-4f56-7da8-123b45c4b6e6"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/microsoft.storage/storageaccounts/contosoStore",
                "resourceGroupName":"CONTOSO",
                "resourceProviderName":"MICROSOFT.STORAGE",
                "status":"Active",
                "subStatus":"",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "resourceType":"MICROSOFT.STORAGE/STORAGEACCOUNTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="servicehealth"></a>ServiceHealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
            "channels": "Admin",
            "correlationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "description": "Active: Virtual Machines - Australia East",
            "eventSource": "ServiceHealth",
            "eventTimestamp": "2017-10-18T23:49:25.3736084+00:00",
            "eventDataId": "6fa98c0f-334a-b066-1934-1a4b3d929856",
            "level": "Informational",
            "operationName": "Microsoft.ServiceHealth/incident/action",
            "operationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "properties": {
                "title": "Virtual Machines - Australia East",
                "service": "Virtual Machines",
                "region": "Australia East",
                "communication": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "incidentType": "Incident",
                "trackingId": "0NIH-U2O",
                "impactStartTime": "2017-10-18T02:48:00.0000000Z",
                "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"}],\"ServiceName\":\"Virtual Machines\"}]",
                "defaultLanguageTitle": "Virtual Machines - Australia East",
                "defaultLanguageContent": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "stage": "Active",
                "communicationId": "636439673646212912",
                "version": "0.1.1"
            },
            "status": "Active",
            "subscriptionId": "45529734-0ed9-4895-a0df-44b59a5a07f9",
            "submissionTimestamp": "2017-10-18T23:49:28.7864349+00:00"
        }
    },
    "properties": {}
    }
}
```

Spezifische Schemainformationen zu Dienstintegritätsbenachrichtigungs-Aktivitätsprotokollwarnungen finden Sie unter [Dienstintegritätsbenachrichtigungen](../../service-health/service-notifications.md). Erfahren Sie außerdem, wie Sie [Webhookbenachrichtigungen zur Dienstintegrität mit Ihren vorhandenen Problemverwaltungslösungen konfigurieren](../../service-health/service-health-alert-webhook-guide.md).

### <a name="resourcehealth"></a>ResourceHealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Admin, Operation",
                "correlationId": "a1be61fd-37ur-ba05-b827-cb874708babf",
                "eventSource": "ResourceHealth",
                "eventTimestamp": "2018-09-04T23:09:03.343+00:00",
                "eventDataId": "2b37e2d0-7bda-4de7-ur8c6-1447d02265b2",
                "level": "Informational",
                "operationName": "Microsoft.Resourcehealth/healthevent/Activated/action",
                "operationId": "2b37e2d0-7bda-489f-81c6-1447d02265b2",
                "properties": {
                    "title": "Virtual Machine health status changed to unavailable",
                    "details": "Virtual machine has experienced an unexpected event",
                    "currentHealthStatus": "Unavailable",
                    "previousHealthStatus": "Available",
                    "type": "Downtime",
                    "cause": "PlatformInitiated"
                },
                "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
                "resourceGroupName": "<resource group>",
                "resourceProviderName": "Microsoft.Resourcehealth/healthevent/action",
                "status": "Active",
                "subscriptionId": "<subscription Id>",
                "submissionTimestamp": "2018-09-04T23:11:06.1607287+00:00",
                "resourceType": "Microsoft.Compute/virtualMachines"
            }
        }
    }
}
```

| Elementname | BESCHREIBUNG |
| --- | --- |
| status |Wird für Metrikwarnungen verwendet. Muss für Aktivitätsprotokollwarnungen immer auf „aktiviert“ festgelegt werden. |
| context |Der Kontext des Ereignisses. |
| resourceProviderName |Der Ressourcenanbieter der betroffenen Ressource. |
| conditionType |Muss immer „Event“ lauten. |
| name |Der Name der Warnungsregel. |
| id |Die Ressourcen-ID der Warnung. |
| description |Warnungsbeschreibung, die beim Erstellen der Warnung festgelegt wird. |
| subscriptionId |Die Azure-Abonnement-ID. |
| timestamp |Die Zeit, zu der das Ereignis durch den Azure-Dienst generiert wurde, der die Anforderung verarbeitet hat. |
| resourceId |Ressourcen-ID der betroffenen Ressource. |
| resourceGroupName |Name der Ressourcengruppe für die betroffene Ressource. |
| properties |Eine Gruppe von `<Key, Value>`-Paaren (`Dictionary<String, String>`) mit Details zum Ereignis |
| Ereignis |Element, das Metadaten zum Ereignis enthält. |
| authorization |Die Eigenschaften der rollenbasierten Zugriffssteuerung in Azure für das Ereignis. Zu diesen Eigenschaften zählen üblicherweise Aktion, Rolle und Bereich. |
| category |Kategorie des Ereignisses. Unterstützte Werte: „Administration“, „Warnung“, „Sicherheit“, „Dienstintegrität“ und „Empfehlung“. |
| caller |Die E-Mail-Adresse des Benutzers, der den Vorgang, UPN-Anspruch oder SPN-Anspruch ausgeführt hat (sofern verfügbar). Kann für bestimmte Systemaufrufe NULL sein. |
| correlationId |Üblicherweise eine GUID in Zeichenfolgenformat. Ereignisse mit „correlationId“ gehören zur gleichen übergeordneten Aktion und besitzen üblicherweise den gleichen correlationId-Wert. |
| eventDescription |Statische Beschreibung des Ereignisses. |
| eventDataId |Eindeutiger Bezeichner für das Ereignis. |
| eventSource |Der Name des Azure-Diensts oder der Infrastruktur, der bzw. die das Ereignis generiert hat. |
| httpRequest |Die Anforderung umfasst üblicherweise clientRequestId, clientIpAddress und HTTP-Methode (z.B. PUT). |
| level |Einer der folgenden Werte: Critical, Error, Warning und Informational. |
| operationId |Üblicherweise eine gemeinsame GUID für alle Ereignisse des gleichen Vorgangs. |
| operationName |Name des Vorgangs. |
| properties |Die Eigenschaften des Ereignisses. |
| status |Eine Zeichenfolge. Der Status des Vorgangs. Gängige Werte: „Gestartet“, „In Bearbeitung“, „Erfolgreich“, „Fehler“, „Aktiv“ und „Aufgelöst“. |
| subStatus |Enthält üblicherweise den HTTP-Statuscode des zugehörigen REST-Aufrufs. Es können auch weitere Zeichenfolgen enthalten sein, die einen Unterstatus beschreiben. Gängige Unterstatuswerte: OK (HTTP-Statuscode: 200), Erstellt (HTTP-Statuscode: 201), Akzeptiert (HTTP-Statuscode: 202), Kein Inhalt (HTTP-Statuscode: 204), Ungültige Anforderung (HTTP-Statuscode: 400), Nicht gefunden (HTTP-Statuscode: 404), Konflikt (HTTP-Statuscode: 409), Interner Serverfehler (HTTP-Statuscode: 500), Dienst nicht verfügbar (HTTP-Statuscode: 503) und Gatewaytimeout (HTTP-Statuscode: 504). |

Spezifische Schemainformationen zu allen anderen Aktivitätsprotokollwarnungen finden Sie unter [Überwachen der Abonnementaktivität per Azure-Aktivitätsprotokoll](./platform-logs-overview.md).

## <a name="next-steps"></a>Nächste Schritte
* [Weitere Informationen zum Aktivitätsprotokoll](./platform-logs-overview.md).
* [Ausführen von Azure Automation-Skripts (Runbooks) für Azure-Warnungen](https://go.microsoft.com/fwlink/?LinkId=627081).
* [Senden einer SMS über Twilio auf der Grundlage einer Azure-Warnung mithilfe einer Logik-App](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Dieses Beispiel ist eigentlich für Metrikwarnungen konzipiert, kann jedoch für Aktivitätsprotokollwarnungen angepasst werden.
* [Senden einer Slack-Nachricht auf der Grundlage einer Azure-Warnung mithilfe einer Logik-App](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Dieses Beispiel ist eigentlich für Metrikwarnungen konzipiert, kann jedoch für Aktivitätsprotokollwarnungen angepasst werden.
* [Senden einer Nachricht an eine Azure-Warteschlange auf der Grundlage einer Azure-Warnung mithilfe einer Logik-App](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Dieses Beispiel ist eigentlich für Metrikwarnungen konzipiert, kann jedoch für Aktivitätsprotokollwarnungen angepasst werden.