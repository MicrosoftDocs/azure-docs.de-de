---
title: Anforderungslimits – Textübersetzungs-API
titleSuffix: Azure Cognitive Services
description: In diesem Artikel werden die Anforderungslimits für die Textübersetzungs-API aufgeführt. Die Gebühren werden basierend auf der Anzahl der Zeichen berechnet, nicht nach der Anforderungshäufigkeit, mit einem Grenzwert von 5.000 Zeichen pro Anforderung. Die Zeichengrenzwerte sind abonnementbasiert, wobei F0 auf 2 Millionen Zeichen pro Stunde beschränkt ist.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: c19e39918aa64730a35a27fcdadd70800f47f4fa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514651"
---
# <a name="request-limits-for-translator-text"></a>Anforderungslimits für die Textübersetzungs-API

In diesem Artikel werden die Drosselungslimits für die Textübersetzungs-API aufgeführt. Zu den Diensten gehören Übersetzung, Transliteration, Satzlängenerkennung, Spracherkennung und alternative Übersetzungen.

## <a name="character-and-array-limits-per-request"></a>Zeichen- und Arraygrenzwerte pro Anforderung

Jede Übersetzungsanforderung (Translate) kann maximal 5.000 Zeichen lang sein. Die Abrechnung erfolgt nach der Anzahl der Zeichen, nicht nach der Anzahl der Anforderungen. Es wird empfohlen, kürzere Anforderungen zu senden.

In der folgenden Tabelle sind Arrayelement- und Zeichengrenzwerte für jeden Vorgang der Textübersetzungs-API aufgeführt.

| Vorgang | Maximale Größe des Arrayelements |   Maximale Anzahl von Arrayelementen |  Maximale Anforderungsgröße (Zeichen) |
|:----|:----|:----|:----|
| Translate | 5\.000 | 100   | 5\.000 |
| Transliterate | 5\.000 | 10    | 5\.000 |
| Detect | 10.000 | 100 |   50.000 |
| BreakSentence | 10.000    | 100 | 5\.0000 |
| Wörterbuchsuche| 100 |  10  | 1\.000 |
| Wörterbuchbeispiele | 100 für Text und 100 für Übersetzung (200 insgesamt)| 10|   2\.000 |

## <a name="character-limits-per-hour"></a>Zeichengrenzwerte pro Stunde

Ihre Zeichengrenzwerte pro Stunde basieren auf der Abonnementebene der Textübersetzungs-API. Das Stundenkontingent sollte gleichmäßig über die gesamte Stunde verbraucht werden. Wenn Sie diese Grenzwerte erreichen oder überschreiten oder einen zu großen Teil des Kontingents in kurzer Zeit senden, werden Sie wahrscheinlich eine Antwort zur Kontingentüberschreitung erhalten.

| Tarif | Zeichengrenzwert |
|------|-----------------|
| F0 | 2 Millionen Zeichen pro Stunde |
| S1 | 40 Millionen Zeichen pro Stunde |
| S2/C2 | 40 Millionen Zeichen pro Stunde |
| S3/C3 | 120 Millionen Zeichen pro Stunde |
| S4/C4 | 200 Millionen Zeichen pro Stunde |

Die Grenzwerte für [Abonnements mit mehreren Diensten](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) sind dieselben wie für den S1-Tarif.

Diese Grenzwerte sind auf die Standardübersetzungsmodelle von Microsoft beschränkt. Für benutzerdefinierte Übersetzungsmodelle, die den benutzerdefinierten Translator verwenden, gilt ein Grenzwert von 1.800 Zeichen pro Sekunde.

## <a name="latency"></a>Latency

Die maximale Latenz der Textübersetzungs-API beträgt bei Verwendung von Standardmodellen 15 Sekunden. Bei der Übersetzung mit benutzerdefinierten Modellen beträgt die maximale Latenz 25 Sekunden. Bis zu diesem Zeitpunkt müssen Sie ein Ergebnis oder eine Zeitlimitantwort erhalten haben. In der Regel werden die Antworten innerhalb von 150 Millisekunden bis 300 Millisekunden zurückgegeben. Die Antwortzeiten variieren je nach der Größe der Anforderung und dem Sprachpaar. Wenn Sie innerhalb dieses Zeitrahmens keine Übersetzung oder eine [Fehlerantwort](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) erhalten, überprüfen Sie die Netzwerkverbindung, und versuchen Sie es erneut.

## <a name="sentence-length-limits"></a>Grenzwerte bei der Satzlänge

Bei Verwendung der [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence)-Funktion ist die Satzlänge auf 275 Zeichen beschränkt. Es gelten Ausnahmen für diese Sprachen:

| Sprache | Code | Zeichengrenzwert |
|----------|------|-----------------|
| Chinesisch | zh | 132 |
| Deutsch | de | 290 |
| Italienisch | it | 280 |
| Japanisch | ja | 150 |
| Portugiesisch | pt | 290 |
| Spanisch | es | 280 |
| Italienisch | it | 280 |
| Thailändisch | th | 258 |

> [!NOTE]
> Dieser Grenzwert gilt nicht für Übersetzungen.

## <a name="next-steps"></a>Nächste Schritte

* [Preise](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Regionale Verfügbarkeit](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [Referenz für Version 3 der Textübersetzungs-API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
