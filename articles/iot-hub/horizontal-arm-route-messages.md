---
title: Verwenden einer ARM-Vorlage zum Veröffentlichen von Azure IoT Hub und Speicherkonto sowie zum Weiterleiten von Nachrichten
description: Verwenden einer ARM-Vorlage zum Veröffentlichen von Azure IoT Hub und Speicherkonto sowie zum Weiterleiten von Nachrichten
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: robinsh
ms.custom: mvc, subject-armqs
ms.openlocfilehash: fc8ddba2ec9b7bc9f1c2db8673ab805810afe17e
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981288"
---
# <a name="quickstart-deploy-an-azure-iot-hub-and-a-storage-account-using-an-arm-template"></a>Schnellstart: Bereitstellen einer Azure IoT Hub-Instanz und eines Speicherkontos mithilfe einer ARM-Vorlage

In dieser Schnellstartanleitung wird eine ARM-Vorlage (Azure Resource Manager) verwendet, um eine IoT Hub-Instanz, die Nachrichten an Azure Storage weiterleitet, und ein Speicherkonto zum Speichern der Nachrichten zu erstellen. Nachdem Sie dem Hub manuell ein virtuelles IoT-Gerät für die Nachrichtenübermittlung hinzugefügt haben, konfigurieren Sie die Verbindungsinformationen in einer Anwendung namens *arm-read-write*, um Nachrichten vom Gerät an den Hub zu übermitteln. Der Hub ist so konfiguriert, dass die an den Hub gesendeten Nachrichten automatisch an das Speicherkonto weitergeleitet werden. Am Ende dieser Schnellstartanleitung können Sie das Speicherkonto öffnen und die gesendeten Nachrichten anzeigen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt aus den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-iothub-auto-route-messages) und heißt `101-iothub-auto-route-messages`.

:::code language="json" source="~/quickstart-templates/101-iothub-auto-route-messages/azuredeploy.json":::

Zwei Azure-Ressourcen sind in der Vorlage definiert:

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Devices/IotHubs](/azure/templates/microsoft.devices/iothubs)

## <a name="deploy-the-template-and-run-the-sample-app"></a>Bereitstellen der Vorlage und Ausführen der Beispiel-App

In diesem Abschnitt erfahren Sie, wie Sie die Vorlage bereitstellen, ein virtuelles Gerät erstellen und die Anwendung „arm-read-write“ ausführen, um die Nachrichten zu senden.

1. Erstellen Sie die Ressourcen, indem Sie die ARM-Vorlage bereitstellen.

    > [!TIP]
    > Wählen Sie die im Anschluss bereitgestellte Schaltfläche aus, um die Bereitstellung der Vorlage zu starten. Während die Bereitstellung läuft, können Sie die auszuführende Anwendung „arm-read-write“ einrichten.

    [![In Azure bereitstellen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

1. Laden Sie die [IoT-Beispiele für C#](/samples/azure-samples/azure-iot-samples-csharp/azure-iot-samples-for-csharp-net/) herunter, und entpacken Sie sie.

1. Öffnen Sie ein Befehlsfenster, und navigieren Sie zu dem Ordner, in dem Sie die IoT-Beispiele für C# entzippt haben. Suchen Sie nach dem Ordner, der die Datei „arm-read-write.csproj“ enthält. Dieses Befehlsfenster wird zum Erstellen der Umgebungsvariablen verwendet. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, um die Schlüssel zu erhalten. Wählen Sie **Ressourcengruppen** und anschließend die für diese Schnellstartanleitung verwendete Ressourcengruppe aus.

   ![Auswählen der Ressourcengruppe](./media/horizontal-arm-route-messages/01-select-resource-group.png)

1. Die im Zuge der ARM-Vorlagenbereitstellung erstellte IoT Hub-Instanz und das entsprechende Speicherkonto werden angezeigt. Warten Sie, bis die Vorlage vollständig bereitgestellt wurde. Wählen Sie dann Ihre Ressourcengruppe aus, um Ihre Ressourcen anzuzeigen.

   ![Anzeigen der Ressourcen in der Ressourcengruppe](./media/horizontal-arm-route-messages/02-view-resources-in-group.png)

1. Sie benötigen den **Namen des Hubs**. Wählen Sie den Hub in der Ressourcenliste aus. Kopieren Sie am oberen Rand des IoT Hub-Abschnitts den Namen des Hubs in die Windows-Zwischenablage.

   ![Kopieren des Hubnamens](./media/horizontal-arm-route-messages/03-copy-hub-name.png)

    Fügen Sie den Namen des Hubs im folgenden Befehl an der angegebenen Stelle ein, und führen Sie den Befehl im Befehlsfenster aus:

    ```cmd
    SET IOT_HUB_URI=<hub name goes here>.azure-devices-net;
    ```

   Beispiel:

   ```cmd
   SET IOT_HUB_URI=ContosoTestHubdlxlud5h.azure-devices-net;
   ```

1. Die nächste Umgebungsvariable ist der IoT-Geräteschlüssel. Fügen Sie dem Hub ein neues Gerät hinzu, indem Sie im IoT Hub-Menü für den Hub die Option **IoT-Geräte** auswählen.

   ![Auswählen von IoT-Geräten](./media/horizontal-arm-route-messages/04-select-iot-devices.png)

1. Wählen Sie auf der rechten Seite des Bildschirms die Option **+ NEU** aus, um ein neues Gerät hinzuzufügen.

   Geben Sie den Namen des neuen Geräts an. In dieser Schnellstartanleitung wird ein Name verwendet, der mit **Contoso-Test-Device** beginnt. Speichern Sie das Gerät, und öffnen Sie den Bildschirm anschließend erneut, um den Geräteschlüssel abzurufen. (Der Schlüssel wird generiert, wenn Sie den Bereich schließen.) Wählen Sie entweder den primären oder den sekundären Schlüssel aus, und kopieren Sie ihn in die Windows-Zwischenablage. Legen Sie im Befehlsfenster den auszuführenden Befehl fest, und drücken Sie anschließend die **EINGABETASTE**. Der Befehl sollte wie folgt aussehen (allerdings mit eingefügtem Geräteschlüssel):

   ```cmd
   SET IOT_DEVICE_KEY=<device-key-goes-here>
   ```

1. Die letzte Umgebungsvariable ist die **Geräte-ID**. Richten Sie im Befehlsfenster den Befehl ein, und führen Sie ihn aus.

   ```cmd
   SET IOT_DEVICE_ID=<device-id-goes-here>
   ```

   Beispiel:

   ```cmd
   SET IOT_DEVICE_ID=Contoso-Test-Device
   ```

1. Geben Sie zum Anzeigen der definierten Umgebungsvariablen den Befehl „SET“ in die Befehlszeile ein, drücken Sie die EINGABETASTE, und suchen Sie nach Variablen, die mit **IoT** **beginnen**.

   ![Anzeigen von Umgebungsvariablen](./media/horizontal-arm-route-messages/06-environment-variables.png)

    Nachdem Sie die Umgebungsvariablen festgelegt haben, können Sie die Anwendung über das gleiche Befehlsfenster ausführen. Da Sie das gleiche Fenster verwenden, sind die Variablen im Arbeitsspeicher verfügbar, wenn Sie die Anwendung ausführen.

1. Geben Sie zum Ausführen der Anwendung im Befehlsfenster den folgenden Befehl ein, und drücken Sie die **EINGABETASTE**.

    `dotnet run arm-read-write`

   Von der Anwendung werden Nachrichten generiert und in der Konsole angezeigt, während die einzelnen Nachrichten an die IoT Hub-Instanz gesendet werden. Der Hub wurde in der ARM-Vorlage mit automatisiertem Routing konfiguriert. Nachrichten, die den Text `level = storage` enthalten, werden automatisch an das Speicherkonto weitergeleitet. Führen Sie die App zehn bis 15 Minuten aus, und drücken Sie dann ein- oder zweimal die **EINGABETASTE**, um die App zu beenden.

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie die Ressourcengruppe und anschließend das Speicherkonto aus.

1. Führen Sie einen Drilldown für das Speicherkonto aus, bis Sie zu Dateien gelangen.

   ![Betrachten der Speicherkontodateien](./media/horizontal-arm-route-messages/07-see-storage.png)

1. Wählen Sie eine der Dateien und anschließend **Herunterladen** aus, um die Datei an einen Speicherort herunterzuladen, den Sie später leicht finden. Sie hat einen numerischen Namen (beispielsweise „47“). Fügen Sie am Ende _.txt_ hinzu, und doppelklicken Sie auf die Datei, um sie zu öffnen.

1. In der Datei steht jede Zeile für eine andere Nachricht, und der Nachrichtentext ist verschlüsselt. Das muss so sein, damit Sie Abfragen für den Nachrichtentext durchführen können.

   ![Anzeigen der gesendeten Nachrichten](./media/horizontal-arm-route-messages/08-messages.png)

   > [!NOTE]
   > Diese Nachrichten sind in UTF-32 und Base64 codiert. Wenn Sie die Nachricht auslesen, müssen Sie sie aus Base64 und UTF-32 decodieren, um sie im ASCII-Format lesen zu können. Bei Interesse können Sie die Methode „ReadOneRowFromFile“ aus dem Routingtutorial verwenden, um eine einzelne Zeile aus einer dieser Nachrichtendateien zu lesen und in ASCII zu decodieren. „ReadOneRowFromFile“ befindet sich im Repository mit IoT-Beispielen für C#, das Sie für diese Schnellstartanleitung entzippt haben. Der Pfad lautet wie folgt (von der obersten Ebene des Ordners): *./iot-hub/Tutorials/Routing/SimulatedDevice/Program.cs*. Legen Sie den booleschen Wert `readTheFile` auf „true“ fest, und hartcodieren Sie den Pfad der Datei auf dem Datenträger. Daraufhin wird die Datei geöffnet und die erste Zeile darin gelesen.

Sie haben eine ARM-Vorlage bereitgestellt, um eine IoT Hub-Instanz und ein Speicherkonto zu erstellen. Außerdem haben Sie ein Programm ausgeführt, um Nachrichten an den Hub zu senden. Die Nachrichten werden automatisch im Speicherkonto gespeichert, wo sie angezeigt werden können.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Melden Sie sich zum Entfernen der in dieser Schnellstartanleitung hinzugefügten Ressourcen beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **Ressourcengruppen** aus, und navigieren Sie zu der in dieser Schnellstartanleitung verwendeten Ressourcengruppe. Wählen Sie die Ressourcengruppe und anschließend *Löschen* aus. Daraufhin werden alle Ressourcen in der Gruppe gelöscht.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Erstellen und Bereitstellen Ihrer ersten ARM-Vorlage](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
