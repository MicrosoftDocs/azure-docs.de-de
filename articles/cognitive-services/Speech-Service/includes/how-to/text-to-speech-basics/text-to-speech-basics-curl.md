---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 8a877e1773431053c5ad7344209076cb868a0ee3
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425298"
---
In dieser Schnellstartanleitung wird beschrieben, wie Sie Text in Sprache konvertieren, indem Sie den Speech-Dienst und cURL verwenden.

Allgemeine Informationen zu den Konzepten von Text-zu-Sprache finden Sie im [Übersichtsartikel](../../../text-to-speech.md).

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein Azure-Konto und über ein Abonnement für den Speech-Dienst verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-text-to-speech"></a>Konvertieren von Text in Sprache

Führen Sie an der Eingabeaufforderung den folgenden Befehl aus. Sie müssen die folgenden Werte in den Befehl einfügen.
- Ihr Abonnementschlüssel des Spracherkennungsdiensts.
- Die Region Ihres Speech-Diensts.

Unter Umständen ist es auch erforderlich, die folgenden Werte zu ändern.
- Headerwert `X-Microsoft-OutputFormat`, mit dem das Format der Audioausgabe gesteuert wird. Eine Liste mit den unterstützten Formaten für die Audioausgabe finden Sie in der [Referenz zur Text-to-Speech-REST-API](../../../rest-text-to-speech.md#audio-outputs).
- Die Stimme für die Ausgabe. Eine Liste mit den Stimmen, die für Ihren Speech-Endpunkt verfügbar sind, finden Sie im nächsten Abschnitt.
- Die Ausgabedatei. In diesem Beispiel leiten wir die Antwort des Servers an eine Datei mit dem Namen `output.wav` weiter.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/text-to-speech.sh":::

## <a name="list-available-voices-for-your-speech-endpoint"></a>Auflisten von verfügbaren Stimmen für Ihren Speech-Endpunkt

Führen Sie den folgenden Befehl aus, um die verfügbaren Stimmen für Ihren Speech-Endpunkt aufzulisten.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="request":::

Die Antwort sollte in etwa wie folgt aussehen:

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="response":::