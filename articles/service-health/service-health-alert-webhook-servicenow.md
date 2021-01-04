---
title: Senden von Azure Service Health-Warnungen mit ServiceNow
description: Erhalten Sie personalisierte Benachrichtigungen zu Service Health-Ereignissen an Ihre ServiceNow-Instanz.
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom: devx-track-js
ms.openlocfilehash: f120e9d950cc349d0331a476dbfbfe9d7e599f0c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000639"
---
# <a name="send-azure-service-health-alerts-with-servicenow-using-webhooks"></a>Senden von Azure Service-Integritätswarnungen mit ServiceNow mit Webhooks

In diesem Artikel erfahren Sie, wie Sie Azure Service Health-Warnungen mit Webhook in ServiceNow integrieren. Nachdem Sie die Webhookintegration in Ihrer ServiceNow-Instanz eingerichtet haben, erhalten Sie bei Azure-Dienstprobleme, die Sie betreffen, Warnungen über die vorhandene Benachrichtigungsinfrastruktur. Jedes Mal, wenn eine Azure Service Health-Warnung ausgelöst wird, wird ein Webhook über die skriptgesteuerte REST-API von ServiceNow aufgerufen.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Erstellen einer skriptgesteuerte REST-API in ServiceNow

1.  Stellen Sie sicher, dass Sie Ihr [ServiceNow](https://www.servicenow.com/)-Konto registriert wurde und dass Sie angemeldet sind.

1.  Navigieren Sie zum Abschnitt **System Web Services** (Systemwebdienste) in ServiceNow, und wählen Sie im Abschnitt **Scripted REST APIs** (skriptgesteuerte REST-APIs) aus.

    ![Abschnitt „Scripted Web Service“ in ServiceNow](./media/webhook-alerts/servicenow-sws-section.png)

1.  Wählen Sie **New** (Neu) aus, um einen neuen skriptgesteuerten REST-Dienst zu erstellen.
 
    ![Schaltfläche „New Scripted REST API“ in ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

1.  Fügen Sie einen **Namen** für Ihre REST-API hinzu und legen Sie die **API-ID** auf `azureservicehealth` fest.

1.  Klicken Sie auf **Submit** (Senden).

    ![„REST API Settings“ in ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

1.  Wählen Sie die REST-API, die Sie erstellt haben, und dann auf der Registerkarte **Resources** (Ressourcen) die Option **New** (Neu) aus.

    ![Registerkarte „Resources“ in ServiceNow](./media/webhook-alerts/servicenow-resources-tab.png)

1.  Geben Sie der neuen Ressource den **Namen**`event`, und ändern Sie die **HTTP-Methode** in `POST`.

1.  Fügen Sie im Abschnitt **Script** (Skript) den folgenden JavaScript-Code hinzu:

    >[!NOTE]
    >Sie müssen die Werte `<secret>`,`<group>` und `<email>` im unten aufgeführten Skript aktualisieren.
    >* `<secret>` sollte eine zufällige Zeichenfolge sein, z. B. eine GUID
    >* `<group>` sollte die ServiceNow-Gruppe sein, der Sie den Vorfall zuweisen möchten
    >* `<email>` sollte die bestimmte Person sein, der Sie den Vorfall zuweisen möchten (optional)
    >

    ```javascript
    (function process( /*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {
        var apiKey = request.queryParams['apiKey'];
        var secret = '<secret>';
        if (apiKey == secret) {
            var event = request.body.data;
            var responseBody = {};
            if (event.data.context.activityLog.operationName == 'Microsoft.ServiceHealth/incident/action') {
                var inc = new GlideRecord('incident');
                var incidentExists = false;
                inc.addQuery('number', event.data.context.activityLog.properties.trackingId);
                inc.query();
                if (inc.hasNext()) {
                    incidentExists = true;
                    inc.next();
                } else {
                    inc.initialize();
                }
                var short_description = "Azure Service Health";
                if (event.data.context.activityLog.properties.incidentType == "Incident") {
                    short_description += " - Service Issue - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Maintenance") {
                    short_description += " - Planned Maintenance - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Informational" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
                    short_description += " - Health Advisory - ";
                }
                short_description += event.data.context.activityLog.properties.title;
                inc.short_description = short_description;
                inc.description = event.data.context.activityLog.properties.communication;
                inc.work_notes = "Impacted subscription: " + event.data.context.activityLog.subscriptionId;
                if (incidentExists) {
                    if (event.data.context.activityLog.properties.stage == 'Active') {
                        inc.state = 2;
                    } else if (event.data.context.activityLog.properties.stage == 'Resolved') {
                        inc.state = 6;
                    } else if (event.data.context.activityLog.properties.stage == 'Closed') {
                        inc.state = 7;
                    }
                    inc.update();
                    responseBody.message = "Incident updated.";
                } else {
                    inc.number = event.data.context.activityLog.properties.trackingId;
                    inc.state = 1;
                    inc.impact = 2;
                    inc.urgency = 2;
                    inc.priority = 2;
                    inc.assigned_to = '<email>';
                    inc.assignment_group.setDisplayValue('<group>');
                    var subscriptionId = event.data.context.activityLog.subscriptionId;
                    var comments = "Azure portal Link: https://app.azure.com/h";
                    comments += "/" + event.data.context.activityLog.properties.trackingId;
                    comments += "/" + subscriptionId.substring(0, 3) + subscriptionId.slice(-3);
                    var impactedServices = JSON.parse(event.data.context.activityLog.properties.impactedServices);
                    var impactedServicesFormatted = "";
                    for (var i = 0; i < impactedServices.length; i++) {
                        impactedServicesFormatted += impactedServices[i].ServiceName + ": ";
                        for (var j = 0; j < impactedServices[i].ImpactedRegions.length; j++) {
                            if (j != 0) {
                                impactedServicesFormatted += ", ";
                            }
                            impactedServicesFormatted += impactedServices[i].ImpactedRegions[j].RegionName;
                        }

                        impactedServicesFormatted += "\n";

                    }
                    comments += "\n\nImpacted Services:\n" + impactedServicesFormatted;
                    inc.comments = comments;
                    inc.insert();
                    responseBody.message = "Incident created.";
                }
            } else {
                responseBody.message = "Hello from the other side!";
            }
            response.setBody(responseBody);
        } else {
            var unauthorized = new sn_ws_err.ServiceError();
            unauthorized.setStatus(401);
            unauthorized.setMessage('Invalid apiKey');
            response.setError(unauthorized);
        }
    })(request, response);
    ```

1.  Deaktivieren Sie auf der Registerkarte „Security“ (Sicherheit) die Option **Requires authentication** (Authentifizierung erforderlich), und wählen Sie **Submit** (Absenden) aus. Die API wird stattdessen durch den von Ihnen festgelegten `<secret>` geschützt.

    ![Kontrollkästchen „Requires Authentication“ in ServiceNow](./media/webhook-alerts/servicenow-resource-settings.png)

1.  Zurück im Abschnitt „Scripted REST APIs“ (Skriptgesteuerte REST-APIs) finden Sie den **Base API Path** (API-Basispfad) für Ihre neue REST-API:

     ![„Base API Path“ in ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

1.  Die vollständige Integrations-URL sieht wie folgt aus:

    ```http
    https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>
    ```

## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Erstellen einer Warnung mithilfe von ServiceNow im Azure-Portal
### <a name="for-a-new-action-group"></a>Für eine neue Aktionsgruppe:
1. Führen Sie die Schritte 1 bis 8 in [diesem Artikel](./alerts-activity-log-service-notifications-portal.md) aus, um eine Warnung mit einer neuen Aktionsgruppe zu erstellen.

1. Definieren Sie in der Liste der **Aktionen** Folgendes:

    a. **Aktionstyp:** *Webhook*

    b. **Details:** Die zuvor gespeicherte ServiceNow-**Integrations-URL**.

    c. **Name:** Name, Alias oder Bezeichner des Webhook.

1. Wählen Sie **Save** (Speichern) aus, wenn das Erstellen der Warnung abgeschlossen ist.

### <a name="for-an-existing-action-group"></a>Für eine vorhandene Aktionsgruppe:
1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) die Option **Überwachen** aus.

1. Wählen Sie im Abschnitt **Einstellungen** die Option **Aktionsgruppen** aus.

1. Suchen und markieren Sie die Aktionsgruppe, die Sie bearbeiten möchten.

1. Fügen Sie Folgendes zur Liste der **Aktionen** hinzu:

    a. **Aktionstyp:** *Webhook*

    b. **Details:** Die zuvor gespeicherte ServiceNow-**Integrations-URL**.

    c. **Name:** Name, Alias oder Bezeichner des Webhook.

1. Wählen Sie **Save** (Speichern) aus, wenn Sie mit dem Aktualisieren der Aktionsgruppe fertig sind.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testen der Webhookintegration über eine HTTP POST-Anforderung
1. Erstellen Sie die Service Health-Nutzlast, die Sie senden möchten. Eine Service Health-Beispielwebhook-Nutzlast finden Sie unter [Webhooks für Azure-Aktivitätsprotokollwarnungen](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Erstellen Sie eine HTTP POST-Anforderung, indem Sie wie folgt vorgehen:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Sie sollten eine `200 OK`-Antwort mit der Meldung „Incident created“ (Vorfall erstellt) erhalten.

1. Wechseln Sie zu [ServiceNow](https://www.servicenow.com/), um zu überprüfen, ob Ihre Integration erfolgreich eingerichtet wurde.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie, wie Sie [Webhookbenachrichtigungen für vorhandene Problemverwaltungssysteme konfigurieren](service-health-alert-webhook-guide.md).
- Weitere Informationen zum [Webhookschema für Aktivitätsprotokollwarnungen](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Weitere Informationen zu [Dienstintegritätsbenachrichtigungen](./service-notifications.md).
- Weitere Informationen zu [Aktionsgruppen](../azure-monitor/platform/action-groups.md).
