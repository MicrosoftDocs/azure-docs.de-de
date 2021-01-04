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
ms.openlocfilehash: a76c6467dac69fd3d21aa659c52227046c166938
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94816657"
---
## <a name="prerequisites"></a>Voraussetzungen
Führen Sie die folgenden Schritte aus, bevor Sie beginnen:
- Erstellen Sie ein Azure-Konto mit einem aktiven Abonnement. Details finden Sie auf der [Seite zum Erstellen eines kostenloses Azure-Kontos](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Installieren von [Visual Studio](https://visualstudio.microsoft.com/downloads/) 
- Erstellen Sie eine Azure Communication Services-Ressource. Ausführlichere Informationen hierzu finden Sie unter [Erstellen einer Azure Communication Services-Ressource](../../create-communication-resource.md). Für diese Schnellstartanleitung müssen Sie den **Endpunkt** Ihrer Ressource aufzeichnen.
- Ein [Benutzerzugriffstoken](../../access-tokens.md). Achten Sie darauf, dass Sie den Bereich auf „Chat“ festlegen, und notieren Sie sich die Tokenzeichenfolge und die userId-Zeichenfolge.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-c-application"></a>Erstellen einer neuen C#-Anwendung

Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen `ChatQuickstart`. Dieser Befehl erstellt ein einfaches „Hallo Welt“-C#-Projekt mit einer einzigen Quelldatei: **Program.cs**.

```console
dotnet new console -o ChatQuickstart
```

Wechseln Sie zum neu erstellten App-Ordner, und verwenden Sie den Befehl `dotnet build`, um Ihre Anwendung zu kompilieren.

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installieren des Pakets

Installieren der .NET-Clientbibliothek für Chats von Azure Communication Services

```PowerShell
dotnet add package Azure.Communication.Chat --version 1.0.0-beta.3
``` 

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen werden für einige der wichtigsten Features der C#-Clientbibliothek für Chats von Azure Communication Services verwendet.

| Name                                  | Beschreibung                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Diese Klasse wird für die Chatfunktionalität benötigt. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie zum Erstellen, Abrufen und Löschen von Threads. |
| ChatThreadClient | Diese Klasse wird für die Chatthreadfunktionalität benötigt. Sie rufen eine Instanz über den ChatClient ab und verwenden sie zum Senden/Empfangen/Aktualisieren/Löschen von Nachrichten, Hinzufügen/Entfernen/Abrufen von Benutzern und Senden von Eingabebenachrichtigungen und Lesebestätigungen. |

## <a name="create-a-chat-client"></a>Erstellen eines Chatclients

Zum Erstellen eines Chatclients verwenden Sie Ihren Communication Services-Endpunkt und das Zugriffstoken, das im Rahmen der Schritte zur Erfüllung der Voraussetzungen generiert wurde. Sie müssen die `CommunicationIdentityClient`-Klasse über die Clientbibliothek `Administration` verwenden, um einen Benutzer zu erstellen und ein Token auszustellen, das Sie an Ihren Chatclient übergeben können. Informieren Sie sich über [Benutzerzugriffstoken](../../access-tokens.md).

```csharp
using Azure.Communication.Identity;
using Azure.Communication.Chat;

// Your unique Azure Communication service endpoint
Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(<Access_Token>);
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential);
```

## <a name="start-a-chat-thread"></a>Starten eines Chatthreads

Verwenden Sie die `createChatThread`-Methode, um einen Chatthread zu erstellen.
- Verwenden Sie `topic`, um für diesen Chat ein Thema anzugeben. Nach der Erstellung des Chatthreads kann das Thema mit der Funktion `UpdateThread` aktualisiert werden.
- Verwenden Sie die `members`-Eigenschaft zum Übergeben einer Liste mit `ChatThreadMember`-Objekten, die dem Chatthread hinzugefügt werden sollen. Das `ChatThreadMember`-Objekt wird mit einem `CommunicationUser`-Objekt initialisiert. Zum Abrufen eines `CommunicationUser`-Objekts müssen Sie eine Zugriffs-ID übergeben, die Sie gemäß der Anleitung zum [Erstellen eines Benutzers](../../access-tokens.md#create-an-identity) erstellt haben.

Die Antwort `chatThreadClient` wird verwendet, um Vorgänge für den erstellten Chatthread durchzuführen: Hinzufügen von Mitgliedern zum Chatthread, Senden einer Nachricht, Löschen einer Nachricht usw. Sie enthält das Attribut `Id`, bei dem es sich um die eindeutige ID des Chatthreads handelt. 

```csharp
var chatThreadMember = new ChatThreadMember(new CommunicationUser("<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync(topic: "Chat Thread topic C# sdk", members: new[] { chatThreadMember });
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>Abrufen eines Clients für den Chatthread
Die `GetChatThreadClient`-Methode gibt einen Threadclient für einen bereits vorhandenen Thread zurück. Sie kann zum Durchführen von Vorgängen im erstellten Thread verwendet werden: Hinzufügen von Mitgliedern, Senden einer Nachricht usw. „threadId“ ist die eindeutige ID des vorhandenen Chatthreads.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Senden einer Nachricht an einen Chatthread

Verwenden Sie die `SendMessage`-Methode, um eine Nachricht an einen Thread zu senden, der mit der „threadId“ identifiziert wird.

- Verwenden Sie `content`, um den Inhalt der Chatnachricht anzugeben. Dies ist erforderlich.
- Verwenden Sie `priority`, um die Prioritätsstufe der Nachricht anzugeben, z. B. „Normal“ oder „High“ (Hoch). Wenn Sie nichts angeben, wird „Normal“ verwendet.
- Verwenden Sie `senderDisplayName`, um den Anzeigenamen des Absenders anzugeben. Wenn Sie nichts angeben, bleibt der Name leer.

`SendChatMessageResult` ist die Antwort, die nach dem Senden einer Nachricht zurückgegeben wird. Sie enthält eine ID, bei der es sich um die eindeutige ID der Nachricht handelt.

```csharp
var content = "hello world";
var priority = ChatMessagePriority.Normal;
var senderDisplayName = "sender name";

SendChatMessageResult sendChatMessageResult = await chatThreadClient.SendMessageAsync(content, priority, senderDisplayName);
string messageId = sendChatMessageResult.Id;
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Empfangen von Chatnachrichten aus einem Chatthread

Sie können Chatnachrichten abrufen, indem Sie die `GetMessages`-Methode auf dem Chatthreadclient jeweils nach dem angegebenen Intervallzeitraum abfragen.

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Sender.Id}:{message.Content}");
}
```

Für `GetMessages` wird der optionale Parameter `DateTimeOffset` verwendet. Bei Angabe dieses Offsetwerts erhalten Sie Nachrichten, die nach diesem Datum empfangen, aktualisiert oder gelöscht wurden. Beachten Sie hierbei Folgendes: Es werden auch Nachrichten zurückgegeben, die vor dem Offsetdatum empfangen, aber erst danach bearbeitet oder entfernt wurden.

`GetMessages` gibt die aktuelle Version der Nachricht zurück, einschließlich aller Bearbeitungen oder Löschungen, die für die Nachricht mit `UpdateMessage` und `DeleteMessage` durchgeführt wurden. Für gelöschte Nachrichten wird von `chatMessage.DeletedOn` ein datetime-Wert zurückgegeben, mit dem der Löschzeitpunkt der Nachricht angegeben wird. Für bearbeitete Nachrichten gibt `chatMessage.EditedOn` einen datetime-Wert zurück, mit dem der Bearbeitungszeitpunkt der Nachricht angegeben wird. Auf den ursprünglichen Zeitpunkt der Nachrichtenerstellung kann mit `chatMessage.CreatedOn` zugegriffen werden. Dieses Element kann zum Sortieren der Nachrichten verwendet werden.

Mit `GetMessages` werden unterschiedliche Nachrichtentypen zurückgegeben, die mit `chatMessage.Type` identifiziert werden können. Diese Typen lauten:

- `Text`: Reguläre Chatnachricht, die von einem Threadmitglied gesendet wurde.

- `ThreadActivity/TopicUpdate`: Systemnachricht mit dem Hinweis, dass das Thema aktualisiert wurde.

- `ThreadActivity/AddMember`: Systemnachricht mit dem Hinweis, dass dem Chatthread mindestens ein Mitglied hinzugefügt wurde.

- `ThreadActivity/DeleteMember`: Systemnachricht mit dem Hinweis, dass ein Mitglied aus dem Chatthread entfernt wurde.

Weitere Details finden Sie unter [Nachrichtentypen](../../../concepts/chat/concepts.md#message-types).

## <a name="update-a-message"></a>Aktualisieren einer Nachricht

Sie können eine bereits gesendete Meldung aktualisieren, indem Sie `UpdateMessage` für `ChatThreadClient` aufrufen.

```csharp
string id = "id-of-message-to-edit";
string content = "updated content";
await chatThreadClient.UpdateMessageAsync(id, content);
```

## <a name="deleting-a-message"></a>Löschen einer Nachricht

Sie können eine Nachricht löschen, indem Sie `DeleteMessage` für `ChatThreadClient` aufrufen.

```csharp
string id = "id-of-message-to-delete";
await chatThreadClient.DeleteMessageAsync(id);
```

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Hinzufügen eines Benutzers als Mitglied des Chatthreads

Nach der Erstellung eines Threads können Sie dafür Benutzer hinzufügen und entfernen. Indem Sie Benutzer hinzufügen, gewähren Sie ihnen Zugriff zum Senden von Nachrichten an den Thread und zum Hinzufügen/Entfernen anderer Mitglieder. Stellen Sie vor dem Aufrufen von `AddMembers` sicher, dass Sie ein neues Zugriffstoken und eine Identität für den Benutzer beschafft haben. Der Benutzer benötigt dieses Zugriffstoken, um den Chatclient initialisieren zu können.

Verwenden Sie die `AddMembers`-Methode zum Hinzufügen von Mitgliedern zum Thread, der mit der „threadId“ identifiziert wird.

 - Verwenden Sie `members`, um die Mitglieder aufzulisten, die dem Chatthread hinzugefügt werden sollen.
 - `User` (erforderlich) ist die Identität, die Sie für diesen neuen Benutzer erhalten.
 - `DisplayName` (optional) ist der Anzeigename für das Threadmitglied.
 - `ShareHistoryTime` (optional) ist der Zeitpunkt, ab dem der Chatverlauf für das Mitglied freigegeben wird. Verwenden Sie „DateTime.MinValue“, um den Verlauf seit Beginn des Chatthreads freizugeben. Geben Sie den aktuellen Zeitpunkt an, um den Verlauf vor der Hinzufügung des Mitglieds nicht freizugeben. Geben Sie einen Zeitpunkt an, der zwischen der Threaderstellung und dem aktuellen Zeitpunkt liegt, um einen Teil des Verlaufs freizugeben.

```csharp
ChatThreadMember member = new ChatThreadMember(communicationUser);
member.DisplayName = "display name member 1";
member.ShareHistoryTime = DateTime.MinValue; // share all history
await chatThreadClient.AddMembersAsync(members: new[] {member});
```
## <a name="remove-user-from-a-chat-thread"></a>Entfernen eines Benutzers aus einem Chatthread

Sie können Benutzer einem Thread nicht nur hinzufügen, sondern diese auch aus einem Chatthread entfernen. Zu diesem Zweck müssen Sie die Identität (CommunicationUser) der von Ihnen hinzugefügten Mitglieder nachverfolgen.

```csharp
await chatThreadClient.RemoveMemberAsync(communicationUser);
```

## <a name="run-the-code"></a>Ausführen des Codes

Führen Sie die Anwendung mit dem Befehl `dotnet run` aus dem Anwendungsverzeichnis aus.

```console
dotnet run
```
