---
title: Grundlagen der Speech-Befehlszeilenschnittstelle
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie das Speech-Befehlszeilenschnittstellen-Tool für das Arbeiten mit dem Speech-Dienst verwenden, mit nur minimaler Einrichtung und ohne zu programmieren.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 1b92d1b5853d6b794ebdcf0e2052b8f15081d608
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507573"
---
# <a name="learn-the-basics-of-the-speech-cli"></a>Erlernen der Grundlagen der Speech-Befehlszeilenschnittstelle

In diesem Artikel lernen Sie die grundlegenden Verwendungsmuster der Speech-Befehlszeilenschnittstelle kennen, eines Befehlszeilentools zur Nutzung des Speech-Diensts ohne Programmierung. Sie können die Hauptfunktionen des Speech-Diensts schnell ausprobieren, ohne Entwicklungsumgebungen zu erstellen oder zu programmieren, um festzustellen, ob Sie Ihren Anwendungsfällen angemessen gerecht werden können. Die Speech-Befehlszeilenschnittstelle ist für den Produktionsbetrieb bereit und kann verwendet werden, um einfache Arbeitsabläufe im Speech-Dienst mithilfe von `.bat`- oder Shellskripts zu automatisieren.

In diesem Artikel wird davon ausgegangen, dass Sie mit der Eingabeaufforderung, mit dem Terminal oder mit PowerShell vertraut sind.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Grundlegende Verwendung

In diesem Abschnitt werden einige grundlegende SPX-Befehle gezeigt, die häufig für erste Tests und Experimente nützlich sind. Führen Sie zunächst den unten angegebenen Befehl aus, um die integrierte Hilfe des Tools anzuzeigen.

```shell
spx
```

Achten Sie auf die Hilfethemen vom Typ **Siehe:** , die sich rechts von den Befehlsparametern befinden. Sie können diese Befehle eingeben, um ausführliche Hilfe zu Unterbefehlen zu erhalten.

Sie können Hilfethemen nach Schlüsselwort durchsuchen. Geben Sie beispielsweise den folgenden Befehl ein, um eine Liste der Beispiele für die Speech-CLI-Verwendung anzuzeigen:

```shell
spx help find --topics "examples"
```

Geben Sie den folgenden Befehl ein, um die Optionen für den Befehl „recognize“ anzuzeigen:

```shell
spx help recognize
```

Verwenden wir nun die Speech-Befehlszeilenschnittstelle, um eine Spracherkennung unter Verwendung des Standardmikrofons Ihres Systems durchzuführen. 

>[!WARNING]
> Bei Verwendung eines Docker-Containers funktioniert dieser Befehl nicht.

Führen Sie den folgenden Befehl aus:

```shell
spx recognize --microphone
```

Mit der Speech-Befehlszeilenschnittstelle können Sie auch Sprache aus einer Audiodatei erkennen.

```shell
spx recognize --file /path/to/file.wav
```
> [!TIP]
> Wenn Sie Sprache aus einer Audiodatei in einem Docker-Container erkennen möchten, vergewissern Sie sich, dass sich die Audiodatei in dem Verzeichnis befindet, das Sie im vorherigen Schritt eingebunden haben.

Nachdem Sie den Befehl eingegeben haben, beginnt SPX mit dem aktiven Lauschen nach Audiodaten am aktuellen aktiven Eingabegerät und hält an, wenn Sie `ENTER` drücken. Die aufgezeichnete Sprache wird dann erkannt und in der Konsolenausgabe in Text konvertiert. Auch die Sprachsynthese lässt sich mithilfe der Speech-Befehlszeilenschnittstelle einfach verwenden. 

Beim Ausführen des folgenden Befehls wird der eingegebene Text als Eingabe angenommen und die synthetisierte Sprache auf dem aktuellen aktiven Ausgabegerät ausgegeben.

```shell
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

Über Spracherkennung und -synthese hinaus können Sie mit der Speech-Befehlszeilenschnittstelle auch Übersetzungen erstellen. Führen Sie ähnlich wie beim Spracherkennungsbefehl oben den folgenden Befehl aus, um Audio von Ihrem Standardmikrofon zu erfassen und die Übersetzung in Text in der Zielsprache durchzuführen.

```shell
spx translate --microphone --source en-US --target ru-RU --output file C:\some\file\path\russian_translation.txt
```

In diesem Befehl geben Sie sowohl die Quellsprache (Sprache, **aus** der übersetzt wird) als auch die Zielsprache (Sprache, **in** die übersetzt wird) ein. Wenn Sie das Argument `--microphone` angeben, wird am aktuellen Eingabegerät nach Audio gelauscht und angehalten, wenn Sie `ENTER` drücken. Die Ausgabe ist eine Textübersetzung in die Zielsprache, die in eine Textdatei geschrieben wird.

> [!NOTE]
> Eine Liste aller unterstützten Sprachen mit ihren entsprechenden Gebietsschemacodes finden Sie im [Artikel zu Sprachen und Gebietsschemas](language-support.md).

### <a name="configuration-files-in-the-datastore"></a>Konfigurationsdateien im Datenspeicher

Das Verhalten der Speech-Befehlszeilenschnittstelle (Speech CLI) kann auf Einstellungen in Konfigurationsdateien basieren, auf die Sie in Speech CLI-Aufrufen mit dem Symbol \@ verweisen können.
Von der Speech CLI wird eine neue Einstellung im neuen Unterverzeichnis `./spx/data` gespeichert, das im aktuellen Arbeitsverzeichnis erstellt wird.
Beim Suchen nach einem Konfigurationswert sieht die Speech CLI zuerst in Ihrem aktuellen Arbeitsverzeichnis, anschließend im Datenspeicher unter `./spx/data` und dann in anderen Datenspeichern nach, z. B. in einem schreibgeschützten endgültigen Datenspeicher in der `spx`-Binärdatei.
Bisher haben Sie den Datenspeicher zum Speichern Ihrer Werte `@key` und `@region` verwendet und mussten sie daher nicht in jedem Befehlszeilenaufruf angeben.
Sie können Konfigurationsdateien auch verwenden, um Ihre eigenen Konfigurationseinstellungen zu speichern, oder sie sogar nutzen, um URLs oder andere dynamische Inhalte zu übergeben, die zur Laufzeit generiert werden.

In diesem Abschnitt wird die Verwendung einer Konfigurationsdatei im lokalen Datenspeicher zum Speichern und Abrufen von Befehlseinstellungen mit `spx config` und die Speicherung der Ausgabe der Speech CLI mit der Option `--output` veranschaulicht.

Im folgenden Beispiel wird die Konfigurationsdatei `@my.defaults` geleert, es werden Schlüssel-Wert-Paare für **key** und **region** in der Datei hinzugefügt, und die Konfiguration wird in einem Aufruf von `spx recognize` verwendet.

```shell
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

Sie können auch dynamischen Inhalt in eine Konfigurationsdatei schreiben. Mit dem folgenden Befehl wird ein benutzerdefiniertes Sprachmodell erstellt und die URL des neuen Modells in einer Konfigurationsdatei gespeichert. Der nächste Befehl wartet, bis das Modell unter dieser URL für die Verwendung bereit ist, bevor die Rückgabe erfolgt.

```shell
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

Im folgenden Beispiel werden zwei URLs in die Konfigurationsdatei `@my.datasets.txt` geschrieben.
In diesem Szenario kann `--output` das optionale Schlüsselwort **add** zum Erstellen einer Konfigurationsdatei bzw. zum Anfügen an die vorhandene Datei enthalten.


```shell
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

Geben Sie den folgenden Befehl ein, um weitere Informationen zu Datenspeicherdateien anzuzeigen, z. B. zur Verwendung von Standardkonfigurationsdateien (`@spx.default`, `@default.config` und `@*.default.config` für befehlsspezifische Standardeinstellungen):

```shell
spx help advanced setup
```

## <a name="batch-operations"></a>Batchvorgänge

Die Befehle im vorherigen Abschnitt eignen sich hervorragend, um schnell zu sehen, wie der Speech-Dienst funktioniert. Bei der Beurteilung, ob Ihre Anwendungsfälle sinnvoll bedient werden können, müssen Sie wahrscheinlich Batchvorgänge für eine Bandbreite von Eingaben ausführen, die Sie bereits besitzen, um zu sehen, wie der Dienst eine gewisse Bandbreite von Szenarien behandelt. Wie das geht, erfahren Sie in diesem Abschnitt:

* Ausführen der Spracherkennung für ein Verzeichnis mit Audiodateien
* Iterieren durch eine `.tsv`-Datei und Ausführen von Sprachsynthese

## <a name="batch-speech-recognition"></a>Batch-Spracherkennung

Wenn Sie über ein Verzeichnis mit Audiodateien verfügen, können Sie dafür mithilfe der Speech-Befehlszeilenschnittstelle schnell eine Batch-Spracherkennung ausführen. Führen Sie einfach den folgenden Befehl aus, wobei Sie mit dem Befehl `--files` auf Ihr Verzeichnis verweisen. In diesem Beispiel fügen Sie `\*.wav` an das Verzeichnis an, um alle im Verzeichnis vorhandenen `.wav`-Dateien zu erkennen. Geben Sie darüber hinaus das `--threads`-Argument an, um die Erkennung auf 10 parallelen Threads auszuführen.

> [!NOTE]
> Das `--threads`-Argument kann außerdem im nächsten Abschnitt für `spx synthesize`-Befehle verwendet werden, und die verfügbaren Threads hängen von der CPU und dem aktuellen Prozentsatz ihrer Auslastung ab.

```shell
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

Die erkannte Sprachausgabe wird mit dem Argument `--output file` nach `speech_output.tsv` geschrieben. Es folgt ein Strukturbeispiel für die Ausgabedatei.

```output
audio.input.id    recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="synthesize-speech-to-a-file"></a>Synthetisieren von Sprache in eine Datei

Führen Sie den folgenden Befehl aus, um die Ausgabe von Ihrem Lautsprecher in eine Datei vom Typ `.wav` zu ändern.

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

Die Speech-Befehlszeilenschnittstelle gibt natürlichsprachliches Englisch in die Audiodatei `greetings.wav` aus.
Unter Windows können Sie die Audiodatei wiedergeben, indem Sie `start greetings.wav` eingeben.


## <a name="batch-text-to-speech-synthesis"></a>Batch-Sprachsynthese

Die einfachste Möglichkeit zum Ausführen von Batch-Sprachsynthese besteht darin, eine neue `.tsv`-Datei (Text mit Tabstopps) zu erstellen und den `--foreach`-Befehl an der Speech-Befehlszeilenschnittstelle zu verwenden. Betrachten Sie die folgende Datei `text_synthesis.tsv`:

```output
audio.output    text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

 Führen Sie als nächstes einen Befehl aus, um auf `text_synthesis.tsv` zu verweisen, Synthese für jedes `text`-Feld auszuführen und das Ergebnis unter dem entsprechenden `audio.output`-Pfad als `.wav`-Datei zu schreiben. 

```shell
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

Dieser Befehl ist das Äquivalent zur Ausführung von `spx synthesize --text Sample text to synthesize --audio output C:\batch_wav_output\wav_1.wav` **für jeden** Datensatz in der `.tsv`-Datei. Ein paar Dinge sind zu beachten:

* Die Spaltenüberschriften `audio.output` und `text` entsprechen den Befehlszeilenargumenten `--audio output` bzw. `--text`. Mehrteilige Befehlszeilenargumente wie `--audio output` sollten in der Datei ohne Leerzeichen, ohne führende Bindestriche und Punkte zur Trennung von Zeichenfolgen (Beispiel: `audio.output`) formatiert werden. Alle anderen vorhandenen Befehlszeilenargumente können der Datei mithilfe dieses Musters als zusätzliche Spalten hinzugefügt werden.
* Wenn die Datei auf diese Weise formatiert wird, müssen keine zusätzlichen Argumente an `--foreach` übergeben werden.
* Achten Sie darauf, jeden Wert in der `.tsv`-Datei mit einem **Tabstopp** zu trennen.

Im Fall einer `.tsv`-Datei wie im folgenden Beispiel jedoch, deren Spaltenüberschriften mit den Befehlszeilenargumenten **nicht übereinstimmen**:

```output
wav_path    str_text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

Sie können diese Feldnamen mit den richtigen Argumenten überschreiben, sofern Sie dazu im `--foreach`-Aufruf die folgende Syntax verwenden. Dies ist derselbe Aufruf wie oben.

```shell
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Nächste Schritte

* Führen Sie die Schnellstarts zur [Spracherkennung](get-started-speech-to-text.md?pivots=programmer-tool-spx) oder [Sprachsynthese](get-started-text-to-speech.md?pivots=programmer-tool-spx) mithilfe der Speech CLI durch.
