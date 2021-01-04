---
title: Chatkonzepte in Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Erfahren Sie mehr über Chatkonzepte in Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: f0e69e3f62d3b9e4debb5761d877dcdfdd246f60
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886021"
---
# <a name="chat-concepts"></a>Chatkonzepte

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Die Clientbibliotheken für Chats von Azure Communication Services können verwendet werden, um Echtzeittextchat zu Ihren Anwendungen hinzuzufügen. Auf dieser Seite werden wichtige Chatkonzepte und -funktionen zusammengefasst.

Weitere Informationen zu den einzelnen Sprachen und Funktionen der Clientbibliothek finden Sie in der [Übersicht über die Clientbibliothek für Chats von Communication Services](./sdk-features.md).

## <a name="chat-overview"></a>Übersicht über den Chat 

Chatkonversationen erfolgen innerhalb von Chatthreads. Ein Chatthread kann viele Nachrichten und Benutzer umfassen. Jede Nachricht gehört zu einem einzelnen Thread, und ein Benutzer kann einem oder mehreren Threads angehören. 

Die einzelnen Benutzer im Chatthread werden als Mitglieder bezeichnet. In einem Chatthread sind bis zu 250 Mitglieder möglich. Nur Threadmitglieder können Nachrichten senden und empfangen oder Mitglieder zu einem Chatthread hinzufügen bzw. daraus entfernen. Die maximal zulässige Nachrichtengröße beträgt ca. 28 KB. Mithilfe des `List/Get Messages`-Vorgangs können Sie alle Nachrichten in einem Chatthread abrufen. Communication Services speichert den Chatverlauf, bis Sie einen Löschvorgang für den Chatthread oder die Nachricht ausführen oder bis keine Mitglieder mehr im Chatthread verbleiben – an diesem Punkt ist er verwaist und wird für die Löschung verarbeitet.   

Für Chatthreads mit mehr als 20 Mitgliedern sind Lesebestätigungen und Eingabeindikatorfunktionen deaktiviert. 

## <a name="chat-architecture"></a>Chatarchitektur

Die Chatarchitektur besteht aus zwei zentralen Teilen: 1) einem vertrauenswürdigen Dienst und 2) einer Clientanwendung.

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagramm, das die Chatarchitektur von Communication Services zeigt":::

 - **Vertrauenswürdiger Dienst:** Damit Sie eine Chatsitzung ordnungsgemäß verwalten können, benötigen Sie einen Dienst, der Ihnen hilft, mithilfe ihrer Ressourcen-Verbindungszeichenfolge eine Verbindung mit Communication Services herzustellen. Dieser Dienst ist für das Erstellen von Chatthreads, das Verwalten von Threadmitgliedschaften und das Bereitstellen von Zugriffstoken für Benutzer verantwortlich. Weitere Informationen zu Zugriffstoken finden Sie in unserem Schnellstart zu [Zugriffstoken](../../quickstarts/access-tokens.md).

 - **Client-App:**  Die Clientanwendung stellt eine Verbindung mit Ihrem vertrauenswürdigen Dienst her und empfängt die Zugriffstoken, die zum Herstellen einer direkten Verbindung mit Communication Services verwendet werden. Nachdem diese Verbindung hergestellt wurde, kann Ihre Client-App Nachrichten senden und empfangen.
    
## <a name="message-types"></a>Nachrichtentypen

Ein Communication Services-Chat teilt vom Benutzer sowie vom System generierte Nachrichten, die als **Threadaktivitäten** bezeichnet werden. Threadaktivitäten werden generiert, wenn ein Chatthread aktualisiert wird. Wenn Sie `List Messages` oder `Get Messages` in einem Chatthread aufrufen, enthält das Ergebnis die vom Benutzer generierten Textnachrichten sowie die Systemnachrichten in chronologischer Reihenfolge. Auf diese Weise können Sie ermitteln, wann ein Mitglied hinzugefügt oder entfernt wurde oder wann das Thema des Chatthreads aktualisiert wurde. Die folgende Nachrichtentypen werden unterstützt:  

 - `Text`: Die von einem Benutzer als Teil der Chatkonversation verfasste und versendete Nachricht 
 - `ThreadActivity/AddMember`: Eine Systemnachricht, die angibt, dass mindestens ein Mitglied zum Chatthread hinzugefügt wurde Beispiel:

```xml

<addmember>
    <eventtime>1598478187549</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <detailedinitiatorinfo>
        <friendlyName>User 1</friendlyName>
    </detailedinitiatorinfo>
    <rosterVersion>1598478184564</rosterVersion>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</id>
        <friendlyName>User 1</friendlyName>
    </detailedtargetinfo>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</id>
        <friendlyName>User 2</friendlyName>
    </detailedtargetinfo>
</addmember>

```  

- `ThreadActivity/DeleteMember`: Eine Systemnachricht, die angibt, dass ein Mitglied aus dem Chatthread entfernt wurde Beispiel:

```xml

<deletemember>
    <eventtime>1598478187642</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <detailedinitiatorinfo>
        <friendlyName>User 1</friendlyName>
    </detailedinitiatorinfo>
    <rosterVersion>1598478184564</rosterVersion>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</id>
        <friendlyName>User 2</friendlyName>
    </detailedtargetinfo>
</deletemember>

```

- `ThreadActivity/TopicUpdate`: Systemnachricht, die angibt, dass das Thema aktualisiert wurde Beispiel:

```xml

<topicupdate>
    <eventtime>1598477591811</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <value>New topic</value>
</topicupdate>

```

## <a name="real-time-signaling"></a>Echtzeitsignalisierung 

Die JavaScript-Clientbibliothek für Chats enthält Echtzeitsignalisierung. Dadurch können Clients auf Echtzeitaktualisierungen und eingehende Nachrichten in einem Chatthread lauschen, ohne die APIs abfragen zu müssen. Verfügbare Ereignisse umfassen Folgendes:

 - `ChatMessageReceived`: wenn eine neue Nachricht an einen Chatthread gesendet wird, in dem der Benutzer Mitglied ist. Dieses Ereignis wird nicht für automatisch generierte Systemnachrichten gesendet, die im vorherigen Thema erläutert wurden.  
 - `ChatMessageEdited`: wenn eine Nachricht in einem Chatthread bearbeitet wird, in dem der Benutzer Mitglied ist 
 - `ChatMessageDeleted`: wenn eine Nachricht in einem Chatthread gelöscht wird, in dem der Benutzer Mitglied ist 
 - `TypingIndicatorReceived`: wenn ein anderes Mitglied eine Nachricht in einem Chatthread eingibt, in dem der Benutzer Mitglied ist 
 - `ReadReceiptReceived`: wenn ein anderes Mitglied die Nachricht gelesen hat, die der Benutzer in einem Chatthread gesendet hat 

## <a name="chat-events"></a>Chatereignisse 

Die Echtzeitsignalisierung ermöglicht es Ihren Benutzern, in Echtzeit zu chatten. Ihre Dienste können Azure Event Grid verwenden, um Ereignisse zu abonnieren, die für den Chat relevant sind. Weitere Informationen finden Sie unter [Konzepte für die Ereignisbehandlung](../event-handling.md).

## <a name="using-cognitive-services-with-chat-client-library-to-enable-intelligent-features"></a>Verwenden von Cognitive Services mit der Clientbibliothek für Chats zum Aktivieren intelligenter Features

Sie können [Azure Cognitive-APIs](../../../cognitive-services/index.yml) zusammen mit der Clientbibliothek für Chats verwenden, um intelligente Features zu Ihren Anwendungen hinzuzufügen. Beispielsweise können Sie folgende Aktionen ausführen:

- Ermöglichen Sie es Benutzern, miteinander in verschiedenen Sprachen zu chatten. 
- Helfen Sie einem Support-Agent bei der Priorisierung von Tickets, indem Sie eine negative Stimmung in einem eingehenden Problem eines Kunden erkennen.
- Analysieren Sie die eingehenden Nachrichten zur Schlüssel- und Entitätserkennung, und fordern Sie von Ihrem Benutzer in Ihrer App basierend auf dem Nachrichteninhalt relevante Informationen an.

Eine Möglichkeit, um dies zu erreichen, besteht darin, dass Ihr vertrauenswürdiger Dienst als Mitglied eines Chatthreads fungiert. Angenommen, Sie möchten die Sprachübersetzung aktivieren. Dieser Dienst ist dafür zuständig, auf Nachrichten zu lauschen, die von anderen Mitgliedern ausgetauscht werden [1], Cognitive-APIs aufzurufen, um den Inhalt in die gewünschte Sprache zu übersetzen [2,3] und das übersetzte Ergebnis als Nachricht im Chatthread zu senden [4]. 

So enthält der Nachrichtenverlauf sowohl die ursprünglichen als auch übersetzte Nachrichten. In der Clientanwendung können Sie Logik hinzufügen, um die ursprüngliche oder die übersetzte Nachricht anzuzeigen. Informationen zur Verwendung von Cognitive APIs zum Übersetzen von Text in verschiedene Sprachen finden Sie in [diesem Schnellstart](../../../cognitive-services/translator/quickstart-translator.md). 

:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagramm, das zeigt, wie Cognitive Services mit Communication Services interagieren":::

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erste Schritte mit dem Chat](../../quickstarts/chat/get-started.md)

Die folgenden Dokumente könnten Sie auch interessieren:

- Machen Sie sich mit der [Chatclientbibliothek](sdk-features.md) vertraut.