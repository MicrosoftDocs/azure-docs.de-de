---
title: Erfassen von Azure-Aktivitätsprotokollen in einem Log Analytics-Arbeitsbereich über Azure-Mandanten hinweg | Microsoft-Dokumentation
description: Erfassen Sie mit Event Hubs und Logik-Apps Daten aus dem Azure-Aktivitätsprotokoll, und senden Sie diese Daten an einen Log Analytics-Arbeitsbereich in Azure Monitor eines anderen Mandanten.
services: log-analytics, logic-apps, event-hubs
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: magoedte
ms.openlocfilehash: d8cea59cd0bbeff410f585693cb7ffed82fd9327
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245599"
---
# <a name="collect-azure-activity-logs-into-azure-monitor-across-azure-active-directory-tenants"></a>Erfassen von Azure-Aktivitätsprotokollen in Azure Monitor über Azure Active Directory-Mandanten hinweg

In diesem Artikel wird schrittweise die Methode beschrieben, mit der Sie mithilfe des Datensammlers von Azure Log Analytics (Connector für Logik-Apps) Azure-Aktivitätsprotokolle in einem Log Analytics-Arbeitsbereich in Azure Monitor erfassen. Führen Sie die in diesem Artikel beschriebenen Schritte aus, wenn Sie Protokolle an einen Arbeitsbereich senden möchten, der sich in einem anderen Azure Active Directory-Mandanten befindet. Als Managed Service Provider (MSP) beispielsweise möchten Sie vielleicht Aktivitätsprotokolle aus dem Abonnement eines Kunden erfassen und in einem Log Analytics-Arbeitsbereich Ihres eigenen Abonnements speichern.

Wenn der Log Analytics-Arbeitsbereich zum gleichen oder einem anderen Azure-Abonnement gehört, sich aber im selben Azure Active Directory befindet, erfassen Sie Azure-Aktivitätsprotokolle mit den unter [Erfassen und Analysieren von Azure-Aktivitätsprotokollen im Log Analytics-Arbeitsbereich in Azure Monitor](activity-log-collect.md) beschriebenen Schritten.

## <a name="overview"></a>Übersicht

Entsprechend der in diesem Szenario verwendeten Strategie soll das Azure-Aktivitätsprotokoll Ereignisse an einen [Event Hub](../../event-hubs/event-hubs-about.md) senden, während eine [Logik-App](../../logic-apps/logic-apps-overview.md) diese an Ihren Log Analytics-Arbeitsbereich sendet. 

![Datenfluss vom Aktivitätsprotokoll zum Log Analytics-Arbeitsbereich](media/collect-activity-logs-subscriptions/data-flow-overview.png)

Vorteile dieses Ansatzes:
- Niedrige Latenz, da das Azure-Aktivitätsprotokoll in den Event Hub gestreamt wird.  Daraufhin wird die Logik-App ausgelöst und sendet die Daten an den Arbeitsbereich. 
- Es ist nur minimaler Code erforderlich, und es muss keine Serverinfrastruktur bereitgestellt werden.

In diesem Artikel werden Sie durch folgende Aufgaben geführt:
1. Erstellen Sie einen Event Hub. 
2. Exportieren von Aktivitätsprotokollen in einen Event Hub mithilfe eines Exportprofils für das Azure-Aktivitätsprotokoll
3. Erstellen einer Logik-App, die Ereignisse aus dem Event Hub liest und an den Log Analytics-Arbeitsbereich sendet

## <a name="requirements"></a>Requirements (Anforderungen)
Nachfolgend sind die Anforderungen an die Azure-Ressourcen aufgeführt, die in diesem Szenario verwendet werden.

- Der Event Hub-Namespace muss nicht zu dem Abonnement gehören, das die Protokolle ausgibt. Der Benutzer, der die Einstellung konfiguriert, benötigt die entsprechenden Zugriffsberechtigungen für beide Abonnements. Wenn Sie in einem Azure Active Directory über mehrere Abonnements verfügen, können Sie die Aktivitätsprotokolle für alle Abonnements an einen einzelnen Event Hub senden.
- Die Logik-App kann zu einem anderen Abonnement als der Event Hub gehören und muss sich auch nicht im selben Azure Active Directory befinden. Die Logik-App liest mit dem gemeinsam verwendeten Zugriffsschlüssel des Event Hubs die Ereignisse aus dem Event Hub.
- Der Log Analytics-Arbeitsbereich kann zu einem anderen Abonnement und Azure Active Directory als die Logik-App gehören. Aus Gründen der Einfachheit wird jedoch die Zugehörigkeit zum selben Abonnement empfohlen. Die Logik-App sendet die Ereignisse an den Arbeitsbereich und verwendet dabei die ID und den Schlüssel des Log Analytics-Arbeitsbereichs.



## <a name="step-1---create-an-event-hub"></a>Schritt 1: Erstellen eines Event Hubs

<!-- Follow the steps in [how to create an Event Hubs namespace and Event Hub](../../event-hubs/event-hubs-create.md) to create your event hub. -->

1. Klicken Sie im Azure-Portal auf **Ressource erstellen** > **Internet der Dinge** > **Event Hubs**.

   ![Marketplace > Neuer Event Hub](media/collect-activity-logs-subscriptions/marketplace-new-event-hub.png)

3. Geben Sie unter **Namespace erstellen**, entweder einen neuen Namespace ein, oder wählen Sie einen vorhandenen aus. Das System überprüft sofort, ob dieser Name verfügbar ist.

   ![Dialogfeld "Namespace erstellen – Event Hub"](media/collect-activity-logs-subscriptions/create-event-hub1.png)

4. Wählen Sie den Tarif (Basis oder Standard), ein Azure-Abonnement, eine Ressourcengruppe und einen Speicherort für die neue Ressource aus.  Klicken Sie auf **Erstellen** , um den Namespace zu erstellen. Möglicherweise müssen Sie ein paar Minuten warten, bis das System die Ressourcen vollständig bereitgestellt hat.
6. Klicken Sie in der Liste auf den Namespace, den Sie gerade erstellt haben.
7. Wählen Sie **SAS-Richtlinien** aus, und klicken Sie dann auf **RootManageSharedAccessKey**.

   ![SAS-Richtlinien für den Event Hub](media/collect-activity-logs-subscriptions/create-event-hub7.png)
   
8. Klicken Sie auf die Kopierschaltfläche, um die Verbindungszeichenfolge **RootManageSharedAccessKey** in die Zwischenablage zu kopieren. 

   ![Gemeinsam verwendeter Zugriffsschlüssel für den Event Hub](media/collect-activity-logs-subscriptions/create-event-hub8.png)

9. Bewahren Sie an einem temporären Speicherort (z. B. Editor) eine Kopie des Event Hub-Namens und der primären oder sekundären Verbindungszeichenfolge für den Event Hub auf. Diese Werte sind für die Logik-App erforderlich.  Als Event Hub-Verbindungszeichenfolge können Sie die Verbindungszeichenfolge **RootManageSharedAccessKey** verwenden oder eine eigene erstellen.  Die verwendete Verbindungszeichenfolge muss mit `Endpoint=sb://` beginnen und für eine Richtlinie mit der Zugriffsrichtlinie **Verwalten** bestimmt sein.


## <a name="step-2---export-activity-logs-to-event-hub"></a>Schritt 2: Exportieren von Aktivitätsprotokollen in einen Event Hub

Um das Streaming des Aktivitätsprotokolls zu aktivieren, wählen Sie einen Event Hub-Namespace und eine SAS-Richtlinie für diesen Namespace aus. In diesem Namespace wird ein Event Hub erstellt, wenn das erste neue Aktivitätsprotokollereignis auftritt. 

Sie können einen Event Hub-Namespace verwenden, der nicht zu dem Abonnement gehört, das die Protokolle ausgibt; die Abonnements müssen sich jedoch im gleichen Azure Active Directory befinden. Der Benutzer, der die Einstellung konfiguriert, benötigt den entsprechenden RBAC-Zugriff für beide Abonnements. 

1. Wählen Sie im Azure-Portal **Monitor** > **Aktivitätsprotokoll** aus.
3. Klicken Sie oben auf der Seite auf die Schaltfläche **Exportieren**.

   ![Azure-Monitor während der Navigation](media/collect-activity-logs-subscriptions/activity-log-blade.png)

4. Wählen Sie das **Abonnement** für den Export aus, und klicken Sie dann im Dropdown-Menü **Regionen** auf **Alle auswählen**, um Ereignisse für alle Ressourcen in allen Regionen auszuwählen. Aktivieren Sie das Kontrollkästchen **In einen Event Hub exportieren**.
7. Klicken Sie auf **Servicebus-Namespace**, und wählen Sie dann das **Abonnement** mit dem Event Hub, den **Event Hub-Namespace** und einen **Event Hub-Richtliniennamen** aus.

    ![Seite für den Export in einen Event Hub](media/collect-activity-logs-subscriptions/export-activity-log2.png)

11. Klicken Sie auf **OK** und dann auf **Speichern**, um diese Einstellungen zu speichern. Die Einstellungen werden sofort auf Ihr Abonnement angewendet.

<!-- Follow the steps in [stream the Azure Activity Log to Event Hubs](../../azure-monitor/platform/activity-logs-stream-event-hubs.md) to configure a log profile that writes activity logs to an event hub. -->

## <a name="step-3---create-logic-app"></a>Schritt 3: Erstellen einer Logik-App

Wenn die Aktivitätsprotokolle in den Event Hub geschrieben werden, erstellen Sie eine Logik-App, um die Protokolle im Event Hub zu erfassen und in den Log Analytics-Arbeitsbereich zu schreiben.

Die Logik-App umfasst Folgendes:
- Ein Trigger [Event Hub-Connector](https://docs.microsoft.com/connectors/eventhubs/) für den Lesevorgang im Event Hub
- Eine [JSON Analyseaktion](../../logic-apps/logic-apps-content-type.md) zum Extrahieren der JSON-Ereignisse
- Eine [Compose-Aktion](../../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) zum Konvertieren der JSON in ein Objekt
- Ein [Log Analytics-Connector zum Senden der Daten](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) zum Bereitstellen der Daten in den Log Analytics-Arbeitsbereich

   ![Hinzufügen des Event Hub-Triggers in Logik-Apps](media/collect-activity-logs-subscriptions/log-analytics-logic-apps-activity-log-overview.png)

### <a name="logic-app-requirements"></a>Anforderungen an Logik-Apps
Stellen Sie vor dem Erstellen der Logik-App sicher, dass Ihnen die folgenden Informationen aus den vorherigen Schritten vorliegen:
- Event Hub-Name
- Event Hub Verbindungszeichenfolge (entweder primär oder sekundär) für den Event Hub-Namespace
- ID des Log Analytics-Arbeitsbereichs
- Gemeinsam verwendeter Schlüssel für Log Analytics

Um Name und Verbindungszeichenfolge des Event Hubs abzurufen, führen Sie die unter [Überprüfen von Berechtigungen für Event Hub-Namespaces und Suchen nach der Verbindungszeichenfolge](../../connectors/connectors-create-api-azure-event-hubs.md#permissions-connection-string) beschriebenen Schritte aus.


### <a name="create-a-new-blank-logic-app"></a>Erstellen einer neuen leeren Logik-App

1. Wählen Sie im Azure-Portal **Ressource erstellen** > **Unternehmensintegration** > **Logik-App** aus.

    ![Marketplace > Neue Logik-App](media/collect-activity-logs-subscriptions/marketplace-new-logic-app.png)

2. Geben Sie die in der folgenden Tabelle aufgeführten Einstellungen ein.

    ![Erstellen einer Logik-App](media/collect-activity-logs-subscriptions/create-logic-app.png)

   |Einstellung | BESCHREIBUNG  |
   |:---|:---|
   | NAME           | Eindeutiger Name für die Logik-App. |
   | Abonnement   | Wählen Sie das Azure-Abonnement aus, das die Logik-App enthalten soll. |
   | Ressourcengruppe | Wählen Sie für die Logik-App eine vorhandene Azure-Ressourcengruppe aus, oder erstellen Sie eine neue Ressourcengruppe. |
   | Location       | Wählen Sie die Datencenterregion für die Bereitstellung Ihrer Logik-App aus. |
   | Log Analytics  | Aktivieren Sie diese Einstellung, wenn bei jeder Ausführung der Logik-App der Status in einem Log Analytics-Arbeitsbereich protokolliert werden soll.  |

    
3. Klicken Sie auf **Erstellen**. Wenn die Benachrichtigung **Bereitstellung erfolgreich** angezeigt wird, klicken Sie auf **Zu Ressource wechseln**, um Ihre Logik-App zu öffnen.

4. Wählen Sie unter **Vorlagen** die Option **Leere Logik-App**. 

Der Logik-App-Designer zeigt nun die verfügbaren Connectors und die dazugehörigen Trigger an, mit denen Sie den Logik-App-Workflow starten.

<!-- Learn [how to create a logic app](../../logic-apps/quickstart-create-first-logic-app-workflow.md). -->

### <a name="add-event-hub-trigger"></a>Hinzufügen des Event Hub-Triggers

1. Geben Sie im Suchfeld des Logik-App-Designers *Event Hubs* als Filter ein. Wählen Sie den Trigger **Event Hubs – Wenn Ereignisse im Event Hub verfügbar sind** aus.

   ![Hinzufügen des Event Hub-Triggers in Logik-Apps](media/collect-activity-logs-subscriptions/logic-apps-event-hub-add-trigger.png)

2. Wenn Sie zum Eingeben von Anmeldeinformationen aufgefordert werden, stellen Sie eine Verbindung zu Ihrem Event Hubs-Namespace her. Geben Sie einen Namen für die Verbindung und dann die kopierte Verbindungszeichenfolge ein.  Klicken Sie auf **Erstellen**.

   ![Hinzufügen der Event Hub-Verbindung in Logik-Apps](media/collect-activity-logs-subscriptions/logic-apps-event-hub-add-connection.png)

3. Nachdem Sie die Verbindung erstellt haben, bearbeiten Sie die Einstellungen für den Trigger. Wählen Sie zunächst **insights-operational-logs** im Dropdownmenü **Event Hub-Name** aus.

   ![Dialogfeld bei verfügbaren Ereignissen](media/collect-activity-logs-subscriptions/logic-apps-event-hub-read-events.png)

5. Erweitern von **Erweiterte Optionen anzeigen** und ändern des **Inhaltstyps** in *Anwendung/json*

   ![Dialogfeld zum Konfigurieren des Event Hubs](media/collect-activity-logs-subscriptions/logic-apps-event-hub-configuration.png)

### <a name="add-parse-json-action"></a>Hinzufügen der JSON-Analyseaktion

Die Ausgabe aus dem Event Hub enthält eine JSON-Nutzlast mit einem Array aus Datensätzen. Die Aktion [JSON analysieren](../../logic-apps/logic-apps-content-type.md) wird zum Extrahieren des Arrays aus Datensätzen verwendet, das an den Log Analytics-Arbeitsbereich gesendet werden soll.

1. Klicken Sie auf **Nächster Schritt** > **Aktion hinzufügen**.
2. Geben Sie im Suchfeld *JSON analysieren* als Filter ein. Wählen Sie die Aktion **Datenvorgänge – JSON analysieren** aus.

   ![Hinzufügen der JSON-Analyseaktion in Logik-Apps](media/collect-activity-logs-subscriptions/logic-apps-add-parse-json-action.png)

3. Klicken Sie auf das Feld **Inhalt**, und wählen Sie dann *Text* aus.

4. Kopieren Sie das folgende Schema, und fügen Sie es im Feld **Schema** ein.  Dieses Schema entspricht der Ausgabe der Event Hub-Aktion.  

   ![Dialogfeld „JSON analysieren“](media/collect-activity-logs-subscriptions/logic-apps-parse-json-configuration.png)

``` json-schema
{
    "properties": {
        "body": {
            "properties": {
                "ContentData": {
                    "type": "string"
                },
                "Properties": {
                    "properties": {
                        "ProfileName": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                },
                "SystemProperties": {
                    "properties": {
                        "EnqueuedTimeUtc": {
                            "type": "string"
                        },
                        "Offset": {
                            "type": "string"
                        },
                        "PartitionKey": {},
                        "SequenceNumber": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "type": "object"
        },
        "headers": {
            "properties": {
                "Cache-Control": {
                    "type": "string"
                },
                "Content-Length": {
                    "type": "string"
                },
                "Content-Type": {
                    "type": "string"
                },
                "Date": {
                    "type": "string"
                },
                "Expires": {
                    "type": "string"
                },
                "Location": {
                    "type": "string"
                },
                "Pragma": {
                    "type": "string"
                },
                "Retry-After": {
                    "type": "string"
                },
                "Timing-Allow-Origin": {
                    "type": "string"
                },
                "Transfer-Encoding": {
                    "type": "string"
                },
                "Vary": {
                    "type": "string"
                },
                "X-AspNet-Version": {
                    "type": "string"
                },
                "X-Powered-By": {
                    "type": "string"
                },
                "x-ms-request-id": {
                    "type": "string"
                }
            },
            "type": "object"
        }
    },
    "type": "object"
}
```

>[!TIP]
> Sie können eine Beispielnutzlast erhalten, indem Sie auf **Ausführen** klicken und die **Unformatierte Ausgabe** aus dem Event Hub betrachten.  Anschließend können Sie bei der Aktion **JSON analysieren** mit der Option **Beispielnutzlast zum Generieren eines Schemas verwenden** diese Ausgabe zum Generieren des Schemas verwenden.

### <a name="add-compose-action"></a>Hinzufügen der Compose-Aktion
Die [Compose](../../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action)-Aktion verwendet die JSON-Ausgabe und erstellt ein Objekt, das von der Log Analytics-Aktion verwendet werden kann.

1. Klicken Sie auf **Nächster Schritt** > **Aktion hinzufügen**.
2. Geben Sie *Compose* als Filter ein, und wählen Sie dann die Aktion **Datenvorgänge – Compose** aus.

    ![Hinzufügen der Compose-Aktion](media/collect-activity-logs-subscriptions/logic-apps-add-compose-action.png)

3. Klicken Sie auf das Feld **Eingaben**, und wählen Sie **Text** unter der Aktivität **JSON analysieren** aus.


### <a name="add-log-analytics-send-data-action"></a>Hinzufügen der Aktion zum Senden der Daten an Log Analytics
Die Aktion [Datensammler von Azure Log Analytics](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) übernimmt das Objekt von der Compose-Aktion und sendet es an den Log Analytics-Arbeitsbereich.

1. Klicken Sie auf **Nächster Schritt** > **Aktion hinzufügen**.
2. Geben Sie *Log Analytics* als Filter ein, und wählen Sie dann die Aktion **Datensammler von Azure Log Analytics – Daten senden** aus.

   ![Hinzufügen der Aktion zum Senden der Daten an Log Analytics in Logik-Apps](media/collect-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-connector.png)

3. Geben Sie einen Namen für die Verbindung ein, und fügen Sie die **Arbeitsbereichs-ID** und den **Arbeitsbereichsschlüssel** für Ihren Log Analytics-Arbeitsbereich ein.  Klicken Sie auf **Create**.

   ![Hinzufügen der Log Analytics-Verbindung in Logik-Apps](media/collect-activity-logs-subscriptions/logic-apps-log-analytics-add-connection.png)

4. Nachdem Sie die Verbindung erstellt haben, bearbeiten Sie die in der folgenden Tabelle aufgeführten Einstellungen. 

    ![Konfigurieren der Aktion „Daten senden“](media/collect-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-configuration.png)

   |Einstellung        | Wert           | BESCHREIBUNG  |
   |---------------|---------------------------|--------------|
   |JSON-Anforderungstext  | **Ausgabe** von der **Compose**-Aktion | Ruft die Datensätze aus dem Text der Compose-Aktion ab. |
   | Name des benutzerdefinierten Protokolls | AzureActivity | Name der benutzerdefinierten Protokolltabelle, die im Log Analytics-Arbeitsbereich zur Aufnahme der importierten Daten erstellt werden soll. |
   | time-generated-field | time | Wählen Sie das JSON-Feld für die Zeit nicht aus – geben Sie einfach das Wort **time** ein. Wenn Sie das JSON-Feld auswählen, fügt der Designer die Aktion **Daten senden** in eine *For Each*-Schleife ein, was nicht gewünscht wird. |




10. Klicken Sie auf **Speichern**, um die an der Logik-App vorgenommenen Änderungen zu speichern.

## <a name="step-4---test-and-troubleshoot-the-logic-app"></a>Schritt 4: Testen der Logik-App und Problembehandlung
Wenn der Workflow vollständig ist, können Sie ihn im Designer testen, um eine fehlerfreie Funktion zu gewährleisten.

Klicken Sie im Logik-App-Designer auf **Ausführen**, um die Logik-App zu testen. Jeder Schritt in der Logik-App wird mit einem Statussymbol angezeigt. Ein weißes Häkchen in einem grünen Kreis weist auf eine erfolgreiche Ausführung hin.

   ![Testen der Logik-App](media/collect-activity-logs-subscriptions/test-logic-app.png)

Um ausführliche Informationen zu den einzelnen Schritten anzuzeigen, klicken Sie auf den Namen des Schritts, um ihn zu erweitern. Klicken Sie auf **Unformatierte Eingaben anzeigen** und **Unformatierte Ausgaben anzeigen**, um weitere Informationen zu den bei jedem Schritt empfangenen und gesendeten Daten anzuzeigen.

## <a name="step-5---view-azure-activity-log-in-log-analytics"></a>Schritt 5: Anzeigen des Azure-Aktivitätsprotokolls in Log Analytics
Zuletzt überprüfen Sie den Log Analytics-Arbeitsbereich, um sicherzustellen, dass die Daten erwartungsgemäß erfasst werden.

1. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**. Geben Sie in der Liste mit den Ressourcen **Log Analytics** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Log Analytics**.
2. Wählen Sie in der Liste mit den Log Analytics-Arbeitsbereichen Ihren Arbeitsbereich aus.
3.  Klicken Sie auf die Kachel **Protokollsuche** und im Bereich „Protokollsuche“ auf den Abfragefeldtyp `AzureActivity_CL`, und drücken Sie dann auf EINGABE, oder klicken Sie auf die Suchschaltfläche rechts neben dem Abfragefeld. Wenn Sie das benutzerdefinierte Protokoll nicht *AzureActivity* genannt haben, geben Sie den von Ihnen ausgewählten Namen ein, und fügen Sie `_CL` an.

>[!NOTE]
> Bei einem neuen benutzerdefinierten Protokoll, das zum ersten Mal an den Log Analytics-Arbeitsbereich gesendet wird, kann es bis zu einer Stunde dauern, bis das Protokoll durchsucht werden kann.

>[!NOTE]
> Die Aktivitätsprotokolle werden in eine benutzerdefinierte Tabelle geschrieben und nicht in der [Lösung für Aktivitätsprotokolle](./activity-log-collect.md) angezeigt.


![Testen der Logik-App](media/collect-activity-logs-subscriptions/log-analytics-results.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie eine Logik-App zum Lesen von Azure-Aktivitätsprotokollen aus einem Event Hub und zum Senden dieser Protokolle zur Analyse an den Log Analytics-Arbeitsbereich erstellt. Weitere Informationen zum Visualisieren von Daten in einem Arbeitsbereich, einschließlich dem Erstellen von Dashboards, finden Sie im Tutorial zum Visualisieren von Daten.

> [!div class="nextstepaction"]
> [Lernprogramm – Visualisieren der Daten von einer Protokollsuche](./../../azure-monitor/learn/tutorial-logs-dashboards.md)
