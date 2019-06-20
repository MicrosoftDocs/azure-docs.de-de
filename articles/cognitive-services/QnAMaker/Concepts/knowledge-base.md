---
title: 'Wissensdatenbank: QnA Maker'
titleSuffix: Azure Cognitive Services
description: Eine QnA Maker-Wissensdatenbank besteht aus einer Reihe von Frage-Antwort-Paaren (QnA) und optionalen Metadaten, die jedem QnA-Paar zugeordnet sind.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/04/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 02111ac90fe97ddaddbd41ad42410e7e76f1c405
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61379268"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Was ist eine QnA Maker-Wissensdatenbank?

Eine QnA Maker-Wissensdatenbank besteht aus einer Reihe von Frage-Antwort-Paaren (QnA) und optionalen Metadaten, die jedem QnA-Paar zugeordnet sind.

## <a name="key-knowledge-base-concepts"></a>Wichtige Konzepte für Wissensdatenbanken

* **Fragen**: Eine Frage enthält Text, der eine Benutzerabfrage am besten darstellt. 
* **Antworten**: Eine Antwort ist die Reaktion, die zurückgegeben wird, wenn eine Benutzerabfrage mit der zugehörigen Frage übereinstimmt.  
* **Metadaten**: Metadaten sind Tags, die einem QnA-Paar zugeordnet sind und als Schlüssel-Wert-Paare dargestellt werden. Metadatentags werden verwendet, um QnA-Paare zu filtern und die Datenmenge einzuschränken, für die ein Abfrageabgleich vorgenommen wird.

Eine einzelnes QnA-Element, dargestellt durch eine numerische QnA-ID, verfügt über mehrere Varianten einer Frage (alternative Fragen), die alle auf einer einzigen Antwort abgebildet werden. Außerdem können jedem dieser Paare mehrere Metadatenfelder zugeordnet sein: ein Schlüssel und ein Wert.

![QnA Maker-Wissensdatenbanken](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Inhaltsformat der Wissensdatenbank

Wenn Sie reichhaltige Inhalte in einer Wissensdatenbank erfassen, versucht QnA Maker, die Inhalte in Markdown zu konvertieren. Lesen Sie [diesen](https://aka.ms/qnamaker-docs-markdown-support) Blog, um die Markdownformate zu verstehen, die von den meisten Chatclients verstanden werden.

Metadatenfelder bestehen aus Schlüssel-Wert-Paaren, die durch einen Doppelpunkt **(Produkt:Aktenvernichter)** getrennt werden. Schlüssel und Wert müssen ausschließlich aus Text bestehen. Der Metadatenschlüssel darf keine Leerzeichen enthalten. Metadaten unterstützt nur einen Wert pro Schlüssel.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Entwicklungslebenszyklus einer Wissensdatenbank](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Weitere Informationen

[Übersicht über QnA Maker](../Overview/overview.md)
