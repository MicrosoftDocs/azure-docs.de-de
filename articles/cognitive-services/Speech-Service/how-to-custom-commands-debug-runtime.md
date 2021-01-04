---
title: Debuggen von Fehlern beim Ausführen einer Anwendung für benutzerdefinierte Befehle
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie, wie Sie Laufzeitfehler in einer Anwendung für benutzerdefinierte Befehle debuggen.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 49d9b91df896646da7bf36e077d9f3c9187137dd
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021795"
---
# <a name="debug-errors-when-running-a-custom-commands-application"></a>Debuggen von Fehlern beim Ausführen einer Anwendung für benutzerdefinierte Befehle

Dieser Artikel beschreibt, wie Sie debuggen können, wenn beim Ausführen der Anwendung für benutzerdefinierte Befehle entsprechende Fehler auftreten. 

## <a name="connection-failed"></a>Fehler bei der Verbindung

Wenn Sie Ihre Anwendung für benutzerdefinierte Befehle über eine [Clientanwendung (mit Speech SDK)](./how-to-custom-commands-setup-speech-sdk.md) oder einen [Windows-Sprachassistent-Client](./how-to-custom-commands-developer-flow-test.md) ausführen, können Verbindungsfehler auftreten (wie unten aufgeführt):

| Fehlercode | Details |
| ------- | -------- |
| [401](#error-401) | AuthenticationFailure: Authentifizierungsfehler beim WebSocket-Upgrade. |
| [1002](#error-1002)] | Vom Server wurde Statuscode '404' zurückgegeben, als Statuscode '101' erwartet wurde. |

### <a name="error-401"></a>Fehler 401
- Die in der Clientanwendung angegebene Region stimmt nicht mit der Region der Anwendung für benutzerdefinierte Befehle überein.

- Der Speech-Ressourcenschlüssel ist ungültig.
    
    Stellen Sie sicher, dass Sie den richtigen Speech-Ressourcenschlüssel verwenden.

### <a name="error-1002"></a>Fehler 1002 
- Ihre Anwendung für benutzerdefinierte Befehle wird nicht veröffentlicht.
    
    Veröffentlichen Sie Ihre Anwendung im Portal.

- Die ID Ihrer Anwendung für benutzerdefinierte Befehle ist ungültig.

    Stellen Sie sicher, dass die ID Ihrer Anwendung für benutzerdefinierte Befehle richtig ist.
 Anwendung für benutzerdefinierte Befehle außerhalb Ihrer Speech-Ressource

    Stellen Sie sicher, dass die Anwendung für benutzerdefinierte Befehle unter Ihrer Speech-Ressource erstellt wird.

Weitere Informationen zur Behandlung der Verbindungsprobleme finden Sie im Thema [Problembehandlung für den Windows Voice Assistant-Client](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf#troubleshooting).


## <a name="dialog-is-canceled"></a>Der Dialog ist abgebrochen.

Wenn Sie Ihre Anwendung für benutzerdefinierte Befehle ausführen, wird der Dialog abgebrochen, wenn einige Fehler auftreten.

- Wenn Sie die Anwendung im Portal testen, würde sie direkt die Abbruchbeschreibung anzeigen und ein Fehler-Earcon ausgeben. 

- Wenn Sie die Anwendung mit dem [Windows-Sprachassistent-Client](./how-to-custom-commands-developer-flow-test.md) ausführen, wird ein Fehler-Earcon ausgegeben. Sie finden das **Ereignis: CancelledDialog** unter den **Aktivitätsprotokollen**.

- Wenn Sie unserem Beispiel für eine Clientanwendung [Clientanwendung (mit Speech SDK)](./how-to-custom-commands-setup-speech-sdk.md) folgen, würde es ein Fehler-Earcon ausgeben. Sie finden das **Ereignis: CancelledDialog** unter dem **Status**.

- Wenn Sie Ihre eigene Clientanwendung erstellen, können Sie jederzeit Ihre gewünschten Logiken für die Behandlung der CancelledDialog-Ereignisse entwerfen.

Das Ereignis „CancelledDialog“ besteht aus einem Abbruchcode und einer Beschreibung, wie unten aufgelistet:

| Abbruchcode | Abbruchbeschreibung |
| ------- | --------------- | ----------- |
| [MaxTurnThresholdReached](#no-progress-was-made-after-the-max-number-of-turns-allowed) | Nach der maximal erlaubten Anzahl von Runden wurden keine Fortschritte erzielt. |
| [RecognizerQuotaExceeded](#recognizer-usage-quota-exceeded) | Nutzungskontingent der Erkennung wurde überschritten. |
| [RecognizerConnectionFailed](#connection-to-the-recognizer-failed) | Fehler beim Herstellen der Verbindung mit der Erkennung. |
| [RecognizerUnauthorized](#this-application-cannot-be-accessed-with-the-current-subscription) | Auf diese Anwendung kann mit dem aktuellen Abonnement nicht zugegriffen werden. |
| [RecognizerInputExceededAllowedLength](#input-exceeds-the-maximum-supported-length) | Die Eingabe überschreitet die maximal unterstützte Länge für die Erkennung. |
| [RecognizerNotFound](#recognizer-not-found) | Die Erkennung wurde nicht gefunden. |
| [RecognizerInvalidQuery](#invalid-query-for-the-recognizer) | Ungültige Abfrage für die Erkennung. |
| [RecognizerError](#recognizer-return-an-error) | Die Erkennung gibt einen Fehler zurück. |

### <a name="no-progress-was-made-after-the-max-number-of-turns-allowed"></a>Nach der maximal erlaubten Anzahl von Runden wurden keine Fortschritte erzielt.
Der Dialog wird abgebrochen, wenn ein erforderlicher Slot nach einer bestimmten Anzahl von Runden nicht erfolgreich aktualisiert wurde. Die integrierte maximale Anzahl beträgt 3.

### <a name="recognizer-usage-quota-exceeded"></a>Nutzungskontingent der Erkennung wurde überschritten.
Language Understanding (LUIS) hat Grenzen hinsichtlich der Ressourcennutzung. Normalerweise kann der Fehler „Nutzungskontingent der Erkennung wurde überschritten“ wie folgt verursacht werden: 
- Ihre LUIS-Dokumenterstellung überschreitet den Grenzwert.

    Fügen Sie eine Vorhersageressource zu Ihrer Anwendung für benutzerdefinierte Befehle hinzu: 
    1. Wechseln Sie zu **Einstellungen**, LUIS-Ressource.
    1. Wählen Sie eine Vorhersageressource aus **Vorhersageressource** aus, oder klicken Sie auf **Neue Ressource erstellen**. 

- Ihre LUIS-Vorhersageressource überschreitet den Grenzwert.

    Wenn Sie sich auf einer F0-Vorhersageressource befinden, hat diese einen Grenzwert von 10.000/Monat, 5 Abfragen/Sekunde.

Weitere Informationen zu den LUIS-Ressourceneinschränkungen finden Sie unter [Language Understanding: Ressourcennutzung und Grenzwert](../luis/luis-limits.md#resource-usage-and-limits)

### <a name="connection-to-the-recognizer-failed"></a>Fehler beim Herstellen der Verbindung mit der Erkennung.
Normalerweise bedeutet es einen vorübergehenden Verbindungsfehler zur LUIS-Erkennung (Language Understanding). Versuchen Sie es erneut und das Problem sollte behoben sein.

### <a name="this-application-cannot-be-accessed-with-the-current-subscription"></a>Auf diese Anwendung kann mit dem aktuellen Abonnement nicht zugegriffen werden.
Ihr Abonnement ist nicht autorisiert, auf die LUIS-Anwendung zuzugreifen. 

### <a name="input-exceeds-the-maximum-supported-length"></a>Eingabe überschreitet die maximal unterstützte Länge
Ihre Eingabe hat 500 Zeichen überschritten. Es sind maximal 500 Zeichen zur Eingabe von Äußerungen zulässig.

### <a name="invalid-query-for-the-recognizer"></a>Ungültige Abfrage für die Erkennung.
Ihre Eingabe hat 500 Zeichen überschritten. Es sind maximal 500 Zeichen zur Eingabe von Äußerungen zulässig.

### <a name="recognizer-return-an-error"></a>Die Erkennung gibt einen Fehler zurück.
Die LUIS-Erkennung hat einen Fehler bei dem Versuch zurückgegeben, Ihre Eingabe zu erkennen.

### <a name="recognizer-not-found"></a>Die Erkennung wurde nicht gefunden.
Der in Ihrem Dialogmodell für benutzerdefinierte Befehle angegebene Erkennungstyp kann nicht gefunden werden. Zurzeit unterstützen wir nur die [LUIS-Erkennung (Language Understanding)](https://www.luis.ai/).

## <a name="other-common-errors"></a>Andere häufige Fehler
### <a name="unexpected-response"></a>Unerwartete Antwort
Unerwartete Antworten können mehrere Ursachen haben. Mit folgenden Prüfungen kann begonnen werden:
- „Ja/Nein“-Absichten in Beispielsätzen

    Da wir derzeit keine „Ja/Nein“-Absichten unterstützen, außer bei Verwendung mit dem Bestätigungsfeature. Alle in den Beispielsätzen definierten „Ja/Nein“-Absichten würden nicht erkannt werden.

- Ähnliche Absichten und Beispielsätze unter den Befehlen

    Die Genauigkeit der LUIS-Erkennung kann beeinträchtigt werden, wenn zwei Befehle ähnliche Absichten und Beispielsätze teilen. Sie können versuchen, die Funktionalität von Befehlen und Beispielsätzen so deutlich wie möglich zu gestalten.

    Eine bewährte Methode zum Verbessern der Erkennungsgenauigkeit finden Sie unter [LUIS: Bewährte Methode](../luis/luis-concept-best-practices.md).

- Der Dialog ist abgebrochen.
    
    Prüfen Sie die Gründe für den Abbruch im obigen Abschnitt.

### <a name="error-while-rendering-the-template"></a>Fehler beim Rendern der Vorlage
In der Sprachausgabe wird ein nicht definierter Parameter verwendet. 

### <a name="object-reference-not-set-to-an-instance-of-an-object"></a>Objektverweis wurde nicht auf eine Objektinstanz festgelegt
Sie verwenden einen leeren Parameter in der JSON-Nutzlast, die in **Aktivität an Client senden** definiert ist.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen finden Sie in den Beispielen auf GitHub](https://aka.ms/speech/cc-samples)