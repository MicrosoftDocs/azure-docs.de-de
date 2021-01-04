---
title: 'Bewährte Methoden: QnA Maker'
description: Nutzen Sie diese bewährten Methoden, um Ihre Knowledge Base zu verbessern und bessere Ergebnisse für die Endbenutzer Ihrer Anwendung bzw. Ihres Chatbots zu liefern.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: ee8d838ba315c2e261a61699948b71a710341165
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96346357"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Best Practices für eine QnA Maker-Wissensdatenbank

Die Anleitungen zum [Entwicklungszyklus einer Wissensdatenbank](../Concepts/development-lifecycle-knowledge-base.md) helfen Ihnen bei sämtlichen Schritten der Verwaltung Ihrer Wissensdatenbank. Nutzen Sie diese bewährten Methoden, um Ihre Wissensdatenbank zu optimieren und bessere Ergebnisse für die Endbenutzer Ihrer Clientanwendung bzw. Ihres Chatbots zu liefern.

## <a name="extraction"></a>Extraktion

Der QnA Maker-Dienst optimiert kontinuierlich die Algorithmen zum Extrahieren von Fragen und Antworten (QnA) aus Inhalten und erweitert die Liste der unterstützten Datei- und HTML-Formate. Befolgen Sie die [Richtlinien](../index.yml) für die Datenextraktion basierend auf Ihrem Dokumenttyp.

Ganz allgemein sollten die Seiten mit häufig gestellten Fragen eigenständig bereitgestellt und nicht mit anderen Informationen kombiniert werden. Produkthandbücher sollten klare Überschriften und vorzugsweise eine Indexseite aufweisen.

### <a name="configuring-multi-turn"></a>Konfigurieren von mehreren Durchläufen

[Erstellen Sie Ihre Wissensdatenbank](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure) mit aktivierter Mehrfachdurchlauf-Extrahierung. Wenn Ihre Wissensdatenbank eine Fragenhierarchie unterstützt bzw. unterstützen sollte, kann diese Hierarchie aus dem Dokument extrahiert oder nach der Extraktion des Dokuments erstellt werden.

## <a name="creating-good-questions-and-answers"></a>Formulieren guter Fragen und Antworten

### <a name="good-questions"></a>Gute Fragen

Die besten Fragen sind einfach. Überlegen Sie sich das Schlüsselwort oder den Ausdruck für jede Frage. Formulieren Sie dann eine einfache Frage zu diesem Schlüsselwort oder Ausdruck.

Fügen Sie so viele alternative Fragen hinzu, wie Sie benötigen, aber halten Sie die Änderungen einfach. Das Hinzufügen weiterer Wörter oder Ausdrücke, die nicht dem Hauptzweck der Frage entsprechen, hilft QnA Maker nicht, eine Übereinstimmung zu finden.


### <a name="add-relevant-alternative-questions"></a>Hinzufügen von relevanten alternativen Fragen

Ihr Benutzer kann entweder Fragen im Unterhaltungsstil eingeben (`How do I add a toner cartridge to my printer?`) oder eine Stichwortsuche (z. B. `toner cartridge`) verwenden. Die Wissensdatenbank sollte über beide Arten von Fragen verfügen, damit jeweils die beste Antwort zurückgegeben werden kann. Wenn Sie nicht sicher sind, welche Stichwörter ein Kunde eingibt, sollten Sie Application Insights-Daten nutzen, um Abfragen zu analysieren.

### <a name="good-answers"></a>Gute Antworten

Die besten Antworten sind einfache Antworten, solange diese nicht zu einfach gehalten sind. Verwenden Sie keine Antworten wie `yes` und `no`. Wenn Ihre Antwort auf andere Quellen verweisen oder mit Medien und Links umfassend gestaltet sein soll, sollten Sie [Metadatentags](../how-to/edit-knowledge-base.md#add-metadata) verwenden, um zwischen Antworten zu unterscheiden. [Übermitteln Sie die Abfrage](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) dann mit Metadatentags in der `strictFilters`-Eigenschaft, um die richtige Antwortversion zu erhalten.

|Antwort|Folgeaufforderungen|
|--|--|
|Fahren Sie das Surface-Laptop mithilfe der Ein-Aus-Taste auf der Tastatur herunter.|* Tastenkombinationen für Standbymodus, Herunterfahren und Neustart.<br>* Vorgehensweise beim Hardboot eines Surface-Laptops<br>* Vorgehensweise zum Ändern des BIOS für einen Surface-Laptop<br>* Unterschiede zwischen Standbymodus, Herunterfahren und Neustart|
|Der Kundendienst steht über Telefon, Skype-und SMS-Nachrichten rund um die Uhr zur Verfügung.|* Kontaktinformationen für den Vertrieb.<br> * Büro- und Store-Standorte und Öffnungszeiten für einen persönlichen Besuch.<br> * Zubehör für einen Surface-Laptop.|

## <a name="chit-chat"></a>Geplauder
Fügen Sie Ihrem Bot Geplauder hinzu, um ihn mit geringem Aufwand gesprächiger und ansprechender zu gestalten. Sie können beim Erstellen Ihrer Wissensdatenbank ganz einfach Geplauderdatasets von vordefinierten Persönlichkeiten hinzufügen und diese jederzeit ändern. Weitere Informationen zum [Hinzufügen von Geplauder zur Wissensdatenbank](../How-To/chit-chat-knowledge-base.md).

Das Geplauder wird in [vielen Sprachen](../how-to/chit-chat-knowledge-base.md#language-support)unterstützt.

### <a name="choosing-a-personality"></a>Auswählen einer Persönlichkeit
Geplauder wird für verschiedene vordefinierte Persönlichkeiten unterstützt:

|Persönlichkeit |QnA Maker-Datasetdatei |
|---------|-----|
|Professionell |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Freundlich |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Witzig |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Mitfühlend |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Begeistert |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

Die Antworten reichen von formell bis informell und irrelevant. Sie sollten die Persönlichkeit auswählen, die am besten zu dem Sprachstil passt, den Sie für Ihren Bot wünschen. Sie können die [Datasets](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) anzeigen und ein Dataset auswählen, das als Basis für Ihren Bot dient, und dann die Antworten anpassen.

### <a name="edit-bot-specific-questions"></a>Bearbeiten von botspezifische Fragen
Es sind einige botspezifische Fragen vorhanden, die Teil des Geplauderdatasets sind und mit generischen Antworten aufgefüllt wurden. Ändern Sie diese Antworten so, dass sie am besten zu den Details Ihres Bots passen.

Wir empfehlen, die folgenden Geplauder-QnAs zu präzisieren:

* Wer sind Sie?
* Was können Sie tun?
* Wie alt sind Sie?
* Wer hat Sie erstellt?
* Hallo

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Hinzufügen von benutzerdefiniertem Geplauder mit einem Metadatentag

Stellen Sie beim Hinzufügen Ihrer eigenen Frage-und-Antwort-Paare für Geplauder sicher, dass Sie auch Metadaten hinzufügen, damit diese Antworten zurückgegeben werden. Das Paar aus Metadatenname und -wert lautet `editorial:chitchat`.

## <a name="searching-for-answers"></a>Suchen nach Antworten

Für die GenerateAnswer-API werden sowohl Fragen als auch die Antwort verwendet, um nach den besten Antworten auf die Abfrage eines Benutzers zu suchen.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Suchen nach Fragen nur dann, wenn eine Antwort nicht relevant ist

Verwenden Sie [`RankerType=QuestionOnly`](#choosing-ranker-type), wenn Sie nicht nach Antworten suchen möchten.

Ein Beispiel hierfür ist, wenn die Wissensdatenbank aus einem Katalog mit Akronymen als Fragen und deren vollständigen Form als Antwort besteht. Der Wert der Antwort ist beim Suchen nach der passenden Antwort nicht hilfreich.

## <a name="rankingscoring"></a>Rangfolge/Bewertung
Nutzen Sie unbedingt auch die von QnA Maker unterstützten Rangfolgefeatures. Damit erhöhen Sie die Wahrscheinlichkeit, dass eine bestimmten Benutzerfrage angemessen beantwortet wird.

### <a name="choosing-a-threshold"></a>Auswählen eines Schwellenwerts

Die standardmäßige [Zuverlässigkeitsbewertung](confidence-score.md), die als Schwellenwert verwendet wird, ist 0. Sie können den [Schwellenwert aber für Ihre Wissensdatenbank gemäß Ihren Bedürfnissen ändern](confidence-score.md#set-threshold). Da jede Wissensdatenbank anders ist, sollten Sie den Schwellenwert testen und einen Wert auswählen, der für Ihre Wissensdatenbank am besten geeignet ist.

### <a name="choosing-ranker-type"></a>Auswählen des Typs der Rangfolgefunktion
Standardmäßig durchsucht QnA Maker Fragen und Antworten. Wenn Sie nur Fragen durchsuchen möchten, um eine Antwort zu generieren, verwenden Sie `RankerType=QuestionOnly` im POST-Text der GenerateAnswer-Anforderung.

### <a name="add-alternate-questions"></a>Hinzufügen alternativer Fragen
[Alternative Fragen](../How-To/edit-knowledge-base.md) verbessern die Wahrscheinlichkeit einer Übereinstimmung mit einer Benutzerfrage. Alternative Fragen sind besonders dann nützlich, wenn es mehrere Möglichkeiten gibt, die gleiche Frage zu stellen. Dies können z.B. Änderungen in der Satzstruktur und in der Wortwahl sein.

|Ursprüngliche Abfrage|Alternative Abfragen|Change|
|--|--|--|
|Sind Parkplätze verfügbar?|Haben Sie einen Parkplatz?|Satzstruktur|
 |Hi|Yo<br>Hallo!|Wortwahl oder Slang|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Verwenden von Metadatentags zum Filtern von Fragen und Antworten

Mit [Metadaten](../How-To/edit-knowledge-base.md) kann einer Clientanwendung mitgeteilt werden, dass nicht alle Antworten verwendet werden sollen, sondern stattdessen die Ergebnisse einer Benutzerabfrage basierend auf Metadatentags eingegrenzt werden sollen. Die Antwort aus der Knowledge Base kann basierend auf Metadatentags variieren, selbst wenn die Frage identisch ist. So gibt es beispielsweise auf die Frage *„Wo ist der Parkplatz?“* eine andere Antwort, wenn ein anderer Standort für eine Filiale der Restaurantkette verwendet wird: Die Metadaten sind für *Standort: Seattle* anders als für *Standort: Redmond*.

### <a name="use-synonyms"></a>Verwenden von Synonymen
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)
Für die englische Sprache werden Synonyme zwar teilweise unterstützt, aber Sie sollten Wortvarianten über die [Alterations-API](/rest/api/cognitiveservices/qnamaker/alterations/replace) verwenden (ohne Berücksichtigung der Groß- und Kleinschreibung), um Synonyme für Schlüsselwörter hinzuzufügen, die unterschiedliche Formen aufweisen. Synonyme werden auf QnA Maker-Dienstebene hinzugefügt und **für alle Wissensdatenbanken des Diensts gemeinsam verwendet**.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)
Für die englische Sprache werden Synonyme zwar teilweise unterstützt, aber Sie sollten Wortvarianten über die [Alterations-API](/rest/api/cognitiveservices/qnamaker/alterations/replace) verwenden (ohne Berücksichtigung der Groß- und Kleinschreibung), um Synonyme für Schlüsselwörter hinzuzufügen, die unterschiedliche Formen aufweisen. Synonyme in QnA Maker verwaltet (Vorschau) werden **pro Wissensdatenbank hinzugefügt**.

|Ursprüngliches Wort|Synonyme|
|--|--|
|kaufen|erwerben<br>Electronic Banking<br>E-Banking|

---

### <a name="use-distinct-words-to-differentiate-questions"></a>Verwenden unterschiedlicher Wörter für die Unterscheidung von Fragen
Die QnA Maker-Algorithmen für Rangfolgen, die eine Benutzerfrage einer Frage in der Wissensdatenbank zuordnen, funktionieren am besten, wenn jede Frage eine andere Anforderung behandelt. Die Wiederholung derselben Wortgruppe in unterschiedlichen Fragen reduziert die Wahrscheinlichkeit, dass die richtige Antwort für eine bestimmte Benutzerfrage mit diesen Wörtern ausgewählt wird.

Beispielsweise könnten Sie zwei separate QnAs mit den folgenden Fragen haben:

|QnAs|
|--|
|Wo ist der *Standort* des Parkplatzes?|
|Wo ist der *Standort* des Geldautomaten?|

Da diese beiden QnAs sehr ähnliche Wörter verwenden, könnte diese Ähnlichkeit für viele Benutzeranfragen sehr ähnliche Bewertungen verursachen, die als *„Wo ist der Standort des `<x>`“* formuliert sind. Versuchen Sie stattdessen, mit Abfragen wie *„Wo ist der Parkplatz?“* und *„Wo ist der Geldautomat?“* klar zu unterscheiden, indem Sie Wörter wie „Standort“ vermeiden, die in vielen Fragen in Ihrer Wissensdatenbank vorkommen könnten.

## <a name="collaborate"></a>Zusammenarbeiten
QnA Maker ermöglicht Benutzern das [Zusammenarbeiten](../index.yml) an einer Knowledge Base. Benutzer benötigen Zugriff auf die Azure QnA Maker-Ressourcengruppe, um auf Wissensdatenbanken zugreifen zu können. Einige Organisationen lagern die Bearbeitung und Verwaltung ihrer Knowledge Base aus, möchten aber eventuell trotzdem weiterhin den Zugriff auf ihre Azure-Ressourcen schützen. Dieses Modell aus bearbeitenden und genehmigenden Personen erfolgt durch das Einrichten von zwei identischen [QnA Maker-Diensten](../How-to/set-up-qnamaker-service-azure.md) in unterschiedlichen Abonnements, von denen einer für den Bearbeitungs- und Testzyklus ausgewählt wird. Nach Abschluss der Tests werden die Inhalte der Wissensdatenbank mit einem [Import-/Export](../Tutorials/migrate-knowledge-base.md)vorgang an den QnA Maker-Dienst der genehmigenden Person übertragen, die die Wissensdatenbank schließlich veröffentlicht und den Endpunkt aktualisiert.



## <a name="active-learning"></a>Aktives Lernen

[Aktives Lernen](../How-to/use-active-learning.md) leistet die beste Arbeit beim Vorschlagen alternativer Fragen, wenn ein breites Spektrum an Qualität und Quantität von benutzerbezogenen Abfragen zur Verfügung steht. Es ist wichtig, dass die Benutzerabfragen von Clientanwendungen ohne Zensur an der Feedbackschleife des aktiven Lernens teilnehmen können. Sobald Fragen im QnA Maker-Portal vorgeschlagen werden, können Sie **[nach Vorschlägen filtern](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** und diese Vorschläge dann überprüfen, um sie zu akzeptieren oder abzulehnen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bearbeiten einer Knowledge Base](../How-to/edit-knowledge-base.md)