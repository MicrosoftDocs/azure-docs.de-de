---
title: Richtlinien für menschenmarkierte Transkriptionen – Spracherkennungsdienst
titleSuffix: Azure Cognitive Services
description: Um die Genauigkeit der Spracherkennung zu verbessern, z. B. wenn Wörter gelöscht oder falsch ersetzt werden, können Sie menschenmarkierte Transkriptionen zusammen mit Ihren Audiodaten verwenden. Menschenmarkierte Transkriptionen sind wortwörtliche Transkriptionen einer Audiodatei.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 4516bbddd3fda593021288a440e1b354d4d7f1e3
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340236"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Erstellen von menschenmarkierten Transkriptionen

Wenn Sie die Genauigkeit der Spracherkennung verbessern möchten, insbesondere bei Problemen, die durch Löschen oder falsches Ersetzen von Wörtern verursacht werden, können Sie menschenmarkierte Transkriptionen zusammen mit Ihren Audiodaten verwenden. Doch was sind menschenmarkierte Transkriptionen? Ganz einfach: Es sind wortwörtliche Transkriptionen einer Audiodatei.

Zum Verbessern der Erkennung ist eine umfangreiche Stichprobe von Transkriptionsdaten erforderlich. Es wird empfohlen, zwischen 10 und 1.000 Stunden an Transkriptionsdaten bereitzustellen. Auf dieser Seite werden Richtlinien besprochen, die Ihnen beim Erstellen qualitativ hochwertiger Transkriptionen helfen sollen. Dieser Leitfaden ist nach Gebietsschema unterteilt und enthält Abschnitte für Englisch (USA), Chinesisch (Mandarin) und Deutsch.

## <a name="us-english-en-us"></a>Englisch (USA) (en-US)

Menschenmarkierte Transkriptionen für Audioaufnahmen in Englisch müssen als Nur-Text ausschließlich in ASCII-Zeichen bereitgestellt werden. Vermeiden Sie die Verwendung von Lateinisch-1- oder Unicode-Interpunktionszeichen. Diese Zeichen werden oft versehentlich beim Kopieren von Text aus einem Textverarbeitungsprogramm oder beim Erfassen von Daten aus Webseiten hinzugefügt. Wenn solche Zeichen vorhanden sind, stellen Sie sicher, dass sie durch die entsprechenden ASCII-Zeichen ersetzt werden.

Hier sind einige Beispiele:

| Nicht zu verwendende Zeichen | Ersetzung | Notizen |
| ------------------- | ------------ | ----- |
| “Hello world” | "Hello world" | Die öffnenden und schließenden Anführungszeichen wurden durch entsprechende ASCII-Zeichen ersetzt. |
| John’s day | John's day | Das Apostroph wurde durch das entsprechende ASCII-Zeichen ersetzt. |
| it was good—no, it was great! | it was good--no, it was great! | Der Gedankenstrich wurde durch zwei Bindestriche ersetzt. |

### <a name="text-normalization-for-us-english"></a>Textnormalisierung für Englisch (USA)

Textnormalisierung ist die Transformation von Wörtern in ein konsistentes Format, das beim Trainieren eines Modells verwendet wird. Einige Normalisierungsregeln werden automatisch auf Text angewendet, doch wird empfohlen, beim Vorbereiten der menschenmarkierten Transkriptionsdaten die folgenden Richtlinien zu beachten:

- Schreiben Sie Abkürzungen in Wörtern aus.
- Schreiben Sie nicht standardmäßige numerische Zeichenfolgen in Wörtern aus (z.B. Buchhaltungsbegriffe).
- Nicht alphabetische Zeichen oder gemischte alphanumerische Zeichen müssen entsprechend ihrer Aussprache transkribiert werden.
- Abkürzungen, die als Wörter ausgesprochen werden, dürfen nicht bearbeitet werden (z.B. „Radar“, „Laser“, „RAM“ oder „NATO“).
- Schreiben Sie Abkürzungen, die als einzelne Buchstaben ausgesprochen werden, mit jeweils einem Leerzeichen zwischen den einzelnen Buchstaben aus.

Es folgen einige Beispiele für die Normalisierung, die Sie für die Transkription durchführen sollten:

| Ursprünglicher Text               | Text nach der Normalisierung              |
| --------------------------- | ------------------------------------- |
| Dr. Bruce Banner            | Doctor Bruce Banner                   |
| James Bond, 007             | James Bond, double oh seven           |
| Ke$ha                       | Kesha                                 |
| How long is the 2x4         | How long is the two by four           |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| My blood type is O+         | My blood type is O positive           |
| Water is H20                | Water is H 2 O                        |
| Play OU812 by Van Halen     | Play O U 8 1 2 by Van Halen           |
| UTF-8 with BOM              | U T F 8 with BOM                      |

Die folgenden Normalisierungsregeln werden automatisch auf Transkriptionen angewendet:

- Verwenden von Kleinbuchstaben
- Entfernen aller Interpunktionszeichen außer Apostrophen in Wörtern
- Erweitern von Zahlen in Wörter/gesprochene Form, z.B. Dollarangaben

Es folgen einige Beispiele für die Normalisierung, die automatisch für die Transkription durchgeführt wird:

| Ursprünglicher Text                          | Text nach der Normalisierung          |
| -------------------------------------- | --------------------------------- |
| "Holy cow!" said Batman.               | holy cow said batman              |
| "What?" said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em!                            | go get em                         |
| I'm double-jointed                     | I'm double jointed                |
| 104 Elm Street                         | one oh four Elm street            |
| Tune to 102.7                          | tune to one oh two point seven    |
| Pi is about 3.14                       | pi is about three point one four  |
| It costs \$3.14                        | it costs three fourteen           |

## <a name="mandarin-chinese-zh-cn"></a>Chinesisch (Mandarin) (zh-CN)

Menschenmarkierte Transkriptionen für Audioaufnahmen in Chinesisch (Mandarin) müssen UTF-8-codiert sein und eine Bytereihenfolge-Marke aufweisen. Vermeiden Sie die Verwendung von Interpunktionszeichen halber Breite. Diese Zeichen können unbeabsichtigt eingefügt werden, wenn die Daten in einem Textverarbeitungsprogramm vorbereitet oder aus Webseiten erfasst werden. Wenn solche Zeichen vorhanden sind, stellen Sie sicher, dass sie durch die entsprechenden Zeichen normaler Breite ersetzt werden.

Hier sind einige Beispiele:

| Nicht zu verwendende Zeichen | Ersetzung   | Notizen |
| ------------------- | -------------- | ----- |
| "你好" | "你好" | Die öffnenden und schließenden Anführungszeichen wurden durch entsprechende Zeichen ersetzt. |
| 需要什么帮助? | 需要什么帮助？| Das Fragezeichen wurde durch das entsprechende Zeichen ersetzt. |

### <a name="text-normalization-for-mandarin-chinese"></a>Textnormalisierung für Chinesisch (Mandarin)

Textnormalisierung ist die Transformation von Wörtern in ein konsistentes Format, das beim Trainieren eines Modells verwendet wird. Einige Normalisierungsregeln werden automatisch auf Text angewendet, doch wird empfohlen, beim Vorbereiten der menschenmarkierten Transkriptionsdaten die folgenden Richtlinien zu beachten:

- Schreiben Sie Abkürzungen in Wörtern aus.
- Schreiben Sie numerische Zeichenfolgen wie in gesprochener Form aus.

Es folgen einige Beispiele für die Normalisierung, die Sie für die Transkription durchführen sollten:

| Ursprünglicher Text | Text nach der Normalisierung |
| ------------- | ------------------------ |
| 我今年 21 | 我今年二十一 |
| 3 号楼 504 | 三号 楼 五 零 四 |

Die folgenden Normalisierungsregeln werden automatisch auf Transkriptionen angewendet:

- Entfernen aller Interpunktionszeichen
- Erweitern von Zahlen in die gesprochene Form
- Konvertieren von Buchstaben normaler Breite in Buchstaben halber Breite
- Verwenden von Großbuchstaben für alle englischen Wörter

Es folgen einige Beispiele für die Normalisierung, die automatisch für die Transkription durchgeführt wird:

| Ursprünglicher Text | Text nach der Normalisierung |
| ------------- | ------------------------ |
| 3.1415 | 三 点 一 四 一 五 |
| ￥ 3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992 年 8 月 8 日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午 5:00 的航班 | 下午 五点 的 航班 |
| 我今年 21 岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Deutsch (de-DE) und andere Sprachen

Menschenmarkierte Transkriptionen für Audioaufnahmen in Deutsch (sowie anderen Sprachen außer Englisch oder Mandarin) müssen UTF-8-codiert sein und eine Bytereihenfolge-Marke aufweisen. Für jede Audiodatei muss ein menschenmarkiertes Transkript bereitgestellt werden.

### <a name="text-normalization-for-german"></a>Textnormalisierung für Deutsch

Textnormalisierung ist die Transformation von Wörtern in ein konsistentes Format, das beim Trainieren eines Modells verwendet wird. Einige Normalisierungsregeln werden automatisch auf Text angewendet, doch wird empfohlen, beim Vorbereiten der menschenmarkierten Transkriptionsdaten die folgenden Richtlinien zu beachten:

- Schreiben Sie Dezimaltrennzeichen als Komma (,) und nicht als Punkt (.).
- Schreiben Sie Zeittrennzeichen als Doppelpunkt (:) und nicht als Punkt (.) (Beispiel: 12:00 Uhr).
- Abkürzungen wie „ca.“ werden nicht ersetzt. Es empfiehlt sich, die vollständige gesprochene Form zu verwenden.
- Die vier wichtigsten mathematischen Operatoren (+, -, \* und /) werden entfernt. Es wird empfohlen, sie durch die jeweilige Schriftform zu ersetzen: „plus“, „minus“, „mal“ und „geteilt“.
- Vergleichsoperatoren werden entfernt (=, < und >). Wir empfehlen, sie durch „gleich“, „kleiner als“ und „grösser als“ zu ersetzen.
- Schreiben Sie Brüche wie 3/4 in Schriftform (Beispiel: „drei viertel“ anstatt 3/4).
- Ersetzen Sie das Symbol „€“ durch die Schriftform „Euro“.

Es folgen einige Beispiele für die Normalisierung, die Sie für die Transkription durchführen sollten:

| Ursprünglicher Text    | Text nach Normalisierung durch den Benutzer | Text nach Normalisierung durch das System       |
| ---------------- | ----------------------------- | ------------------------------------- |
| Es ist 12.23 Uhr | Es ist 12:23 Uhr              | es ist zwölf uhr dreiundzwanzig |
| {12,45}          | {12,45}                       | zwölf komma vier fünf                 |
| 2 + 3 - 4        | 2 plus 3 minus 4              | zwei plus drei minus vier             |

Die folgenden Normalisierungsregeln werden automatisch auf Transkriptionen angewendet:

- Verwenden von Kleinbuchstaben für sämtlichen Text
- Entfernen aller Interpunktionszeichen, einschließlich verschiedener Typen von Anführungszeichen ("test", 'test', „test“ oder «test» können verwendet werden)
- Verwerfen aller Zeilen mit Sonderzeichen des folgenden Zeichensatzes: ¢ ¤ ¥ ¦ § © ª ¬ ® ° ± ² µ × ÿ Ø¬¬
- Erweitern von Zahlen in die Schriftform, einschließlich Dollar-/Euroangaben
- Akzeptieren von Umlauten nur für a, o und u; andere werden durch „th“ ersetzt oder verworfen

Es folgen einige Beispiele für die Normalisierung, die automatisch für die Transkription durchgeführt wird:

| Ursprünglicher Text    | Text nach der Normalisierung |
| ---------------- | ------------------------ |
| Frankfurter Ring | frankfurter ring         |
| ¡Eine Frage!     | eine frage               |
| wir, haben       | wir haben                |

## <a name="next-steps"></a>Nächste Schritte

- [Vorbereiten und Testen Ihrer Daten](./how-to-custom-speech-test-and-train.md)
- [Überprüfen Ihrer Daten](how-to-custom-speech-inspect-data.md)
- [Bewerten Ihrer Daten](how-to-custom-speech-evaluate-data.md)
- [Trainieren Ihres Modells](how-to-custom-speech-train-model.md)
- [Bereitstellen Ihres Modells](./how-to-custom-speech-train-model.md)