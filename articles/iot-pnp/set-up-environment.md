---
title: 'Schnellstart: Einrichten der IoT-Ressourcen, die Sie für IoT Plug & Play benötigen | Microsoft-Dokumentation'
description: 'Schnellstart: Erstellen Sie eine IoT Hub- und eine Device Provisioning Service-Instanz zur Verwendung in den IoT Plug & Play-Schnellstarts und -Tutorials.'
author: dominicbetts
ms.author: dobett
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: b2b17cffb8abd748315c437f46000995c4cb598a
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831891"
---
# <a name="quickstart---set-up-your-environment-for-the-iot-plug-and-play-quickstarts-and-tutorials"></a>Schnellstart: Einrichten Ihrer Umgebung für die IoT Plug & Play-Schnellstarts und -Tutorials

Bevor Sie einen der Schnellstarts und eines der Tutorials zu IoT Plug & Play durcharbeiten können, müssen Sie einen IoT Hub und den Device Provisioning Service (DPS) in Ihrem Azure-Abonnement konfigurieren. Sie benötigen auch lokale Kopien der Modelldateien, die von den Beispielanwendungen und dem Tool „Azure IoT-Explorer“ verwendet werden.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

Damit Sie die Azure-Befehlszeilenschnittstelle (Azure CLI) nicht lokal installieren müssen, können Sie die Clouddienste über die Azure Cloud Shell einrichten.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resources"></a>Erstellen der Ressourcen

So erstellen Sie eine Azure-Ressourcengruppe für die Ressourcen:

```azurecli-interactive
az group create --name my-pnp-resourcegroup --location centralus
```

Erstellen Sie einen IoT Hub. Im folgenden Befehl wird der Name `my-pnp-hub` als Beispiel für den Namen des zu erstellenden IoT Hubs verwendet. Wählen Sie einen eindeutigen Namen für Ihren IoT Hub aus, der statt `my-pnp-hub` verwendet werden soll:

```azurecli-interactive
az iot hub create --name my-pnp-hub --resource-group my-pnp-resourcegroup --sku F1 --partition-count 2
```

Erstellen Sie eine DPS-Instanz. Im folgenden Befehl wird der Name `my-pnp-dps` als Beispiel für den Namen der zu erstellenden DPS-Instanz verwendet. Wählen Sie einen eindeutigen Namen für Ihre DPS-Instanz aus, der statt `my-pnp-dps` verwendet werden soll:

```azurecli-interactive
az iot dps create --name my-pnp-dps --resource-group my-pnp-resourcegroup
```

Verwenden Sie die folgenden Befehle zum Verknüpfen der DPS-Instanz mit Ihrem IoT Hub. Ersetzen Sie `my-pnp-dps` und `my-pnp-hub` durch die eindeutigen Namen, die Sie zuvor gewählt haben:

```azurecli-interactive
hubConnectionString=$(az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString -o tsv)
az iot dps linked-hub create --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --location centralus --connection-string $hubConnectionString
```

## <a name="retrieve-the-settings"></a>Abrufen der Einstellungen

In einigen Schnellstarts und Tutorials wird die Verbindungszeichenfolge für Ihren IoT Hub verwendet. Sie benötigen die Verbindungszeichenfolge auch beim Einrichten des Tools „Azure IoT-Explorer“. Rufen Sie die Verbindungszeichenfolge ab, und notieren Sie sie sich. Ersetzen Sie `my-pnp-hub` durch den eindeutigen Namen, den Sie für Ihre IoT Hub-Instanz gewählt haben:

```azurecli-interactive
az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString
```

In den meisten Schnellstarts und Tutorials wird der *ID-Bereich* Ihrer DPS-Konfiguration verwendet. Rufen Sie den ID-Bereich ab, und notieren Sie ihn sich. Ersetzen Sie `my-pnp-dps` durch den eindeutigen Namen, den Sie für Ihre DPS-Instanz gewählt haben:

```azurecli-interactive
az iot dps show --name my-pnp-dps --query properties.idScope
```

In allen Schnellstarts und Tutorials wird eine DPS-Geräteregistrierung verwendet. Verwenden Sie den folgenden Befehl zum Erstellen einer `my-pnp-device` *einzelnen Geräteregistrierung* in Ihrer DPS-Instanz. Ersetzen Sie `my-pnp-dps` durch den eindeutigen Namen, den Sie für Ihre DPS-Instanz gewählt haben. Notieren Sie sich die Registrierungs-ID und Primärschlüsselwerte, die in den Schnellstarts und Tutorials verwendet werden sollen:

```azurecli-interactive
az iot dps enrollment create --attestation-type symmetrickey --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --enrollment-id my-pnp-device --device-id my-pnp-device --query '{registrationID:registrationId,primaryKey:attestation.symmetricKey.primaryKey}'
```

## <a name="create-environment-variables"></a>Erstellen von Umgebungsvariablen

Erstellen Sie fünf Umgebungsvariablen, um die Beispiele in den Schnellstarts und Tutorials so zu konfigurieren, dass der Device Provisioning Service (DPS) zum Herstellen einer Verbindung mit Ihrem IoT Hub verwendet wird:

* **IOTHUB_DEVICE_SECURITY_TYPE**: der Wert `DPS`.
* **IOTHUB_DEVICE_DPS_ID_SCOPE**: der DPS-ID-Bereich, den Sie sich zuvor notiert haben.
* **IOTHUB_DEVICE_DPS_DEVICE_ID**: der Wert `my-pnp-device`.
* **IOTHUB_DEVICE_DPS_DEVICE_KEY**: der Primärschlüssel für Registrierung, den Sie sich zuvor notiert haben.
* **IOTHUB_DEVICE_DPS_ENDPOINT**: der Wert `global.azure-devices-provisioning.net`.

Die Dienstbeispiele benötigen die folgenden Umgebungsvariablen zum Identifizieren des Hubs und Geräts, mit denen eine Verbindung hergestellt werden soll:

* **IOTHUB_CONNECTION_STRING**: die IoT-Hub-Verbindungszeichenfolge, die Sie sich zuvor notiert haben.
* **IOTHUB_DEVICE_ID**: `my-pnp-device`.

Beispielsweise in einer Linux Bash-Shell:

```bash
export IOTHUB_DEVICE_SECURITY_TYPE="DPS"
export IOTHUB_DEVICE_DPS_ID_SCOPE="<Your ID scope>"
export IOTHUB_DEVICE_DPS_DEVICE_ID="my-pnp-device"
export IOTHUB_DEVICE_DPS_DEVICE_KEY="<Your enrolment primary key>"
export IOTHUB_DEVICE_DPS_ENDPOINT="global.azure-devices-provisioning.net"
export IOTHUB_CONNECTION_STRING="<Your IoT hub connection string>"
export IOTHUB_DEVICE_ID="my-pnp-device"
```

Beispielsweise in der Windows-Befehlszeile:

```cmd
set IOTHUB_DEVICE_SECURITY_TYPE=DPS
set IOTHUB_DEVICE_DPS_ID_SCOPE=<Your ID scope>
set IOTHUB_DEVICE_DPS_DEVICE_ID=my-pnp-device
set IOTHUB_DEVICE_DPS_DEVICE_KEY=<Your enrolment primary key>
set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
set IOTHUB_CONNECTION_STRING=<Your IoT hub connection string>
set IOTHUB_DEVICE_ID=my-pnp-device
```

## <a name="download-the-model-files"></a>Herunterladen der Modelldateien

In den Schnellstarts und Tutorials werden Beispielmodelldateien für die Geräte Temperaturregler und Thermostat verwendet. So laden Sie die Beispielmodelldateien herunter:

1. Erstellen Sie auf Ihrem lokalen Computer einen Ordner mit dem Namen *models*.

1. Klicken Sie mit der rechten Maustaste auf [TemperatureController.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json), und speichern Sie die JSON-Datei im Ordner *models*.

1. Klicken Sie mit der rechten Maustaste auf [Thermostat.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json), und speichern Sie die JSON-Datei im Ordner *models*.

## <a name="install-the-azure-iot-explorer"></a>Installieren von Azure IoT-Explorer

In den Schnellstarts und Tutorials wird das Tool **Azure IoT-Explorer** verwendet. Navigieren Sie auf GitHub zu den [Releases von Azure-IoT-Explorer](https://github.com/Azure/azure-iot-explorer/releases), und erweitern Sie die Liste der Assets, um die neueste Version anzuzeigen. Laden Sie die neueste Version der Anwendung für Ihr Betriebssystem herunter, und installieren Sie sie.

Bei der ersten Ausführung des Tools werden Sie zur Eingabe der IoT Hub-Verbindungszeichenfolge aufgefordert. Verwenden Sie die Verbindungszeichenfolge, die Sie sich zuvor notiert haben.

Konfigurieren Sie das Tool zur Verwendung der zuvor heruntergeladenen Modelldateien. Wählen Sie auf der Startseite des Tools **IoT Plug & Play-Einstellungen** und dann **+ Hinzufügen > Lokaler Ordner** aus. Wählen Sie den zuvor erstellten Ordner *Modelle* aus. Wählen Sie dann **Speichern** aus, um die Einstellungen zu speichern.

Weitere Informationen finden Sie unter [Installieren und Verwenden des Azure IoT-Explorers](howto-use-iot-explorer.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Weil Sie den IoT Hub und die DPS-Instanz für alle IoT Plug & Play-Schnellstarts und -Tutorials verwenden können, müssen Sie die Schritte in diesem Artikel nur einmal ausführen. Wenn Sie damit fertig sind, können Sie die Ressourcen mit folgendem Befehl aus Ihrem Abonnement entfernen:

```azurecli-interactive
az group delete --name my-pnp-resourcegroup
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Umgebung eingerichtet haben, können Sie einen der Schnellstarts oder eines der Tutorials wie die folgenden ausprobieren:

> [!div class="nextstepaction"]
> [Verbinden einer IoT Plug & Play-Beispielgeräteanwendung mit IoT Hub](quickstart-connect-device.md)
