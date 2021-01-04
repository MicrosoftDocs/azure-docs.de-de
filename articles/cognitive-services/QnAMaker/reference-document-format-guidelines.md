---
title: Importieren von Richtlinien für die Dokumentformatierung – QnA Maker
description: Verwenden Sie diese Richtlinien zum Importieren von Dokumenten, um die besten Ergebnisse für Ihre Inhalte zu erzielen.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: 1bf6eebcfc43452dd9a62e4a49f9aacbcd517ffc
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352234"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Formatierungsrichtlinien für importierte Dokumente und URLs

Überprüfen Sie diese Formatierungsrichtlinien, um die besten Ergebnisse für Ihre Inhalte zu erzielen.

## <a name="formatting-considerations"></a>Überlegungen zur Formatierung

Nach dem Importieren einer Datei oder URL konvertiert QnA Maker den Inhalt im [Markdown-Format](https://en.wikipedia.org/wiki/Markdown) und speichert diesen. Beim Konvertierungsvorgang werden neue Zeilen im Text hinzugefügt, z. B. `\n\n`. Wenn Sie sich bereits mit dem Markdown-Format auskennen, können Sie den konvertierten Inhalt eher verstehen und den Inhalt der Wissensdatenbank verwalten.

Wenn Sie Ihren Inhalt direkt in der Wissensdatenbank hinzufügen oder bearbeiten, können Sie das **Markdown-Format** verwenden, um umfangreiche Textinhalte zu erstellen oder Inhalte im Markdown-Format zu ändern, die bereits in der Antwort vorhanden sind. QnA Maker unterstützt einen Großteil des Markdown-Formats, um umfassende Textfunktionen für Ihre Inhalte bereitzustellen. Die Clientanwendung, z. B. ein Chatbot, unterstützt jedoch möglicherweise nicht dieselben Markdown-Formate. Es ist wichtig, die Anzeige von Antworten in der Clientanwendung zu testen.

Hier finden Sie eine vollständige Liste mit [Inhaltstypen und Beispielen](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base).

## <a name="basic-document-formatting"></a>Grundlegende Dokumentformatierung

QnA Maker identifiziert Abschnitte und Unterabschnitte sowie Beziehungen in der Datei anhand von visuellen Hinweisen wie:

* Schriftgrad
* Schriftstil
* Nummerierung
* Farben

## <a name="product-manuals"></a>Produkthandbücher

Bei einem Handbuch handelt es sich in der Regel um Anleitungen, die mit einem Produkt geliefert werden. Es hilft dem Benutzer beim Einrichten, Verwenden und Warten des Produkts sowie der Problembehandlung. Wenn QnA Maker ein Handbuch verarbeitet, werden die Überschriften und Unterüberschriften als Fragen und die nachfolgenden Inhalte als Antworten extrahiert. Ein Beispiel finden Sie [hier](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Unten sehen Sie ein Beispiel für ein Handbuch mit einer Indexseite und hierarchischen Inhalten.

 ![Beispiel für ein Produkthandbuch für eine Wissensdatenbank](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> Das Extrahieren funktioniert am besten bei Handbüchern, die über ein Inhaltsverzeichnis und/oder eine Indexseite verfügen und eine klare Struktur mit hierarchischen Überschriften aufweisen.

## <a name="brochures-guidelines-papers-and-other-files"></a>Broschüren, Richtlinien, Artikel und andere Dateien

Viele andere Arten von Dokumenten können ebenfalls verarbeitet werden, um QA-Paare zu generieren, vorausgesetzt, sie weisen eine klare Struktur und ein klares Layout auf. Dazu gehören: Broschüren, Leitfäden, Berichte, Whitepaper, wissenschaftliche Veröffentlichungen, Richtlinien, Bücher usw. Ein Beispiel finden Sie [hier](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Unten sehen Sie ein Beispiel für ein semistrukturiertes Dokument ohne Index:

 ![Semistrukturiertes Azure Blob Storage-Dokument](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

## <a name="structured-qna-document"></a>Strukturiertes QnA-Dokument

Das Format für strukturierte Fragen/Antworten in DOC-Dateien besteht aus abwechselnden Fragen und Antworten pro Zeile: eine Frage pro Zeile, gefolgt von ihrer Antwort in der folgenden Zeile, wie unten gezeigt:

```text
Question1

Answer1

Question2

Answer2
```

Unten sehen Sie ein Beispiel für ein strukturiertes QnA-Word-Dokument:

 ![Beispiel für ein strukturiertes QnA-Dokument für eine Wissensdatenbank](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>Strukturierte *TXT*-, *TSV*- und *XLS*-Dateien

QnAs in Form von strukturierten *TXT*-, *TSV*- oder *XLS*-Dateien können auch in QnA Maker hochgeladen werden, um eine Wissensdatenbank zu erstellen oder zu erweitern.  Dabei kann es sich um Nur-Text-Dateien oder um Dateien mit Inhalten im RTF- oder HTML-Format handeln.

| Frage  | Antwort  | Metadaten (1 Schlüssel: 1 Wert) |
|-----------|---------|-------------------------|
| Frage1 | Antwort1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Frage2 | Antwort2 |      `Key:Value`           |

Alle weiteren Spalten in der Quelldatei werden ignoriert.

### <a name="example-of-structured-excel-file"></a>Beispiel für eine strukturierte Excel-Datei

Unten sehen Sie ein Beispiel für eine strukturierte QnA-*XLS*-Datei mit HTML-Inhalten:

 ![Beispiel für eine strukturierte QnA-Excel-Datei für eine Wissensdatenbank](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Beispiel für alternative Fragen zu einer einzelnen Antwort in einer Excel-Datei

Unten sehen Sie ein Beispiel für eine strukturierte QnA-*XLS*-Datei mit mehreren alternativen Fragen zu einer einzelnen Antwort:

 ![Beispiel für alternative Fragen zu einer einzelnen Antwort in einer Excel-Datei](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Nachdem die Datei importiert wurde, befindet sich das Frage- und Antwortpaar in der Wissensdatenbank wie nachfolgend gezeigt:

 ![Screenshot alternativer Fragen zu einer einzelnen Antwort, die in die Wissensdatenbank importiert wurden](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Strukturiertes Datenformat durch Import

Beim Importieren einer Wissensdatenbank wird der Inhalt der vorhandenen Wissensdatenbank ersetzt. Der Import erfordert eine strukturierte TSV-Datei, die Informationen zur Datenquelle enthält. Diese Informationen helfen QnA Maker beim Gruppieren der Frage-Antwort-Paare und dem Zuweisen zu einer bestimmten Datenquelle.

| Frage  | Antwort  | `Source`| Metadaten (1 Schlüssel: 1 Wert) |
|-----------|---------|----|---------------------|
| Frage1 | Antwort1 | URL1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Frage2 | Antwort2 | Redaktionelle Änderung|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>Dokumentformatierung mit Mehrfachdurchläufen

* Verwenden Sie Überschriften und Unterüberschriften zum Angeben der Hierarchie. Beispielsweise können Sie h1 hinzufügen, um die übergeordnete QnA zu bezeichnen, und h2, um die QnA anzugeben, die als Aufforderung verwendet werden soll. Verwenden Sie einen kleinen Überschrifts-Schriftgrad, um nachfolgende Hierarchie anzugeben. Verwenden Sie keine Auszeichnung, Farbe oder einen anderen Mechanismus zum Angeben von Struktur in Ihrem Dokument, QnA Maker kann die Mehrfachdurchlauf-Aufforderungen nicht extrahieren.
* Das erste Zeichen der Überschrift muss groß geschrieben werden.
* Beenden Sie eine Überschrift nicht mit einem Fragezeichen, `?`.

**Beispieldokumente**:<br>[Surface Pro (DOCX)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso Benefits (DOCX)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso Benefits (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie eine vollständige Liste mit [Inhaltstypen und Beispielen](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base).