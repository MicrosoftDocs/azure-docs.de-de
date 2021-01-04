---
title: Auslösen komplexer Aktionen mit Azure Monitor-Warnungen
description: Erfahren Sie, wie eine Aktion einer Logik-App zum Verarbeiten von Azure Monitor-Warnungen erstellt wird.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 07/18/2018
ms.subservice: alerts
ms.openlocfilehash: f76d28018fdf55314593dabc44ef1e9a1dab9494
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995096"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Auslösen komplexer Aktionen mit Azure Monitor-Warnungen

Dieser Artikel zeigt Ihnen, wie eine Logik-App eingerichtet und ausgelöst wird, um eine Konversation in Microsoft Teams zu erstellen, wenn eine Warnung ausgelöst wird.

## <a name="overview"></a>Übersicht

Wenn eine Azure Monitor-Warnung auslöst wird, wird eine [Aktionsgruppe](./action-groups.md) aufgerufen. Aktionsgruppen ermöglichen Ihnen das Auslösen von mindestens einer Aktion, um andere über eine Warnung zu benachrichtigen und diese auch zu beseitigen.

Der allgemeine Vorgang besteht aus den folgenden Schritten:

-   Erstellen Sie die Logik-App für den jeweiligen Warnungstyp.

-   Importieren Sie eine Beispielnutzlast für den jeweiligen Warnungstyp in die Logik-App.

-   Definieren Sie das Verhalten der Logik-App.

-   Kopieren Sie den HTTP-Endpunkt der Logik-App in eine Azure-Aktionsgruppe.

Der Vorgang ist ähnlich, wenn die Logik-App eine andere Aktion ausführen soll.

## <a name="create-an-activity-log-alert-administrative"></a>Erstellen einer Aktivitätsprotokollwarnung: Administrative

1.  Wählen Sie im Azure-Portal oben links **Ressource erstellen** aus.

2.  Suchen Sie nach **Logik-App**, und wählen Sie diese Option und dann **Erstellen** aus.

3.  Geben Sie der Logik-App einen **Namen**, wählen Sie eine **Ressourcengruppe** aus usw.

    ![Erstellen einer Logik-App](media/action-groups-logic-app/create-logic-app-dialog.png "Erstellen einer Logik-App")

4.  Wählen Sie **Erstellen** aus, um die Logik-App zu erstellen. Eine Popupmeldung zeigt an, dass die Logik-App erstellt ist. Wählen Sie **Ressource starten** aus, um den **Logik-App-Designer** zu öffnen.

5.  Wählen Sie den folgenden Trigger aus: **Beim Empfang einer HTTP-Anforderung**.

    ![Logik-App-Trigger](media/action-groups-logic-app/logic-app-triggers.png "Logik-App-Trigger")

6.  Wählen Sie **Bearbeiten** aus, um den HTTP-Anforderungstrigger zu ändern.

    ![HTTP-Anforderungstrigger](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP-Anforderungstrigger")

7.  Wählen Sie **Beispielnutzlast zum Generieren eines Schemas verwenden** aus.

    ![Verwenden einer Beispielnutzlast](media/action-groups-logic-app/use-sample-payload-button.png "Verwenden einer Beispielnutzlast")

8.  Kopieren Sie die folgende Beispielnutzlast, und fügen Sie sie in das Dialogfeld ein:

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. Der **Logik-App-Designer** zeigt ein Popupfenster an, das Sie daran erinnert, dass in der an die Logik-App gesendeten Anforderung der **Content-Type**-Header auf **application/json** festgelegt werden muss. Schließen Sie das Popupfenster. Die Azure Monitor-Warnung legt den Header fest.

    ![Festlegen des Content-Type-Headers](media/action-groups-logic-app/content-type-header.png "Festlegen des Content-Type-Headers")

10. Wählen Sie **+** **Neuer Schritt** und anschließend **Aktion hinzufügen** aus.

    ![Hinzufügen einer Aktion](media/action-groups-logic-app/add-action.png "Hinzufügen einer Aktion")

11. Suchen Sie nach dem Microsoft Teams-Connector, und wählen ihn aus. Wählen Sie die Aktion **Microsoft Teams: Nachricht veröffentlichen** aus.

    ![Microsoft Teams-Aktionen](media/action-groups-logic-app/microsoft-teams-actions.png "Microsoft Teams-Aktionen")

12. Konfigurieren Sie die Microsoft Teams-Aktion. Der **Logic Apps Designer** fordert Sie zur Authentifizierung mit Ihrem Geschäfts-, Schul- oder Unikonto auf. Wählen Sie die **Team-ID** und die **Kanal-ID** zum Senden der Nachricht aus.

13. Konfigurieren Sie die Nachricht mithilfe einer Kombination aus statischem Text und Verweisen auf die \<fields\> im dynamischen Inhalt. Kopieren Sie den folgenden Text, und fügen Sie ihn in das Feld **Nachricht** ein:

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Suchen Sie dann nach den \<fields\> mit dynamischen Inhaltstags mit dem gleichen Namen, und ersetzen Sie diese.

    > [!NOTE]
    > Es gibt zwei dynamische Felder mit dem Namen **Status**. Fügen Sie der Nachricht beide Felder hinzu. Verwenden Sie das Feld im **activityLog**-Eigenschaftenbehälter, und löschen Sie das andere Feld. Wenn Sie Ihren Mauszeiger über dem Feld **Status** positionieren, wird der vollqualifizierte Feldverweis wie im folgenden Screenshot gezeigt angezeigt:

    ![Microsoft Teams-Aktion: Nachricht versenden](media/action-groups-logic-app/teams-action-post-message.png "Microsoft Teams-Aktion: Nachricht versenden")

14. Wählen Sie oben im **Logik-Apps-Designer** die Option **Speichern** aus, um Ihre Logik-App zu speichern.

15. Öffnen Sie Ihre vorhandene Aktionsgruppe, und fügen Sie eine Aktion hinzu, um auf die Logik-App zu verweisen. Wenn Sie nicht über eine Aktionsgruppe verfügen, erfahren Sie unter [Erstellen und Verwalten von Aktionsgruppen im Azure-Portal](./action-groups.md), wie Sie eine erstellen. Vergessen Sie nicht, Ihre Änderungen zu speichern.

    ![Aktualisieren der Aktionsgruppe](media/action-groups-logic-app/update-action-group.png "Aktualisieren der Aktionsgruppe")

Das nächste Mal, wenn Ihre Aktionsgruppe durch eine Warnung aufgerufen wird, wird Ihre Logik-App aufgerufen.

## <a name="create-a-service-health-alert"></a>Erstellen einer Service Health-Warnung

Azure Service Health-Einträge sind ein Teil des Aktivitätsprotokolls. Der Prozess zum Erstellen der Warnung ähnelt abgesehen von ein paar Änderungen dem [Erstellen einer Aktivitätsprotokollwarnung](#create-an-activity-log-alert-administrative):

- Die Schritte 1 bis 7 sind identisch.
- Verwenden Sie für Schritt 8 die folgende Beispielnutzlast für den HTTP-Anforderungstrigger:

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "...",
                        "service": "...",
                        "region": "Global",
                        "communication": "...",
                        "incidentType": "Incident",
                        "trackingId": "...",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "...",
                        "defaultLanguageContent": "...",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "...",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  Die Schritte 9 und 10 sind identisch.
-  Verwenden Sie für die Schritte 11 bis 14 folgenden Prozess:

   1. Wählen Sie **+** **Neuer Schritt** und anschließend **Bedingung hinzufügen** aus. Legen Sie die folgenden Bedingungen fest, sodass die Logik-App nur ausgeführt wird, wenn die Eingabedaten den folgenden Werten entsprechen.  Wenn Sie den Versionswert in das Textfeld eingeben, setzen Sie ihn in Anführungszeichen ("0.1.1"), um sicherzustellen, dass er als Zeichenfolge und nicht als numerischer Typ ausgewertet wird.  Das System zeigt die Anführungszeichen nicht an, wenn Sie zur Seite zurückzukehren, aber der zugrunde liegende Code behält den String-Datentyp bei.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      ![„Bedingung Service Health-Nutzlast“](media/action-groups-logic-app/service-health-payload-condition.png "Bedingung Service Health-Nutzlast")

   1. Befolgen Sie in der **If true**-Bedingung die Anweisungen in den Schritten 11 bis 13 in [Erstellen einer Aktivitätsprotokollwarnung](#create-an-activity-log-alert-administrative), um die Microsoft Teams-Aktion hinzuzufügen.

   1. Definieren Sie die Nachricht unter Verwendung einer Kombination von HTML und dynamischem Inhalt. Kopieren Sie den folgenden Inhalt, und fügen Sie ihn in das Feld **Nachricht** ein. Ersetzen Sie die Felder `[incidentType]`, `[trackingID]`, `[title]` und `[communication]` mit dynamischen Inhaltstags gleichen Namens:

       ```html
       <p>
       <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
       <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
       <strong>Title:</strong>&nbsp;[title]</p>
       <p>
       <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.</a>
       </p>
       <p>[communication]</p>
       ```

       ![„Veröffentlichungsaktion von Service Health, wenn Bedingung TRUE ist“](media/action-groups-logic-app/service-health-true-condition-post-action.png "Veröffentlichungsaktion von Service Health, wenn Bedingung TRUE ist")

   1. Geben Sie für die Bedingung **If false** eine sinnvolle Nachricht an:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       ![„Veröffentlichungsaktion von Service Health, wenn Bedingung FALSE ist“](media/action-groups-logic-app/service-health-false-condition-post-action.png "Veröffentlichungsaktion von Service Health, wenn Bedingung FALSE ist")

- Schritt 15 ist identisch. Befolgen Sie die Anweisungen zum Speichern Ihrer Logik-App und Aktualisieren Ihrer Aktionsgruppe.

## <a name="create-a-metric-alert"></a>Erstellen einer Metrikwarnung

Der Prozess zum Erstellen einer Metrikwarnung ähnelt abgesehen von ein paar Änderungen dem [Erstellen einer Aktivitätsprotokollwarnung](#create-an-activity-log-alert-administrative):

- Die Schritte 1 bis 7 sind identisch.
- Verwenden Sie für Schritt 8 die folgende Beispielnutzlast für den HTTP-Anforderungstrigger:

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "...",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "...",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "...",
        "portalLink": "..."
        },
        "properties": {}
    }
    }
    ```

- Die Schritte 9 und 10 sind identisch.
- Verwenden Sie für die Schritte 11 bis 14 folgenden Prozess:

  1. Wählen Sie **+** **Neuer Schritt** und anschließend **Bedingung hinzufügen** aus. Legen Sie die folgenden Bedingungen fest, sodass die Logik-App nur ausgeführt wird, wenn die Eingabedaten den folgenden Werten entsprechen. Wenn Sie den Versionswert in das Textfeld eingeben, setzen Sie ihn in Anführungszeichen ("2.0"), um sicherzustellen, dass er als Zeichenfolge und nicht als numerischer Typ ausgewertet wird.  Das System zeigt die Anführungszeichen nicht an, wenn Sie zur Seite zurückzukehren, aber der zugrunde liegende Code behält den String-Datentyp bei. 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       ![„Bedingung Metrikwarnungnutzlast“](media/action-groups-logic-app/metric-alert-payload-condition.png "Bedingung Metrikwarnungnutzlast")

  1. Fügen Sie in der **If true**-Bedingung eine **For each**-Schleife und die Microsoft Teams-Aktion hinzu. Definieren Sie die Nachricht unter Verwendung einer Kombination von HTML und dynamischem Inhalt.

      ![„Veröffentlichungsaktion Metrikwarnung, wenn Bedingung TRUE ist“](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "Veröffentlichungsaktion Metrikwarnung, wenn Bedingung TRUE ist")

  1. Definieren Sie in der **If false**-Bedingung eine Microsoft Teams-Aktion, die kommuniziert, dass die Metrikwarnung nicht mit den Erwartungen der Logik-App übereinstimmt. Schließen Sie die JSON-Nutzlast ein. Beachten Sie, wie im `json()`-Ausdruck auf den dynamischen Inhalt `triggerBody` verwiesen wird.

      ![„Veröffentlichungsaktion Metrikwarnung, wenn Bedingung FALSE ist“](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "Veröffentlichungsaktion Metrikwarnung, wenn Bedingung FALSE ist")

- Schritt 15 ist identisch. Befolgen Sie die Anweisungen zum Speichern Ihrer Logik-App und Aktualisieren Ihrer Aktionsgruppe.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Aufrufen anderer Anwendungen außer Microsoft Teams
Logic Apps verfügt über verschiedene Connectors, mit denen Sie Aktionen in einer Vielzahl von Anwendungen und Datenbanken auslösen können. Slack, SQL Server, Oracle, Salesforce sind lediglich einige Beispiele. Weitere Informationen zu Connectors finden Sie unter [Logic App-Connectors](../../connectors/apis-list.md).  

## <a name="next-steps"></a>Nächste Schritte
* Verschaffen Sie sich eine [Übersicht über Azure Aktivitätsprotokollwarnungen](./alerts-overview.md), und erfahren Sie, wie Sie Warnungen empfangen können.  
* Erfahren Sie, wie Sie [Warnungen konfigurieren, wenn eine Azure Service Health-Benachrichtigung gesendet wird](../../service-health/alerts-activity-log-service-notifications-portal.md).
* Weitere Informationen zu [Aktionsgruppen](./action-groups.md).

