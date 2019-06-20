---
title: Entwickeln und Konfigurieren von Anwendungen mit Azure Functions und SignalR Service
description: Ausführliche Informationen zur Entwicklung und Konfiguration serverloser Echtzeitanwendungen unter Verwendung von Azure Functions und Azure SignalR Service
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: 9b68b9d0bbac984c29759cf4b7b026a559a9d819
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60809004"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Azure Functions-Entwicklung und -Konfiguration mit Azure SignalR Service

Azure Functions-Anwendungen können mithilfe der [Azure SignalR Service-Bindungen](../azure-functions/functions-bindings-signalr-service.md) mit Echtzeitfunktionen versehen werden. Clientanwendungen verwenden in mehreren Sprachen verfügbare Client-SDKs, um eine Verbindung mit Azure SignalR Service herzustellen und Nachrichten in Echtzeit zu empfangen.

In diesem Artikel werden die Konzepte für die Entwicklung und Konfiguration einer Azure Functions-App mit SignalR Service-Integration beschrieben.

## <a name="signalr-service-configuration"></a>SignalR Service-Konfiguration

Azure SignalR Service kann in verschiedenen Modi konfiguriert werden. Bei Verwendung mit Azure Functions muss der Dienst im *serverlosen* Modus konfiguriert werden.

Navigieren Sie im Azure-Portal zu Seite *Einstellungen* Ihrer SignalR Service-Ressource. Legen Sie den *Dienstmodus* auf *Serverlos* fest.

![SignalR Service-Modus](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Azure Functions-Entwicklung

Eine serverlose Echtzeitanwendung, die mit Azure Functions und Azure SignalR Service erstellt wird, benötigt in der Regel zwei Azure-Funktionen:

* Eine Aushandlungsfunktion, die der Client aufruft, um ein gültiges SignalR Service-Zugriffstoken und eine Dienstendpunkt-URL zu erhalten.
* Mindestens eine Funktion, die Nachrichten sendet oder die Gruppenmitgliedschaft verwaltet.

### <a name="negotiate-function"></a>Aushandlungsfunktion

Eine Clientanwendung benötigt ein gültiges Zugriffstoken, um eine Verbindung mit Azure SignalR Service herstellen zu können. Ein Zugriffstoken kann anonym oder für eine bestimmte Benutzer-ID authentifiziert sein. Serverlose SignalR Service-Anwendungen benötigen einen HTTP-Endpunkt namens „negotiate“, um ein Token und andere Verbindungsinformationen (etwa die SignalR Service-Endpunkt-URL) zu erhalten.

Verwenden Sie eine Azure-Funktion mit HTTP-Trigger sowie die Eingabebindung *SignalRConnectionInfo*, um das Verbindungsinformationenobjekt zu generieren. Die Funktion muss über eine HTTP-Route verfügen, die mit `/negotiate` endet.

Weitere Informationen zum Erstellen der Aushandlungsfunktion finden Sie in der [Referenz für die Eingabebindung *SignalRConnectionInfo*](../azure-functions/functions-bindings-signalr-service.md#signalr-connection-info-input-binding).

Informationen zum Erstellen eines authentifizierten Tokens finden Sie unter [Verwenden der App Service-Authentifizierung](#using-app-service-authentication).

### <a name="sending-messages-and-managing-group-membership"></a>Senden von Nachrichten und Verwalten der Gruppenmitgliedschaft

Verwenden Sie die Ausgabebindung *SignalR*, um Nachrichten an Clients zu senden, die mit Azure SignalR Service verbunden sind. Sie können Nachrichten an alle Clients übertragen oder nur an einen Teil der Clients senden, die mit einer bestimmten Benutzer-ID authentifiziert oder einer bestimmten Gruppe hinzugefügt wurden.

Benutzer können einer einzelnen Gruppe oder mehreren Gruppen hinzugefügt werden. Mit der Ausgabebindung *SignalR* können Benutzer auch zu Gruppen hinzugefügt oder aus Gruppen entfernt werden.

Weitere Informationen finden Sie in der [Referenz für die Ausgabebindung *SignalR*](../azure-functions/functions-bindings-signalr-service.md#signalr-output-binding).

### <a name="signalr-hubs"></a>SignalR-Hubs

SignalR verfügt über ein Hub-Konzept. Jede Clientverbindung und jede aus Azure Functions gesendete Nachricht ist auf einen bestimmten Hub ausgerichtet. Mithilfe von Hubs können Sie Ihre Verbindungen und Nachrichten in logische Namespaces aufteilen.

## <a name="client-development"></a>Cliententwicklung

SignalR-Clientanwendungen können das für mehrere Sprachen verfügbare SignalR-Client-SDK nutzen, um problemlos eine Verbindung mit Azure SignalR Service herzustellen und Nachrichten von Azure SignalR Service zu empfangen.

### <a name="configuring-a-client-connection"></a>Konfigurieren einer Clientverbindung

Für die Verbindungsherstellung mit SignalR Service ist eine erfolgreiche Verbindungsaushandlung durch den Client erforderlich. Diese umfasst folgende Schritte:

1. Senden einer Anforderung an den weiter oben besprochenen HTTP-Endpunkt *negotiate*, um gültige Verbindungsinformationen zu erhalten
1. Herstellen der Verbindung mit SignalR Service unter Verwendung der Dienstendpunkt-URL und des Zugriffstokens, die vom Endpunkt *negotiate* bezogen wurden

Die für den Aushandlungs-Handshake erforderliche Logik ist bereits in den SignalR-Client-SDKs enthalten. Übergeben Sie die URL des Aushandlungsendpunkts (ohne das Segment `negotiate`) an `HubConnectionBuilder` des SDKs. Beispiel in JavaScript:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Konventionsgemäß fügt das SDK automatisch `/negotiate` an die URL an und verwendet sie, um mit der Aushandlung zu beginnen.

> [!NOTE]
> Falls Sie das JavaScript/TypeScript SDK in einem Browser verwenden, müssen Sie in Ihrer Funktionen-App die [Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS) aktivieren](#enabling-cors).

Weitere Informationen zur Verwendung des SignalR-Client-SDK finden Sie in der Dokumentation für die verwendete Sprache:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Senden von Nachrichten von einem Client an den Dienst

Das SignalR-SDK ermöglicht Clientanwendungen zwar das Aufrufen von Back-End-Logik in einem SignalR-Hub, diese Funktion wird bei Verwendung von SignalR Service mit Azure Functions jedoch noch nicht unterstützt. Verwenden Sie HTTP-Anforderungen, um Azure Functions aufzurufen.

## <a name="azure-functions-configuration"></a>Azure Functions-Konfiguration

Azure-Funktions-Apps mit Azure SignalR Service-Integration können wie jede typische Azure-Funktions-App mit Techniken wie [Continuous Deployment](../azure-functions/functions-continuous-deployment.md), [ZIP-Bereitstellung](../azure-functions/deployment-zip-push.md) und [Ausführen aus einem Paket](../azure-functions/run-functions-from-deployment-package.md) bereitgestellt werden.

Bei Apps mit SignalR Service-Bindungen müssen allerdings ein paar Besonderheiten berücksichtigt werden. Wenn der Client in einem Browser ausgeführt wird, muss CORS aktiviert werden. Sollte die App eine Authentifizierung erfordern, können Sie den Aushandlungsendpunkt in die App Service-Authentifizierung integrieren.

### <a name="enabling-cors"></a>Aktivieren von CORS

Der JavaScript/TypeScript-Client sendet HTTP-Anforderungen an die Aushandlungsfunktion, um die Verbindungsaushandlung zu initiieren. Wird die Clientanwendung in einer anderen Domäne gehostet als die Azure-Funktions-App, muss für die Funktions-App die Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS) aktiviert werden, da der Browser die Anforderungen andernfalls blockiert.

#### <a name="localhost"></a>Localhost

Wenn Sie die Funktions-App auf Ihrem lokalen Computer ausführen, können Sie *local.settings.json* einen Abschnitt vom Typ `Host` hinzufügen, um CORS zu aktivieren. Fügen Sie im Abschnitt `Host` zwei Eigenschaften hinzu:

* `CORS`: Geben Sie die Basis-URL (also den Ursprung der Clientanwendung) ein.
* `CORSCredentials`: Legen Sie diese Eigenschaft auf `true` fest, um Anforderungen vom Typ „withCredentials“ zu ermöglichen.

Beispiel:

```json
{
  "IsEncrypted": false,
  "Values": {
    // values
  },
  "Host": {
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

#### <a name="azure"></a>Azure

Wenn Sie CORS für eine Azure-Funktions-App aktivieren möchten, navigieren Sie im Azure-Portal unter der Registerkarte *Plattformfeatures* Ihrer Funktions-App zum CORS-Konfigurationsbildschirm.

CORS muss mit „Access-Control-Allow-Credentials“ aktiviert werden, damit der SignalR-Client die Aushandlungsfunktion aufrufen kann. Aktivieren Sie das entsprechende Kontrollkästchen.

Fügen Sie im Abschnitt *Zulässige Ursprünge* einen Eintrag mit der Basis-URL des Ursprungs Ihrer Webanwendung hinzu.

![Konfigurieren von CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

### <a name="using-app-service-authentication"></a>Verwenden der App Service-Authentifizierung

Azure Functions verfügt über eine integrierte Authentifizierung und unterstützt gängige Anbieter wie Facebook, Twitter, Microsoft-Konto, Google und Azure Active Directory. Dieses Feature kann in die Bindung *SignalRConnectionInfo* integriert werden, um Verbindungen mit Azure SignalR Service zu erstellen, die für eine Benutzer-ID authentifiziert wurden. Ihre Anwendung kann unter Verwendung der Ausgabebindung *SignalR* Nachrichten senden, die für diese Benutzer-ID bestimmt sind.

Öffnen Sie im Azure-Portal auf der Registerkarte *Plattformfeatures* Ihrer Funktions-App das Fenster mit den ** Authentifizierungs-/Autorisierungseinstellungen. Gehen Sie gemäß der Dokumentation für die [App Service-Authentifizierung](../app-service/overview-authentication-authorization.md) vor, um die Authentifizierung mit einem Identitätsanbieter Ihrer Wahl zu konfigurieren.

Nach Abschluss der Konfiguration enthalten authentifizierte HTTP-Anforderungen Header vom Typ `x-ms-client-principal-name` und `x-ms-client-principal-id`, die wiederum den Benutzernamen bzw. die Benutzer-ID der authentifizierten Identität enthalten.

Diese Header können in der Konfiguration der Bindung *SignalRConnectionInfo* verwendet werden, um authentifizierte Verbindungen zu erstellen. Das folgende Beispiel zeigt eine C#-Aushandlungsfunktion mit dem Header `x-ms-client-principal-id`:

```csharp
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

Anschließend können Sie Nachrichten an diesen Benutzer senden, indem Sie die Eigenschaft `UserId` einer SignalR-Nachricht festlegen.

```csharp
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

Informationen zu anderen Sprachen finden Sie unter [Bindungen des SignalR-Diensts für Azure Functions](../azure-functions/functions-bindings-signalr-service.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie mithilfe von Azure Functions serverlose SignalR Service-Anwendungen entwickeln und konfigurieren. Versuchen Sie als Nächstes, anhand einer der Schnellstartanleitungen oder eines der Tutorials auf der [Übersichtsseite für SignalR Service](index.yml) selbst eine Anwendung zu erstellen.