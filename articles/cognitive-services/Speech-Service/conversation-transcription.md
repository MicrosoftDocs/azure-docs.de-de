---
title: 'Unterhaltungstranskription (Vorschau): Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Bei der Unterhaltungstranskription handelt es sich um eine Lösung für Besprechungen, die Erkennung, Sprecher-ID und Diarisierung miteinander kombiniert, um die Transkription einer beliebigen Unterhaltung zu ermöglichen.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
ms.openlocfilehash: c27493748381a3aced92aab41745f352f228c50e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024992"
---
# <a name="what-is-conversation-transcription-in-meetings-preview"></a>Worum handelt es sich bei der Unterhaltungstranskription (Vorschau) in Besprechungen?

Die Unterhaltungstranskription ist eine [Spracherkennungslösung](speech-to-text.md), die Spracherkennung, Sprecheridentifikation und Satzzuordnung zu jedem Sprecher (auch als _Diarisierung_ bezeichnet) kombiniert, um eine Echtzeit- und/oder asynchrone Transkription einer beliebigen Unterhaltung zu ermöglichen. Die Unterhaltungstranskription unterscheidet Sprecher in einer Unterhaltung, um festzustellen, wer was wann gesagt hat, und vereinfacht es für Entwickler, ihren Anwendungen, die eine Diarisierung mit mehreren Sprechern durchführen, eine Spracherkennung hinzuzufügen.

## <a name="key-features"></a>Wichtige Features

- **Zeitstempel**: Jede Äußerung eines Sprechers weist einen Zeitstempel auf, sodass Sie leicht erkennen können, wann ein Ausdruck geäußert wurde.
- **Lesbare Transkriptionen**: Transkripte werden automatisch mit Formatierung und Interpunktion versehen, um sicherzustellen, dass der Text genau dem entspricht, was gesagt wurde.
- **Benutzerprofile**: Benutzerprofile werden generiert, indem Sprachbeispiele von Benutzern erfasst und an die Signaturgenerierung gesendet werden.
- **Sprecheridentifikation**: Sprecher werden über Benutzerprofile identifiziert und jedem Sprecher ist ein _Sprecherbezeichner_ zugeordnet.
- **Diarisierung mehrerer Sprecher**: Ermittlung, wer was gesagt hat, durch Synthetisieren des Audiostreams mit den einzelnen Sprecherbezeichnern.
- **Echtzeittranskription**: Bereitstellung von Live-Transkripten darüber, wer was und wann sagt, während die Unterhaltung stattfindet.
- **Asynchrone Transkription**: Bereitstellung von Transkripten mit höherer Genauigkeit durch Verwendung eines Mehrkanalaudiostreams.

> [!NOTE]
> Obwohl die Unterhaltungstranskription die Anzahl der Sprecher im Raum nicht begrenzt, ist sie für 2 bis 10 Sprecher pro Sitzung optimiert.

## <a name="get-started"></a>Erste Schritte

Informationen zu den ersten Schritten finden Sie in der [Schnellstartanleitung](how-to-use-conversation-transcription.md) zur Unterhaltungstranskription in Echtzeit.

## <a name="use-cases"></a>Anwendungsfälle

Um Besprechungen für alle Beteiligten, z. B. taube und schwerhörige Teilnehmer, zu ermöglichen, ist es wichtig, dass die Transkription in Echtzeit erfolgt. Die Unterhaltungstranskription im Echtzeitmodus nimmt den Audiostream von Besprechungen auf und bestimmt, wer was sagt, sodass alle Besprechungsteilnehmer der Transkription folgen und ohne Verzögerung an der Besprechung teilnehmen können.

### <a name="improved-efficiency"></a>Verbesserte Effizienz

Die Besprechungsteilnehmer können sich auf die Besprechung konzentrieren und das Aufzeichnen der Notizen der Unterhaltungstranskription überlassen. Die Teilnehmer können sich aktiv an der Besprechung beteiligen und schnell die nächsten Schritte verfolgen, indem sie das Transkript verwenden, anstatt Notizen zu machen und möglicherweise etwas während der Besprechung zu verpassen.

## <a name="how-it-works"></a>Funktionsweise

Dies ist eine allgemeine Übersicht darüber, wie die Unterhaltungstranskription funktioniert.

![Diagramm zur Einbindung der Unterhaltungstranskription](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>Erwartete Eingaben

- **Mehrkanalaudiostream**: Weitere Informationen zu Spezifikationen und Design finden Sie unter den [Mikrofonempfehlungen für das Speech-Geräte-SDK von Microsoft](./speech-devices-sdk-microphone.md). Weitere Informationen zum Development Kit und dessen Erwerb finden Sie unter [Abrufen des Speech-Geräte-SDK von Microsoft](./get-speech-devices-sdk.md).
- **Benutzersprachbeispiele**: Für die Unterhaltungstranskription sind vor der Unterhaltung Benutzerprofile erforderlich. Sie müssen Audioaufzeichnungen von jedem Benutzer sammeln und die Aufzeichnungen dann an den [Dienst für die Signaturgenerierung](https://aka.ms/cts/signaturegenservice) senden, um die Audioaufnahmen zu überprüfen und Benutzerprofile zu generieren.

> [!NOTE]
> Samples der Benutzerstimme sind optional. Ohne diese Eingabe weist die Transkription verschiedene Sprecher aus, zeigt sie aber als „Sprecher1“, „Speaker2“ usw., statt sie als zuvor registrierte Namen spezifischer Sprecher zu erkennen.


## <a name="real-time-vs-asynchronous"></a>Echtzeitmodus im Vergleich zum asynchronen Modus

Die Unterhaltungstranskription bietet drei Transkriptionsmodi:

### <a name="real-time"></a>Echtzeit

Die Audiodaten werden live verarbeitet, um den Sprecherbezeichner und das Transkript zurückzugeben. Wählen Sie diesen Modus, wenn Ihre Transkriptionslösung den Teilnehmern der Unterhaltung eine Live-Transkriptansicht ihrer laufenden Unterhaltung bieten soll. Die Erstellung einer Anwendung, die Besprechungen für gehörlose und schwerhörige Teilnehmer einfacher zugänglich macht, ist z. B. ein idealer Anwendungsfall für die Echtzeittranskription.

### <a name="asynchronous"></a>Asynchron

Die Audiodaten werden als Batch verarbeitet, um den Sprecherbezeichner und das Transkript zurückzugeben. Wählen Sie diesen Modus aus, wenn Ihre Transkriptionslösung eine höhere Genauigkeit ohne Live-Transkriptansicht bieten soll. Wenn Sie z. B. eine Anwendung erstellen möchten, mit der die Besprechungsteilnehmer verpasste Besprechungen leicht nachvollziehen können, verwenden Sie den asynchronen Transkriptionsmodus, um Transkriptionsergebnisse mit hoher Genauigkeit zu erhalten.

### <a name="real-time-plus-asynchronous"></a>Echtzeit- und asynchroner Modus

Audiodaten werden live verarbeitet, um den Sprecherbezeichner und das Transkript zurückzugeben, und zusätzlich wird eine Anforderung erstellt, um auch ein Transkript mit hoher Genauigkeit durch asynchrone Verarbeitung zu erhalten. Wählen Sie diesen Modus aus, wenn Ihre Anwendung eine Echtzeittranskription benötigt, aber auch eine Transkription mit höherer Genauigkeit für die Verwendung nach der Unterhaltung oder Besprechung erfordert.

## <a name="language-support"></a>Sprachunterstützung

Aktuell unterstützt die Unterhaltungstranskription [alle Sprachen für Sprache-in-Text](language-support.md#speech-to-text) in den folgenden Regionen:  `centralus`, `eastasia`, `eastus`, `westeurope`. Wenn Sie die Unterstützung zusätzlicher Gebietsschemas benötigen, wenden Sie sich an das [Team für das Unterhaltungstranskriptionfeature](mailto:CTSFeatureCrew@microsoft.com).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Transkribieren von Unterhaltungen in Echtzeit](how-to-use-conversation-transcription.md)