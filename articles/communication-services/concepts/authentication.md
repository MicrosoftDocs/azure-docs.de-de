---
title: Authentifizieren bei Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Erfahren Sie mehr über die Möglichkeiten, wie sich eine App oder ein Dienst bei Communication Services authentifizieren kann.
author: matthewrobertson
manager: jken
services: azure-communication-services
ms.author: marobert
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 4d6e02852dcd2d30a764417a4b5e0e012a1d2ab5
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571095"
---
# <a name="authenticate-to-azure-communication-services"></a>Authentifizieren bei Azure Communication Services

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Dieser Artikel bietet Informationen zur Authentifizierung von Clients bei Azure Communication Services unter Verwendung von *Zugriffsschlüsseln* und *Benutzerzugriffstoken*. Jede Clientinteraktion mit Azure Communication Services muss authentifiziert werden.

In der folgenden Tabelle werden die Authentifizierungsoptionen beschrieben, die von den Azure Communication Services-Clientbibliotheken unterstützt werden:

| Clientbibliothek | Zugriffsschüssel    | Benutzerzugriffstoken |
| -------------- | ------------- | ------------------ |
| Verwaltung | Unterstützt     | Nicht unterstützt      |
| SMS            | Unterstützt     | Nicht unterstützt      |
| Chat           | Nicht unterstützt | Unterstützt          |
| Aufrufen        | Nicht unterstützt | Unterstützt          |

Im Anschluss werden die einzelnen Autorisierungsoptionen kurz erläutert:

- Die Authentifizierung per **Zugriffsschlüssel** für Vorgänge in „SMS“ oder „Administration“. Die Authentifizierung per Zugriffsschlüssel eignet sich für Anwendungen, die in einer vertrauenswürdigen Dienstumgebung ausgeführt werden. Zum Authentifizieren mit einem Zugriffsschlüssel generiert ein Client einen [HMAC (Hash-based Message Authentication Code)](https://en.wikipedia.org/wiki/HMAC) und schließt diesen in den `Authorization`-Header jeder HTTP-Anforderung ein. Weitere Informationen finden Sie unter [Authentifizieren mit einem Zugriffsschlüssel](#authenticate-with-an-access-key).
- Die Authentifizierung per **Benutzerzugriffstoken** ist für „Chat“ und „Calling“ vorgesehen. Benutzerzugriffstoken ermöglichen die direkte Authentifizierung Ihrer Clientanwendungen bei Azure Communication Services. Diese Token werden über einen von Ihnen erstellten serverseitigen Tokenbereitstellungsdienst generiert. Anschließend werden sie Clientgeräten zur Verfügung gestellt, die das Token zur Initialisierung der Clientbibliotheken „Chat“ und „Calling“ verwenden. Weitere Informationen finden Sie unter [Authentifizieren mit einem Benutzerzugriffstoken](#authenticate-with-a-user-access-token).

## <a name="authenticate-with-an-access-key"></a>Authentifizieren mit einem Zugriffsschlüssel

Die Authentifizierung per Zugriffsschlüssel nutzt einen gemeinsamen geheimen Schlüssel, um einen HMAC für jede HTTP-Anforderung zu generieren, der mit dem SHA256-Algorithmus berechnet wird, und sendet ihn im `Authorization`-Header mithilfe des `HMAC-SHA256`-Schemas.

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

Die Clientbibliotheken von Azure Communication Services, die die Authentifizierung per Zugriffsschlüssel verwenden, müssen mit der Verbindungszeichenfolge Ihrer Ressource initialisiert werden. Wenn Sie keine Clientbibliothek verwenden, können Sie HMACs programmgesteuert unter Verwendung des Zugriffsschlüssels Ihrer Ressource generieren. Weitere Informationen zu Verbindungszeichenfolgen finden Sie im [Schnellstart zur Bereitstellung von Ressourcen](../quickstarts/create-communication-resource.md).

### <a name="sign-an-http-request"></a>Signieren einer HTTP-Anforderung

Wenn Sie keine Clientbibliothek verwenden, um HTTP-Anforderungen an die REST-APIs von Azure Communication Services zu richten, müssen Sie HMACs für jede HTTP-Anforderung programmgesteuert erstellen. In den folgenden Schritten wird beschrieben, wie Sie den Autorisierungsheader erstellen:

1. Geben Sie den Zeitstempel der koordinierten Weltzeit (UTC) für die Anforderung entweder im Header `x-ms-date` oder im HTTP-Standardheader `Date` an. Der Dienst überprüft dies zum Schutz vor bestimmten Angriffen auf die Sicherheit, einschließlich Replay-Angriffen.
1. Hashen Sie den HTTP-Anforderungstext mit dem SHA256-Algorithmus, und übergeben Sie ihn dann mit der Anforderung über den `x-ms-content-sha256`-Header.
1. Erstellen Sie die zu signierende Zeichenfolge durch Verketten des HTTP-Verbs (z. B. `GET` oder `PUT`), des Pfads der HTTP-Anforderung und der Werte der HTTP-Header `Date`, `Host` und `x-ms-content-sha256` im folgenden Format:
    ```
    VERB + "\n"
    URLPathAndQuery + "\n"
    DateHeaderValue + ";" + HostHeaderValue + ";" + ContentHashHeaderValue
    ```
1. Generieren Sie eine HMAC-256-Signatur der UTF-8-codierten Zeichenfolge, die Sie im vorherigen Schritt erstellt haben. Codieren Sie als Nächstes die Ergebnisse als Base64. Beachten Sie, dass Sie Ihren Zugriffsschlüssel auch mit Base64 decodieren müssen. Verwenden Sie folgendes Format (das als Pseudocode gezeigt wird):
    ```
    Signature=Base64(HMAC-SHA256(UTF8(StringToSign), Base64.decode(<your_access_key>)))
    ```
1. Geben Sie den Autorisierungsheader wie folgt an:
    ```
    Authorization="HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"  
    ```
    Dabei ist `<hmac-sha256-signature>` der im vorherigen Schritt berechnete HMAC.

## <a name="authenticate-with-a-user-access-token"></a>Authentifizieren mit einem Benutzerzugriffstoken

Benutzerzugriffstoken ermöglichen die direkte Authentifizierung Ihrer Clientanwendungen bei Azure Communication Services. Hierzu müssen Sie einen vertrauenswürdigen Dienst einrichten, der die Anwendungsbenutzer authentifiziert und mit der Clientbibliothek „Administration“ Benutzerzugriffstoken ausstellt. Informieren Sie sich in der konzeptionellen Dokumentation zur [Client- und Serverarchitektur](./client-and-server-architecture.md), um mehr über unsere Überlegungen zur Architektur zu erfahren.

Die `CommunicationUserCredential`-Klasse enthält die Logik zur Bereitstellung von Anmeldeinformationen mittels Benutzerzugriffstoken für die Clientbibliotheken und zur Verwaltung ihres Lebenszyklus.

### <a name="initialize-the-client-libraries"></a>Initialisieren der Clientbibliotheken

Zur Initialisierung der Clientbibliotheken von Azure Communication Services, die eine Authentifizierung per Benutzerzugriffstoken erfordern, erstellen Sie zunächst eine Instanz der `CommunicationUserCredential`-Klasse und verwenden diese dann zur Initialisierung eines API-Clients.

Die folgenden Ausschnitte zeigen, wie Sie die Clientbibliothek „Chat“ mit einem Benutzerzugriffstoken initialisieren können:

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
// user access tokens should be created by a trusted service using the Administration client library
var token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
var userCredential = new CommunicationUserCredential(token);

// initialize the chat client library with the credential
var chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// user access tokens should be created by a trusted service using the Administration client library
const token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance with the AzureCommunicationUserCredential class
const userCredential = new AzureCommunicationUserCredential(token);

// initialize the chat client library with the credential
let chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
// user access tokens should be created by a trusted service using the Administration client library
let token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
let userCredential = try CommunicationUserCredential(token: token)

// initialize the chat client library with the credential
let chatClient = try CommunicationChatClient(credential: userCredential, endpoint: ENDPOINT_URL)
```

#### <a name="java"></a>[Java](#tab/java)

```java
// user access tokens should be created by a trusted service using the Administration client library
String token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
CommunicationUserCredential userCredential = new CommunicationUserCredential(token);

// Initialize the chat client
final ChatClientBuilder builder = new ChatClientBuilder();
builder.endpoint(ENDPOINT_URL)
    .credential(userCredential)
    .httpClient(HTTP_CLIENT);
ChatClient chatClient = builder.buildClient();
```

---

### <a name="refreshing-user-access-tokens"></a>Aktualisieren von Benutzerzugriffstoken

Benutzerzugriffstoken sind kurzlebige Anmeldeinformationen, die neu ausgestellt werden müssen, um Dienstunterbrechungen für Ihre Benutzer zu vermeiden. Der Konstruktor `CommunicationUserCredential` akzeptiert eine Rückruffunktion zur Aktualisierung, mit der Sie Benutzerzugriffstoken aktualisieren können, ehe sie ablaufen. Sie sollten diesen Rückruf verwenden, um ein neues Benutzerzugriffstoken von Ihrem vertrauenswürdigen Dienst abzurufen.

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var userCredential = new CommunicationUserCredential(
    initialToken: token,
    refreshProactively: true,
    tokenRefresher: cancellationToken => fetchNewTokenForCurrentUser(cancellationToken)
);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const userCredential = new AzureCommunicationUserCredential({
  tokenRefresher: async () => fetchNewTokenForCurrentUser(),
  refreshProactively: true,
  initialToken: token
});
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
 let userCredential = try CommunicationUserCredential(initialToken: token, refreshProactively: true) { |completionHandler|
   let updatedToken = fetchTokenForCurrentUser()
   completionHandler(updatedToken, nil)
 }
```

#### <a name="java"></a>[Java](#tab/java)

```java
TokenRefresher tokenRefresher = new TokenRefresher() {
    @Override
    Future<String> getFetchTokenFuture() {
        return fetchNewTokenForCurrentUser();
    }
}

CommunicationUserCredential credential = new CommunicationUserCredential(tokenRefresher, token, true);
```
---

Mit der Option `refreshProactively` können Sie entscheiden, wie Sie den Tokenlebenszyklus verwalten möchten. Wenn ein Token veraltet ist, blockiert der Rückruf standardmäßig API-Anforderungen und versucht, es zu aktualisieren. Wenn `refreshProactively` auf `true` festgelegt ist, wird der Rückruf geplant und asynchron ausgeführt, ehe das Token abläuft.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen von Benutzerzugriffstoken](../quickstarts/access-tokens.md)

Weitere Informationen finden Sie in den folgenden Artikeln:
- [Erfahren Sie mehr über die Client- und Serverarchitektur](../concepts/client-and-server-architecture.md)
