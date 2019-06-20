---
title: Protokollieren von Ereignissen in Azure Event Hubs mit Azure API Management | Microsoft Docs
description: Erfahren Sie, wie Sie mit Azure API Management Ereignisse in Azure Event Hubs protokollieren.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 14f84b5380a1c106114cdab425de7f69f4e19825
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60657542"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Protokollieren von Ereignissen in Azure Event Hubs mit Azure API Management
Azure Event Hubs ist ein hochgradig skalierbarer Dateneingangsdienst, der Millionen von Ereignissen pro Sekunde erfassen kann. Auf diese Weise können Sie riesige Datenmengen verarbeiten und analysieren, die von vernetzten Geräten und Anwendungen erzeugt werden. Event Hubs fungiert als „Eingangstür“ für eine Ereignispipeline. Nach der Erfassung in Event Hubs können Sie Daten mit einem beliebigen Echtzeit-Analyseanbieter oder mit Batchverarbeitungs-/Speicheradaptern umwandeln und speichern. Event Hubs entkoppelt die Erzeugung eines Datenstroms von Ereignissen von der Nutzung dieser Ereignisse, sodass  Ereignisconsumer nach einem eigenen Zeitplan auf Ereignisse zugreifen können.

Dies ist ein Begleitartikel zum Video [Integrate Azure API Management with Event Hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) (Integrieren von Azure API Management in Event Hubs; in englischer Sprache). Hier wird beschrieben, wie API Management-Ereignisse mithilfe von Azure Event Hubs protokolliert werden.

## <a name="create-an-azure-event-hub"></a>Erstellen eines Azure Event Hubs

Ausführliche Schritte zum Erstellen eines Event Hubs und zum Abrufen von Verbindungszeichenfolgen, die Sie zum Senden und Empfangen von Ereignis aus dem bzw. im Event Hub benötigen, finden Sie unter [Erstellen eines Event Hubs-Namespace und eines Event Hubs mithilfe des Azure-Portals](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-api-management-logger"></a>Erstellen eines API Management-Loggers
Der Event Hub ist nun vorhanden. Der nächste Schritt besteht darin, einen [Logger](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) im API Management-Dienst zu konfigurieren, sodass Ereignisse im Event Hub protokolliert werden können.

API Management-Logger werden mit der [API Management-REST-API](https://aka.ms/smapi)konfiguriert. Machen Sie sich vor der erstmaligen Verwendung der REST-API mit den [Voraussetzungen](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest) vertraut, und stellen Sie sicher, dass der [Zugriff auf die REST- API aktiviert wurde](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Um die Protokollierung zu erstellen, senden Sie mithilfe der folgenden URL-Vorlage eine HTTP PUT-Anforderung:

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2017-03-01`

* Ersetzen Sie `{your service}` durch den Namen Ihrer API Management-Dienstinstanz.
* Ersetzen Sie `{new logger name}` durch den gewünschten Namen für den neuen Logger. Sie verweisen auf diesen Namen, wenn Sie die [log-to-eventhub](/azure/api-management/api-management-advanced-policies#log-to-eventhub)-Richtlinie konfigurieren.

Fügen Sie der Anforderung die folgenden Header hinzu:

* Content-Type: application/json
* Autorisierung: SharedAccessSignature 58...
  * Anweisungen zum Generieren von `SharedAccessSignature` finden Sie unter [Authentifizierung für Azure API Management-REST-API](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Geben Sie den Anforderungstext mithilfe der folgenden Vorlage ein:

```json
{
  "loggerType" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `loggerType` muss auf `AzureEventHub` festgelegt werden.
* `description` stellt eine optionale Beschreibung des Loggers bereit und kann auf Wunsch auch leer gelassen werden (als Zeichenfolge mit der Länge null).
* `credentials` enthält den `name` und die `connectionString` Ihres Azure Event Hubs.

Wenn Sie die Anforderung senden und die Protokollierung daraufhin erstellt wurde, wird der Statuscode `201 Created` zurückgegeben. Eine Beispielantwort basierend auf der oben angegebenen Beispielanforderung ist unten dargestellt.

```json
{
    "id": "/loggers/{new logger name}",
    "loggerType": "azureEventHub",
    "description": "Sample logger description",
    "credentials": {
        "name": "Name of the Event Hub from the Portal",
        "connectionString": "{{Logger-Credentials-xxxxxxxxxxxxxxx}}"
    },
    "isBuffered": true,
    "resourceId": null
}
```

> [!NOTE]
> Informationen über weitere mögliche Rückgabecodes und die Gründe dafür erhalten Sie unter [Erstellen eines Loggers](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Die Vorgehensweisen für weitere Vorgänge, z.B. zum Auflisten, Aktualisieren und Löschen, finden Sie in der Dokumentation zur Entität [Logger](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity).
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Konfigurieren von log-to-eventhub-Richtlinien

Nachdem Sie den Logger in API Management konfiguriert haben, können Sie die Richtlinien zum Protokollieren im Event Hub („log-to-eventhub-Richtlinien“) für die gewünschten Ereignisse konfigurieren. Die log-to-eventhub-Richtlinie kann im Abschnitt mit Richtlinien für eingehenden Datenverkehr oder im Abschnitt mit Richtlinien für ausgehenden Datenverkehr verwendet werden.

1. Navigieren Sie zu Ihrer APIM-Instanz.
2. Wählen Sie die Registerkarte „API“ aus.
3. Wählen Sie die API aus, der Sie die Richtlinie hinzufügen möchten. In diesem Beispiel fügen wir die Richtlinie zur **Echo-API** im Produkt **Unlimited** hinzu.
4. Wählen Sie **Alle Vorgänge** aus.
5. Klicken Sie im oberen Seitenbereich auf die Registerkarte „Entwurf“.
6. Klicken Sie im Fenster „Eingehende Verarbeitung“ bzw. „Ausgehende Verarbeitung“ auf das Dreieck (neben dem Stift).
7. Wählen Sie den Code-Editor aus. Weitere Informationen finden Sie unter [Einrichten oder Bearbeiten von Richtlinien](set-edit-policies.md).
8. Positionieren Sie den Cursor im Richtlinienabschnitt `inbound` oder `outbound`.
9. Wählen Sie im Fenster rechts die Optionen **Erweiterte Richtlinien** > **Bei EventHub anmelden** aus. Damit wird die Richtlinienanweisungsvorlage `log-to-eventhub` eingefügt.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Ersetzen Sie `logger-id` durch den Wert, den Sie im vorherigen Schritt in der URL für `{new logger name}` verwendet haben, um die Protokollierung zu erstellen.

Sie können jeden Ausdruck verwenden, der eine Zeichenfolge als Wert für das Element `log-to-eventhub` zurückgibt. In diesem Beispiel wird eine Zeichenfolge protokolliert, die das Datum, die Uhrzeit, den Dienstnamen, die Anforderungs-ID, die IP-Adresse der Anforderung und den Vorgangsnamen enthält.

Klicken Sie auf **Speichern** , um die aktualisierte Richtlinienkonfiguration zu speichern. Die Richtlinie ist sofort nach dem Speichern aktiv, und im vorgesehenen Event Hub werden Ereignisse protokolliert.

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zu Azure Event Hubs
  * [Erste Schritte mit Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Empfangen von Nachrichten mit EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Programmierleitfaden für Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Erfahren Sie mehr über die Integration der API-Verwaltung und Event Hubs
  * [Verweis zu Protokollierungstool](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [log-to-eventhub policy reference](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Überwachen von APIs mit Azure API Management, Event Hubs und Moesif](api-management-log-to-eventhub-sample.md)  
* Erfahren Sie mehr über die [Integration in Azure Application Insights](api-management-howto-app-insights.md).

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
