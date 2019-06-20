---
title: Bereitstellen von Azure Machine Learning für ein Gerät – Azure IoT Edge | Microsoft-Dokumentation
description: In diesem Tutorial erstellen Sie ein Azure Machine Learning-Modell und stellen es dann als Modul auf einem Edgegerät bereit.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 6c48aaf404803c45122ed3fad0d6af1345406f7b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239665"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Tutorial: Bereitstellen von Azure Machine Learning als IoT Edge-Modul (Vorschauversion)

Verwenden Sie Azure Notebooks, um ein Modul für maschinelles Lernen zu entwickeln und es auf einem Linux-Gerät bereitzustellen, das Azure IoT Edge ausführt. 

Mithilfe von IoT Edge-Modulen können Sie Code bereitstellen, mit dem Ihre Geschäftslogik direkt auf Ihren IoT Edge-Geräten implementiert wird. In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie ein Azure Machine Learning-Modul bereitstellen, das anhand der Temperaturdaten eines simulierten Computers den Ausfall eines Geräts vorhersagt. Weitere Informationen zu Azure Machine Learning Service in IoT Edge finden Sie in der [Azure Machine Learning-Dokumentation](../machine-learning/service/how-to-deploy-to-iot.md).

Das Azure Machine Learning-Modul, das Sie in diesem Tutorial erstellen, liest die von Ihrem Gerät generierten Umgebungsdaten und kennzeichnet die Nachrichten ggf. als anomal.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Azure Machine Learning-Moduls
> * Pushen eines Modulcontainers in eine Azure Container Registry
> * Bereitstellen eines Azure Machine Learning-Moduls auf Ihrem IoT Edge-Gerät
> * Anzeigen generierter Daten

>[!NOTE]
>Azure Machine Learning-Module in Azure IoT Edge sind als Public Preview verfügbar.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Voraussetzungen

Ein Azure IoT Edge-Gerät:

* Sie können einen virtuellen Azure-Computer als IoT Edge-Gerät verwenden, indem Sie die Schritte im Schnellstart für [Linux](quickstart-linux.md) ausführen.
* Das Azure Machine Learning-Modul unterstützt keine Windows-Container.
* Das Azure Machine Learning-Modul unterstützt keine ARM-Prozessoren.

Cloudressourcen:

* Eine [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-Instanz in Azure im Tarif „Free“ oder „Standard“.
* Ein Azure Machine Learning-Arbeitsbereich. Befolgen Sie die Anweisungen unter [Verwenden des Azure-Portals zum Ausführen der ersten Schritte mit Azure Machine Learning](../machine-learning/service/quickstart-get-started.md), um einen zu erstellen und zu erfahren, wie Sie ihn verwenden.
   * Notieren Sie sich den Arbeitsbereichsnamen, die Ressourcengruppe und die Abonnement-ID. Diese Werte sind in der Übersicht über den Arbeitsbereich im Azure-Portal verfügbar. Diese Werte verwenden Sie später im Tutorial zum Verbinden eines Azure-Notebooks mit Ihren Arbeitsbereichsressourcen. 


## <a name="create-and-deploy-azure-machine-learning-module"></a>Erstellen und Bereitstellen eines Azure Machine Learning-Moduls

In diesem Abschnitt konvertieren Sie die trainierten Machine Learning-Modelldateien und laden sie in einen Azure Machine Learning Service-Container herunter. Alle erforderlichen Komponenten für das Docker-Image finden Sie im [Git-Repository mit dem KI-Toolkit für Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Führen Sie die folgenden Schritte zum Hochladen dieses Repositorys in Microsoft Azure Notebooks aus, um den Container zu erstellen und in Azure Container Registry zu pushen.


1. Navigieren Sie zu Ihren Azure Notebooks-Projekten. Dorthin können Sie über Ihren Azure Machine Learning Service-Arbeitsbereich im [Azure-Portal](https://portal.azure.com) oder durch Anmelden bei [Microsoft Azure Notebooks](https://notebooks.azure.com/home/projects) mit Ihrem Azure-Konto navigieren.

2. Wählen Sie **Upload GitHub Repo** (GitHub-Repository hochladen) aus.

3. Geben Sie den folgenden GitHub-Repositorynamen an: `Azure/ai-toolkit-iot-edge`. Deaktivieren Sie das Kontrollkästchen **Öffentlich**, wenn das Projekt privat bleiben soll. Wählen Sie **Importieren** aus. 

4. Navigieren Sie nach Abschluss des Imports zu dem neuen Projekt **ai-toolkit-iot-edge**, und öffnen Sie den Ordner **IoT Edge anomaly detection tutorial**. 

5. Vergewissern Sie sich, dass Ihr Projekt ausgeführt wird. Wenn dies nicht der Fall ist, wählen Sie **Run on Free Compute** (Mit kostenlosem Compute ausführen) aus.

   ![Run on free compute (Mit kostenlosem Compute ausführen)](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Öffnen Sie die Datei **aml_config/config.json**.

7. Bearbeiten Sie die Konfigurationsdatei, und geben Sie die Werte für die Azure-Abonnement-ID, eine Ressourcengruppe in Ihrem Abonnement und den Namen Ihres Azure Machine Learning Service-Arbeitsbereichs an. Alle diese Werte finden Sie im Abschnitt **Übersicht** für Ihren Arbeitsbereich in Azure. 

8. Speichern Sie die Konfigurationsdatei.

9. Öffnen Sie die Datei **00-anomaly-detection-tutorial.ipynb**.

10. Wählen Sie bei der entsprechenden Aufforderung den **Python 3.6**-Kernel und dann **Set Kernel** (Kernel festlegen) aus.

11. Bearbeiten Sie die erste Zelle im Notebook entsprechend den Anweisungen in den Kommentaren. Verwenden Sie die gleichen Werte für Ressourcengruppe, Abonnement-ID und Arbeitsbereichsnamen, die Sie in der Konfigurationsdatei eingefügt haben.

12. Führen Sie die Zellen im Notebook aus, indem Sie sie auswählen und dann **Ausführen** auswählen oder `Shift + Enter` drücken.

    >[!TIP]
    >Einige der Zellen im Tutorial-Notebook zur Anomalieerkennung sind optional, da sie Ressourcen erstellen, über die einige Benutzer möglicherweise noch nicht verfügen, z. B. einen IoT Hub. Wenn Sie die Informationen zu Ihren vorhandenen Ressourcen in der ersten Zelle einfügen, werden beim Ausführen der Zellen, über die neue Ressourcen erstellt werden, Fehler angezeigt, da Ressourcen in Azure nicht dupliziert werden. Dies stellt kein Problem dar. Sie können die Fehler ignorieren oder diese optionalen Abschnitte ganz überspringen. 

Nach Abschluss aller Schritte im Notebook haben Sie ein Modell zur Anomalieerkennung trainiert, es als Docker-Containerimage erstellt und dieses Image mithilfe von Push an Azure Container Registry übertragen. Zudem haben Sie das Modell getestet und es schließlich für Ihr IoT Edge-Gerät bereitgestellt. 

## <a name="view-container-repository"></a>Anzeigen des Containerrepositorys

Überprüfen Sie, ob Ihr Containerimage erfolgreich erstellt und in der Azure Container Registry-Instanz gespeichert wurde, die Ihrer Umgebung für maschinelles Lernen zugeordnet ist. Mit dem Notebook, das Sie im vorherigen Abschnitt verwendet haben, wurden automatisch das Containerimage und die Registrierungsanmeldeinformationen für Ihr IoT Edge-Gerät angegeben. Sie sollten jedoch wissen, wo diese Angaben gespeichert sind, sodass Sie die Informationen später finden können. 

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Machine Learning Service-Arbeitsbereich. 

2. Im Abschnitt **Übersicht** sind die Informationen zum Arbeitsbereich und die zugehörigen Ressourcen aufgeführt. Wählen Sie unter **Registrierung** den Wert aus, der als Arbeitsbereichsnamen dienen soll, gefolgt von Zufallszahlen. 

3. Wählen Sie in der Containerregistrierung die Option **Repositorys** aus. Es sollte das Repository **tempanomalydetection** angezeigt werden, das mit dem im vorherigen Abschnitt ausgeführten Notebook erstellt wurde. 

4. Wählen Sie **tempanomalydetection** aus. Das Repository sollte ein Tag enthalten: **1**. 

   Sie kennen nun den Registrierungsnamen, den Repositorynamen und das Tag und damit den vollständigen Imagepfad des Containers. Imagepfade sehen wie folgt aus: **\<Registrierungsname\>.azurecr.io/tempanomalydetection:1**. Über den Imagepfad können Sie den Container auf IoT Edge-Geräten bereitstellen. 

5. Wählen Sie in der Containerregistrierung die Option **Zugriffsschlüssel** aus. Daraufhin sollten Zugriffsanmeldeinformationen, einschließlich **Anmeldeserver** sowie **Benutzername** und **Kennwort** für einen Administratorbenutzer angezeigt werden.

   Diese Anmeldeinformationen können im Bereitstellungsmanifest eingefügt werden, sodass das IoT Edge-Gerät Zugriff zum Abrufen von Containerimages aus der Registrierung erhält. 

Damit wissen Sie, wo das Machine Learning-Containerimage gespeichert ist. Der nächste Abschnitt führt Sie schrittweise durch das Anzeigen des als Modul auf Ihrem IoT Edge-Gerät ausgeführten Containers. 

## <a name="view-generated-data"></a>Anzeigen generierter Daten

Sie können Nachrichten anzeigen, die von den einzelnen IoT Edge-Modulen generiert werden, und Sie können Nachrichten anzeigen, die an Ihre IoT Hub-Instanz übermittelt werden.

### <a name="view-data-on-your-iot-edge-device"></a>Anzeigen von Daten auf Ihrem IoT Edge-Gerät

Auf Ihrem IoT Edge-Gerät können Sie die Nachrichten anzeigen, die von den einzelnen Modulen gesendet werden.

Möglicherweise müssen Sie `sudo` verwenden, um erhöhte Berechtigungen zu erhalten und `iotedge`-Befehle ausführen zu können. Durch Abmelden und erneutes Anmelden bei Ihrem Gerät werden Ihre Berechtigungen automatisch aktualisiert.

1. Zeigen Sie alle Module auf Ihrem IoT Edge-Gerät an.

   ```cmd/sh
   iotedge list
   ```

2. Zeigen Sie die Nachrichten an, die von einem bestimmten Gerät gesendet werden. Verwenden Sie den Modulnamen aus der Ausgabe des vorherigen Befehls.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Anzeigen von Daten, die bei Ihrer IoT Hub-Instanz eingehen

Sie können die Gerät-zu-Cloud-Nachrichten, die Ihre IoT Hub-Instanz empfängt, mithilfe der [Azure IoT Hub-Toolkit-Erweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (bisher als Azure IoT-Toolkit-Erweiterung bekannt) anzeigen.

Die folgenden Schritte zeigen, wie Sie Visual Studio Code einrichten, um Gerät-zu-Cloud-Nachrichten zu überwachen, die bei Ihrer IoT Hub-Instanz eingehen.

1. Wählen Sie in Visual Studio Code **IoT Hub-Geräte** aus.

2. Wählen Sie **...** und dann **IoT Hub-Verbindungszeichenfolge festlegen** aus dem Menü aus.

   ![Festlegen der IoT Hub-Verbindungszeichenfolge](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Geben Sie in das Textfeld, das sich oben auf der Seite öffnet, die Verbindungszeichenfolge „iothubowner“ für Ihren IoT Hub ein. Ihr IoT Edge-Gerät sollte in der Liste der IoT Hub-Geräte angezeigt werden.

4. Wählen Sie erneut die Ellipse ( **...** ) und anschließend die Option **Start Monitoring Built-in Event Endpoint** (Überwachung des integrierten Ereignisendpunkts starten) aus.

5. Beobachten Sie die ankommenden Nachrichten von tempSensor alle fünf Sekunden. Der Nachrichtentext enthält die **anomaly**-Eigenschaft, die vom Machine Learning-Modul den Wert TRUE oder FALSE erhält. Die **AzureMLResponse**-Eigenschaft enthält den Wert „OK“, wenn das Modell erfolgreich ausgeführt wurde.

   ![Antwort von Azure Machine Learning Service im Nachrichtentext](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit dem nächsten empfohlenen Artikel fortfahren möchten, können Sie die erstellten Ressourcen und Konfigurationen beibehalten und wiederverwenden. Sie können auch dasselbe IoT Edge-Gerät als Testgerät weiter nutzen.

Andernfalls können Sie die in diesem Artikel erstellten lokalen Konfigurationen und Azure-Ressourcen löschen, um Kosten zu vermeiden.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein von Azure Machine Learning unterstütztes IoT Edge-Modul bereitgestellt. Sie können mit einem der anderen Tutorials fortfahren, um andere Möglichkeiten kennenzulernen, wie Azure IoT Edge Ihnen beim Umwandeln von Daten in geschäftliche Erkenntnisse auf Edge-Ebene helfen kann.

> [!div class="nextstepaction"]
> [Tutorial: Perform image classification at the edge with Custom Vision Service](tutorial-deploy-custom-vision.md) (Tutorial: Ausführen der Bildklassifizierung im Edge-Bereich mit Custom Vision Service)

