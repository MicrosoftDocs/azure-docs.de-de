---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 0964872d03d6e321d25d51a18edbb4a6f0be8a4f
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94424719"
---
In dieser Schnellstartanleitung wird beschrieben, wie Sie die Konvertierung von Sprache in Text durchführen, indem Sie den Speech-Dienst und cURL verwenden.

Allgemeine Informationen zu den Konzepten von Sprache-in-Text finden Sie im [Übersichtsartikel](../../../speech-to-text.md).

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein Azure-Konto und über ein Abonnement für den Speech-Dienst verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-speech-to-text"></a>Konvertieren von Sprache in Text

Führen Sie an der Eingabeaufforderung den folgenden Befehl aus. Sie müssen die folgenden Werte in den Befehl einfügen.
- Ihr Abonnementschlüssel des Spracherkennungsdiensts.
- Die Region Ihres Speech-Diensts.
- Den Pfad für die Audioeingabedatei. Sie können Audiodateien per [Sprachsynthese (Text-zu-Sprache)](../../../get-started-text-to-speech.md) generieren.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="request":::

Die Antwort sollte in etwa wie folgt aussehen:

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="response":::

Weitere Informationen finden Sie in der [Referenz zur Spracherkennungs-REST-API](../../../rest-speech-to-text.md).