---
title: Problembehandlung – Azure IoT Hub-Fehler „404104 DeviceConnectionClosedRemotely“
description: Grundlegendes zum Beheben des Fehlers „404104 DeviceConnectionClosedRemotely“
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: 673a76417739fa59a91979cca7c6807a584868f0
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538254"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

In diesem Artikel werden die Ursachen des Fehlers **404104 DeviceConnectionClosedRemotely** und die Lösungen dafür beschrieben.

## <a name="symptoms"></a>Symptome

### <a name="symptom-1"></a>Symptom 1

Die Geräteverbindung wird in regelmäßigen Abständen (beispielsweise alle 65 Minuten) getrennt, und in den IoT Hub-Ressourcenprotokollen wird die Meldung **404104 DeviceConnectionClosedRemotely** angezeigt. Manchmal wird auch **401003 IoTHubUnauthorized** und weniger als eine Minute später ein Ereignis des Typs „Geräteverbindung erfolgreich“ angezeigt.

### <a name="symptom-2"></a>Symptom 2

Die Geräteverbindung wird nach dem Zufallsprinzip getrennt, und in den IoT Hub-Ressourcenprotokollen wird die Meldung **404104 DeviceConnectionClosedRemotely** angezeigt.

### <a name="symptom-3"></a>Symptom 3

Die Verbindung vieler Geräte wird auf einmal getrennt, die [Metrik „Verbundene Geräte“ (connectedDeviceCount)](monitor-iot-hub-reference.md) fällt ab, und in den Azure Monitor-Protokollen treten mehr Meldungen des Typs **404104 DeviceConnectionClosedRemotely** und [interne 500xxx-Fehler](iot-hub-troubleshoot-error-500xxx-internal-errors.md) als üblich auf.

## <a name="causes"></a>Ursachen

### <a name="cause-1"></a>Ursache 1

Das [zum Herstellen einer Verbindung mit IoT Hub verwendete SAS-Token](iot-hub-devguide-security.md#security-tokens) ist abgelaufen. Dies führt dazu, dass IoT Hub die Verbindung mit dem Gerät trennt. Die Verbindung wird wiederhergestellt, wenn das Token vom Gerät aktualisiert wurde. Beispielsweise [läuft das SAS-Token für C SDK standardmäßig stündlich ab](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication). Das kann zu regulären Trennungen der Verbindung führen.

Weitere Informationen finden Sie unter [401003 IoTHubUnauthorized cause](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1) (Ursache für „401003 IoTHubUnauthorized“).

### <a name="cause-2"></a>Ursache 2

Einige Möglichkeiten sind die folgenden:

- Das Gerät hat die zugrunde liegende Netzwerkkonnektivität länger als das [MQTT-Keep-Alive](iot-hub-mqtt-support.md#default-keep-alive-timeout) verloren, was zu einem Remote-Leerlauftimeout führte. Die Einstellung für das MQTT-Keep-Alive kann bei jedem Gerät unterschiedlich sein.

- Das Gerät hat eine Zurücksetzung auf TCP/IP-Ebene gesendet, aber keine Anwendungsebene `MQTT DISCONNECT`. Im Wesentlichen hat das Gerät die zugrunde liegende Socketverbindung plötzlich geschlossen. Manchmal wird dieses Problem durch Fehler in älteren Versionen des Azure IoT SDKs verursacht.

- Die geräteseitige Anwendung ist abgestürzt.

### <a name="cause-3"></a>Ursache 3

Bei IoT Hub ist möglicherweise ein vorübergehendes Problem aufgetreten. Weitere Informationen finden Sie unter [IoT Hub internal server error cause](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause) (IoT Hub – Ursache für internen Serverfehler).

## <a name="solutions"></a>Lösungen

### <a name="solution-1"></a>Lösung 1

Weitere Informationen finden Sie unter [401003 IoTHubUnauthorized – Lösung 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1).

### <a name="solution-2"></a>Lösung 2

- Stellen Sie sicher, dass das Gerät über eine gute Konnektivität zum IoT Hub verfügt, indem Sie [die Verbindung testen](tutorial-connectivity.md). Wenn das Netzwerk unzuverlässig oder zeitweilig ist, raten wir davon ab, den Keep-Alive-Wert zu erhöhen. Dies könnte nämlich dazu führen, dass die Erkennung (z. B. über Azure Monitor-Warnungen) länger dauert. 

- Verwenden Sie die neuesten Versionen der [IoT SDKs](iot-hub-devguide-sdks.md).

### <a name="solution-3"></a>Lösung 3

Weitere Informationen finden Sie unter [solutions to IoT Hub internal server errors](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution) (Lösungen für interne IoT Hub-Serverfehler).

## <a name="next-steps"></a>Nächste Schritte

Wir empfehlen die Verwendung von Azure IoT-Geräte-SDKs, um Verbindungen zuverlässig zu verwalten. Weitere Informationen finden Sie unter [Verwalten von Konnektivität und zuverlässigem Messaging mithilfe von Azure IoT Hub-Geräte-SDKs](iot-hub-reliability-features-in-sdks.md).