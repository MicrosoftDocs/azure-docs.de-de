---
title: Registrieren eines neuen Geräts im Azure-Portal – Azure IoT Edge | Microsoft-Dokumentation
description: Verwenden des Azure-Portals zum Registrieren eines neuen IoT Edge-Geräts und Abrufen der Verbindungszeichenfolge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 16660fbed465cc70f16cde430024f33b8aa4350e
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495357"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Registrieren eines neuen Azure IoT Edge-Geräts über das Azure-Portal

Bevor Sie Ihre IoT-Geräte mit Azure IoT Edge verwenden können, müssen Sie sie bei Ihrem IoT Hub registrieren. Nachdem Sie ein Gerät registriert haben, erhalten Sie eine Verbindungszeichenfolge, die zum Einrichten Ihres Geräts für IoT Edge-Workloads verwendet werden kann.

In diesem Artikel wird gezeigt, wie Sie ein neues IoT Edge-Gerät mithilfe des Azure-Portals registrieren.

## <a name="prerequisites"></a>Voraussetzungen

Ein [IoT-Hub](../iot-hub/iot-hub-create-through-portal.md) in Ihrem Azure-Abonnement im Tarif „Free“ oder „Standard“.

## <a name="create-a-device"></a>Erstellen eines Geräts

Im Azure-Portal werden IoT Edge-Geräte separat von Geräten erstellt und verwaltet, die eine Verbindung mit dem IoT-Hub herstellen, aber nicht mit Edge verwendet werden können.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem IoT Hub.
2. Wählen Sie im Menü **IoT Edge** aus.
3. Wählen Sie **IoT Edge-Gerät hinzufügen** aus.
4. Geben Sie eine beschreibende Geräte-ID an. Verwenden Sie die Standardeinstellungen, um Authentifizierungsschlüssel automatisch zu generieren und das neue Gerät mit Ihrem Hub zu verbinden.
5. Wählen Sie **Speichern** aus.

## <a name="view-all-devices"></a>Anzeigen aller Geräte

Alle auf die Verwendung mit Edge ausgelegten Geräte, die eine Verbindung mit Ihrem IoT-Hub herstellen, sind auf der Seite **IoT Edge** aufgeführt.

## <a name="retrieve-the-connection-string"></a>Abrufen der Verbindungszeichenfolge

Wenn Sie bereit für die Einrichtung Ihres Geräts sind, benötigen Sie die Verbindungszeichenfolge, die Ihr physisches Gerät mit seiner Identität in IoT Hub verknüpft.

1. Klicken Sie im Portal auf der Seite **IoT Edge** in der Liste der IoT Edge-Geräte auf die Geräte-ID.
2. Kopieren Sie den Wert **Verbindungszeichenfolge (primärer Schlüssel)** oder **Verbindungszeichenfolge (sekundärer Schlüssel)** .

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [mit dem Azure-Portal Module auf einem Gerät bereitstellen](how-to-deploy-modules-portal.md).
