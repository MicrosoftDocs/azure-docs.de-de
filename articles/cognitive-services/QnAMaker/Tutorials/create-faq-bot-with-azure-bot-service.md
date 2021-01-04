---
title: 'Tutorial: Erstellen eines FAQ-Bots mit Azure Bot Service'
description: In diesem Tutorial erstellen Sie mit QnA Maker und Azure Bot Service einen FAQ-Bot ohne Code.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 08/31/2020
ms.openlocfilehash: 356b954c65086ff65264ce7a62b038cd7a8eac3d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350958"
---
# <a name="tutorial-create-an-faq-bot-with-azure-bot-service"></a>Tutorial: Erstellen eines FAQ-Bots mit Azure Bot Service
Erstellen Sie mit QnA Maker und Azure [Bot Service](https://azure.microsoft.com/services/bot-service/) einen FAQ-Bot ohne Code.

In diesem Tutorial lernen Sie Folgendes:

<!-- green checkmark -->
> [!div class="checklist"]
> * Verknüpfen einer QnA Maker-Wissensdatenbank mit einer Azure Bot Service-Instanz
> * Bereitstellen des Bots
> * Chatten mit dem Bot im Webchat
> * Hervorheben des Bots in den unterstützten Kanälen

## <a name="create-and-publish-a-knowledge-base"></a>Erstellen und Veröffentlichen einer Wissensdatenbank

Befolgen Sie die [Schnellstartanleitung](../Quickstarts/create-publish-knowledge-base.md), um eine Wissensdatenbank zu erstellen. Nach der erfolgreichen Veröffentlichung der Wissensdatenbank wird die folgende Seite angezeigt:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

![Screenshot: Erfolgreiche Veröffentlichung](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

![Screenshot: Erfolgreiche Veröffentlichung der verwalteten Version](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint-managed.png)

---

## <a name="create-a-bot"></a>Erstellen eines Bots

Nach der Veröffentlichung können Sie auf der Seite **Veröffentlichen** einen Bot erstellen:

* Sie können schnell mehrere Bots erstellen, die alle auf die gleiche Wissensdatenbank verweisen, und dabei verschiedene Regionen oder Tarife für die einzelnen Bots verwenden.
* Wenn Sie nur einen einzelnen Bot für die Wissensdatenbank erstellen möchten, verwenden Sie den Link zum **Anzeigen Ihrer Bots im Azure-Portal**, um eine Liste mit Ihren derzeitigen Bots anzuzeigen.

Wenn Sie Änderungen an der Wissensdatenbank vornehmen und sie erneut veröffentlichen, müssen Sie keine weiteren Schritte für den Bot ausführen. Er ist bereits für die Verwendung mit der Wissensdatenbank konfiguriert und funktioniert auch nach späteren Änderungen. Nach jeder Veröffentlichung einer Wissensdatenbank werden alle mit ihr verbundenen Bots automatisch aktualisiert.

1. Wählen Sie im QnA Maker-Portal auf der Seite **Veröffentlichen** die Option **Bot erstellen** aus. Diese Schaltfläche wird erst angezeigt, wenn die Wissensdatenbank veröffentlicht wurde.

     # <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

    ![Screenshot zum Erstellen eines Bots](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

    # <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

    ![Screenshot: Erstellen einer von einem Bot verwalteten Vorschau](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page-managed.png)

    ---
    

1. Für das Azure-Portal wird eine neue Browserregisterkarte mit der Erstellungsseite von Azure Bot Service geöffnet. Konfigurieren Sie Azure Bot Service. Der Bot und QnA Maker können den gleichen Web-App-Serviceplan, aber nicht die gleiche Web-App verwenden. Der **App-Name** für den Bot muss sich daher von dem App-Namen für den QnA Maker-Dienst unterscheiden.

    * **Empfohlene Vorgehensweise**
        * Ändern Sie das Bot-Handle, wenn es nicht eindeutig ist.
        * Wählen Sie die SDK-Sprache aus. Nachdem der Bot erstellt wurde, können Sie den Code in die lokale Entwicklungsumgebung herunterladen und den Entwicklungsprozess fortsetzen.
    * **Nicht empfohlene Vorgehensweise**
        * Ändern Sie beim Erstellen des Bots die folgenden Einstellungen im Azure-Portal nicht. Sie wurden automatisch für Ihre vorhandene Wissensdatenbank aufgefüllt:
           * QnA-Authentifizierungsschlüssel
           * App Service-Plan und Standort


1. Öffnen Sie nach der Erstellung des Bots die **Bot Service**-Ressource.
1. Wählen Sie unter **Botverwaltung** die Option **Testen im Webchat** aus.
1. Geben Sie bei der Chataufforderung **Nachricht eingeben** Folgendes ein:

    `Azure services?`

    Der Chatbot antwortet mit einer Antwort aus Ihrer Wissensdatenbank.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-web-chat.png" alt-text="Geben Sie eine Benutzerabfrage in den Testwebchat ein.":::
1. Heben Sie den Bot in zusätzlichen [unterstützten Kanälen](/azure/bot-service/bot-service-manage-channels?preserve-view=true&view=azure-bot-service-4.0) hervor.