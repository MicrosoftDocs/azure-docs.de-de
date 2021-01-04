---
title: 'Tutorial: Beispiel für visuelle Warnungen in IoT'
titleSuffix: Azure Cognitive Services
description: In diesem Tutorial verwenden Sie Custom Vision mit einem IoT-Gerät, um visuelle Zustände aus dem Videofeed einer Kamera zu erkennen und zu melden.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: c6405e2fcddef9ae3228ede76dfa57f7542164c8
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96020176"
---
# <a name="tutorial-use-custom-vision-with-an-iot-device-to-report-visual-states"></a>Tutorial: Verwenden von Custom Vision mit einem IoT-Gerät zum Melden von visuellen Zuständen

Diese Beispiel-App veranschaulicht, wie Sie Custom Vision verwenden, um ein Gerät mit einer Kamera für das Erkennen von visuellen Zuständen trainieren. Sie können dieses Erkennungsszenario auf einem IoT-Gerät ausführen, indem Sie ein exportiertes ONNX-Modell verwenden.

Ein visueller Zustand beschreibt den Inhalt eines Bilds: ein leerer Raum oder ein Raum mit Personen, eine leere Straße oder eine Straße mit einem LKW usw. Im Bild unten wird gezeigt, wie die App erkennt, dass eine Banane oder ein Apfel vor die Kamera gelegt wird.

![Animation einer Benutzeroberfläche, die Obst vor der Kamera beschriftet](./media/iot-visual-alerts-tutorial/scoring.gif)

In diesem Lernprogramm lernen Sie Folgendes:
> [!div class="checklist"]
> * Konfigurieren der Beispiel-App zur Verwendung Ihrer eigenen Custom Vision- und IoT Hub-Ressourcen
> * Verwenden der App zum Trainieren Ihres Custom Vision-Projekts
> * Verwenden der App zum Bewerten neuer Bilder in Echtzeit und Senden der Ergebnisse an Azure

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services) erstellen, bevor Sie beginnen. 

## <a name="prerequisites"></a>Voraussetzungen

* [!INCLUDE [create-resources](includes/create-resources.md)]
    > [!IMPORTANT]
    > Bei diesem Projekt muss eine **kompakte** Bildklassifizierung verwendet werden, weil das Modell später in ONNX exportiert werden soll.
* Sie müssen auch [eine IoT Hub-Ressource in Azure erstellen](https://ms.portal.azure.com/#create/Microsoft.IotHub).
* [Visual Studio 2015 (oder höher)](https://www.visualstudio.com/downloads/)
* Optional ein IoT-Gerät unter Windows 10 IoT Core, Version 17763 oder höher. Sie können die App auch direkt auf Ihrem PC ausführen.
   * Bei Raspberry Pi 2 und 3 können Sie Windows 10 direkt über die IoT Dashboard-App einrichten. Bei anderen Geräten wie z. B. DragonBoard müssen Sie mithilfe der [eMMC-Methode](/windows/iot-core/tutorials/quickstarter/devicesetup#flashing-with-emmc-for-dragonboard-410c-other-qualcomm-devices) einen Flash ausführen. Wenn Sie Hilfe beim Einrichten eines neuen Geräts benötigen, finden Sie weitere Informationen in der Windows-IoT-Dokumentation unter [Einrichten deines Geräts](/windows/iot-core/tutorials/quickstarter/devicesetup).

## <a name="about-the-visual-alerts-app"></a>Informationen zur App für visuelle Warnungen

Die App für visuelle Warnungen in IoT wird in einer kontinuierlichen Schleife ausgeführt und wechselt je nach Bedarf zwischen vier verschiedenen Zuständen:

* **Kein Modell**: Ein nicht operativer Zustand. Die App wird ständig für eine Sekunde in den Ruhezustand versetzt und überprüft die Kamera.
* **Erfassen von Trainingsbildern**: In diesem Zustand erfasst die App ein Bild und lädt es als Trainingsbild in das Custom Vision-Zielprojekt hoch. Dann wird die App für 500 ms in den Ruhezustand versetzt und wiederholt den Vorgang, bis die festgelegte Zielanzahl von Bildern erfasst ist. Dann löst die App das Training des Custom Vision-Modells aus.
* **Warten auf trainiertes Modell**: In diesem Zustand ruft die App die Custom Vision-API jede Sekunde auf, um zu überprüfen, ob das Zielprojekt eine trainierte Iteration enthält. Wenn eine solche gefunden wird, lädt die App das entsprechende ONNX-Modell in eine lokale Datei herunter und wechselt in den Zustand **Bewertung**.
* **Bewertung**: In diesem Zustand verwendet die App Windows ML, um einen einzelnen Frame aus der Kamera im Vergleich zum lokalen ONNX-Modell auszuwerten. Die resultierende Bildklassifizierung wird auf dem Bildschirm angezeigt und als Meldung an den IoT Hub gesendet. Dann wird die App für eine Sekunde in den Ruhezustand versetzt, bevor die Bewertung eines neuen Bilds ausgeführt wird.

## <a name="examine-the-code-structure"></a>Untersuchen der Codestruktur

Die folgenden Dateien verarbeiten die Hauptfunktionalität der App.

| Datei | BESCHREIBUNG |
|-------------|-------------|
| [MainPage.xaml](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml) | Diese Datei definiert die XAML-Benutzeroberfläche. Sie hostet das Webkamera-Steuerelement und enthält die Beschriftungen, die für Statusupdates verwendet werden.|
| [MainPage.xaml.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml.cs) | Dieser Code steuert das Verhalten der XAML-Benutzeroberfläche. Er enthält den Verarbeitungscode für den Zustandsautomaten.|
| [CustomVision\CustomVisionServiceWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionServiceWrapper.cs) | Dies ist eine Wrapper-Klasse, die die Integration in den Custom Vision-Dienst verarbeitet.|
| [CustomVision\CustomVisionONNXModel.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionONNXModel.cs) | Dies ist eine Wrapper-Klasse, die die Integration in Windows ML zum Laden des ONNX-Modells und Bewerten von Bildern im Vergleich zu diesem Modell verarbeitet.|
| [IoTHub\IotHubWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/IoTHub/IoTHubWrapper.cs) | Dies ist eine Wrapper-Klasse, die die Integration in IoT Hub zum Hochladen der Bewertungsergebnisse in Azure verarbeitet.|

## <a name="set-up-the-visual-alerts-app"></a>Einrichten der App für visuelle Warnungen

Führen Sie die folgenden Schritte aus, um die App für visuelle Warnungen in IoT auf Ihrem PC oder IoT-Gerät einzurichten.

1. Klonen Sie das [IoTVisualAlerts-Beispiel](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts) auf GitHub, oder laden Sie es herunter.
1. Öffnen Sie die Projektmappe _IoTVisualAlerts.sln_ in Visual Studio.
1. Integrieren Sie Ihr Custom Vision-Projekt:
    1. Aktualisieren Sie im Skript _CustomVision\CustomVisionServiceWrapper.cs_ die `ApiKey`-Variable mit Ihrem Trainingsschlüssel.
    1. Aktualisieren Sie dann die `Endpoint`-Variable mit der Endpunkt-URL, die mit dem Schlüssel verknüpft ist.
    1. Aktualisieren Sie die `targetCVSProjectGuid`-Variable mit der entsprechenden ID des Custom Vision-Projekts, das Sie verwenden möchten. 
1. Richten Sie die IoT Hub-Ressource ein:
    1. Aktualisieren Sie im Skript _IoTHub\IotHubWrapper.cs_ die `s_connectionString`-Variable mit der richtigen Verbindungszeichenfolge für Ihr Gerät. 
    1. Laden Sie Ihre IoT Hub-Instanz im Azure-Portal, klicken Sie unter **Explorer** auf **IoT-Geräte**, wählen Sie Ihr Zielgerät aus (oder erstellen Sie bei Bedarf ein Gerät), und suchen Sie unter **Primäre Verbindungszeichenfolge** nach der Verbindungszeichenfolge. Die Zeichenfolge enthält den Namen Ihrer IoT Hub-Instanz, die Geräte-ID und den freigegebenen Zugriffsschlüssel und weist folgendes Format auf: `{your iot hub name}.azure-devices.net;DeviceId={your device id};SharedAccessKey={your access key}`.

## <a name="run-the-app"></a>Ausführen der App

Wenn Sie die App auf Ihrem PC ausführen, wählen Sie in Visual Studio **Lokaler Computer** als Zielgerät und **x64** oder **x86** als Zielplattform aus. Drücken Sie die Taste F5, um das Programm auszuführen. Die App sollte starten und den Livefeed aus der Kamera sowie eine Statusmeldung anzeigen.

Wenn die Bereitstellung auf einem IoT-Gerät mit ARM-Prozessor erfolgt, müssen Sie **ARM** als Zielplattform und **Remotecomputer** als Zielgerät auswählen. Geben Sie die IP-Adresse Ihres Geräts ein, wenn Sie dazu aufgefordert werden (das Gerät muss sich im gleichen Netzwerk befinden wie Ihr PC). Sie können die IP-Adresse aus der Windows-IoT-Standard-App abrufen, nachdem Sie das Gerät gestartet und mit dem Netzwerk verbunden haben. Drücken Sie die Taste F5, um das Programm auszuführen.

Wenn Sie die App zum ersten Mal ausführen, sind keine visuellen Zustände verfügbar. Die App zeigt eine Statusmeldung an, dass kein Modell zur Verfügung steht. 

## <a name="capture-training-images"></a>Erfassen von Trainingsbildern

Um ein Modell einzurichten, müssen Sie die App in den Zustand **Erfassen von Trainingsbildern** versetzen. Führen Sie einen der folgenden Schritte aus:
* Wenn Sie die App auf einem PC ausführen, verwenden Sie die Schaltfläche in der oberen rechten Ecke der Benutzeroberfläche.
* Wenn Sie die App auf einem IoT-Gerät ausführen, rufen Sie über den IoT Hub die Methode `EnterLearningMode` auf dem Gerät auf. Sie können die Methode über den Geräteeintrag im IoT Hub-Menü im Azure-Portal oder mit einem Tool wie [IoT Hub-Geräte-Explorer](https://github.com/Azure/azure-iot-sdk-csharp) aufrufen.
 
Wenn die App in den Zustand **Erfassen von Trainingsbildern** versetzt wird, erfasst sie zwei Bilder pro Sekunde, bis die Zielanzahl von Bildern erreicht ist. Das Ziel beträgt standardmäßig 30 Bilder, aber Sie können diesen Parameter festlegen, indem Sie die gewünschte Zahl als Argument an die IoT Hub-Methode `EnterLearningMode` übergeben. 

Während die App Bilder erfasst, müssen Sie die Kamera auf die Arten von visuellen Zuständen richten, die Sie erkennen möchten (z. B. einen leeren Raum, einen Raum mit Personen, einen leeren Schreibtisch, einen Schreibtisch mit einem Spielzeuglaster usw.).

## <a name="train-the-custom-vision-model"></a>Trainieren des Custom Vision-Modells

Sobald die App die Erfassung von Bildern beendet hat, lädt sie diese hoch und wechselt dann in den Zustand **Warten auf trainiertes Modell**. An diesem Punkt müssen Sie die [Custom Vision-Website](https://www.customvision.ai/) aufrufen und basierend auf den neuen Trainingsbildern ein Modell erstellen. Die folgende Animation zeigt ein Beispiel dieses Prozesses.

![Animation: Tagging mehrerer Bilder von Bananen](./media/iot-visual-alerts-tutorial/labeling.gif)

So wiederholen Sie diesen Prozess in Ihrem eigenen Szenario:

1. Melden Sie sich bei der [Custom Vision-Website](http://customvision.ai) an.
1. Suchen Sie Ihr Zielprojekt, in dem jetzt alle Trainingsbilder enthalten sein sollten, die von der App hochgeladen wurden.
1. Wählen Sie für jeden visuellen Zustand, den Sie identifizieren möchten, die geeigneten Bilder aus, und fügen Sie manuell ein Tag hinzu.
    * Wenn Ihr Ziel beispielsweise darin besteht, zwischen einem leeren Raum und einem Raum mit Personen darin zu unterscheiden, empfiehlt es sich, mindestens fünf Bilder mit Personen als neue Klasse **Personen** zu markieren und mindestens fünf Bilder ohne Personen mit dem Tag **Negativ** zu versehen. Dadurch kann das Modell zwischen den beiden Zuständen unterscheiden.
    * Ein anderes Beispiel: Sie möchten ungefähr abschätzen, wie voll ein Regal ist. Dann verwenden Sie Tags wie **LeeresRegal**, **HalbvollesRegal** und **VollesRegal**.
1. Wenn Sie fertig sind, klicken Sie auf die Schaltfläche **Trainieren**.
1. Sobald das Training abgeschlossen ist, erkennt die App, dass eine trainierte Iteration verfügbar ist. Sie startet den Prozess, mit dem das trainierte Modell in ONNX exportiert und auf das Gerät heruntergeladen wird.

## <a name="use-the-trained-model"></a>Verwenden des trainierten Modells

Nachdem die App das trainierte Modell heruntergeladen hat, wechselt sie in den Zustand **Bewertung** und beginnt damit, Bilder der Kamera in einer kontinuierlichen Schleife zu bewerten.

Für jedes erfasste Bild zeigt die App das wichtigste Tag auf dem Bildschirm an. Wenn der visuelle Zustand nicht zu erkennen ist, zeigt die App **Keine Übereinstimmungen** an. Die App sendet diese Meldungen auch an den IoT Hub, und wenn eine Klasse erkannt wurde, enthält die Meldung die Beschriftung, den Konfidenzwert und eine Eigenschaft namens `detectedClassAlert`, die von IoT Hub-Clients verwendet werden kann, Meldungen basierend auf Eigenschaften schnell weiterzuleiten.

Darüber hinaus verwendet das Beispiel eine [Sense HAT Library](https://github.com/emmellsoft/RPi.SenseHat), um zu erkennen, ob die Ausführung auf einem Raspberry Pi mit einer Sense HAT-Einheit erfolgt. In diesem Fall kann die Einheit als Ausgabeanzeige verwendet werden, indem alle Anzeigeleuchten auf Rot geschaltet werden, wenn eine Klasse erkannt wird. Wenn nichts erkannt wurde, bleiben die Leuchten deaktiviert.

## <a name="reuse-the-app"></a>Wiederverwenden der App

Wenn Sie die App wieder in ihren ursprünglichen Zustand versetzen möchten, können Sie auf die Schaltfläche in der oberen rechten Ecke der Benutzeroberfläche klicken, oder die Methode `DeleteCurrentModel` über den IoT Hub aufrufen.

Sie können den Schritt zum Hochladen von Trainingsbildern jederzeit wiederholen, indem Sie oben rechts in der Benutzeroberfläche auf die Schaltfläche klicken oder die Methode `EnterLearningMode` erneut aufrufen.

Wenn Sie die App auf einem Gerät ausführen und die IP-Adresse erneut abrufen müssen (um beispielsweise eine Remoteverbindung über den [Windows-IoT-Remoteclient](https://www.microsoft.com/p/windows-iot-remote-client/9nblggh5mnxz#activetab=pivot:overviewtab) herzustellen), können Sie die `GetIpAddress`-Methode über den IoT Hub aufrufen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie das Custom Vision-Projekt, wenn Sie es nicht beibehalten möchten. Navigieren Sie auf der [Custom Vision-Website](https://customvision.ai) zu **Projekte**, und klicken Sie auf das Papierkorbsymbol unter Ihrem neuen Projekt.

![Screenshot eines Panels mit der Bezeichnung „My New Project“ (Mein neues Projekt) mit einem Papierkorbsymbol](./media/csharp-tutorial/delete_project.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Anwendung eingerichtet und ausgeführt, die Informationen zu visuellen Zuständen auf einem IoT-Gerät erkennt und die Ergebnisse an den IoT Hub sendet. Als Nächstes können Sie den Quellcode erkunden oder eine der unten vorgeschlagenen Änderungen vornehmen.

> [!div class="nextstepaction"]
> [IoTVisualAlerts-Beispiel (GitHub)](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts)

* Fügen Sie eine IoT Hub-Methode hinzu, um die App direkt in den Zustand **Warten auf trainiertes Modell** zu schalten. Auf diese Weise können Sie das Modell mit Bildern trainieren, die nicht vom Gerät selbst erfasst werden, und das neue Modell dann auf Befehl per Push auf das Gerät übertragen.
* Arbeiten Sie das Tutorial [Visualisieren von Sensordaten in Echtzeit](../../iot-hub/iot-hub-live-data-visualization-in-power-bi.md) durch, um ein Power BI-Dashboard zu erstellen, mit dem Sie die vom Beispiel gesendeten IoT Hub-Warnungen visualisieren können.
* Arbeiten Sie das Tutorial [IoT-Remoteüberwachung](../../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md) durch, um eine Logik-App zu erstellen, die auf IoT Hub-Warnungen reagiert, wenn visuelle Zustände erkannt werden.