---
title: 'Übersicht über die Absichtserkennung: Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Mit der Absichtserkennung können Sie vordefinierte Zielsetzungen von Benutzern erkennen. Dieser Artikel bietet einen Überblick über die Vorteile und Funktionen des Absichtserkennungsdiensts.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: v-demjoh
keywords: Absichtserkennung
ms.openlocfilehash: 215b25e440b8cad76e0656e47d32b184edf4ac66
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018700"
---
# <a name="what-is-intent-recognition"></a>Was ist die Absichtserkennung?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

In dieser Übersicht lernen Sie die Vorteile und Funktionen der Absichtserkennung kennen. Das Speech SDK von Cognitive Services ist in den Language Understanding Service (LUIS) integriert, um Absichtserkennung zu ermöglichen. Eine Absicht ist eine Aufgabe, die der Benutzer ausführen möchte: einen Flug buchen, sich über das Wetter informieren oder einen Anruf tätigen.
Mithilfe der Absichtserkennung können Ihre Anwendungen, Tools und Geräte bestimmen, was der Benutzer basierend auf den in LUIS definierten Optionen initiieren oder tun möchte.

## <a name="luis-key-required"></a>LUIS-Schlüssel erforderlich

* LUIS ist in Speech Service integriert, um Absichten aus Sprache zu erkennen. Ein Speech Service-Abonnement ist nicht erforderlich, nur LUIS.
* Sprechabsichtserkennung ist in das SDK integriert. Sie können einen LUIS-Schlüssel mit dem Speech-Dienst verwenden.
* Die Absichtserkennung durch das Speech SDK wird [für einen Teil der von LUIS unterstützten Regionen angeboten](./regions.md#intent-recognition).

## <a name="get-started"></a>Erste Schritte

Verwenden Sie den [Schnellstart](quickstarts/intent-recognition.md), um mit der Absichtserkennung zu beginnen.

## <a name="sample-code"></a>Beispielcode

Beispielcode für Absichtserkennung:

* [Schnellstart: Verwenden der vordefinierten Home Automation-App](../luis/luis-get-started-create-app.md)
* [Erkennen von Absichten anhand von gesprochener Sprache mit dem Speech SDK für C#](./how-to-recognize-intents-from-speech-csharp.md)
* [Absichtserkennung und andere Speech-Dienste mit Unity in C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/speechrecognizer)
* [Erkennen von Absichten mit dem Speech SDK für Python](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
* [Absichtserkennung und andere Speech-Dienste mit dem Speech SDK für C++ unter Windows](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console)
* [Absichtserkennung und andere Speech-Dienste mit dem Speech SDK für Java unter Windows oder Linux](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/jre/console)
* [Absichtserkennung und andere Speech-Dienste mit dem Speech SDK für JavaScript in einem Webbrowser](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser)

## <a name="reference-docs"></a>Referenz

* [Speech SDK](./speech-sdk.md)

## <a name="next-steps"></a>Nächste Schritte

* Arbeiten Sie die [Schnellstartanleitung](quickstarts/intent-recognition.md) für die Absichtserkennung durch.
* [Kostenloses Testen des Speech-Diensts](overview.md#try-the-speech-service-for-free)
* [Abrufen des Speech SDK](speech-sdk.md)