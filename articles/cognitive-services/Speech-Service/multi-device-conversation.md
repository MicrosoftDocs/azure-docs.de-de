---
title: 'Mehrgerätekonversation (Vorschau): Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Mit der Mehrgerätekonversation können Sie auf einfache Weise eine Sprach- oder Textkonversation zwischen mehreren Clients erstellen und die zwischen ihnen gesendeten Nachrichten koordinieren.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 7b38bc390b28788003c2de757f45e8ecdfd5c89a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932424"
---
# <a name="what-is-multi-device-conversation-preview"></a>Worum handelt es sich bei der Konversation mit mehreren Geräten (Vorschauversion)?

Mit der **Mehrgerätekonversation** können Sie auf einfache Weise eine Sprach- oder Textkonversation zwischen mehreren Clients erstellen und die zwischen ihnen gesendeten Nachrichten koordinieren.

Die **Mehrgerätekonversation** bietet folgende Funktionen:

- Verbinden mehrerer Clients mit derselben Konversation und Verwalten des Sendens und Empfangens von Nachrichten zwischen diesen
- Einfaches Transkribieren von Audiodaten von jedem Client und Übertragen der Transkription mit optionaler Übersetzung an die anderen Clients
- Einfaches Senden von Textnachrichten zwischen Clients mit optionaler Übersetzung

Sie können ein Feature oder eine Lösung erstellen, das bzw. die für ein ganzes Array von Geräten funktioniert. Jedes Gerät kann unabhängig Nachrichten (Transkriptionen von Audiodaten oder Sofortnachrichten) an alle anderen Geräte senden.

Während die [**Unterhaltungstranskription**](conversation-transcription.md) auf einem einzelnen Gerät mit einem Multichannel-Mikrofonarray funktioniert, eignet sich die **Mehrgerätekonversation** für Szenarien mit mehreren Geräten, von denen jedes über ein eigenes Mikrofon verfügt.

>[!IMPORTANT]
> Die Konversation von mehreren Geräten unterstützt **nicht** das Senden von Audiodateien zwischen Clients, sondern nur von Transkriptionen oder Übersetzungen.

## <a name="key-features"></a>Wichtige Features

- **Echtzeittranskription:** Jeder Teilnehmer erhält ein Transkript der Konversation, um den Text in Echtzeit zu verfolgen oder für die spätere Verwendung zu speichern.
- **Echtzeitübersetzung:** Bei mehr als 70 [unterstützten Sprachen](language-support.md#text-languages) für die Textübersetzung können Benutzer die Konversation in ihre bevorzugten Sprachen übersetzen.
- **Lesbare Transkriptionen:** Die Transkription und die Übersetzung können ganz einfach verfolgt werden, da sie auch Interpunktion und Satzumbrüche enthalten.
- **Sprach- oder Texteingabe:** Jeder Benutzer kann in Abhängigkeit von den unterstützten Sprachfunktionen, die für die ausgewählte Sprache des Teilnehmers aktiviert sind, auf seinem eigenen Gerät Sprach- oder Texteingaben vornehmen. Weitere Informationen finden Sie unter [Sprachunterstützung](language-support.md#speech-to-text).
- **Nachrichtenrelay:** Der Dienst für die Mehrgerätekonversation verteilt die von einem Client gesendeten Nachrichten in den gewünschten Sprachen.
- **Nachrichtenidentifikation:** Jede Nachricht, die Benutzer während der Konversation empfangen, wird mit dem Spitznamen des Benutzers gekennzeichnet, der die Nachricht gesendet hat.

## <a name="use-cases"></a>Anwendungsfälle

### <a name="lightweight-conversations"></a>Einfache Konversationen

Das Erstellen einer Konversation und das Beitreten zu dieser sind ganz einfach. Ein Benutzer fungiert als „Gastgeber“ und erstellt eine Konversation, die einen zufälligen, aus fünf Buchstaben bestehenden Konversationscode und einen QR-Code generiert. Alle anderen Benutzer können der Konversation beitreten, indem sie den Konversationscode eingeben oder den QR-Code scannen. 

Da Benutzer über den Konversationscode beitreten können und keine Kontaktinformationen freigeben müssen, ist es einfach, schnelle und spontane Konversationen zu erstellen.

### <a name="inclusive-meetings"></a>Inklusive Besprechungen

Mithilfe von Echtzeittranskription und -übersetzung können Konversationen auch für Personen zugänglich gemacht werden, die andere Sprachen sprechen oder gehörlos oder schwerhörig sind. Jede Person kann außerdem aktiv in ihrer bevorzugten Sprache oder per Sofortnachrichten an der Konversation teilnehmen.

### <a name="presentations"></a>Präsentationen

Sie können auch Untertitel für Präsentationen und Vorträge auf dem Bildschirm und auf den eigenen Geräten der Teilnehmer bereitstellen. Nachdem die Teilnehmer mithilfe des Konversationscodes beigetreten sind, können sie die Aufzeichnung in ihrer bevorzugten Sprache auf ihrem eigenen Gerät anzeigen.

## <a name="how-it-works"></a>Funktionsweise

Alle Clients verwenden das Speech SDK, um eine Konversation zu erstellen oder dieser beizutreten. Das Speech SDK interagiert mit dem Mehrgerätekonversations-Dienst, der die Lebensdauer einer Konversation verwaltet. Dies schließt auch die Liste der Teilnehmer, die ausgewählten Sprachen der einzelnen Clients und die gesendeten Nachrichten ein.  

Jeder Client kann Audiodaten oder Sofortnachrichten senden. Der Dienst verwendet die Spracherkennung, um Audiodaten in Text zu konvertieren und damit unveränderte Sofortnachrichten zu senden. Wenn Clients andere Sprachen auswählen, übersetzt der Dienst alle Nachrichten in die angegebenen Sprachen für die einzelnen Clients.

![Übersichtsdiagramm für die Mehrgerätekonversation](media/scenarios/multi-device-conversation.png)

## <a name="overview-of-conversation-host-and-participant"></a>Übersicht über Konversation, Gastgeber und Teilnehmer

Eine **Konversation** ist eine Sitzung, die ein Benutzer startet und der die anderen Teilnehmer beitreten können. Alle Clients verwenden für das Herstellen einer Verbindung mit der Konversation den aus fünf Buchstaben bestehenden **Konversationscode**.

Jede Konversation erstellt Metadaten, die Folgendes enthalten:
-    Zeitstempel für die Anfangs- und Endzeit der Konversation
-    Liste aller Teilnehmer an der Konversation, einschließlich der ausgewählten Spitznamen der einzelnen Benutzer und der primären Sprachen für Sprach- oder Texteingaben


Es gibt zwei Benutzertypen in einer Konversation: **Gastgeber** und **Teilnehmer**.

Der **Gastgeber** ist der Benutzer, der eine Konversation startet und als Administrator für diese Konversation fungiert.
- Jede Konversation kann nur einen Gastgeber haben.
- Der Gastgeber muss für die gesamte Dauer der Konversation mit dieser verbunden bleiben. Wenn der Gastgeber die Konversation verlässt, endet die Konversation für alle anderen Teilnehmer.
- Dem Gastgeber stehen einige zusätzliche Steuerelemente für die Verwaltung der Konversation zur Verfügung: 
    - Konversation sperren: verhindert den Beitritt weiterer Teilnehmer
    - Alle Teilnehmer stummschalten: verhindert, dass andere Teilnehmer Nachrichten an die Konversation senden können (gilt für Transkriptionen von Sprach- und Sofortnachrichten)
    - Einzelne Teilnehmer stummschalten
    - Stummschaltung für alle Teilnehmer aufheben
    - Stummschaltung für einzelne Teilnehmer aufheben

Ein **Teilnehmer** ist ein Benutzer, der einer Konversation beitritt.
- Ein Teilnehmer kann dieselbe Konversation jederzeit verlassen und ihr wieder beitreten, ohne dass die Konversation damit für andere Teilnehmer endet.
- Teilnehmer können weder die Konversation sperren noch andere Teilnehmer stummschalten bzw. deren Stummschaltung aufheben.

> [!NOTE]
> Jede Konversation kann bis zu 100 Teilnehmer haben, von denen zu jedem beliebigen Zeitpunkt maximal 10 gleichzeitig sprechen können.

## <a name="language-support"></a>Sprachunterstützung

Beim Erstellen oder Beitreten zu einer Konversation muss jeder Benutzer eine **primäre Sprache** auswählen: Dies ist die Sprache, in der er spricht und Sofortnachrichten sendet, und auch die Sprache, in der die Nachrichten anderer Benutzer angezeigt werden.

Es gibt zwei Arten von Sprachen: **Speech-to-Text-** und **Textformat**:
- Wenn der Benutzer als primäre Sprache eine **Speech-to-Text**-Sprache auswählt, kann er in der Konversation sowohl Sprach- als auch Texteingaben verwenden.

- Wenn der Benutzer eine **Textformatsprache** auswählt, kann er nur Texteingaben verwenden und Sofortnachrichten in der Konversation senden. Textformatsprachen sind die Sprachen, die für die Textübersetzung unterstützt werden, jedoch nicht für die Spracherkennung (Speech-to-Text). Die verfügbaren Sprachen finden Sie auf der Seite [Sprachunterstützung](./language-support.md).

Neben der primären Sprache kann jeder Teilnehmer auch weitere Sprachen zum Übersetzen der Konversation angeben.

Im Folgenden finden Sie eine Zusammenfassung der Aktionen, die ein Benutzer in einer Mehrgerätekonversation basierend auf der ausgewählten primären Sprache ausführen kann.


| Benutzeraktionen in der Konversation | Spracherkennung | Textformat |
|-----------------------------------|----------------|------|
| Spracheingabe verwenden | ✔️ | ❌ |
| Sofortnachricht senden | ✔️ | ✔️ |
| Konversation übersetzen | ✔️ | ✔️ |

> [!NOTE]
> Eine Liste der Spracherkennungs- und Sprachübersetzungssprachen finden Sie unter [Unterstützte Sprachen](./language-support.md).



## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Übersetzen von Konversationen in Echtzeit](quickstarts/multi-device-conversation.md)