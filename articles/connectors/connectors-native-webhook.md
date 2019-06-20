---
title: Erstellen ereignisbasierter Workflows oder Aktionen – Azure Logic Apps | Microsoft-Dokumentation
description: Automatisieren ereignisbasierter Workflows oder Aktionen mithilfe von Webhooks und Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.topic: article
tags: connectors
ms.date: 07/21/2016
ms.openlocfilehash: c3047000843e054e71ec1a80313118a25e7c4905
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60447199"
---
# <a name="create-event-based-workflows-or-actions-by-using-webhooks-and-azure-logic-apps"></a>Erstellen ereignisbasierter Workflows oder Aktionen mithilfe von Webhooks und Azure Logic Apps

Mit der Webhookaktion und dem Webhooktrigger können Sie Flows starten, anhalten und fortsetzen, um die folgenden Aufgaben auszuführen:

* Auslösen über [Azure Event Hub](https://github.com/logicappsio/EventHubAPI), wenn ein Element empfangen wird
* Warten auf eine Genehmigung, bevor ein Workflow fortgesetzt wird

Weitere Informationen zum [Erstellen von benutzerdefinierten APIs, die einen Webhook unterstützen](../logic-apps/logic-apps-create-api-app.md).

## <a name="use-the-webhook-trigger"></a>Verwenden des Webhooktriggers

Ein [*Trigger*](../connectors/apis-list.md) ist ein Ereignis, das einen Logik-App-Workflow startet. Der Webhooktrigger ist ereignisbasiert und erfordert nicht das Abrufen neuer Elemente. Wenn Sie Ihre Logik-App mit einem Webhooktrigger speichern oder Ihre Logik-App von „deaktiviert“ in „aktiviert“ ändern, *abonniert* der Webhooktrigger den angegebenen Dienst oder Endpunkt, indem er eine *Rückruf-URL* mit diesem Dienst oder Endpunkt registriert. Der Trigger verwendet diese URL dann für die Ausführung der Logik-App nach Bedarf. Die Logik-App wird wie der [Anforderungstrigger](connectors-native-reqres.md) in dem Moment ausgelöst, in dem ein Ereignis eintritt. Der Trigger *hebt das Abonnement auf*, wenn Sie ihn entfernen und Ihre Logik-App speichern oder wenn Sie Ihre Logik-App von „aktiviert“ in „deaktiviert“ ändern.

Im Anschluss finden Sie ein Beispiel für die Einrichtung eines HTTP-Triggers im Logik-App-Designer. In den Schritten wird davon ausgegangen, dass Sie bereits eine API bereitgestellt haben oder auf eine API zugreifen, die dem [Muster zum Abonnieren und Abbestellen von Webhooks in Logik-Apps folgt](../logic-apps/logic-apps-create-api-app.md#webhook-triggers). 

**So fügen Sie den Webhooktrigger hinzu**

1. Fügen Sie den Trigger **HTTP-Webhook** als ersten Schritt in einer Logik-App hinzu.
2. Geben Sie die Parameter für die Aufrufe zum Abonnieren und Abbestellen des Webhooks ein.

   Für diesen Schritt gilt dasselbe Muster wie beim Format für die [HTTP-Aktion](connectors-native-http.md).

     ![HTTP-Trigger](./media/connectors-native-webhook/using-trigger.png)

3. Fügen Sie mindestens eine Aktion hinzu.
4. Klicken Sie auf **Speichern**, um die Logik-App zu veröffentlichen. Durch diesen Schritt wird der Abonnementendpunkt mit der Rückruf-URL aufgerufen, die zum Auslösen dieser Logik-App erforderlich ist.
5. Immer wenn der Dienst `HTTP POST` an die Rückruf-URL sendet, wird die Logik-App ausgelöst, und sie enthält alle Daten, die in der Anforderung übergeben wurden.

## <a name="use-the-webhook-action"></a>Verwenden der Webhookaktion

Ein [*Aktion*](../connectors/apis-list.md) ist ein definierter Vorgang, der vom Workflow Ihrer Logik-App ausgeführt wird. Wenn eine Logik-App eine Webhookaktion ausführt, *abonniert* die Aktion den angegebenen Dienst oder Endpunkt durch die Registrierung einer *Rückruf-URL* mit diesem Dienst oder Endpunkt. Die Webhookaktion wartet dann, bis der Dienst die URL aufruft, bevor die Ausführung der Logik-App fortgesetzt wird. Die Logik-App beendet das Abonnement für den Dienst oder Endpunkt in folgenden Fällen: 

* Wenn die Webhookaktion erfolgreich abgeschlossen wurde
* Wenn die Logik-App während des Wartens auf eine Antwort abgebrochen wird
* Bevor Ein Timeout für die Logik-App auftritt

Die Aktion zum [**Senden einer Genehmigungs-E-Mail**](connectors-create-api-office365-outlook.md) ist ein Beispiel für eine Webhookaktion, die diesem Muster folgt. Sie können dieses Muster über die Webhookaktion auf jeden Dienst erweitern. 

Im Anschluss finden Sie ein Beispiel für die Einrichtung einer Webhookaktion im Logik-App-Designer. In diesen Schritten wird davon ausgegangen, dass Sie bereits eine API bereitgestellt haben oder auf eine API zugreifen, die dem [in Logik-Apps verwendeten Muster zum Abonnieren und Abbestellen von Webhooks Apps folgt](../logic-apps/logic-apps-create-api-app.md#webhook-actions). 

**So fügen Sie eine Webhookaktion hinzu**

1. Wählen Sie **Neuer Schritt** > **Aktion hinzufügen** aus.

2. Geben Sie im Suchfeld die Zeichenfolge „Webhook“ ein, um die Aktion **HTTP-Webhook** zu suchen.

    ![Auswählen der Abfrageaktion](./media/connectors-native-webhook/using-action-1.png)

3. Geben Sie die Parameter für die Aufrufe zum Abonnieren und Abbestellen des Webhooks ein.

   Für diesen Schritt gilt dasselbe Muster wie beim Format für die [HTTP-Aktion](connectors-native-http.md).

     ![Abschließen der Abfrageaktion](./media/connectors-native-webhook/using-action-2.png)
   
   Zur Laufzeit ruft die Logik-App nach Erreichen dieses Schritts den Abonnementendpunkt auf.

4. Klicken Sie auf **Speichern**, um die Logik-App zu veröffentlichen.

## <a name="technical-details"></a>Technische Details

Im Folgenden finden Sie weitere Details zu den von Webhooks unterstützten Triggern und Aktionen.

## <a name="webhook-triggers"></a>Webhooktrigger

| Aktion | BESCHREIBUNG |
| --- | --- |
| HTTP-Webhook |Abonnieren Sie eine Rückruf-URL für einen Dienst, der die URL aufrufen kann, um die Logik-App nach Bedarf auszulösen. |

### <a name="trigger-details"></a>Triggerdetails

#### <a name="http-webhook"></a>HTTP-Webhook

Abonnieren Sie eine Rückruf-URL für einen Dienst, der die URL aufrufen kann, um die Logik-App nach Bedarf auszulösen.
Mit einem * gekennzeichnete Felder sind Pflichtfelder.

| Anzeigename | Eigenschaftenname | BESCHREIBUNG |
| --- | --- | --- |
| Abonnieren: Methode* |method |Für die Abonnementanforderung zu verwendende HTTP-Methode |
| Abonnieren: URI* |uri |Für die Abonnementanforderung zu verwendender HTTP-URI |
| Abbestellen: Methode* |method |Für die Abbestellanforderung zu verwendende HTTP-Methode |
| Abbestellen: URI* |uri |Für die Abbestellanforderung zu verwendender HTTP-URI |
| Abonnieren: Text |body |HTTP-Anforderungstext für das Abonnement |
| Abonnieren: Header |headers |HTTP-Anforderungsheader für das Abonnement |
| Abonnieren: Authentifizierung |authentication |HTTP-Authentifizierung für das Abonnement. Für weitere Details siehe [HTTP-Connector](connectors-native-http.md#authentication). |
| Abbestellen: Text |body |HTTP-Anforderungstext für die Abbestellung |
| Abbestellen: Header |headers |HTTP-Anforderungsheader für die Abbestellung |
| Abbestellen: Authentifizierung |authentication |HTTP-Authentifizierung für die Abbestellung. Für weitere Details siehe [HTTP-Connector](connectors-native-http.md#authentication). |

**Ausgabedetails**

Webhookanforderung

| Eigenschaftenname | Datentyp | BESCHREIBUNG |
| --- | --- | --- |
| headers |Objekt |Webhookanforderungsheader |
| Body |object |Webhookanforderungsobjekt |
| Statuscode |int |Statuscode der Webhookanforderung |

## <a name="webhook-actions"></a>Webhookaktionen

| Aktion | BESCHREIBUNG |
| --- | --- |
| HTTP-Webhook |Abonnieren Sie eine Rückruf-URL für einen Dienst, der die URL aufrufen kann, um einen Workflowschritt nach Bedarf fortzusetzen. |

### <a name="action-details"></a>Aktionsdetails

#### <a name="http-webhook"></a>HTTP-Webhook

Abonnieren Sie eine Rückruf-URL für einen Dienst, der die URL aufrufen kann, um einen Workflowschritt nach Bedarf fortzusetzen.
Mit einem * gekennzeichnete Felder sind Pflichtfelder.

| Anzeigename | Eigenschaftenname | BESCHREIBUNG |
| --- | --- | --- |
| Abonnieren: Methode* |method |Für die Abonnementanforderung zu verwendende HTTP-Methode |
| Abonnieren: URI* |uri |Für die Abonnementanforderung zu verwendender HTTP-URI |
| Abbestellen: Methode* |method |Für die Abbestellanforderung zu verwendende HTTP-Methode |
| Abbestellen: URI* |uri |Für die Abbestellanforderung zu verwendender HTTP-URI |
| Abonnieren: Text |body |HTTP-Anforderungstext für das Abonnement |
| Abonnieren: Header |headers |HTTP-Anforderungsheader für das Abonnement |
| Abonnieren: Authentifizierung |authentication |HTTP-Authentifizierung für das Abonnement. Für weitere Details siehe [HTTP-Connector](connectors-native-http.md#authentication). |
| Abbestellen: Text |body |HTTP-Anforderungstext für die Abbestellung |
| Abbestellen: Header |headers |HTTP-Anforderungsheader für die Abbestellung |
| Abbestellen: Authentifizierung |authentication |HTTP-Authentifizierung für die Abbestellung. Für weitere Details siehe [HTTP-Connector](connectors-native-http.md#authentication). |

**Ausgabedetails**

Webhookanforderung

| Eigenschaftenname | Datentyp | BESCHREIBUNG |
| --- | --- | --- |
| headers |Objekt |Webhookanforderungsheader |
| Body |object |Webhookanforderungsobjekt |
| Statuscode |int |Statuscode der Webhookanforderung |

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Andere Connectors](apis-list.md)
