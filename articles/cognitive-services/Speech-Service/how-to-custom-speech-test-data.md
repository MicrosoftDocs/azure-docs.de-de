---
title: Vorbereiten von Testdaten für Custom Speech – Speech-Dienste
titlesuffix: Azure Cognitive Services
description: Ganz gleich, ob Sie die Genauigkeit der Microsoft-Spracherkennung testen oder Ihre eigenen Modelle trainieren möchten – Sie brauchen Daten (in Form von Audio und/oder Text). Auf dieser Seite werden die einzelnen Datentypen, ihre Verwendung und ihre Verwaltung beschrieben.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: de2f1009c574d9768330d4e6a38a219ba1f81daa
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237951"
---
# <a name="prepare-data-for-custom-speech"></a>Vorbereiten von Daten für Custom Speech

Ganz gleich, ob Sie die Genauigkeit der Microsoft-Spracherkennung testen oder Ihre eigenen Modelle trainieren möchten – Sie benötigen Daten in Form von Audio und Text. Auf dieser Seite werden die einzelnen Datentypen, ihre Verwendung und ihre Verwaltung beschrieben.

## <a name="data-types"></a>Datentypen

Die folgende Tabelle enthält die zulässigen Datentypen, gibt an, wann der jeweilige Datentyp zu verwenden ist, und nennt die empfohlene Datenmenge. Nicht jeder Datentyp ist zum Erstellen eines Modells erforderlich. Die Datenanforderungen sind unterschiedlich, je nachdem, ob Sie einen Test erstellen oder ein Modell trainieren.

| Datentyp | Für Tests verwendet | Menge | Für Training verwendet | Menge |
|-----------|-----------------|----------|-------------------|----------|
| [Audio](#audio-data-for-testing) | Ja<br>Zur visuellen Prüfung verwendet | Mindestens 5 Audiodateien | Nein | Nicht zutreffend |
| [Audio + menschenmarkierte Transkripte](#audio--human-labeled-transcript-data-for-testingtraining) | Ja<br>Zur Bewertung der Genauigkeit verwendet | 0,5 – 5 Stunden Audio | Ja | 1 – 1.000 Stunden Audio |
| [Zugehöriger Text](##related-text-data-for-training) | Nein | Nicht zutreffend | Ja | 1 – 200 MB zugehöriger Text |

Dateien sollten nach Typ in einem Dataset gruppiert und als ZIP-Datei hochgeladen werden. Jedes Dataset darf nur einen einzelnen Datentyp enthalten.

## <a name="upload-data"></a>Hochladen von Daten

Wenn Sie Ihre Daten hochladen möchten, klicken Sie auf **Daten hochladen**, um den Assistenten zu starten und Ihr erstes Dataset zu erstellen. Sie werden aufgefordert, einen Sprachdatentyp für das Dataset auszuwählen, bevor Sie die Daten hochladen können.

![Auswählen von Audio im Speech-Portal](./media/custom-speech/custom-speech-select-audio.png)

Jedes hochgeladene Dataset muss den Anforderungen für den ausgewählten Datentyp entsprechen. Es ist wichtig, dass Sie die Daten vor dem Hochladen richtig formatieren. Dadurch wird sichergestellt, dass die Daten vom Custom Speech-Dienst korrekt verarbeitet werden. Die Anforderungen sind in den folgenden Abschnitten aufgelistet.

Nach dem Hochladen des Datasets haben Sie verschiedene Möglichkeiten:

* Sie können zur Registerkarte **Test** navigieren und nur Audiodaten oder Audio- und menschenmarkierte Transkriptionsdaten visuell überprüfen.
* Sie können zur Registerkarte **Training** navigieren und Audio- und menschenmarkierte Transkriptionsdaten oder zugehörige Textdaten zum Trainieren eines benutzerdefinierten Modells verwenden.

## <a name="audio-data-for-testing"></a>Audiodaten für Tests

Audiodaten eignen sich optimal, um die Genauigkeit des Microsoft-Basismodells für die Spracherkennung oder eines benutzerdefinierten Modells zu testen. Denken Sie daran, dass mit Audiodaten die Genauigkeit der Spracherkennung im Hinblick auf die Leistung eines bestimmten Modells überprüft wird. Wenn Sie die Genauigkeit eines Modells bemessen möchten, verwenden Sie [Audio- und menschenmarkierte Transkriptionsdaten](#audio--human-labeled-transcript-data-for-testingtraining).

Anhand der folgenden Tabelle können Sie sicherstellen, dass Ihre Audiodateien für die Verwendung mit Custom Speech richtig formatiert sind:

| Eigenschaft | Wert |
|----------|-------|
| Dateiformat | RIFF (WAV) |
| Samplingrate | 8.000 Hz oder 16.000 Hz |
| Kanäle | 1 (Mono) |
| Maximale Länge pro Audioaufnahme | 2 Stunden |
| Beispielformat | PCM, 16 Bit |
| Archivierungsformat | .zip |
| Maximale Archivgröße | 2 GB |

Wenn Ihre Audiodaten diese Eigenschaften nicht erfüllen oder Sie prüfen möchten, ob das der Fall ist, wird das Herunterladen von [SoX](http://sox.sourceforge.net) empfohlen, um die Audiodaten zu überprüfen oder zu konvertieren. Es folgen einige Beispiele für das Ausführen der einzelnen Aktivitäten über die Befehlszeile:

| Aktivität | BESCHREIBUNG | SoX-Befehl |
|----------|-------------|-------------|
| Audioformat überprüfen | Verwenden Sie diesen Befehl, um das Audiodateiformat zu überprüfen. | `sox --i <filename>` |
| Audioformat konvertieren | Verwenden Sie diesen Befehl, um die Audiodatei in einen einzigen Kanal, 16 Bit, 16 KHz, zu konvertieren. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Audio- und menschenmarkierte Transkriptionsdaten für Tests/Training

Um die Genauigkeit der Microsoft-Spracherkennung bei der Verarbeitung Ihrer Audiodateien zu bemessen, müssen Sie menschenmarkierte Transkriptionen (wortwörtliche Transkriptionen) für den Vergleich bereitstellen. Zwar ist das Erstellen einer menschenmarkierten Transkription oft sehr zeitaufwendig, doch wird sie benötigt, um die Genauigkeit zu bewerten und das Modell für Ihre Anwendungsfälle zu trainieren. Denken Sie daran, dass die Verbesserung der Erkennung nur so gut ist wie die bereitgestellten Daten. Aus diesem Grund ist es wichtig, dass nur qualitativ hochwertige Transkripte hochgeladen werden.  

| Eigenschaft | Wert |
|----------|-------|
| Dateiformat | RIFF (WAV) |
| Samplingrate | 8.000 Hz oder 16.000 Hz |
| Kanäle | 1 (Mono) |
| Maximale Länge pro Audioaufnahme | 60 s |
| Beispielformat | PCM, 16 Bit |
| Archivierungsformat | .zip |
| Maximale ZIP-Dateigröße | 2 GB |

In Hinsicht auf Probleme wie die Löschung oder Ersetzung von Wörtern sind erhebliche Datenmengen erforderlich, um die Erkennung zu verbessern. Im Allgemeinen wird empfohlen, wortwörtliche Transkriptionen für ungefähr 10 bis 1.000 Stunden Audio bereitzustellen. Die Transkriptionen für sämtliche WAV-Dateien sollten in einer einzelnen Textdatei enthalten sein. Jede Zeile der Transkriptionsdatei muss den Namen einer der Audiodateien enthalten – gefolgt von der jeweiligen Transkription. Der Dateiname und die Transkription sollten durch ein Tabulatorzeichen (\t) getrennt werden.

  Beispiel:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> Die Transkription muss als UTF-8-Bytereihenfolge-Marke (byte order mark, BOM) codiert sein.

Der Text der Transkriptionen wird normalisiert, damit diese vom System verarbeitet werden können. Einige wichtige Normalisierungen müssen jedoch noch _vor_ dem Hochladen der Daten für den Custom Speech Service durch den Benutzer vorgenommen werden. Informationen dazu, welche Sprache bei der Vorbereitung Ihrer Transkriptionen verwendet werden muss, finden Sie unter [Erstellen einer menschenmarkierten Transkription](how-to-custom-speech-human-labeled-transcriptions.md).

Nachdem Sie die Audiodateien und entsprechenden Transkriptionen zusammengetragen haben, sollten diese in eine einzige ZIP-Datei gepackt werden, bevor Sie sie in das Custom Speech-Portal hochladen. Hier sehen Sie ein Beispieldataset mit drei Audiodateien und einer menschenmarkierten Transkriptionsdatei:

![Auswählen von Audio im Speech-Portal](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Zugehörige Textdaten für das Training

Wenn Sie über eindeutige Produktnamen oder Features verfügen und sicherstellen möchten, dass diese richtig erkannt werden, ist es wichtig, zugehörige Textdaten für das Training einzuschließen. Es können zwei Arten von zugehörigen Textdaten für eine verbesserte Erkennung bereitgestellt werden:

| Datentyp | Mit diesen Daten erzielte Verbesserung |
|-----------|------------------------------------|
| Äußerungen und/oder Sätze | Diese können die Genauigkeit beim Erkennen von Produktnamen oder branchenspezifischem Vokabular innerhalb des Kontexts eines Satzes verbessern. |
| Aussprache | Diese können die Aussprache von selten vorkommenden Begriffen, Abkürzungen oder anderen Wörtern mit nicht definierter Aussprache verbessern. |

Äußerungen können als eine einzelne oder mehrere Textdateien bereitgestellt werden. Je stärker die Textdaten dem gesprochenen Text entsprechen, desto größer ist die Wahrscheinlichkeit, dass die Genauigkeit verbessert wird. Aussprache sollte als eine einzige Textdatei bereitgestellt werden. Alles kann als eine einzige ZIP-Datei gepackt und in das Custom Speech-Portal hochgeladen werden.

### <a name="guidelines-to-create-an-utterances-file"></a>Richtlinien zum Erstellen einer Datei mit Äußerungen

Um ein benutzerdefiniertes Modell mithilfe von zugehörigem Text zu erstellen, müssen Sie eine Liste von Beispieläußerungen bereitstellen. Diese Äußerungen müssen keine vollständigen Sätze oder grammatikalisch korrekt sein, doch sie müssen der gesprochenen Eingabe, die Sie in der Produktionsumgebung erwarten, genau entsprechen. Wenn bestimmte Begriffe eine höhere Gewichtung haben sollen, können Sie der zugehörigen Datendatei mehrere Sätze hinzufügen, die diese bestimmten Begriffe enthalten.

Stellen Sie anhand der folgenden Tabelle sicher, dass die zugehörige Datendatei für Äußerungen richtig formatiert ist:

| Eigenschaft | Wert |
|----------|-------|
| Textcodierung | UTF-8 BOM |
| Anzahl von Äußerungen pro Zeile | 1 |
| Maximale Dateigröße | 200 MB |

Darüber hinaus sollten Sie die folgenden Einschränkungen beachten:

* Vermeiden Sie mehr als vier Wiederholungen eines Zeichens, z.B. „aaaa“ oder „uuuu“.
* Verwenden Sie keine Sonderzeichen oder UTF-8-Zeichen über U+00A1.
* URIs werden zurückgewiesen.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Richtlinien zum Erstellen einer Aussprachedatei

Wenn die Benutzer auf selten vorkommende Begriffe ohne Standardaussprache treffen oder diese verwenden werden, können Sie eine benutzerdefinierte Aussprachedatei bereitstellen, um die Erkennung zu verbessern.

> [!IMPORTANT]
> Es wird davon abgeraten, dieses Feature zu verwenden, um die Aussprache gebräuchlicher Wörter zu ändern.

Hier sehen Sie Beispiele für eine gesprochene Äußerung und die jeweilige benutzerdefinierte Aussprache:

| Gesprochene Form | Erkannte/angezeigte Form |
|--------------|--------------------------|
| drei c p o | 3CPO |  
| c n t k | CNTK |
| i doppel e | IEEE |

Die gesprochene Form ist die jeweilige Lautfolge. Sie kann aus Buchstaben, Wörtern, Silben oder einer Kombination aus allen drei bestehen.

Eine benutzerdefinierte Aussprache ist in Englisch (en-US) und Deutsch (de-DE) verfügbar. Die folgende Tabelle enthält die unterstützten Zeichen nach Sprache:

| Sprache | Gebietsschema | Zeichen |
|----------|--------|------------|
| Englisch | en-US | a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |
| Deutsch | de-DE | ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

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
* [Bereitstellen Ihres Modells](how-to-custom-speech-deploy-model.md)
