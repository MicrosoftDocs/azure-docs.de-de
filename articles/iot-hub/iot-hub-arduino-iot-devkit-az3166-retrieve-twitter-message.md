---
title: Abrufen einer Twitter-Meldung mit Azure Functions | Microsoft-Dokumentation
description: Verwenden Sie den Bewegungssensor zum Erkennen von Erschütterungen, und suchen Sie mit Azure Functions einen zufälligen Tweet zu einem festgelegten Hashtag.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/07/2018
ms.author: liydu
ms.openlocfilehash: dc4ff35ff04680e8635d54c25212c8ae639ae472
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60779834"
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Nutzung des Bewegungssensors zum Abrufen einer Twitter-Nachricht mit Azure Functions

In diesem Projekt erfahren Sie, wie Sie den Bewegungssensor verwenden, um mithilfe von Azure Functions ein Ereignis auszulösen. Die App ruft einen zufälligen Tweet mit einem Hashtag (#) ab, das Sie in einem Arduino-Sketch konfigurieren. Der Tweet ist auf der DevKit-Anzeige zu sehen.

## <a name="what-you-need"></a>Voraussetzungen

Befolgen Sie die Anweisungen im [Leitfaden zu den ersten Schritten](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started), um folgende Voraussetzungen zu erfüllen:

* Stellen Sie eine Wi-Fi-Verbindung mit dem DevKit her.
* Bereiten Sie die Entwicklungsumgebung vor.

Ein aktives Azure-Abonnement. Wenn Sie kein Abonnement haben, können Sie sich mit einer der beiden folgenden Methoden registrieren:

* [Kostenloses Microsoft Azure-Testkonto für 30 Tage](https://azure.microsoft.com/free/) aktivieren
* [Azure-Guthaben](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) in Anspruch nehmen, wenn Sie MSDN- oder Visual Studio-Abonnent sind

## <a name="open-the-project-folder"></a>Öffnen des Projektordners

Öffnen Sie zunächst den Projektordner. 

### <a name="start-vs-code"></a>Starten von Visual Studio Code

* Stellen Sie sicher, dass DevKit mit Ihrem Computer verbunden ist.

* Starten Sie Visual Studio Code.

* Verbinden Sie das DevKit mit Ihrem Computer.

   > [!NOTE]
   > Beim Start von VS Code wird möglicherweise eine Fehlermeldung angezeigt, dass die Arduino-IDE oder das zugehörige Boardpaket nicht gefunden werden kann. Wenn dieser Fehler auftritt, schließen Sie VS Code und starten Sie die Arduino-IDE erneut. VS Code sollte nun den Arduino-IDE-Pfad ordnungsgemäß erkennen.

### <a name="open-the-arduino-examples-folder"></a>Öffnen des Ordners „Arduino-Beispiele“

Erweitern Sie auf der linken Seite den Abschnitt **ARDUINO-BEISPIELE**, wechseln Sie zu **Beispiele für MXCHIP AZ3166 > AzureIoT**, und wählen Sie **ShakeShake** aus. Daraufhin wird ein neues VS Code-Fenster mit dem Projektordner geöffnet. Wenn Sie den Abschnitt „MXCHIP AZ3166“ nicht anzeigen können, stellen Sie sicher, dass Ihr Gerät ordnungsgemäß verbunden ist, und starten Sie Visual Studio Code neu.  
![mini-solution-examples](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

Sie können das Beispielprojekt auch über die Befehlspalette öffnen. Klicken Sie auf `Ctrl+Shift+P`(macOS: `Cmd+Shift+P`), um die Befehlspalette zu öffnen, geben Sie **Arduino** ein, suchen Sie dann nach **Arduino: Beispiele**, und wählen Sie den Eintrag aus.

## <a name="provision-azure-services"></a>Bereitstellen von Azure-Diensten

Führen Sie im Projektmappenfenster Ihre Aufgabe über `Ctrl+P` (macOS: `Cmd+P`) durch Eingabe von `task cloud-provision` aus.

Im Visual Studio Code-Terminal werden Sie über eine interaktive Befehlszeile durch die Bereitstellung der erforderlichen Azure-Dienste geführt:

![Cloudbereitstellung](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Wenn die Seite beim Anmeldeversuch in Azure im Ladestatus hängt, lesen Sie den [Abschnitt zum Anmeldefehler in Azure in den FAQs zum IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure).
 
## <a name="modify-the-hashtag"></a>Ändern des Hashtags

Öffnen Sie `ShakeShake.ino`, und suchen Sie nach dieser Codezeile:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Ersetzen Sie die Zeichenfolge `iot` in den geschweiften Klammern durch Ihr bevorzugtes Hashtag. Das DevKit ruft später einen zufälligen Tweet ab, der das in diesem Schritt angegebene Hashtag enthält.

## <a name="deploy-azure-functions"></a>Deploy Azure Functions (Bereitstellen von Azure Functions)

Drücken Sie `Ctrl+P` (macOS: `Cmd+P`), um `task cloud-deploy` auszuführen und mit der Bereitstellung des Azure Functions-Codes zu beginnen:

![Cloudbereitstellung](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> In einigen Fällen funktioniert Azure Functions nicht ordnungsgemäß. Um dieses Problem zu beheben, lesen Sie den [Abschnitt zum Kompilierungsfehler in den FAQs des IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Erstellen und Hochladen des Gerätecodes

Erstellen Sie als Nächstes den Gerätecode, und laden Sie ihn hoch.

### <a name="windows"></a>Windows

1. Verwenden Sie `Ctrl+P` zum Ausführen von `task device-upload`.

2. Das Terminal fordert Sie zum Wechseln in den Konfigurationsmodus auf. Gehen Sie dazu wie folgt vor:

   * Halten Sie die Taste A gedrückt.

   * Drücken Sie gleichzeitig einmal die Reset-Taste.

3. Auf dem Bildschirm werden die DevKit-ID und „Konfiguration“ angezeigt.

### <a name="macos"></a>macOS

1. Versetzen Sie das DevKit in den Konfigurationsmodus:

   Halten Sie hierfür Taste „A“ gedrückt, und drücken Sie anschließend gleichzeitig einmal die Reset-Taste. Auf dem Bildschirm wird „Konfiguration“ angezeigt.

2. Verwenden Sie `Cmd+P` zum Ausführen von `task device-upload`, um die Verbindungszeichenfolge festzulegen, die im `task cloud-provision`-Schritt abgerufen wird.

### <a name="verify-upload-and-run"></a>Überprüfen, Hochladen und Ausführen

Nachdem die Verbindungszeichenfolge festgelegt wurde, überprüft es die App, lädt sie hoch und führt sie aus. 

1. VS Code beginnt mit dem Überprüfen und Hochladen des Arduino-Sketches in Ihr DevKit:

   ![device-upload](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)

2. Das DevKit startet neu und beginnt mit der Ausführung des Codes.

Möglicherweise wird die Fehlermeldung „Fehler: AZ3166: Unbekanntes Paket“ angezeigt. Dieser Fehler tritt auf, wenn der Boardpaketindex nicht ordnungsgemäß aktualisiert wurde. Um dieses Problem zu beheben, lesen Sie den [Abschnitt zum Fehler bezüglich eines unbekanntes Pakets in den FAQs zum IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testen des Projekts

Drücken Sie nach der App-Initialisierung einmalig die Taste A, und schütteln Sie dann vorsichtig das DevKit-Board. Durch diese Aktion wird ein zufälliger Tweet abgerufen, der das zuvor von Ihnen angegebene Hashtag enthält. Innerhalb weniger Sekunden ist ein Tweet auf der DevKit-Anzeige zu sehen:

### <a name="arduino-application-initializing"></a>Arduino-Anwendung wird initialisiert...

![Arduino-Anwendung wird initialisiert.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Drücken der Taste A, um zu schütteln...

![Drücken der Taste A, um zu schütteln.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Bereit zum Schütteln...

![Bereit zum Schütteln](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Wird verarbeitet...

![Verarbeitung](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Drücken der Taste B, um zu lesen...

![Drücken der Taste B, um zu lesen.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Anzeige eines zufälligen Tweets...

![Anzeige eines zufälligen Tweets](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Drücken Sie erneut A, und schütteln Sie das Board vorsichtig für einen neuen Tweet.
- Drücken Sie B, um durch den restlichen Tweet zu scrollen.

## <a name="how-it-works"></a>So funktioniert's

![Diagramm](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

Der Arduino-Sketch sendet ein Ereignis an das Azure IoT Hub. Dieses Ereignis löst die Azure Functions-App aus. Die Azure Functions-App enthält die Logik zum Verbinden mit der Twitter-API und Abrufen eines Tweets. Der Text wird dann in einer C2D-Nachricht (Cloud-zu-Gerät) umschlossen und zurück an das Gerät gesendet.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Optional: Verwenden eines eigenen Twitter-Bearertokens

Zu Testzwecken wird in diesem Projekt ein vorkonfiguriertes Twitter-Bearertoken verwendet. Es gibt jedoch ein [Ratenlimit](https://dev.twitter.com/rest/reference/get/search/tweets) für jedes Twitter-Konto. Wenn Sie die Verwendung eines eigenen Tokens in Betracht ziehen, führen Sie die folgenden Schritte aus:

1. Öffnen Sie das [Twitter-Entwicklerportal](https://dev.twitter.com/), um eine neue Twitter-App zu registrieren.

2. [Holen Sie sich Verbraucherschlüssel und -geheimnis](https://support.yapsody.com/hc/en-us/articles/360003291573-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) für Ihre App.

3. Verwenden Sie ein [Dienstprogramm](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/), um aus diesen beiden Schlüsseln ein Twitter-Bearertoken zu generieren.

4. Wechseln Sie im [Azure-Portal](https://portal.azure.com/) {:target="_blank"} in die **Ressourcengruppe**, und suchen Sie die Azure-Funktion (Typ: App-Dienst) für Ihr Bewegungssensorprojekt. Der Name enthält immer die Zeichenfolge „shake...“.

   ![azure-function](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Aktualisieren Sie den Code für `run.csx` unter **Funktionen > shakeshake-cs** mit Ihrem eigenen Token:

   ```csharp
   string authHeader = "Bearer " + "[your own token]";
   ```
  
   ![Twitter-Token](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Speichern Sie die Datei, und klicken Sie auf **Ausführen**.

## <a name="problems-and-feedback"></a>Probleme und Feedback

Gewusst wie: Beheben von Problemen oder Mitteilen von Feedback 

### <a name="problems"></a>Probleme

Ein Problem, das auftreten könnte, ist die Anzeige von „Keine Tweets“ auf dem Bildschirm, obwohl jeder Schritt erfolgreich verlaufen ist. Dies tritt normalerweise zum ersten Mal auf, wenn Sie das Beispiel bereitstellen und ausführen, da die Funktions-App ein paar Sekunden bis eine Minute für den Kaltstart der App benötigt. 

Oder, beim Ausführen des Codes treten einige Abweichungen auf, die einen Neustart der App verursachen. Wenn diese Bedingung eintritt, kann die App des Geräts ein Timeout für das Abrufen des Tweets erhalten. In diesem Fall können Sie eine oder beide dieser Methoden ausprobieren, um das Problem zu lösen:

1. Klicken Sie im DevKit auf die Schaltfläche zum Zurücksetzen, um die Geräte-App erneut auszuführen.

2. Suchen Sie im [Azure-Portal](https://portal.azure.com/) nach der Azure Functions-App, die Sie erstellt haben, und starten Sie diese neu:

   ![azure-function-restart](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

### <a name="feedback"></a>Feedback

Wenn andere Probleme auftreten, helfen Ihnen die Informationen in den [häufig gestellten Fragen zum IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) weiter, oder wenden Sie sich über folgende Kanäle an uns:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie Sie die Verbindung eines DevKit-Geräts mit dem Solution Accelerator für die Azure IoT-Remoteüberwachung herstellen und einen Tweet abrufen, werden die folgenden nächsten Schritte empfohlen:

* [Solution Accelerator für die Azure IoT-Remoteüberwachung: Übersicht](https://docs.microsoft.com/azure/iot-suite/)