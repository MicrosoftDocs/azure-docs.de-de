---
title: include file
description: include file
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: edf48bc75817b3510264d852eb9cc717ed022f33
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94915513"
---
## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/?preserve-view=true&view=azure-java-stable), Version 8 oder höher.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Eine bereitgestellte Communication Services-Ressource und eine Verbindungszeichenfolge. [Erstellen Sie eine Communication Services-Ressource](../../create-communication-resource.md).
- Ein [Benutzerzugriffstoken](../../access-tokens.md). Achten Sie darauf, dass Sie den Bereich auf „Chat“ festlegen, und notieren Sie sich die Tokenzeichenfolge und die userId-Zeichenfolge.


## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-java-application"></a>Erstellen einer neuen Java-Anwendung

Öffnen Sie Ihr Terminal- oder Befehlsfenster, und navigieren Sie zu dem Verzeichnis, in dem Sie Ihre Java-Anwendung erstellen möchten. Führen Sie den unten angegebenen Befehl aus, um das Java-Projekt aus der Vorlage „maven-archetype-quickstart“ zu generieren.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Sie sehen, dass über das Ziel „generate“ ein Verzeichnis mit dem gleichen Namen wie für die „artifactId“ erstellt wurde. In diesem Verzeichnis enthält `src/main/java directory` den Quellcode des Projekts, das Verzeichnis `src/test/java` enthält die Testquelle, und die Datei „pom.xml“ ist das Projektobjektmodell ([POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) des Projekts.

Aktualisieren Sie die POM-Datei Ihrer Anwendung für die Verwendung von Java 8 oder höher:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

### <a name="add-the-package-references-for-the-chat-client-library"></a>Hinzufügen der Paketverweise für die Chatclientbibliothek

Verweisen Sie in Ihrer POM-Datei auf das Paket `azure-communication-chat` mit den Chat-APIs:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version>1.0.0-beta.3</version> 
</dependency>
```

Für die Authentifizierung muss Ihr Client auf das Paket `azure-communication-common` verweisen:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version>1.0.0-beta.3</version> 
</dependency>

```

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen werden für einige der wichtigsten Features der Java-Clientbibliothek für Chats von Azure Communication Services verwendet.

| Name                                  | Beschreibung                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Diese Klasse wird für die Chatfunktionalität benötigt. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie zum Erstellen, Abrufen und Löschen von Threads. |
| ChatAsyncClient | Diese Klasse wird für die asynchrone Chatfunktionalität benötigt. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie zum Erstellen, Abrufen und Löschen von Threads. |
| ChatThreadClient | Diese Klasse wird für die Chatthreadfunktionalität benötigt. Sie rufen eine Instanz über den ChatClient ab und verwenden sie zum Senden/Empfangen/Aktualisieren/Löschen von Nachrichten, Hinzufügen/Entfernen/Abrufen von Benutzern und Senden von Eingabebenachrichtigungen und Lesebestätigungen. |
| ChatThreadAsyncClient | Diese Klasse wird für die asynchrone Chatthreadfunktionalität benötigt. Sie rufen eine Instanz über den ChatAsyncClient ab und verwenden sie zum Senden/Empfangen/Aktualisieren/Löschen von Nachrichten, Hinzufügen/Entfernen/Abrufen von Benutzern und Senden von Eingabebenachrichtigungen und Lesebestätigungen. |

## <a name="create-a-chat-client"></a>Erstellen eines Chatclients
Zum Erstellen eines Chatclients verwenden Sie den Communication Services-Endpunkt und das Zugriffstoken, das im Rahmen der Schritte zur Erfüllung der Voraussetzungen generiert wurde. Mit Benutzerzugriffstoken können Sie Clientanwendungen erstellen, die gegenüber Azure Communication Services direkt authentifiziert werden. Nachdem Sie diese Token auf Ihrem Server generiert haben, übergeben Sie sie zurück an ein Clientgerät. Sie müssen die „CommunicationUserCredential“-Klasse aus der allgemeinen Clientbibliothek verwenden, um das Token an Ihren Chatclient zu übergeben. 

Achten Sie beim Hinzufügen der Importanweisungen darauf, nur Importe aus den Namespaces „com.azure.communication.chat“ und „com.azure.communication.chat.models“ hinzuzufügen (nicht aus dem Namespace „com.azure.communication.chat.implementation“). In der Datei „App.java“, die über Maven generiert wurde, können Sie den folgenden Code verwenden, um zu beginnen:

```Java
import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.HttpClient;

import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");
        
        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // Create an HttpClient builder of your choice and customize it
        // Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
        NettyAsyncHttpClientBuilder yourHttpClientBuilder = new NettyAsyncHttpClientBuilder();
        HttpClient httpClient = yourHttpClientBuilder.build();

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationUserCredential with the given access token, which is only valid until the token is valid
        CommunicationUserCredential userCredential = new CommunicationUserCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential)
            .httpClient(httpClient);
        ChatClient chatClient = builder.buildClient();
    }
}
```


## <a name="start-a-chat-thread"></a>Starten eines Chatthreads

Verwenden Sie die `createChatThread`-Methode, um einen Chatthread zu erstellen.
`createChatThreadOptions` wird zum Beschreiben der Threadanforderung verwendet.

- Verwenden Sie `topic`, um für diesen Chat ein Thema anzugeben. Nach der Erstellung des Chatthreads kann das Thema mit der Funktion `UpdateThread` aktualisiert werden.
- Verwenden Sie `members`, um die Threadmitglieder aufzulisten, die dem Thread hinzugefügt werden sollen. Für `ChatThreadMember` wird der Benutzer verwendet, den Sie in der Schnellstartanleitung [Benutzerzugriffstoken](../../access-tokens.md) erstellt haben.

Die Antwort `chatThreadClient` wird verwendet, um Vorgänge für den erstellten Chatthread durchzuführen: Hinzufügen von Mitgliedern zum Chatthread, Senden einer Nachricht, Löschen einer Nachricht usw. Sie enthält die `chatThreadId`-Eigenschaft, bei der es sich um die eindeutige ID des Chatthreads handelt. Auf die Eigenschaft kann über die öffentliche „.getChatThreadId()“-Methode zugegriffen werden.

```Java
List<ChatThreadMember> members = new ArrayList<ChatThreadMember>();

ChatThreadMember firstThreadMember = new ChatThreadMember()
    .setUser(firstUser)
    .setDisplayName("Member Display Name 1");
    
ChatThreadMember secondThreadMember = new ChatThreadMember()
    .setUser(secondUser)
    .setDisplayName("Member Display Name 2");

members.add(firstThreadMember);
members.add(secondThreadMember);

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic("Topic")
    .setMembers(members);
ChatThreadClient chatThreadClient = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = chatThreadClient.getChatThreadId();
```

## <a name="send-a-message-to-a-chat-thread"></a>Senden einer Nachricht an einen Chatthread

Verwenden Sie die `sendMessage`-Methode zum Senden einer Nachricht an den Thread, den Sie gerade erstellt haben und der anhand der „chatThreadId“ identifiziert wird.
`sendChatMessageOptions` wird verwendet, um die Anforderung der Chatnachricht zu beschreiben.

- Verwenden Sie `content`, um den Inhalt der Chatnachricht anzugeben.
- Verwenden Sie `priority`, um die Prioritätsstufe für die Chatnachricht anzugeben, z. B. „Normal“ oder „High“ (Hoch). Diese Eigenschaft kann verwendet werden, um für den empfangenden Benutzer in Ihrer App einen Hinweis in der Benutzeroberfläche anzuzeigen, auf die Nachricht aufmerksam zu machen oder benutzerdefinierte Geschäftslogik auszuführen.
- Verwenden Sie `senderDisplayName`, um den Anzeigenamen des Absenders anzugeben.

Die Antwort `sendChatMessageResult` enthält eine `id`, bei der es sich um die eindeutige ID der Nachricht handelt.

```Java
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setPriority(ChatMessagePriority.NORMAL)
    .setSenderDisplayName("Sender Display Name");

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```


## <a name="get-a-chat-thread-client"></a>Abrufen eines Clients für den Chatthread

Die `getChatThreadClient`-Methode gibt einen Threadclient für einen bereits vorhandenen Thread zurück. Sie kann zum Durchführen von Vorgängen im erstellten Thread verwendet werden: Hinzufügen von Mitgliedern, Senden einer Nachricht usw. `chatThreadId` ist die eindeutige ID des vorhandenen Chatthreads.

```Java
String chatThreadId = "Id";
ChatThread chatThread = chatClient.getChatThread(chatThreadId);
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Empfangen von Chatnachrichten aus einem Chatthread

Sie können Chatnachrichten abrufen, indem Sie die `listMessages`-Methode auf dem Chatthreadclient jeweils nach dem angegebenen Intervallzeitraum abfragen.

```Java
chatThreadClient.listMessages().iterableByPage().forEach(resp -> {
    System.out.printf("Response headers are %s. Url %s  and status code %d %n", resp.getHeaders(),
        resp.getRequest().getUrl(), resp.getStatusCode());
    resp.getItems().forEach(message -> {
        System.out.printf("Message id is %s.", message.getId());
    });
});
```

`listMessages` gibt die aktuelle Version der Nachricht zurück, einschließlich aller Bearbeitungen oder Löschungen, die für die Nachricht mit „.editMessage()“ und „.deleteMessage()“ durchgeführt wurden. Für gelöschte Nachrichten wird von `chatMessage.getDeletedOn()` ein datetime-Wert zurückgegeben, mit dem der Löschzeitpunkt der Nachricht angegeben wird. Für bearbeitete Nachrichten gibt `chatMessage.getEditedOn()` einen datetime-Wert zurück, mit dem der Bearbeitungszeitpunkt der Nachricht angegeben wird. Auf den ursprünglichen Zeitpunkt der Nachrichtenerstellung kann mit `chatMessage.getCreatedOn()` zugegriffen werden. Dieses Element kann zum Sortieren der Nachrichten genutzt werden.

Mit `listMessages` werden unterschiedliche Nachrichtentypen zurückgegeben, die mit `chatMessage.getType()` identifiziert werden können. Diese Typen lauten:

- `Text`: Reguläre Chatnachricht, die von einem Threadmitglied gesendet wurde.

- `ThreadActivity/TopicUpdate`: Systemnachricht mit dem Hinweis, dass das Thema aktualisiert wurde.

- `ThreadActivity/AddMember`: Systemnachricht mit dem Hinweis, dass dem Chatthread mindestens ein Mitglied hinzugefügt wurde.

- `ThreadActivity/DeleteMember`: Systemnachricht mit dem Hinweis, dass ein Mitglied aus dem Chatthread entfernt wurde.

Weitere Details finden Sie unter [Nachrichtentypen](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Hinzufügen eines Benutzers als Mitglied des Chatthreads

Nach der Erstellung eines Chatthreads können Sie dafür Benutzer hinzufügen und entfernen. Indem Sie Benutzer hinzufügen, gewähren Sie ihnen Zugriff zum Senden von Nachrichten an den Chatthread und zum Hinzufügen/Entfernen anderer Mitglieder. Zunächst müssen Sie ein neues Zugriffstoken und eine Identität für den Benutzer abrufen. Stellen Sie vor dem Aufrufen der „addMembers“-Methode sicher, dass Sie ein neues Zugriffstoken und eine Identität für den Benutzer beschafft haben. Der Benutzer benötigt dieses Zugriffstoken, um den Chatclient initialisieren zu können.

Verwenden Sie die `addMembers`-Methode, um dem Thread, der mit der „threadId“ identifiziert wird, Threadmitglieder hinzuzufügen.

- Verwenden Sie `members`, um die Mitglieder aufzulisten, die dem Chatthread hinzugefügt werden sollen.
- `user` (erforderlich) ist der „CommunicationUser“, den Sie mit „CommunicationIdentityClient“ in der Schnellstartanleitung [Benutzerzugriffstoken](../../access-tokens.md) erstellt haben.
- `display_name` (optional) ist der Anzeigename für das Threadmitglied.
- `share_history_time` (optional) ist der Zeitpunkt, ab dem der Chatverlauf für das Mitglied freigegeben wird. Sie können den Verlauf seit dem Beginn des Chatthreads freigeben, indem Sie diese Eigenschaft auf das Datum der Threaderstellung (oder früher) festlegen. Geben Sie das aktuelle Datum an, um den Verlauf vor dem Hinzufügezeitpunkt des Mitglieds nicht freizugeben. Geben Sie ein gewünschtes Datum an, um nur einen Teil des Verlaufs freizugeben.

```Java
List<ChatThreadMember> members = new ArrayList<ChatThreadMember>();

ChatThreadMember firstThreadMember = new ChatThreadMember()
    .setUser(user1)
    .setDisplayName("Display Name 1");

ChatThreadMember secondThreadMember = new ChatThreadMember()
    .setUser(user2)
    .setDisplayName("Display Name 2");

members.add(firstThreadMember);
members.add(secondThreadMember);

AddChatThreadMembersOptions addChatThreadMembersOptions = new AddChatThreadMembersOptions()
    .setMembers(members);
chatThreadClient.addMembers(addChatThreadMembersOptions);
```

## <a name="remove-user-from-a-chat-thread"></a>Entfernen eines Benutzers aus einem Chatthread

Sie können Benutzer einem Thread nicht nur hinzufügen, sondern diese auch aus einem Chatthread entfernen. Hierfür müssen Sie die Benutzeridentitäten der von Ihnen hinzugefügten Mitglieder nachverfolgen.

Verwenden Sie `removeMember`. Hierbei ist `user` der von Ihnen erstellte „CommunicationUser“.

```Java
chatThreadClient.removeMember(user);
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