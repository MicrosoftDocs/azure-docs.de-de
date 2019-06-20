---
title: Was ist der Sprachübersetzungsdienst?
titleSuffix: Azure Cognitive Services
description: Verwenden Sie die Sprachübersetzungsdienst-API, um Ihre Anwendungen mit Sprache-in-Sprache- und Sprache-in-Text-Übersetzungen auszustatten.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: overview
ms.date: 3/5/2018
ms.author: v-jansko
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 24014bb06a779c214f18f966dfb1d26d61adee8d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "60827516"
---
# <a name="what-is-translator-speech-api"></a>Was ist die Sprachübersetzungs-API?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Mit der Sprachübersetzungs-API können Sie Anwendungen, Tools und Lösungen, die eine Sprachübersetzung für mehrere Sprachen erfordern, unabhängig vom Zielbetriebssystem oder den Entwicklungssprachen mit umfassenden Sprachübersetzungen in Echtzeit ausstatten. Die API kann sowohl für Sprache-zu-Sprache-Übersetzungen als auch für Sprache-zu-Text-Übersetzungen verwendet werden.

Die Textübersetzungs-API ist ein Azure-Dienst und gehört zur [API-Sammlung von Azure Cognitive Services](https://docs.microsoft.com/azure/). Hierbei handelt es sich um eine Sammlung von Machine Learning- und KI-Algorithmen in der Cloud, die Sie direkt in Ihren Entwicklungsprojekten verwenden können.

Mit der Sprachübersetzungs-API streamen Clientanwendungen Audio an den Dienst und empfangen einen Stream mit text- und audiobasierten Ergebnissen. Diese umfassen den erkannten Text in der Ausgangssprache und die entsprechende Übersetzung in der Zielsprache. Zur Generierung der Textergebnisse wird auf den eingehenden Audiostream eine auf neuronalen Netzwerken basierende automatische Spracherkennung (Automatic Speech Recognition, ASR) angewendet. Eine unformatierte ASR-Ausgabe wird mithilfe einer neuen Technik namens TrueText weiter verbessert, um die Benutzerabsicht noch besser zu erfassen. So entfernt TrueText beispielsweise Elemente, die den Textfluss stören (etwa „Hmm“ und Husten), sowie Wortwiederholungen und sorgt für eine ordnungsgemäße Interpunktion und Großschreibung. Es besteht auch die Möglichkeit, anstößige Ausdrücke zu maskieren oder auszuschließen. Die Erkennungs- und Übersetzungsengines werden speziell für die Verarbeitung von Konversationen trainiert.

Der Sprachübersetzungsdienst verwendet die Erkennung von Stille, um das Ende einer Äußerung zu bestimmen. Nach einer Sprechpause gibt der Dienst mittels Streaming ein Endergebnis der abgeschlossenen Äußerung zurück. Der Dienst kann auch Teilergebnisse zurückgeben, die Zwischeninformationen zu Erkennungen und Übersetzungen einer noch nicht abgeschlossenen Äußerung liefern.

Bei Sprache-zu-Sprache-Übersetzungen bietet der Dienst die Möglichkeit, Sprache aus dem gesprochenen Text in den Zielsprachen zu synthetisieren (Text-to-Speech). Das Audio der Sprachsynthese wird im vom Client angegebenen Format erstellt. Verfügbare Formate sind WAV und MP3.

Die Sprachübersetzungs-API verwendet für die Bereitstellung eines Vollduplex-Kommunikationskanals zwischen dem Client und dem Server das WebSocket-Protokoll.

## <a name="about-microsoft-translator"></a>Informationen zu Microsoft Translator
Microsoft Translator ist ein cloudbasierter Übersetzungsdienst. Das Herzstück dieses Diensts bilden die [Textübersetzungs-API](https://www.microsoft.com/en-us/translator/translatorapi.aspx) und die Sprachübersetzungs-API, die in verschiedensten Produkten und Diensten von Microsoft zum Einsatz kommen und in Anwendungen und Workflows von Tausenden von Unternehmen auf der ganzen Welt genutzt werden, die mit ihren Inhalten ein globales Publikum erreichen möchten.

Weitere Informationen zum Microsoft Translator-Dienst finden Sie [hier](https://www.microsoft.com/en-us/translator/home.aspx).

## <a name="microsoft-translator-neural-machine-translation-nmt"></a>Neuronale maschinelle Übersetzungen (NMT) von Microsoft Translator
Die Sprachübersetzungs-API verwendet sowohl die ältere statistische Maschinenübersetzung (Statistical Machine Translation, SMT) als auch die neuere neuronale maschinelle Übersetzung (Neural Machine Translation, NMT), um Übersetzungen bereitzustellen.

Die Leistung der statistischen Maschinenübersetzung hat ihren Zenit erreicht: Die Übersetzungsqualität lässt sich bei generischen Systemen mit SMT nicht mehr nennenswert verbessern. Dafür ist eine neue Übersetzungstechnologie auf dem Vormarsch, die auf künstlicher Intelligenz und neuronale Netzwerken (NN) basiert.

Im Vergleich zu SMT liefert NMT bessere Übersetzungen – nicht nur im Hinblick auf die grundsätzliche Übersetzungsqualität, sondern auch im Hinblick auf Textfluss und Natürlichkeit.
Der Hauptgrund für diesen Textfluss besteht darin, dass NMT bei der Übersetzung von Wörtern den gesamten Kontext eines Satzes berücksichtigt. SMT berücksichtigt dagegen nur den unmittelbaren Kontext weniger Wörter vor und nach jedem Wort.

NMT-Modelle sind das Herzstück der API und für Endbenutzer nicht sichtbar. Sie machen sich einzig durch Folgendes bemerkbar:
* Höhere Übersetzungsqualität – insbesondere für Sprachen wie Chinesisch, Japanisch und Arabisch
* Inkompatibilität mit den vorhandenen Hub-Anpassungsfeatures (zur Verwendung mit der Textübersetzungs-API von Microsoft)

Alle unterstützten Sprachen für die Sprachübersetzung basieren auf NMT. Daher kommt bei allen Sprache-zu-Sprache-Übersetzungen NMT zum Einsatz.

Bei Sprache-zu-Text-Übersetzungen kann je nach Sprachpaar eine Kombination aus SMT und NMT verwendet werden. Wenn die Zielsprache von NMT unterstützt wird, wird die gesamte Übersetzung über NMT abgewickelt. Wenn die Zielsprache nicht von NMT unterstützt wird, wird für die Übersetzung eine Kombination aus NMT und SMT mit Englisch als „Pivot“ zwischen den beiden Sprachen verwendet.

Die unterstützten Sprachen finden Sie auf [Microsoft.com](https://www.microsoft.com/en-us/translator/languages.aspx).

Weitere Informationen zur Funktionsweise von NMT finden Sie [hier](https://www.microsoft.com/en-us/translator/mt.aspx#nnt).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Registrieren](translator-speech-how-to-signup.md)

> [!div class="nextstepaction"]
> [Programmieren](quickstarts/csharp.md)

## <a name="see-also"></a>Weitere Informationen
- [Dokumentationsseite zu Cognitive Services](https://docs.microsoft.com/azure/)
- [Produktseite zu Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
- [Lösungs- und Preisinformationen](https://www.microsoft.com/en-us/translator/home.aspx)
