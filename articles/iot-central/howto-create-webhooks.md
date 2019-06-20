---
title: Erstellen von Webhooks für Regeln in Azure IoT Central | Microsoft-Dokumentation
description: Erstellen Sie Webhooks in Azure IoT Central, um andere Anwendungen automatisch über ausgelöste Regeln zu informieren.
author: viv-liu
ms.author: viviali
ms.date: 06/16/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: fdeefdc1ec5372d6ac17f0f985ee0c50ce902e56
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165320"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Erstellen von Webhookaktionen für Regeln in Azure IoT Central

*Dieses Thema gilt für Generatoren und Administratoren.*

Webhooks ermöglichen es Ihnen, Ihre IoT Central-App mit anderen Anwendungen und Diensten für Remoteüberwachung und Benachrichtigung zu verbinden. Durch Webhooks werden automatisch andere Anwendungen und Dienste benachrichtigt, die Sie verbinden, wenn eine Regel in Ihrer IoT Central-App ausgelöst wird. Ihre IoT Central-App sendet eine POST-Anforderung an den HTTP-Endpunkt der anderen Anwendung, sobald eine Regel ausgelöst wird. Die Nutzlast enthält Details zu Gerät und Regeltriggern.

## <a name="set-up-the-webhook"></a>Einrichten des Webhooks

In diesem Beispiel stellen Sie mithilfe von Webhooks eine Verbindung mit RequestBin her, damit Sie Benachrichtigungen erhalten, sobald Regeln ausgelöst werden.

1. Öffnen Sie [RequestBin](https://requestbin.net/).

1. Erstellen Sie ein neues RequestBin-Element, und kopieren Sie die **Bin-URL**.

1. Erstellen Sie eine [Telemetrieregel](howto-create-telemetry-rules.md) oder eine [Ereignisregel](howto-create-event-rules.md). Speichern Sie die Regel, und fügen Sie eine neue Aktion hinzu.

    ![Bildschirm für die Webhookerstellung](media/howto-create-webhooks/webhookcreate.png)

1. Wählen Sie die Webhookaktion, geben Sie einen Anzeigenamen an, und fügen Sie die Bin-URL als Rückruf-URL ein.

1. Speichern Sie die Regel.

Wenn jetzt die Regel ausgelöst wird, wird eine neue Anforderung in RequestBin angezeigt.

## <a name="payload"></a>Nutzlast

Wenn eine Regel ausgelöst wird, erfolgt eine HTTP-POST-Anforderung an die Rückruf-URL, die eine JSON-Nutzlast mit den Messungen, dem Gerät, der Regel und den Anwendungsdetails enthält. Für eine Telemetrieregel sieht die Nutzlast folgendermaßen aus:

```json
{
    "id": "ID",
    "timestamp": "date-time",
    "device" : {
        "id":"ID",
        "name":  "Refrigerator1",
        "simulated" : true,
        "deviceId": "deviceID",
        "deviceTemplate":{
            "id": "ID",
            "version":"1.0.0"
        },
        "properties":{
            "device":{
                "firmwareversion":"1.0"
            },
            "cloud":{
                "location":"One Microsoft Way"
            }
        },
        "measurements":{
            "telemetry":{
                "temperature":20,
                "pressure":10
            }
        }

    },
    "rule": {
        "id": "ID",
        "name": "High temperature alert",
        "enabled": true,
        "deviceTemplate": {
            "id":"GUID",
            "version":"1.0.0"
        }
    },
    "application": {
        "id": "ID",
        "name": "Contoso app",
        "subdomain":"contoso-app"
    }
}
```

## <a name="known-limitations"></a>Bekannte Einschränkungen

Derzeit gibt es keine programmatische Möglichkeit, sich über eine API von diesen Webhooks an- oder abzumelden.

Wenn Sie Ideen haben, wie Sie diese Funktion verbessern können, posten Sie Ihre Vorschläge in unserem [Uservoice-Forum](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie Sie Webhooks einrichten und verwenden, empfiehlt es sich, als nächsten Schritt die [Erstellung von Workflows in Microsoft Flow](howto-add-microsoft-flow.md) zu erkunden.
