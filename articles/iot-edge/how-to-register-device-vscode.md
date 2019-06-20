---
title: Registrieren eines neuen Geräts in Visual Studio Code – Azure IoT Edge | Microsoft-Dokumentation
description: Verwenden von Visual Studio Code zum Erstellen eines neuen IoT Edge-Geräts in Ihrem Azure IoT Hub und Abrufen der Verbindungszeichenfolge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c8fce104d48acc3a562599c65eb15cb0a66657b7
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495266"
---
# <a name="register-a-new-azure-iot-edge-device-from-visual-studio-code"></a>Registrieren eines neuen Azure IoT Edge-Geräts über Visual Studio Code

Bevor Sie Ihre IoT-Geräte mit Azure IoT Edge verwenden können, müssen Sie sie bei Ihrem IoT Hub registrieren. Nachdem Sie ein Gerät registriert haben, erhalten Sie eine Verbindungszeichenfolge, die zum Einrichten Ihres Geräts für IoT Edge-Workloads verwendet werden kann.

Dieser Artikel zeigt die Registrierung eines neuen IoT Edge-Geräts mithilfe von Visual Studio Code. Es gibt mehrere Möglichkeiten zum Durchführen der meisten Vorgänge in Visual Studio Code. In diesem Artikel wird der Explorer verwendet, Sie können aber auch die Befehlspalette verwenden, um die Schritte auszuführen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) in Ihrem Azure-Abonnement
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) für Visual Studio Code

## <a name="sign-in-to-access-your-iot-hub"></a>Anmeldung zum Zugreifen auf Ihren IoT Hub

Sie können die Azure IoT-Erweiterungen für Visual Studio Code verwenden, um Vorgänge mit Ihren IoT Hub auszuführen. Damit diese Vorgänge funktionieren, müssen Sie sich bei Ihrem Azure-Konto anmelden und Ihren IoT Hub auswählen.

1. Öffnen Sie in Visual Studio Code die **Explorer**-Ansicht.

1. Erweitern Sie im unteren Bereich des Explorers den Abschnitt **Azure IoT Hub**.

   ![Erweitern des Abschnitts „Azure IoT Hub Devices“ (Azure IoT Hub-Geräte)](./media/how-to-register-device-vscode/azure-iot-hub-devices.png)

1. Klicken Sie auf **...** in der Kopfzeile des Abschnitts **Azure IoT Hub**. Wenn die Ellipse nicht angezeigt wird, zeigen Sie auf den Header, oder klicken Sie darauf.

1. Wählen Sie **Select IoT Hub** (IoT Hub auswählen) aus.

1. Wenn Sie nicht bei Ihrem Azure-Konto angemeldet sind, befolgen Sie die angezeigten Aufforderungen.

1. Wählen Sie Ihr Azure-Abonnement.

1. Wählen Sie Ihren IoT Hub aus.

## <a name="create-a-device"></a>Erstellen eines Geräts

1. Erweitern Sie im Visual Studio Code-Explorer den Abschnitt **Azure IoT Hub Devices** (Azure IoT Hub-Geräte).

1. Klicken Sie auf **...** in der Überschrift des Abschnitts **Azure IoT Hub Devices** (Azure IoT Hub-Geräte). Wenn die Ellipse nicht angezeigt wird, zeigen Sie auf den Header, oder klicken Sie darauf.

1. Wählen Sie **Create IoT Edge Device** (IoT Edge-Gerät erstellen) aus.

1. Geben Sie im geöffneten Textfeld eine ID für Ihr Gerät an.

Auf dem Ausgabebildschirm wird das Ergebnis des Befehls angezeigt. Die Geräteinformationen werden ausgegeben, einschließlich der **deviceID**, die Sie angegeben haben, und der Zeichenfolge **connectionString**, mit der Sie Ihr physisches Gerät mit Ihrem IoT Hub verbinden können.

## <a name="view-all-devices"></a>Anzeigen aller Geräte

Alle Geräte, die Verbindungen mit Ihrem IoT Hub herstellen, werden im Abschnitt **Azure IoT Hub** des Visual Studio Code-Explorers aufgeführt. IoT Edge-Geräte können von Nicht-Edge-Geräten unterschieden werden, da sie ein anderes Symbol aufweisen, und aufgrund der Tatsache, dass die Module **$edgeAgent** und **$edgeHub** auf jedem IoT Edge-Gerät bereitgestellt sind.

   ![Anzeigen aller IoT Edge-Geräte in Ihrem IoT-Hub](./media/how-to-register-device-vscode/view-devices.png)

## <a name="retrieve-the-connection-string"></a>Abrufen der Verbindungszeichenfolge

Wenn Sie bereit für die Einrichtung Ihres Geräts sind, benötigen Sie die Verbindungszeichenfolge, die Ihr physisches Gerät mit seiner Identität in IoT Hub verknüpft.

1. Klicken Sie mit der rechten Maustaste auf die ID des Geräts im Abschnitt **Azure IoT Hub**.

1. Wählen Sie **Copy Device Connection String** (Verbindungszeichenfolge des Geräts kopieren) aus.

   Die Verbindungszeichenfolge wird in die Zwischenablage kopiert.

Sie können auch im Kontextmenü **Get Device Info** (Geräteinformationen abrufen) auswählen, um alle Geräteinformationen einschließlich der Verbindungszeichenfolge im Ausgabefenster anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [mit Visual Studio Code Module auf einem Gerät bereitstellen](how-to-deploy-modules-vscode.md).
