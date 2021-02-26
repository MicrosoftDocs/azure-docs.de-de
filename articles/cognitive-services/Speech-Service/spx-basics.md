---
title: 'Schnellstart für die Speech-Befehlszeilenschnittstelle: Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Hier finden Sie Informationen zu den ersten Schritten mit der Azure Speech-Befehlszeilenschnittstelle. Sie können mit Speech-Diensten wie Spracherkennung, Sprachsynthese und Sprachübersetzung interagieren, ohne Code zu schreiben.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: trbye
ms.openlocfilehash: 4a6c7b36665c7a38534ce8e470bc8b327c274d95
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2021
ms.locfileid: "99095178"
---
# <a name="get-started-with-the-azure-speech-cli"></a>Erste Schritte mit der Azure Speech-Befehlszeilenschnittstelle

In diesem Artikel erfahren Sie, wie Sie über die Speech-Befehlszeilenschnittstelle auf Speech-Dienste wie Spracherkennung, Sprachsynthese und Sprachübersetzung zugreifen, ohne Code zu schreiben. Die Speech-Befehlszeilenschnittstelle ist für den Produktionsbetrieb bereit und kann verwendet werden, um einfache Arbeitsabläufe im Speech-Dienst mithilfe von `.bat`- oder Shellskripts zu automatisieren.

In diesem Artikel wird davon ausgegangen, dass Sie mit der Eingabeaufforderung, mit dem Terminal oder mit PowerShell vertraut sind.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Grundlegende Verwendung

In diesem Abschnitt werden einige grundlegende SPX-Befehle gezeigt, die häufig für erste Tests und Experimente nützlich sind. Führen Sie zunächst den unten angegebenen Befehl aus, um die integrierte Hilfe des Tools anzuzeigen.

```console
spx
```

Sie können Hilfethemen nach Schlüsselwort durchsuchen. Geben Sie beispielsweise den folgenden Befehl ein, um eine Liste der Beispiele für die Speech-CLI-Verwendung anzuzeigen:

```console
spx help find --topics "examples"
```

Geben Sie den folgenden Befehl ein, um die Optionen für den Befehl „recognize“ anzuzeigen:

```console
spx help recognize
```

In der rechten Spalte sind weitere Hilfebefehle aufgeführt. Diese Befehle können Sie eingeben, um ausführliche Hilfe zu Unterbefehlen zu erhalten.

## <a name="speech-to-text-speech-recognition"></a>Spracherkennung (Umwandlung von Sprache in Text)

In diesem Abschnitt verwenden wir die Speech-Befehlszeilenschnittstelle, um Sprache unter Verwendung des Standardmikrofons Ihres Systems in Text zu konvertieren (Spracherkennung). Nach Eingabe des Befehls beginnt SPX damit, am aktuellen aktiven Eingabegerät auf Audiodaten zu lauschen, und beendet das Lauschen, wenn Sie die EINGABETASTE drücken **.** Die aufgezeichnete Sprache wird dann erkannt und in der Konsolenausgabe in Text konvertiert.

>[!IMPORTANT]
> Bei Verwendung eines Docker-Containers funktioniert `--microphone` nicht.

Führen Sie den folgenden Befehl aus:

```console
spx recognize --microphone
```

Die Speech-Befehlszeilenschnittstelle ermöglicht auch die Erkennung von Sprache aus einer Audiodatei.

```console
spx recognize --file /path/to/file.wav
```

> [!TIP]
> Wenn Sie Sprache aus einer Audiodatei in einem Docker-Container erkennen möchten, vergewissern Sie sich, dass sich die Audiodatei in dem Verzeichnis befindet, das Sie im vorherigen Schritt eingebunden haben.

Zur Erinnerung: Sollten Sie einmal nicht weiterkommen oder weitere Informationen zu den Erkennungsoptionen der Speech-Befehlszeilenschnittstelle benötigen, geben Sie einfach Folgendes ein:

```console
spx help recognize
```

## <a name="text-to-speech-speech-synthesis"></a>Sprachsynthese (Umwandlung von Text in Sprache)

Wenn Sie den folgenden Befehl ausführen, wird Text als Eingabe verwendet und die synthetisierte Sprache über das aktuelle aktive Ausgabegerät (beispielsweise über die Lautsprecher Ihres Computers) ausgegeben.

```console
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

Die synthetisierte Ausgabe kann auch in einer Datei gespeichert werden. In diesem Beispiel wird eine Datei namens `my-sample.wav` in dem Verzeichnis gespeichert, in dem der Befehl ausgeführt wird.

```console
spx synthesize --text "We hope that you enjoy using the Speech CLI." --audio output my-sample.wav
```

Bei diesen Beispielen wird davon ausgegangen, dass Sie zum Testen die englische Sprache verwenden. Die Sprachsynthese wird jedoch in vielen Sprachen unterstützt. Eine umfassende Liste der verfügbaren erhalten Sie mithilfe des folgenden Befehls oder auf der Seite [Sprach- und Stimmunterstützung für den Speech-Dienst](./language-support.md):

```console
spx synthesize --voices
```

So verwenden Sie eine dieser Stimmen:

```console
spx synthesize --text "Bienvenue chez moi." --voice fr-CA-Caroline --speakers
```

Zur Erinnerung: Sollten Sie einmal nicht weiterkommen oder weitere Informationen zu den Syntheseoptionen der Speech-Befehlszeilenschnittstelle benötigen, geben Sie einfach Folgendes ein:

```console
spx help synthesize
```

## <a name="speech-to-text-translation"></a>Sprache-in-Text-Übersetzung

Mit der Speech-Befehlszeilenschnittstelle sind auch Sprache-in-Text-Übersetzungen möglich. Führen Sie den folgenden Befehl aus, um Audio über Ihr Standardmikrofon zu erfassen und die Übersetzung als Text auszugeben. Beachten Sie, dass für den Befehl `translate` die Ausgangs- und die Zielsprache (`source` und `target`) angegeben werden müssen.

```console
spx translate --microphone --source en-US --target ru-RU
```

Wenn die Übersetzung in mehrere Sprachen erfolgen soll, trennen Sie die Sprachcodes jeweils durch `;`.

```console
spx translate --microphone --source en-US --target ru-RU;fr-FR;es-ES
```

Wenn Sie die Ausgabe der Übersetzung speichern möchten, verwenden Sie das Flag `--output`. In diesem Beispiel wird auch aus einer Datei gelesen.

```console
spx translate --file /some/file/path/input.wav --source en-US --target ru-RU --output file /some/file/path/russian_translation.txt
```

> [!NOTE]
> Eine Liste aller unterstützten Sprachen mit ihren entsprechenden Gebietsschemacodes finden Sie im [Artikel zu Sprachen und Gebietsschemas](language-support.md).

Zur Erinnerung: Sollten Sie einmal nicht weiterkommen oder weitere Informationen zu den Übersetzungsoptionen der Speech-Befehlszeilenschnittstelle benötigen, geben Sie einfach Folgendes ein:

```console
spx help translate
```

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurationsoptionen für die Speech-Befehlszeilenschnittstelle](./spx-data-store-configuration.md)
* [Batchvorgänge für die Speech-Befehlszeilenschnittstelle](./spx-batch-operations.md)
