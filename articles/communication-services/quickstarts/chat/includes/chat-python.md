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
ms.openlocfilehash: 2b7d00335253772683b867acf0765b77fc493e79
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523972"
---
## <a name="prerequisites"></a>Voraussetzungen
Führen Sie die folgenden Schritte aus, bevor Sie beginnen:

- Erstellen Sie ein Azure-Konto mit einem aktiven Abonnement. Details finden Sie auf der [Seite zum Erstellen eines kostenloses Azure-Kontos](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- [Installieren Sie Python.](https://www.python.org/downloads/)
- Erstellen Sie eine Azure Communication Services-Ressource. Ausführlichere Informationen hierzu finden Sie unter [Erstellen einer Azure Communication Services-Ressource](../../create-communication-resource.md). Für diese Schnellstartanleitung müssen Sie den **Endpunkt** Ihrer Ressource aufzeichnen.
- Ein [Benutzerzugriffstoken](../../access-tokens.md). Achten Sie darauf, dass Sie den Bereich auf „Chat“ festlegen, und notieren Sie sich die Tokenzeichenfolge und die userId-Zeichenfolge.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

Öffnen Sie Ihr Terminal- bzw. Befehlsfenster, erstellen Sie ein neues Verzeichnis für Ihre App, und greifen Sie auf das Verzeichnis zu.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Verwenden Sie einen Text-Editor zum Erstellen einer Datei mit dem Namen **start-chat.py** im Stammverzeichnis des Projekts, und fügen Sie die Struktur für das Programm hinzu, z. B. eine einfache Ausnahmebehandlung. In den folgenden Abschnitten fügen Sie der Datei den gesamten Quellcode für diese Schnellstartanleitung hinzu.

```python
import os
# Add required client library components from quickstart here

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-client-library"></a>Installieren der Clientbibliothek

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Objektmodell

Die folgenden Klassen und Schnittstellen werden für einige der wichtigsten Features der Python-Clientbibliothek für Chats von Azure Communication Services verwendet.

| Name                                  | Beschreibung                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Diese Klasse wird für die Chatfunktionalität benötigt. Sie instanziieren sie mit Ihren Abonnementinformationen und verwenden sie zum Erstellen, Abrufen und Löschen von Threads. |
| ChatThreadClient | Diese Klasse wird für die Chatthreadfunktionalität benötigt. Sie rufen eine Instanz über den ChatClient ab und verwenden sie zum Senden/Empfangen/Aktualisieren/Löschen von Nachrichten, Hinzufügen/Entfernen/Abrufen von Benutzern und Senden von Eingabebenachrichtigungen und Lesebestätigungen. |

## <a name="create-a-chat-client"></a>Erstellen eines Chatclients

Zum Erstellen eines Chatclients verwenden Sie den Communication Services-Endpunkt und das `Access Token` (Zugriffstoken), das im Rahmen der Schritte zur Erfüllung der Voraussetzungen generiert wurde. Informieren Sie sich über [Benutzerzugriffstoken](../../access-tokens.md).

```console
pip install azure-communication-administration
```

```python
from azure.communication.chat import ChatClient, CommunicationUserCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationUserCredential(<Access Token>))
```

## <a name="start-a-chat-thread"></a>Starten eines Chatthreads

Verwenden Sie die `create_chat_thread`-Methode, um einen Chatthread zu erstellen.

- Verwenden Sie `topic`, um ein Threadthema anzugeben. Nach der Erstellung des Chatthreads kann das Thema mit der Funktion `update_thread` aktualisiert werden.
- Verwenden Sie `members`, um das `ChatThreadMember`-Element aufzulisten, das dem Chatthread hinzugefügt werden soll. Für `ChatThreadMember` wird für `CommunicationUser` der Typ `user` verwendet. Dies war das Ergebnis, das Sie unter [Erstellen eines Benutzers](../../access-tokens.md#create-an-identity) erhalten haben.

Die Antwort `chat_thread_client` wird verwendet, um Vorgänge für den neu erstellten Chatthread durchzuführen, z. B. das Hinzufügen von Mitgliedern zum Chatthread, Senden einer Nachricht, Löschen einer Nachricht usw. Sie enthält die `thread_id`-Eigenschaft, bei der es sich um die eindeutige ID des Chatthreads handelt.

```python
from datetime import datetime
from azure.communication.chat import ChatThreadMember

topic="test topic"
thread_members=[ChatThreadMember(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]
chat_thread_client = chat_client.create_chat_thread(topic, thread_members)
```

## <a name="get-a-chat-thread-client"></a>Abrufen eines Clients für den Chatthread
Die „get_chat_thread_client“-Methode gibt einen Threadclient für einen Thread zurück, der bereits vorhanden ist. Sie kann zum Durchführen von Vorgängen im erstellten Thread verwendet werden: Hinzufügen von Mitgliedern, Senden einer Nachricht usw. „thread_id“ ist die eindeutige ID des vorhandenen Chatthreads.

```python
thread_id = 'id'
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>Senden einer Nachricht an einen Chatthread

Verwenden Sie die `send_message`-Methode zum Senden einer Nachricht an einen Chatthread, den Sie gerade erstellt haben und der anhand der „threadId“ identifiziert wird.

- Verwenden Sie `content`, um den Inhalt der Chatnachricht anzugeben.
- Verwenden Sie `priority`, um die Prioritätsstufe der Nachricht anzugeben, z. B. „Normal“ oder „High“ (Hoch). Diese Eigenschaft kann verwendet werden, um für den empfangenden Benutzer in Ihrer App einen Hinweis in der Benutzeroberfläche anzuzeigen, auf die Nachricht aufmerksam zu machen oder benutzerdefinierte Geschäftslogik auszuführen.
- Verwenden Sie `senderDisplayName`, um den Anzeigenamen des Absenders anzugeben.

Die Antwort `SendChatMessageResult` enthält eine „ID“, bei der es sich um die eindeutige ID der Nachricht handelt.

```python
from azure.communication.chat import ChatMessagePriority

content='hello world'
priority=ChatMessagePriority.NORMAL
sender_display_name='sender name'

send_message_result = chat_thread_client.send_message(content, priority=priority, sender_display_name=sender_display_name)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Empfangen von Chatnachrichten aus einem Chatthread

Sie können Chatnachrichten abrufen, indem Sie die `list_messages`-Methode jeweils nach dem angegebenen Intervallzeitraum abfragen.

```python
chat_messages = chat_thread_client.list_messages()
```
`list_messages` gibt die aktuelle Version der Nachricht zurück, einschließlich aller Bearbeitungen oder Löschungen, die für die Nachricht mit `update_message` und `delete_message` durchgeführt wurden. Für gelöschte Nachrichten wird von `ChatMessage.deleted_on` ein datetime-Wert zurückgegeben, mit dem der Löschzeitpunkt der Nachricht angegeben wird. Für bearbeitete Nachrichten gibt `ChatMessage.edited_on` einen datetime-Wert zurück, mit dem der Bearbeitungszeitpunkt der Nachricht angegeben wird. Auf den ursprünglichen Zeitpunkt der Nachrichtenerstellung kann mit `ChatMessage.created_on` zugegriffen werden. Dieses Element kann zum Sortieren der Nachrichten genutzt werden.

Mit `list_messages` werden unterschiedliche Nachrichtentypen zurückgegeben, die mit `ChatMessage.type` identifiziert werden können. Diese Typen lauten:

- `Text`: Reguläre Chatnachricht, die von einem Threadmitglied gesendet wurde.

- `ThreadActivity/TopicUpdate`: Systemnachricht mit dem Hinweis, dass das Thema aktualisiert wurde.

- `ThreadActivity/AddMember`: Systemnachricht mit dem Hinweis, dass dem Chatthread mindestens ein Mitglied hinzugefügt wurde.

- `ThreadActivity/DeleteMember`: Systemnachricht mit dem Hinweis, dass ein Mitglied aus dem Chatthread entfernt wurde.

Weitere Details finden Sie unter [Nachrichtentypen](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Hinzufügen eines Benutzers als Mitglied des Chatthreads

Nach der Erstellung eines Chatthreads können Sie dafür Benutzer hinzufügen und entfernen. Indem Sie Benutzer hinzufügen, gewähren Sie ihnen Zugriff zum Senden von Nachrichten an den Chatthread und zum Hinzufügen/Entfernen anderer Mitglieder. Stellen Sie vor dem Aufrufen der `add_members`-Methode sicher, dass Sie ein neues Zugriffstoken und eine Identität für den Benutzer beschafft haben. Der Benutzer benötigt dieses Zugriffstoken, um den Chatclient initialisieren zu können.

Verwenden Sie die `add_members`-Methode, um dem Thread, der mit der „threadId“ identifiziert wird, Threadmitglieder hinzuzufügen.

- Verwenden Sie `members`, um die Mitglieder aufzulisten, die dem Chatthread hinzugefügt werden sollen.
- `user` (erforderlich) ist der `CommunicationUser`, den Sie mit `CommunicationIdentityClient` unter [Erstellen eines Benutzers](../../access-tokens.md#create-an-identity) erstellt haben.
- `display_name` (optional) ist der Anzeigename für das Threadmitglied.
- `share_history_time` (optional) ist der Zeitpunkt, ab dem der Chatverlauf für das Mitglied freigegeben wird. Sie können den Verlauf seit dem Beginn des Chatthreads freigeben, indem Sie diese Eigenschaft auf das Datum der Threaderstellung (oder früher) festlegen. Geben Sie das aktuelle Datum an, um den Verlauf vor dem Hinzufügezeitpunkt des Mitglieds nicht freizugeben. Falls Sie einen Teil des Verlaufs freigeben möchten, können Sie ein Datum angeben, das zwischen diesen Zeitpunkten liegt.

```python
new_user = identity_client.create_user()

from azure.communication.chat import ChatThreadMember
from datetime import datetime
member = ChatThreadMember(
    user=new_user,
    display_name='name',
    share_history_time=datetime.utcnow())
thread_members = [member]
chat_thread_client.add_members(thread_members)
```

## <a name="remove-user-from-a-chat-thread"></a>Entfernen eines Benutzers aus einem Chatthread

Sie können Mitglieder einem Thread nicht nur hinzufügen, sondern diese auch daraus entfernen. Zum Entfernen müssen Sie die IDs der von Ihnen hinzugefügten Mitglieder nachverfolgen.

Verwenden Sie die `remove_member`-Methode, um das Threadmitglied aus dem Thread zu entfernen, der mit der „threadId“ identifiziert wird.
- `user` ist der „CommunicationUser“, der aus dem Thread entfernt werden soll.

```python
chat_thread_client.remove_member(user)
```

## <a name="run-the-code"></a>Ausführen des Codes

Führen Sie die Anwendung mit dem Befehl `python` aus dem Anwendungsverzeichnis aus.

```console
python start-chat.py
```
