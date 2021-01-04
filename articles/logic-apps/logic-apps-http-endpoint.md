---
title: Abrufen, Auslösen oder Schachteln von Logik-Apps mithilfe von Anforderungstriggern
description: Einrichten von HTTPS-Endpunkten zum Aufrufen, Auslösen oder Schachteln von Logik-App-Workflows in Azure Logic Apps
services: logic-apps
ms.workload: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 11/19/2020
ms.openlocfilehash: b345168dad63b1846d46c12721587eaffb5f887e
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981203"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-https-endpoints-in-azure-logic-apps"></a>Aufrufen, Auslösen oder Schachteln von Logik-Apps mithilfe von HTTPS-Endpunkten in Azure Logic Apps

Damit Ihre Logik-App über eine URL aufgerufen werden und eingehende Anforderungen von anderen Diensten empfangen kann, können Sie einen synchronen HTTPS-Endpunkt nativ als anforderungsbasierten Trigger in dieser Logik-App verfügbar machen. Mit dieser Funktion können Sie Ihre Logik-App aus anderen Logik-Apps aufrufen und ein Muster für aufrufbare Endpunkte erstellen. Um einen aufrufbaren Endpunkt für die Verarbeitung eingehender Aufrufe einzurichten, können Sie einen der folgenden Triggertypen verwenden:

* [Anforderung](../connectors/connectors-native-reqres.md)
* [HTTP Webhook](../connectors/connectors-native-webhook.md)
* Verwaltete Connectortrigger vom Typ [ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger), die eingehende HTTPS-Anforderungen empfangen können

In diesem Artikel wird gezeigt, wie Sie einen aufrufbaren Endpunkt in Ihrer Logik-App mithilfe des Anforderungstriggers erstellen und ihn aus einer anderen Logik-App aufrufen. Alle Prinzipien gelten genauso für die anderen Triggertypen, mit denen Sie eingehende Anforderungen empfangen können.


Weitere Informationen zu Sicherheit, Autorisierung und Verschlüsselung für eingehende Aufrufe Ihrer Logik-App, etwa über [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) (früher bekannt als Secure Sockets Layer (SSL)) oder [Azure Active Directory Open Authorization (Azure AD OAuth)](../active-directory/develop/index.yml), finden Sie unter [Sicherer Zugriff und Daten: Zugriff für eingehende Aufrufe anforderungsbasierter Trigger](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests). In diesem Artikel erfahren Sie auch, wie Sie Ihre Logik-App mit Azure API Management verfügbar machen oder IP-Adressen aus eingehenden Aufrufen einschränken.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Die Logik-App, in der Sie den Trigger zum Erstellen des aufrufbaren Endpunkts verwenden möchten. Sie können mit einer leeren Logik-App beginnen oder eine vorhandene Logik-App verwenden, in der Sie den aktuellen Trigger ersetzen. Dieses Beispiel beginnt mit einer leeren Logik-App. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md) und [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-callable-endpoint"></a>Erstellen eines aufrufbaren Endpunkts

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Erstellen Sie im Logik-App-Designer eine leere Logik-App, und öffnen Sie sie.

1. Wählen Sie unter dem Suchfeld die Option **Integriert** aus. Geben Sie im Suchfeld den Begriff `request` als Filter ein. Wählen Sie in der Liste der Trigger die Option **Beim Empfang einer HTTP-Anforderung** aus.

   ![Suchen und Auswählen des Anforderungstriggers](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. Optional können Sie im Feld **JSON-Schema für Anforderungstext** ein JSON-Schema eingeben, das die Nutzlast bzw. Daten beschreibt, die der Trigger empfangen soll.

   Der Designer verwendet dieses Schema, um Token zu generieren, die Triggerausgaben darstellen. Dann können Sie im gesamten Workflow Ihrer Logik-App ganz einfach auf diese Ausgaben verweisen. Informieren Sie sich über [Token, die aus JSON-Schemas generiert werden](#generated-tokens).

   Geben Sie für das vorliegende Beispiel das folgende Schema ein:

   ```json
      {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "streetNumber": {
                  "type": "string"
               },
               "streetName": {
                  "type": "string"
               },
               "town": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "string"
               }
            }
         }
      }
   }
    ```

   ![Bereitstellen eines JSON-Schemas für die Anforderungsaktion](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   Alternativ dazu können Sie ein JSON-Schema auch durch Bereitstellen einer Beispielnutzlast generieren:

   1. Wählen Sie im **Anforderungstrigger** die Option **Beispielnutzlast zum Generieren eines Schemas verwenden** aus.

   1. Geben Sie im Feld **Geben oder fügen Sie eine JSON-Beispielnutzlast ein** Ihre Beispielnutzlast ein, beispielsweise:

      ```json
      {
         "address": {
            "streetNumber": "00000",
            "streetName": "AnyStreet",
            "town": "AnyTown",
            "postalCode": "11111-1111"
        }
      }
      ```

   1. Wählen Sie abschließend **Fertig** aus.

      Im Feld **JSON-Schema für Anforderungstext** wird jetzt das generierte Schema angezeigt.

1. Speichern Sie Ihre Logik-App.

   Im Feld **HTTP POST URL** wird die generierte Rückruf-URL angezeigt, mit der andere Dienste Ihre Logik-App aufrufen und auslösen können. Diese URL enthält Abfrageparameter, die einen Shared Access Signature-Schlüssel (SAS) angeben, der für die Authentifizierung verwendet wird.

   ![Generierte Rückruf-URL für den Endpunkt](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

1. Zum Kopieren der Rückruf-URL haben Sie folgende Optionen:

   * Wählen Sie rechts neben dem Feld **HTTP POST URL** die Option **URL kopieren** (Symbol „Dateien kopieren“) aus.

   * Führen Sie diesen Befehl mit der Methode aus, die der Anforderungsbefehl erwartet. In diesem Beispiel wird die `POST`-Methode verwendet:

     `POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01`

   * Kopieren Sie die Rückruf-URL aus dem Bereich **Übersicht** Ihrer Logik-App.

     1. Wählen Sie im Menü Ihrer Logik-App die Option **Übersicht** aus.

     1. Wählen Sie im Abschnitt **Zusammenfassung** die Option **Triggerverlauf anzeigen** aus.

        ![Abrufen der Endpunkt-URL aus dem Azure-Portal](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

     1. Kopieren Sie die URL unter **Rückruf-URL [POST]** :

        ![Kopieren der Endpunkt-URL aus dem Azure-Portal](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url-post.png)

<a name="select-method"></a>

## <a name="select-expected-request-method"></a>Auswählen der erwarteten Anforderungsmethode

Standardmäßig erwartet der Anforderungstrigger eine `POST`-Anforderung. Sie können jedoch eine andere Methode angeben, die vom Aufrufer verwendet werden muss (aber nur eine einzige).

1. Öffnen Sie im Anforderungstrigger die Liste **Neuen Parameter hinzufügen**, und wählen Sie **Methode** aus. Dadurch wird diese Eigenschaft zum Trigger hinzugefügt.

   ![Hinzufügen der Eigenschaft „Methode“ zum Trigger](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. Wählen Sie aus der Liste **Methode** die Methode aus, die der Trigger stattdessen erwarten sollte. Alternativ dazu können Sie auch eine benutzerdefinierte Methode angeben.

   Wählen Sie beispielsweise die Methode **GET** aus, damit Sie die Endpunkt-URL später testen können.

   ![Auswählen der vom Trigger erwarteten Anforderungsmethode](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

<a name="endpoint-url-parameters"></a>

## <a name="pass-parameters-through-endpoint-url"></a>Übergeben von Parametern über die Endpunkt-URL

Wenn Sie Parameterwerte über die URL des Endpunkts annehmen möchten, haben Sie folgende Optionen:

* [Akzeptieren Sie Werte durch GET-Parameter](#get-parameters) oder URL-Parameter.

  Diese Werte werden als Name-Wert-Paare an die Endpunkt-URL übergeben. Für diese Option müssen Sie die GET-Methode in Ihrem Anforderungstrigger verwenden. In einer nachfolgenden Aktion können Sie die Parameterwerte als Triggerausgaben abrufen, indem Sie die `triggerOutputs()`-Funktion in einem Ausdruck verwenden.

* [Akzeptieren Sie Werte über einen relativen Pfad](#relative-path) für Parameter in Ihrem Anforderungstrigger.

  Diese Werte werden über einen relativen Pfad in der Endpunkt-URL übergeben. Sie müssen zudem die [Methode explizit auswählen](#select-method), die Ihr Trigger erwartet. In einer nachfolgenden Aktion können Sie die Parameterwerte als Triggerausgaben abrufen, indem direkt auf diese Ausgaben verweisen.

<a name="get-parameters"></a>

### <a name="accept-values-through-get-parameters"></a>Akzeptieren von Werten durch GET-Parameter

1. Öffnen Sie im Anforderungstrigger die Liste **Neuen Parameter hinzufügen**, fügen Sie dem Trigger die Eigenschaft **Methode** hinzu, wählen Sie die Methode **GET** aus.

   Weitere Informationen finden Sie unter [Auswählen der erwarteten Anforderungsmethode](#select-method).

1. Fügen Sie unter dem Anforderungstrigger die Aktion hinzu, mit der Sie den Parameterwert verwenden möchten. Fügen Sie für dieses Beispiel die Aktion **Antwort** hinzu.

   1. Wählen Sie unter dem Anforderungstrigger **Neuer Schritt** > **Aktion hinzufügen** aus.
   
   1. Geben Sie unter **Aktion auswählen** im Suchfeld `response` als Filter ein. Wählen Sie in der Liste der Aktionen die Aktion **Antwort** aus.

1. Führen Sie die folgenden Schritte aus, um den `triggerOutputs()`-Ausdruck zu erstellen, der den Parameterwert abruft:

   1. Klicken Sie in die Eigenschaft **Body** der Aktion „Antwort“, damit die dynamische Inhaltsliste angezeigt wird, und wählen Sie **Ausdruck** aus.

   1. Geben Sie in das Feld **Ausdruck** diesen Ausdruck ein, ersetzen Sie dabei `parameter-name` durch ihren Parameternamen, und wählen Sie **OK** aus.

      `triggerOutputs()['queries']['parameter-name']`

      ![„triggerOutputs()“-Ausdruck zum Trigger hinzufügen](./media/logic-apps-http-endpoint/trigger-outputs-expression.png)

      In der Eigenschaft **Body** wird der Ausdruck in das `triggerOutputs()`-Token aufgelöst.

      ![Aufgelöster „triggerOutputs()“-Ausdruck](./media/logic-apps-http-endpoint/trigger-outputs-expression-token.png)

      Wenn Sie die Logik-App speichern, vom Designer weg und wieder zurück zum Designer navigieren, zeigt das Token den von Ihnen angegebenen Parameternamen an, z. B.:

      ![Aufgelöster Ausdruck für den Parameternamen](./media/logic-apps-http-endpoint/resolved-expression-parameter-token.png)

      In der Codeansicht wird die Eigenschaft **Body** in der Definition der Aktion „Antwort“ wie folgt angezeigt:

      `"body": "@{triggerOutputs()['queries']['parameter-name']}",`

      Nehmen Sie beispielsweise an, dass Sie einen Wert für einen Parameter mit dem Namen `postalCode` übergeben möchten. Die Eigenschaft **Body** gibt die Zeichenfolge `Postal Code: ` mit einem nachfolgenden Leerzeichen an, gefolgt vom entsprechenden Ausdruck:

      ![„triggerOutputs()“-Beispielausdruck zum Trigger hinzufügen](./media/logic-apps-http-endpoint/trigger-outputs-expression-postal-code.png)

1. Um Ihren aufrufbaren Endpunkt zu testen, kopieren Sie die Rückruf-URL aus dem Anforderungstrigger, und fügen Sie die URL in ein anderes Browserfenster ein. Fügen Sie in der URL den Parameternamen und -wert hinter dem Fragezeichen (`?`) im folgenden Format hinzu, und drücken Sie die EINGABETASTE.

   `...?{parameter-name=parameter-value}&api-version=2016-10-01...`

   `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?{parameter-name=parameter-value}&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   Der Browser gibt eine Antwort mit dem folgenden Text zurück: `Postal Code: 123456`.

   ![Antwort vom Senden der Anforderung an die Rückruf-URL](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

1. Um den Parameternamen und -wert an einer anderen Stelle innerhalb der URL zu platzieren, stellen Sie sicher, dass Sie das kaufmännische Und-Zeichen (`&`) als Präfix verwenden, z. B.:

   `...?api-version=2016-10-01&{parameter-name=parameter-value}&...`

   In diesem Beispiel wird die Rückruf-URL mit dem Beispielparameternamen und -wert `postalCode=123456` an verschiedenen Positionen innerhalb der URL angezeigt:

   * 1\. Position: `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?postalCode=123456&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   * 2\. Position: `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?api-version=2016-10-01&postalCode=123456&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

> [!NOTE]
> Wenn Sie das Hash- oder Nummernzeichen ( **#** ) im URI verwenden möchten, nutzen Sie stattdessen diese codierte Version: `%25%23`.

<a name="relative-path"></a>

### <a name="accept-values-through-a-relative-path"></a>Akzeptieren von Werten durch einen relativen Pfad

1. Öffnen Sie im Anforderungstrigger die Liste **Neuen Parameter hinzufügen**, und wählen Sie **Relativer Pfad** aus. Dadurch wird diese Eigenschaft zum Trigger hinzugefügt.

   ![Hinzufügen der Eigenschaft „Relativer Pfad“ zum Trigger](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. Geben Sie in der Eigenschaft **Relativer Pfad** den relativen Pfad für den Parameter in Ihrem JSON-Schema an, der von Ihrer URL akzeptiert werden soll. Beispiel: `/address/{postalCode}`.

   ![Angeben des relativen Pfads für den Parameter](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. Fügen Sie unter dem Anforderungstrigger die Aktion hinzu, mit der Sie den Parameterwert verwenden möchten. Fügen Sie für dieses Beispiel die Aktion **Antwort** hinzu.

   1. Wählen Sie unter dem Anforderungstrigger **Neuer Schritt** > **Aktion hinzufügen** aus.

   1. Geben Sie unter **Aktion auswählen** im Suchfeld `response` als Filter ein. Wählen Sie in der Liste der Aktionen die Aktion **Antwort** aus.

1. Schließen Sie in die Eigenschaft **Text** der Antwortaktion das Token für den Parameter ein, den Sie im relativen Pfad des Triggers angegeben haben.

   Nehmen Sie beispielsweise an, dass die Antwortaktion `Postal Code: {postalCode}` zurückgeben soll.

   1. Geben Sie in der Eigenschaft **Text** die Zeichenfolge `Postal Code: ` mit einem nachfolgenden Leerzeichen ein. Lassen Sie Ihren Cursor im Bearbeitungsfeld, damit die dynamische Inhaltsliste geöffnet bleibt.

   1. Wählen Sie in der dynamischen Inhaltsliste im Abschnitt **Beim Empfang einer HTTP-Anforderung** das Token **postalCode** aus.

      ![Hinzufügen des angegebenen Parameters zum Antworttext](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

      Die Eigenschaft **Text** enthält jetzt den ausgewählten Parameter.

      ![Beispiel für Antworttext mit Parameter](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. Speichern Sie Ihre Logik-App.

   Im Anforderungstrigger wird die Rückruf-URL aktualisiert und enthält nun den relativen Pfad, z. B.:

   `https://prod-07.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

1. Um Ihren aufrufbaren Endpunkt zu testen, kopieren Sie die aktualisierte Rückruf-URL aus dem Anforderungstrigger, fügen Sie die URL in ein anderes Browserfenster ein, ersetzen Sie `{postalCode}` in der URL durch `123456`, und drücken Sie die EINGABETASTE.

   Der Browser gibt eine Antwort mit dem folgenden Text zurück: `Postal Code: 123456`.

   ![Antwort vom Senden der Anforderung an die Rückruf-URL](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

> [!NOTE]
> Wenn Sie das Hash- oder Nummernzeichen ( **#** ) im URI verwenden möchten, nutzen Sie stattdessen diese codierte Version: `%25%23`.

## <a name="call-logic-app-through-endpoint-url"></a>Aufrufen einer Logik-App über eine Endpunkt-URL

Nach dem Erstellen des Endpunkts können Sie die Logik-App auslösen, indem Sie eine HTTPS-Anforderung an die vollständige URL des Endpunkts senden. Logik-Apps bieten integrierte Unterstützung für Direktzugriffs-Endpunkte.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>Aus dem Schema generierte Token

Wenn Sie in Ihrem Anforderungstrigger ein JSON-Schema angeben, generiert der Logik-App-Designer Token für die Eigenschaften in diesem Schema. Diese Token können Sie dann zur Weitergabe von Daten über Ihren Logik-App-Workflow verwenden.

Wenn Sie Ihrem JSON-Schema beispielsweise weitere Eigenschaften wie `"suite"` hinzufügen, stehen Ihnen in den späteren Schritten für Ihre Logik-App Token für diese Eigenschaften zur Verfügung. So sieht das vollständige JSON-Schema aus:

```json
   {
   "type": "object",
   "properties": {
      "address": {
         "type": "object",
         "properties": {
            "streetNumber": {
               "type": "string"
            },
            "streetName": {
               "type": "string"
            },
            "suite": {
               "type": "string"
            },
            "town": {
               "type": "string"
            },
            "postalCode": {
               "type": "string"
            }
         }
      }
   }
}
```

## <a name="create-nested-logic-apps"></a>Erstellen von geschachtelten Logik-Apps

Sie können Workflows in Ihrer Logik-App schachteln, indem Sie andere Logik-Apps hinzufügen, die Anforderungen empfangen können. Führen Sie zum Einschließen dieser Logik-Apps die folgenden Schritte aus:

1. Wählen Sie in dem Schritt, in dem eine weitere Logik-App aufgerufen werden soll, die Option **Neuer Schritt** > **Aktion hinzufügen** aus.

1. Wählen Sie unter **Aktion auswählen** die Option **Integriert** aus. Geben Sie im Suchfeld den Begriff `logic apps` als Filter ein. Wählen Sie aus der Liste der Aktionen die Option **Logic Apps-Workflow auswählen** aus.

   ![Schachteln einer Logik-App in der aktuellen Logik-App](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   Der Designer zeigt die geeigneten Logik-Apps an, die Sie auswählen können.

1. Wählen Sie die Logik-App aus, die aus Ihrer aktuellen Logik-App aufgerufen werden soll.

   ![Auswählen der Logik-App, die aus der aktuellen Logik-App aufgerufen werden soll](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>Verweisen auf Inhalt von einer eingehenden Anforderung aus

Wenn der Inhaltstyp der eingehenden Anforderung `application/json` lautet, können Sie auf die Eigenschaften in der eingehenden Anforderung verweisen. Andernfalls wird dieser Inhalt als einzelne binäre Einheit behandelt, die Sie an andere APIs übergeben können. Um innerhalb des Workflows Ihrer Logik-App auf diesen Inhalt zu verweisen, müssen Sie den Inhalt zuerst konvertieren.

Wenn Sie beispielsweise Inhalt vom Typ `application/xml` übergeben, können Sie mit dem [`@xpath()`-Ausdruck](../logic-apps/workflow-definition-language-functions-reference.md#xpath) eine XPath-Extrahierung durchführen oder mit dem [`@json()`-Ausdruck](../logic-apps/workflow-definition-language-functions-reference.md#json) XML in JSON konvertieren. Informieren Sie sich über das Arbeiten mit unterstützten [Inhaltstypen](../logic-apps/logic-apps-content-type.md).

Um die Ausgabe aus einer eingehenden Anforderung abzurufen, können Sie den [`@triggerOutputs`-Ausdruck](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) verwenden. Nehmen Sie einmal an, Ihre Ausgabe sieht wie im folgenden Beispiel aus:

```json
{
   "headers": {
      "content-type" : "application/json"
   },
   "body": {
      "myProperty" : "property value"
   }
}
```

Um speziell auf die Eigenschaft `body` zuzugreifen, können Sie den [`@triggerBody()`-Ausdruck](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) als Verknüpfung verwenden.

## <a name="respond-to-requests"></a>Reagieren auf Anforderungen

Auf bestimmte Anforderungen, die Ihre Logik-App auslösen, möchten Sie möglicherweise mit Rückgabe von Inhalten an den Aufrufer reagieren. Um Statuscode, Header und Text für die Antwort zu erstellen, können Sie die Antwortaktion verwenden. Diese Aktion kann an einer beliebigen Stelle in der Logik-App auftreten, nicht nur am Ende des Workflows. Wenn Ihre Logik-App keine Antwortaktion enthält, antwortet der Endpunkt *sofort* mit dem Status **202 – akzeptiert**.

Damit der ursprüngliche Aufrufer die Antwort erhält, müssen alle erforderlichen Schritte für die Antwort innerhalb des [Timeoutlimits der Anforderung](./logic-apps-limits-and-config.md) beendet sein, es sei denn, die ausgelöste Logik-App wurde als geschachtelte Logik-App aufgerufen. Wenn innerhalb dieses Limits keine Antwort erfolgt, kommt es bei der eingehenden Anforderung zu einem Timeout mit der Antwort **408 – Clienttimeout**.

Bei geschachtelten Logik-Apps wartet die übergeordnete Logik-App so lange auf eine Antwort, bis alle Schritte abgeschlossen sind, unabhängig davon, wie lange dies dauert.

### <a name="construct-the-response"></a>Erstellen der Antwort

Sie können mehrere Header und jeden Inhaltstyp in den Antworttext einbeziehen. Der Header der folgenden Antwort gibt beispielsweise an, dass der Inhaltstyp der Antwort `application/json` lautet und dass der Text Werte für die Eigenschaften `town` und `postalCode` enthält, basierend auf dem weiter oben in diesem Thema beschriebenen JSON-Schemas für den Anforderungstrigger.

![Bereitstellen des Antwortinhalts für die HTTPS-Antwortaktion](./media/logic-apps-http-endpoint/content-for-response-action.png)

Antworten haben folgende Eigenschaften:

| Eigenschaft (Anzeige) | Eigenschaft (JSON) | BESCHREIBUNG |
|--------------------|-----------------|-------------|
| **Statuscode** | `statusCode` | Der HTTPS-Statuscode, der in der Antwort auf die eingehende Anforderung verwendet werden soll. Dieser Code kann jeder gültige Statuscode sein, der mit 2xx, 4xx oder 5xx beginnt. Mit 3xx beginnende Statuscodes sind jedoch nicht zulässig. |
| **Headers** | `headers` | Mindestens ein Header, der in die Antwort eingefügt werden soll. |
| **Text** | `body` | Ein Textobjekt, das eine Zeichenfolge, ein JSON-Objekt oder sogar binäre Inhalte enthalten kann und auf das in einem vorherigen Schritt verwiesen wird. |
||||

Um die JSON-Definition der Antwortaktion und die vollständige JSON-Definition Ihrer Logik-App anzuzeigen, wählen Sie in der Symbolleiste des Logik-App-Designers die Option **Codeansicht** aus.

``` json
"Response": {
   "type": "Response",
   "kind": "http",
   "inputs": {
      "body": {
         "postalCode": "@triggerBody()?['address']?['postalCode']",
         "town": "@triggerBody()?['address']?['town']"
      },
      "headers": {
         "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {}
}
```

## <a name="q--a"></a>Fragen und Antworten

#### <a name="q-what-about-url-security"></a>F: Wie sieht es mit der URL-Sicherheit aus?

**A:** Azure generiert über eine [Shared Access Signature (SAS)](/rest/api/storageservices/delegate-access-with-shared-access-signature) auf sichere Weise Rückruf-URLs für Logik-Apps. Diese Signatur wird als Abfrageparameter übergeben und muss überprüft werden, bevor Ihre Logik-App ausgeführt werden kann. Azure generiert die Signatur durch eine eindeutige Kombination aus einem geheimen Schlüssel pro Logik-App, dem Namen des Triggers und dem ausgeführten Vorgang. Nur wenn ein Benutzer Zugriff auf den geheimen Logik-App-Schlüssel hat, kann er eine gültige Signatur generieren.

> [!IMPORTANT]
> Bei Produktionssystemen und Systemen mit höherer Sicherheit wird dringend davon abgeraten, Ihre Logik-App direkt aus dem Browser aufzurufen. Das hat folgende Gründe:
>
> * Der Schlüssel für den gemeinsamen Zugriff ist in der URL enthalten.
> * Sie können keine Richtlinien für sichere Inhalte verwalten, da Domänen von Azure Logic Apps-Benutzern gemeinsam verwendet werden.

Weitere Informationen zu Sicherheit, Autorisierung und Verschlüsselung für eingehende Aufrufe Ihrer Logik-App, etwa über [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) (früher bekannt als Secure Sockets Layer (SSL)) oder [Azure Active Directory Open Authorization (Azure AD OAuth)](../active-directory/develop/index.yml), finden Sie unter [Sicherer Zugriff und Daten: Zugriff für eingehende Aufrufe anforderungsbasierter Trigger](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests). In diesem Artikel erfahren Sie auch, wie Sie Ihre Logik-App mit Azure API Management verfügbar machen oder IP-Adressen aus eingehenden Aufrufen einschränken.

#### <a name="q-can-i-configure-callable-endpoints-further"></a>F: Kann ich aufrufbare Endpunkte noch weiter konfigurieren?

**A:** Ja, HTTPS-Endpunkte unterstützen eine erweiterte Konfiguration über [Azure API Management](../api-management/api-management-key-concepts.md). Dieser Dienst ermöglicht Ihnen auch die konsistente Verwaltung aller APIs einschließlich der Logik-Apps, das Einrichten benutzerdefinierter Domänennamen, Verwenden weiterer Authentifizierungsmethoden und vieles mehr, z.B.:

* [Ändern der Anforderungsmethode](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [Ändern der URL-Segmente der Anforderung](../api-management/api-management-transformation-policies.md#RewriteURL)
* Einrichten Ihrer API Management-Domänen im [Azure-Portal](https://portal.azure.com/)
* Einrichten der Richtlinie zum Überprüfen auf Standardauthentifizierung

## <a name="next-steps"></a>Nächste Schritte

* [Empfangen von und Antworten auf HTTPS-Aufrufe mittels Azure Logic Apps](../connectors/connectors-native-reqres.md)
* [Schützen des Zugriffs und der Daten in Azure Logic Apps – Zugriff auf anforderungsbasierte Trigger für eingehende Aufrufe](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
