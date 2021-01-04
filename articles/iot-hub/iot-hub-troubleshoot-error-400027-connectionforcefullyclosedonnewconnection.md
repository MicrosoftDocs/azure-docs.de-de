---
title: Problembehandlung – Azure IoT Hub-Fehler „400027 ConnectionForcefullyClosedOnNewConnection“
description: Grundlegendes zum Beheben des Fehlers „400027 ConnectionForcefullyClosedOnNewConnection“
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: e5d1dc345c72d77be6172fb9c3a10eb2f38d186a
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506327"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

In diesem Artikel werden die Ursachen des Fehlers **400027 ConnectionForcefullyClosedOnNewConnection** und die Lösungen dafür beschrieben.

## <a name="symptoms"></a>Symptome

Die Verbindung Ihres Geräts wird unter Verwendung des .NET SDK und des MQTT-Transporttyps mit **Communication_Error** als **ConnectionStatusChangeReason** getrennt.

Ihr Gerät-zu-Cloud-Zwillingsvorgang (z. B. gemeldete Eigenschaften lesen oder patchen) oder der direkte Methodenaufruf schlägt mit dem Fehlercode **400027** fehl.

## <a name="cause"></a>Ursache

Weil von einem anderen Client eine neue Verbindung mit IoT Hub unter Verwendung derselben Anmeldeinformationen erstellt wurde, hat IoT Hub die vorherige Verbindung geschlossen. IoT Hub lässt nicht zu, dass mehr als ein Client eine Verbindung unter Verwendung desselben Satzes von Anmeldeinformationen herstellt.

## <a name="solution"></a>Lösung

Stellen Sie sicher, dass jeder Client eine Verbindung mit IoT Hub unter Verwendung seiner eigenen Identität herstellt.