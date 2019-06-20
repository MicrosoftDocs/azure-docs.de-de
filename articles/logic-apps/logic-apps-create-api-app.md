---
title: Erstellen von Web- und REST-APIs für Azure Logic Apps | Microsoft-Dokumentation
description: Erstellen von Web- und REST-APIs zum Aufrufen Ihrer APIs, Dienste oder Systeme für die Systemintegration in Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.topic: article
ms.assetid: bd229179-7199-4aab-bae0-1baf072c7659
ms.date: 05/26/2017
ms.openlocfilehash: 620ede672d71338abeff5198fd5f94e92dc193d0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60681826"
---
# <a name="create-custom-apis-you-can-call-from-azure-logic-apps"></a>Erstellen benutzerdefinierter APIs, die über Azure Logic Apps aufgerufen werden können

Obwohl Azure Logic Apps [100+ integrierte Connectors](../connectors/apis-list.md) bietet, die Sie in Logik-App-Workflows verwenden können, möchten Sie möglicherweise APIs, Systeme und Dienste aufrufen, die nicht als Connectors zur Verfügung stehen. Sie können eigene APIs erstellen, die Aktionen und Trigger zur Verwendung in Logik-Apps bereitstellen. Es gibt noch andere Gründe, warum Sie möglicherweise eigene APIs erstellen möchten, die Sie in Logik-App-Workflows aufrufen können:

* Erweitern Ihrer aktuellen Systemintegrations- und Datenintegrationsworkflows.
* Um Kunden zu helfen, Ihren Dienst zum Verwalten professioneller oder persönlicher Aufgaben zu verwenden.
* Erweitern von Reichweite, Erkennbarkeit und Verwendung Ihres Diensts.

Connectors sind grundsätzlich Web-APIs, die REST-APIs für austauschbare Schnittstellen, [Swagger-Metadatenformat](https://swagger.io/specification/) für Dokumentation und JSON als Datenaustauschformat verwenden. Da Connectors REST-APIs sind, die über HTTP-Endpunkte kommunizieren, können Sie eine beliebige Sprache wie .NET, Java oder Node.js für die Erstellung von Connectors verwenden. Sie können Ihre APIs auch in [Azure App Service](../app-service/overview.md) hosten, einem „Platform as a Service“-Angebot (PaaS), das eine der besten, einfachsten und skalierbarsten Möglichkeiten zum Hosten von APIs bietet. 

Damit benutzerdefinierte APIs mit Logik-Apps funktionieren, kann Ihre API [*Aktionen*](./logic-apps-overview.md#logic-app-concepts) bereitstellen, die bestimmte Aufgaben in Logis App-Workflows ausführen. Ihre API kann auch als [*Trigger*](./logic-apps-overview.md#logic-app-concepts) dienen, der einen Logik-App-Workflow startet, wenn neue Daten oder ein Ereignis eine angegebene Bedingung erfüllen. Dieses Thema beschreibt allgemeine Muster, die Sie zum Erstellen von Aktionen und Triggern in Ihrer API befolgen können, basierend auf dem Verhalten, dass Ihre API bieten soll.

Sie können Ihre APIs in [Azure App Service](../app-service/overview.md) hosten, einem PaaS-Angebot (Platform-as-a-Service), das ein einfaches API-Hosting mit hoher Skalierbarkeit ermöglicht.

> [!TIP] 
> Sie können Ihre APIs zwar als Web-Apps bereitstellen, sollten jedoch in Erwägung ziehen, Ihre APIs als API-Apps bereitzustellen. Dadurch wird das Erstellen, Hosten und Nutzen der APIs in der Cloud und lokal vereinfacht. Sie müssen keinen Code in Ihren APIs ändern. Stellen Sie einfach Ihren Code für eine API-App bereit. Erfahren Sie beispielsweise, wie Sie mit den folgenden Sprachen API-Apps erstellen: 
> 
> * [ASP.NET](../app-service/app-service-web-get-started-dotnet.md). 
> * [Java](../app-service/app-service-web-get-started-java.md)
> * [Node.js](../app-service/app-service-web-get-started-nodejs.md)
> * [PHP](../app-service/app-service-web-get-started-php.md)
> * [Python](../app-service/containers/quickstart-python.md)
> * [Ruby](../app-service/containers/quickstart-ruby.md)
>
> API-App-Beispiele für Logik-Apps finden Sie im [Azure Logic Apps GitHub-Repository](https://github.com/logicappsio) oder [Blog](https://aka.ms/logicappsblog).

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>Wie unterscheiden sich benutzerdefinierte APIs von benutzerdefinierten Connectors?

Benutzerdefinierte APIs und [benutzerdefinierte Connectors](../logic-apps/custom-connector-overview.md) sind Web-APIs, die REST-APIs für austauschbare Schnittstellen, das [Swagger-Metadatenformat](https://swagger.io/specification/) für Dokumentation und JSON als Datenaustauschformat verwenden. Da diese APIS und Connectors REST-APIs sind, die über HTTP-Endpunkte kommunizieren, können Sie eine beliebige Sprache wie .NET, Java oder Node.js für die Erstellung benutzerdefinierter APIs und Connectors verwenden.

Mit benutzerdefinierten APIs können Sie APIs aufrufen, die keine Connectors sind, und Endpunkte bereitstellen, die Sie mit HTTP + Swagger, Azure API Management oder App Services aufrufen können. Benutzerdefinierte Connectors funktionieren wie benutzerdefinierte APIs, haben aber auch diese Merkmale:

* Werden als Logic Apps-Connectorressourcen in Azure registriert.
* Werden im Designer für Logic Apps mit Symbolen neben von Microsoft verwalteten Connectors angezeigt.
* Sind nur für die Ersteller der Connectors und Benutzer von Logik-Apps verfügbar, die denselben Azure Active Directory-Mandanten und dasselbe Azure-Abonnement in der Region haben, in der die Logik-Apps bereitgestellt werden.

Sie können registrierte Connectors auch für die Microsoft-Zertifizierung vorschlagen. Dieser Prozess überprüft, ob registrierte Connectors die Kriterien für die öffentliche Nutzung erfüllen, und stellt diese Connectors Benutzern in Microsoft Flow und Microsoft PowerApps zur Verfügung.

Weitere Informationen zu benutzerdefinierten Connectors finden Sie unter 

* [Übersicht über benutzerdefinierte Connectors](../logic-apps/custom-connector-overview.md)
* [Erstellen benutzerdefinierter Connectors aus Web-APIs](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Registrieren benutzerdefinierter Connectors in Azure Logic Apps](../logic-apps/logic-apps-custom-connector-register.md)

## <a name="helpful-tools"></a>Nützliche Tools

Eine benutzerdefinierte API funktioniert am besten mit Logik-Apps, wenn die API auch über ein [Swagger-Dokument](https://swagger.io/specification/) verfügt, das die API-Vorgänge und -Parameter beschreibt.
Viele Bibliotheken wie [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle) können die Swagger-Datei automatisch für Sie generieren. Um die Swagger-Datei für Anzeigenamen, Eigenschaftentypen usw. mit Anmerkungen zu versehen, können Sie auch [TRex](https://github.com/nihaue/TRex) verwenden, damit Ihre Swagger-Datei gut mit Logik-Apps funktioniert.

<a name="actions"></a>

## <a name="action-patterns"></a>Aktionsmuster

Damit Logik-Apps Aufgaben ausführen, sollte Ihre benutzerdefinierte API [*Aktionen*](./logic-apps-overview.md#logic-app-concepts) bereitstellen. Jeder Vorgang in Ihrer API wird einer Aktion zugeordnet. Eine einfache Aktion ist ein Controller, der HTTP-Anforderungen akzeptiert und HTTP-Antworten zurückgibt. Beispielsweise sendet eine Logik-App eine HTTP-Anforderung an Ihre Web-App oder API-App. Ihre App gibt dann eine HTTP-Antwort zurück, zusammen mit Inhalt, den die Logik-App verarbeiten kann.

Für Standardaktionen können Sie eine HTTP-Anforderungsmethode in Ihre API schreiben und diese Methode in einer Swagger-Datei beschreiben. Sie können dann Ihre API direkt mit einer [HTTP-Aktion](../connectors/connectors-native-http.md) oder einer [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)-Aktion aufrufen. Standardmäßig müssen Antworten innerhalb des [Timeoutlimits für die Anforderung](./logic-apps-limits-and-config.md) zurückgegeben werden. 

![Standardaktionsmuster](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a> Damit eine Logik-App wartet, während Ihre API zeitaufwändigere Aufgaben abschließt, kann Ihre API dem in diesem Thema beschriebenen [asynchronen Abrufmuster](#async-pattern) oder [Webhookmuster](#webhook-actions) folgen. Stellen Sie sich als Analogie, anhand der Sie sich die unterschiedlichen Verhaltensweisen dieser Muster veranschaulichen können, den Prozess der Bestellung eines nach Kundenwünschen gebackenen Kuchens bei einer Bäckerei vor. Das Abrufmuster entspricht dem Verhalten, dass Sie alle 20 Minuten in der Bäckerei nachfragen, ob der Kuchen fertig ist. Das Webhookmuster entspricht dem Verhalten, dass die Bäckerei nach Ihrer Telefonnummer fragt, um Sie anrufen zu können, wenn der Kuchen fertig ist.

Beispiele finden Sie im [Logic Apps GitHub-Repository](https://github.com/logicappsio). Erfahren Sie auch mehr über [die Nutzung der Messung für Aktionen](logic-apps-pricing.md).

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Ausführen zeitaufwändiger Aufgaben mit dem Abrufaktionsmuster

Damit Ihre API Aufgaben ausführen kann, deren Ausführungsdauer das [Timeoutlimit für die Anforderung](./logic-apps-limits-and-config.md) überschreiten könnte, können Sie das asynchrone Abrufmuster verwenden. Bei diesem Muster arbeitet Ihre API in einem separaten Thread, behält aber eine aktive Verbindung mit der Logic Apps-Engine bei. So wird kein Timeout für die Logik-App wirksam, noch wird mit dem nächsten Schritt im Workflow fortgefahren, bevor die Ausführung Ihrer API abgeschlossen ist.

Das allgemeine Muster sieht so aus:

1. Stellen Sie sicher, dass die Engine weiß, dass Ihre API die Anforderung akzeptiert und die Verarbeitung gestartet hat.
2. Wenn die Engine darauffolgende Anforderungen zum Auftragsstatus stellt, sorgen Sie dafür, dass die Engine informiert wird, wenn Ihre API die Aufgabe abschließt.
3. Lassen Sie relevante Daten an die Engine zurückgeben, sodass der Logik-App-Workflow fortgesetzt werden kann.

<a name="bakery-polling-action"></a> Wenden Sie nun die obige Bäckereianalogie auf das Abrufmuster an, und stellen Sie sich vor, dass Sie in einer Bäckerei anrufen und einen Kuchen nach Ihren Wünschen bestellen. Die Herstellung des Kuchens braucht ihre Zeit, und Sie möchten nicht am Telefon warten, während die Bäckerei den Kuchen backt. Die Bäckerei bestätigt Ihre Bestellung, und Sie rufen alle 20 Minuten an, um den Status des Kuchens abzufragen. Nach 20 Minuten rufen Sie in der Bäckerei an, aber sie erfahren, dass der Kuchen noch nicht fertig ist, und Sie nach weiteren 20 Minuten anrufen sollen. Dieses Hin und Her und wird fortgesetzt, bis die Bäckerei Ihnen, als Sie anrufen, mitteilt, dass Ihre Bestellung fertig ist und den Kuchen liefert. 

Wir werden nun dieses Abrufmuster zurückverfolgen. Die Bäckerei stellt Ihre benutzerdefinierte API dar, während Sie als Kuchenkäufer die Logic Apps-Engine darstellen. Wenn die Engine eine Anforderung an Ihre API sendet, bestätigt die API die Anforderung und antwortet mit dem Zeitintervall, in dem die Engine den Auftragsstatus abfragen kann. Die Engine prüft immer wieder den Auftragsstatus, bis Ihre API antwortet, dass der Auftrag erledigt ist, und Daten an Ihre Logik-App zurückgibt, die dann den Workflow fortsetzt. 

![Abrufaktionsmuster](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Hier werden die einzelnen Schritte Ihrer API aus API-Sicht beschrieben:

1. Wenn Ihre API eine HTTP-Anforderung zum Starten der Ausführung erhält, wird unverzüglich eine HTTP-`202 ACCEPTED`-Antwort mit dem `location`-Header zurückgegeben, der weiter unten in diesem Schritt beschrieben wird. Diese Antwort teilt der Logic Apps-Engine mit, dass Ihre API die Anforderung erhalten hat, die Anforderungsnutzlast (Dateneingabe) akzeptiert hat und sie nun verarbeitet. 
   
   Die `202 ACCEPTED`-Antwort sollte diese Header enthalten:
   
   * *Erforderlich*: Ein `location`-Header, der den absoluten Pfad zu einer URL angibt, über die die Logic Apps-Engine den Auftragsstatus Ihrer API überprüfen kann.

   * *Optional:* Ein `retry-after`-Header, der die Anzahl der Sekunden angibt, für die die Engine vor der Überprüfung des Auftragsstatus über die `location`-URL warten sollte. 

     Standardmäßig führt die Engine alle 20 Sekunden eine Überprüfung durch. Schließen Sie zum Angeben eines anderen Zeitintervalls den `retry-after`-Header und die Anzahl der Sekunden bis zum nächsten Abruf ein.

2. Wenn die angegebene Zeit verstrichen ist, ruft die Logic Apps-Engine die `location`-URL zum Prüfen des Auftragsstatus auf. Ihre API sollte diese Überprüfungen ausführen und diese Antworten zurückgeben:
   
   * Wenn der Auftrag abgeschlossen ist, wird eine HTTP-`200 OK`-Antwort zusammen mit der Antwortnutzlast (Eingabe für den nächsten Schritt) zurückgegeben.

   * Wenn der Auftrag immer noch verarbeitet wird, wird eine weitere HTTP-`202 ACCEPTED`-Antwort zurückgegeben, aber mit den gleichen Headern wie die ursprüngliche Antwort.

Wenn Ihre API diesem Muster folgt, müssen Sie keine Änderungen in der Definition des Logik-App-Workflows vornehmen, um die Überprüfung des Auftragsstatus fortzusetzen. Wenn die Engine eine HTTP-`202 ACCEPTED`-Antwort und einen gültigen `location`-Header erhält, respektiert die Engine das asynchrone Muster und überprüft die `location`-Header, bis Ihre API eine Nicht-202-Antwort zurückgibt.

> [!TIP]
> Ein Beispiel für asynchrone Muster finden Sie in diesem [asynchronen Controller-Antwortbeispiel in GitHub](https://github.com/logicappsio/LogicAppsAsyncResponseSample).

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Ausführen zeitaufwändiger Aufgaben mit dem Webhookaktionsmuster

Als Alternative können Sie die Webhookmuster für zeitaufwändige Aufgaben und asynchrone Verarbeitung verwenden. Bei diesem Muster hält die Logik-App an und wartet vor dem Fortsetzen des Workflows auf einen "Rückruf" von Ihrer API, der den Abschluss der Verarbeitung anfordert. Dieser Rückruf ist ein HTTP POST, der eine Nachricht an eine URL sendet, wenn ein Ereignis eintritt. 

<a name="bakery-webhook-action"></a> Wenden Sie nun die obige Bäckereianalogie auf das Webhookmuster an, und stellen Sie sich vor, dass Sie in einer Bäckerei anrufen und einen Kuchen nach Ihren Wünschen bestellen. Die Herstellung des Kuchens braucht ihre Zeit, und Sie möchten nicht am Telefon warten, während die Bäckerei den Kuchen backt. Die Bäckerei bestätigt Ihre Bestellung, aber diesmal geben Sie Ihre Telefonnummer an, damit Sie angerufen werden können, wenn der Kuchen fertig ist. Dieses Mal informiert die Bäckerei Sie, wenn Ihre Bestellung ausgeführt ist, und liefert den Kuchen.

Beim Rückverfolgen dieses Webhookmusters stellen wir fest, dass die Bäckerei Ihre benutzerdefinierte API darstellt, während Sie als Kuchenkäufer die Logic Apps-Engine darstellen. Die Engine ruft Ihre API mit einer Anforderung auf und bezieht einen URL-„Rückruf“ ein.
Wenn der Auftrag abgeschlossen ist, benachrichtigt Ihre API die Engine über die URL und gibt Daten an Ihre Logik-App zurück, die dann den Workflow fortsetzt. 

Richten Sie für dieses Muster zwei Endpunkte auf dem Controller ein: `subscribe` und`unsubscribe`.

*  `subscribe`-Endpunkt: Wenn die Ausführung die Aktion Ihrer API im Workflow erreicht, ruft die Logic Apps-Engine den `subscribe`-Endpunkt auf. Dieser Schritt bewirkt, dass die Logik-App eine Rückruf-URL erstellt, die Ihre API speichert, und dann auf den Rückruf von Ihrer API wartet, wenn die Arbeit abgeschlossen ist. Dann sendet Ihre API wieder einen HTTP POST-Rückruf an die URL und übergibt alle zurückgegebenen Inhalte und Header als Eingabe an die Logik-App.

* `unsubscribe`-Endpunkt: Wenn die Logik-App abgebrochen wird, ruft die Logic Apps-Engine den `unsubscribe`-Endpunkt auf. Ihre API kann dann die Registrierung der Rückruf-URL aufheben und alle Prozesse nach Bedarf beenden.

![Webhookaktionsmuster](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

> [!NOTE]
> Der Logic App-Designer unterstützt derzeit keine Ermittlung von Webhookendpunkten über Swagger. Also müssen Sie für dieses Muster eine [**Webhook**aktion](../connectors/connectors-native-webhook.md) hinzufügen und URL, Header und Text für die Anforderung angeben. Siehe auch [Workflow-Aktionen und -Trigger](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action). Um die Rückruf-URL zu übergeben, können Sie in jedem dieser vorherigen Felder nach Bedarf die `@listCallbackUrl()`-Workflowfunktion verwenden.

> [!TIP]
> Ein Beispiel für Webhookmuster finden Sie in diesem [Webhooktriggerbeispiel in GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Triggermuster

Ihre benutzerdefinierte API kann als [*Trigger*](./logic-apps-overview.md#logic-app-concepts) dienen, der einen Logik-App-Workflow startet, wenn neue Daten oder ein Ereignis eine angegebene Bedingung erfüllen. Dieser Trigger kann entweder regelmäßig überprüfen, ob neue Daten oder Ereignisse an Ihrem Dienstendpunkt vorliegen, oder warten und lauschen. Wenn neue Daten oder ein Ereignis die angegebene Bedingung erfüllen, wird der Trigger ausgelöst und startet die Logik-App, die auf diesen Trigger lauscht. Um Logik-Apps auf diese Weise zu starten, kann Ihre API dem [*Abruftrigger*](#polling-triggers)- oder [*Webhooktrigger*](#webhook-triggers)muster folgen. Diese Muster ähneln ihren Äquivalenten für [Abrufaktionen](#async-pattern) und [Webhookaktionen](#webhook-actions). Erfahren Sie auch mehr über [die Nutzung der Messung für Trigger](logic-apps-pricing.md).

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Regelmäßiges Überprüfen auf neue Daten oder Ereignisse mit dem Abruftriggermuster

Ein *Abruftrigger* verhält sich ähnlich wie die zuvor in diesem Thema beschriebene [Abrufaktion](#async-pattern). Die Logic Apps-Engine ruft in regelmäßigen Abständen den Triggerendpunkt auf und überprüft ihn auf neue Daten oder Ereignisse. Wenn die Engine neue Daten oder Ereignisse findet, die die angegebene Bedingung erfüllen, wird der Trigger ausgelöst. Dann erstellt die Engine eine Instanz der Logik-App, die die Daten als Eingabe verarbeitet. 

![Abruftriggermuster](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Jede Abrufanforderung zählt als Aktionsausführung, auch wenn keine Logik-App-Instanz erstellt wird. Um die mehrfache Verarbeitung der gleichen Daten zu verhindern, sollte Ihr Trigger Daten bereinigen, die bereits gelesen und an die Logik-App übergeben wurden.

Hier werden die einzelnen Schritte für einen Abruftrigger aus API-Sicht beschrieben:

| Neue Daten oder Ereignis gefunden?  | API-Antwort | 
| ------------------------- | ------------ |
| Gefunden | Zurückgeben eines HTTP-`200 OK`-Status mit der Antwortnutzlast (Eingabe für den nächsten Schritt). <br/>Diese Antwort erstellt eine Instanz der Logik-App und startet den Workflow. | 
| Nicht gefunden | Zurückgeben eines HTTP-`202 ACCEPTED`-Status mit einem `location`- und einem `retry-after`-Header. <br/>Für Trigger sollte der `location`-Header auch einen `triggerState`-Abfrageparameter enthalten, der in der Regel ein „Zeitstempel“ ist. Ihre API kann mit diesem Bezeichner den Zeitpunkt der letzten Auslösung der Logik-App nachverfolgen. | 
||| 

Um beispielsweise Ihren Dienst in regelmäßigen Abständen auf neue Dateien zu überprüfen, können Sie einen Abruftrigger mit diesem Verhalten erstellen:

| Anforderung enthält `triggerState`? | API-Antwort | 
| -------------------------------- | -------------| 
| Nein | Zurückgeben eines HTTP-`202 ACCEPTED`-Status zuzüglich eines `location`-Headers, wobei `triggerState` auf den aktuellen Zeitpunkt und das `retry-after`-Intervall auf 15 Sekunden festgelegt ist. | 
| Ja | Überprüfen Ihres Diensts auf Dateien, die nach `DateTime` für `triggerState` hinzugefügt wurden. | 
||| 

| Anzahl der gefundenen Dateien | API-Antwort | 
| --------------------- | -------------| 
| Einzelne Datei | Zurückgeben eines HTTP-`200 OK`-Status und der Inhaltsnutzlast, Aktualisieren von `triggerState` auf `DateTime` für die zurückgegebene Datei und Festlegen des `retry-after`-Intervalls auf 15 Sekunden. | 
| Mehrere Dateien | Zurückgeben jeweils einer Datei und eines HTTP-`200 OK`-Status, Aktualisieren von `triggerState` und Festlegen des `retry-after`-Intervalls auf 0 Sekunden. </br>Diese Schritte teilen der Engine mit, dass mehr Daten verfügbar sind, und dass die Engine die Daten sofort über die URL im `location`-Header anfordern sollte. | 
| Keine Dateien | Zurückgeben eines HTTP-`202 ACCEPTED`-Status, keine Änderung von `triggerState` und Festlegen des `retry-after`-Intervalls auf 15 Sekunden. | 
||| 

> [!TIP]
> Ein Beispiel des Abruftriggermusters finden Sie im [Abruftriggercontroller-Beispiel in GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs).

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Warten und Lauschen auf neue Daten oder Ereignisse mit dem Webhooktriggermuster

Ein Webhooktrigger ist ein *Pushtrigger*, der an Ihrem Dienstendpunkt auf neue Daten oder Ereignisse wartet und lauscht. Wenn neue Daten oder Ereignisse die angegebene Bedingung erfüllen, wird der Trigger ausgelöst und erstellt eine Instanz der Logik-App, die dann die Daten als Eingabe verarbeitet.
Webhooktrigger fungieren ähnlich wie die zuvor in diesem Thema beschriebenen [Webhookaktionen](#webhook-actions) und werden mit `subscribe`- und `unsubscribe`-Endpunkten eingerichtet. 

* `subscribe`-Endpunkt: Wenn Sie in der Logik-App einen Webhooktrigger hinzufügen und speichern, ruft die Logic Apps-Engine den `subscribe`-Endpunkt auf. Dieser Schritt bewirkt, dass die Logik-App eine Rückruf-URL erstellt, die Ihre API speichert. Wenn neue Daten oder Ereignisse vorliegen, die die angegebene Bedingung erfüllen, ruft Ihre API mit einem HTTP POST zur URL zurück. Inhaltsnutzlast und Header werden der Logik-App als Eingabe übergeben.

* `unsubscribe`-Endpunkt: Wenn der Webhooktrigger oder die gesamte Logik-App gelöscht wird, ruft die Logic Apps-Engine den `unsubscribe`-Endpunkt auf. Ihre API kann dann die Registrierung der Rückruf-URL aufheben und alle Prozesse nach Bedarf beenden.

![Webhooktriggermuster](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

> [!NOTE]
> Der Logic App-Designer unterstützt derzeit keine Ermittlung von Webhookendpunkten über Swagger. Also müssen Sie für dieses Muster einen [**Webhook**trigger](../connectors/connectors-native-webhook.md) hinzufügen und URL, Header und Text für die Anforderung angeben. Siehe auch [HTTPWebhook-Trigger](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). Um die Rückruf-URL zu übergeben, können Sie in jedem dieser vorherigen Felder nach Bedarf die `@listCallbackUrl()`-Workflowfunktion verwenden.
>
> Um die mehrfache Verarbeitung der gleichen Daten zu verhindern, sollte Ihr Trigger Daten bereinigen, die bereits gelesen und an die Logik-App übergeben wurden.

> [!TIP]
> Ein Beispiel für Webhookmuster finden Sie in diesem [Webhooktriggercontroller-Beispiel in GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

## <a name="secure-calls-to-your-apis-from-logic-apps"></a>Schützen von Aufrufen Ihrer APIs in Logik-Apps

Nachdem Sie Ihre benutzerdefinierten APIs erstellt haben, richten Sie die Authentifizierung für Ihre APIs so ein, dass Sie sie in Logik-Apps sicher aufrufen können. Erfahren Sie mehr zum [Schützen von Aufrufen Ihrer benutzerdefinierten APIs in Logik-Apps](../logic-apps/logic-apps-custom-api-authentication.md).

## <a name="deploy-and-call-your-apis"></a>Bereitstellen und Aufrufen Ihrer APIs

Nachdem Sie die Authentifizierung eingerichtet haben, richten Sie Bereitstellung Ihrer APIs ein. Erfahren Sie mehr zum [Bereitstellen und Aufrufen benutzerdefinierter APIs in Logik-Apps](../logic-apps/logic-apps-custom-api-host-deploy-call.md).

## <a name="publish-custom-apis-to-azure"></a>Veröffentlichen benutzerdefinierter APIs in Azure

Um Ihre benutzerdefinierten APIs für andere Logic Apps-Benutzer in Azure verfügbar zu machen, müssen Sie Sicherheitseinstellungen hinzufügen und sie als Logic App-Connectors registrieren. Weitere Informationen finden Sie unter [Übersicht über benutzerdefinierte Connectors](../logic-apps/custom-connector-overview.md). 

Um Ihre benutzerdefinierten APIs allen Benutzern in Logic Apps, Microsoft Flow und Microsoft PowerApps zur Verfügung zu stellen, müssen Sie Sicherheitseinstellungen hinzufügen, Ihre APIs als Logic App-Connectors registrieren und Ihre Connectors für das [Microsoft Azure Certified-Programm](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/) vorschlagen. 

## <a name="get-support"></a>Support

* Spezifische Hilfe zu benutzerdefinierten APIs finden Sie unter [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com).

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Zur Optimierung von Logic Apps können Sie auf der Website für [Benutzerfeedback zu Logic Apps](https://aka.ms/logicapps-wish) über Ideen abstimmen oder selbst Ideen einreichen. 

## <a name="next-steps"></a>Nächste Schritte

* [Behandeln von Fehlern und Ausnahmen](../logic-apps/logic-apps-exception-handling.md)
* [Aufrufen, Auslösen oder Schachteln von Workflows mit HTTP-Endpunkten in Logik-Apps](../logic-apps/logic-apps-http-endpoint.md)
* [Nutzung der Messung für Aktionen und Trigger](../logic-apps/logic-apps-pricing.md)
