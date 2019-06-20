---
title: 'Unterhaltungstranskription: Speech-Dienste'
titleSuffix: Azure Cognitive Services
description: Die Unterhaltungstranskription ist ein erweitertes Feature der Speech-Dienste, das Spracherkennung, Sprecheridentifikation und Diarisierung in Echtzeit kombiniert. Die Unterhaltungstranskription eignet sich optimal für das Transkribieren persönlicher Besprechungen, wobei zwischen Sprechern unterschieden werden kann. Sie können sehen, wann jemand was gesagt hat, und die Teilnehmer können sich auf die Besprechung konzentrieren und zeitnah die nächsten Schritte ausführen. Dieses Feature verbessert auch die Barrierefreiheit. Durch eine Transkription können Sie Teilnehmer mit vermindertem Hörvermögen aktiv einbeziehen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: a31921e229a4bbfccd6fdd871666aad1eeef3232
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243872"
---
# <a name="what-is-conversation-transcription"></a>Was ist Unterhaltungstranskription?

Die Unterhaltungstranskription ist ein erweitertes Feature der Speech-Dienste, das Spracherkennung, Sprecheridentifikation und Diarisierung in Echtzeit kombiniert. Die Unterhaltungstranskription eignet sich optimal für das Transkribieren persönlicher Besprechungen, wobei zwischen Sprechern unterschieden werden kann. Sie können sehen, wann jemand was gesagt hat, und die Teilnehmer können sich auf die Besprechung konzentrieren und zeitnah die nächsten Schritte ausführen. Dieses Feature verbessert auch die Barrierefreiheit. Durch eine Transkription können Sie Teilnehmer mit vermindertem Hörvermögen aktiv einbeziehen.   

Die Unterhaltungstranskription bietet eine genaue Erkennung mit anpassbaren Sprachmodellen, die Sie an das branchen- und unternehmensspezifische Vokabular anpassen können. Darüber hinaus können Sie die Unterhaltungstranskription mit dem Speech Devices SDK kombinieren, um die Funktionsweise für Geräte mit mehreren Mikrofonen zu optimieren.

>[!NOTE]
> Derzeit wird die Unterhaltungstranskription für kleine Besprechungen empfohlen. Wenn Sie die Unterhaltungstranskription für Besprechungen in großem Umfang erweitern möchten, setzen Sie sich mit uns in Verbindung.

In diesem Diagramm sehen Sie die Hardware, Software und Dienste, die bei der Unterhaltungstranskription zusammenarbeiten.

![Diagramm zur Einbindung der Unterhaltungstranskription](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> Es ist ein kreisförmiges Mikrofonarray mit sieben Mikrofonen und einer spezifischen Geometriekonfiguration erforderlich. Weitere Informationen zu Spezifikationen und Design finden Sie unter den [Mikrofonempfehlungen für das Speech-Geräte-SDK von Microsoft](https://aka.ms/cts/microphone). Weitere Informationen zum Development Kit und dessen Erwerb finden Sie unter [Abrufen des Speech-Geräte-SDK von Microsoft](https://aka.ms/cts/getsdk).

## <a name="get-started-with-conversation-transcription"></a>Erste Schritte mit der Unterhaltungstranskription

Zum Einstieg in die Unterhaltungstranskription müssen Sie drei Schritte ausführen.

1. Sammeln von Sprachbeispielen der Benutzer
2. Generieren von Benutzerprofilen anhand der Sprachbeispiele
3. Verwenden des Speech SDK zum Identifizieren von Benutzern (Sprechern) und Transkribieren von gesprochener Sprache

## <a name="collect-user-voice-samples"></a>Sammeln von Sprachbeispielen der Benutzer

Zuerst müssen Sie Audioaufnahmen der einzelnen Benutzer sammeln. Die Sprache der Benutzer sollte in einer ruhigen Umgebung ohne Hintergrundgeräusche aufgezeichnet werden. Die empfohlene Länge der einzelnen Audiobeispiele liegt zwischen 30 Sekunden und zwei Minuten. Längere Audiobeispiele führen zu einer höheren Genauigkeit beim Identifizieren von Sprechern. Die Audioaufnahme muss Monokanal mit einer Abtastrate von 16 KHz sein.

Über die oben genannten Anleitungen hinaus liegt es an Ihnen, wie Audio aufgezeichnet und gespeichert wird – eine sichere Datenbank wird empfohlen. Im nächsten Abschnitt wird beschrieben, wie anhand dieser Audioaufnahmen Benutzerprofile generiert werden, die dann mit dem Speech SDK zum Erkennen von Sprechern verwendet werden.

## <a name="generate-user-profiles"></a>Generieren von Benutzerprofilen

Als Nächstes müssen Sie die gesammelten Audioaufnahmen an den Dienst für die Signaturgenerierung senden, um die Audioaufnahmen zu überprüfen und Benutzerprofile zu generieren. Beim [Dienst für die Signaturgenerierung](https://aka.ms/cts/signaturegenservice) handelt es sich um eine Reihe von REST-APIs, mit denen Sie Benutzerprofile generieren und abrufen können.

Zum Erstellen eines Benutzerprofils müssen Sie die `GenerateVoiceSignature`-API verwenden. Weitere Informationen zu Spezifikationen und Beispielcode stehen zur Verfügung:

> [!NOTE]
> Die Unterhaltungstranskription ist derzeit in „en-US“ und „zh-CN“ in den folgenden Regionen verfügbar: `centralus` und `eastasia`.

* [REST-Spezifikation](https://aka.ms/cts/signaturegenservice)
* [Verwenden der Unterhaltungstranskription](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>Transkribieren und Identifizieren von Sprechern

Die Unterhaltungstranskription setzt Mehrkanal-Audiostreams und Benutzerprofile als Eingaben voraus, um Transkriptionen zu erstellen und Sprecher zu identifizieren. Audio- und Benutzerprofildaten werden mit dem Speech Devices SDK an die Unterhaltungstranskription gesendet. Wie bereits erwähnt sind für die Unterhaltungstranskription ein kreisförmiges Mikrofonarray mit sieben Mikrofonen und das Speech Devices SDK erforderlich.

>[!NOTE]
> Weitere Informationen zu Spezifikationen und Design finden Sie unter den [Mikrofonempfehlungen für das Speech-Geräte-SDK von Microsoft](https://aka.ms/cts/microphone). Weitere Informationen zum Development Kit und dessen Erwerb finden Sie unter [Abrufen des Speech-Geräte-SDK von Microsoft](https://aka.ms/cts/getsdk).

Informationen zum Verwenden der Unterhaltungstranskription mit dem Speech Devices SDK finden Sie unter [Verwenden der Unterhaltungstranskription](https://aka.ms/cts/howto).


## <a name="quick-start-with-a-sample-app"></a>Schnellstart mit einer Beispielanwendung

Das Microsoft Speech-Geräte-SDK verfügt über eine Schnellstart-Beispielanwendung für alle gerätebezogenen Beispiele. Die Unterhaltungstranskription ist eines davon. Sie finden es in [Schnellstart zum Speech-Geräte-SDK für Android](https://aka.ms/sdsdk-quickstart) mit Beispielanwendung und dem Quellcode als Referenz.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informationen zum Speech-Geräte-SDK](speech-devices-sdk.md)
