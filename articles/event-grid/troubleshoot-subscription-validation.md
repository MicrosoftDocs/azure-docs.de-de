---
title: Azure Event Grid – Problembehandlung bei der Abonnementüberprüfung
description: In diesem Artikel erfahren Sie, wie Sie Probleme bei Abonnementüberprüfungen beheben können.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 857760182675d5673a3b09495c2faaf7372a4164
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592939"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>Problembehandlung bei der Abonnementüberprüfung für Azure Event Grid
Wenn bei der Erstellung eines Ereignisabonnements eine Fehlermeldung wie `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation` angezeigt wird, weist dies darauf hin, dass im Überprüfungshandshake ein Fehler aufgetreten ist. Überprüfen Sie Folgendes, um diesen Fehler zu beheben:

- Führen Sie einen HTTP POST-Aufruf an Ihre Webhook-URL mit dem Beispielanforderungstext [SubscriptionValidationEvent](webhook-event-delivery.md#validation-details) unter Verwendung von Postman oder curl oder einem ähnlichen Tool aus.
- Wenn Ihr Webhook einen Handshake-Mechanismus mit synchroner Überprüfung implementiert, überprüfen Sie, ob der Überprüfungscode als Teil der Antwort zurückgegeben wird.
- Wenn Ihr Webhook einen Handshake-Mechanismus mit asynchroner Überprüfung implementiert, überprüfen Sie, ob Ihr „HTTP POST“-Aufruf „200 OK“ zurückgibt.
- Wenn Ihr Webhook `403 (Forbidden)` in der Antwort zurückgibt, überprüfen Sie, ob sich Ihr Webhook hinter einem Azure Application Gateway oder einer Web Application Firewall befindet. Wenn dies der Fall ist, müssen Sie diese Firewallregeln deaktivieren und erneut einen HTTP POST-Aufruf ausführen:
    - 920300 (Fehlender Accept-Header für Anforderung)
    - 942430 (Eingeschränkte Anomalieerkennung für SQL-Zeichen (Argumente): Anzahl von Sonderzeichen überschritten (12))
    - 920230 (Mehrere URL-Codierungen erkannt)
    - 942130 (Angriff mit Einschleusung von SQL-Befehlen: SQL-Tautologie erkannt.)
    - 931130 (Möglicher RFI-Angriff (Remote File Inclusion) = Domänenexterner Verweis/Link)

> [!IMPORTANT]
> Ausführliche Informationen zur Endpunktüberprüfung für Webhooks finden Sie unter [Webhook-Ereignisbereitstellung](webhook-event-delivery.md).

In den folgenden Abschnitten wird gezeigt, wie Sie mithilfe von Postman und Curl ein Ereignisabonnement überprüfen.  

## <a name="validate-event-grid-event-subscription-using-postman"></a>Überprüfen eines Event Grid-Ereignisabonnements mithilfe von Postman
Im folgenden Beispiel erfahren Sie, wie Sie ein Webhookabonnement eines Event Grid-Ereignisses mit Postman überprüfen: 

![Überprüfen eines Event Grid-Ereignisabonnements mithilfe von Postman](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

Hier ein Beispiel für die Verwendung von **SubscriptionValidationEvent** in JSON:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Nachstehend die erfolgreiche Antwort auf den Beispielcode:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Weitere Informationen zur Event Grid-Ereignisüberprüfung für Webhooks finden Sie unter [Endpunktüberprüfung mit Event Grid-Ereignissen](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).


## <a name="validate-event-grid-event-subscription-using-curl"></a>Überprüfen eines Event Grid-Ereignisabonnements mithilfe von Curl 
Mit dem Curl-Befehl im folgenden Beispiel können Sie ein Webhookabonnement eines Event Grid-Ereignisses überprüfen: 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="validate-cloud-event-subscription-using-postman"></a>Überprüfen eines Cloudereignisabonnements mithilfe von Postman
Im folgenden Beispiel erfahren Sie, wie Sie ein Webhookabonnement eines Cloudereignisses mit Postman überprüfen: 

![Überprüfen eines Cloudereignisabonnements mithilfe von Postman](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

Verwenden Sie die **HTTP OPTIONS**-Methode für die Überprüfung mit Cloudereignissen. Weitere Informationen zur Cloudereignisüberprüfung für Webhooks finden Sie unter [Endpunktüberprüfung mit Cloudereignissen](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).

## <a name="troubleshoot-event-subscription-validation"></a>Problembehandlung bei der Überprüfung von Ereignisabonnements

## <a name="next-steps"></a>Nächste Schritte
Wenn Sie weitere Hilfe benötigen, veröffentlichen Sie Ihr Problem im [Stack Overflow-Forum](https://stackoverflow.com/questions/tagged/azure-eventgrid), oder öffnen Sie ein [Supportticket](https://azure.microsoft.com/support/options/). 
