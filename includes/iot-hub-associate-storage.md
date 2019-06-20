---
title: include file
description: include file
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 8693c48905155ed757bb727e42f4180f36c015f1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178194"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Zuweisen eines Azure Storage-Kontos zu IoT Hub

Da von der simulierten Geräte-App eine Datei in ein Blob hochgeladen wird, müssen Sie über ein [Azure Storage](../articles/storage/common/storage-quickstart-create-account.md)-Konto verfügen, das Ihrem IoT Hub zugeordnet ist. Wenn Sie Azure Storage-Konto einem IoT Hub zuordnen, generiert der IoT Hub einen SAS-URI. Ein Gerät kann diesen SAS-URI für das sichere Hochladen einer Datei zu einem Blobcontainer verwenden. Der IoT Hub-Dienst und die Geräte-SDKs koordinieren den Prozess, der die SAS-URI generiert und für ein Gerät verfügbar macht, sodass er beim Hochladen einer Datei verwendet werden kann.

Folgen Sie den Anleitungen in [Konfigurieren des Dateiuploads im Azure-Portal](../articles/iot-hub/iot-hub-configure-file-upload.md). Stellen Sie sicher, dass Ihrem IoT Hub ein Blobcontainer zugeordnet ist und die Dateibenachrichtigungen aktiviert sind.

![Aktivieren von Dateibenachrichtigungen im Portal](./media/iot-hub-associate-storage/enable-file-notifications.png)