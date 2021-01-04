---
title: 'Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags mit der Azure CLI'
description: In dieser Schnellstartanleitung wird veranschaulicht, wie Sie die Azure CLI verwenden, um einen Azure Stream Analytics-Auftrag zu erstellen.
services: stream-analytics
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 07/01/2020
ms.openlocfilehash: 48ef9cc48f48cec92ae57774e5d89e300ee43385
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485378"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-using-the-azure-cli"></a>Schnellstart: Erstellen eines Azure Stream Analytics-Auftrags mit der Azure CLI

In dieser Schnellstartanleitung verwenden Sie die Azure CLI zum Definieren eines Stream Analytics-Auftrag, bei dem Echtzeit-Sensormeldungen mit einem Temperaturmesswert von über 27 Grad herausgefiltert werden. Ihr Stream Analytics-Auftrag liest Daten von IoT Hub, transformiert die Daten und schreibt sie zurück in einen Container im Blobspeicher. Die in dieser Schnellstartanleitung verwendeten Eingabedaten werden von einem Raspberry Pi-Onlinesimulator generiert.

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Erstellen Sie eine Ressourcengruppe. Alle Azure-Ressourcen müssen in einer Ressourcengruppe bereitgestellt werden. Mit Ressourcengruppen können verwandte Azure-Ressourcen organisiert und verwaltet werden.

   Erstellen Sie für diese Schnellstartanleitung mit dem folgenden [az group create](/cli/azure/group#az-group-create)-Befehl eine Ressourcengruppe mit dem Namen *streamanalyticsrg* am Standort *eastus*:

   ```azurecli
   az group create --name streamanalyticsrg --location eastus
   ```

## <a name="prepare-the-input-data"></a>Vorbereiten der Eingabedaten

Bereiten Sie vor dem Definieren des Stream Analytics-Auftrags die Daten vor, die als Eingabe für den Auftrag verwendet werden sollen.

Bei den folgenden Azure CLI-Codeblöcken handelt es sich um Befehle, mit denen die erforderlichen Eingabedaten für den Auftrag vorbereitet werden. Sehen Sie sich die Abschnitte an, um den Code zu verstehen.

1. Erstellen Sie mithilfe des Befehls [az iot hub create](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub) eine IoT Hub-Instanz. In diesem Beispiel wird eine IoT Hub-Instanz mit dem Namen **MyASAIoTHub** erstellt. Da IoT Hub-Namen eindeutig sind, müssen Sie einen eigenen IoT Hub-Namen festlegen. Legen Sie die SKU auf F1 fest, um den Free-Tarif zu nutzen, sofern dieser für Ihr Abonnement verfügbar ist. Wählen Sie andernfalls den niedrigsten verfügbaren Tarif aus.

    ```azurecli
    az iot hub create --name "MyASAIoTHub" --resource-group streamanalyticsrg --sku S1
    ```

    Nachdem der IoT-Hub erstellt wurde, rufen Sie die IoT Hub-Verbindungszeichenfolge mit dem Befehl [az iot hub show-connection-string](/cli/azure/iot/hub) ab. Kopieren Sie die komplette Verbindungszeichenfolge, und speichern Sie sie (Sie benötigen sie, wenn Sie die IoT Hub-Instanz als Eingabe für den Stream Analytics-Auftrag hinzufügen).

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

2. Fügen Sie IoT Hub mit dem Befehl [az iothub device-identity create](../iot-hub/quickstart-send-telemetry-c.md#register-a-device) ein Gerät hinzu. In diesem Beispiel wird ein Gerät namens **MyASAIoTDevice** erstellt.

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

3. Rufen Sie die Geräteverbindungszeichenfolge mithilfe des Befehls [az iot hub device-identity show-connection-string](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) ab. Kopieren Sie die komplette Verbindungszeichenfolge, und speichern Sie sie (Sie benötigen sie, wenn Sie den Raspberry Pi-Simulator erstellen).

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **Ausgabebeispiel:**

    ```output
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-a-blob-storage-account"></a>Erstellen eines Blob Storage-Kontos

Mit den folgenden Azure CLI-Codeblöcken wird ein Blobspeicherkonto erstellt, das für die Auftragsausgabe verwendet wird. Sehen Sie sich die Abschnitte an, um den Code zu verstehen.

1. Erstellen Sie mit dem Befehl [az storage account create](/cli/azure/storage/account) ein allgemeines Speicherkonto. Das allgemeine Speicherkonto kann für alle vier Dienste verwendet werden: Blobs, Files, Tables und Queues.

   Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

   ```azurecli
   az storage account create \
       --name <storage-account> \
       --resource-group streamanalyticsrg \
       --location eastus \
       --sku Standard_ZRS \
       --encryption-services blob
   ```

2. Rufen Sie den Schlüssel für Ihr Speicherkonto ab, indem Sie den Befehl[az storage account keys list](/cli/azure/storage/account/keys) ausführen. Speichern Sie diesen Schlüssel für die Verwendung im nächsten Schritt.

   ```azurecli
   az storage account keys list -g streamanalyticsrg -n <storage-account>
   ```

3. Erstellen Sie mit dem Befehl [az storage container create](/cli/azure/storage/container) einen Container zum Speichern von Blobs. Sie verwenden den Schlüssel des Speicherkontos, um den Vorgang zur Erstellung des Containers zu autorisieren. Weitere Informationen zum Autorisieren von Datenvorgängen mithilfe der Azure CLI finden Sie unter [Autorisieren des Zugriffs auf Blob- oder Warteschlangendaten mit der Azure-Befehlszeilenschnittstelle](../storage/blobs/authorize-data-operations-cli.md).

   ```azurecli
   az storage container create \
       --account-name <storage-account> \
       --name sample-container \
       --account-key <key>
       --auth-mode key
   ```

## <a name="create-a-stream-analytics-job"></a>Erstellen eines Stream Analytics-Auftrags

Mit den folgenden Azure CLI-Codeblöcken wird ein Stream Analytics-Auftrag erstellt. Sehen Sie sich die Abschnitte an, um den Code zu verstehen.

1. Erstellen Sie einen Stream Analytics-Auftrag, indem Sie den Befehl [az stream-analytics job create](/cli/azure/ext/stream-analytics/stream-analytics/job#ext-stream-analytics-az-stream-analytics-job-create) verwenden.

```azurecli
az stream-analytics job create \
    --resource-group streamanalyticsrg 
    --name streamanalyticsjob \
    --location eastus \
    --output-error-policy "Drop" \
    --events-outoforder-policy "Drop" \
    --events-outoforder-max-delay 5 \
    --events-late-arrival-max-delay 16 \     
    --data-locale "en-US"
```

## <a name="configure-input-to-the-job"></a>Konfigurieren einer Eingabe für den Auftrag

Fügen Sie Ihrem Auftrag eine Eingabe hinzu, indem Sie das Cmdlet [az stream-analytics input](/cli/azure/ext/stream-analytics/stream-analytics/input#ext-stream-analytics-az-stream-analytics-input-create) verwenden. Dieses Cmdlet übernimmt den Auftragsnamen, den Auftragseingabenamen, den Ressourcengruppennamen und die Auftragseingabedefinition als Parameter. Bei der Auftragseingabedefinition handelt es sich um eine JSON-Datei mit den Eigenschaften, die zum Konfigurieren der Eingabe eines Auftrags erforderlich sind. In diesem Beispiel erstellen Sie eine IoT Hub-Instanz als Eingabe.

Erstellen Sie auf Ihrem lokalen Computer eine Datei mit dem Namen `datasource.json`, und fügen Sie ihr die folgenden JSON-Daten hinzu. Ersetzen Sie den Wert für `sharedAccessPolicyKey` durch den `SharedAccessKey`-Teil der IoT Hub-Verbindungszeichenfolge, die Sie zuvor gespeichert haben.

```json
{
    "type": "Microsoft.Devices/IotHubs",
    "properties": {
        "iotHubNamespace": "iothub",
        "sharedAccessPolicyName": "iothubowner",
        "sharedAccessPolicyKey": "sharedAccessPolicyKey=",
        "consumerGroupName": "sdkconsumergroup",
        "endpoint": "messages/events"
    }
}
```

Erstellen Sie auf Ihrem lokalen Computer eine Datei mit dem Namen `serialization.json`, und fügen Sie ihr die folgenden JSON-Daten hinzu.

```json
{
     "type": "Json",
     "properties": {
         "encoding": "UTF8"
     }
}
```

Führen Sie anschließend das Cmdlet `az stream-analytics input create` aus. Ersetzen Sie den Wert der Variablen `datasource` durch den Pfad, unter dem Sie die JSON-Datei mit der Definition der Auftragseingabe gespeichert haben, und den Wert der Variablen `serialization` durch den Pfad, unter dem Sie die JSON-Serialisierungsdatei gespeichert haben.

```azurecli
az stream-analytics input create \
    --resource-group streamanalyticsrg 
    --job-name streamanalyticsjob \
    --name asaiotinput \
    --type Stream \
    --datasource datasource.json \
    --serialization serialization.json
```

## <a name="configure-output-to-the-job"></a>Konfigurieren einer Ausgabe für den Auftrag

Fügen Sie Ihrem Auftrag eine Ausgabe hinzu, indem Sie das Cmdlet [az stream-analytics output create](/cli/azure/ext/stream-analytics/stream-analytics/output#ext-stream-analytics-az-stream-analytics-output-create) verwenden. Dieses Cmdlet übernimmt den Auftragsnamen, den Auftragsausgabenamen, den Ressourcengruppennamen und die Auftragsausgabedefinition als Parameter. Bei der Auftragsausgabedefinition handelt es sich um eine JSON-Datei mit den Eigenschaften, die zum Konfigurieren der Ausgabe eines Auftrags erforderlich sind. In diesem Beispiel wird der Blobspeicher als Ausgabe verwendet.

Erstellen Sie auf Ihrem lokalen Computer eine Datei mit dem Namen `datasink.json`, und fügen Sie ihr die folgenden JSON-Daten hinzu. Ersetzen Sie den Wert für `accountKey` durch den Zugriffsschlüssel Ihres Speicherkontos. Dieser Wert ist in „$storageAccountKey“ gespeichert.

```json
{
    "type": "Microsoft.Storage/Blob",
    "properties": {
        "storageAccounts": [
            {
                "accountName": "<storage-account>",
                "accountKey": "accountKey=="
            }
        ],
        "container": "state",
        "pathPattern": "{date}/{time}",
        "dateFormat": "yyyy/MM/dd",
        "timeFormat": "HH"
    }
}
```

Führen Sie anschließend das Cmdlet `az stream-analytics output` aus. Ersetzen Sie den Wert der Variablen `datasource` durch den Pfad, unter dem Sie die JSON-Datei mit der Definition der Auftragsausgabe gespeichert haben, und den Wert der Variablen `serialization` durch den Pfad, unter dem Sie die JSON-Serialisierungsdatei gespeichert haben.

```azurecli
az stream-analytics output create \
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name asabloboutput \
    --datasource datasink.json \
    --serialization serialization.json
```

## <a name="define-the-transformation-query"></a>Definieren der Transformationsabfrage

Fügen Sie Ihrem Auftrag eine Transformation hinzu, indem Sie das Cmdlet [az stream-analytics transformation create](/cli/azure/ext/stream-analytics/stream-analytics/transformation#ext-stream-analytics-az-stream-analytics-transformation-create) verwenden. Dieses Cmdlet übernimmt den Auftragsnamen, den Auftragstransformationsnamen, den Ressourcengruppennamen und die Auftragstransformationsdefinition als Parameter. 

Führen Sie das `az stream-analytics transformation create`-Cmdlet aus.

```azurecli
az stream-analytics transformation create \
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name Transformation \
    --streaming-units "6" \
    --transformation-query "SELECT * INTO asabloboutput FROM asaiotinput HAVING Temperature > 27"
```
## <a name="run-the-iot-simulator"></a>Ausführen des IoT-Simulators

1. Öffnen Sie den [Raspberry Pi-Azure IoT-Onlinesimulator](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. Ersetzen Sie den Platzhalter in Zeile 15 durch die gesamte zuvor gespeicherte Verbindungszeichenfolge für das Azure IoT Hub-Gerät.

3. Klicken Sie auf **Ausführen**. In der Ausgabe sollten nun die Sensordaten und -nachrichten angezeigt werden, die an Ihre IoT Hub-Instanz gesendet werden.

    ![Raspberry Pi-Azure IoT-Onlinesimulator](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Starten des Stream Analytics-Auftrags und Überprüfen der Ausgabe

Starten Sie den Auftrag mit dem Cmdlet [az stream-analytics job start](/cli/azure/ext/stream-analytics/stream-analytics/job#ext-stream-analytics-az-stream-analytics-job-start). Dieses Cmdlet übernimmt den Auftragsnamen, den Ressourcengruppennamen, den Ausgabestartmodus und die Startzeit als Parameter. `OutputStartMode` akzeptiert die Werte `JobStartTime`, `CustomTime` und `LastOutputEventTime`.

Nach der Ausführung des folgenden Cmdlets wird als Ausgabe `True` zurückgegeben, wenn der Auftrag gestartet wird. Im Speichercontainer wird ein Ausgabeordner mit den transformierten Daten erstellt.

```azurecli
az stream-analytics job start \
    --resource-group streamanalyticsrg \
    --name streamanalyticsjob \
    --output-start-mode JobStartTime
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, den Streamingauftrag und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen. Durch das Löschen des Auftrags verhindern Sie, dass Kosten für die vom Auftrag verbrauchten Streamingeinheiten anfallen. Wenn Sie den Auftrag in Zukunft verwenden möchten, können Sie den Löschvorgang überspringen und Auftrag vorläufig beenden. Wenn Sie diesen Auftrag nicht weiter verwenden möchten, löschen Sie alle in diesem Schnellstart erstellten Ressourcen, indem Sie das folgende Cmdlet ausführen:

```azurecli
az group delete \
    --name streamanalyticsrg \
    --no-wait
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen einfachen Stream Analytics-Auftrag mit der Azure CLI bereitgestellt. Stream Analytics-Aufträge können auch mit dem [Azure-Portal](stream-analytics-quick-create-portal.md) und [Visual Studio](stream-analytics-quick-create-vs.md) bereitgestellt werden.

Wenn Sie Informationen zum Konfigurieren anderer Eingabequellen sowie zum Ausführen der Echtzeiterkennung benötigen, lesen Sie den folgenden Artikel:

> [!div class="nextstepaction"]
> [Erste Schritte mit Azure Stream Analytics: Betrugserkennung in Echtzeit](stream-analytics-real-time-fraud-detection.md)