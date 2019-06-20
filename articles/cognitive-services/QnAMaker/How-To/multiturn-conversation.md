---
title: Konversationen mit Mehrfachdurchläufen
titleSuffix: Azure Cognitive Services
description: Verwalten Sie für Ihren Bot von einer Frage zur nächsten die verschiedenen Durchläufe (Multi-Turn) mit Eingabeaufforderungen und Kontext. Mehrfachdurchläufe sind die Fähigkeit, eine wechselseitige Konversation zu führen, wobei der Kontext der vorherigen Frage die nächste Frage und die nächste Antwort beeinflusst.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/12/2019
ms.author: diberry
ms.openlocfilehash: 7c7d7c480068b754413fd2309a2251d1e3855106
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075187"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Erstellen von Mehrfachdurchläufen einer Konversation mit Folgeaufforderungen

Verwalten Sie für Ihren Bot von einer Frage zur nächsten die Mehrfachdurchläufe (_Multi-Turn_) mit Folgeaufforderungen und Kontext.

Im folgenden Video wird veranschaulicht, wie dies funktioniert.

[![](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample).

## <a name="what-is-a-multi-turn-conversation"></a>Was ist eine Konversation mit Mehrfachdurchläufen?

Einige Fragen können nicht in einem Zug beantwortet werden. Wenn Sie Konversationen (Chatbot) in Ihrer Clientanwendung entwerfen, kann ein Benutzer eine Frage stellen, die gefiltert oder verfeinert werden muss, um die richtige Antwort zu bestimmen. Ein derartiger Flow durch die Fragen kann erreicht werden, indem dem Benutzer **Folgeaufforderungen** präsentiert werden.

Wenn der Benutzer die Frage stellt, gibt QnA Maker die Antwort _und_ etwaige Folgeaufforderungen zurück. Dadurch können Folgefragen als Auswahlmöglichkeiten präsentiert werden. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Beispiel für Konversation mit Mehrfachdurchläufen mit Chatbot

Ein Chatbot verwaltet die Konversation mit dem Benutzer Frage für Frage, um die abschließende Antwort zu bestimmen.

![Verwalten Sie im Konversationsablauf den Konversationszustand in einem Mehrfachdurchlauf-Dialogsystem, indem Sie innerhalb der Antworten Eingabeaufforderungen als Optionen präsentieren, mit denen die Konversation fortgesetzt werden kann.](../media/conversational-context/conversation-in-bot.png)

In der voranstehenden Abbildung hat der Benutzer `My account` eingegeben. Die Wissensdatenbank verfügt über 3 verknüpfte Frage- und Antwortpaare. Der Benutzer muss zum Verfeinern der Antwort eine der drei Optionen auswählen. In der Wissensdatenbank hat die Frage (#1) drei Folgeaufforderungen, die im Chatbot als drei Auswahlmöglichkeiten (#2) angezeigt werden. 

Wenn der Benutzer eine Auswahlmöglichkeit (#3) auswählt, wird die Liste der Verfeinerungsoptionen (#4) angezeigt. Dieser Vorgang kann fortgesetzt werden (#5), bis die richtige und abschließende Antwort (#6) bestimmt wird.

In der voranstehenden Abbildung ist **Enable multi-turn** (Mehrfachdurchläufe aktivieren) ausgewählt, um die Eingabeaufforderungen anzuzeigen. 

### <a name="using-multi-turn-in-a-bot"></a>Verwenden von Mehrfachdurchläufen in einem Bot

Sie müssen Ihre Clientanwendung ändern, um die kontextbezogene Konversation zu verwalten. Sie müssen [Ihrem Bot Code hinzufügen](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting), um Eingabeaufforderungen anzuzeigen.  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Erstellen einer Mehrfachdurchlauf-Konversation anhand der Struktur eines Dokuments

Beim Erstellen einer Wissensdatenbank sehen Sie ein optionales Kontrollkästchen, mit dem Sie die Mehrfachdurchlauf-Extrahierung aktivieren können. 

![Beim Erstellen einer Wissensdatenbank sehen Sie ein optionales Kontrollkästchen, mit dem Sie die Mehrfachdurchlauf-Extrahierung aktivieren können.](../media/conversational-context/enable-multi-turn.png)

Bei Aktivierung dieser Option kann die Mehrfachdurchlauf-Konversation beim Importieren eines Dokuments aus der Struktur abgeleitet werden. Ist diese Struktur vorhanden, erstellt QnA Maker automatisch die QnA-Paare mit Folgeaufforderungen. 

Die Mehrfachdurchlauf-Struktur kann nur aus URLs, PDF- und DOCX-Dateien abgeleitet werden. 

Nutzen Sie die folgende Abbildung einer Microsoft Surface-[PDF-Datei](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf) als Anleitung. Aufgrund der Größe der PDF-Datei erfordert die Azure QnA Maker-Ressource für die Suche mindestens die Preisstufe „B (15 Indizes)“. 

![![Wenn Sie ein Dokument importieren, kann eine kontextbezogene Konversation aus der Struktur abgeleitet werden. Ist diese Struktur vorhanden, erstellt QnA Maker im Rahmen des Dokumentimports automatisch die QnA-Paare mit Folgeaufforderungen.](../media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

Beim Importieren des PDF-Dokuments bestimmt QnA Maker Folgeaufforderungen anhand der Struktur, um den Konversationsablauf zu erstellen. 

1. In **Schritt 1** wählen Sie in der oberen Navigation die Option **Create a knowledge base** (Wissensdatenbank erstellen) aus.
1. In **Schritt2** erstellen Sie einen Frage- und Antwortdienst, oder verwenden Sie einen vorhandenen. Achten Sie darauf, einen Frage- und Antwortdienst mit einem Suchdienst der Preisstufe „B (15 Indizes)“ oder höher zu verwenden, da die PDF-Datei des Surface-Handbuchs zu groß für eine niedrigere Preisstufe ist.
1. In **Schritt 3** geben Sie einen Namen für Ihre Wissensdatenbank ein, z.B. `Surface manual`.
1. In **Schritt 4** aktivieren Sie **Enable multi-turn extraction from URLs, .pdf or .docx files** (Aktivieren der Mehrfachdurchlauf-Extrahierung von URLs, PDF- oder DOCX). Auswählen der URL für das Surface-Handbuch

    ```text
    https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf
    ```

1. Wählen Sie die Schaltfläche **KB erstellen** aus. 

    Sobald die Wissensdatenbank erstellt wurde, wird ein Überblick der Frage- und Antwortpaare angezeigt.

## <a name="show-questions-and-answers-with-context"></a>Anzeigen von Fragen und Antworten mit Kontext

Reduzieren Sie die Anzahl der angezeigten Frage- und Antwortpaare auf diejenigen, die kontextbezogene Konversationen aufweisen. 

1. Wählen Sie **Optionen anzeigen** und anschließend **Show context (PREVIEW)** (Kontext anzeigen (VORSCHAU)) aus. Die Liste enthält die Frage- und Antwortpaare mit Folgeaufforderungen. 

    ![Filtern von Frage- und Antwortpaaren nach kontextbezogenen Konversationen](../media/conversational-context/filter-question-and-answers-by-context.png)

2. Der Mehrfachdurchlauf-Kontext wird in der ersten Spalte angezeigt.

    ![![Beim Importieren des PDF-Dokuments bestimmt QnA Maker Folgeaufforderungen anhand der Struktur, um den Konversationsablauf zu erstellen. ](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

    Das „#1“ in der vorherigen Abbildung zeigt fett formatierten Text in die Spalte und damit die aktuelle Frage an. Die übergeordnete Frage wird als oberstes Element in der Zeile angezeigt. Alle Fragen darunter sind die verknüpften Frage- und Antwortpaare. Diese Elemente können ausgewählt werden, um sofort zu den anderen Kontextelementen zu gelangen. 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>Hinzufügen eines vorhandenen Frage- und Antwortpaars als Folgeaufforderung

Die ursprüngliche Frage von `My account` verfügt über Folgeaufforderung wie z.B. `Accounts and signing in`. 

![Die ursprüngliche Frage von „Mein Konto“ gibt ordnungsgemäß die Antworten zu „Konten und Anmeldung“ zurück, und ist schon mit der Folgeaufforderung verknüpft.](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Fügen Sie eine Folgeaufforderung zu einem vorhandenen Frage- und Antwortpaar hinzu, das derzeit noch nicht verknüpft ist. Da die Frage nicht mit einem Frage- und Antwortpaar verknüpft ist, muss sich die aktuelle Einstellung für die Ansicht ändern.

1. Um ein vorhandenes Frage- und Antwortpaar als Folgeaufforderung zu verknüpfen, wählen Sie die Zeile für das betreffende Frage- und Antwortpaar aus. Für das Surface-Handbuch suchen Sie nach `Sign out`, um die Liste zu reduzieren.
1. Wählen Sie in der Zeile für `Signout` aus der Spalte **Answer** (Antwort) die Option **Add follow-up prompt** (Folgeaufforderung hinzufügen) aus.
1. Geben Sie im Popupfenster **Follow-up prompt (PREVIEW)** (Folgeaufforderung (VORSCHAU)) Folgendes ein:

    |Feld|Wert|
    |--|--|
    |Anzeigetext|`Turn off the device`. Dies ist benutzerdefinierter Text, der für die Folgeaufforderung angezeigt werden soll.|
    |Context-only (Nur Kontext)|Aktiviert. Diese Antwort wird nur zurückgegeben, wenn die Frage Kontext angibt.|
    |Link to answer (Link zur Antwort)|Geben Sie `Use the sign-in screen` ein, um das vorhandene Frage- und Antwortpaar zu suchen.|


1.  Es wird eine Übereinstimmung zurückgegeben. Wählen Sie diese Antwort als Folgeaufforderung und dann **Save** (Speichern) aus. 

    ![Suchen Sie mit dem Antworttext im Dialogfeld „Link to Answer“ (Link zur Antwort) der Folgeaufforderung nach einer vorhandenen Antwort.](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Wenn Sie die Folgeaufforderung hinzugefügt haben, müssen Sie in der oberen Navigation **Save and train** (Speichern und trainieren) auswählen.
  
### <a name="edit-the-display-text"></a>Bearbeiten des Anzeigetexts 

Wenn eine Folgeaufforderung erstellt und ein vorhandenes Frage- und Antwortpaar als **Link zur Antwort** ausgewählt wird, können Sie neuen **Anzeigetext** eingeben. Dieser Text ersetzt die vorhandene Frage nicht, und es wird keine neue alternative Frage hinzugefügt. Es ist von diesen Werten getrennt. 

1. Zum Bearbeiten des Anzeigetexts suchen Sie im Feld **Context** (Kontext) nach der Frage, und wählen Sie sie aus.
1. Wählen Sie in der Zeile dieser Frage die Folgeaufforderung in der Antwortspalte aus. 
1. Wählen Sie den Anzeigetext, den Sie bearbeiten möchten, und dann **Edit** (Bearbeiten) aus.

    ![Wählen Sie den Anzeigetext, den Sie bearbeiten möchten, und dann „Edit“ (Bearbeiten) aus.](../media/conversational-context/edit-existing-display-text.png)

1. Das Popup **Folgeaufforderung** ermöglicht Ihnen, den vorhandenen Anzeigetext zu ändern. 
1. Wenn Sie die Bearbeitung Ihres Anzeigetexts abgeschlossen haben, wählen Sie **Save** (Speichern) aus. 
1. Denken Sie daran, in der oberen Navigationsleiste **Save and train** (Speichern und trainieren) auszuwählen.


<!--

## To find best prompt answer, add metadata to follow-up prompts 

If you have several follow-up prompts for a given QnA pair, but you know as the knowledge base manager, that not all prompts should be returned, use metadata to categorize the prompts in the knowledge base, then send the metadata from the client application as part of the GenerateAnswer request.

In the knowledge base, when a question-and-answer pair is linked to follow-up prompts, the metadata filters are applied first, then the follow-ups are returned.

1. For the two follow-up QnA pairs, add metadata to each one:

    |Question|Add metadata|
    |--|--|
    |`Feedback on an QnA Maker service`|"Feature":"all"|
    |`Feedback on an existing feature`|"Feature":"one"|
    
    ![Add metadata to follow-up prompt so it can be filtered in conversation response from service](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Save and train. 

    When you send the question `Give feedback` with the metadata filter `Feature` with a value of `all`, only the QnA pair with that metadata will be returned. Both QnA pairs are not returned because they both do not match the filter. 

-->

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>Hinzufügen eines neuen Frage- und Antwortpaars als Folgeaufforderung

Fügen Sie ein neues Frage- und Antwortpaar zur Wissensdatenbank hinzu. Das Frage- und Antwortpaar sollte mit einer vorhandenen Frage als Folgeaufforderung verknüpft sein.

1. Suchen Sie in der Symbolleiste der Wissensdatenbank das vorhandene Frage- und Antwortpaar für `Accounts and Signing In` aus, und wählen Sie es aus. 

1. Wählen Sie in der Spalte **Antwort** für diese Frage **Add follow-up prompt** (Folgeaufforderung hinzufügen) aus. 
1. Erstellen Sie in **Follow-up prompt (PREVIEW)** (Folgeaufforderung (VORSCHAU)) eine neue Folgeaufforderung, indem Sie folgende Werte eingeben: 

    |Textfeld|Wert|
    |--|--|
    |**Display Text** (Anzeigetext)|`Create a Windows Account`. Dies ist benutzerdefinierter Text, der für die Folgeaufforderung angezeigt werden soll.|
    |**Context-only** (Nur Kontext)|Aktiviert. Diese Antwort wird nur zurückgegeben, wenn die Frage Kontext angibt.|
    |**Link to answer** (Link zur Antwort)|Geben Sie den folgenden Text als Antwort ein:<br>`[Create](https://account.microsoft.com/) a Windows account with a new or existing email account.`<br>Wenn Sie die Datenbank speichern und trainieren, wird dieser Text konvertiert in |
    |||

    ![Erstellen eines neuen Frage- und Antwortpaars mit Aufforderung](../media/conversational-context/create-child-prompt-from-parent.png)


1. Wählen Sie **Neu erstellen** und anschließend **Speichern** aus. 

    Dadurch wurde ein neues Frage- und Antwortpaar erstellt, und die ausgewählte Frage wurde als Folgeaufforderung verknüpft. In der Spalte **Context** (Kontext) wird für beide Fragen eine Folgeaufforderungsbeziehung angegeben. 

1. Ändern Sie die **Ansichtsoptionen** so, dass [Kontext angezeigt wird](#show-questions-and-answers-with-context).

    Die neue Frage zeigt die Art der Verknüpfung an.

    ![Erstellen einer neuen Folgeaufforderung ](../media/conversational-context/new-qna-follow-up-prompt.png)

    Die übergeordneten Frage zeigt die neue Frage als eine der möglichen Optionen an.

    ![![In der Spalte „Kontext“ wird für beide Fragen eine Folgeaufforderungsbeziehung angegeben.](../media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Wenn Sie die Folgeaufforderung hinzugefügt haben, müssen Sie in der oberen Navigation **Save and train** (Speichern und trainieren) auswählen.

## <a name="enable-multi-turn-when-testing-follow-up-prompts"></a>Aktivieren von Mehrfachdurchläufen beim Testen von Folgeaufforderungen

Wenn Sie die Frage mit Folgeaufforderungen im Bereich **Test** testen, wählen Sie **Mehrfachdurchlauf aktivieren**, und geben Sie Ihre Frage ein. Die Antwort enthält die Folgeaufforderungen.

![Beim Testen der Frage im Testbereich schließt die Antwort die Folgeaufforderungen ein.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Wenn Sie Mehrfachdurchläufe nicht aktivieren, wird zwar die Antwort zurückgegeben, aber Folgeaufforderungen werden nicht zurückgegeben.

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>JSON-Anforderung für Rückgabe der ersten Antwort und der Folgeaufforderungen

Fordern Sie mit dem leeren `context`-Objekt die Antwort auf die Frage des Benutzers an, und schließen Sie Folgeaufforderungen ein. 

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="json-response-to-return-initial-answer-and-follow-up-prompts"></a>JSON-Antwort für Rückgabe der ersten Antwort und der Folgeaufforderungen

Im vorherigen Abschnitt wurden eine Antwort und etwaige Folgeaufforderungen für `Accounts and signing in` angefordert. Die Antwort enthält die Aufforderungsinformationen, die sich unter `answers[0].context` befinden und den Text einschließen, der für den Benutzer angezeigt werden soll. 

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 17,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 18,
                        "qna": null,
                        "displayText": "Sign out"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 79,
                        "qna": null,
                        "displayText": "Create a Windows Account"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

Das `prompts`-Array gibt Text in der `displayText`-Eigenschaft sowie den Wert für `qnaId` an, sodass Sie diese Antworten als nächste angezeigte Auswahlmöglichkeiten im Konversationsfluss präsentieren können, und sendet den ausgewählten Wert anschließend in der folgenden Anforderung an QnA Maker. 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>JSON-Anforderung für Rückgabe einer nachfolgenden Antwort und der Folgeaufforderungen

Füllen Sie das `context`-Objekt aus, um den vorherigen Kontext einzuschließen.

In der folgenden JSON-Anforderung ist die aktuelle Frage `Use Windows Hello to sign in`, und die vorherige Frage war `Accounts and signing in`. 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
    "previousUserQuery": "accounts and signing in"
  }
}
``` 

##  <a name="json-response-to-return-non-initial-answer-and-follow-up-prompts"></a>JSON-Antwort für Rückgabe einer nachfolgenden Antwort und der Folgeaufforderungen

QnA Maker-JSON-Antwort _GenerateAnswer_ enthält die Folgeaufforderungen in der `context`-Eigenschaft des ersten Elements im `answers`-Objekt:

```JSON
{
    "answers": [
        {
            "questions": [
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere’s a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you’re ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3” display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth® \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-id"></a>Abfragen der Wissensdatenbank mit der QnA-ID

In der Antwort auf die erste Frage werden alle Folgeaufforderungen und zugehörigen `qnaId` zurückgegeben. Da Sie nun über die ID verfügen, können Sie diese im Anforderungstext der Folgeaufforderung übergeben. Wenn der Anforderungstext die `qnaId` und das Kontextobjekt (mit den vorherigen QnA-Eigenschaften) enthält, wird von „GenerateAnswer“ die genaue Frage anhand der ID zurückgegeben, anstatt den Rangfolgenalgorithmus zu verwenden, um die Antwort anhand des Fragentexts zu suchen. 

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>Anzeigen von Eingabeaufforderungen und Senden von Kontext in der Clientanwendung 

Sie haben Eingabeaufforderungen zu Ihrer Wissensdatenbank hinzugefügt und den Datenfluss im Testbereich getestet. Jetzt müssen Sie diese Eingabeaufforderungen in der Clientanwendung verwenden. Für Bot Framework werden die Eingabeaufforderungen nicht automatisch in den Clientanwendungen angezeigt. Sie können die Eingabeaufforderungen als vorgeschlagene Aktionen oder Schaltflächen als Teil der Antwort auf die Abfrage des Benutzers in Clientanwendungen anzeigen, indem Sie dieses [Bot Framework-Beispiel](https://aka.ms/qnamakermultiturnsample) in Ihren Code einschließen. Die Clientanwendung sollte die aktuelle QnA-ID sowie die Benutzerabfrage speichern und für die nächste Benutzerabfrage an das [Kontextobjekt der GenerateAnswer-API](#json-request-to-return-non-initial-answer-and-follow-up-prompts) übergeben. 

## <a name="display-order-supported-in-api"></a>Unterstützung der Anzeigereihenfolge in der API

Der in der JSON-Antwort zurückgegebene [Anzeigetext und die Anzeigereihenfolge](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto) können von der [Update-API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update) bearbeitet werden. 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über kontextbezogene Konversationen im [Dialogbeispiel](https://aka.ms/qnamakermultiturnsample), oder informieren Sie sich über [konzeptionelles Bot-Design für Konversationen mit Mehrfachdurchläufen](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrieren einer Wissensdatenbank](../Tutorials/migrate-knowledge-base.md)
