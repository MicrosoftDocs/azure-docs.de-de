---
title: Includedatei
description: Includedatei
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 40a5416f15b0e2d66d6ce4b4787573560ee4af00
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66156402"
---
## <a name="create-a-device-identity"></a>Erstellen einer Geräteidentität

In diesem Abschnitt verwenden Sie die Azure-Befehlszeilenschnittstelle zum Erstellen einer Geräteidentität für dieses Tutorial. Die Azure-Befehlszeilenschnittstelle ist in der [Azure Cloud Shell](~/articles/cloud-shell/overview.md) vorinstalliert, Sie können sie aber auch [lokal installieren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Bei Geräte-IDs wird die Groß-/Kleinschreibung beachtet.

1. Führen Sie den folgenden Befehl in der Befehlszeilenumgebung aus, in der Sie die IoT-Erweiterung mit der Azure-Befehlszeilenschnittstelle installieren:

    ```cmd/sh
    az extension add --name azure-cli-iot-ext
    ```

1. Wenn Sie die Azure-Befehlszeilenschnittstelle lokal ausführen, verwenden Sie den folgenden Befehl, um sich bei Ihrem Azure-Konto anzumelden. (Wenn Sie Cloud Shell verwenden, sind Sie automatisch angemeldet und müssen diesen Befehl nicht ausführen.)

    ```cmd/sh
    az login
    ```

1. Erstellen Sie abschließend die neue Geräteidentität `myDeviceId`, und rufen Sie die Verbindungszeichenfolge für das Gerät mit den folgenden Befehlen ab:

    ```cmd/sh
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Notieren Sie sich die Verbindungszeichenfolge für das Gerät aus dem Ergebnis. Diese Geräteverbindungszeichenfolge wird von der Geräte-App verwendet, um die Verbindung mit Ihrem IoT Hub als Gerät herzustellen.

<!-- images and links -->
