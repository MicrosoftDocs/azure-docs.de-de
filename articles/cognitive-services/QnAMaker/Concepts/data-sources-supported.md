---
title: 'Unterstützte Datenquellen: QnA Maker'
titleSuffix: Azure Cognitive Services
description: QnA Maker extrahiert automatisch Frage-Antwort-Paare aus semistrukturierten Inhalten wie FAQs, Produkthandbüchern, Leitfäden, Supportdokumenten und Richtlinien, die als Webseiten, PDF-Dateien oder MS Word-Dokumente gespeichert sind. Inhalte können der Wissensdatenbank auch aus strukturierten QnA-Inhaltsdateien hinzugefügt werden.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/26/2019
ms.author: tulasim
ms.openlocfilehash: 8fcc3ea8340a8645a1983eebb4a619904f884a19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61379449"
---
# <a name="data-sources-for-qna-maker-content"></a>Datenquellen für QnA Maker-Inhalt

QnA Maker extrahiert automatisch Frage-Antwort-Paare aus semistrukturierten Inhalten wie FAQs, Produkthandbüchern, Leitfäden, Supportdokumenten und Richtlinien, die als Webseiten, PDF-Dateien oder MS Word-Dokumente gespeichert sind. Inhalte können der Wissensdatenbank auch aus strukturierten QnA-Inhaltsdateien hinzugefügt werden. 

Die folgende Tabelle fasst die Inhaltstypen und Dateiformate zusammen, die von QnA Maker unterstützt werden.

|Quellentyp|Inhaltstyp| Beispiele|
|--|--|--|
|URL|Häufig gestellte Fragen<br> (flach, mit Abschnitten oder mit einer Themenstartseite)<br>Supportseiten <br> (kurze Anleitungen, Problembehandlungsartikel und Ähnliches)|[Einfache FAQ](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[FAQ mit Links](https://www.microsoft.com/software-download/faq),<br> [FAQ mit Themenstartseite](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Supportartikel](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|Häufig gestellte Fragen,<br> Produkthandbuch,<br> Broschüren,<br> Artikel,<br> Flyerrichtlinie,<br> Supporthandbuch,<br> strukturierte QnA<br> usw.|[Strukturiertes QnA-Dokument (DOC-Datei)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Beispielprodukthandbuch (PDF-Datei)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Semistrukturiertes Beispiel (DOC-Datei)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Beispielwhitepaper (PDF-Datei)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)|
|Excel|Strukturierte QnA-Datei<br> (einschließlich RTF- und HTML-Unterstützung)|[QnA-Beispiel-FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|TXT/TSV|Strukturierte QnA-Datei|[Beispielgeplauder.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

## <a name="data-source-locations"></a>Speicherorte von Datenquellen

Die meisten Speicherorte der Datenquellen müssen öffentliche URLs oder Dateien bereitstellen, die keine Authentifizierung erfordern. 

[Speicherorte für SharePoint-Datenquellen](../How-to/add-sharepoint-datasources.md) dürfen authentifizierte Dateien bereitstellen. Bei SharePoint-Ressourcen muss es sich um Dateien und nicht um Webseiten handeln. 

Wenn Sie über eine authentifizierte Datei oder URL verfügen, besteht eine alternative Option darin, die Datei von der authentifizierten Website auf Ihren lokalen Computer herunterzuladen und dann die Datei von Ihrem lokalen Computer zur Wissensdatenbank hinzuzufügen. 

## <a name="faq-urls"></a>FAQ-URLs

QnA Maker kann Webseiten mit häufig gestellten Fragen auf drei Arten unterstützen: Einfache FAQ-Seiten, FAQ-Seiten mit Links, FAQ-Seiten mit einer Themenstartseite.

### <a name="plain-faq-pages"></a>Einfache FAQ-Seiten

Dies ist der am häufigsten verwendete Typ einer FAQ-Seite, bei der auf der gleichen Seite die Antworten unmittelbar auf die Fragen folgen. 

Unten sehen Sie ein Beispiel für eine einfache FAQ-Seite:

![Beispiel für eine einfache FAQ-Seite für eine Wissensdatenbank](../media/qnamaker-concepts-datasources/plain-faq.png) 

 
### <a name="faq-pages-with-links"></a>FAQ-Seiten mit Links 

Bei diesem FAQ-Seitentyp sind Fragen zusammengefasst und mit Antworten verknüpft, die sich in verschiedenen Abschnitten auf derselben Seite oder auf verschiedenen Seiten befinden.

Unten sehen Sie ein Beispiel für eine FAQ-Seite mit Links in Abschnitten, die sich auf derselben Seite befinden:

 ![Beispiel für eine FAQ-Seite mit Abschnittsverknüpfung für eine Wissensdatenbank](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


### <a name="faq-pages-with-a-topics-homepage"></a>FAQ-Seiten mit einer Themenstartseite

Dieser Typ von FAQ weist eine Startseite mit den Themen auf, wobei jedes Thema ein Link zu seinen relevanten QnAs auf einer anderen Seite ist. In diesem Fall durchsucht QnA Maker alle verknüpften Seiten, um die entsprechenden Fragen und Antworten zu extrahieren.

Unten sehen Sie ein Beispiel für eine FAQ-Seite, auf der eine Themenstartseite Links zu FAQ-Abschnitten auf verschiedenen Seiten enthält. 

 ![Beispiel für eine FAQ-Seite mit Deep-Link für eine Wissensdatenbank](../media/qnamaker-concepts-datasources/topics-faq.png) 


### <a name="support-urls"></a>Support-URLs

QnA Maker kann semistrukturierte Supportwebseiten verarbeiten. Hierzu zählen beispielsweise Webartikel, in denen die Vorgehensweise zum Ausführen einer bestimmte Aufgabe, die Diagnostizierung oder Behebung eines bestimmten Problems oder die Verwendung bewährter Methoden für einen bestimmten Prozess beschrieben wird. Die Extraktion funktioniert am besten für Inhalte mit klarer Struktur und hierarchischen Überschriften.

> [!NOTE]
> Die Extraktion für Supportartikel ist ein neues Feature und befindet sich noch in einer frühen Phase. Sie funktioniert am besten für einfache, gut strukturierte Seiten ohne komplexe Kopf- und Fußzeilen.

![QnA Maker unterstützt die Extraktion von semistrukturierten Webseiten, die über eine klare Struktur mit hierarchischen Überschriften verfügen.](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>PDF-/DOC-Dateien

QnA Maker kann semistrukturierte Inhalte in einer PDF- oder DOC-Datei verarbeiten und in QnAs konvertieren. Eine geeignete Datei, die gut extrahiert werden kann, ist eine Datei, in der der Inhalt in einer strukturierten Form organisiert ist und in wohldefinierten Abschnitten dargestellt wird. Die Abschnitte können weiter in Unterabschnitte oder Unterthemen unterteilt werden. Die Extraktion funktioniert am besten für Dokumente, die eine klare Struktur mit hierarchischen Überschriften aufweisen.

QnA Maker identifiziert Abschnitte und Unterabschnitte sowie Beziehungen in der Datei anhand von visuellen Hinweisen wie Schriftgröße, Schriftstil, Nummerierung, Farben usw. Semistrukturierte PDF- oder DOC-Dateien können Handbücher, FAQs, Leitfäden, Richtlinien, Broschüren, Flyer und viele andere Dateitypen sein. Nachfolgend finden Sie einige Beispieltypen für diese Dateien.

### <a name="product-manuals"></a>Produkthandbücher

Bei einem Handbuch handelt es sich in der Regel um Anleitungen, die mit einem Produkt geliefert werden. Es hilft dem Benutzer beim Einrichten, Verwenden und Warten des Produkts sowie der Problembehandlung. Wenn QnA Maker ein Handbuch verarbeitet, werden die Überschriften und Unterüberschriften als Fragen und die nachfolgenden Inhalte als Antworten extrahiert. Ein Beispiel finden Sie [hier](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Unten sehen Sie ein Beispiel für ein Handbuch mit einer Indexseite und hierarchischen Inhalten.

 ![Beispiel für ein Produkthandbuch für eine Wissensdatenbank](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> Das Extrahieren funktioniert am besten bei Handbüchern, die über ein Inhaltsverzeichnis und/oder eine Indexseite verfügen und eine klare Struktur mit hierarchischen Überschriften aufweisen.

### <a name="brochures-guidelines-papers-and-other-files"></a>Broschüren, Richtlinien, Artikel und andere Dateien

Viele andere Arten von Dokumenten können ebenfalls verarbeitet werden, um QA-Paare zu generieren, vorausgesetzt, sie weisen eine klare Struktur und ein klares Layout auf. Das umfasst: Broschüren, Leitfäden, Berichte, Whitepaper, wissenschaftliche Veröffentlichungen, Richtlinien, Bücher usw. Ein Beispiel finden Sie [hier](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Unten sehen Sie ein Beispiel für ein semistrukturiertes Dokument ohne Index:

 ![Semistrukturiertes Azure Blob Storage-Dokument](../media/qnamaker-concepts-datasources/semi-structured-doc.png) 

### <a name="structured-qna-document"></a>Strukturiertes QnA-Dokument

Das Format für strukturierte Fragen/Antworten in DOC-Dateien besteht aus abwechselnden Fragen und Antworten pro Zeile: eine Frage pro Zeile, gefolgt von ihrer Antwort in der folgenden Zeile, wie unten gezeigt: 

```text
Question1

Answer1

Question2

Answer2
```

Unten sehen Sie ein Beispiel für ein strukturiertes QnA-Word-Dokument:

 ![Beispiel für ein strukturiertes QnA-Dokument für eine Wissensdatenbank](../media/qnamaker-concepts-datasources/structured-qna-doc.png) 

## <a name="structured-txt-tsv-and-xls-files"></a>Strukturierte *TXT*-, *TSV*- und *XLS*-Dateien

QnAs in Form von strukturierten *TXT*-, *TSV*- oder *XLS*-Dateien können auch in QnA Maker hochgeladen werden, um eine Wissensdatenbank zu erstellen oder zu erweitern.  Dabei kann es sich um Nur-Text-Dateien oder um Dateien mit Inhalten im RTF- oder HTML-Format handeln. 

| Frage  | Antwort  | Metadaten (1 Schlüssel: 1 Wert) |
|-----------|---------|-------------------------|
| Frage1 | Antwort1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Frage2 | Antwort2 |      `Key:Value`           |

Alle weiteren Spalten in der Quelldatei werden ignoriert.

Unten sehen Sie ein Beispiel für eine strukturierte QnA-*XLS*-Datei mit HTML-Inhalten:

 ![Beispiel für eine strukturierte QnA-Excel-Datei für eine Wissensdatenbank](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

## <a name="structured-data-format-through-import"></a>Strukturiertes Datenformat durch Import

Beim Importieren einer Wissensdatenbank wird der Inhalt der vorhandenen Wissensdatenbank ersetzt. Der Import erfordert eine strukturierte TSV-Datei, die Informationen zur Datenquelle enthält. Diese Informationen helfen QnA Maker beim Gruppieren der Frage-Antwort-Paare und dem Zuweisen zu einer bestimmten Datenquelle.

| Frage  | Antwort  | `Source`| Metadaten (1 Schlüssel: 1 Wert) |          
|-----------|---------|----|---------------------|
| Frage1 | Antwort1 | URL1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Frage2 | Antwort2 | Redaktionelle Änderung|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Redaktionelles Hinzufügen zur Wissensdatenbank

Wenn Sie nicht über bereits vorhandene Inhalte zum Füllen der Wissensdatenbank verfügen, können QnAs auch redaktionell zur QnA Maker-Wissensdatenbank hinzufügen. Informationen zum Aktualisieren Ihrer Wissensdatenbank finden Sie [hier](../How-To/edit-knowledge-base.md).

## <a name="formatting-considerations"></a>Überlegungen zur Formatierung

Nach dem Importieren einer Datei oder URL wird diese in Markdown konvertiert und in diesem Format gespeichert. Sollten Links in Ihren Dateien und URLs nicht ordnungsgemäß konvertiert werden, müssen die Fragen und Antworten auf der Seite **Bearbeiten** bearbeitet werden. 

|Format|Zweck|
|--|--|
|`\n\n`| Neue Zeile|
|`\n*`|Aufzählungspunkt für eine geordnete Liste|

## <a name="editing-your-knowledge-base-locally"></a>Lokales Bearbeiten Ihrer Wissensdatenbank

Nachdem eine Wissensdatenbank erstellt wurde, wird empfohlen, den Text der Wissensdatenbank im [QnA Maker-Portal](https://qnamaker.ai) zu bearbeiten, anstatt diesen in lokale Dateien zu exportieren und wieder zu importieren. Es kann jedoch vorkommen, dass Sie eine Wissensdatenbank lokal bearbeiten müssen. 

Exportieren Sie die Wissensdatenbank auf der Seite **Einstellungen**, und bearbeiten Sie die Wissensdatenbank dann mit Microsoft Excel. Wenn Sie eine andere Anwendung verwenden, um die exportierte TSV-Datei zu bearbeiten, fügt die Anwendung eventuell Syntaxfehler ein, da sie nicht vollständig TSV-konform ist. TSV-Dateien von Microsoft Excel fügen in der Regel keine Formatierungsfehler ein. 

Wenn Sie mit der Bearbeitung fertig sind, importieren Sie die TSV-Datei auf der Seite **Einstellungen**. Die aktuelle Wissensdatenbank wird dabei vollständig durch die importierte Wissensdatenbank ersetzt. 

## <a name="testing-your-markdown"></a>Testen Ihres Markdowncodes

Sie können Ihren Markdowncode anhand des Tutorials **[CommonMark](https://commonmark.org/help/tutorial/index.html)** überprüfen. Das Tutorial verfügt über eine Funktion **Ausprobieren** für die schnelle Überprüfung per Kopieren und Einfügen. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Einrichten eines QnA Maker-Diensts](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Weitere Informationen 

[Übersicht über QnA Maker](../Overview/overview.md)
