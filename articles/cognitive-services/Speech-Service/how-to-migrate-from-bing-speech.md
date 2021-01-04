---
title: Migrieren von der Bing-Spracheingabe zum Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie aus einem vorhandenen Bing-Spracheingabe-Abonnement zum Speech-Dienst von Azure Cognitive Services migrieren.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: nitinme
ms.openlocfilehash: d0ffd786d3fb6bb5f0d70095d947c81caa070518
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499134"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migrieren von der Bing-Spracheingabe zum Speech-Dienst

Verwenden Sie diesen Artikel, um Ihre Anwendungen von der Bing-Spracheingabe-API zum Speech-Dienst zu migrieren.

In diesem Artikel werden die Unterschiede zwischen den Bing-Spracheingabe-APIs und dem Speech-Dienst beschrieben und Strategien für die Migration Ihrer Anwendungen vorgeschlagen. Ihr Abonnementschlüssel für die Bing-Spracheingabe-API funktioniert nicht mit dem Speech-Dienst. Sie benötigen ein neues Abonnement für den Speech-Dienst.

Mit einem einzelnen Abonnementschlüssel für den Speech-Dienst wird Zugriff auf die folgenden Funktionen gewährt. Da die Abrechnung jeweils separat erfolgt, werden Ihnen nur die genutzten Features berechnet.

* [Spracherkennung](speech-to-text.md)
* [Benutzerdefinierte Spracherkennung](https://cris.ai)
* [Sprachsynthese](text-to-speech.md)
* [Benutzerdefinierte Stimmen für Sprachsynthese](./how-to-custom-voice-create-voice.md)
* [Sprachübersetzung](speech-translation.md) (ohne [Textübersetzung](../translator/translator-info-overview.md))

Das [Sprach-SDK](speech-sdk.md) ist ein funktionaler Ersatz für die Clientbibliotheken der Bing-Spracheingabe. Es verwendet aber eine andere API.

## <a name="comparison-of-features"></a>Vergleich der Features

Der Speech-Dienst ähnelt weitgehend der Bing-Spracheingabe, wobei die folgenden Unterschiede bestehen.

| Funktion | Bing-Spracheingabe | Speech-Dienst | Details |
|--|--|--|--|
| C# SDK | :heavy_check_mark: | :heavy_check_mark: | Der Speech-Dienst unterstützt Windows 10, UWP (Universelle Windows-Plattform) und .NET Standard 2.0. |
| C++ SDK | :heavy_minus_sign: | :heavy_check_mark: | Der Speech-Dienst unterstützt Windows und Linux. |
| Java-SDK | :heavy_check_mark: | :heavy_check_mark: | Der Speech-Dienst unterstützt Android- und Speech-Geräte. |
| Kontinuierliche Spracherkennung | 10 Minuten | Unbegrenzt | Das Speech SDK unterstützt eine unbegrenzte kontinuierliche Erkennung und stellt die Verbindung bei einem Timeout oder einem Verbindungsabbruch automatisch wieder her. |
| Teil- oder Zwischenergebnisse | :heavy_check_mark: | :heavy_check_mark: | Unterstützt mit dem Speech SDK. |
| Benutzerdefinierte Spracherkennungsmodelle | :heavy_check_mark: | :heavy_check_mark: | Die Bing-Spracheingabe erfordert ein separates Custom Speech-Abonnement. |
| Benutzerdefinierte Voicefonts | :heavy_check_mark: | :heavy_check_mark: | Die Bing-Spracheingabe erfordert ein separates Custom Voice-Abonnement. |
| 24-KHz-Stimmen | :heavy_minus_sign: | :heavy_check_mark: |
| Erkennung der Absicht bei gesprochenen Inhalten | Erfordert separaten LUIS-API-Aufruf | Integriert (mit SDK) | Sie können einen LUIS-Schlüssel mit dem Speech-Dienst verwenden. |
| Einfache Absichtserkennung | :heavy_minus_sign: | :heavy_check_mark: |
| Batch-Transkription bei langen Audiodateien | :heavy_minus_sign: | :heavy_check_mark: |
| Erkennungsmodus | Manuell über den Endpunkt-URI | Automatic | Der Erkennungsmodus ist im Speech-Dienst nicht verfügbar. |
| Endpunktregion | Global | Länderspezifisch | Regionale Endpunkte verbessern die Latenz. |
| REST-APIs | :heavy_check_mark: | :heavy_check_mark: | Die REST-APIs des Speech-Diensts sind kompatibel mit der Bing-Spracheingabe (unterschiedlicher Endpunkt). REST-APIs unterstützen Sprachsynthese- und eingeschränkte Spracherkennungsfunktionen. |
| WebSockets-Protokolle | :heavy_check_mark: | :heavy_minus_sign: | Das Speech SDK abstrahiert Websocketverbindungen für Funktionen, die eine ständige Verbindung mit dem Dienst erfordern, sodass es keine Unterstützung mehr gibt, diese manuell zu abonnieren. |
| Dienst-zu-Dienst-API-Aufrufe | :heavy_check_mark: | :heavy_minus_sign: | Wird bei der Bing-Spracheingabe über die C#-Dienstbibliothek bereitgestellt. |
| Open Source SDK | :heavy_check_mark: | :heavy_minus_sign: |

Der Speech-Dienst verwendet ein zeitbasiertes Preismodell (anstelle eines transaktionsbasierten Modells). Ausführlichere Informationen finden Sie unter [Preise für den Speech-Dienst](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="migration-strategies"></a>Migrationsstrategien

Wenn Sie oder Ihr Unternehmen Anwendungen in der Entwicklungs- oder Produktionsumgebung nutzen, die eine Bing-Spracheingabe-API verwenden, sollten Sie diese aktualisieren, um den Speech-Dienst so schnell wie möglich zu nutzen. Informationen zu verfügbaren SDKs, Codebeispielen und Tutorials finden Sie in der [Dokumentation zum Speech-Dienst](index.yml).

Die [REST-APIs](./overview.md#reference-docs) des Speech-Diensts sind mit den Bing-Spracheingabe-APIs kompatibel. Wenn Sie derzeit die Bing-Spracheingabe-REST-APIs verwenden, müssen Sie nur den REST-Endpunkt ändern und zu einem Abonnementschlüssel des Speech-Diensts wechseln.

Wenn Sie eine Bing-Spracheingabe-Clientbibliothek für eine bestimmte Programmiersprache verwenden, erfordert die Migration zum [Spracherkennungs-SDK](speech-sdk.md) Änderungen an Ihrer Anwendung, weil es sich um eine andere API handelt. Das Spracherkennungs-SDK kann Ihren Code einfacher gestalten und Ihnen auch Zugriff auf neue Features bieten. Das Speech SDK ist in zahlreichen Programmiersprachen verfügbar. APIs sind auf den verschiedenen Plattformen ähnlich, was die Entwicklung für mehrere Plattformen erleichtert.

Der Speech-Dienst bietet keinen globalen Endpunkt. Ermitteln Sie, ob Ihre Anwendung effizient funktioniert, wenn sie einen einzigen regionalen Endpunkt für den gesamten Datenverkehr verwendet. Wenn nicht, verwenden Sie die Geolokalisierung, um den effizientesten Endpunkt zu bestimmen. Sie benötigen für jede verwendete Region ein separates Abonnement für den Speech-Dienst.

Erste Schritte mit dem Spracherkennungs-SDK:

1. Laden Sie das [Spracherkennungs-SDK](speech-sdk.md) herunter.
1. Arbeiten Sie die [Leitfäden für den Schnellstart](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) und die [Tutorials](how-to-recognize-intents-from-speech-csharp.md) für den Speech-Dienst durch. Sehen Sie sich auch die [Codebeispiele](./speech-sdk.md#sample-source-code) an, um sich mit den neuen APIs vertraut zu machen.
1. Aktualisieren Sie Ihre Anwendung, um den Speech-Dienst verwenden zu können.

## <a name="support"></a>Support

Kunden der Bing-Spracheingabe sollten sich an den Kundendienst wenden, indem sie ein [Supportticket](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) öffnen. Sie können sich auch an uns wenden, wenn Ihr Supportbedarf einen [technischen Supportplan](https://azure.microsoft.com/support/plans/) erfordert.

Informationen zur Unterstützung von Speech-Dienst, SDK und API finden Sie auf der [Supportseite](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) des Speech-Diensts.

## <a name="next-steps"></a>Nächste Schritte

* [Speech-Dienst kostenlos testen](overview.md#try-the-speech-service-for-free)
* [Erste Schritte mit der Spracherkennung](get-started-speech-to-text.md)
* [Erste Schritte bei der Sprachsynthese](get-started-text-to-speech.md)

## <a name="see-also"></a>Weitere Informationen

* [Versionshinweise zum Speech-Dienst](releasenotes.md)
* [Worum handelt es sich beim Spracherkennungsdienst?](overview.md)
* [Dokumentation zum Speech-Dienst und dem Speech SDK](speech-sdk.md#get-the-speech-sdk)
