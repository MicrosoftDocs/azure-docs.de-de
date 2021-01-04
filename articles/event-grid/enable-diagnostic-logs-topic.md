---
title: 'Azure Event Grid: Aktivieren von Diagnoseprotokollen für Themen oder Domänen'
description: Dieser Artikel enthält schrittweise Anleitungen zum Aktivieren von Diagnoseprotokollen für ein Azure Event Grid-Thema.
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: ff00c1438c49cbc9f9e67eba0cf0acef7991a5a4
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576450"
---
#  <a name="enable-diagnostic-logs-for-azure-event-grid-topics-or-domains"></a>Aktivieren von Diagnoseprotokollen für Azure Event Grid-Themen oder -Domänen
Dieser Artikel enthält Schrittanleitungen zum Aktivieren von Diagnoseeinstellungen für Event Grid-Themen oder Domänen.  Mit diesen Einstellungen können Sie Protokolle zu **Veröffentlichungs- und Übermittlungsfehlern** erfassen und anzeigen. 

## <a name="prerequisites"></a>Voraussetzungen

- Ein bereitgestelltes Event Grid-Thema
- Ein bereitgestelltes Ziel zum Erfassen von Diagnoseprotokollen Es kann eines der folgenden Ziele am gleichen Speicherort wie das Event Grid-Thema sein:
    - Azure-Speicherkonto
    - Event Hub
    - Log Analytics-Arbeitsbereich

## <a name="enable-diagnostic-logs-for-a-custom-topic"></a>Aktivieren von Diagnoseprotokollen für ein benutzerdefiniertes Thema

> [!NOTE]
> Das folgende Verfahren bietet eine schrittweise Anleitung zum Aktivieren von Diagnoseprotokollen für ein Thema. Schritte zum Aktivieren von Diagnoseprotokollen für eine Domäne sind sehr ähnlich. Navigieren Sie in Schritt 2 zur Event Grid-**Domäne** im Azure-Portal.  

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Navigieren Sie zum Event Grid-Thema, für das Sie Diagnoseprotokolleinstellungen aktivieren möchten. 
    1. Suchen Sie oben in der Suchleiste nach **Event Grid-Themen**. 
    
        ![Suchen nach benutzerdefinierten Themen](./media/enable-diagnostic-logs-topic/search-custom-topics.png)
    1. Wählen Sie in der Liste, für die Sie Diagnoseeinstellungen konfigurieren möchten, das **Thema** aus. 
1. Wählen Sie unter **Überwachung** im linken Menü die Option **Diagnoseeinstellungen** aus.
1. Wählen Sie auf der Seite **Diagnoseeinstellungen** den Eintrag **Diagnoseeinstellung hinzufügen** aus. 
    
    ![Schaltfläche „Diagnoseeinstellung hinzufügen“](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Geben Sie einen **Namen** für die Diagnoseeinstellung an. 
6. Wählen Sie im Abschnitt **Protokoll** die Optionen **DeliveryFailures** und **PublishFailures** aus. 
    ![Auswählen der Fehler](./media/enable-diagnostic-logs-topic/log-failures.png)
7. Aktivieren Sie mindestens eines der Erfassungsziele für die Protokolle, und konfigurieren Sie diese dann, indem Sie eine zuvor erstellte Erfassungsressource auswählen. 
    - Wenn Sie **In ein Speicherkonto archivieren** auswählen, wählen Sie **Speicherkonto - Konfigurieren** aus, und wählen Sie dann das Speicherkonto in Ihrem Azure-Abonnement aus. 

        ![Screenshot, der die Seite „Diagnoseeinstellungen“ mit aktivierter Option „In einem Azure-Speicherkonto archivieren“ sowie ein ausgewähltes Speicherkonto zeigt.](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Wenn Sie **An einen Event Hub streamen** auswählen, wählen Sie **Event Hub - Konfigurieren** aus, und wählen Sie dann den Event Hubs-Namespace, den Event Hub und die Zugriffsrichtlinie aus. 
        ![Screenshot, der die Seite „Diagnoseeinstellungen“ mit aktivierter Option „An einen Event Hub streamen“ zeigt.](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Wenn Sie **An Log Analytics senden** auswählen, wählen Sie den Log Analytics-Arbeitsbereich aus.
        ![Screenshot, der die Seite „Diagnoseeinstellungen“ mit aktivierter Option „An Log Analytics senden“ zeigt.](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
8. Wählen Sie **Speichern** aus. Wählen Sie dann in der rechten oberen Ecke **X** aus, um die Seite zu schließen. 
9. Vergewissern Sie sich nun auf der Seite **Diagnoseeinstellungen**, dass ein neuer Eintrag in der Tabelle **Diagnoseeinstellungen** angezeigt wird. 
    ![Screenshot, der die Seite „Diagnoseeinstellungen“ mit einem neuen Eintrag zeigt, der in der Tabelle „Diagnoseeinstellungen“ hervorgehoben ist.](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     Sie können auch die Erfassung aller Metriken für das Thema aktivieren. 

## <a name="enable-diagnostic-logs-for-a-system-topic"></a>Aktivieren von Diagnoseprotokollen für ein Systemthema

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Navigieren Sie zum Event Grid-Thema, für das Sie Diagnoseprotokolleinstellungen aktivieren möchten. 
    1. Suchen Sie oben in der Suchleiste nach **Event Grid-Systemthemen**. 
    
        ![Suchen nach Systemthemen](./media/enable-diagnostic-logs-topic/search-system-topics.png)
    1. Wählen Sie das **Systemthema** aus, für das Sie Diagnoseeinstellungen konfigurieren möchten. 
    
        ![Auswählen des Systemthemas](./media/enable-diagnostic-logs-topic/select-system-topic.png)
3. Wählen Sie im linken Menü **Diagnoseeinstellungen** unter **Überwachung** aus, und wählen Sie dann **Diagnoseeinstellung hinzufügen** aus. 

    ![Schaltfläche „Diagnoseeinstellungen hinzufügen“](./media/enable-diagnostic-logs-topic/system-topic-add-diagnostic-settings-button.png)
4. Geben Sie einen **Namen** für die Diagnoseeinstellung an. 
7. Wählen Sie im Abschnitt **Protokoll** die Option **DeliveryFailures** aus. 
    ![Auswählen von Übermittlungsfehlern](./media/enable-diagnostic-logs-topic/system-topic-select-delivery-failures.png)
6. Aktivieren Sie mindestens eines der Erfassungsziele für die Protokolle, und konfigurieren Sie diese dann, indem Sie eine zuvor erstellte Erfassungsressource auswählen. 
    - Wenn Sie **An Log Analytics senden** auswählen, wählen Sie den Log Analytics-Arbeitsbereich aus.
        ![An Log Analytics senden](./media/enable-diagnostic-logs-topic/system-topic-select-log-workspace.png) 
    - Wenn Sie **In ein Speicherkonto archivieren** auswählen, wählen Sie **Speicherkonto - Konfigurieren** aus, und wählen Sie dann das Speicherkonto in Ihrem Azure-Abonnement aus. 

        ![Archivieren in ein Azure-Speicherkonto](./media/enable-diagnostic-logs-topic/system-topic-select-storage-account.png)
    - Wenn Sie **An einen Event Hub streamen** auswählen, wählen Sie **Event Hub - Konfigurieren** aus, und wählen Sie dann den Event Hubs-Namespace, den Event Hub und die Zugriffsrichtlinie aus. 
        ![An einen Event Hub streamen](./media/enable-diagnostic-logs-topic/system-topic-select-event-hub.png)
8. Wählen Sie **Speichern** aus. Wählen Sie dann in der rechten oberen Ecke **X** aus, um die Seite zu schließen. 
9. Vergewissern Sie sich nun auf der Seite **Diagnoseeinstellungen**, dass ein neuer Eintrag in der Tabelle **Diagnoseeinstellungen** angezeigt wird. 
    ![Diagnoseeinstellung in der Liste](./media/enable-diagnostic-logs-topic/system-topic-diagnostic-settings-targets.png)

     Sie können auch die Erfassung aller **Metriken** für das Systemthema aktivieren.

    ![Systemthema: alle Metriken aktivieren](./media/enable-diagnostic-logs-topic/system-topics-metrics.png)

## <a name="view-diagnostic-logs-in-azure-storage"></a>Anzeigen von Diagnoseprotokollen in Azure Storage 

1. Nachdem Sie ein Speicherkonto als Erfassungsziel aktiviert haben, beginnt Event Grid mit der Ausgabe von Diagnoseprotokollen. Es sollten im Speicherkonto neue Container mit den Namen **insights-logs-deliveryfailures** und **insights-logs-publishfailures** angezeigt werden. 

    ![Speicher: Container für Diagnoseprotokolle](./media/enable-diagnostic-logs-topic/storage-containers.png)
2. Wenn Sie durch einen der Container navigieren, gelangen Sie zu einem Blob im JSON-Format. Die Datei enthält Protokolleinträge entweder für einen Zustellungs- oder Veröffentlichungsfehler. Der Navigationspfad stellt die **Ressourcen-ID** des Event Grid-Themas und den Zeitstempel (auf Minutenebene) dar, wann die Protokolleinträge ausgegeben wurden. Die Blob/JSON-Datei, die heruntergeladen werden kann, hält sich letztlich an das im nächsten Abschnitt beschriebene Schema. 

    [ ![JSON-Datei im Speicher](./media/enable-diagnostic-logs-topic/select-json.png) ](./media/enable-diagnostic-logs-topic/select-json.png)
3. Sie sollten den Inhalt in der JSON-Datei ähnlich wie im folgenden Beispiel sehen: 

    ```json
    {
        "time": "2019-11-01T00:17:13.4389048Z",
        "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
        "eventSubscriptionName": "SAMPLEDESTINATION",
        "category": "DeliveryFailures",
        "operationName": "Deliver",
        "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
    }
    ```
## <a name="next-steps"></a>Nächste Schritte
Informationen zum Protokollschema und andere konzeptionelle Informationen zu Diagnoseprotokollen für Themen oder Domänen finden Sie unter [Diagnoseprotokolle](diagnostic-logs.md).
