---
title: Generieren einer Geräte-Verbindungszeichenfolge für Azure IoT Central | Microsoft-Dokumentation
description: Wie generiere ich als Entwickler eine Verbindungszeichenfolge für ein Gerät, das eine Verbindung mit einer IoT Central-Anwendung herstellen muss?
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f302cbfa7152ae30be434f560c0c39056d40f9f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60885617"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>Generieren einer Geräte-Verbindungszeichenfolge für die Verbindung mit einer Azure IoT Central-Anwendung

Dieser Artikel beschreibt, wie ein Entwickler eine Verbindungszeichenfolge für ein Gerät generieren kann, das eine Verbindung mit einer IoT Central-Anwendung herstellen muss. Die in diesem Artikel beschriebene Vorgehensweise zeigt Ihnen, wie eine Verbindung für ein einzelnes Gerät über eine Shared Access Signature (SAS) schnell hergestellt wird. Dieser Ansatz ist nützlich, wenn Sie mit IoT Central experimentieren oder Geräte testen. Alternative Ansätze für die Verwendung in einer Produktionsumgebung finden Sie unter [Gerätekonnektivität in Azure IoT Central](concepts-connectivity.md).

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

- Eine Azure IoT Central-Anwendung. Weitere Informationen finden Sie unter [Schnellstart: Erstellen einer Anwendung](quick-deploy-iot-central.md).
- Einen Entwicklungscomputer mit installierter [Node.js](https://nodejs.org/)-Version 8.0.0 oder höher. Sie können `node --version` in der Befehlszeile ausführen, um Ihre Version zu überprüfen. Node.js ist für eine Vielzahl von Betriebssystemen verfügbar.

## <a name="get-connection-information"></a>Abrufen von Verbindungsinformationen

In den folgenden Schritten wird beschrieben, wie Sie die Informationen zum Generieren einer SAS-Verbindungszeichenfolge für ein Gerät abrufen:

1. Suchen Sie im **Device Explorer** das echte Gerät, das eine Verbindung mit Ihrer Anwendung herstellen soll:

    ![Auswählen eines echten Geräts](media/howto-generate-connection-string/real-devices.png)

1. Wählen Sie auf der Seite **Gerät** die Option **Verbinden** aus:

    ![Auswählen von „Verbinden“](media/howto-generate-connection-string/connect.png)

1. Notieren Sie sich die Verbindungsdetails (**Bereichs-ID**, **Geräte-ID** und **Primärschlüssel**), die in den Folgeschritten verwendet werden:

    ![Verbindungsdetails](media/howto-generate-connection-string/device-connect.png)

    Sie können die Werte auf dieser Seite kopieren, um Sie zu speichern.

## <a name="generate-the-connection-string"></a>Generieren der Verbindungszeichenfolge

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun eine Verbindungszeichenfolge für ein echtes Gerät, das eine Verbindung mit Ihrer Azure IoT Central-Anwendung herstellen soll, generiert haben, können Sie mit folgenden Schritten fortfahren:

* [Vorbereiten und Verbinden eines DevKit-Geräts (C)](howto-connect-devkit.md)
* [Vorbereiten und Verbinden eines Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [Vorbereiten und Verbinden eines Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [Vorbereiten und Verbinden eines Windows 10 IoT Core-Geräts (C#)](howto-connect-windowsiotcore.md)
* [Verbinden eines generischen Node.js-Clients mit Ihrer Azure IoT Central-Anwendung](howto-connect-nodejs.md)