---
title: Markdown-Format – QnA Maker
description: Im Folgenden finden Sie eine Liste der Markdown-Formate, die Sie im Antworttext von QnA Maker verwenden können.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 03/19/2020
ms.openlocfilehash: f49e05578f55a38845acbd4010f928fb17c51606
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352268"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>Im Antworttext von QnA Maker unterstütztes Markdown-Format

QnA Maker speichert den Antworttext im Markdown-Format. Es gibt viele Varianten des Markdown-Formats. Um sicherzustellen, dass der Antworttext ordnungsgemäß zurückgegeben und angezeigt wird, verwenden Sie diese Referenz.

Sie können Ihren Markdowncode anhand des Tutorials **[CommonMark](https://commonmark.org/help/tutorial/index.html)** überprüfen. Das Tutorial verfügt über eine Funktion **Ausprobieren** für die schnelle Überprüfung per Kopieren und Einfügen.

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>Vergleich der Anwendungsfälle für Rich-Text-Bearbeitung und Markdown

Mit der [Rich-Text-Bearbeitung](How-To/edit-knowledge-base.md#add-an-editorial-qna-set) von Antworten können Sie als Autor die Formatierungssymbolleiste verwenden, um Text schnell auszuwählen und zu formatieren.

Markdown eignet sich besser, wenn Sie Inhalte automatisch generieren müssen, um Wissensdatenbanken zu erstellen, die im Rahmen einer CI/CD-Pipeline oder für [Batchtests](./index.yml) importiert werden.

## <a name="supported-markdown-format"></a>Unterstütztes Markdown-Format

Im Folgenden finden Sie eine Liste der Markdown-Formate, die Sie im Antworttext von QnA Maker verwenden können.

|Zweck|Format|Beispielmarkdown|Darstellung<br>wie im Chatbot angezeigt|
|--|--|--|--|
Eine neue Zeile zwischen zwei Sätzen.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![Neue Zeile zwischen zwei Sätzen formatieren](./media/qnamaker-concepts-datasources/format-newline.png)|
|Header von h1 bis h6, die Anzahl der `#` gibt den Header an. 1 `#` ist h1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![Mit Markdown-Headern formatieren](./media/qnamaker-concepts-datasources/format-headers.png)<br>![Mit Markdown-Headern h1 bis h5 formatieren](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Kursiv |`*text*`|`How do I create a bot with *QnA Maker*?`|![Kursiv formatieren](./media/qnamaker-concepts-datasources/format-italics.png)|
|Strong (fett)|`**text**`|`How do I create a bot with **QnA Maker**?`|![Mit starker Markierung fett formatieren](./media/qnamaker-concepts-datasources/format-strong.png)|
|URL für einen Link|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![Für URL (Hyperlink) formatieren](./media/qnamaker-concepts-datasources/format-url.png)|
|*URL für öffentliches Bild|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![Für öffentliche Bild-URL formatieren ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|Durchgestrichen|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![Durchgestrichen formatieren](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Fett und kursiv|`***text**_`|`How can I create a _*_QnA Maker_*_ bot?`|![Fett und kursiv formatieren](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|Fette URL für einen Link|`[_*text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![Fett formatierte URL](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|Kursive URL für Link|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![Kursive URL formatieren](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|Markdown-Escapesymbole|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![Format für Markdown-Escapesymbole](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Sortierte Liste|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>Im vorangehenden Beispiel wird die in Markdown integrierte automatische Nummerierung verwendet.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>Im vorangehenden Beispiel wird die explizite Nummerierung verwendet.|![Geordnete Liste formatieren](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Unsortierte Liste|`\n * item1 \n * item2`<br>oder<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![Unsortierte Liste](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Geschachtelte Listen|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>Sie können sortierte und unsortierte Listen schachteln. Der Tabstopp `\t` gibt die Einzugsebene des untergeordneten Elements an.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![Unsortierte geschachtelte Liste formatieren](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![Sortierte geschachtelte Liste formatieren](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

*QnA Maker verarbeitet das Bild nicht. Die Clientanwendung ist für das Rendern des Bilds zuständig.

Wenn Sie Inhalte mithilfe von Wissensdatenbank-APIs aktualisieren/ersetzen möchten und der Inhalt bzw. die Datei HTML-Tags enthält, können Sie HTML in Ihrer Datei beibehalten, indem Sie sicherstellen, dass die öffnenden und schließenden Tags in das codierte Format konvertiert werden.

| Beibehalten von HTML  | Darstellung in der API-Anforderung  | Darstellung in Wissensdatenbank |
|-----------|---------|-------------------------|
| Ja | \&lt;br\&gt; | &lt;br&gt; |
| Ja | \&lt;h3\&gt;header\&lt;/h3\&gt; | &lt;h3&gt;header&lt;/h3&gt; |

Außerdem werden CR LF(\r\n) in der Wissensdatenbank in \n konvertiert. LF(\n) bleibt unverändert. Wenn Sie eine Escapesequenz wie \t oder \n als Escapezeichen verwenden möchten, können Sie einen Schrägstrich verwenden. Beispiel: '\\\\r\\\\n' und '\\\\t'

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über [Dateiformate](reference-tsv-format-batch-testing.md) für Batchtests.