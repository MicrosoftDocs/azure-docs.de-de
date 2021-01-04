---
title: 'Schnellstart: Erstellen eines LUIS-Schlüssels'
titleSuffix: Azure Cognitive Services
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie eine LUIS-Anwendung erstellen und einen Schlüssel erhalten.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 06/25/2020
ms.author: trbye
ms.openlocfilehash: f547b353091cc81816f0974fced8d4dc836d22b2
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023819"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>Schnellstart: Abrufen eines LUIS-Endpunktschlüssels

## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich, dass folgende Elemente vorhanden sind, bevor Sie mit diesem Tutorial beginnen:

* Ein LUIS-Konto. Sie können ein kostenloses Konto über das [LUIS-Portal](https://www.luis.ai/home) erhalten.

## <a name="luis-and-speech"></a>LUIS und Spracherkennung

LUIS ist in Speech Service integriert, um Absichten aus Sprache zu erkennen. Ein Speech Service-Abonnement ist nicht erforderlich, nur LUIS.

LUIS verwendet drei Arten von Schlüsseln:

|Schlüsseltyp|Zweck|
|--------|-------|
|Erstellen|Ermöglicht Ihnen das programmgesteuerte Erstellen und Ändern von LUIS-Apps.|
|Starter|Hiermit können Sie Ihre LUIS-Anwendung nur mit Text testen.|
|Endpunkt |Autorisiert den Zugriff auf eine bestimmte LUIS-App.|

Für dieses Tutorial benötigen Sie den Endpunktschlüsseltyp. Dieses Tutorial verwendet die LUIS-Beispiel-App für die Gebäudeautomatisierung, die Sie erstellen können, indem Sie die Anleitung im Schnellstart [Verwenden der vordefinierten Home Automation-App](../../luis/luis-get-started-create-app.md) befolgen. Wenn Sie selbst eine LUIS-App erstellt haben, können Sie diese stattdessen verwenden.

Wenn Sie eine LUIS-App erstellen, wird automatisch ein Startschlüssel generiert, mit dem Sie die App mithilfe von Textabfragen testen können. Mit diesem Schlüssel ist keine Integration des Speech-Diensts möglich, und er ist für dieses Tutorial nicht geeignet. Erstellen Sie eine LUIS-Ressource im Azure-Dashboard, und weisen Sie sie der LUIS-App zu. Sie können den Tarif für ein kostenloses Abonnement für dieses Tutorial verwenden.

Nachdem Sie die LUIS-Ressource im Azure-Dashboard erstellt haben, melden Sie sich beim [LUIS-Portal](https://www.luis.ai/home) an, wählen Sie Ihre Anwendung auf der Seite **Eigene Anwendungen** aus, und wechseln Sie dann zur Seite **Verwalten** der App. Wählen Sie schließlich auf der Seitenleiste die Option **Schlüssel und Endpunkte** aus.

![Schlüssel und Endpunkteinstellungen im LUIS-Portal](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-endpoints-page.png)

Gehen Sie auf der Seite **Schlüssel- und Endpunkteinstellungen** folgendermaßen vor:

1. Scrollen Sie nach unten zum Abschnitt **Ressourcen und Schlüssel**, und wählen Sie **Ressource zuweisen** aus.
1. Nehmen Sie im Dialogfeld **App einen Schlüssel zuweisen** die folgenden Änderungen vor:

   * Wählen Sie unter **Mandant** die Option **Microsoft** aus.
   * Wählen Sie unter **Abonnementname** das Azure-Abonnement aus, das die LUIS-Ressource enthält, die Sie verwenden möchten.
   * Wählen Sie unter **Schlüssel** die LUIS-Ressource aus, die Sie mit der App verwenden möchten.

   Nach einem kurzen Moment wird das neue Abonnement in der Tabelle unten auf der Seite angezeigt.

1. Wählen Sie das Symbol neben einem Schlüssel aus, um diesen in die Zwischenablage zu kopieren. (Sie können einen der beiden Schlüssel verwenden.)

![Abonnementschlüssel der LUIS-App](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-assigned.png)


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erkennen von Absichten](~/articles/cognitive-services/Speech-Service/quickstarts/intent-recognition.md)