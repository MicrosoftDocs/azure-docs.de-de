---
title: Konfigurieren des Dateiuploads in IoT Hub mit der Azure CLI | Microsoft Docs
description: Erfahren Sie, wie Sie Dateiuploads in Azure IoT Hub mithilfe der plattformübergreifenden Azure CLI konfigurieren.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: 4dbda13ffe04e0a4214b24ccaca2b8103a39b9f2
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92536061"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Konfigurieren von IoT Hub-Dateiuploads mit der Azure CLI

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Damit Sie [Dateien von einem Gerät hochladen](iot-hub-devguide-file-upload.md) können, müssen Sie Ihrem IoT Hub zunächst ein Azure Storage-Konto zuordnen. Sie können ein vorhandenes Speicherkonto auswählen oder ein neues erstellen.

Für dieses Tutorial benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.

* [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).

* Einen Azure IoT Hub. Wenn Sie über keinen IoT Hub verfügen, können Sie mit dem [`az iot hub create`-Befehl](/cli/azure/iot/hub#az-iot-hub-create) oder [über das Portal](iot-hub-create-through-portal.md) einen IoT Hub erstellen.

* Azure Storage-Konto Wenn Sie über kein Azure Storage Konto verfügen, können Sie über die Azure CLI ein Konto erstellen. Weitere Informationen finden Sie unter [Erstellen eines Speicherkontos](../storage/common/storage-account-create.md).

## <a name="sign-in-and-set-your-azure-account"></a>Anmelden und Festlegen Ihres Azure-Kontos

Melden Sie sich bei Ihrem Azure-Konto an, und wählen Sie Ihr Abonnement aus.

1. Führen Sie an der Eingabeaufforderung den [Anmeldebefehl](/cli/azure/get-started-with-azure-cli) aus:

    ```azurecli
    az login
    ```

    Befolgen Sie die Anweisungen zur Authentifizierung mit dem Code, und melden Sie sich in einem Webbrowser bei Ihrem Azure-Konto an.

2. Wenn Sie über mehrere Azure-Abonnements verfügen, erhalten Sie durch die Anmeldung bei Azure Zugriff auf alle Azure-Konten, die mit Ihren Anmeldeinformationen verknüpft sind. Führen Sie den folgenden [Befehl aus, um eine Liste der Azure-Konten anzuzeigen](/cli/azure/account), die Sie verwenden können:

    ```azurecli
    az account list
    ```

    Führen Sie den folgenden Befehl aus, um das Abonnement auszuwählen, das Sie zum Ausführen der Befehle zum Erstellen Ihres IoT Hubs verwenden möchten. Sie können entweder den Abonnementnamen oder die ID aus der Ausgabe des vorherigen Befehls verwenden:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Abrufen der Details Ihres Speicherkontos

In den folgenden Schritten wird davon ausgegangen, dass Sie Ihr Speicherkonto mit dem **Ressourcen-Manager** -Bereitstellungsmodell und nicht mit dem **klassischen** Bereitstellungsmodell erstellt haben.

Sie benötigen die Verbindungszeichenfolge eines Azure-Speicherkontos, um das Hochladen von Dateien von Ihren Geräten zu konfigurieren. Das Speicherkonto muss sich in demselben Abonnement wie der IoT-Hub befinden. Sie benötigen auch den Name des Blobcontainers im Speicherkonto. Verwenden Sie den folgenden Befehl, um Ihre Speicherkontoschlüssel abzurufen:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Notieren Sie sich den **connectionString** -Wert. Sie benötigen ihn in den folgenden Schritten.

Sie können einen vorhandenen Blobcontainer für das Hochladen von Dateien verwenden oder einen neuen Blobcontainer erstellen:

* Um die vorhandenen Blobcontainer in Ihrem Speicherkonto aufzulisten, verwenden Sie den folgenden Befehl:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Um einen Blobcontainer in Ihrem Speicherkonto zu erstellen, verwenden Sie den folgenden Befehl:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Dateiupload

Sie können nun Ihren IoT Hub so konfigurieren, dass er die Möglichkeit zum [Hochladen von Dateien auf den IoT Hub](iot-hub-devguide-file-upload.md) unter Verwendung Ihrer Speicherkontodetails bietet.

Die Konfiguration erfordert die folgenden Werte:

* **Speichercontainer** : Ein Blobcontainer in einem Azure-Speicherkonto Ihres aktuellen Azure-Abonnements, der Ihrer IoT Hub-Instanz zugeordnet werden soll. Sie haben die erforderlichen Speicherkontoinformationen im vorherigen Abschnitt abgerufen. IoT Hub generiert automatisch SAS-URIs mit Schreibberechtigungen für diesen Blobcontainer, die Geräte beim Hochladen von Dateien verwenden können.

* **Benachrichtigungen für hochgeladene Dateien empfangen** : Aktivieren oder deaktivieren Sie Benachrichtigungen zum Hochladen von Dateien.

* **SAS-TTL** : Diese Einstellung dient zum Festlegen der Gültigkeitsdauer der SAS-URIs, die von IoT Hub an das Gerät zurückgegeben werden. Standardmäßig auf 1 Stunde festgelegt.

* **Standard-TTL für Dateibenachrichtigungseinstellungen** : Die Gültigkeitsdauer einer Dateiuploadbenachrichtigung (Zeit bis zum Ablauf). Standardmäßig auf 1 Tag festgelegt.

* **Anzahl maximaler Zustellungen für Dateibenachrichtigungen** : Gibt an, wie oft IoT Hub versucht, eine Dateiuploadbenachrichtigung zu senden. Standardmäßig auf 10 festgelegt.

Verwenden Sie die folgenden Azure CLI-Befehle, um die Einstellungen für Dateiuploads in Ihren IoT Hub zu konfigurieren:

<!--Robinsh this is out of date, add cloud powershell -->

Verwenden Sie Folgendes in einer Bash-Shell:

```azurecli
az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} \
  --set properties.enableFileUploadNotifications=true

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Sie können die Konfiguration für Dateiuploads in Ihren IoT Hub mithilfe des folgenden Befehls überprüfen:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Dateiuploadfunktionen von IoT Hub finden Sie unter [Hochladen von Dateien von einem Gerät](iot-hub-devguide-file-upload.md).

Folgen Sie diesen Links, um mehr über das Verwalten von Azure IoT Hub zu erfahren:

* [Massenverwaltung von IoT-Geräten](iot-hub-bulk-identity-mgmt.md)
* [Überwachen von IoT-Hubs](monitor-iot-hub.md)

Weitere Informationen zu den Funktionen von IoT Hub finden Sie unter:

* [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide.md)
* [Bereitstellen von KI auf Edge-Geräten mit Azure IoT Edge](../iot-edge/quickstart-linux.md)
* [Schützen Ihrer IoT-Lösung von Grund auf](../iot-fundamentals/iot-security-ground-up.md)