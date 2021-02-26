---
title: Empfangen und Beantworten von Aufrufen mit HTTPS
description: Hier erfahren Sie, wie Sie eingehende HTTPS-Anforderungen externer Dienste mit Azure Logic Apps behandeln.
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 11/19/2020
tags: connectors
ms.openlocfilehash: 83ffccb7bae4fabc10796c36e782e72c661bd346
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063011"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Empfangen und Beantworten eingehender HTTPS-Anforderungen in Azure Logic Apps

Mit [Azure Logic Apps](../logic-apps/logic-apps-overview.md) und dem integrierten Anforderungstrigger und der Antwortaktion können Sie automatisierte Tasks und Workflows erstellen, die eingehende HTTPS-Anforderungen über HTTPS empfangen können. Verwenden Sie zum Senden von ausgehenden Anforderungen stattdessen den integrierten [HTTP-Trigger oder die HTTP-Aktion](../connectors/connectors-native-http.md).

Beispielsweise können Sie mit Ihrer Logik-App Folgendes durchführen:

* Empfangen von und Antworten auf eine HTTPS-Anforderung von Daten in einer lokalen Datenbank.

* Auslösen eines Workflows, wenn ein externes Webhookereignis auftritt

* Empfangen von und Antworten auf HTTPS-Aufrufe aus einer anderen Logik-App.

Dieser Artikel zeigt, wie Sie den Anforderungstrigger und die Antwortaktion verwenden können, damit Ihre Logik-App eingehende Aufrufe empfangen und darauf antworten kann.

Weitere Informationen zu Sicherheit, Autorisierung und Verschlüsselung für eingehende Aufrufe Ihrer Logik-App, etwa über [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) (früher bekannt als Secure Sockets Layer [SSL]) oder [Azure Active Directory Open Authentication (Azure AD OAuth)](../active-directory/develop/index.yml), finden Sie unter [Sicherer Zugriff und Daten: Zugriff für eingehende Aufrufe anforderungsbasierter Trigger](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests). In diesem Artikel erfahren Sie auch, wie Sie Ihre Logik-App mit Azure API Management verfügbar machen oder IP-Adressen aus eingehenden Aufrufen einschränken.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Grundlegende Kenntnisse über das [Erstellen von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Anforderungstrigger hinzufügen

Dieser integrierte Trigger erstellt einen manuell aufrufbaren Endpunkt, der *nur* eingehende Anforderungen über HTTPS verarbeiten kann. Wenn ein Aufrufer eine Anforderung an diesen Endpunkt sendet, wird der [Anforderungstrigger](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) ausgelöst und führt die Logik-App aus. Weitere Informationen zum Aufrufen dieses Triggers finden Sie unter [Aufrufen, Auslösen oder Schachteln von Workflows mit HTTPS-Endpunkten in Azure Logic Apps](../logic-apps/logic-apps-http-endpoint.md).

Ihre Logik-App hält eine eingehende Anforderung nur für [begrenzte Zeit](../logic-apps/logic-apps-limits-and-config.md#http-limits) geöffnet. Unter der Annahme, dass Ihre Logik-App eine [Antwortaktion](#add-response) enthält, gibt Ihre Logik-App einen `504 GATEWAY TIMEOUT`-Status an den Aufrufer zurück, wenn Ihre Logik-App nach Ablauf dieses Zeitraums keine Antwort an den Aufrufer sendet. Wenn Ihre Logik-App keine Antwortaktion enthält, gibt sie sofort einen `202 ACCEPTED`-Status an den Aufrufer zurück.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Erstellen einer leeren Logik-App

1. Wenn der Designer für Logik-Apps geöffnet wird, geben Sie im Suchfeld `http request` als Filter ein. Wählen Sie aus der Triggerliste diesen Trigger aus: **Beim Empfang einer HTTP-Anforderung**.

   ![Anforderungstrigger auswählen](./media/connectors-native-reqres/select-request-trigger.png)

   Der Anforderungstrigger zeigt diese Eigenschaften an:

   ![Anforderungstrigger](./media/connectors-native-reqres/request-trigger.png)

   | Eigenschaftenname | JSON-Eigenschaftenname | Erforderlich | BESCHREIBUNG |
   |---------------|--------------------|----------|-------------|
   | **HTTP-POST-URL** | {keine} | Ja | Die Endpunkt-URL, die nach dem Speichern der Logik-App generiert wird und zum Aufrufen ihrer Logik-App verwendet wird |
   | **JSON-Schema für Anforderungstext** | `schema` | Nein | Das JSON-Schema, das die Eigenschaften und Werte im Text der eingehenden Anforderung beschreibt |
   |||||

1. Geben Sie im Feld **JSON-Schema für Anforderungstext** optional ein JSON-Schema ein, das den Text in der eingehenden Anforderung beschreibt. Beispiel:

   ![Beispiel für das JSON-Schema](./media/connectors-native-reqres/provide-json-schema.png)

   Der Designer verwendet dieses Schema zum Generieren von Token für die Eigenschaften in der Anforderung. Auf diese Weise kann Ihre Logik-App Daten aus der Anforderung über den Trigger analysieren, nutzen und an den Workflow übergeben.

   Hier sehen Sie das Beispielschema:

   ```json
   {
      "type": "object",
      "properties": {
         "account": {
            "type": "object",
            "properties": {
               "name": {
                  "type": "string"
               },
               "ID": {
                  "type": "string"
               },
               "address": {
                  "type": "object",
                  "properties": {
                     "number": {
                        "type": "string"
                     },
                     "street": {
                        "type": "string"
                     },
                     "city": {
                        "type": "string"
                     },
                     "state": {
                        "type": "string"
                     },
                     "country": {
                        "type": "string"
                     },
                     "postalCode": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      }
   }
   ```

   Wenn Sie ein JSON-Schema eingeben, zeigt der Designer eine Erinnerung an, dass der `Content-Type`-Header in die Anforderung eingeschlossen und der Headerwert auf `application/json` festgelegt werden muss. Weitere Informationen finden Sie unter [Behandeln von Inhaltstypen](../logic-apps/logic-apps-content-type.md).

   ![Erinnerung zum Einschließen des Headers „Content-Type“](./media/connectors-native-reqres/include-content-type.png)

   Dieser Header sieht im JSON-Format wie folgt aus:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   Zum Generieren eines JSON-Schemas, das auf der erwarteten Nutzlast (Daten) basiert, können Sie ein Tool wie z. B. [JSONSchema.net](https://jsonschema.net) verwenden oder die folgenden Schritte ausführen:

   1. Wählen Sie im Anforderungstrigger **Beispielnutzlast zum Generieren eines Schemas verwenden** aus.

      ![Screenshot mit ausgewählter Option „Beispielnutzdaten zum Generieren eines Schemas verwenden“](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Geben Sie die Beispielnutzlast ein, und wählen Sie dann **Fertig** aus.

      ![Eingeben einer Beispielnutzlast zum Generieren eines Schemas](./media/connectors-native-reqres/enter-payload.png)

      Hier sehen Sie die Beispielnutzlast:

      ```json
      {
         "account": {
            "name": "Contoso",
            "ID": "12345",
            "address": {
               "number": "1234",
               "street": "Anywhere Street",
               "city": "AnyTown",
               "state": "AnyState",
               "country": "USA",
               "postalCode": "11111"
            }
         }
      }
      ```

1. Gehen Sie folgendermaßen vor, um zu überprüfen, ob der eingehende Aufruf einen Anforderungstext enthält, der dem angegebenen Schema entspricht:

   1. Klicken Sie auf der Titelleiste des Anforderungstriggers auf die Schaltfläche mit den Auslassungspunkten ( **...** ).

   1. Aktivieren Sie in den Einstellungen des Triggers die **Schemavalidierung**, und wählen Sie **Fertig** aus.

      Wenn der Anforderungstext des eingehenden Aufrufs nicht mit dem Schema identisch ist, gibt der Trigger eine `HTTP 400 Bad Request`-Fehlermeldung zurück.

1. Öffnen Sie zum Hinzufügen weiterer Eigenschaften die Liste **Neuen Parameter hinzufügen**, und wählen Sie die Parameter aus, die hinzugefügt werden sollen.

   | Eigenschaftenname | JSON-Eigenschaftenname | Erforderlich | BESCHREIBUNG |
   |---------------|--------------------|----------|-------------|
   | **Methode** | `method` | Nein | Die Methode, die die eingehende Anforderung zum Aufrufen der Logik-App verwenden muss |
   | **Relativer Pfad** | `relativePath` | Nein | Der relative Pfad für den Parameter, der von der Endpunkt-URL der Logik-App akzeptiert werden kann |
   |||||

   In diesem Beispiel wird die Eigenschaft **Methode** hinzugefügt:

   ![Hinzufügen des Methodenparameters](./media/connectors-native-reqres/add-parameters.png)

   Die Eigenschaft **Methode** wird im Trigger angezeigt, sodass Sie eine Methode aus der Liste auswählen können.

   ![Wählen Sie die Methode aus.](./media/connectors-native-reqres/select-method.png)

1. Fügen Sie nun eine weitere Aktion als nächsten Schritt in Ihrem Workflow hinzu. Wählen Sie unter dem Trigger die Option **Nächster Schritt** aus, um die Aktion zu finden, die Sie hinzufügen möchten.

   Beispielsweise können Sie auf die Anforderung reagieren, indem Sie eine [Antwortaktion hinzufügen](#add-response), um damit eine benutzerdefinierte Antwort zurückzugeben. Dies wird weiter unten in diesem Thema beschrieben.

   Ihre Logik-App hält die eingehende Anforderung nur für [begrenzte Zeit](../logic-apps/logic-apps-limits-and-config.md#http-limits) geöffnet. Falls der Logik-App-Workflow eine Antwortaktion enthält, gibt Ihre Logik-App ein `504 GATEWAY TIMEOUT` an den Aufrufer zurück, wenn die Logik-App nach diesem Zeitraum nicht geantwortet hat. Falls Ihre Logik-App keine Antwortaktion enthält, gibt Ihre Logik-App sofort eine `202 ACCEPTED`-Antwort an den Aufrufer zurück.

1. Wenn Sie fertig sind, speichern Sie Ihre Logik-App. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

   Dieser Schritt generiert die URL, die zum Senden der Anforderung verwendet werden soll, durch die die Logik-App ausgelöst wird. Um diese URL zu kopieren, wählen Sie das Kopiersymbol neben der URL aus.

   ![URL zum Auslösen Ihrer Logik-App](./media/connectors-native-reqres/generated-url.png)

   > [!NOTE]
   > Wenn Sie das Hash- oder Nummernzeichen ( **#** ) im URI verwenden möchten, wenn Sie einen Aufruf an den Anforderungstrigger senden, nutzen Sie stattdessen diese codierte Version: `%25%23`.

1. Senden Sie zum Testen Ihrer Logik-App eine HTTP-Anforderung an die generierte URL.

   Zum Senden der HTTP-Anforderung kann beispielsweise ein Tool wie [Postman](https://www.getpostman.com/) verwendet werden. Weitere Informationen zur zugrunde liegenden JSON-Definition des Triggers und zum Aufzurufen dieses Triggers finden Sie in den Themen [Anforderungstriggertyp](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) und unter [Aufrufen, Auslösen oder Schachteln von Workflows mit HTTP-Endpunkten in Azure Logic Apps](../logic-apps/logic-apps-http-endpoint.md).

Weitere Informationen zu Sicherheit, Autorisierung und Verschlüsselung für eingehende Aufrufe Ihrer Logik-App, etwa über [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) (früher bekannt als Secure Sockets Layer [SSL]) oder [Azure Active Directory Open Authentication (Azure AD OAuth)](../active-directory/develop/index.yml), finden Sie unter [Sicherer Zugriff und Daten: Zugriff für eingehende Aufrufe anforderungsbasierter Trigger](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests). In diesem Artikel erfahren Sie auch, wie Sie Ihre Logik-App mit Azure API Management verfügbar machen oder IP-Adressen aus eingehenden Aufrufen einschränken.

## <a name="trigger-outputs"></a>Triggerausgaben

Im Folgenden finden Sie weitere Informationen zu den Ausgaben des Anforderungstriggers:

| JSON-Eigenschaftenname | Datentyp | BESCHREIBUNG |
|--------------------|-----------|-------------|
| `headers` | Object | Ein JSON-Objekt, das die Header aus der Anforderung beschreibt |
| `body` | Object | Ein JSON-Objekt, das den Textinhalt aus der Anforderung beschreibt |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Hinzufügen einer Antwortaktion

Wenn Sie den Anforderungstrigger zur Verarbeitung eingehender Anforderungen verwenden, können Sie die Antwort modellieren und die Nutzlastergebnisse mithilfe der integrierten [Antwortaktion](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action) an den Aufrufer zurücksenden. Sie können die Antwortaktion *nur* mit dem Anforderungstrigger verwenden. Diese Kombination aus dem Anforderungstrigger und der Antwortaktion erstellt die [Anforderungs-/Antwortmuster](https://en.wikipedia.org/wiki/Request%E2%80%93response). Außer innerhalb von Foreach-Schleifen und Until-Schleifen sowie parallelen Branches können Sie die Antwortaktion an beliebiger Stelle in Ihren Workflow hinzufügen.

> [!IMPORTANT]
> Wenn eine Antwortaktion diese Header enthält, entfernt Logic Apps sie aus der generierten Antwortnachricht, ohne eine Warnung oder einen Fehler anzuzeigen:
>
> * `Allow`
> * `Content-*`-Header mit Ausnahme von `Content-Disposition`, `Content-Encoding` und `Content-Type`, wenn Sie POST- und PUT-Vorgänge verwenden, aber nicht für GET-Vorgänge
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Logic Apps verhindert nicht, dass Sie Logik-Apps speichern, in denen eine Antwortaktion mit diesen Headern verwendet wird, sondern ignoriert diese Header.

1. Wählen Sie im Logik-App-Designer unter dem Schritt, dem Sie eine Antwortaktion hinzufügen möchten, die Option **Neuer Schritt** aus.

   Verwenden Sie beispielsweise den zuvor beschriebenen Anforderungstrigger:

   ![Neuen Schritt hinzufügen](./media/connectors-native-reqres/add-response.png)

   Wenn Sie zwischen Schritten eine Aktion einfügen möchten, bewegen Sie den Mauszeiger über den Pfeil zwischen diesen Schritten. Wählen Sie das angezeigte Pluszeichen ( **+** ) aus, und wählen Sie dann **Aktion hinzufügen** aus.

1. Geben Sie unter **Aktion auswählen** im Suchfeld den Text `response` als Filter ein, und wählen Sie die **Antwortaktion** aus.

   ![Auswählen der Aktion „Antwort“](./media/connectors-native-reqres/select-response-action.png)

   Der Anforderungstrigger ist in diesem Beispiel der Einfachheit halber zugeklappt.

1. Fügen Sie alle Werte hinzu, die für die Antwortnachricht erforderlich sind.

   Bei einigen Feldern wird durch Klicken die Liste mit dynamischen Inhalten geöffnet. Anschließend können Sie Token auswählen, die die verfügbaren Ausgaben aus vorherigen Schritten des Workflows darstellen. Eigenschaften aus dem Schema, das im vorherigen Beispiel angegeben wurde, werden jetzt in der Liste mit dynamischen Inhalten angezeigt.

   Schließen Sie beispielsweise im Feld **Header** den Eintrag `Content-Type` als Schlüsselnamen ein, und legen Sie den Schlüsselwert auf `application/json` fest, wie weiter oben in diesem Thema beschrieben. Für das Feld **Text** können Sie die Ausgabe des Triggertexts aus der Liste mit dynamischen Inhalten auswählen.

   ![Details der Antwortaktion](./media/connectors-native-reqres/response-details.png)

   Um die Header im JSON-Format anzuzeigen, wählen Sie **Zur Textansicht wechseln** aus.

   ![Header: Zur Textansicht wechseln](./media/connectors-native-reqres/switch-to-text-view.png)

   Hier finden Sie weitere Informationen zu den Eigenschaften, die Sie in der Antwortaktion festlegen können.

   | Eigenschaftenname | JSON-Eigenschaftenname | Erforderlich | BESCHREIBUNG |
   |---------------|--------------------|----------|-------------|
   | **Statuscode** | `statusCode` | Ja | Der in der Antwort zurückzugebende Statuscode |
   | **Headers** | `headers` | Nein | Ein JSON-Objekt, das einen oder mehrere Header beschreibt, die in die Antwort eingeschlossen werden sollen |
   | **Text** | `body` | Nein | Der Antworttext. |
   |||||

1. Um weitere Eigenschaften hinzuzufügen, z. B. das JSON-Schema für den Antworttext, öffnen Sie die Liste **Neuen Parameter hinzufügen**, und wählen Sie die Parameter aus, die hinzugefügt werden sollen.

1. Wenn Sie fertig sind, speichern Sie Ihre Logik-App. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte

* [Sicherer Zugriff und Daten: Zugriff für eingehende Aufrufe anforderungsbasierter Trigger](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
* [Connectors für Logic Apps](../connectors/apis-list.md)
