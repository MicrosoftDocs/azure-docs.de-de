---
title: Hinzufügen und Aufrufen von Azure-Funktionen aus Azure Logic Apps
description: Hinzufügen und Ausführen von Azure-Funktionen aus Logik-Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.topic: article
ms.date: 06/04/2019
ms.reviewer: klam, LADocs
ms.openlocfilehash: 524b927ec0966199c51cdee93e920d7b847139ae
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495131"
---
# <a name="call-azure-functions-from-azure-logic-apps"></a>Aufrufen von Azure-Funktionen aus Azure Logic Apps

Wenn Sie Code ausführen möchten, der einen bestimmten Auftrag in Ihren Logik-Apps ausführt, können Sie Ihre eigene Funktion erstellen, indem Sie [Azure Functions](../azure-functions/functions-overview.md) verwenden. Mit diesem Dienst können Sie Node.js-, C#- and F#-Funktionen erstellen, sodass Sie keine vollständige App oder Infrastruktur zur Ausführung von Code erstellen müssen. Sie können [Logik-Apps auch aus Azure-Funktionen aufrufen](#call-logic-app). Azure Functions ermöglicht serverloses Computing in der Cloud und ist nützlich, um Aufgaben auszuführen, z.B. diese Beispiele:

* Erweitern Sie das Verhalten Ihrer Logik-App um Funktionen in Node.js oder C#.
* Führen Sie in Ihrem Logik-App-Workflow Berechnungen durch.
* Wenden Sie in Ihren Logik-Apps die erweiterte Formatierung oder Computefelder an.

Informationen zum Ausführen von Codeausschnitten ohne Erstellen von Azure-Funktionen finden Sie unter [Hinzufügen und Ausführen von Inlinecode](../logic-apps/logic-apps-add-run-inline-code.md).

> [!NOTE]
> Die Integration zwischen Logic Apps und Azure Functions funktioniert zurzeit nicht mit aktivierten Slots.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Eine Funktions-App in Azure, bei der es sich um einen Container für Azure-Funktionen handelt, zusammen mit Ihrer Azure-Funktion. Wenn noch keine Funktionen-App vorhanden ist, [erstellen Sie zuerst Ihre Funktionen-App](../azure-functions/functions-create-first-azure-function.md). Sie können dann Ihre Funktion außerhalb Ihrer Logik-App im Azure-Portal oder [aus Ihrer Logik-App heraus](#create-function-designer) im Logik-App-Designer erstellen.

* Wenn Sie mit Logik-Apps arbeiten, gelten sowohl für vorhandene als auch für neue Funktions-Apps und Funktionen dieselben Anforderungen:

  * Ihre Funktions-App und Logik-App müssen dasselbe Azure-Abonnement verwenden.

  * Neue Funktions-Apps müssen entweder .NET oder JavaScript als Laufzeitstapel verwenden. Wenn Sie vorhandenen Funktions-Apps eine neue Funktion hinzufügen, haben Sie die Wahl zwischen C# und JavaScript.

  * Ihre Funktion verwendet die **HTTP-Trigger**-Vorlage.

    Die HTTP-Trigger-Vorlage kann Inhalte mit dem Typ `application/json` von Ihrer Logik-App akzeptieren. Wenn Sie Ihrer Logik-App eine Azure-Funktion hinzufügen, zeigt der Logik-App-Designer benutzerdefinierte Funktionen, die im Rahmen Ihres Azure-Abonnements aus dieser Vorlage erstellt wurden.

  * Ihre Funktion verwendet keine benutzerdefinierten Routen – es sei denn, dass Sie eine [OpenAPI-Definition](../azure-functions/functions-openapi-definition.md) festgelegt haben (früher als [Swagger-Datei](https://swagger.io/) bezeichnet).

  * Wenn Sie für Ihre Funktion eine OpenAPI-Definition festgelegt haben, erhalten Sie im Logik-App-Designer eine umfangreichere Benutzeroberfläche für die Arbeit mit Funktionsparametern. Bevor Ihre Logik-App Funktionen mit OpenAPI-Definitionen finden und darauf zugreifen kann, [müssen Sie Ihre Funktionen-App mit diesen Schritten einrichten](#function-swagger).

* Die Logik-App, in der Sie die Funktion hinzufügen möchten, einschließlich eines [Triggers](../logic-apps/logic-apps-overview.md#logic-app-concepts) als erstem Schritt in Ihrer Logik-App

  Bevor Sie Aktionen zum Ausführen von Funktionen hinzufügen können, muss Ihre Logik-App über einen Trigger gestartet werden. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md) und [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>Suchen von Funktionen, die OpenAPI-Beschreibungen besitzen

Um eine umfassendere Benutzeroberfläche zu erhalten, wenn Sie im Logik-App-Designer mit Funktionsparametern arbeiten, [generieren Sie eine OpenAPI-Definition](../azure-functions/functions-openapi-definition.md), früher als [Swagger-Datei](https://swagger.io/) bezeichnet, für Ihre Funktion. Wenn Sie Ihre Funktionen-App so einrichten möchten, dass Ihre Logik-App Funktionen, die über Swagger-Beschreibungen verfügen, finden und nutzen kann, führen Sie die folgenden Schritte aus:

1. Stellen Sie sicher, dass Ihre Funktions-App aktiv ausgeführt wird.

1. Richten Sie in Ihrer Funktions-App die [Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) mit den folgenden Schritten ein, damit alle Ursprünge zulässig sind:

   1. Wählen Sie in der Liste **Funktions-Apps** Ihre Funktions-App aus. Wählen Sie im rechten Bereich **Plattformfeatures** > **CORS** aus.

      ![Auswählen von Funktionen-App > Plattformfeatures > CORS](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. Fügen Sie unter **CORS** das Sternchen-Platzhalterzeichen ( **`*`** ) hinzu, aber entfernen Sie alle anderen Ursprünge in der Liste, und wählen Sie **Speichern** aus.

      ![Legen Sie für "CORS* das Platzhalterzeichen "*" fest.](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>Zugreifen auf Eigenschaftswerte in HTTP-Anforderungen

Webhook-Funktionen können HTTP-Anforderungen als Eingaben akzeptieren und diese Anforderungen an andere Funktionen übergeben. Obwohl Logic Apps beispielsweise über [Funktionen zum Konvertieren von DateTime-Werten](../logic-apps/workflow-definition-language-functions-reference.md) verfügt, wird in diesem einfachen Beispiel für eine JavaScript-Funktion Folgendes veranschaulicht: Zugreifen auf eine Eigenschaft in einem Anforderungsobjekt, das an die Funktion übergeben wird, und Durchführen von Vorgängen für diesen Eigenschaftswert. Für den Zugriff auf Eigenschaften in Objekten wird in diesem Beispiel der Operator [Punkt (.)](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors) verwendet:

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

In dieser Funktion läuft Folgendes ab:

1. Die Funktion erstellt die Variable `data` und weist das `body`-Objekt im `request`-Objekt dieser Variablen zu. In der Funktion wird der Operator „Punkt“ (.) eingesetzt, um auf das `body`-Objekt im `request`-Objekt zu verweisen:

   ```javascript
   var data = request.body;
   ```

1. Die Funktion kann nun über die Variable `data` auf die `date`-Eigenschaft zugreifen und diesen Eigenschaftswert aus dem DateTime-Typ in den DateString-Typ konvertieren, indem die Funktion `ToDateString()` aufgerufen wird. Die Funktion gibt auch das Ergebnis über die `body`-Eigenschaft in der Antwort der Funktion zurück:

   ```javascript
   body: data.date.ToDateString();
   ```

Nachdem Sie nun Ihre Azure-Funktion erstellt haben, können Sie die Schritte zum [Hinzufügen von Funktionen zu Logik-Apps](#add-function-logic-app) ausführen.

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Erstellen von Funktionen innerhalb von Logik-Apps

Bevor Sie eine Azure-Funktion erstellen können, indem Sie in Ihrer Logik-App im Logik-App-Designer beginnen, müssen Sie zuerst über eine Azure-Funktions-App verfügen. Hierbei handelt es sich um einen Container für Ihre Funktionen. Erstellen Sie zuerst diese Funktionen-App, falls sie noch nicht vorhanden ist. Weitere Informationen finden Sie unter [Erstellen Ihrer ersten Funktion im Azure-Portal](../azure-functions/functions-create-first-azure-function.md).

1. Öffnen Sie Ihre Logik-App über das [Azure-Portal](https://portal.azure.com) im Logik-App-Designer.

1. Führen Sie zum Erstellen und Hinzufügen Ihrer Funktion den Schritt aus, der für Ihr Szenario zutrifft:

   * Wählen Sie unter dem letzten Schritt im Workflow Ihrer Logik-App **Neuer Schritt** aus.

   * Bewegen Sie Ihren Mauszeiger zwischen vorhandenen Schritten im Workflow Ihrer Logik-App auf den Pfeil, wählen Sie das Pluszeichen (+) und dann **Aktion hinzufügen** aus.

1. Geben Sie im Suchfeld „azure functions“ als Filter ein. Wählen Sie in der Liste mit den Aktionen diese Aktion aus: **Azure-Funktion auswählen – Azure Functions**

   ![Suchen nach „Azure Functions“](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Wählen Sie in der Liste mit den Funktionen-Apps Ihre Funktionen-App aus. Wählen diese Aktion aus, nachdem die Liste mit den Aktionen geöffnet wurde: **Azure Functions – Neue Funktion erstellen**

   ![Auswählen Ihrer Funktionen-App](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. Definieren Sie im Editor für Funktionsdefinitionen Ihre Funktion:

   1. Geben Sie im Feld **Funktionsname** einen Namen für Ihre Funktion an.

   1. Fügen Sie im Feld **Code** der Funktionsvorlage Ihren Code hinzu. Binden Sie auch die Antwort und Nutzlast ein, die für Ihre Logik-App zurückgegeben werden soll, nachdem die Ausführung Ihrer Funktion abgeschlossen ist.

      ![Definieren Ihrer Funktion](./media/logic-apps-azure-functions/function-definition.png)

      Im Code der Vorlage bezieht sich das *`context`-Objekt* auf die Nachricht, die Ihre Logik-App in einem späteren Schritt über das Feld **Anforderungstext** sendet. Verwenden Sie für den Zugriff auf die Eigenschaften des `context`-Objekts aus Ihrer Funktion heraus die folgende Syntax:

      `context.body.<property-name>`

      Um beispielsweise auf die `content`-Eigenschaft im `context`-Objekt zu verweisen, verwenden Sie diese Syntax: 

      `context.body.content`

      Der Vorlagencode enthält auch eine `input`-Variable, in der der Wert aus dem Parameter `data` gespeichert wird, damit Ihre Funktion Vorgänge an diesem Wert durchführen kann. Innerhalb der JavaScript-Funktionen ist die `data`-Variable außerdem eine Kurzform für `context.body`.

      > [!NOTE]
      > Die `body`-Eigenschaft gilt hier für das `context`-Objekt und ist mit dem **Body**-Token aus der Ausgabe einer Aktion nicht identisch, das Sie möglicherweise ebenfalls an Ihre Funktion übergeben.

   1. Wenn Sie fertig sind, wählen Sie **Erstellen** aus.

1. Geben Sie im Feld **Anforderungstext** die Eingabe für Ihre Funktion ein, die als JSON-Objekt (JavaScript Object Notation) formatiert werden muss.

   Diese Eingabe ist das *Kontextobjekt* oder die Nachricht, das bzw. die von Ihrer Logik-App an Ihre Funktion gesendet wird. Wenn Sie in das Feld **Anforderungstext** klicken, wird die Liste mit dem dynamischen Inhalt angezeigt, und Sie können Token für Ausgaben aus vorherigen Schritten auswählen. In diesem Beispiel wird angegeben, dass die Kontextnutzlast die Eigenschaft `content` enthält, die den Wert des Tokens **Von** aus dem E-Mail-Trigger aufweist:

   ![Beispiel „Anforderungstext“ –Nutzlast des Kontextobjekts](./media/logic-apps-azure-functions/function-request-body-example.png)

   Hier wird das Kontextobjekt nicht in eine Zeichenfolge umgewandelt, sodass der Inhalt des Objekts der JSON-Nutzlast direkt hinzugefügt wird. Wenn das Kontextobjekt aber kein JSON-Token ist, das eine Zeichenfolge, ein JSON-Objekt oder ein JSON-Array übergibt, wird eine Fehlermeldung angezeigt. Wurde in diesem Beispiel stattdessen das Token **Empfangszeit** verwendet, können Sie das Kontextobjekt in eine Zeichenfolge umwandeln, indem Sie doppelte Anführungszeichen hinzufügen:  

   ![Umwandeln eines Objekts in eine Zeichenfolge](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Um weitere Details wie die zu verwendende Methode, Anforderungskopfzeilen oder Abfrageparameter anzugeben, öffnen Sie die Liste **Neuen Parameter hinzufügen**, und wählen Sie die gewünschten Optionen aus.

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Hinzufügen von vorhandenen Funktionen zu Logik-Apps

Zum Aufrufen von vorhandenen Azure-Funktionen aus Ihren Logik-Apps können Sie Azure-Funktionen wie alle anderen Aktionen im Logik-App-Designer hinzufügen.

1. Öffnen Sie Ihre Logik-App über das [Azure-Portal](https://portal.azure.com) im Logik-App-Designer.

1. Wählen Sie im Schritt zum Hinzufügen der Funktion **Neuer Schritt** und dann **Aktion hinzufügen** aus.

1. Geben Sie im Suchfeld „azure functions“ als Filter ein. Wählen Sie in der Liste mit den Aktionen diese Aktion aus: **Azure-Funktion auswählen – Azure Functions**

   ![Suchen nach „Azure Functions“](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Wählen Sie in der Liste mit den Funktionen-Apps Ihre Funktionen-App aus. Wählen Sie Ihre Funktion aus, wenn die Liste mit den Funktionen angezeigt wird.

   ![Auswählen Ihrer Funktionen-App und Azure-Funktion](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Für Funktionen, die über API-Definitionen (Swagger-Beschreibungen) verfügen und [so eingerichtet sind, dass Ihre Logik-App diese Funktionen finden und darauf zugreifen kann](#function-swagger), können Sie **Swagger-Aktionen** auswählen:

   ![Auswählen von Funktionen-App, „Swagger-Aktionen“ und Azure-Funktion](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. Geben Sie im Feld **Anforderungstext** die Eingabe für Ihre Funktion ein, die als JSON-Objekt (JavaScript Object Notation) formatiert werden muss.

   Diese Eingabe ist das *Kontextobjekt* oder die Nachricht, das bzw. die von Ihrer Logik-App an Ihre Funktion gesendet wird. Wenn Sie in das Feld **Anforderungstext** klicken, wird die Liste mit dem dynamischen Inhalt angezeigt, und Sie können Token für Ausgaben aus vorherigen Schritten auswählen. In diesem Beispiel wird angegeben, dass die Kontextnutzlast die Eigenschaft `content` enthält, die den Wert des Tokens **Von** aus dem E-Mail-Trigger aufweist:

   ![Beispiel „Anforderungstext“ –Nutzlast des Kontextobjekts](./media/logic-apps-azure-functions/function-request-body-example.png)

   Hier wird das Kontextobjekt nicht in eine Zeichenfolge umgewandelt, sodass der Inhalt des Objekts der JSON-Nutzlast direkt hinzugefügt wird. Wenn das Kontextobjekt aber kein JSON-Token ist, das eine Zeichenfolge, ein JSON-Objekt oder ein JSON-Array übergibt, wird eine Fehlermeldung angezeigt. Wurde in diesem Beispiel stattdessen das Token **Empfangszeit** verwendet, können Sie das Kontextobjekt in eine Zeichenfolge umwandeln, indem Sie doppelte Anführungszeichen hinzufügen:

   ![Umwandeln eines Objekts in eine Zeichenfolge](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Um weitere Details wie die zu verwendende Methode, Anforderungskopfzeilen oder Abfrageparameter anzugeben, öffnen Sie die Liste **Neuen Parameter hinzufügen**, und wählen Sie die gewünschten Optionen aus.

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>Aufrufen von Logik-Apps aus Azure Functions

Wenn Sie eine Logik-App aus einer Azure-Funktion auslösen möchten, muss diese App mit einem Trigger starten, der einen aufrufbaren Endpunkt bereitstellt. So können Sie beispielsweise die Logik-App mit dem Trigger **HTTP**, **Anforderung**, **Azure-Warteschlangen** oder **Event Grid** starten. Senden Sie in Ihrer Funktion eine HTTP POST-Anforderung an die URL des Triggers, und binden Sie die Nutzlast ein, die von dieser Logik-App verarbeitet werden soll. Weitere Informationen hierzu finden Sie unter [Aufrufen, Auslösen oder Schachteln von Logik-Apps](../logic-apps/logic-apps-http-endpoint.md).

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Logic Apps-Connectors](../connectors/apis-list.md).
