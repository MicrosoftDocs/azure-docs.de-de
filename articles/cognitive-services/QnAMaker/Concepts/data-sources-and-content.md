---
title: 'Datenquellen und Inhaltstypen: QnA Maker'
description: Erfahren Sie, wie Sie Frage-Antwort-Paare aus Datenquellen und unterstützten Inhaltstypen importieren können, zu denen viele strukturierte Standarddokumente wie PDF, DOCX und TXT gehören – QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 0d4d32aba34a97c6a060c999694f66d79933d011
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99556042"
---
# <a name="importing-from-data-sources"></a>Importieren aus Datenquellen

Eine Wissensdatenbank besteht aus Frage- und Antwortpaaren, die über öffentliche URLs und Dateien eingefügt werden.

## <a name="data-source-locations"></a>Speicherorte von Datenquellen

Der Inhalt wird aus einer Datenquelle in eine Wissensdatenbank übertragen. Speicherorte von Datenquellen sind **öffentliche URLs oder Dateien**, die keine Authentifizierung erfordern.

[SharePoint-Dateien](../how-to/add-sharepoint-datasources.md), die durch Authentifizierung geschützt sind, stellen eine Ausnahme dar. Bei SharePoint-Ressourcen muss es sich um Dateien und nicht um Webseiten handeln. Wenn die URL mit einer Weberweiterung endet (z. B. „.ASPX“), wird sie nicht von SharePoint in QnA Maker importiert.

## <a name="chit-chat-content"></a>Smalltalkinhalte

Smalltalkinhalte werden als vollständige Inhaltsdatenquelle in verschiedenen Sprachen und Unterhaltungsformaten bereitgestellt. Dies kann ein Ausgangspunkt für die Persönlichkeit Ihres Bots sein und Ihnen die Zeit und die Kosten ersparen, diese Elemente von Grund auf neu zu schreiben. Erfahren Sie, wie Sie Ihrer Wissensdatenbank [Smalltalkinhalte hinzufügen](../how-to/chit-chat-knowledge-base.md).

## <a name="structured-data-format-through-import"></a>Strukturiertes Datenformat durch Import

Beim Importieren einer Wissensdatenbank wird der Inhalt der vorhandenen Wissensdatenbank ersetzt. Der Import erfordert eine strukturierte `.tsv`-Datei, die Fragen und Antworten enthält. Diese Informationen helfen QnA Maker beim Gruppieren der Frage-Antwort-Paare und dem Zuweisen zu einer bestimmten Datenquelle.

| Frage  | Antwort  | `Source`| Metadaten (1 Schlüssel: 1 Wert) |
|-----------|---------|----|---------------------|
| Frage1 | Antwort1 | URL1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Frage2 | Antwort2 | Redaktionelle Änderung|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Strukturiertes Mehrfachdurchlauf-Format durch Import

Sie können Unterhaltungen mit Mehrfachdurchläufen in einem `.tsv`-Dateiformat erstellen. Das Format bietet Ihnen die Möglichkeit, die Unterhaltungen mit Mehrfachdurchläufen zu erstellen, indem Sie vorherige Chatprotokolle analysieren (mit anderen Prozessen, nicht mithilfe von QnA Maker), und dann die `.tsv`-Datei durch Automatisierung zu erstellen. Importieren Sie die Datei, um die vorhandene Wissensdatenbank zu ersetzen.

> [!div class="mx-imgBorder"]
> ![Konzeptionelles Modell mit drei Ebenen von Fragen mit Mehrfachdurchläufen](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

Die Spalte für eine `.tsv`-Datei mit Mehrfachdurchläufen, die sich speziell für Mehrfachdurchläufe eignet, ist **Eingabeaufforderungen**. Eine `.tsv`-Beispieldatei in Excel zeigt die Informationen, die zum Definieren der untergeordneten Elemente bei Mehrfachdurchläufen eingeschlossen werden müssen:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**displayOrder** ist numerisch, während **displayText** ein Text ist, der kein Markdown enthalten darf.

> [!div class="mx-imgBorder"]
> ![Beispiel für eine Frage für Mehrfachdurchläufe in Excel](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Exportieren als Beispiel

Wenn Sie nicht sicher sind, wie Sie Ihr QnA-Paar in der Datei vom Typ `.tsv` darstellen, gehen Sie wie folgt vor:
* Verwenden Sie [dieses Beispiel](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true) zum Herunterladen von GitHub.
* Oder erstellen Sie das Paar im QnA Maker-Portal, speichern Sie es, und exportieren Sie dann die Wissensdatenbank, um ein Beispiel für die Darstellung des Paars zu erhalten.

## <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Inhaltstypen von Dokumenten, die Sie einer Wissensdatenbank hinzufügen können
Zu den Inhaltstypen gehören viele strukturierte Standarddokumente wie PDF, DOC und TXT.

### <a name="file-and-url-data-types"></a>Datei- und URL-Datentypen

Die folgende Tabelle fasst die Inhaltstypen und Dateiformate zusammen, die von QnA Maker unterstützt werden.

|Quellentyp|Inhaltstyp| Beispiele|
|--|--|--|
|URL|Häufig gestellte Fragen<br> (flach, mit Abschnitten oder mit einer Themenstartseite)<br>Supportseiten <br> (kurze Anleitungen, Problembehandlungsartikel und Ähnliches)|[Einfache FAQ](../troubleshooting.md), <br>[FAQ mit Links](https://www.microsoft.com/en-us/software-download/faq),<br> [FAQ mit Themenstartseite](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Supportartikel](./best-practices.md)|
|PDF/DOC|Häufig gestellte Fragen,<br> Produkthandbuch,<br> Broschüren,<br> Artikel,<br> Flyerrichtlinie,<br> Supporthandbuch,<br> strukturierte QnA<br> usw.|**Ohne Mehrfachdurchläufe**<br>[Strukturiertes QnA-Dokument (DOCX-Datei)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Beispielprodukthandbuch (PDF-Datei)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Semistrukturiertes Beispiel (DOCX-Datei)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Beispielwhitepaper (PDF-Datei)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)<br><br>**Mehrfachdurchläufe**:<br>[Surface Pro (DOCX)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso Benefits (DOCX)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso Benefits (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|*Excel|Strukturierte QnA-Datei<br> (einschließlich RTF- und HTML-Unterstützung)|**Ohne Mehrfachdurchläufe**:<br>[QnA-Beispiel-FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>**Mehrfachdurchläufe**:<br>[Strukturierte einfache FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[Surface-Laptop-FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
|*TXT/TSV|Strukturierte QnA-Datei|[Beispielgeplauder.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Wenn eine Authentifizierung für Ihre Datenquelle erforderlich ist, ziehen Sie die folgenden Methoden in Betracht, um diese Inhalte in QnA Maker aufzunehmen:

* Manuelles Herunterladen und Importieren der Datei in QnA Maker
* Hinzufügen der Datei aus einem authentifizierten [SharePoint-Speicherort](../how-to/add-sharepoint-datasources.md)

### <a name="url-content"></a>URL-Inhalt

Zwei Arten von Dokumenten können über die **URL** in QnA Maker importiert werden:

* FAQ-URLs
* Support-URLs

Jeder Typ gibt ein erwartetes Format an.

### <a name="file-based-content"></a>Dateibasierte Inhalte

Sie können im [QnA Maker-Portal](https://www.qnamaker.ai) einer Wissensdatenbank Dateien aus einer öffentlichen Quelle oder Ihrem lokalen Dateisystem hinzufügen.

### <a name="content-format-guidelines"></a>Richtlinien zum Inhaltsformat

Erfahren Sie mehr über die [Formatierungsrichtlinien](../reference-document-format-guidelines.md) für die verschiedenen Dateien.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [QnAs bearbeiten](../how-to/edit-knowledge-base.md).
