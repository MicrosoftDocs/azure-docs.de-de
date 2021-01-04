---
title: Vorbereiten von Daten für Custom Speech – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Wenn Sie die Genauigkeit der Spracherkennung von Microsoft testen oder Ihre benutzerdefinierten Modelle trainieren möchten, benötigen Sie Audio- und Textdaten. Auf dieser Seite werden die einzelnen Datentypen, ihre Verwendung und ihre Verwaltung beschrieben.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: a5457dc94082f089d3adf02c9614d05d2c5db244
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484004"
---
# <a name="prepare-data-for-custom-speech"></a>Vorbereiten von Daten für Custom Speech

Wenn Sie die Genauigkeit der Spracherkennung von Microsoft testen oder Ihre benutzerdefinierten Modelle trainieren möchten, benötigen Sie Audio- und Textdaten. Auf dieser Seite werden die Datentypen behandelt, die für ein benutzerdefiniertes Sprachmodell erforderlich sind.

## <a name="data-diversity"></a>Vielfältigkeit der Daten

Text- und Audiodaten zum Testen und Trainieren eines benutzerdefinierten Modells müssen Beispiele von unterschiedlichen Sprechern und Szenarien enthalten, die Ihr Modell erkennen soll.
Berücksichtigen Sie beim Sammeln von Daten für das Testen und Trainieren benutzerdefinierter Modelle diese Faktoren:

* Ihre Text- und Sprachaudiodaten müssen alle Arten von verbalen Aussagen abdecken, die Ihre Benutzer bei der Interaktion mit Ihrem Modell abgeben. Beispielsweise muss ein Modell, das die Temperatur erhöht und senkt, zu Aussagen trainiert werden, die Personen zum Anfordern solcher Änderungen abgeben können.
* Ihre Daten müssen alle Sprachvarianten enthalten, die das Modell erkennen muss. Viele Faktoren können die Sprache verändern, einschließlich Akzenten, Dialekten, Sprachmischung, Alter, Geschlecht, Sprachwiedergabe, Belastung und Tageszeit.
* Sie müssen Beispiele aus unterschiedlichen Umgebungen (drinnen, draußen, mit Straßenlärm) einschließen, in denen das Modell verwendet wird.
* Die Audiodaten müssen mit Hardwaregeräten gesammelt werden, die auch im Produktionssystem verwendet werden. Wenn Ihr Modell Sprache identifizieren muss, die auf Aufnahmegeräten mit unterschiedlicher Qualität aufgezeichnet wird, müssen die Audiodaten, die Sie zum Trainieren des Modells bereitstellen, auch diese verschiedenen Szenarien widerspiegeln.
* Sie können Ihrem Modell später weitere Daten hinzufügen. Achten Sie jedoch darauf, dass das Dataset vielfältig und repräsentativ für Ihre Projektanforderungen ist.
* Das Einschließen von Daten, die *nicht* zu den Erkennungsanforderungen Ihres benutzerdefinierten Modells gehören, kann die Erkennungsqualität insgesamt beeinträchtigen. Sie sollten daher keine Daten einschließen, die das Modell nicht transkribieren soll.

Ein Modell, das für eine Teilmenge von Szenarien trainiert wurde, wird nur in diesen Szenarien gut funktionieren. Wählen Sie die Daten sorgfältig aus, damit sie sämtliche Szenarien abdecken, die mit Ihrem benutzerdefinierten Modell erkannt werden sollen.

> [!TIP]
> Beginnen Sie mit kleinen Sätzen von Beispieldaten, die der Sprache und der Akustik Ihres Modells entsprechen.
> Zeichnen Sie z. B. ein kleines, aber repräsentatives Audiobeispiel auf derselben Hardware und in derselben akustischen Umgebung auf, in der Ihr Modell in Produktionsszenarien verwendet werden soll.
> Kleine Datasets mit repräsentativen Daten können Probleme offenlegen, bevor Sie mit dem Sammeln von wesentlich größeren Datasets beginnen.

## <a name="data-types"></a>Datentypen

Die folgende Tabelle enthält die zulässigen Datentypen, gibt an, wann der jeweilige Datentyp zu verwenden ist, und nennt die empfohlene Datenmenge. Nicht jeder Datentyp ist zum Erstellen eines Modells erforderlich. Die Datenanforderungen sind unterschiedlich, je nachdem, ob Sie einen Test erstellen oder ein Modell trainieren.

| Datentyp | Zum Testen verwendet | Empfohlene Menge | Für Training verwendet | Empfohlene Menge |
|-----------|-----------------|----------|-------------------|----------|
| [Audio](#audio-data-for-testing) | Ja<br>Zur visuellen Prüfung verwendet | Mindestens 5 Audiodateien | Nein | – |
| [Audio + menschenmarkierte Transkripte](#audio--human-labeled-transcript-data-for-testingtraining) | Ja<br>Zur Bewertung der Genauigkeit verwendet | 0,5 – 5 Stunden Audio | Ja | 1 – 1.000 Stunden Audio |
| [Zugehöriger Text](#related-text-data-for-training) | Nein | Nicht zutreffend | Ja | 1 – 200 MB zugehöriger Text |

Dateien sollten nach Typ in einem Dataset gruppiert und als ZIP-Datei hochgeladen werden. Jedes Dataset darf nur einen einzelnen Datentyp enthalten.

> [!TIP]
> Zum schnellen Einstieg sollten Sie Beispieldaten verwenden. In diesem GitHub-Repository finden Sie <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">Custom Speech-Beispieldaten<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

## <a name="upload-data"></a>Hochladen von Daten

Navigieren Sie zum Hochladen der Daten zum <a href="https://speech.microsoft.com/customspeech" target="_blank">Custom Speech-Portal<span class="docon docon-navigate-external x-hidden-focus"></span></a>. Klicken Sie im Portal auf **Daten hochladen**, um den Assistenten zu starten und Ihr erstes Dataset zu erstellen. Sie werden aufgefordert, einen Sprachdatentyp für das Dataset auszuwählen, bevor Sie die Daten hochladen können.

![Screenshot: Hervorgehobene Option zum Hochladen von Audio im Speech-Portal](./media/custom-speech/custom-speech-select-audio.png)

Jedes hochgeladene Dataset muss den Anforderungen für den ausgewählten Datentyp entsprechen. Ihre Daten müssen ordnungsgemäß formatiert sein, bevor sie hochgeladen werden. Durch die richtige Formatierung wird sichergestellt, dass die Daten vom Custom Speech-Dienst korrekt verarbeitet werden. Die Anforderungen sind in den folgenden Abschnitten aufgelistet.

Nach dem Hochladen des Datasets haben Sie verschiedene Möglichkeiten:

* Sie können zur Registerkarte **Test** navigieren und nur Audiodaten oder Audio- und menschenmarkierte Transkriptionsdaten visuell überprüfen.
* Sie können zur Registerkarte **Training** navigieren und Audio- und menschenmarkierte Transkriptionsdaten oder zugehörige Textdaten zum Trainieren eines benutzerdefinierten Modells verwenden.

## <a name="audio-data-for-testing"></a>Audiodaten für Tests

Audiodaten eignen sich optimal, um die Genauigkeit des Microsoft-Basismodells für die Spracherkennung oder eines benutzerdefinierten Modells zu testen. Denken Sie daran, dass mit Audiodaten die Genauigkeit der Spracherkennung im Hinblick auf die Leistung eines bestimmten Modells überprüft wird. Wenn Sie die Genauigkeit eines Modells bemessen möchten, verwenden Sie [Audio- und menschenmarkierte Transkriptionsdaten](#audio--human-labeled-transcript-data-for-testingtraining).

Anhand der folgenden Tabelle können Sie sicherstellen, dass Ihre Audiodateien für die Verwendung mit Custom Speech richtig formatiert sind:

| Eigenschaft                 | Wert                 |
|--------------------------|-----------------------|
| Dateiformat              | RIFF (WAV)            |
| Samplingrate              | 8\.000 Hz oder 16.000 Hz |
| Kanäle                 | 1 (Mono)              |
| Maximale Länge pro Audioaufnahme | 2 Stunden               |
| Beispielformat            | PCM, 16 Bit           |
| Archivierungsformat           | .zip                  |
| Maximale Archivgröße     | 2 GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!TIP]
> Beim Hochladen von Trainings- und Testdaten darf die ZIP-Datei maximal 2 GB groß sein. Sollten Sie mehr Daten zum Trainieren benötigen, teilen Sie sie auf mehrere ZIP-Dateien auf, und laden Sie sie separat hoch. Später können Sie auswählen, dass Sie *mehrere* Datasets zum Trainieren verwenden möchten. Sie können jedoch nur aus *einem* Dataset testen.

Verwenden Sie <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">SoX<span class="docon docon-navigate-external x-hidden-focus"></span></a>, um Audioeigenschaften zu überprüfen oder vorhandene Audiodaten in die entsprechenden Formate zu konvertieren. Im Folgenden finden Sie einige Beispiele für das Ausführen der einzelnen Aktivitäten über die SoX-Befehlszeile:

| Aktivität | BESCHREIBUNG | SoX-Befehl |
|----------|-------------|-------------|
| Audioformat überprüfen | Verwenden Sie diesen Befehl zum Überprüfen<br>des Dateiformats der Audiodaten. | `sox --i <filename>` |
| Audioformat konvertieren | Verwenden Sie diesen Befehl zum Konvertieren<br>der Audiodatei für einen einzelnen Kanal mit 16 Bit und 16 kHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Audio- und menschenmarkierte Transkriptionsdaten für Tests/Training

Um die Genauigkeit der Microsoft-Spracherkennung bei der Verarbeitung Ihrer Audiodateien zu bemessen, müssen Sie menschenmarkierte Transkriptionen (wortwörtliche Transkriptionen) für den Vergleich bereitstellen. Zwar ist das Erstellen einer menschenmarkierten Transkription oft sehr zeitaufwendig, doch wird sie benötigt, um die Genauigkeit zu bewerten und das Modell für Ihre Anwendungsfälle zu trainieren. Denken Sie daran, dass die Verbesserung der Erkennung nur so gut ist wie die bereitgestellten Daten. Aus diesem Grund ist es wichtig, dass nur qualitativ hochwertige Transkripte hochgeladen werden.

Audiodateien können am Anfang und am Ende der Aufzeichnung Stille aufweisen. Schließen Sie nach Möglichkeit in jeder Beispieldatei mindestens eine halbe Sekunde Stille vor und nach Sprache ein. Auch wenn Audiodaten mit geringer Aufzeichnungslautstärke oder störenden Hintergrundgeräuschen nicht hilfreich sind, sollte dies keine negativen Auswirkungen auf Ihr benutzerdefiniertes Modell haben. Sie sollten immer ein Upgrade Ihrer Mikrofone und Signalverarbeitungshardware in Erwägung ziehen, bevor Sie Audiostichproben sammeln.

| Eigenschaft                 | Wert                               |
|--------------------------|-------------------------------------|
| Dateiformat              | RIFF (WAV)                          |
| Samplingrate              | 8\.000 Hz oder 16.000 Hz               |
| Kanäle                 | 1 (Mono)                            |
| Maximale Länge pro Audioaufnahme | 2 Stunden (Tests)/60 s (Training) |
| Beispielformat            | PCM, 16 Bit                         |
| Archivierungsformat           | .zip                                |
| Maximale ZIP-Dateigröße         | 2 GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> Beim Hochladen von Trainings- und Testdaten darf die ZIP-Datei maximal 2 GB groß sein. Sie können nur aus *einem* Dataset testen. Achten Sie darauf, dass dieses die geeignete Dateigröße einhält. Darüber hinaus dürfen die einzelnen Trainingsdateien 60 Sekunden nicht überschreiten, andernfalls tritt ein Fehler auf.

In Hinsicht auf Probleme wie die Löschung oder Ersetzung von Wörtern sind erhebliche Datenmengen erforderlich, um die Erkennung zu verbessern. Im Allgemeinen wird empfohlen, wortwörtliche Transkriptionen für ungefähr 10 bis 1.000 Stunden Audio bereitzustellen. Die Transkriptionen für sämtliche WAV-Dateien sollten in einer einzelnen Textdatei enthalten sein. Jede Zeile der Transkriptionsdatei muss den Namen einer der Audiodateien enthalten – gefolgt von der jeweiligen Transkription. Der Dateiname und die Transkription sollten durch ein Tabulatorzeichen (\t) getrennt werden.

  Beispiel:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```

> [!IMPORTANT]
> Die Transkription muss als UTF-8-Bytereihenfolge-Marke (byte order mark, BOM) codiert sein.

Der Text der Transkriptionen wird normalisiert, damit diese vom System verarbeitet werden können. Einige wichtige Normalisierungen müssen jedoch noch vor dem Hochladen der Daten in Speech Studio vorgenommen werden. Informationen dazu, welche Sprache bei der Vorbereitung Ihrer Transkriptionen verwendet werden muss, finden Sie unter [Erstellen einer menschenmarkierten Transkription](how-to-custom-speech-human-labeled-transcriptions.md).

Nachdem Sie die Audiodateien und entsprechenden Transkriptionen zusammengetragen haben, packen Sie diese in eine einzige ZIP-Datei, bevor Sie sie im <a href="https://speech.microsoft.com/customspeech" target="_blank">Custom Speech-Portal<span class="docon docon-navigate-external x-hidden-focus"></span></a> hochladen. Im Folgenden sehen Sie ein Beispieldataset mit drei Audiodateien und einer von einer Person markierten Transkriptionsdatei:

> [!div class="mx-imgBorder"]
> ![Auswählen von Audio im Speech-Portal](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Zugehörige Textdaten für das Training

Produktnamen oder Features, die eindeutig sind, sollten verwandte Textdaten für das Training enthalten. Verwandter Text sorgt für eine korrekte Erkennung. Es können zwei Arten von zugehörigen Textdaten für eine verbesserte Erkennung bereitgestellt werden:

| Datentyp | Mit diesen Daten erzielte Verbesserung |
|-----------|------------------------------------|
| Sätze (Äußerungen) | Diese können die Genauigkeit beim Erkennen von Produktnamen oder branchenspezifischem Vokabular innerhalb des Kontexts eines Satzes verbessern. |
| Aussprache | Diese können die Aussprache von selten vorkommenden Begriffen, Abkürzungen oder anderen Wörtern mit nicht definierter Aussprache verbessern. |

Sätze können als eine einzelne oder mehrere Textdateien bereitgestellt werden. Um die Genauigkeit zu erhöhen, verwenden Sie Textdaten, die näher an den erwarteten gesprochenen Äußerungen liegen. Aussprache sollte als eine einzige Textdatei bereitgestellt werden. Alles kann als eine einzige ZIP-Datei gepackt und im <a href="https://speech.microsoft.com/customspeech" target="_blank">Custom Speech-Portal<span class="docon docon-navigate-external x-hidden-focus"></span></a> hochgeladen werden.

### <a name="guidelines-to-create-a-sentences-file"></a>Richtlinien zum Erstellen einer Datei mit Sätzen

Um ein benutzerdefiniertes Modell mithilfe von Sätzen zu erstellen, müssen Sie eine Liste von Beispieläußerungen bereitstellen. Diese Äußerungen müssen _keine_ vollständigen oder grammatikalisch korrekten Sätze sein, doch sie müssen der gesprochenen Eingabe, die Sie in der Produktionsumgebung erwarten, genau entsprechen. Wenn bestimmte Begriffe eine höhere Gewichtung haben sollen, fügen Sie mehrere Sätze hinzu, die diese Begriffe enthalten.

Als allgemeine Richtlinie gilt: Die Modellanpassung ist am effektivsten, wenn der Trainingstext so nah wie möglich an dem in der Produktion zu erwartenden Text liegt. Themenspezifischer Jargon und Ausdrücke, die Sie ergänzen möchten, sollten im Trainingstext enthalten sein. Versuchen Sie nach Möglichkeit, einen Satz oder ein Schlüsselwort auf einer separaten Zeile einzufügen. Schlüsselwörter und Ausdrücke, die für Sie wichtig sind (z. B. Produktnamen), können Sie mehrmals kopieren. Achten Sie jedoch darauf, nicht zu viel zu kopieren, da sich dies auf die Gesamterkennungsrate auswirken kann.

Stellen Sie anhand der folgenden Tabelle sicher, dass die zugehörige Datendatei für Äußerungen richtig formatiert ist:

| Eigenschaft | Wert |
|----------|-------|
| Textcodierung | UTF-8 BOM |
| Anzahl von Äußerungen pro Zeile | 1 |
| Maximale Dateigröße | 200 MB |

Darüber hinaus sollten Sie die folgenden Einschränkungen beachten:

* Vermeiden Sie mehr als vier Wiederholungen eines Zeichens, z.B. „aaaa“ oder „uuuu“.
* Verwenden Sie keine Sonderzeichen oder UTF-8-Zeichen über `U+00A1`.
* URIs werden zurückgewiesen.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Richtlinien zum Erstellen einer Aussprachedatei

Wenn die Benutzer auf selten vorkommende Begriffe ohne Standardaussprache treffen oder diese verwenden werden, können Sie eine benutzerdefinierte Aussprachedatei bereitstellen, um die Erkennung zu verbessern.

> [!IMPORTANT]
> Es wird davon abgeraten, benutzerdefinierte Aussprachedateien zu verwenden, um die Aussprache gebräuchlicher Wörter zu ändern.

Hier sehen Sie Beispiele für eine gesprochene Äußerung und die jeweilige benutzerdefinierte Aussprache:

| Erkannte/angezeigte Form | Gesprochene Form |
|--------------|--------------------------|
| 3CPO | drei c p o |
| CNTK | c n t k |
| IEEE | i doppel e |

Die gesprochene Form ist die jeweilige Lautfolge. Sie kann aus Buchstaben, Wörtern, Silben oder einer Kombination aus allen drei bestehen.

Eine benutzerdefinierte Aussprache ist für Englisch (`en-US`) und Deutsch (`de-DE`) verfügbar. Die folgende Tabelle enthält die unterstützten Zeichen nach Sprache:

| Sprache | Gebietsschema | Zeichen |
|----------|--------|------------|
| Englisch | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| Deutsch | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

Stellen Sie anhand der folgenden Tabelle sicher, dass die zugehörige Datendatei für Aussprache richtig formatiert ist. Aussprachedateien sind klein und sollten einige KB nicht übersteigen.

| Eigenschaft | Wert |
|----------|-------|
| Textcodierung | UTF-8 BOM (ANSI wird für Englisch ebenfalls unterstützt) |
| Anzahl der Aussprachen pro Zeile | 1 |
| Maximale Dateigröße | 1 MB (1 KB für Free-Tarif) |

## <a name="next-steps"></a>Nächste Schritte

* [Überprüfen Ihrer Daten](how-to-custom-speech-inspect-data.md)
* [Bewerten Ihrer Daten](how-to-custom-speech-evaluate-data.md)
* [Trainieren Ihres Modells](how-to-custom-speech-train-model.md)
* [Bereitstellen Ihres Modells](./how-to-custom-speech-train-model.md)