---
title: 'Schnellstart: Erstellen, Trainieren und Veröffentlichen einer Wissensdatenbank: QnA Maker'
description: Sie können eine QnA Maker-Wissensdatenbank (KB) aus Ihren eigenen Inhalten erstellen, z.B. aus FAQs oder Produkthandbüchern. Dieser Artikel enthält ein Beispiel zum Erstellen einer QnA Maker-Wissensdatenbank auf der Grundlage einer einfachen FAQ-Webseite, um Fragen zu QnA Maker zu beantworten.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: c59529db0981a1071b76714c48aacaf675e4b17a
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99987908"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Schnellstart: Erstellen, Trainieren und Veröffentlichen der QnA Maker-Wissensdatenbank

Sie können eine QnA Maker-Wissensdatenbank (KB) aus Ihren eigenen Inhalten erstellen, z.B. aus FAQs oder Produkthandbüchern. Dieser Artikel enthält ein Beispiel zum Erstellen einer QnA Maker-Wissensdatenbank auf der Grundlage einer einfachen FAQ-Webseite, um Fragen zu QnA Maker zu beantworten.

## <a name="prerequisites"></a>Voraussetzungen

> [!div class="checklist"]
> * Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen, bevor Sie beginnen.
> * Eine im Azure-Portal erstellte QnA Maker-[Ressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker). Merken Sie sich die Azure Active Directory-ID, das Abonnement und den QnA-Ressourcennamen, die Sie beim Erstellen der Ressource ausgewählt haben.

## <a name="create-your-first-qna-maker-knowledge-base"></a>Erstellen Ihrer ersten QnA Maker-Wissensdatenbank

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

1. Melden Sie sich mit Ihren Azure-Anmeldeinformationen beim Portal [QnAMaker.ai](https://QnAMaker.ai) an.

2. Wählen Sie im QnA Maker-Portal die Option **Wissensdatenbank erstellen** aus.

3. Überspringen Sie **Schritt 1** auf der Seite **Erstellen**, wenn Sie bereits über eine QnA Maker-Ressource verfügen.

    Wenn Sie die Ressource noch nicht erstellt haben, wählen Sie **Create a QnA service** (QnA-Dienst erstellen) aus. Sie werden an das [Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) weitergeleitet, um einen QnA Maker-Dienst in Ihrem Abonnement einzurichten. Merken Sie sich die Azure Active Directory-ID, das Abonnement und den QnA-Ressourcennamen, die Sie beim Erstellen der Ressource ausgewählt haben.

    Wenn Sie die Ressource im Azure-Portal erstellt haben, navigieren Sie zurück zum QnA Maker-Portal, aktualisieren Sie die Browserseite, und fahren Sie mit **Schritt 2** fort.

4. Wählen Sie in **Schritt 2** Ihre Active Directory-Instanz, das Abonnement, den Dienst (Ressource) und die Sprache für alle im Dienst erstellten Wissensdatenbanken aus.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/qnaservice-selection.png" alt-text="Screenshot: Auswählen einer Wissensdatenbank im QnA Maker-Dienst":::

5. Geben Sie in **Schritt 3** Ihrer Wissensdatenbank den Namen **My Sample QnA KB**.

6. Konfigurieren Sie in **Schritt 4** die Einstellungen anhand der folgenden Tabelle:

    |Einstellung|Wert|
    |--|--|
    |**Enable multi-turn extraction from URLs, .pdf or .docx files** (Mehrfachdurchlauf-Extrahierung von URLs, PDF- oder DOCX-Dateien aktivieren)|Aktiviert|
    |**Mehrfachdurchlauf-Standardtext**| Option auswählen|
    |**+ URL hinzufügen**|`https://www.microsoft.com/en-us/software-download/faq`|
    |**Smalltalk**|Wählen Sie **Professional** aus.|

7. Wählen Sie in **Schritt 5** die Option **Wissensdatenbank erstellen** aus.

    Der Extraktionsvorgang nimmt einen Moment in Anspruch, um das Dokument zu lesen sowie Fragen und Antworten zu identifizieren.

    Nachdem die Wissensdatenbank von QnA Maker erfolgreich erstellt wurde, wird die Seite **Wissensdatenbank** geöffnet. Auf dieser Seite können Sie den Inhalt der Wissensdatenbank bearbeiten.

# <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/v2)

1. Melden Sie sich mit Ihren Azure-Anmeldeinformationen beim Portal [QnAMaker.ai](https://QnAMaker.ai) an.

2. Wählen Sie im QnA Maker-Portal die Option **Wissensdatenbank erstellen** aus.

3. Überspringen Sie **Schritt 1** auf der Seite **Erstellen**, wenn Sie bereits über eine QnA Maker-Ressource verfügen.

    Wenn Sie die Ressource noch nicht erstellt haben, wählen Sie **Create a QnA service** (QnA-Dienst erstellen) aus. Sie werden an das [Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) weitergeleitet, um einen QnA Maker-Dienst in Ihrem Abonnement einzurichten. Merken Sie sich die Azure Active Directory-ID, das Abonnement und den QnA-Ressourcennamen, die Sie beim Erstellen der Ressource ausgewählt haben.

    Wenn Sie die Ressource im Azure-Portal erstellt haben, navigieren Sie zurück zum QnA Maker-Portal, aktualisieren Sie die Browserseite, und fahren Sie mit **Schritt 2** fort.

4. Wählen Sie in **Schritt 2** Ihre Active Directory-Instanz, das Abonnement, den Dienst (Ressource) und die Sprache für alle im Dienst erstellten Wissensdatenbanken aus.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/connect-your-knowledge-base.png" alt-text="Screenshot des Auswählens einer im QnA Maker-Dienst verwalteten Wissensdatenbank (Vorschau)":::

5. Wenn Sie in **Schritt 2** die erste Wissensdatenbank für Ihren Dienst erstellen, haben Sie die Möglichkeit, die Spracheinstellung für jede Wissensdatenbank spezifisch festzulegen. Nachdem die Spracheinstellung für die erste Wissensdatenbank definiert wurde, können Sie die Einstellungen für den Dienst später nicht mehr ändern.

6. Geben Sie in  **Schritt 3** Ihrer Wissensdatenbank den Namen  **My Sample QnA KB**. 

7. Konfigurieren Sie in **Schritt 4** die Einstellungen anhand der folgenden Tabelle:

    |Einstellung|Wert|
    |--|--|
    |**Enable multi-turn extraction from URLs, .pdf or .docx files** (Mehrfachdurchlauf-Extrahierung von URLs, PDF- oder DOCX-Dateien aktivieren)|Aktiviert|
    |**Mehrfachdurchlauf-Standardtext**| Option auswählen|
    |**+ Datei hinzufügen**| Handbuch für Surface-Laptops herunterladen: https://download.microsoft.com/download/7/B/1/7B10C82E-F520-4080-8516-5CF0D803EEE0/surface-book-user-guide-EN.pdf 
    |**Smalltalk**|Wählen Sie **Professional** aus.|

8. Wählen Sie in **Schritt 5** die Option **Wissensdatenbank erstellen** aus.

    Der Extraktionsvorgang nimmt einen Moment in Anspruch, um das Dokument zu lesen sowie Fragen und Antworten zu identifizieren.

    Nachdem die Wissensdatenbank von QnA Maker erfolgreich erstellt wurde, wird die Seite **Wissensdatenbank** geöffnet. Auf dieser Seite können Sie den Inhalt der Wissensdatenbank bearbeiten.

---

## <a name="add-a-new-question-and-answer-set"></a>Hinzufügen eines neuen Frage-Antwort-Satzes

1. Wählen Sie im QnA Maker-Portal auf der Seite **Bearbeiten** in der Kontextsymbolleiste die Option **+ Frage- und Antwortpaar hinzufügen** aus.
1. Fügen Sie die folgende Frage hinzu:

    `How many Azure services are used by a knowledge base?`

1. Fügen Sie die mit _Markdown_ formatierte Antwort hinzu:

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png" alt-text="Fügen Sie die Frage als Text und die mit Markdown formatierte Antwort hinzu.":::

    Das Markdownsymbol `*` wird für Aufzählungszeichen verwendet. `\n` wird für eine neue Zeile verwendet.

    Auf der Seite **Bearbeiten** wird der Markdowncode angezeigt. Wenn Sie später das Panel **Testen** verwenden, wird der Markdowncode richtig angezeigt.

## <a name="save-and-train"></a>Speichern und trainieren

Wählen Sie in der oberen rechten Ecke **Speichern und trainieren** aus, um Ihre Änderungen zu speichern und QnA Maker zu trainieren. Änderungen werden nur beibehalten, wenn sie gespeichert werden.

## <a name="test-the-knowledge-base"></a>Testen der Wissensdatenbank

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

1. Wählen Sie rechts oben im QnA Maker-Portal die Option **Testen** aus, um zu testen, ob die von Ihnen vorgenommenen Änderungen übernommen wurden.
2. Geben Sie eine Beispielbenutzerabfrage in das Textfeld ein.

    `I want to know the difference between 32 bit and 64 bit Windows`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/query-dialogue.png" alt-text="Geben Sie eine Beispielbenutzerabfrage in das Textfeld ein.":::

3. Wählen Sie **Untersuchen** aus, um die Antwort ausführlicher zu untersuchen. Das Testfenster wird verwendet, um die Änderungen an der Wissensdatenbank zu testen, bevor die Wissensdatenbank veröffentlicht wird.

4. Wählen Sie erneut **Testen** aus, um das Panel **Testen** zu schließen.

# <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/v2)

1. Wählen Sie rechts oben im QnA Maker-Portal die Option **Testen** aus, um zu testen, ob die von Ihnen vorgenommenen Änderungen übernommen wurden.
2. Geben Sie eine Beispielbenutzerabfrage in das Textfeld ein.

    `whats the size of the touchscreen`

3. Wenn Sie die MRC-Funktion für Ihre Wissensdatenbank aktivieren, indem Sie **Kurze Antwort anzeigen** auswählen, wird auch eine genaue Antwort angezeigt, falls verfügbar, zusammen mit der Antwortpassage im Testbereich. 

    ![Verwalteter aktivierter Testbereich](../media/conversational-context/test-pane-with-managed.png)
    

4. Wählen Sie „Untersuchen“ aus, um die Antwort ausführlicher zu untersuchen. Das Testfenster wird verwendet, um die Änderungen an der Wissensdatenbank zu testen, bevor die Wissensdatenbank veröffentlicht wird. 
5. Wählen Sie erneut **Testen** aus, um das Panel **Testen** zu schließen.
---

## <a name="publish-the-knowledge-base"></a>Veröffentlichen der Knowledge Base

Wenn Sie eine Wissensdatenbank veröffentlichen, werden die Inhalte Ihrer Wissensdatenbank aus dem Index `test` in den Index `prod` in Azure Search verschoben.

![Screenshot: Verschieben der Inhalte Ihrer Wissensdatenbank](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. Wählen Sie im QnA Maker-Portal **Veröffentlichen** aus. Wählen Sie dann zum Bestätigen **Veröffentlichen** auf der Seite aus.

    Die QnA Maker-Dienst wurde nun erfolgreich veröffentlicht. Sie können den Endpunkt in Ihrer Anwendung oder im Botcode verwenden.

    ![Screenshot: Erfolgreiche Veröffentlichung](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Erstellen eines Bots

Nach der Veröffentlichung können Sie auf der Seite **Veröffentlichen** einen Bot erstellen:

* Sie können schnell mehrere Bots erstellen, die alle auf die gleiche Wissensdatenbank verweisen, und dabei verschiedene Regionen oder Tarife für die einzelnen Bots verwenden.
* Wenn Sie nur einen einzelnen Bot für die Wissensdatenbank erstellen möchten, verwenden Sie den Link zum **Anzeigen Ihrer Bots im Azure-Portal**, um eine Liste mit Ihren derzeitigen Bots anzuzeigen.

Wenn Sie Änderungen an der Wissensdatenbank vornehmen und sie erneut veröffentlichen, müssen Sie keine weiteren Schritte für den Bot ausführen. Er ist bereits für die Verwendung mit der Wissensdatenbank konfiguriert und funktioniert auch nach späteren Änderungen. Nach jeder Veröffentlichung einer Wissensdatenbank werden alle mit ihr verbundenen Bots automatisch aktualisiert.

1. Wählen Sie im QnA Maker-Portal auf der Seite **Veröffentlichen** die Option **Bot erstellen** aus. Diese Schaltfläche wird erst angezeigt, wenn die Wissensdatenbank veröffentlicht wurde.

    ![Screenshot zum Erstellen eines Bots](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Für das Azure-Portal wird eine neue Browserregisterkarte mit der Erstellungsseite von Azure Bot Service geöffnet. Konfigurieren Sie Azure Bot Service. Der Bot und QnA Maker können den gleichen Web-App-Serviceplan, aber nicht die gleiche Web-App verwenden. Der **App-Name** für den Bot muss sich daher von dem App-Namen für den QnA Maker-Dienst unterscheiden.

    * **Empfohlene Vorgehensweise**
        * Ändern Sie das Bot-Handle, wenn es nicht eindeutig ist.
        * Wählen Sie die SDK-Sprache aus. Nachdem der Bot erstellt wurde, können Sie den Code in die lokale Entwicklungsumgebung herunterladen und den Entwicklungsprozess fortsetzen.
    * **Nicht empfohlene Vorgehensweise**
        * Ändern Sie die folgenden Einstellungen im Azure-Portal beim Erstellen des Bots nicht. Sie wurden automatisch für Ihre vorhandene Wissensdatenbank aufgefüllt:
           * QnA-Authentifizierungsschlüssel
           * App Service-Plan und Standort


1. Öffnen Sie nach der Erstellung des Bots die **Bot Service**-Ressource.
1. Wählen Sie unter **Botverwaltung** die Option **Testen im Webchat** aus.
1. Geben Sie bei der Chataufforderung **Nachricht eingeben** Folgendes ein:

    `Azure services?`

    Der Chatbot antwortet mit einer Antwort aus Ihrer Wissensdatenbank.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-web-chat.png" alt-text="Geben Sie eine Benutzerabfrage in den Testwebchat ein.":::

## <a name="what-did-you-accomplish"></a>Was haben Sie erreicht?

Sie haben eine neue Wissensdatenbank erstellt, der Wissensdatenbank eine öffentliche URL hinzugefügt, Ihr eigenes QnA-Paar hinzugefügt, trainiert, getestet und die Wissensdatenbank veröffentlicht.

Nachdem Sie die Wissensdatenbank veröffentlicht haben, haben Sie einen Bot erstellt und getestet.

Dies haben Sie innerhalb weniger Minuten erreicht, ohne Code schreiben oder den Inhalt bereinigen zu müssen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie nicht mit der nächsten Schnellstartanleitung fortfahren, löschen Sie die QnA Maker- und Bot Framework-Ressourcen im Azure-Portal.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hinzufügen von Fragen mit Metadaten](add-question-metadata-portal.md)

Weitere Informationen finden Sie unter:

* [Markdownformat in Antworten](../reference-markdown-format.md)
* [Datenquellen](../Concepts/data-sources-and-content.md) für QnA Maker-Inhalt
