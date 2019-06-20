---
title: Aufrufen, Auslösen oder Schachteln von Workflows mit HTTP-Endpunkten – Azure Logic Apps
description: Einrichten von HTTP-Endpunkten zum Aufrufen, Auslösen oder Schachteln von Workflows für Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.workload: integration
author: ecfan
ms.author: klam; LADocs
ms.reviewer: jehollan, klam, LADocs
manager: carmonm
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 03/31/2017
ms.openlocfilehash: b091fb8c6f0b2b655ce0595188c362206f79d702
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495052"
---
# <a name="call-trigger-or-nest-workflows-with-http-endpoints-in-azure-logic-apps"></a>Aufrufen, Auslösen oder Schachteln von Workflows mit HTTP-Endpunkten in Azure Logic Apps

Sie können synchrone HTTP-Endpunkte nativ als Trigger in Logik-Apps verfügbar machen, sodass Sie Ihre Logik-Apps über eine URL auslösen oder aufrufen können. Sie können Workflows auch durch die Verwendung eines Musters aufrufbarer Endpunkte in Ihren Logik-Apps schachteln.

Um HTTP-Endpunkte zu erstellen, können Sie diese Trigger hinzufügen, sodass Ihre Logik-Apps eingehende Anforderungen empfangen können:

* [Anforderung](../connectors/connectors-native-reqres.md)

* [API Connection Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnection-trigger)

* [HTTP Webhook](../connectors/connectors-native-webhook.md)

   > [!NOTE]
   > In diesen Beispielen wird zwar der Trigger **Request** verwendet, doch Sie können jeden der aufgelisteten HTTP-Trigger verwenden, und alle Prinzipien gelten gleichermaßen für die anderen Triggertypen.

## <a name="set-up-an-http-endpoint-for-your-logic-app"></a>Einrichten eines HTTP-Endpunkts für Ihre Logik-App

Um einen HTTP-Endpunkt zu erstellen, fügen Sie einen Trigger hinzu, der eingehende Anforderungen empfangen kann.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com "Azure-Portal") an. Wechseln Sie zu Ihrer Logik-App, und öffnen Sie den Logik-App-Designer.

2. Fügen Sie einen Trigger hinzu, der Ihrer Logik-App den Empfang eingehender Anforderungen ermöglicht. Fügen Sie Ihrer Logik-App z.B. den **Anforderungstrigger** hinzu.

3.  Sie können unter **JSON-Schema für Anforderungstext** optional ein JSON-Schema für die Nutzlast (Daten) eingeben, die der Trigger empfangen soll.

    Der Designer verwendet dieses Schema zum Generieren von Token, die Ihre Logik-App zum Nutzen, Analysieren und Übergeben von Daten vom Trigger über Ihren Workflow verwenden kann. 
    Weitere Informationen zu [Token, die aus JSON-Schemata generiert werden](#generated-tokens).

    Geben Sie für dieses Beispiel das im Designer angezeigte Schema ein:

    ```json
    {
      "type": "object",
      "properties": {
        "address": {
          "type": "string"
        }
      },
      "required": [
        "address"
      ]
    }
    ```

    ![Hinzufügen der Anforderungsaktion][1]

    > [!TIP]
    > 
    > Sie können ein Schema für eine Beispiel-JSON-Nutzlast mit einem Tool wie [jsonschema.net](https://jsonschema.net/) generieren oder im Trigger **Request** durch Auswahl von **Beispielnutzlast zum Generieren eines Schemas verwenden**. 
    > Geben Sie die Beispielnutzlast ein, und wählen Sie **Fertig**.

    Diese Beispielnutzlast:

    ```json
    {
       "address": "21 2nd Street, New York, New York"
    }
    ```

    generiert dieses Schema:

    ```json
    {
       "type": "object",
       "properties": {
          "address": {
             "type": "string" 
          }
       }
    }
    ```

4.  Speichern Sie Ihre Logik-App. Unter **HTTP POST an diese URL** sollten Sie jetzt eine generierte Rückruf-URL wie in diesem Beispiel finden:

    ![Generierte Rückruf-URL für den Endpunkt](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

    Diese URL enthält einen Shared Access Signature-Schlüssel (SAS) in den für die Authentifizierung verwendeten Abfrageparametern. 
    Sie können auch die HTTP-Endpunkt-URL aus der Übersicht Ihrer Logik-App im Azure-Portal abrufen. Wählen Sie unter **Triggerverlauf** Ihren Trigger:

    ![Abrufen der HTTP-Endpunkt-URL im Azure-Portal][2]

    Außerdem können Sie die URL mit diesem Aufruf abrufen:

    ```
    POST https://management.azure.com/{logic-app-resourceID}/triggers/{myendpointtrigger}/listCallbackURL?api-version=2016-06-01
    ```

## <a name="change-the-http-method-for-your-trigger"></a>Ändern der HTTP-Methode für Ihren Trigger

Standardmäßig erwartet der Trigger **Request** eine HTTP POST-Anforderung, jedoch können Sie eine andere HTTP-Methode verwenden. 

> [!NOTE]
> Sie können nur einen Methodentyp angeben.

1. Wählen Sie für Ihren **Request**-Trigger **Erweiterte Optionen anzeigen** aus.

2. Öffnen Sie die Liste **Methode**. Wählen Sie für dieses Beispiel **GET**, damit Sie später die HTTP-Endpunkt-URL testen können.

    > [!NOTE]
    > Sie können andere HTTP-Methoden auswählen, oder geben Sie eine benutzerdefinierte Methode für Ihre eigene Logik-App an.

    ![Ändern der HTTP-Methode](./media/logic-apps-http-endpoint/change-method.png)

## <a name="accept-parameters-through-your-http-endpoint-url"></a>Akzeptieren von Parametern über Ihre HTTP-Endpunkt-URL

Wenn Sie möchten, dass Ihre HTTP-Endpunkt-URL Parameter akzeptiert, passen Sie den relativen Pfad des Triggers an.

1. Wählen Sie für Ihren **Request**-Trigger **Erweiterte Optionen anzeigen** aus. 

2. Geben Sie unter **Methode** die HTTP-Methode an, die Ihre Anforderung verwenden soll. Wählen Sie für dieses Beispiel die **GET**-Methode, sofern nicht bereits geschehen, sodass Sie die HTTP-Endpunkt-URL testen können.

      > [!NOTE]
      > Wenn Sie einen relativen Pfad für den Trigger angeben, müssen Sie auch explizit eine HTTP-Methode für den Trigger angeben.

3. Geben Sie unter **Relativer Pfad** den relativen Pfad für den Parameter an, den Ihre URL akzeptieren soll, z.B. `customers/{customerID}`.

    ![Festlegen der HTTP-Methode und des relativen Pfads für den Parameter](./media/logic-apps-http-endpoint/relativeurl.png)

4. Um den Parameter zu verwenden, fügen Sie Ihrer Logik-App eine **Response**-Aktion hinzu. (Wählen Sie unter Ihrem Trigger **Neuer Schritt** > **Aktion hinzufügen** > **Antwort**.) 

5. Schließen Sie in den **Hauptteil** Ihrer Antwort das Token für den Parameter ein, den Sie im relativen Pfad des Triggers angegeben haben.

    Um beispielsweise `Hello {customerID}` zurückzugeben, aktualisieren Sie den **Hauptteil** Ihrer Antwort mit `Hello {customerID token}`. 
    In der eingeblendeten dynamischen Inhaltsliste sollte Ihnen das `customerID`-Token zur Auswahl angezeigt werden.

    ![Hinzufügen eines Parameters zum Antworthauptteil](./media/logic-apps-http-endpoint/relativeurlresponse.png)

    Ihr **Hauptteil** sollte wie im folgenden Beispiel aussehen:

    ![Antworthauptteil mit Parameter](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

6. Speichern Sie Ihre Logik-App. 

    Ihre HTTP-Endpunkt-URL enthält nun den relativen Pfad, z.B.: 

    https&#58;//prod-00.southcentralus.logic.azure.com/workflows/f90cb66c52ea4e9cabe0abf4e197deff/triggers/manual/paths/invoke/customers/{customerID}...

7. Um den HTTP-Endpunkt zu testen, kopieren Sie die aktualisierte URL, und fügen Sie sie in einem anderen Browserfenster ein, aber ersetzen Sie `{customerID}` mit `123456`, und drücken Sie die EINGABETASTE.

    Ihr Browser sollte diesen Text anzeigen: 

    `Hello 123456`

<a name="generated-tokens"></a>

### <a name="tokens-generated-from-json-schemas-for-your-logic-app"></a>Aus den JSON-Schemata für Ihre Logik-App generierte Token

Wenn Sie in Ihrem **Request**-Trigger ein JSON-Schema angeben, generiert der Logik-App-Designer Token für Eigenschaften in diesem Schema. Diese Token können Sie dann zur Weitergabe von Daten über Ihren Logik-App-Workflow verwenden.

Wenn Sie in diesem Beispiel die Eigenschaften `title` und `name` Ihrem JSON-Schema hinzufügen, stehen deren Token jetzt zur Verwendung in späteren Schritten des Workflows zur Verfügung. 

So sieht das vollständige JSON-Schema aus:

```json
{
   "type": "object",
   "properties": {
      "address": {
         "type": "string"
      },
      "title": {
         "type": "string"
      },
      "name": {
         "type": "string"
      }
   },
   "required": [
      "address",
      "title",
      "name"
   ]
}
```

## <a name="create-nested-workflows-for-logic-apps"></a>Erstellen geschachtelter Workflows für Logik-Apps

Sie können Workflows in Ihrer Logik-App schachteln, indem Sie andere Logik-Apps hinzufügen, die Anforderungen empfangen können. Um diese Logik-Apps einzuschließen, fügen Sie die Aktion **Azure Logic Apps – Logic Apps-Workflow wählen** Ihrem Trigger hinzu. Sie können dann berechtigte Logik-Apps auswählen.

![Hinzufügen einer anderen Logik-App](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

## <a name="call-or-trigger-logic-apps-through-http-endpoints"></a>Aufrufen oder Auslösen von Logik-Apps über HTTP-Endpunkte

Nachdem Sie den HTTP-Endpunkt erstellt haben, können Sie Ihre Logik-App über eine `POST`-Methode mit der vollständigen URL aufrufen. Logik-Apps bieten integrierte Unterstützung für Direktzugriffs-Endpunkte.

> [!NOTE] 
> Um zu einem beliebigen Zeitpunkt manuell eine Logik-App auszuwählen, klicken Sie auf der Symbolleiste des Logik-App-Designers oder der Logik-App-Codeansicht auf **Ausführen**.

## <a name="reference-content-from-an-incoming-request"></a>Verweisen auf Inhalt von einer eingehenden Anforderung aus

Wenn der Typ des Inhalts `application/json` ist, können Sie von der eingehenden Anforderung aus auf Eigenschaften verweisen. Andernfalls wird der Inhalt als einzelne binäre Einheit behandelt, die Sie an andere APIs übergeben können. Um innerhalb des Workflows auf diesen Inhalt zu verweisen, müssen Sie diesen Inhalt konvertieren. Wenn Sie beispielsweise `application/xml`-Inhalt übergeben, können Sie mit `@xpath()` eine XPath-Extrahierung durchführen oder mit `@json()` XML in JSON konvertieren. Erfahren Sie mehr über das [Arbeiten mit Inhaltstypen](../logic-apps/logic-apps-content-type.md).

Um die Ausgabe aus einer eingehenden Anforderung abzurufen, können Sie die `@triggerOutputs()`-Funktion verwenden. Die Ausgabe könnte folgendem Beispiel entsprechen:

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

Sie können über die Verknüpfung `body` speziell auf die `@triggerBody()`-Eigenschaft zugreifen. 

## <a name="respond-to-requests"></a>Reagieren auf Anforderungen

Auf einige Anforderungen, die eine Logik-App starten, möchten Sie möglicherweise mit Rückgabe von Inhalten an den Aufrufer reagieren. Um Statuscode, Header und Hauptteil für die Antwort zu erstellen, können Sie die **Response**-Aktion verwenden. Diese Aktion kann an einer beliebigen Stelle in der Logik-App auftreten, nicht nur am Ende des Workflows.

> [!NOTE] 
> Wenn Ihre Logik-App keine **Response** enthält, antwortet der HTTP-Endpunkt *sofort* mit einem **202 Accepted**-Status. Damit die ursprüngliche Anforderung die Antwort erhält, müssen außerdem alle erforderlichen Schritte für die Antwort innerhalb des [Timeoutlimits der Anforderung](./logic-apps-limits-and-config.md) beendet sein, es sei denn, Sie rufen den Workflow als geschachtelte Logik-App auf. Wenn innerhalb dieses Limits keine Antwort erfolgt, kommt es bei der eingehenden Anforderung zu einem Timeout mit der HTTP-Antwort **408 – Clienttimeout**. Bei geschachtelten Logik-Apps wartet die übergeordnete Logik-App unabhängig von der Zeitdauer, bis die Antwort abgeschlossen ist.

### <a name="construct-the-response"></a>Erstellen der Antwort

Sie können mehrere Header und jeden Inhaltstyp in den Antworthauptteil einbeziehen. In der Beispielantwort gibt der Header an, dass die Antwort den Inhaltstyp `application/json` aufweist, und der Hauptteil enthält `title` und `name` – je nach dem JSON-Schema, das zuvor für den **Request**-Trigger aktualisiert wurde.

![HTTP-Antwortaktion][3]

Antworten haben folgende Eigenschaften:

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| statusCode |Legt den HTTP-Statuscode für die Antwort auf die eingehende Anforderung fest. Dieser Code kann jeder gültige Statuscode sein, der mit 2xx, 4xx oder 5xx beginnt. Mit 3xx beginnende Statuscodes sind jedoch nicht zulässig. |
| headers |Definiert eine beliebige Anzahl von Headern, die in die Antwort einbezogen werden sollen. |
| body |Legt ein Hauptteilobjekt fest, das eine Zeichenfolge, ein JSON-Objekt oder sogar binäre Inhalte aufweisen kann, auf die in einem vorherigen Schritt verwiesen wird. |

Das JSON-Schema für die **Response**-Aktion sieht jetzt so aus:

``` json
"Response": {
   "inputs": {
      "body": {
         "title": "@{triggerBody()?['title']}",
         "name": "@{triggerBody()?['name']}"
      },
      "headers": {
           "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {},
   "type": "Response"
}
```

> [!TIP]
> Wählen Sie zum Anzeigen der vollständigen JSON-Definition für die Logik-App im Logik-App-Designer **Codeansicht**.

## <a name="q--a"></a>Fragen und Antworten

#### <a name="q-what-about-url-security"></a>F: Wie sieht es mit der URL-Sicherheit aus?

A: Azure generiert sicher Rückruf-URLs für Logik-Apps über eine Shared Access Signature (SAS). Diese Signatur wird als Abfrageparameter übergeben und muss geprüft werden, bevor Ihre Logik-App ausgelöst werden kann. Azure generiert die Signatur durch eine eindeutige Kombination aus einem geheimen Schlüssel pro Logik-App, dem Namen des Triggers und dem ausgeführten Vorgang. Nur wenn ein Benutzer Zugriff auf den geheimen Logik-App-Schlüssel hat, kann er eine gültige Signatur generieren.

   > [!IMPORTANT]
   > Für Produktions- und sichere Systeme wird dringend davon abgeraten, Ihre Logik-App direkt aus dem Browser aufzurufen. Das hat folgende Gründe:
   > 
   > * Der Schlüssel für den gemeinsamen Zugriff ist in der URL enthalten.
   > * Sie können keine Richtlinien für sichere Inhalte verwalten, da Domänen von Benutzern der Logik-App gemeinsam verwendet werden.

#### <a name="q-can-i-configure-http-endpoints-further"></a>F: Kann ich weitere HTTP-Endpunkte konfigurieren?

A: Ja, HTTP-Endpunkte unterstützen die erweiterte Konfiguration über [**API Management**](../api-management/api-management-key-concepts.md). Dieser Dienst ermöglicht Ihnen auch die konsistente Verwaltung aller APIs einschließlich der Logik-Apps, das Einrichten benutzerdefinierter Domänennamen, Verwenden weiterer Authentifizierungsmethoden und vieles mehr, z.B.:

* [Ändern der Anforderungsmethode](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#SetRequestMethod)
* [Ändern der URL-Segmente der Anforderung](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#RewriteURL)
* Einrichten Ihrer API Management-Domänen im [Azure-Portal](https://portal.azure.com/ "Azure-Portal")
* Einrichten der Richtlinie zum Überprüfen auf Standardauthentifizierung

#### <a name="q-what-changed-when-the-schema-migrated-from-the-december-1-2014-preview"></a>F: Was hat sich mit der Migration des Schemas von der Vorschau vom 1. Dezember 2014 geändert?

A: Hier ist eine Zusammenfassung dieser Änderungen:

| Vorschau vom 1. Dezember 2014 | 1. Juni 2016 |
| --- | --- |
| Klicken auf die API-App **HTTP-Listener** |Klicken auf **Manueller Trigger** (keine API-App erforderlich) |
| HTTP-Listener-Einstellung*Sendet Antwort automatisch* |Hinzufügen oder Nichthinzufügen einer **Response**-Aktion zur Workflowdefinition |
| Konfigurieren der Standard- oder OAuth-Authentifizierung |Über API Management |
| Konfigurieren der HTTP-Methode |Auswahl einer HTTP-Methode unter **Erweiterte Optionen anzeigen** |
| Konfigurieren des relativen Pfads |Hinzufügen eines relativen Pfads unter **Erweiterte Optionen anzeigen** |
| Verweisen auf den eingehenden Hauptteil über `@triggerOutputs().body.Content` |Verweisen über `@triggerOutputs().body` |
| **HTTP-Antwort senden** im HTTP-Listener |Klicken auf **Auf HTTP-Anforderung reagieren** (keine API-App erforderlich) |

## <a name="get-help"></a>Hier erhalten Sie Hilfe

Im [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) können Sie Fragen stellen, Fragen beantworten und sich über die Aktivitäten anderer Azure Logic Apps-Benutzer informieren.

Zur Optimierung von Azure Logic Apps und Connectors können Sie auf der [Benutzerfeedbackwebsite für Azure Logic Apps](https://aka.ms/logicapps-wish) über Ideen abstimmen oder selbst Ideen einreichen.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen von Logik-App-Definitionen](./logic-apps-author-definitions.md)
* [Behandeln von Fehlern und Ausnahmen](./logic-apps-exception-handling.md)

[1]: ./media/logic-apps-http-endpoint/manualtrigger.png
[2]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[3]: ./media/logic-apps-http-endpoint/response.png