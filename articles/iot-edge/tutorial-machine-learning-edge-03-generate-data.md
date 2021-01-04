---
title: 'Tutorial: Generieren simulierter Gerätedaten: Machine Learning in Azure IoT Edge'
description: 'Tutorial: Erstellen virtueller Geräte, die simulierte Telemetriedaten generieren, mit denen dann ein Machine Learning-Modell trainiert werden kann'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/20/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: eef5e60b06eedb1fb07c57aa2e369dd3830fcad5
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932403"
---
# <a name="tutorial-generate-simulated-device-data"></a>Tutorial: Generieren simulierter Gerätedaten

In diesem Artikel verwenden wir Machine Learning-Trainingsdaten, um ein Gerät zu simulieren, das Telemetriedaten an Azure IoT Hub sendet. Wie eingangs erwähnt, wird in diesem Tutorial das [Turbofan Engine Degradation Simulation-Dataset](https://c3.nasa.gov/dashlink/resources/139/) genutzt, um Flugzeugtriebwerk-Daten für das Trainieren und Testen zu simulieren.

Wir verfügen für unser experimentelles Szenario über das folgende Wissen:

* Die Daten bestehen aus mehreren multivariaten Zeitreihen.
* Jedes Dataset ist in Unterbereiche für das Training und für Tests unterteilt.
* Jede Zeitreihe stammt von einem anderen Triebwerk.
* Bei jedem Triebwerk wird mit unterschiedlich großem Anfangsverschleiß und unterschiedlicher Fertigungsabweichung begonnen.

In diesem Tutorial verwenden wir nur die Trainingsdaten eines Datasets (FD003).

In einer realen Umgebung stellt jedes Triebwerk ein unabhängiges IoT-Gerät dar. Da wir davon ausgehen, dass Sie nicht über eine Sammlung mit Turbofan-Triebwerken mit Internetverbindung verfügen, erstellen wir einen Softwareersatz für diese Geräte.

Der Simulator ist ein C#-Programm, bei dem die IoT Hub-APIs zum programmgesteuerten Registrieren von virtuellen Geräten bei IoT Hub verwendet werden. Anschließend lesen wir die Daten für jedes Gerät aus der Datenteilmenge aus, die von der NASA bereitgestellt wurde, und senden sie über ein simuliertes IoT-Gerät an Ihren IoT-Hub. Den gesamten Code für diesen Teil des Tutorials finden Sie im Verzeichnis „DeviceHarness“ des Repositorys.

Das DeviceHarness-Projekt ist ein .NET Core-Projekt in C#, das aus vier Klassen besteht:

* **Program:** Der Einstiegspunkt für die Ausführung, der für die Verarbeitung der Benutzereingabe und die allgemeine Koordination zuständig ist.
* **TrainingFileManager:** Zuständig für das Lesen und Analysieren der ausgewählten Datendatei.
* **CycleData:** Steht für eine einzelne Zeile mit Daten in einer Datei, die in das Nachrichtenformat konvertiert wurde.
* **TurbofanDevice:** Zuständig für die Erstellung eines IoT-Geräts, das einem Gerät (Zeitreihe) in den Daten entspricht und die Daten an IoT Hub überträgt.

Die Abarbeitung der in diesem Artikel beschriebenen Aufgaben sollte ungefähr 20 Minuten dauern.

Die realen Abläufe dieses Schritts werden meist von Geräte- und Cloudentwicklern durchgeführt.

In diesem Abschnitt des Tutorials lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Integrieren eines externes Projekt in Ihre Entwicklungsumgebung
> * Generieren simulierter IoT-Gerätedaten mithilfe des Beispielprojekts „DeviceHarness“
> * Anzeigen generierter Daten in IoT Hub

## <a name="prerequisites"></a>Voraussetzungen

Dieser Artikel ist Teil einer Tutorialreihe zur Verwendung von Azure Machine Learning für IoT Edge. Jeder Artikel in der Reihe baut auf der Arbeit im vorherigen Artikel auf. Wenn Sie diesen Artikel direkt aufgerufen haben, wechseln Sie zum [ersten Artikel](tutorial-machine-learning-edge-01-intro.md) in der Reihe.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Konfigurieren von Visual Studio Code und Erstellen des DeviceHarness-Projekts

1. Öffnen Sie eine Remotedesktopsitzung auf Ihrem virtuellen Entwicklungscomputer.

1. Öffnen Sie in Visual Studio Code den Ordner `C:\source\IoTEdgeAndMlSample\DeviceHarness`.

1. Da Sie auf diesem Computer zum ersten Mal Erweiterungen nutzen, werden einige Erweiterungen aktualisiert und die zugehörigen Abhängigkeiten installiert. Unter Umständen werden Sie aufgefordert, die Erweiterung zu aktualisieren. Wählen Sie in diesem Fall die Option **Fenster erneut laden**.

   Wenn im Ausgabefenster OmniSharp-Fehler angezeigt werden, müssen Sie die C#-Erweiterung deinstallieren.

1. Sie werden aufgefordert, die erforderliche Ressourcen für „DeviceHarness“ hinzuzufügen. Wählen Sie **Ja**, um sie hinzuzufügen.

   * Es kann einige Sekunden dauern, bis die Benachrichtigung angezeigt wird.
   * Falls Sie diese Benachrichtigung verpasst haben, können Sie über das Glockensymbol in der unteren rechten Ecke darauf zugreifen.

   ![VS Code-Popup für Erweiterung](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Wählen Sie **Wiederherstellen**, um die Paketabhängigkeiten wiederherzustellen.

   ![VS Code-Eingabeaufforderung für Wiederherstellung](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

   Wenn Sie diese Benachrichtigungen nicht erhalten, sollten Sie Visual Studio Code schließen, die Verzeichnisse „bin“ und „obj“ unter `C:\source\IoTEdgeAndMlSample\DeviceHarness` löschen, Visual Studio Code öffnen und dann wieder den Ordner „DeviceHarness“ öffnen.

1. Überprüfen Sie, ob Ihre Umgebung richtig eingerichtet ist, indem Sie einen Buildvorgang auslösen. Verwenden Sie hierfür entweder **STRG** + **UMSCHALT** + **B** oder die Option **Terminal** > **Buildtask ausführen**.

1. Sie werden aufgefordert, den auszuführenden Buildtask auszuwählen. Wählen Sie **Build** aus.

1. Der Buildvorgang wird durchgeführt, und es wird eine Erfolgsmeldung ausgegeben.

   ![Ausgabenachricht für erfolgreichen Buildvorgang](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Sie können diesen Buildvorgang mit der Standard-Buildtask durchführen, indem Sie **Terminal** > **Configure Default Build Task...** (Standard-Buildtask konfigurieren...) und dann in der Eingabeaufforderung die Option **Build** wählen.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Herstellen einer Verbindung mit dem IoT-Hub und Ausführen von „DeviceHarness“

Da das Projekt jetzt erstellt wird, können Sie eine Verbindung mit Ihrem IoT-Hub herstellen, um auf die Verbindungszeichenfolge zuzugreifen und den Fortschritt bei der Datengenerierung zu überwachen.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Anmelden bei Azure in Visual Studio Code

1. Melden Sie sich an Ihrem Azure-Abonnement in Visual Studio Code an, indem Sie die Befehlspalette öffnen. Verwenden Sie hierfür `Ctrl + Shift + P` oder **Ansicht** > **Befehlspalette**.

1. Suchen Sie nach dem Befehl **Azure: Anmelden**.

   Ein Browserfenster wird geöffnet, und Sie werden zum Eingeben Ihrer Anmeldeinformationen aufgefordert. Sie können den Browser schließen, nachdem Sie auf eine Seite mit einer Erfolgsmeldung geleitet wurden.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Herstellen einer Verbindung mit Ihrem IoT-Hub und Abrufen der Hub-Verbindungszeichenfolge

1. Wählen Sie im unteren Abschnitt des Visual Studio Code-Explorers den Bereich **Azure IoT Hub** aus, um ihn zu erweitern.

1. Klicken Sie im erweiterten Bereich auf **IoT-Hub auswählen**.

1. Wählen Sie bei entsprechender Aufforderung Ihr Azure-Abonnement und dann Ihren IoT-Hub aus.

1. Klicken Sie rechts von **Azure IoT Hub** auf **...** , um weitere Aktionen anzuzeigen. Wählen Sie die Option **Copy IoT Hub connection string** (IoT Hub-Verbindungszeichenfolge kopieren).

   ![Kopieren der IoT Hub-Verbindungszeichenfolge](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Ausführen des Projekts „DeviceHarness“

1. Wählen Sie **Ansicht** > **Terminal**, um das Visual Studio Code-Terminal zu öffnen.

   Drücken Sie die EINGABETASTE, wenn keine Eingabeaufforderung angezeigt wird.

1. Geben Sie im Terminal `dotnet run` ein.

1. Fügen Sie die Verbindungszeichenfolge ein, die Sie im vorherigen Schritt kopiert haben, wenn Sie zum Eingeben der IoT Hub-Verbindungszeichenfolge aufgefordert werden.

1. Klicken Sie im Bereich **Azure IoT Hub-Geräte** auf die Schaltfläche „Aktualisieren“.

   ![Aktualisieren der IoT Hub-Geräteliste](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Beachten Sie, dass Geräte dem IoT-Hub hinzugefügt und die Geräte grün angezeigt werden, um anzugeben, dass über das jeweilige Gerät Daten gesendet werden. Nachdem Geräte Nachrichten an den IoT-Hub gesendet haben, wird die Verbindung getrennt, und sie werden blau angezeigt.

1. Sie können an den Hub gesendete Nachrichten anzeigen, indem Sie mit der rechten Maustaste auf ein Gerät klicken und **Überwachung des integrierten Ereignisendpunkts starten** auswählen. Die Nachrichten werden in Visual Studio Code im Ausgabebereich angezeigt.

1. Beenden Sie die Überwachung, indem Sie auf den Ausgabebereich **Azure IoT Hub** klicken und die Option **Überwachung des integrierten Ereignisendpunkts beenden** auswählen.

1. Warten Sie, bis die Ausführung der Anwendung abgeschlossen ist. Dies dauert einige Minuten.

## <a name="check-iot-hub-for-activity"></a>Überprüfen des IoT-Hubs auf Aktivität

Die von DeviceHarness gesendeten Daten wurden an Ihren IoT-Hub übertragen. Dies können Sie im Azure-Portal überprüfen.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/), und navigieren Sie zum IoT-Hub, der für dieses Tutorial erstellt wurde.

1. Wählen Sie im Menü im linken Bereich unter **Überwachung** die Option **Metriken** aus.

1. Klicken Sie auf der Seite mit der Diagrammdefinition auf die Dropdownliste **Metrik**, scrollen Sie in der Liste nach unten, und wählen Sie **Routing: An den Speicher übermittelte Daten** aus. Im Diagramm sollte für den Zeitpunkt, zu dem die Daten an den Speicher weitergeleitet wurden, ein Spitzenwert angezeigt werden.

   ![Spitzenwert in Diagramm für Weiterleitung der Daten an den Speicher](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Überprüfen von Daten in Azure Storage

Die Daten, die wir gerade an Ihren IoT-Hub gesendet haben, wurden an den Speichercontainer geleitet, den wir im vorherigen Artikel erstellt haben. Wir sehen uns nun die Daten in unserem Speicherkonto an.

1. Navigieren Sie im Azure-Portal zu Ihrem Speicherkonto.

1. Wählen Sie im Navigator des Speicherkontos die Option **Storage-Explorer (Vorschau)** .

1. Wählen Sie im Storage-Explorer die Option **Blobcontainer** und dann `devicedata` aus.

1. Klicken Sie im Inhaltsbereich auf den Ordner mit dem Namen des IoT-Hubs (gefolgt von Jahr, Monat, Tag und Stunde). Es werden mehrere Ordner angezeigt. Diese stehen für die Minuten, in denen der Schreibvorgang für die Daten durchgeführt wurde.

   ![Anzeigen von Ordnern im Blobspeicher](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Klicken Sie in einen dieser Ordner, um nach Datendateien mit den Bezeichnungen **00** und **01** für die Partition zu suchen.

1. Die Dateien werden im [Avro](https://avro.apache.org/)-Format geschrieben. Doppelklicken Sie auf eine dieser Dateien, um eine weitere Browserregisterkarte zu öffnen und die Daten teilweise zu rendern. Wenn Sie aufgefordert werden, die Datei in einem Programm zu öffnen, können Sie VS Code auswählen. Die Daten werden dann richtig gerendert.

1. Es ist nicht erforderlich, die Daten jetzt zu lesen oder zu interpretieren. Dies erledigen wir im nächsten Artikel.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Dieses Tutorial ist Teil einer Reihe, in der jeder Artikel auf den Schritten aufbaut, die jeweils im vorherigen Artikel ausgeführt wurden. Warten Sie mit dem Bereinigen von Ressourcen, bis Sie das letzte Tutorial abgeschlossen haben.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben wir ein .NET Core-Projekt zum Erstellen einer Reihe von virtuellen IoT-Geräten verwendet, über die Daten an unseren IoT-Hub und in einen Azure Storage-Container gesendet wurden. Mit diesem Projekt wird ein reales Szenario simuliert, bei dem physische IoT-Geräte Daten an einen IoT-Hub und weiter in einen zusammengestellten Speicher senden. Bei diesen Daten handelt es sich um Sensormesswerte, Betriebseinstellungen, Fehlersignale und -modi usw. Nachdem genügend Daten erfasst wurden, verwenden wir diese zum Trainieren von Modellen, mit denen die Restlebensdauer (RUL) des Geräts vorhergesagt wird. Dieser Vorgang für maschinelles Lernen wird im nächsten Artikel veranschaulicht.

Fahren Sie mit dem nächsten Artikel fort, um ein Machine Learning-Modell mit den Daten zu trainieren.

> [!div class="nextstepaction"]
> [Trainieren und Bereitstellen eines Azure Machine Learning-Modells](tutorial-machine-learning-edge-04-train-model.md)
