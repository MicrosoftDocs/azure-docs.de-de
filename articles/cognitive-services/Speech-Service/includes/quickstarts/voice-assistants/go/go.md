---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: c37637ba1cb5154bbd56f7fd22bbbca62b8c3438
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187627"
---
## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen:

> [!div class="checklist"]
> * [Erstellen einer Azure Speech-Ressource](../../../../overview.md#try-the-speech-service-for-free)
> * [Einrichten Ihrer Entwicklungsumgebung und Erstellen eines leeren Projekts](../../../../quickstarts/setup-platform.md)
> * Erstellen eines Bots, der mit dem [Direct Line Speech-Kanal](/azure/bot-service/bot-service-channel-connect-directlinespeech) verbunden ist
> * Stellen Sie sicher, dass Sie Zugriff auf ein Mikrofon für die Audioaufnahme haben.
>
  > [!NOTE]
  > Beachten Sie [die Liste mit den unterstützten Regionen für Sprach-Assistenten](~/articles/cognitive-services/speech-service/regions.md#voice-assistants), und stellen Sie sicher, dass Ihre Ressourcen in einer dieser Regionen bereitgestellt werden.

## <a name="setup-your-environment"></a>Einrichten Ihrer Umgebung

Aktualisieren Sie die go.mod-Datei mit der aktuellsten SDK-Version, indem Sie diese Zeile hinzufügen
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.14.0
)
```

## <a name="start-with-some-boilerplate-code"></a>Beginnen mit Codebausteinen
Ersetzen Sie den Inhalt Ihrer Quelldatei (z. B. `quickstart.go`) durch den Inhalt unten, der Folgendes beinhaltet:

- Die „main“-Paketdefinition
- Das Importieren der erforderlichen Module aus dem Speech SDK
- Variablen zum Speichern der Botinformationen, die später in dieser Schnellstartanleitung ersetzt werden
- Eine einfache Implementierung, bei der das Mikrofon für die Audioeingabe verwendet wird
- Ereignishandler für verschiedene Ereignisse, die während einer Sprachinteraktion stattfinden

```sh
package main

import (
    "fmt"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/dialog"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_BOT_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := dialog.NewBotFrameworkConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    connector, err := dialog.NewDialogServiceConnectorFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer connector.Close()
    activityReceivedHandler := func(event dialog.ActivityReceivedEventArgs) {
        defer event.Close()
        fmt.Println("Received an activity.")
    }
    connector.ActivityReceived(activityReceivedHandler)
    recognizedHandle := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognized ", event.Result.Text)
    }
    connector.Recognized(recognizedHandle)
    recognizingHandler := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognizing ", event.Result.Text)
    }
    connector.Recognizing(recognizingHandler)
    connector.ListenOnceAsync()
    <-time.After(10 * time.Second)
}
```

Ersetzen Sie die Werte `YOUR_SUBSCRIPTION_KEY` und `YOUR_BOT_REGION` durch tatsächliche Werte aus der Speech-Ressource.

- Navigieren Sie zum Azure-Portal, und öffnen Sie Ihre Speech-Ressource.
- Auf der linken Seite sehen Sie unter **Schlüssel und Endpunkt** zwei verfügbare Abonnementschlüssel.
    - Ersetzen Sie mit einem davon den Wert `YOUR_SUBSCRIPTION_KEY`.
- Sehen Sie sich auf der linken Seite unter **Übersicht** die Region an, und ordnen Sie sie dem Regionsbezeichner zu.
    - Verwenden Sie den Regionsbezeichner als Ersatz für den Wert `YOUR_BOT_REGION`, z. B. `"westus"` für **USA, Westen**.

   > [!NOTE]
   > Beachten Sie [die Liste mit den unterstützten Regionen für Sprach-Assistenten](~/articles/cognitive-services/speech-service/regions.md#voice-assistants), und stellen Sie sicher, dass Ihre Ressourcen in einer dieser Regionen bereitgestellt werden.

   > [!NOTE]
   > Informationen zum Konfigurieren Ihres Bots finden Sie in der Bot Framework-Dokumentation zum [Direct Line Speech-Kanal](/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="code-explanation"></a>Erläuterung zum Code
Für die Erstellung eines Sprachkonfigurationsobjekts sind der Speech-Abonnementschlüssel und die Region erforderlich. Das Konfigurationsobjekt wird zum Instanziieren eines Spracherkennungsobjekts benötigt.

Die Erkennungsinstanz bietet mehrere Spracherkennungsmöglichkeiten. In diesem Beispiel wird Sprache fortlaufend erkannt. Durch diese Funktion wird dem Speech-Dienst mitgeteilt, dass Sie viele Ausdrücke zur Erkennung senden, und wann das Programm beendet wird und die Spracherkennung einstellt. Erzielte Ergebnisse werden vom Code in die Konsole geschrieben.

## <a name="build-and-run"></a>Erstellen und Ausführen
Sie sind jetzt so weit, Ihr Projekt zu erstellen und den benutzerdefinierten Sprachassistenten mit dem Speech-Dienst zu testen.
1. Erstellen Sie Ihr Projekt, beispielsweise mit **„go build“**
2. Führen Sie das Modul aus, und sprechen Sie einen Ausdruck oder einen Satz in das Mikrofon Ihres Geräts. Ihre Spracheingabe wird an den Direct Line Speech-Kanal übermittelt, in Text transkribiert und als Ausgabe angezeigt.


> [!NOTE]
> Das Speech SDK verwendet für die Erkennung standardmäßig amerikanisches Englisch (en-us). Informationen zum Auswählen der Ausgangssprache finden Sie unter [Angeben der Ausgangssprache für die Spracherkennung](../../../../how-to-specify-source-language.md).

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [footer](./footer.md)]