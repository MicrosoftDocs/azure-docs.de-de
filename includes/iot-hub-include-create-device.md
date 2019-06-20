---
title: include file
description: include file
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: d70544866b9e321d98acd3978da145276e036025
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178190"
---
<!-- put the ## header in the file that includes this file -->

In diesem Abschnitt erstellen Sie eine Geräteidentität in der Identitätsregistrierung im IoT Hub. Ein Gerät kann nur eine Verbindung mit IoT Hub herstellen, wenn in der Identitätsregistrierung ein Eintrag für dieses Gerät vorhanden ist. Weitere Informationen finden Sie im [IoT Hub-Entwicklerhandbuch](../articles/iot-hub/iot-hub-devguide-identity-registry.md) im Abschnitt zur Identitätsregistrierung. 

1. Öffnen Sie in Ihrem IoT Hub-Navigationsmenü die Option **IoT-Geräte**, und wählen Sie **Hinzufügen** aus, um ein neues Gerät in Ihrem IoT Hub zu registrieren.

    ![Erstellen der Geräteidentität im Portal](./media/iot-hub-include-create-device/create-identity-portal.png)

1. Geben Sie einen Namen für Ihr neues Gerät an, z.B. **myDeviceId**, und wählen Sie **Speichern** aus. Mit dieser Aktion wird eine neue Geräteidentität für Ihren IoT Hub erstellt.

   ![Hinzufügen eines neuen Geräts](./media/iot-hub-include-create-device/create-a-device.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]


1. Öffnen Sie das Gerät nach der Erstellung in der Liste im Bereich **IoT-Geräte**. Kopieren Sie **Verbindungszeichenfolge – Primärschlüssel** zur späteren Verwendung.

    ![Geräte-Verbindungszeichenfolge](./media/iot-hub-include-create-device/device-details.png)

> [!NOTE]
> Die Identitätsregistrierung in IoT Hub speichert nur Geräteidentitäten, um einen sicheren Zugriff auf den IoT-Hub zu ermöglichen. Sie speichert Geräte-IDs und Schlüssel, die als Sicherheitsanmeldeinformationen verwendet werden, sowie ein Aktiviert/Deaktiviert-Kennzeichen, mit dem Sie den Zugriff für ein einzelnes Gerät deaktivieren können. Wenn Ihre Anwendung das Speichern weiterer gerätespezifischer Metadaten erfordert, sollte dafür ein anwendungsspezifischer Speicher verwendet werden. Weitere Informationen finden Sie im [IoT Hub-Entwicklerhandbuch](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
