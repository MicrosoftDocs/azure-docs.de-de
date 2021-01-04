---
title: Hinzufügen und Aufrufen von Azure Functions aus Azure Logic Apps
description: Aufrufen und Ausführen benutzerdefinierten Codes in Ihren Azure Functions aus automatisierten Aufgaben und Workflows in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/01/2019
ms.custom: devx-track-js
ms.openlocfilehash: 75693c57a8d120aad53a15d03ae4054bac8262af
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023056"
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

Sie können Azure-Funktionen direkt aus dem Workflow Ihrer Logik-App erstellen, indem Sie die integrierte Azure Functions-Aktion im Logik-App-Designer verwenden. Sie können diese Methode jedoch nur für in JavaScript geschriebene Azure-Funktionen verwenden. Für andere Sprachen können Sie Azure Functions-Instanzen auf der Azure Functions-Oberfläche im Azure-Portal erstellen. Weitere Informationen finden Sie unter [Erstellen Ihrer ersten Funktion im Azure-Portal](../azure-functions/functions-create-first-azure-function.md).

Bevor Sie jedoch eine Azure-Funktion erstellen können, müssen Sie bereits über eine Azure-Funktions-App verfügen, die ein Container für Ihre Funktionen ist. Erstellen Sie zuerst diese Funktionen-App, falls sie noch nicht vorhanden ist. Weitere Informationen finden Sie unter [Erstellen Ihrer ersten Funktion im Azure-Portal](../azure-functions/functions-create-first-azure-function.md).

1. Öffnen Sie Ihre Logik-App über das [Azure-Portal](https://portal.azure.com) im Logik-App-Designer.

1. Führen Sie zum Erstellen und Hinzufügen Ihrer Funktion den Schritt aus, der für Ihr Szenario zutrifft:

   * Wählen Sie unter dem letzten Schritt im Workflow Ihrer Logik-App **Neuer Schritt** aus.

   * Bewegen Sie Ihren Mauszeiger zwischen vorhandenen Schritten im Workflow Ihrer Logik-App auf den Pfeil, wählen Sie das Pluszeichen (+) und dann **Aktion hinzufügen** aus.

1. Geben Sie im Suchfeld „azure functions“ als Filter ein. Wählen Sie in der Aktionsliste beispielsweise die Aktion **Azure-Funktion wählen** aus.

   ![Suchen nach „Azure Functions“](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Wählen Sie in der Liste mit den Funktionen-Apps Ihre Funktionen-App aus. Wählen diese Aktion aus, nachdem die Liste mit den Aktionen geöffnet wurde: **Erstellen einer neuen Funktion**

   ![Auswählen Ihrer Funktionen-App](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. Definieren Sie im Editor für Funktionsdefinitionen Ihre Funktion:

   1. Geben Sie im Feld **Funktionsname** einen Namen für Ihre Funktion an.

   1. Fügen Sie im Feld **Code** der Funktionsvorlage Ihren Code hinzu. Binden Sie auch die Antwort und Nutzlast ein, die für Ihre Logik-App zurückgegeben werden soll, nachdem die Ausführung Ihrer Funktion abgeschlossen ist. Wählen Sie **Erstellen**, wenn Sie fertig sind.

   Beispiel:

   ![Definieren Ihrer Funktion](./media/logic-apps-azure-functions/add-code-function-definition.png)

   Im Code der Vorlage bezieht sich das *`context`-Objekt* auf die Nachricht, die Ihre Logik-App in einem späteren Schritt über das Feld **Anforderungstext** sendet. Verwenden Sie für den Zugriff auf die Eigenschaften des `context`-Objekts aus Ihrer Funktion heraus die folgende Syntax:

   `context.body.<property-name>`

   Um beispielsweise auf die `content`-Eigenschaft im `context`-Objekt zu verweisen, verwenden Sie diese Syntax:

   `context.body.content`

   Der Vorlagencode enthält auch eine `input`-Variable, in der der Wert aus dem Parameter `data` gespeichert wird, damit Ihre Funktion Vorgänge an diesem Wert durchführen kann. Innerhalb der JavaScript-Funktionen ist die `data`-Variable außerdem eine Kurzform für `context.body`.

   > [!NOTE]
   > Die `body`-Eigenschaft gilt hier für das `context`-Objekt und ist mit dem **Body**-Token aus der Ausgabe einer Aktion nicht identisch, das Sie möglicherweise ebenfalls an Ihre Funktion übergeben.

1. Geben Sie im Feld **Anforderungstext** die Eingabe für Ihre Funktion ein, die als JSON-Objekt (JavaScript Object Notation) formatiert werden muss.

   Diese Eingabe ist das *Kontextobjekt* oder die Nachricht, das bzw. die von Ihrer Logik-App an Ihre Funktion gesendet wird. Wenn Sie in das Feld **Anforderungstext** klicken, wird die Liste mit dem dynamischen Inhalt angezeigt, und Sie können Token für Ausgaben aus vorherigen Schritten auswählen. In diesem Beispiel wird angegeben, dass die Kontextnutzlast die Eigenschaft `content` enthält, die den Wert des Tokens **Von** aus dem E-Mail-Trigger aufweist.

   ![Beispiel „Anforderungstext“ –Nutzlast des Kontextobjekts](./media/logic-apps-azure-functions/function-request-body-example.png)

   Hier wird das Kontextobjekt nicht in eine Zeichenfolge umgewandelt, sodass der Inhalt des Objekts der JSON-Nutzlast direkt hinzugefügt wird. Wenn das Kontextobjekt aber kein JSON-Token ist, das eine Zeichenfolge, ein JSON-Objekt oder ein JSON-Array übergibt, wird eine Fehlermeldung angezeigt. Wurde in diesem Beispiel stattdessen das Token **Empfangszeit** verwendet, können Sie das Kontextobjekt in eine Zeichenfolge umwandeln, indem Sie doppelte Anführungszeichen hinzufügen.

   ![Umwandeln eines Objekts in eine Zeichenfolge](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Um weitere Details wie die zu verwendende Methode, Anforderungskopfzeilen, Abfrageparameter oder Authentifizierung anzugeben, öffnen Sie die Liste **Neuen Parameter hinzufügen**, und wählen Sie die gewünschten Optionen aus. Bei der Authentifizierung unterscheiden sich die Optionen je nach der von Ihnen ausgewählten Funktion. Weitere Informationen finden Sie unter [Aktivieren der Authentifizierung für Azure Functions](#enable-authentication-functions).

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Hinzufügen von vorhandenen Funktionen zu Logik-Apps

Zum Aufrufen von vorhandenen Azure-Funktionen aus Ihren Logik-Apps können Sie Azure-Funktionen wie alle anderen Aktionen im Logik-App-Designer hinzufügen.

1. Öffnen Sie Ihre Logik-App über das [Azure-Portal](https://portal.azure.com) im Logik-App-Designer.

1. Wählen Sie im Schritt zum Hinzufügen der Funktion die Option **Neuer Schritt** aus.

1. Geben Sie unter **Aktion auswählen** im Suchfeld „azure functions“ als Filter ein. Wählen Sie in der Aktionsliste die Aktion **Azure-Funktion wählen** aus.

   ![Suchen nach „Azure Functions“](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Wählen Sie in der Liste mit den Funktionen-Apps Ihre Funktionen-App aus. Wählen Sie Ihre Funktion aus, wenn die Liste mit den Funktionen angezeigt wird.

   ![Auswählen Ihrer Funktionen-App und Azure-Funktion](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Für Funktionen, die über API-Definitionen (Swagger-Beschreibungen) verfügen und [so eingerichtet sind, dass Ihre Logik-App diese Funktionen finden und darauf zugreifen kann](#function-swagger), können Sie **Swagger-Aktionen** auswählen.

   ![Auswählen von Funktions-App, „Swagger-Aktionen“ und Azure-Funktion](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. Geben Sie im Feld **Anforderungstext** die Eingabe für Ihre Funktion ein, die als JSON-Objekt (JavaScript Object Notation) formatiert werden muss.

   Diese Eingabe ist das *Kontextobjekt* oder die Nachricht, das bzw. die von Ihrer Logik-App an Ihre Funktion gesendet wird. Wenn Sie in das Feld **Anforderungstext** klicken, wird die Liste mit dem dynamischen Inhalt angezeigt, und Sie können Token für Ausgaben aus vorherigen Schritten auswählen. In diesem Beispiel wird angegeben, dass die Kontextnutzlast die Eigenschaft `content` enthält, die den Wert des Tokens **Von** aus dem E-Mail-Trigger aufweist.

   ![Beispiel „Anforderungstext“ –Nutzlast des Kontextobjekts](./media/logic-apps-azure-functions/function-request-body-example.png)

   Hier wird das Kontextobjekt nicht in eine Zeichenfolge umgewandelt, sodass der Inhalt des Objekts der JSON-Nutzlast direkt hinzugefügt wird. Wenn das Kontextobjekt aber kein JSON-Token ist, das eine Zeichenfolge, ein JSON-Objekt oder ein JSON-Array übergibt, wird eine Fehlermeldung angezeigt. Wurde in diesem Beispiel stattdessen das Token **Empfangszeit** verwendet, können Sie das Kontextobjekt in eine Zeichenfolge umwandeln, indem Sie doppelte Anführungszeichen hinzufügen:

   ![Umwandeln eines Objekts in eine Zeichenfolge](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Um weitere Details wie die zu verwendende Methode, Anforderungskopfzeilen, Abfrageparameter oder Authentifizierung anzugeben, öffnen Sie die Liste **Neuen Parameter hinzufügen**, und wählen Sie die gewünschten Optionen aus. Bei der Authentifizierung unterscheiden sich die Optionen je nach der von Ihnen ausgewählten Funktion. Weitere Informationen finden Sie unter [Aktivieren der Authentifizierung in Azure-Funktionen](#enable-authentication-functions).

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>Aufrufen von Logik-Apps aus Azure Functions

Wenn Sie eine Logik-App aus einer Azure-Funktion auslösen möchten, muss diese App mit einem Trigger starten, der einen aufrufbaren Endpunkt bereitstellt. So können Sie beispielsweise die Logik-App mit dem Trigger **HTTP**, **Anforderung**, **Azure-Warteschlangen** oder **Event Grid** starten. Senden Sie in Ihrer Funktion eine HTTP POST-Anforderung an die URL des Triggers, und binden Sie die Nutzlast ein, die von dieser Logik-App verarbeitet werden soll. Weitere Informationen hierzu finden Sie unter [Aufrufen, Auslösen oder Schachteln von Logik-Apps](../logic-apps/logic-apps-http-endpoint.md).

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-azure-functions"></a>Aktivieren der Authentifizierung für Azure-Funktionen

Um den Zugriff auf andere Ressourcen, die von Azure Active Directory (Azure AD) geschützt werden, ohne Anmeldung oder Bereitstellung von Anmeldeinformationen oder Geheimnissen einfach zu authentifizieren, kann Ihre Logik-App eine [verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md) (früher als verwaltete Dienstidentität (Managed Service Identity, MSI) bezeichnet) verwenden. Azure verwaltet diese Identität für Sie und dient als Hilfe beim Schützen Ihrer Anmeldeinformationen, da Sie keine Geheimnisse angeben oder eine Rotation dafür durchführen müssen. Erfahren Sie mehr zu [Azure-Diensten, die verwaltete Identitäten für die Azure AD-Authentifizierung unterstützen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

Wenn Sie Ihre Logik-App so einrichten, dass sie die vom System zugewiesene verwaltete Identität oder eine manuell erstellte benutzerseitig zugewiesene Identität verwendet, können die Azure-Funktionen in Ihrer Logik-App auch dieselbe Identität für die Authentifizierung verwenden. Weitere Informationen zur Unterstützung der Authentifizierung für Azure-Funktionen in Logik-Apps finden Sie unter [Hinzufügen von Authentifizierung zu ausgehenden Aufrufen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

Um die verwaltete Identität mit Ihrer Funktion einzurichten und zu verwenden, führen Sie diese Schritte aus:

1. Aktivieren Sie die verwaltete Identität in Ihrer Logik-App, und richten den Zugriff dieser Identität auf die Zielressource ein. Weitere Informationen finden Sie unter [Authentifizieren des Zugriffs auf Azure-Ressourcen mithilfe verwalteter Identitäten in Azure Logic Apps](../logic-apps/create-managed-service-identity.md).

1. Aktivieren Sie die Authentifizierung in ihrer Azure-Funktion und der Funktions-App, indem Sie folgende Schritte ausführen:

   * [Einrichten der anonymen Authentifizierung in ihrer Funktion](#set-authentication-function-app)
   * [Einrichten der Azure AD-Authentifizierung in ihrer Funktions-App](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>Einrichten der anonymen Authentifizierung in ihrer Funktion

Um die verwaltete Identität Ihrer Logik-App in Ihrer Azure-Funktion zu verwenden, müssen Sie die Authentifizierungsebene Ihrer Funktion auf „Anonym“ festlegen. Andernfalls löst die Logik-App einen „BadRequest“-Fehler aus.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach Ihrer Funktions-App, und wählen Sie sie aus. In diesen Schritten wird „FabrikamFunctionApp“ als Beispiel für eine Funktions-App verwendet.

1. Wählen Sie im Bereich „Funktions-App“ **Plattformfeatures** aus. Wählen Sie unter **Entwicklungstools** die Option **Erweiterte Tools (Kudu)** aus.

   ![Öffnen der erweiterten Tools für Kudu](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. Wählen Sie in der Titelleiste der Kudu-Website im Menü **Debugging-Konsole** **CMD** aus.

   ![Auswählen der Option „CMD“ im Menü „Debugging-Konsole“](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. Wählen Sie auf der angezeigten nächsten Seite **Website** > **wwwroot** > *Ihre Funktion* aus der Ordnerliste aus. In diesen Schritten wird „FabrikamAzureFunction“ als Beispielfunktion verwendet.

   ![Auswählen von „Website“ > „wwwroot“ > Ihre Funktion](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. Öffnet die Datei `function.json` zur Bearbeitung.

   ![Klicken auf „Bearbeiten“ der „function.json“-Datei](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. Überprüfen Sie im Objekt `bindings`, ob die Eigenschaft `authLevel` vorhanden ist. Wenn die Eigenschaft vorhanden ist, setzen Sie den Eigenschaftswert auf `anonymous`. Fügen Sie die Eigenschaft andernfalls hinzu und legen Sie den Wert fest.

   ![Hinzufügen der Eigenschaft „authLevel“ und Festlegen auf „Anonym“](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. Wenn Sie fertig sind, speichern Sie Ihre Einstellungen, und fahren Sie mit den nächsten Abschnitt fort.

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>Einrichten der Azure AD-Authentifizierung für ihre Funktions-App

Suchen Sie diese Werte vor dem Start dieser Aufgabe, und legen Sie sie zur späteren Verwendung zur Seite:

* Die für die vom System zugewiesene Identität generierte Objekt-ID, die Ihre Logik-App darstellt

  * Um diese Objekt-ID zu generieren, müssen [Sie die vom System zugewiesene Identität Ihrer Logik-App aktivieren](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app).

  * Um diese Objekt-ID zu finden, öffnen Sie ansonsten Ihre Logik-App im Logik-App-Designer. Wählen Sie im Menü Ihrer Logik-App unter **Einstellungen** die Optionen **Identität** > **Vom System zugewiesen** aus.

* Die Verzeichnis-ID für Ihren Mandanten in Azure Active Directory (Azure AD)

  Um die Verzeichnis-ID Ihres Mandanten abzurufen, führen Sie den PowerShell-Befehl [`Get-AzureAccount`](/powershell/module/servicemanagement/azure.service/get-azureaccount) aus. Oder gehen Sie im Azure-Portal wie folgt vor:

  1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach Ihrer Funktions-App, und wählen Sie sie aus.

  1. Suchen Sie Ihren Azure AD-Mandanten, und wählen Sie ihn aus. In diesen Schritten wird „Fabrikam“ als Beispielmandant verwendet.

  1. Wählen Sie im Menü des Mandanten unter **Verwalten** die Option **Eigenschaften** aus.

  1. Kopieren Sie z. B. die Verzeichnis-ID Ihres Mandanten, und speichern Sie diese für die spätere Verwendung.

     ![Suchen und Kopieren der Verzeichnis-ID des Azure AD-Mandanten](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* Die Ressourcen-ID der Zielressource, auf die Sie zugreifen möchten

  * Für Informationen dazu, wie Sie diese Ressourcen-IDs finden, lesen Sie [Azure-Dienste, die die Azure AD-Authentifizierung unterstützen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

  > [!IMPORTANT]
  > Diese Zielressourcen-ID muss genau dem Wert entsprechen, den Azure AD erwartet, einschließlich aller erforderlichen nachgestellten Schrägstriche.

  Diese Ressourcen-ID ist außerdem derselbe Wert, den Sie später in der Eigenschaft **Zielgruppe** verwenden, wenn Sie [Ihre Funktionsaktion so einrichten, dass sie die vom System zugewiesene Identität verwendet](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

Jetzt können Sie die Azure AD-Authentifizierung für ihre Funktions-App einrichten.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach Ihrer Funktions-App, und wählen Sie sie aus.

1. Wählen Sie im Bereich „Funktions-App“ **Plattformfeatures** aus. Wählen Sie unter **Netzwerk** die Option **Authentifizierung/Autorisierung** aus.

   ![Anzeigen von Authentifizierung und Autorisierung](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. Ändern Sie die Einstellung **App Service-Authentifizierung** in **Ein**. Wählen Sie in der Liste **Die auszuführende Aktion, wenn die Anforderung nicht authentifiziert ist** die Option **Mit Azure Active Directory anmelden** aus. Wählen Sie unter **Authentifizierungsanbieter** die Option **Azure Active Directory** aus.

   ![Aktivieren der Authentifizierung mit Azure AD](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. Führen Sie im Bereich **Azure Active Directory-Einstellungen** die folgenden Schritte aus:

   1. Legen Sie den **Verwaltungsmodus** auf **Erweitert** fest.

   1. Geben Sie unter **Client-ID** die Objekt-ID für die vom System zugewiesene Identität Ihrer Logik-App ein.

   1. Geben Sie in der Eigenschaft **Aussteller-URL** die URL `https://sts.windows.net/` ein, und fügen Sie die Verzeichnis-ID Ihres Azure AD-Mandanten an.

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. Geben Sie in der Eigenschaft **Zulässige Tokenzielgruppe** die Ressourcen-ID der Zielressource ein, auf die Sie zugreifen möchten.

      Diese Ressourcen-ID ist derselbe Wert, den Sie später in der Eigenschaft **Zielgruppe** verwenden, wenn Sie [Ihre Funktionsaktion so einrichten, dass sie die vom System zugewiesene Identität verwendet](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

   An dieser Stelle sieht Ihre Version in etwa wie in diesem Beispiel aus:

   ![Azure Active Directory-Authentifizierungseinstellungen](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. Wenn Sie fertig sind, wählen Sie **OK**.

1. Kehren Sie zum Logik-App-Designer zurück, und führen Sie die [Schritte zum Authentifizieren des Zugriffs mit der verwalteten Identität](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity) aus.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Logic Apps-Connectors](../connectors/apis-list.md).
