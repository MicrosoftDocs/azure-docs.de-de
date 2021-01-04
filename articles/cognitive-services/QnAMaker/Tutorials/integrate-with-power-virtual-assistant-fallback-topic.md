---
title: 'Tutorial: Integrieren in Power Virtual Agents: QnA Maker'
description: In diesem Tutorial nutzen Sie aktives Lernen, um die Qualität Ihrer Wissensdatenbank zu verbessern. Sie können Einträge überprüfen, annehmen/ablehnen oder hinzufügen, ohne vorhandene Fragen zu entfernen oder zu ändern.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 11/09/2020
ms.openlocfilehash: 3801bb44fed6bf24788957c41de77c89b3025ae8
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351111"
---
# <a name="tutorial-add-your-knowledge-base-to-power-virtual-agents"></a>Tutorial: Hinzufügen Ihrer Wissensdatenbank zu Power Virtual Agents
Erstellen und erweitern Sie einen [Power Virtual Agents](https://powervirtualagents.microsoft.com/)-Bot, um Antworten aus Ihrer Wissensdatenbank bereitzustellen.

In diesem Tutorial lernen Sie Folgendes:

<!-- green checkmark -->
> [!div class="checklist"]
> * Erstellen eines Power Virtual Agents-Bots
> * Erstellen eines Systemfallbackthemas
> * Hinzufügen von QnA Maker als Aktion für ein Thema als Power Automate-Flow
> * Erstellen einer Power Automate-Lösung
> * Hinzufügen eines Power Automate-Flows zu Ihrer Lösung
> * Veröffentlichen von Power Virtual Agents
> * Testen von Power Virtual Agents und Empfangen einer Antwort von Ihrer QnA Maker-Wissensdatenbank

## <a name="integrate-an-agent-with-a-knowledge-base"></a>Integrieren eines Agents in eine Wissensdatenbank

Mithilfe von [Power Virtual Agents](https://powervirtualagents.microsoft.com/) können Teams über eine intuitive grafische Benutzeroberfläche leistungsstarke Bots ohne Programmieraufwand erstellen. Sie benötigen keine Data Scientists oder Entwickler.

In Power Virtual Agents wird ein Agent mit einer Reihe von Themenbereichen erstellt, um Benutzerfragen durch Ausführen von Aktionen zu beantworten. Wenn keine Antwort gefunden wird, kann durch ein Systemfallback eine Antwort zurückgegeben werden.

Konfigurieren Sie den Agent so, dass die Frage als Teil einer Themenaktion oder als Teil des Themenpfads *Systemfallback* an Ihre Wissensdatenbank gesendet wird. In beiden Fällen wird eine Aktion verwendet, um eine Verbindung mit Ihrer Wissensdatenbank herzustellen und eine Antwort zurückzugeben.

## <a name="power-automate-connects-to-generateanswer-action"></a>Power Automate-Verbindung mit der Aktion `GenerateAnswer`

Erstellen Sie die Aktion mithilfe von Power Automate, um Ihren Agent mit Ihrer Wissensdatenbank zu verbinden. Power Automate bietet einen Prozessflow, der eine Verbindung mit der QnA Maker-API `GenerateAnswer` herstellt.

Nachdem Sie den Flow entworfen und gespeichert haben, steht er in einer Power Automate-Lösung zur Verfügung. Verwenden Sie diese Lösung als Aktion in Ihrem Agent.

## <a name="connect-an-agent-to-your-knowledge-base"></a>Verbinden eines Agents mit Ihrer Wissensdatenbank

Im Anschluss finden Sie eine Übersicht über die Schritte, mit denen ein Agent in Power Virtual Agents mit einer Wissensdatenbank in QnA Maker verbunden wird.

* Vorgehensweise im [QnA Maker](https://www.qnamaker.ai/)-Portal:
    * Erstellen und veröffentlichen Sie Ihre Wissensdatenbank.
    * Kopieren Sie die Details Ihrer Wissensdatenbank, einschließlich der ID sowie des Endpunktschlüssels und -hosts für die Laufzeit.
* Vorgehensweise im [Power Virtual Agents](https://powerva.microsoft.com/)-Portal:
    * Erstellen Sie ein Agent-Thema.
    * Rufen Sie eine Aktion (für den Power Automate-Flow) auf.
* Vorgehensweise im [Power Automate](https://us.flow.microsoft.com/)-Portal:
    * Suchen Sie die Vorlage _Generate answer using QnA Maker_.
    * Konfigurieren Sie mithilfe der Vorlage den Flow für das Verwenden von [ GenerateAnswer von QnA Maker](/connectors/cognitiveservicesqnamaker/).
        * Informationen der veröffentlichten QnA Maker-Wissensdatenbank:
            * Wissensdatenbank-ID
            * Endpunkthost der QnA Maker-Ressource
            * Endpunktschlüssel der QnA Maker-Ressource
        * Eingabe – Benutzerabfrage
        * Ausgabe – Antwort aus der Wissensdatenbank
    * Erstellen Sie eine Lösung, und fügen Sie den Flow hinzu, oder fügen Sie den Flow zu einer vorhandenen Lösung hinzu.
* Kehren Sie zu Power Virtual Agents zurück:
    * Wählen Sie die Ausgabe der Lösung als Nachricht für ein Thema aus.

## <a name="create-and-publish-a-knowledge-base"></a>Erstellen und Veröffentlichen einer Wissensdatenbank

1. Befolgen Sie die [Schnellstartanleitung](../Quickstarts/create-publish-knowledge-base.md), um eine Wissensdatenbank zu erstellen. Schließen Sie den letzten Abschnitt zur Boterstellung nicht ab. Erstellen Sie stattdessen anhand dieses Tutorials einen Bot mit Power Virtual Agents.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Einstellungen der veröffentlichen Wissensdatenbank](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Geben Sie die Einstellungen Ihrer veröffentlichten Wissensdatenbank ein. Diese Einstellungen finden Sie im [QnA Maker](https://www.qnamaker.ai/)-Portal auf der Seite **Einstellungen**. Die Angaben werden für den [Power Automate-Schritt](#create-a-power-automate-flow-to-connect-to-your-knowledge-base) benötigt, um Ihre QnA Maker-Verbindung vom Typ `GenerateAnswer` zu konfigurieren.

1. Ermitteln Sie im QnA Maker-Portal auf der Seite **Einstellungen** den Endpunktschlüssel, den Endpunkthost und die Wissensdatenbank-ID.

## <a name="create-an-agent-in-power-virtual-agents"></a>Erstellen eines Agents in Power Virtual Agents

1. [Melden Sie sich bei Power Virtual Agents an.](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409) Verwenden Sie die E-Mail-Adresse Ihres Geschäfts-, Schul- oder Unikontos.

1. Wenn dies Ihr erster Bot ist, wird die **Startseite** des Agents angezeigt. Sollte dies nicht Ihr erster Bot sein, wählen Sie rechts oben auf der Seite den Bot und anschließend **+ New Bot** (+ Neuer Bot) aus.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Startseite von Power Virtual Agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

## <a name="topics-provided-in-the-bot"></a>Im Bot bereitgestellte Themen

Der Agent verwendet die Themensammlung, um Fragen aus Ihrem Themenbereich zu beantworten. In diesem Tutorial stellt der Agent eine Vielzahl von Themen bereit, die in Benutzerthemen und Systemthemen unterteilt sind.

Wählen Sie im linken Navigationsbereich **Themen** aus, um die vom Bot bereitgestellten Themen anzuzeigen.

> [!div class="mx-imgBorder"]
> ![Screenshot: Im Agent bereitgestellte Themen](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-the-system-fallback-topic"></a>Erstellen des Systemfallbackthemas

Der Agent kann zwar von jedem Thema aus eine Verbindung mit Ihrer Wissensdatenbank herstellen, in diesem Tutorial wird jedoch das *Systemfallbackthema* verwendet. Das Fallbackthema wird verwendet, wenn der Agent keine Antwort auf eine Frage findet. Der Agent übergibt den Text des Benutzers an die `GenerateAnswer`-API von QnA Maker, empfängt die Antwort aus Ihrer Wissensdatenbank und zeigt sie dem Benutzer als Nachricht an.

1. Wählen Sie rechts oben im [Power Virtual Agents](https://powerva.microsoft.com/#/)-Portal die Option **Einstellungen** (Zahnradsymbol) aus. Wählen Sie anschließend **System Fallback** (Systemfallback) aus.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Power Virtual Agents-Menüelement für Systemfallback](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. Wählen Sie **+ Hinzufügen** aus, um ein Systemfallbackthema hinzuzufügen.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Hinzufügen eines Fallbackthemas](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. Wählen Sie nach dem Hinzufügen des Themas die Option **Go to Fallback topic** (Zum Fallbackthema) aus, um das Fallbackthema im Erstellungsbereich zu erstellen.

    > [!TIP]
    > Wenn Sie später noch einmal zum Fallbackthema zurückkehren möchten, finden Sie es im Abschnitt **Themen** bei den **Systemthemen**.

## <a name="use-the-authoring-canvas-to-add-an-action"></a>Hinzufügen einer Aktion mithilfe des Erstellungsbereichs

Verbinden Sie das Fallbackthema im Power Virtual Agents-Erstellungsbereich mit Ihrer Wissensdatenbank. Das Thema beginnt mit dem unbekannten Benutzertext. Fügen Sie eine Aktion hinzu, die diesen Text an QnA Maker übergibt und anschließend die Antwort als Nachricht anzeigt. Der letzte Schritt beim Anzeigen einer Antwort wird später in diesem Tutorial als [separater Schritt](#add-your-solutions-flow-to-power-virtual-agents) behandelt.

In diesem Abschnitt wird der Konversationsablauf für das Fallbackthema erstellt.

1. Die neue Fallbackaktion verfügt möglicherweise bereits über Konversationsablaufelemente. Löschen Sie das Element **Eskalieren** über das Menü **Optionen**.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/delete-escalate-action-using-option-menu.png" alt-text="Teilscreenshot des Konversationsflows, mit hervorgehobener Option „Löschen“.":::

1. Wählen Sie das vom Feld **Nachricht** ausgehende Verbindungselement **+** und anschließend **Aktion aufrufen** aus.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png" alt-text="Teilscreenshot von „Aktion aufrufen“.":::

1. Wählen Sie **Flow erstellen** aus. Der Prozess führt Sie zum Power Automate-Portal.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: „Flow erstellen“](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)


    Power Automate öffnet eine neue Vorlage. Diese neue Vorlage wird nicht verwendet.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-automate-flow-initial-template.png" alt-text="Teilscreenshot von Power Automate mit neuer Flowvorlage.":::

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>Erstellen eines Power Automate-Flows für die Verbindung mit Ihrer Wissensdatenbank

> [!NOTE]
> Derzeit unterstützt die Power Automate-Vorlage keine von QnA Maker verwalteten Endpunkte (Vorschau). Um eine vom QnA Maker verwaltete (Vorschau) Wissensdatenbank zu Power Automate hinzuzufügen, überspringen Sie diesen Schritt, und fügen Sie die Endpunkte manuell hinzu. 

Mit dem folgenden Verfahren wird ein Power Automate-Flow für folgende Aufgaben erstellt:
* Empfangen des eingehenden Benutzertexts und Senden des Texts an QnA Maker
* Gibt die häufigste Antwort an den Agent zurück.

1. Wählen Sie in **Power Automate** im linken Navigationsbereich **Vorlagen** aus. Wenn Sie gefragt werden, ob Sie die Browserseite verlassen möchten, übernehmen Sie „Verlassen“.

1. Suchen Sie auf der Seite „Vorlagen“ nach der Vorlage **Generate answer using QnA Maker**, und wählen Sie dann die Vorlage aus. Diese Vorlage enthält alle Schritte, um QnA Maker mit Ihren Wissensdatenbankeinstellungen festzulegen und die häufigste Antwort zurückzugeben.

1. Wählen Sie auf dem neuen Bildschirm für den QnA Maker-Flow **Weiter** aus.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-automate-qna-flow-template-continue.png" alt-text="Teilscreenshot des Flows der QnA Maker-Vorlage mit hervorgehobener Schaltfläche „Weiter“.":::

1. Wählen Sie das Aktionsfeld **Antwort generieren** aus, und geben Sie Ihre QnA Maker-Einstellungen aus einem vorherigen Abschnitt mit dem Titel [Erstellen und Veröffentlichen einer Wissensdatenbank](#create-and-publish-a-knowledge-base) ein. Ihr **Diensthost** in der folgenden Abbildung verweist im Format `https://YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker` auf den **Host** Ihrer Wissensdatenbank.


    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fill-in-generate-answer-settings.png" alt-text="Teilscreenshot des Flows der QnA Maker-Vorlage mit hervorgehobenem „Antwort generieren (Vorschau)“.":::

1. Wählen Sie **Speichern** aus, um den Flow zu speichern.

## <a name="create-a-solution-and-add-the-flow"></a>Erstellen einer Lösung und Hinzufügen des Flows

Damit der Agent den Flow finden und eine Verbindung damit herstellen kann, muss der Flow in einer Power Automate-Lösung enthalten sein.

1. Wählen Sie im linken Navigationsbereich des Power Automate-Portals **Lösungen** aus.

1. Wählen Sie **+ Neue Lösung** aus.

1. Geben Sie einen Anzeigenamen ein. Die Liste der Lösungen umfasst alle Lösungen in Ihrer Organisation oder Schule/Hochschule. Verwenden Sie eine Namenskonvention, die das Filtern nach Ihren Lösungen erleichtert. Eine Möglichkeit wäre beispielsweise, dem Namen Ihrer Lösung Ihre E-Mail-Adresse voranzustellen: `jondoe-power-virtual-agent-qnamaker-fallback`.

1. Wählen Sie in der Liste der Optionen Ihren Herausgeber aus.

1. Übernehmen Sie die Standardwerte für den Namen und die Version.

1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.

## <a name="add-your-flow-to-the-solution"></a>Hinzufügen Ihres Flows zur Lösung

1. Wählen Sie in der Liste der Lösungen die Lösung aus, die Sie soeben erstellt haben. Sie sollte am Anfang der Liste aufgeführt werden. Ist dies nicht der Fall, suchen Sie nach Ihrem E-Mail-Namen, der Teil Ihres Lösungsnamens ist.

1. Wählen Sie in der Lösung **+ Vorhandene hinzufügen** und dann **Flow** aus der Liste aus.

1. Suchen Sie in der Liste **Externe Lösungen** nach Ihrem Flow, und wählen Sie **Hinzufügen** aus, um den Vorgang abzuschließen. Wenn eine Vielzahl von Flows vorhanden sind, ermitteln Sie den neuesten Flow anhand der Spalte **Geändert**.

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>Hinzufügen des Flows Ihrer Lösung zu Power Virtual Agents

1. Kehren Sie zum Browsertab mit Ihrem Agent in Power Virtual Agents zurück. Der Erstellungsbereich sollte noch geöffnet sein.

1. Wählen Sie das Verbindungselement **+** oberhalb des Aktionsfelds **Nachricht** aus, um einen neuen Schritt in den Flow einzufügen. Wählen Sie dann **Aktion aufrufen** aus.

1. Wählen Sie im Popupfenster **Flow** den neuen Flow namens **Antworten mithilfe der QnA Maker-Wissensdatenbank generieren...** aus. Die neue Aktion wird im Flow angezeigt.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-flow-after-adding-action.png" alt-text="Teilscreenshot des Konversationserstellungsbereichs des Power Virtual Agent-Themas nach Hinzufügen des QnA Maker-Flows.":::

1. Um die Eingabevariable ordnungsgemäß auf die QnA Maker-Aktion festzulegen, wählen Sie **Variable auswählen** und dann **bot.unrecognizedTriggerPhrase** aus.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-selection-action-input.png" alt-text="Teilscreenshot des Konversationserstellungsbereichs des Power Virtual Agent-Themas mit Auswählen der Eingabevariablen.":::


1. Um die Ausgabevariable ordnungsgemäß auf die QnA Maker-Aktion festzulegen, wählen Sie in der Aktion **Nachricht** die Option **UnrecognizedTriggerPhrase**, dann das Symbol zum Einfügen einer Variablen, `{x}`, und schließlich **FinalAnswer** aus.

1. Wählen Sie auf der Kontextsymbolleiste **Speichern** aus, um die Details des Erfassungsbereichs für das Thema zu speichern.

Der fertige Agent-Erstellungsbereich sollte wie folgt aussehen:

> [!div class="mx-imgBorder"]
> ![Der Screenshot zeigt den endgültigen Agent-Erstellungsbereich mit den Abschnitten „Triggerbegriffe“, „Aktion“ und „Benachrichtigung“.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>Testen des Agents

1. Aktivieren Sie im Testbereich die Option **Nachverfolgung für Themen**. Mit dieser Option können Sie den Fortschritt zwischen Themen sowie innerhalb eines einzelnen Themas nachverfolgen.

1. Testen Sie den Agent, indem Sie den Benutzertext in der folgenden Reihenfolge eingeben. Im Erstellungsbereich werden die erfolgreichen Schritte mit einem grünen Häkchen gezeigt.

    |Reihenfolge der Fragen|Testfragen|Zweck|
    |--|--|--|
    |1|Hallo|Unterhaltung beginnen|
    |2|Geschäftszeiten|Beispielthema. Ein für Sie vorkonfiguriertes Thema.|
    |3|Ja|Als Antwort auf `Did that answer your question?`|
    |4|Hervorragend|Als Antwort auf `Please rate your experience.`|
    |5|Ja|Als Antwort auf `Can I help with anything else?`|
    |6|Wie kann ich die Durchsatzleistung für Abfragevorhersagen verbessern?|Diese Frage löst die Fallbackaktion aus, die den Text an Ihre Wissensdatenbank sendet. Anschließend wird die Antwort angezeigt. Die grünen Häkchen bei den einzelnen Aktionen zeigen den Erfolg der jeweiligen Aktion an.|

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png" alt-text="Screenshot des Chatbots mit Zeichenbereich und grünen Häkchen für erfolgreiche Aktionen.":::

## <a name="publish-your-bot"></a>Veröffentlichen Ihres Bots

Damit der Agent für alle Mitglieder Ihrer Schule/Hochschule oder Organisation verfügbar ist, müssen Sie ihn veröffentlichen.

1. Wählen Sie im Navigationsbereich auf der linken Seite die Option **Veröffentlichen** aus. Wählen Sie anschließend auf der Seite die Option **Veröffentlichen** aus.

1. Testen Sie Ihren Bot auf der Demowebsite. (Den Link finden Sie unter **Veröffentlichen**.)

    Es wird eine neue Webseite mit Ihrem Bot geöffnet. Stellen Sie dem Bot dieselbe Testfrage: `How can I improve the throughput performance for query predictions?`

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Fertiger Agent-Erstellungsbereich](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Freigeben Ihres Bots

Konfigurieren Sie die Demowebsite als Kanal, um sie freizugeben.

1. Wählen Sie im Navigationsbereich auf der linken Seite **Verwalten** > **Kanäle** aus.

1. Wählen Sie aus der Liste der Kanäle **Demowebsite** aus.

1. Kopieren Sie den Link, und wählen Sie **Speichern** aus. Fügen Sie den Link zu Ihrer Demowebsite in eine E-Mail an die Mitglieder Ihrer Schule/Hochschule oder Organisation ein.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie mit der Wissensdatenbank fertig sind, entfernen Sie die QnA Maker-Ressourcen im Azure-Portal.

## <a name="next-step"></a>Nächster Schritt

[Abrufen von Analysen zu Ihrer Wissensdatenbank](../How-To/get-analytics-knowledge-base.md)

Weitere Informationen:
* [Power Virtual Agents](/power-virtual-agents/)
* [Power Automate](/power-automate/)
* [QnA Maker-Connector](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) und [Einstellungen für den Connector](/connectors/cognitiveservicesqnamaker/)