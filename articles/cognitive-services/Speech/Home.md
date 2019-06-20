---
title: Microsoft Bing-Spracheingabe-Dienst | Microsoft-Dokumentation
titlesuffix: Azure Cognitive Services
description: Verwenden Sie die Sprach-API von Microsoft, um Ihre Apps mit sprachbasierten Aktionen zu versehen – einschließlich Echtzeitinteraktion mit Benutzern.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d2c7211831658a18e65e04aa753607f4eb22dac8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515085"
---
# <a name="what-is-bing-speech"></a>Was ist die Bing-Spracheingabe?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Mit der cloudbasierten Bing-Spracheingabe-API von Microsoft können Entwickler komfortabel leistungsfähige Sprachfunktionen in ihre Anwendungen integrieren. Hierzu zählen beispielsweise die Steuerung per Sprachbefehl, Benutzerdialoge in natürlicher Sprache sowie Transkription und Diktat. Die Sprach-API von Microsoft unterstützt sowohl die *Spracherkennung* (Umwandlung von Sprache in Text) als auch die *Sprachsynthese* (Umwandlung von Text in Sprache).

- Die **Spracherkennungs-API** wandelt gesprochene Sprache in Text um, der als Eingabe oder Befehl zur Steuerung Ihrer Anwendung verwendet werden kann.
- Die **Sprachsynthese-API** wandelt Text in Audiostreams um, die für den Benutzer Ihrer Anwendung wiedergegeben werden können.

## <a name="speech-to-text-speech-recognition"></a>Spracherkennung (Umwandlung von Sprache in Text)

Die Spracherkennungs-API von Microsoft *überträgt* Audiodatenströme in Text, den Ihre Anwendung dem Benutzer anzeigen oder als Befehlseingabe nutzen kann. Er bietet Entwicklern zwei Möglichkeiten, ihre Apps mit Sprachfunktionen zu erweitern: REST-APIs **oder** websocketbasierte Clientbibliotheken.

- [REST-APIs](GetStarted/GetStartedREST.md): Entwickler können in ihren Apps HTTP-Aufrufe an den Spracherkennungsdienst richten.
- [Clientbibliotheken](GetStarted/GetStartedClientLibraries.md): Entwickler, die erweiterte Features nutzen möchten, können Microsoft Speech-Clientbibliotheken herunterladen und in ihre Apps einbinden.  Die Clientbibliotheken sind für verschiedene Plattformen (Windows, Android, iOS) mit verschiedenen Sprachen (C#, Java, JavaScript, Objective-C) verfügbar. Im Gegensatz zu den REST-APIs nutzen die Clientbibliotheken ein auf Websockets basierendes Protokoll.

| Anwendungsfälle | [REST-APIs](GetStarted/GetStartedREST.md) | [Clientbibliotheken](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Konvertieren von kurzem gesprochenem Audio mit einer Länge von weniger als 15 Sekunden (beispielsweise Befehle) ohne Zwischenergebnisse | Ja | Ja |
| Konvertieren von langem Audio (länger als 15 Sekunden) | Nein | Ja |
| Streamen von Audio mit Zwischenergebnissen | Nein | Ja |
| Verstehen des aus Audio konvertierten Texts mithilfe von LUIS | Nein | Ja |

Unabhängig vom gewählten Ansatz (REST-APIs oder Clientbibliotheken) unterstützt der Speech-Dienst von Microsoft Folgendes:

- Erweiterte Spracherkennungstechnologien von Microsoft, die von Cortana, Office-Diktat, Office-Übersetzer und anderen Microsoft-Produkten verwendet werden.
- Fortlaufende Erkennung in Echtzeit. Die Spracherkennungs-API ermöglicht es Benutzern, Audio in Echtzeit in Text umzuwandeln, und unterstützt den Empfang von Zwischenergebnissen für die bislang erkannten Wörter. Der Speech-Dienst unterstützt auch die Erkennung des Endes der Spracheingabe. Darüber hinaus können Benutzer zusätzliche Formatierungsfunktionen wie Großschreibung und Interpunktion, Filterung von anstößigen Ausdrücken und Textnormalisierung auswählen.
- Unterstützt optimierte Spracherkennungsergebnisse für *interaktive Szenarien*, *Konversationen* und *Diktierszenarien*. Für Benutzerszenarien, die benutzerdefinierte Sprach- und Akustikmodelle erfordern, ermöglicht [Custom Speech Service](../custom-speech-service/cognitive-services-custom-speech-home.md) die Erstellung von Sprachmodellen, die speziell auf Ihre Anwendung und Benutzer zugeschnitten sind.
- Unterstützung zahlreicher gesprochener Sprachen in verschiedenen Dialekten. Eine vollständige Liste mit den unterstützten Sprachen für die einzelnen Erkennungsmodi finden Sie unter [Unterstützte Sprachen](api-reference-rest/supportedlanguages.md).
- Integration von Sprachverständnis. Neben der Umwandlung von Audioeingaben in Text bietet die *Spracherkennung* Anwendungen zusätzlich die Möglichkeit, die Bedeutung des Texts zu erfassen. Hierzu werden mithilfe von [Language Understanding Intelligent Service (LUIS)](../LUIS/what-is-luis.md) Absichten und Entitäten aus dem erkannten Text extrahiert.

### <a name="next-steps"></a>Nächste Schritte

- Beginnen Sie mit der Verwendung des Spracherkennungsdiensts von Microsoft mit [REST-APIs](GetStarted/GetStartedREST.md) oder [Clientbibliotheken](GetStarted/GetStartedClientLibraries.md).
- Sehen Sie sich [Beispielanwendungen](samples.md) in Ihrer bevorzugten Programmiersprache an.
- Informieren Sie sich im Referenzabschnitt über Details zum [Spracherkennungsprotokoll von Microsoft](API-Reference-REST/websocketprotocol.md) sowie über API-Referenzen.

## <a name="text-to-speech-speech-synthesis"></a>Sprachsynthese (Umwandlung von Text in Sprache)

*Sprachsynthese-APIs* verwenden REST, um strukturierten Text in einen Audiostream umzuwandeln. Die APIs bieten eine schnelle Umwandlung von Text in Sprache mit verschiedenen Stimmen und Sprachen. Darüber hinaus können Benutzer Audioeigenschaften wie Aussprache, Lautstärke, Tonlage usw. mithilfe von SSML-Tags ändern.

### <a name="next-steps"></a>Nächste Schritte

- Beginnen Sie mit der Verwendung des Sprachsynthesediensts von Microsoft: [Referenz zur Text-to-Speech-API](api-reference-rest/bingvoiceoutput.md). Eine vollständige Liste mit den unterstützten Sprachen und Stimmen der Sprachsynthese finden Sie unter [Supported locales and voice fonts](api-reference-rest/bingvoiceoutput.md#SupLocales) (Unterstützt Gebietsschemas und Voicefonts).
