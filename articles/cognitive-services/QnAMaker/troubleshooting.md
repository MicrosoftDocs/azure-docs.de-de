---
title: 'Problembehandlung: QnA Maker'
description: Die kuratierte Liste der am häufigsten gestellten Fragen in Bezug auf den QnA Maker-Dienst ermöglichen einen schnelleren Einstieg in die Nutzung des Diensts und bessere Ergebnisse.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: troubleshooting
ms.date: 11/09/2020
ms.openlocfilehash: e8b1d985fcb2852df52382e005ec0f0266e23d9d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96345643"
---
# <a name="troubleshooting-for-qna-maker"></a>Problembehandlung für QnA Maker

Die kuratierte Liste der am häufigsten gestellten Fragen in Bezug auf den QnA Maker-Dienst ermöglichen einen schnelleren Einstieg in die Nutzung des Diensts und bessere Ergebnisse.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>Verwalten von Vorhersagen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

<details>
<summary><b>Wie kann ich die Durchsatzleistung für Abfragevorhersagen verbessern?</b></summary>

**Antwort:** Probleme bei der Durchsatzleistung weisen darauf hin, dass Sie sowohl für Ihren App-Dienst als auch für die Cognitive Search-Instsanz zentral hochskalieren müssen. Fügen Sie Ihrer Cognitive Search-Instanz ggf. ein Replikat hinzu, um die Leistung zu verbessern.

[Hier](Concepts/azure-resources.md) finden Sie weitere Informationen zu Tarifen.
</details>

<details>
<summary><b>Abrufen des QnA Maker-Dienstendpunkts</b></summary>

**Antwort:** Der QnA Maker-Dienstendpunkt ist beim Debuggen hilfreich, wenn Sie sich an den QnA Maker-Support oder UserVoice wenden. Der Endpunkt ist eine URL mit folgendem Format: `https://your-resource-name.azurewebsites.net`.

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem QnA Maker-Dienst (Ressourcengruppe).

    ![QnA Maker-Ressourcengruppe im Azure-Portal](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Wählen Sie die der QnA Maker-Ressource zugeordnete App Service-Instanz aus. Normalerweise sind die Namen identisch.

     ![Auswählen von QnA Maker App Service](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Sie finden die Endpunkt-URL im Abschnitt „Übersicht“.

    ![QnA Maker-Endpunkt](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

<details>
<summary><b>Wie kann ich die Durchsatzleistung für Abfragevorhersagen verbessern?</b></summary>

**Antwort:** Probleme bei der Durchsatzleistung weisen darauf hin, dass Sie Ihre Cognitive Search-Instanz hochskalieren müssen. Fügen Sie Ihrer Cognitive Search-Instanz ggf. ein Replikat hinzu, um die Leistung zu verbessern.

[Hier](Concepts/azure-resources.md) finden Sie weitere Informationen zu Tarifen.
</details>

---

## <a name="manage-the-knowledge-base"></a>Verwalten der Wissensdatenbank

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

<details>
<summary><b>Ich habe versehentlich einen Teil von QnA Maker gelöscht. Was soll ich tun?</b></summary>

**Antwort:** Löschen Sie keine der Azure-Dienste, die zusammen mit der QnA Maker-Ressource erstellt wurden, wie Search oder Web-App. Diese sind erforderlich, damit QnA Maker funktioniert. Wenn Sie einen dieser Dienste löschen, funktioniert QnA Maker nicht mehr richtig.

Alle Löschvorgänge sind endgültig, dazu gehört auch das Löschen von Frage/Antwort-Paaren, Dateien, URLs, benutzerdefinierten Fragen und Antworten, Wissensdatenbanken oder Azure-Ressourcen. Stellen Sie sicher, dass Sie Ihre Wissensdatenbank von der Seite **Einstellungen** exportieren, bevor Sie Teile der Wissensdatenbank löschen.

</details>

<details>
<summary><b>Warum extrahieren meine URLs/Dateien keine Frage-Antwort-Paare?</b></summary>

**Antwort:** Es ist möglich, dass QnA Maker einige Frage-und-Antwort-Inhalte (Question and Answer, QnA) aus gültigen FAQ-URLs nicht automatisch extrahieren kann. In solchen Fällen können Sie den QnA-Inhalt in eine TXT-Datei einfügen und prüfen, ob das Tool diesen Inhalt erfassen kann. Alternativ dazu können Sie Ihrer Wissensdatenbank über das [QnA Maker-Portal](https://qnamaker.ai) redaktionell Inhalte hinzufügen.

</details>

<details>
<summary><b>Wie groß darf eine von mir erstellte Wissensdatenbank sein?</b></summary>

**Antwort:** Die Größe der Wissensdatenbank hängt von der beim Erstellen des QnA Maker-Diensts ausgewählten SKU für Azure Search ab. Ausführlichere Informationen finden Sie [hier](./concepts/azure-resources.md).

</details>

<details>
<summary><b>Warum werden in der Dropdownliste keine Einträge angezeigt, wenn ich versuche, eine neue Wissensdatenbank zu erstellen?</b></summary>

**Antwort:** Sie haben noch keine QnA Maker-Dienste in Azure erstellt. Klicken Sie [hier](./How-To/set-up-qnamaker-service-azure.md), um zu erfahren, wie das funktioniert.

</details>

<details>
<summary><b>Wie gebe ich eine Wissensdatenbank für andere Personen frei?</b></summary>

**Antwort:** Die Freigabe erfolgt auf der Ebene eines QnA Maker-Diensts, was bedeutet, dass alle Wissensdatenbanken in diesem Dienst freigegeben werden. [Hier](./index.yml) finden Sie Informationen zum Zusammenarbeiten an einer Wissensdatenbank.

</details>

<details>
<summary><b>Kann ich eine Wissensdatenbank zur Bearbeitung für einen Mitwirkenden freigeben, der sich nicht im selben AAD-Mandanten befindet?</b></summary>

**Antwort:** Die Freigabe basiert auf der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC). Wenn Sie _jede_ Ressource in Azure für einen anderen Benutzer freigeben können, können Sie auch QnA Maker freigeben.

</details>

<details>
<summary><b>Angenommen, ich verfüge über einen App Service-Plan mit fünf QnA Maker-Wissensdatenbanken. Kann ich die Lese-/Schreibberechtigungen fünf verschiedenen Benutzern zuweisen, sodass jeder dieser Benutzer nur auf eine QnA Maker-Wissensdatenbank zugreifen kann?</b></summary>

**Antwort:** Sie können den gesamten QnA Maker-Dienst freigeben, nicht aber einzelne Wissensdatenbanken.

</details>

<details>
<summary><b>Wie kann ich die Standardmeldung ändern, die angezeigt wird, wenn keine gute Übereinstimmung gefunden wurde??</b></summary>

**Antwort:** Die Standardmeldung ist Teil der Einstellungen in Ihrem App-Dienst.
- Wechseln Sie im Azure-Portal zu Ihrer App Service-Ressource.

![QnA Maker-App-Dienst](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Klicken Sie auf die Option **Einstellungen**.

![Einstellungen des QnA Maker-App-Diensts](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Ändern Sie den Wert der Einstellung **DefaultAnswer**.
- Starten Sie Ihren App-Dienst neu.

![QnA Maker-App-Dienst neu starten](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>Warum wird mein SharePoint-Link nicht extrahiert?</b></summary>

**Antwort:** Weitere Informationen finden Sie unter [Speicherorte von Datenquellen](./concepts/data-sources-and-content.md#data-source-locations).

</details>

<details>
<summary><b>Die Aktualisierungen, die ich an meiner Wissensdatenbank vorgenommen habe, sind beim Veröffentlichen nicht mehr vorhanden. Warum nicht?</b></summary>

**Antwort:** Jeder Bearbeitungsvorgang, sei es eine Tabellenaktualisierung, ein Test oder eine Änderung der Einstellungen, muss gespeichert werden, bevor die Veröffentlichung erfolgen kann. Klicken Sie nach jedem Bearbeitungsvorgang unbedingt auf die Schaltfläche **Speichern und Trainieren**.

</details>

<details>
<summary><b>Unterstützt die Wissensdatenbank komplexe Daten oder Multimedia?</b></summary>

**Antwort:**

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Automatische Multimediaextraktion für Dateien und URLs

* URLs: eingeschränkte Konvertierungsfunktionen von HTML zu Markdown
* Dateien: nicht unterstützt

#### <a name="answer-text-in-markdown"></a>Antworttext in Markdown
Wenn die QnA-Paare in der Wissensdatenbank hinzugefügt wurden, können Sie den Markdowntext einer Antwort bearbeiten, um Links zu Medien einzufügen, die über öffentliche URLs verfügbar sind.


</details>

<details>
<summary><b>Unterstützt QnA Maker andere Sprachen als Englisch?</b></summary>

**Antwort:** Weitere Informationen hierzu finden Sie unter [Unterstützte Sprachen](./overview/language-support.md).

Wenn Sie über Inhalte in verschiedenen Sprachen verfügen, stellen Sie sicher, dass Sie für jede Sprache einen separaten Dienst erstellen.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

<details>
<summary><b>Warum extrahieren meine URLs/Dateien keine Frage-Antwort-Paare?</b></summary>

**Antwort:** Es ist möglich, dass QnA Maker einige Frage-und-Antwort-Inhalte (Question and Answer, QnA) aus gültigen FAQ-URLs nicht automatisch extrahieren kann. In solchen Fällen können Sie den QnA-Inhalt in eine TXT-Datei einfügen und prüfen, ob das Tool diesen Inhalt erfassen kann. Alternativ dazu können Sie Ihrer Wissensdatenbank über das [QnA Maker-Portal](https://qnamaker.ai) redaktionell Inhalte hinzufügen.

</details>

<details>
<summary><b>Wie groß darf eine von mir erstellte Wissensdatenbank sein?</b></summary>

**Antwort:** Die Größe der Wissensdatenbank hängt von der beim Erstellen des QnA Maker-Diensts ausgewählten SKU für Azure Search ab. Ausführlichere Informationen finden Sie [hier](./concepts/azure-resources.md).

</details>

<details>
<summary><b>Warum werden in der Dropdownliste keine Einträge angezeigt, wenn ich versuche, eine neue Wissensdatenbank zu erstellen?</b></summary>

**Antwort:** Sie haben noch keine QnA Maker-Dienste in Azure erstellt. Klicken Sie [hier](./How-To/set-up-qnamaker-service-azure.md), um zu erfahren, wie das funktioniert.

</details>

<details>
<summary><b>Wie gebe ich eine Wissensdatenbank für andere Personen frei?</b></summary>

**Antwort:** Die Freigabe erfolgt auf der Ebene eines QnA Maker-Diensts, was bedeutet, dass alle Wissensdatenbanken in diesem Dienst freigegeben werden. [Hier](./index.yml) finden Sie Informationen zum Zusammenarbeiten an einer Wissensdatenbank.

</details>

<details>
<summary><b>Kann ich eine Wissensdatenbank zur Bearbeitung für einen Mitwirkenden freigeben, der sich nicht im selben Azure Active Directory-Mandanten befindet?</b></summary>

**Antwort:** Die Freigabe basiert auf der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC). Wenn Sie _jede_ Ressource in Azure für einen anderen Benutzer freigeben können, können Sie auch QnA Maker freigeben.

</details>

<details>
<summary><b>Kann ich die Lese-/Schreibberechtigungen fünf verschiedenen Benutzern zuweisen, sodass jeder dieser Benutzer nur auf eine QnA Maker-Wissensdatenbank zugreifen kann?</b></summary>

**Antwort:** Sie können den gesamten QnA Maker-Dienst freigeben, nicht aber einzelne Wissensdatenbanken.

</details>

<details>
<summary><b>Warum wird mein SharePoint-Link nicht extrahiert?</b></summary>

**Antwort:** Weitere Informationen finden Sie unter [Speicherorte von Datenquellen](./concepts/data-sources-and-content.md#data-source-locations).

</details>

<details>
<summary><b>Die Aktualisierungen, die ich an meiner Wissensdatenbank vorgenommen habe, sind beim Veröffentlichen nicht mehr vorhanden. Warum nicht?</b></summary>

**Antwort:** Jeder Bearbeitungsvorgang, sei es eine Tabellenaktualisierung, ein Test oder eine Änderung der Einstellungen, muss gespeichert werden, bevor die Veröffentlichung erfolgen kann. Klicken Sie nach jedem Bearbeitungsvorgang unbedingt auf die Schaltfläche **Speichern und Trainieren**.

</details>

<details>
<summary><b>Unterstützt die Wissensdatenbank komplexe Daten oder Multimedia?</b></summary>

**Antwort:**

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Automatische Multimediaextraktion für Dateien und URLs

* URLs: eingeschränkte Konvertierungsfunktionen von HTML zu Markdown
* Dateien: nicht unterstützt

#### <a name="answer-text-in-markdown"></a>Antworttext in Markdown
Wenn die QnA-Paare in der Wissensdatenbank hinzugefügt wurden, können Sie den Markdowntext einer Antwort bearbeiten, um Links zu Medien einzufügen, die über öffentliche URLs verfügbar sind.


</details>

<details>
<summary><b>Unterstützt QnA Maker andere Sprachen als Englisch?</b></summary>

**Antwort:** Weitere Informationen hierzu finden Sie unter [Unterstützte Sprachen](./overview/language-support.md).

Wenn Sie über Inhalte in verschiedenen Sprachen verfügen, stellen Sie sicher, dass Sie für jede Sprache einen separaten Dienst erstellen.

</details>

---

## <a name="manage-service"></a>Verwalten eines Diensts

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

<details>
<summary><b>Wann sollte ich meinen App-Dienst neu starten?</b></summary>

**Antwort:** Aktualisieren Sie Ihren App-Dienst, wenn sich das Symbol „Vorsicht“ neben dem Versionswert für die Wissensdatenbank in der Tabelle **Endpunktschlüssel** auf der [Seite](https://www.qnamaker.ai/UserSettings) **Benutzereinstellungen** befindet.

</details>

<details>
<summary><b>Ich habe meinen Suchdienst gelöscht. Wie kann ich dieses Problem beheben?</b></summary>

**Antwort:** Wenn Sie einen Azure Cognitive Search-Index löschen, ist der Vorgang endgültig, weshalb der Index nicht wiederhergestellt werden kann.

</details>

<details>
<summary><b>Ich habe den Index `testkb` in meinem Suchdienst gelöscht. Wie kann ich dieses Problem beheben?</b></summary>

**Antwort:** Ihre alten Daten können nicht wiederhergestellt werden. Erstellen Sie eine neue QnA Maker-Ressource, und erstellen Sie Ihre Wissensdatenbank erneut.

</details>

<details>
<summary><b>Wann sollte ich meine Endpunktschlüssel aktualisieren?</b></summary>

**Antwort:** Aktualisieren Sie Ihre Endpunktschlüssel, wenn Sie den Verdacht hegen, dass sie gestohlen oder ausgespäht wurden.

</details>

<details>
<summary><b>Kann ich die gleiche Azure Cognitive Search-Ressource für Wissensdatenbanken in mehreren Sprachen verwenden?</b></summary>

**Antwort:** Um mehrere Wissensdatenbanken und mehrere Sprachen zu nutzen, müssen Benutzer eine QnA Maker-Ressource für jede Sprache erstellen. Dadurch wird ein separater Azure Search-Dienst pro Sprache erstellt. Das Kombinieren von Wissensdatenbanken verschiedener Sprachen in einem einzelnen Azure Search-Dienst führt zur einer niedrigeren Relevanz der Ergebnisse.

</details>

<details>
<summary><b>Wie kann ich den Namen der Azure Cognitive Search-Ressource, die von QnA Maker verwendet wird, ändern?</b></summary>

**Antwort:** Der Name der Azure Cognitive Search-Ressource ist der Name der QnA Maker-Ressource, an den einige zufällige Buchstaben angefügt sind. Dadurch wird es schwierig, mehrere Search-Ressourcen für QnA Maker zu unterscheiden. Erstellen Sie einen separaten Azure Cognitive Search-Dienst (benennen Sie ihn nach Belieben), und verbinden Sie ihn mit Ihrem QnA-Dienst. Die Schritte dafür sind ähnlich wie bei einem [Upgrade von Azure Search](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

</details>

<details>
<summary><b>QnA Maker gibt `Runtime core is not initialized,` zurück. Wie kann ich das Problem beheben?</b></summary>

**Antwort:** Der Speicherplatz für Ihren App-Dienst ist möglicherweise voll. Schritte zum Bereinigen des Speicherplatzes:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) den App-Dienst für QnA Maker aus, und beenden Sie dann den Dienst.
1. Wählen Sie, während Sie sich noch im App-Dienst befinden, **Entwicklungstools**, **Erweiterte Tools** und dann **Starten** aus. Dadurch wird ein neues Browserfenster geöffnet.
1. Wählen **Debugging-Konsole** und dann **CMD** aus, um ein Befehlszeilentool zu öffnen.
1. Navigieren Sie zum Verzeichnis _site/wwwroot/Data/QnAMaker/_ .
1. Entfernen Sie jeden Ordner, dessen Name mit `rd` beginnt.

    Folgende Elemente dürfen Sie **nicht löschen**:

    * Datei „KbIdToRankerMappings.txt
    * Datei „EndpointSettings.json“
    * Ordner „EndpointKeys“

1. Starten Sie den App-Dienst.
1. Greifen Sie auf Ihre Wissensdatenbank zu, um zu überprüfen, ob sie nun funktioniert.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)


<details>
<summary><b>Ich habe meinen Suchdienst gelöscht. Wie kann ich dieses Problem beheben?</b></summary>

**Antwort:** Wenn Sie einen Azure Cognitive Search-Index löschen, ist der Vorgang endgültig, weshalb der Index nicht wiederhergestellt werden kann.

</details>

<details>
<summary><b>Ich habe den Index `testkb` in meinem Suchdienst gelöscht. Wie kann ich dieses Problem beheben?</b></summary>

**Antwort:** Ihre alten Daten können nicht wiederhergestellt werden. Erstellen Sie eine neue QnA Maker-Ressource, und erstellen Sie Ihre Wissensdatenbank erneut.

</details>

<details>
<summary><b>Kann ich die gleiche Azure Cognitive Search-Ressource für Wissensdatenbanken in mehreren Sprachen verwenden?</b></summary>

**Antwort:** Um mehrere Wissensdatenbanken und mehrere Sprachen zu nutzen, müssen Benutzer eine QnA Maker-Ressource für jede Sprache erstellen. Dadurch wird ein separater Azure Search-Dienst pro Sprache erstellt. Das Kombinieren von Wissensdatenbanken verschiedener Sprachen in einem einzelnen Azure Search-Dienst führt zur einer niedrigeren Relevanz der Ergebnisse.

</details>

<details>
<summary><b>Wie kann ich den Namen der Azure Cognitive Search-Ressource, die von QnA Maker verwendet wird, ändern?</b></summary>

**Antwort:** Der Name der Azure Cognitive Search-Ressource ist der Name der QnA Maker-Ressource, an den einige zufällige Buchstaben angefügt sind. Dadurch wird es schwierig, mehrere Search-Ressourcen für QnA Maker zu unterscheiden. Erstellen Sie einen separaten Azure Cognitive Search-Dienst (benennen Sie ihn nach Belieben), und verbinden Sie ihn mit Ihrem QnA-Dienst. Die Schritte dafür sind ähnlich wie bei einem [Upgrade von Azure Search](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

</details>

---

## <a name="integrate-with-other-services-including-bots"></a>Integration in andere Dienste, einschließlich Bots

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

<details>
<summary><b>Muss ich Bot Framework nutzen, um QnA Maker verwenden zu können?</b></summary>

**Antwort:** Nein. Sie müssen [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) nicht in Verbindung mit QnA Maker verwenden. QnA Maker wird jedoch als eine von mehreren Vorlagen in [Azure Bot Service](/azure/bot-service/?preserve-view=true&view=azure-bot-service-4.0) angeboten. Bot Service ermöglicht die schnelle, intelligente Botentwicklung über Microsoft Bot Framework und wird in einer serverlosen Umgebung ausgeführt.

</details>

<details>
<summary><b>Wie kann ich einen neuen Bot mit QnA Maker erstellen?</b></summary>

**Antwort:** Folgen Sie den Anweisungen in [dieser](./Quickstarts/create-publish-knowledge-base.md) Dokumentation, um Ihren Bot mit Azure Bot Service zu erstellen.

</details>

<details>
<summary><b>Wie kann ich eine andere Wissensdatenbank mit einem bestehenden Azure Bot Service verwenden?</b></summary>

**Antwort:** Sie benötigen die folgenden Informationen über Ihre Wissensdatenbank:

* Wissensdatenbank-ID
* Sie finden den benutzerdefinierten Unterdomänennamen des veröffentlichten Endpunkts der Wissensdatenbank, der auch als `host` bezeichnet wird, nach der Veröffentlichung auf der Seite **Einstellungen**.
* Der Schlüssel des veröffentlichten Endpunkts der Wissensdatenbank – befindet sich nach der Veröffentlichung auf der Seite **Einstellungen**.

Wechseln Sie mit diesen Informationen zum App Service Ihres Bot im Azure-Portal. Ändern Sie unter **Einstellungen -> Konfiguration -> Anwendungseinstellungen** diese Werte.

Der Endpunktschlüssel der Wissensdatenbank ist im ABS-Dienst mit `QnAAuthkey` gekennzeichnet.

</details>

<details>
<summary><b>Können zwei oder mehr Clientanwendungen eine Wissensdatenbank gemeinsam nutzen?</b></summary>

**Antwort:** Ja, die Wissensdatenbank kann von beliebig vielen Clients abgefragt werden. Wenn die Antwort der Wissensdatenbank langsam zu sein scheint oder einen Timeout aufweist, sollten Sie eine Aktualisierung des Diensttarifs für den mit der Wissensdatenbank verbundenen App Service in Betracht ziehen.

</details>

<details>
<summary><b>Wie bette ich den QnA Maker-Dienst in meine Website ein?</b></summary>

**Antwort:** Führen Sie die folgenden Schritte aus, um den QnA Maker-Dienst als Webchat-Steuerelement in Ihre Website einzubetten:

1. Erstellen Sie Ihren FAQ-Bot, indem Sie den [hier](./Quickstarts/create-publish-knowledge-base.md) angegebenen Anweisungen folgen.
2. Aktivieren Sie den Webchat, indem Sie die [hier](/azure/bot-service/bot-service-channel-connect-webchat) aufgeführten Schritte ausführen.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)


<details>
<summary><b>Muss ich Bot Framework nutzen, um QnA Maker verwenden zu können?</b></summary>

**Antwort:** Nein. Sie müssen [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) nicht in Verbindung mit QnA Maker verwenden. QnA Maker wird jedoch als eine von mehreren Vorlagen in [Azure Bot Service](/azure/bot-service/?preserve-view=true&view=azure-bot-service-4.0) angeboten. Bot Service ermöglicht die schnelle, intelligente Botentwicklung über Microsoft Bot Framework und wird in einer serverlosen Umgebung ausgeführt.

</details>

<details>
<summary><b>Wie kann ich einen neuen Bot mit QnA Maker erstellen?</b></summary>

**Antwort:** Folgen Sie den Anweisungen in [dieser](./Quickstarts/create-publish-knowledge-base.md) Dokumentation, um Ihren Bot mit Azure Bot Service zu erstellen.

</details>

<details>
<summary><b>Wie kann ich eine andere Wissensdatenbank mit einem bestehenden Azure Bot Service verwenden?</b></summary>

**Antwort:** Sie benötigen die folgenden Informationen über Ihre Wissensdatenbank:

* Wissensdatenbank-ID
* Sie finden den benutzerdefinierten Unterdomänennamen des veröffentlichten Endpunkts der Wissensdatenbank, der auch als `host` bezeichnet wird, nach der Veröffentlichung auf der Seite **Einstellungen**.
* Der Schlüssel des veröffentlichten Endpunkts der Wissensdatenbank – befindet sich nach der Veröffentlichung auf der Seite **Einstellungen**.

Wechseln Sie mit diesen Informationen zum App Service Ihres Bot im Azure-Portal. Ändern Sie unter **Einstellungen -> Konfiguration -> Anwendungseinstellungen** diese Werte.

Der Endpunktschlüssel der Wissensdatenbank ist im ABS-Dienst mit `QnAAuthkey` gekennzeichnet.

</details>

<details>
<summary><b>Können zwei oder mehr Clientanwendungen eine Wissensdatenbank gemeinsam nutzen?</b></summary>

**Antwort:** Ja, die Wissensdatenbank kann von beliebig vielen Clients abgefragt werden. Wenn die Antwort der Wissensdatenbank langsam zu sein scheint oder einen Timeout aufweist, sollten Sie eine Aktualisierung des Diensttarifs für den mit der Wissensdatenbank verbundenen App Service in Betracht ziehen.

</details>

<details>
<summary><b>Wie bette ich den QnA Maker-Dienst in meine Website ein?</b></summary>

**Antwort:** Führen Sie die folgenden Schritte aus, um den QnA Maker-Dienst als Webchat-Steuerelement in Ihre Website einzubetten:

1. Erstellen Sie Ihren FAQ-Bot, indem Sie den [hier](./Quickstarts/create-publish-knowledge-base.md) angegebenen Anweisungen folgen.
2. Aktivieren Sie den Webchat, indem Sie die [hier](/azure/bot-service/bot-service-channel-connect-webchat) aufgeführten Schritte ausführen.

---

## <a name="data-storage"></a>Datenspeicher

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

<details>
<summary><b>Welche Daten werden gespeichert, und wo werden sie gespeichert?</b></summary>

**Antwort:**

Als Sie den QnA Maker-Dienst erstellt haben, haben Sie eine Azure-Region ausgewählt. Ihre Wissensdatenbanken und Protokolldateien werden in dieser Region gespeichert.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

<details>
<summary><b>Welche Daten werden gespeichert, und wo werden sie gespeichert?</b></summary>

**Antwort:**

Als Sie den QnA Maker-Dienst erstellt haben, haben Sie eine Azure-Region ausgewählt. Ihre Wissensdatenbanken und Protokolldateien werden in dieser Region gespeichert.

</details>

---