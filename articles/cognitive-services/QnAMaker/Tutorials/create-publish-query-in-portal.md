---
title: Erstellen, Veröffentlichen und Antworten in QnA Maker
titleSuffix: Azure Cognitive Services
description: Erstellen Sie eine neue Wissensdatenbank mit Fragen und Antworten aus einer öffentlichen webbasierten FAQ-Instanz. Speichern, trainieren und veröffentlichen Sie die Wissensdatenbank. Senden Sie nach dem Veröffentlichen der Wissensdatenbank mithilfe eines cURL-Befehls eine Frage, und erhalten Sie eine Antwort. Erstellen Sie anschließend einen Bot, und testen Sie ihn mit der gleichen Frage.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a13e0cb0e594571344b16d007ef13475b384b73d
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692998"
---
# <a name="tutorial-from-the-qna-maker-portal-create-a-knowledge-base"></a>Tutorial: Erstellen einer Wissensdatenbank im QnA Maker-Portal

Erstellen Sie eine neue Wissensdatenbank mit Fragen und Antworten aus einer öffentlichen webbasierten FAQ-Instanz. Speichern, trainieren und veröffentlichen Sie die Wissensdatenbank. Senden Sie nach dem Veröffentlichen der Wissensdatenbank mithilfe eines cURL-Befehls eine Frage, und erhalten Sie eine Antwort. Erstellen Sie anschließend einen Bot, und testen Sie ihn mit der gleichen Frage. 

In diesem Tutorial lernen Sie Folgendes: 

> [!div class="checklist"]
> * Erstellen einer Wissensdatenbank über das QnA Maker-Portal
> * Überprüfen, Speichern und Trainieren der Wissensdatenbank
> * Veröffentlichen der Wissensdatenbank
> * Abfragen der Wissensdatenbank mithilfe von cURL
> * Erstellen eines Bots
 

> [!NOTE]
> Die programmgesteuerte Version dieses Tutorials steht zusammen mit einer vollständigen Projektmappe im [GitHub-Repository **Azure-Samples/cognitive-services-qnamaker-csharp**](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base) zur Verfügung.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial muss ein [QnA Maker-Dienst](../How-To/set-up-qnamaker-service-azure.md) vorhanden sein. 

## <a name="create-a-knowledge-base"></a>Erstellen einer Wissensdatenbank 

1. Melden Sie sich beim [QnA Maker-Portal](https://www.qnamaker.ai) an. 

1. Wählen Sie im oberen Menü die Option **Create a knowledge base** (Wissensdatenbank erstellen) aus.

    ![Screenshot des QnA Maker-Portals](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. Da Sie Ihren bereits vorhandenen QnA Maker-Dienst verwenden, können Sie den ersten Schritt überspringen. 

1. Auswählen Ihrer vorhandenen Einstellungen:  

    |Einstellung|Zweck|
    |--|--|
    |Microsoft Azure Directory ID (Microsoft Azure-Verzeichnis-ID)|Diese ID ist dem Konto zugeordnet, das Sie für die Anmeldung beim Azure-Portal und beim QnA Maker-Portal verwenden. |
    |„Azure Subscription name“ (Name des Azure-Abonnements)|Das Abrechnungskonto, unter dem Sie die QnA Maker-Ressource erstellt haben|
    |„Azure QnA Service“ (Azure QnA-Dienst)|Ihre vorhandene QnA Maker-Ressource.|

    ![Screenshot des QnA Maker-Portals](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. Geben Sie den Namen Ihrer Wissensdatenbank ein: `My Tutorial kb`.

    ![Screenshot des QnA Maker-Portals](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. Füllen Sie Ihre Wissensdatenbank mit den folgenden Einstellungen auf:  

    |Einstellungsname|Einstellungswert|Zweck|
    |--|--|--|
    |URL|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |Die Inhalte der häufig gestellten Fragen unter dieser URL sind jeweils als Frage-Antwort-Paar formatiert. QnA Maker kann dieses Format interpretieren, um Fragen und die dazugehörigen Antworten zu extrahieren.|
    |Datei |_In diesem Tutorial nicht verwendet_|Dient zum Hochladen von Dateien für Fragen und Antworten. |
    |„Chit-chat personality“ (Smalltalk-Persönlichkeit)|Freundlich|Dadurch erhalten Sie eine freundliche und lockere [Persönlichkeit](../Concepts/best-practices.md#chit-chat) für allgemeine Fragen und Antworten. Die Fragen und Antworten können später bearbeitet werden. |

    ![Screenshot des QnA Maker-Portals](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. Wählen Sie **Create your KB** (Wissensdatenbank erstellen) aus, um den Erstellungsprozess abzuschließen.

    ![Screenshot des QnA Maker-Portals](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-save-and-train-the-knowledge-base"></a>Überprüfen, Speichern und Trainieren der Wissensdatenbank

1. Überprüfen Sie die Fragen und Antworten. Auf der ersten Seite befinden sich Fragen und Antworten von der URL. 

    ![Screenshot des QnA Maker-Portals](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. Wählen Sie die letzte Seite mit Fragen und Antworten vom Ende der Tabelle aus. Auf der Seite werden Fragen und Antworten der Smalltalk-Persönlichkeit angezeigt. 

1. Wählen Sie auf der Symbolleiste über der Liste mit Fragen und Antworten das Symbol **Ansichtsoptionen** und dann **Metadaten anzeigen** aus. Daraufhin werden die Metadatentags für die einzelnen Fragen und Antworten angezeigt. Für Fragen im Plauderton ist bereits das Metadatentag **editorial: chit-chat** festgelegt. Dieses Metadatentag wird zusammen mit der ausgewählten Antwort an die Clientanwendung zurückgegeben. Anhand dieser gefilterten Metadaten kann die Clientanwendung (beispielsweise ein Chatbot) zusätzliche Verarbeitungsschritte oder Interaktionen mit dem Benutzer bestimmen.

    ![Screenshot des QnA Maker-Portals](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. Wählen Sie auf der oberen Menüleiste die Option **Save and train** (Speichern und trainieren) aus.

## <a name="publish-to-get-knowledge-base-endpoints"></a>Veröffentlichen der Endpunkte der Wissensdatenbank

Wählen Sie im oberen Menü die Schaltfläche **Veröffentlichen** aus. Wählen Sie auf der Veröffentlichungsseite die Option **Veröffentlichen** aus.

![Screenshot des QnA Maker-Portals](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

Der Endpunkt wird nach dem Veröffentlichen der Wissensdatenbank angezeigt.

![Screenshot der Endpunkteinstellungen](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

Schließen Sie die Seite **Veröffentlichen** nicht. Sie benötigen sie später in diesem Tutorial zum Erstellen eines Bots. 

## <a name="use-curl-to-query-for-an-faq-answer"></a>Abfragen einer FAQ-Antwort mithilfe von cURL

1. Wählen Sie die Registerkarte **Curl** aus. 

    ![Screenshot der Registerkarte „Curl“](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Kopieren Sie den Text auf der Registerkarte **Curl**, und führen Sie ihn in einem cURL-fähigen Terminal oder an einer cURL-fähigen Befehlszeile aus. Der Wert des Autorisierungsheaders enthält den Text `Endpoint`, gefolgt von einem Leerzeichen und dem Schlüssel.

1. Ersetzen Sie `<Your question>` durch `How large can my KB be?`. Dies ähnelt der Frage `How large a knowledge base can I create?`, ist aber nicht exakt das Gleiche. QnA Maker ermittelt mittels Verarbeitung natürlicher Sprache, dass die beiden Fragen gleich sind.     

1. Führen Sie den cURL-Befehl aus, um die JSON-Antwort mit der Bewertung und der Antwort auf die Frage zu erhalten. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   581  100   543  100    38    418     29  0:00:01  0:00:01 --:--:--   447{
      "answers": [
        {
          "questions": [
            "How large a knowledge base can I create?"
          ],
          "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
          "score": 42.81,
          "id": 2,
          "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
          "metadata": []
        }
      ]
    }
    
    ```

    QnA Maker ist zu 42,81 Prozent von dem Ergebnis überzeugt.  

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Abfragen einer Smalltalk-Antwort mithilfe von cURL

1. Ersetzen Sie im cURL-fähigen Terminal `How large can my KB be?` durch eine Benutzeraussage, die das Ende der Botunterhaltung signalisiert (etwa `Thank you`).   

1. Führen Sie den cURL-Befehl aus, um die JSON-Antwort mit der Bewertung und der Antwort auf die Frage zu erhalten. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   525  100   501  100    24    525     25 --:--:-- --:--:-- --:--:--   550{
      "answers": [
        {
          "questions": [
            "Thank you",
            "Thanks",
            "Thnx",
            "Kthx",
            "I appreciate it",
            "Thank you so much",
            "I thank you",
            "My sincere thank"
          ],
          "answer": "You're very welcome.",
          "score": 100.0,
          "id": 109,
          "source": "qna_chitchat_the_friend.tsv",
          "metadata": [
            {
              "name": "editorial",
              "value": "chitchat"
            }
          ]
        }
      ]
    }
   
    ```

    Da die Frage `Thank you` exakt einer Smalltalk-Frage entspricht, ist QnA Maker zu 100 Prozent überzeugt. QnA Maker hat auch alle dazugehörigen Fragen sowie die Metadateneigenschaft mit den Informationen zum Metadatentag für Smalltalk zurückgegeben.  

## <a name="use-curl-to-query-for-the-default-answer"></a>Abfragen der Standardantwort mithilfe von cURL

Jede Frage, bei deren Antwort QnA Maker nicht sicher ist, wird mit einer Standardantwort beantwortet. Diese Antwort wird im Azure-Portal konfiguriert. 

1. Ersetzen Sie `Thank you` im cURL-fähigen Terminal durch `x`. 

1. Führen Sie den cURL-Befehl aus, um die JSON-Antwort mit der Bewertung und der Antwort auf die Frage zu erhalten. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   186  100   170  100    16    272     25 --:--:-- --:--:-- --:--:--   297{
      "answers": [
        {
          "questions": [],
          "answer": "No good match found in KB.",
          "score": 0.0,
          "id": -1,
          "metadata": []
        }
      ]
    }
    ```
    
    QnA Maker hat als Bewertung den Wert `0` (nicht überzeugt) zurückgegeben. Darüber hinaus wurde die Standardantwort zurückgegeben. 

## <a name="create-a-knowledge-base-bot"></a>Erstellen einer Wissensdatenbank-Bots

Weitere Informationen finden Sie unter [Tutorial: Erstellen eines QnA-Bots mit Azure Bot Service v4](create-qna-bot.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den Wissensdatenbank-Bot nicht mehr benötigen, entfernen Sie die Ressourcengruppe `my-tutorial-rg`, um alle im Rahmen des Botprozesses erstellten Azure-Ressourcen zu entfernen.

Wenn Sie mit der Wissensdatenbank fertig sind, wählen Sie im QnA Maker-Portal die Option **My knowledge bases** (Meine Knowledge Bases) aus. Wählen Sie anschließend die Wissensdatenbank **My Tutorial kb** und schließlich ganz rechts in der Zeile das Löschsymbol aus.  

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu unterstützten Datenformaten finden Sie unter [Datenquellen für QnA Maker-Inhalt](../Concepts/data-sources-supported.md). 

Weitere Informationen zu Smalltalk-Persönlichkeiten finden Sie [hier](../Concepts/best-practices.md#chit-chat).

Weitere Informationen zur Standardantwort finden Sie unter [Keine Übereinstimmung gefunden](../Concepts/confidence-score.md#no-match-found). 

> [!div class="nextstepaction"]
> [Tutorial: Erstellen eines QnA-Bots mit Azure Bot Service v4](create-qna-bot.md)