---
title: include file
description: include file
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: e7968f4ab01706aa5f8d7d016d93a1b9de2e74b6
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325293"
---
## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/?preserve-view=true&view=azure-java-stable), Version 8 oder höher.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Eine bereitgestellte Communication Services-Ressource und eine Verbindungszeichenfolge. [Erstellen Sie eine Communication Services-Ressource.](../create-communication-resource.md)

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-java-application"></a>Erstellen einer neuen Java-Anwendung

Öffnen Sie Ihr Terminal oder Befehlsfenster. Navigieren Sie zu dem Verzeichnis, in dem Sie Ihre Java-Anwendung erstellen möchten. Führen Sie den unten angegebenen Befehl aus, um das Java-Projekt auf der Grundlage der Vorlage „maven-archetype-quickstart“ zu generieren.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Wie Sie sehen, wurde durch die Aufgabe „generate“ ein Verzeichnis erstellt, das den gleichen Namen besitzt wie die Artefakt-ID (`artifactId`). In diesem Verzeichnis enthält das Verzeichnis „src/main/java“ den Quellcode des Projekts. Das Verzeichnis `src/test/java directory` enthält die Testquelle, und die Datei `pom.xml` ist das Projektobjektmodell (POM) des Projekts.

### <a name="install-the-package"></a>Installieren des Pakets

Öffnen Sie die Datei **pom.xml** in Ihrem Text-Editor. Fügen Sie der Gruppe „dependencies“ das folgende Abhängigkeitselement hinzu.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-administration</artifactId>
    <version>1.0.0-beta.3</version> 
</dependency>
```

### <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks

Über das Projektverzeichnis:

1. Navigieren Sie zum Verzeichnis */src/main/java/com/communication/quickstart*.
1. Öffnen Sie die Datei *App.java* im Editor.
1. Ersetzen Sie die Anweisung `System.out.println("Hello world!");`.
1. Fügen Sie Anweisungen vom Typ `import` hinzu.

Verwenden Sie zum Einstieg den folgenden Code:

```java
import com.azure.communication.administration.*;
import com.azure.communication.common.*;
import java.io.*;
import java.util.*;
import java.time.*;

import com.azure.core.http.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Access Tokens Quickstart");
        // Quickstart code goes here
    }
}
```

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Instanziieren Sie einen Kommunikationsidentitätsclient (`CommunicationIdentityClient`) mit dem Zugriffsschlüssel und dem Endpunkt Ihrer Ressource. Informationen zur Verwaltung der Verbindungszeichenfolge Ihrer Ressource finden Sie [hier](../create-communication-resource.md#store-your-connection-string).

Fügen Sie der `main`-Methode folgenden Code hinzu:

```java
// Your can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessKey = "SECRET";

// Create an HttpClient builder of your choice and customize it
// Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
// -> Add "import com.azure.core.http.netty.*;"
// -> Add azure-core-http-netty dependency to file pom.xml

HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .endpoint(endpoint)
    .accessKey(accessKey)
    .httpClient(httpClient)
    .buildClient();
```

Sie können den Client mit einem beliebigen benutzerdefinierten HTTP-Client initialisieren, der die Schnittstelle `com.azure.core.http.HttpClient` implementiert. Im obigen Code wird die Verwendung des von `azure-core` bereitgestellten [Azure Core-Netty-HTTP-Clients](/java/api/overview/azure/core-http-netty-readme?preserve-view=true&view=azure-java-stable) gezeigt.

Anstelle des Endpunkts und des Zugriffsschlüssels können Sie mithilfe der ConnectionString()-Funktion auch die gesamte Verbindungszeichenfolge bereitstellen. 
```java
// Your can find your connection string from your resource in the Azure Portal
String connectionString = "<connection_string>";
CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .connectionString(connectionString)
    .httpClient(httpClient)
    .buildClient();
```

## <a name="create-an-identity"></a>Erstellen einer Identität

Von Azure Communication Services wird ein einfaches Identitätsverzeichnis gepflegt. Verwenden Sie die Methode `createUser`, um in dem Verzeichnis einen neuen Eintrag mit einer eindeutigen `Id` zu erstellen. Speichern Sie die empfangene Identität mit einer Zuordnung zu den Benutzern Ihrer Anwendung. Beispielsweise, indem Sie sie in der Datenbank des Anwendungsservers speichern. Die Identität ist später erforderlich, um Zugriffstoken auszustellen.

```java
CommunicationUser identity = communicationIdentityClient.createUser();
System.out.println("\nCreated an identity with ID: " + identity.getId());
```

## <a name="issue-access-tokens"></a>Ausstellen von Zugriffstoken

Verwenden Sie die Methode `issueToken`, um ein Zugriffstoken für eine bereits vorhandene Communication Services-Identität auszustellen. Der Parameter `scopes` definiert einen Satz primitiver Elemente, die dieses Zugriffstoken autorisieren. Weitere Informationen finden Sie in der [Liste der unterstützten Aktionen](../../concepts/authentication.md). Eine neue Instanz des Parameters `user` kann basierend auf der Zeichenfolgendarstellung der Azure Communication Service-Identität generiert werden.

```java
// Issue an access token with the "voip" scope for an identity
List<String> scopes = new ArrayList<>(Arrays.asList("voip"));
CommunicationUserToken response = communicationIdentityClient.issueToken(identity, scopes);
OffsetDateTime expiresOn = response.getExpiresOn();
String token = response.getToken();
System.out.println("\nIssued an access token with 'voip' scope that expires at: " + expiresOn + ": " + token);
```

Zugriffstoken sind kurzlebige Anmeldeinformationen, die erneut ausgestellt werden müssen. Wenn dies nicht der Fall ist, kann die Benutzerumgebung Ihrer Anwendung unterbrochen werden. Die Antworteigenschaft `expiresAt` gibt die Lebensdauer des Zugriffstokens an.

## <a name="refresh-access-tokens"></a>Zugriffstoken für die Aktualisierung

Verwenden Sie zum Aktualisieren eines Zugriffstokens das `CommunicationUser`-Objekt für die erneute Ausstellung:

```java  
// Value existingIdentity represents identity of Azure Communication Services stored during identity creation
CommunicationUser identity = new CommunicationUser(existingIdentity);
response = communicationIdentityClient.issueToken(identity, scopes);
```

## <a name="revoke-access-tokens"></a>Widerrufen von Zugriffstoken

In einigen Fällen können Sie Zugriffstoken explizit widerrufen. Beispielsweise dann, wenn der Benutzer einer Anwendung das Kennwort ändert, das für die Authentifizierung beim Dienst verwendet wird. Die Methode `revokeTokens` erklärt alle aktiven Zugriffstoken für ungültig, die für die Identität ausgestellt wurden.

```java  
communicationIdentityClient.revokeTokens(identity, OffsetDateTime.now());
System.out.println("\nSuccessfully revoked all access tokens for identity with ID: " + identity.getId());
```

## <a name="delete-an-identity"></a>Löschen einer Identität

Wird eine Identität gelöscht, werden alle aktiven Zugriffstoken widerrufen, und für die Identität können keine Zugriffstoken mehr ausgestellt werden. Außerdem werden alle gespeicherten Inhalte entfernt, die der Identität zugeordnet sind.

```java
communicationIdentityClient.deleteUser(identity);
System.out.println("\nDeleted the identity with ID: " + identity.getId());
```

## <a name="run-the-code"></a>Ausführen des Codes

Navigieren Sie zum Verzeichnis, das die Datei *pom.xml* enthält, und kompilieren Sie das Projekt mit dem folgenden `mvn`-Befehl.

```console
mvn compile
```

Erstellen Sie dann das Paket.

```console
mvn package
```

Führen Sie die App mit dem folgenden `mvn`-Befehl aus.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```