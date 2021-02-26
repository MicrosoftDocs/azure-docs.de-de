---
title: Importieren von Richtlinien für die Dokumentformatierung – QnA Maker
description: Verwenden Sie diese Richtlinien zum Importieren von Dokumenten, um die besten Ergebnisse für Ihre Inhalte zu erzielen.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: 15ff2ec296cedc37b086a9ca2d0825fb20b4f05a
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549540"
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

> [!NOTE]
> Das Extrahieren von Bildern aus hochgeladenen Dokumenten wird derzeit nicht unterstützt.

### <a name="product-manuals"></a>Produkthandbücher

Bei einem Handbuch handelt es sich in der Regel um Anleitungen, die mit einem Produkt geliefert werden. Es hilft dem Benutzer beim Einrichten, Verwenden und Warten des Produkts sowie der Problembehandlung. Wenn QnA Maker ein Handbuch verarbeitet, werden die Überschriften und Unterüberschriften als Fragen und die nachfolgenden Inhalte als Antworten extrahiert. Ein Beispiel finden Sie [hier](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Unten sehen Sie ein Beispiel für ein Handbuch mit einer Indexseite und hierarchischen Inhalten.

 ![Beispiel für ein Produkthandbuch für eine Wissensdatenbank](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> Das Extrahieren funktioniert am besten bei Handbüchern, die über ein Inhaltsverzeichnis und/oder eine Indexseite verfügen und eine klare Struktur mit hierarchischen Überschriften aufweisen.

### <a name="brochures-guidelines-papers-and-other-files"></a>Broschüren, Richtlinien, Artikel und andere Dateien

Viele andere Arten von Dokumenten können ebenfalls verarbeitet werden, um QA-Paare zu generieren, vorausgesetzt, sie weisen eine klare Struktur und ein klares Layout auf. Dazu gehören: Broschüren, Leitfäden, Berichte, Whitepaper, wissenschaftliche Veröffentlichungen, Richtlinien, Bücher usw. Ein Beispiel finden Sie [hier](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Unten sehen Sie ein Beispiel für ein semistrukturiertes Dokument ohne Index:

 ![Semistrukturiertes Azure Blob Storage-Dokument](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

### <a name="structured-qna-document"></a>Strukturiertes QnA-Dokument

Das Format für strukturierte Fragen/Antworten in DOC-Dateien besteht aus abwechselnden Fragen und Antworten pro Zeile: eine Frage pro Zeile, gefolgt von ihrer Antwort in der folgenden Zeile, wie unten gezeigt:

```text
Question1

Answer1

Question2

Answer2
```

Unten sehen Sie ein Beispiel für ein strukturiertes QnA-Word-Dokument:

 ![Beispiel für ein strukturiertes QnA-Dokument für eine Wissensdatenbank](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

### <a name="structured-txt-tsv-and-xls-files"></a>Strukturierte *TXT*-, *TSV*- und *XLS*-Dateien

QnAs in Form von strukturierten *TXT*-, *TSV*- oder *XLS*-Dateien können auch in QnA Maker hochgeladen werden, um eine Wissensdatenbank zu erstellen oder zu erweitern.  Dabei kann es sich um Nur-Text-Dateien oder um Dateien mit Inhalten im RTF- oder HTML-Format handeln.

| Frage  | Antwort  | Metadaten (1 Schlüssel: 1 Wert) |
|-----------|---------|-------------------------|
| Frage1 | Antwort1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Frage2 | Antwort2 |      `Key:Value`           |

Alle weiteren Spalten in der Quelldatei werden ignoriert.

#### <a name="example-of-structured-excel-file"></a>Beispiel für eine strukturierte Excel-Datei

Unten sehen Sie ein Beispiel für eine strukturierte QnA-*XLS*-Datei mit HTML-Inhalten:

 ![Beispiel für eine strukturierte QnA-Excel-Datei für eine Wissensdatenbank](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

#### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Beispiel für alternative Fragen zu einer einzelnen Antwort in einer Excel-Datei

Unten sehen Sie ein Beispiel für eine strukturierte QnA-*XLS*-Datei mit mehreren alternativen Fragen zu einer einzelnen Antwort:

 ![Beispiel für alternative Fragen zu einer einzelnen Antwort in einer Excel-Datei](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Nachdem die Datei importiert wurde, befindet sich das Frage- und Antwortpaar in der Wissensdatenbank wie nachfolgend gezeigt:

 ![Screenshot alternativer Fragen zu einer einzelnen Antwort, die in die Wissensdatenbank importiert wurden](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

### <a name="structured-data-format-through-import"></a>Strukturiertes Datenformat durch Import

Beim Importieren einer Wissensdatenbank wird der Inhalt der vorhandenen Wissensdatenbank ersetzt. Der Import erfordert eine strukturierte TSV-Datei, die Informationen zur Datenquelle enthält. Diese Informationen helfen QnA Maker beim Gruppieren der Frage-Antwort-Paare und dem Zuweisen zu einer bestimmten Datenquelle.

| Frage  | Antwort  | `Source`| Metadaten (1 Schlüssel: 1 Wert) |
|-----------|---------|----|---------------------|
| Frage1 | Antwort1 | URL1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Frage2 | Antwort2 | Redaktionelle Änderung|    `Key:Value`       |

<a href="#formatting-considerations"></a>

### <a name="multi-turn-document-formatting"></a>Dokumentformatierung mit Mehrfachdurchläufen

* Verwenden Sie Überschriften und Unterüberschriften zum Angeben der Hierarchie. Beispielsweise können Sie h1 hinzufügen, um die übergeordnete QnA zu bezeichnen, und h2, um die QnA anzugeben, die als Aufforderung verwendet werden soll. Verwenden Sie einen kleinen Überschrifts-Schriftgrad, um nachfolgende Hierarchie anzugeben. Verwenden Sie keine Auszeichnung, Farbe oder einen anderen Mechanismus zum Angeben von Struktur in Ihrem Dokument, QnA Maker kann die Mehrfachdurchlauf-Aufforderungen nicht extrahieren.
* Das erste Zeichen der Überschrift muss groß geschrieben werden.
* Beenden Sie eine Überschrift nicht mit einem Fragezeichen, `?`.

**Beispieldokumente**:<br>[Surface Pro (DOCX)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso Benefits (DOCX)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso Benefits (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="faq-urls"></a>FAQ-URLs

QnA Maker kann Webseiten mit häufig gestellten Fragen auf drei Arten unterstützen:

* Einfache FAQ-Seiten
* FAQ-Seiten mit Links
* FAQ-Seiten mit einer Themenstartseite

### <a name="plain-faq-pages"></a>Einfache FAQ-Seiten

Dies ist der am häufigsten verwendete Typ einer FAQ-Seite, bei der auf der gleichen Seite die Antworten unmittelbar auf die Fragen folgen.

Unten sehen Sie ein Beispiel für eine einfache FAQ-Seite:

![Beispiel für eine einfache FAQ-Seite für eine Wissensdatenbank](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>FAQ-Seiten mit Links

Bei diesem FAQ-Seitentyp sind Fragen zusammengefasst und mit Antworten verknüpft, die sich in verschiedenen Abschnitten auf derselben Seite oder auf verschiedenen Seiten befinden.

Unten sehen Sie ein Beispiel für eine FAQ-Seite mit Links in Abschnitten, die sich auf derselben Seite befinden:

 ![Beispiel für eine FAQ-Seite mit Abschnittsverknüpfung für eine Wissensdatenbank](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Übergeordnete Themenseite mit Links zu untergeordneten Antwortseiten

Diese Art von FAQ verfügt über eine Themenseite, auf der jedes Thema mit einem entsprechenden Satz von Fragen und Antworten auf einer anderen Seite verknüpft ist. QnA Maker durchsucht alle verknüpften Seiten, um die entsprechenden Fragen und Antworten zu extrahieren.

Unten sehen Sie ein Beispiel für eine Themenseite mit Links zu FAQ-Abschnitten auf verschiedenen Seiten.

 ![Beispiel für eine FAQ-Seite mit Deep-Link für eine Wissensdatenbank](./media/qnamaker-concepts-datasources/topics-faq.png)

### <a name="support-urls"></a>Support-URLs

QnA Maker kann semistrukturierte Supportwebseiten verarbeiten. Hierzu zählen beispielsweise Webartikel, in denen die Vorgehensweise zum Ausführen einer bestimmte Aufgabe, die Diagnostizierung oder Behebung eines bestimmten Problems oder die Verwendung bewährter Methoden für einen bestimmten Prozess beschrieben wird. Die Extraktion funktioniert am besten für Inhalte mit klarer Struktur und hierarchischen Überschriften.

> [!NOTE]
> Die Extraktion für Supportartikel ist ein neues Feature und befindet sich noch in einer frühen Phase. Sie funktioniert am besten für einfache, gut strukturierte Seiten ohne komplexe Kopf- und Fußzeilen.

![QnA Maker unterstützt die Extraktion von semistrukturierten Webseiten, die über eine klare Struktur mit hierarchischen Überschriften verfügen.](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)

## <a name="import-and-export-knowledge-base"></a>Importieren und Exportieren der Knowledge Base

**TSV- und XLS-Dateien** aus exportierten Wissensdatenbanken können nur verwendet werden, indem die Dateien von der Seite **Einstellungen** im QnA Maker-Portal importiert werden. Sie können nicht als Datenquellen während der Erstellung der Wissensdatenbank oder über die Funktion **+ Datei hinzufügen** oder **+ URL hinzufügen** auf der Seite **Einstellungen** verwendet werden. 

Wenn Sie die Wissensdatenbank über diese **TSV- und XLS-Dateien** importieren, werden die QnA-Paare der redaktionellen Quelle hinzugefügt und nicht den Quellen, aus denen die QnAs in der exportierten Wissensdatenbank extrahiert wurden. 


## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie eine vollständige Liste mit [Inhaltstypen und Beispielen](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base).
