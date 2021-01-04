---
title: Trainieren und Bereitstellen eines Custom Speech-Modells – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie Custom Speech-Modelle trainieren und bereitstellen können. Das Training einer Spracherkennung kann die Erkennungsgenauigkeit sowohl für das Microsoft-Basismodell als auch für ein benutzerdefiniertes Modell verbessern.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 130cd643856b38471eac6d6869cdc1ed8b0bcd2e
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499151"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>Trainieren und Bereitstellen eines Custom Speech-Modells

In diesem Artikel erfahren Sie, wie Sie Custom Speech-Modelle trainieren und bereitstellen können. Das Training einer Spracherkennung kann die Erkennungsgenauigkeit für das Microsoft-Basismodell verbessern. Sie verwenden die Humantranskription sowie zugehörigen Text,um ein Modell zu trainieren. Diese Datasets werden zusammen mit zuvor hochgeladenen Audiodaten verwendet, um das Spracherkennungsmodell zu verfeinern und zu trainieren.

## <a name="use-training-to-resolve-accuracy-problems"></a>Lösen von Genauigkeitsproblemen durch Training

Wenn Sie bei einem Basismodell auf Erkennungsprobleme stoßen, kann die Verwendung von Humantranskripten und zugehörigen Daten zum Training eines benutzerdefinierten Modells dazu beitragen, die Genauigkeit zu verbessern. Bestimmen Sie anhand von dieser Tabelle, welches Dataset zum Beheben Ihrer Probleme verwendet werden soll:

| Anwendungsfall | Datentyp |
| -------- | --------- |
| Verbessern der Erkennungsgenauigkeit für branchenspezifisches Vokabular und entsprechende Grammatik (z. B. aus der Medizin- oder IT-Branche) | Zugehöriger Text (Sätze/Äußerungen) |
| Definieren der phonetischen und angezeigten Form eines Worts oder Begriffs mit nicht standardmäßiger Aussprache (beispielsweise Produktnamen oder Akronyme) | Zugehöriger Text (Aussprache) |
| Verbessern der Erkennungsgenauigkeit für Sprechweisen, Akzente oder bestimmte Hintergrundgeräusche | Audio + menschenmarkierte Transkripte |

## <a name="train-and-evaluate-a-model"></a>Trainieren und Bewerten eines Modells

Der erste Schritt beim Trainieren eines Modells ist das Hochladen von Trainingsdaten. Unter [Vorbereiten und Testen Ihrer Daten](./how-to-custom-speech-test-and-train.md) finden Sie ausführliche Anweisungen zum Vorbereiten von Humantranskriptionen und zugehörigem Text (Äußerungen und Aussprache). Nachdem Sie Trainingsdaten hochgeladen haben, folgen Sie diesen Anweisungen, um mit dem Trainieren Ihres Modells zu beginnen:

1. Melden Sie sich beim [Custom Speech-Portal](https://speech.microsoft.com/customspeech) an.
2. Navigieren Sie zu **Spracherkennung** > **Custom Speech** >  **[Projektname]**  > **Training**.
3. Wählen Sie **Modell trainieren** aus.
4. Geben Sie einen **Namen** und eine **Beschreibung** für Ihr Training ein.
5. Wählen Sie aus der Liste für **Szenario und Basismodell** das für Ihre Domäne am besten geeignete Szenario aus. Wenn Sie sich nicht sicher sind, welches Szenario Sie wählen sollen, wählen Sie **Allgemein** aus. Das Basismodell stellt den Ausgangspunkt für das Training dar. Das neueste Modell ist in der Regel die beste Wahl.
6. Wählen Sie auf der Seite **Trainingsdaten auswählen** ein oder mehrere Audiodatasets und Humantranskriptionsdatasets aus, die Sie für das Training verwenden möchten.
7. Nachdem das Training abgeschlossen ist, können Sie Genauigkeitsprüfungen für das neu trainierte Modell ausführen. Dieser Schritt ist optional.
8. Wählen Sie **Erstellen** aus, um ein benutzerdefiniertes Modell zu erstellen.

In der **Trainingstabelle** wird ein neuer Eintrag angezeigt, der diesem neuen Modell entspricht. Außerdem zeigt die Tabelle den Status an: **Verarbeitung**, **Erfolgreich** oder **Fehler**.

Weitere Informationen zur Bewertung und Verbesserung der Genauigkeit des Custom Speech-Modells finden Sie in dieser [Schrittanleitung](how-to-custom-speech-evaluate-data.md). Wenn Sie sich für einen Genauigkeitstest entscheiden, ist es wichtig, ein anderes akustisches Dataset auszuwählen, als Sie für Ihr Modell verwendet haben, um ein realistisches Bild von der Leistung des Modells zu erhalten.

## <a name="deploy-a-custom-model"></a>Bereitstellen eines benutzerdefinierten Modells

Nachdem Sie Daten hochgeladen und überprüft, die Genauigkeit ausgewertet und ein benutzerdefiniertes Modell trainiert haben, können Sie einen benutzerdefinierten Endpunkt zur Verwendung mit Ihren Apps, Tools und Produkten bereitstellen. 

Melden Sie sich zum Erstellen eines benutzerdefinierten Endpunkts beim [Custom Speech-Portal](https://speech.microsoft.com/customspeech) an. Wählen Sie **Bereitstellung** im Menü **Custom Speech** am oberen Rand der Seite aus. Wenn dies Ihre erste Ausführung ist, werden Sie feststellen, dass in der Tabelle keine Endpunkte aufgeführt sind. Nachdem Sie einen Endpunkt erstellt haben, verwenden Sie diese Seite zum Nachverfolgen der einzelnen bereitgestellten Endpunkte.

Wählen Sie als Nächstes **Endpunkt hinzufügen** aus, und geben Sie einen **Namen** und eine **Beschreibung** für Ihren benutzerdefinierten Endpunkt ein. Wählen Sie dann das benutzerdefinierte Modell aus, das Sie diesem Endpunkt zuordnen möchten.  Auf dieser Seite können Sie auch die Protokollierung aktivieren. Die Protokollierung ermöglicht es Ihnen, den Datenverkehr des Endpunkts zu überwachen. Wenn die Protokollierung deaktiviert ist, wird Datenverkehr nicht gespeichert.

![Screenshot, der die neue Endpunktseite anzeigt](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Denken Sie daran, die Nutzungsbedingungen und Preise zu akzeptieren.

Wählen Sie als Nächstes die Option **Erstellen**. Durch diese Aktion kehren Sie zur Seite **Bereitstellung** zurück. Die Tabelle enthält jetzt einen Eintrag für Ihren benutzerdefinierten Endpunkt. Der Endpunktstatus zeigt den aktuellen Zustand. Es kann bis zu 30 Minuten dauern, bis ein neuer Endpunkt mit Ihren benutzerdefinierten Modellen instanziiert wurde. Sobald sich der Bereitstellungsstatus in **Abgeschlossen** ändert, kann der Endpunkt verwendet werden.

Nachdem Sie Ihren Endpunkt bereitgestellt haben, wird sein Name als Link angezeigt. Klicken Sie auf den Link, um spezifische Informationen zu Ihrem Endpunkt anzuzeigen (z. B. Endpunktschlüssel, Endpunkt-URL und Beispielcode).

## <a name="view-logging-data"></a>Anzeigen von Protokolldaten

Protokolldaten stehen unter **Endpunkt** > **Details** zum Download zur Verfügung.
> [!NOTE]
>Protokolldaten stehen 30 Tage lang in Microsoft-eigenem Speicher zur Verfügung. Nach diesem Zeitraum werden sie entfernt. Falls ein kundeneigenes Speicherkonto mit dem Cognitive Services-Abonnement verknüpft ist, werden die Protokolldaten nicht automatisch gelöscht.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, [wie Sie Ihr benutzerdefiniertes Modell verwenden](how-to-specify-source-language.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Vorbereiten und Testen Ihrer Daten](./how-to-custom-speech-test-and-train.md)
- [Überprüfen Ihrer Daten](how-to-custom-speech-inspect-data.md)
- [Bewerten Ihrer Daten](how-to-custom-speech-evaluate-data.md)
