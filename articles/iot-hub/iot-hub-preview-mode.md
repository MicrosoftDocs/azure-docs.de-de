---
title: Aktivieren des Vorschaumodus für Azure IoT Hub
description: Erfahren Sie, wie Sie den Vorschaumodus für IoT Hub aktivieren, warum Sie es tun sollten, und einige Warnungen.
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: jlian
ms.openlocfilehash: 864870c4392b12477c321c86afd9da848120490c
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621704"
---
# <a name="turn-on-preview-mode-for-iot-hub-to-try-select-new-features"></a>Aktivieren Sie den Vorschaumodus für IoT Hub, um neue Features auszuwählen.

<!-- 
- We are working hard to bring you new features
- Some of these features require a brand new iot hub with preview mode on
- some features may not work at all or have unexpected behavior
- "Normal preview features" do NOT require preview mode 
- Support opt-in at creation time only
- Customer cannot opt back out post creation
- If customer wants to evaluate, they must use new hub dedicated for the preview
- Banners, documentations and all materials indicate preview quality: no GA guarantee at all
-->

Neue Features sind in der öffentlichen Vorschau für IoT Hub verfügbar, einschließlich:

- MQTT 5
- ECC-Serverzertifikat
- Aushandlung der TLS-Fragmentlänge
- Unterstützung der X.509-Zertifikatkette für HTTPS/WebSocket

Da diese Features Verbesserungen der Protokoll- und Authentifizierungsebene von IoT Hub sind, sind sie nur für **neue** IoT Hub-Instanzen verfügbar. Sie sind noch *nicht* für vorhandene IoT Hub-Instanzen verfügbar. Um diese Features in der Vorschau anzuzeigen, muss die IoT Hub-Instanz mit aktiviertem Vorschaumodus erstellt werden.

## <a name="turn-on-preview-mode-for-a-new-iot-hub"></a>Aktivieren des Vorschaumodus für eine neue IoT Hub-Instanz

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie auf der Azure-Startseite die Schaltfläche **+ Ressource erstellen** aus, und geben Sie dann ins Feld **Marketplace durchsuchen** den Begriff *IoT Hub* ein.

1. Wählen Sie in den Suchergebnissen **IoT Hub** und dann **Erstellen** aus.

1. Füllen Sie auf der Registerkarte **Grundlagen** die Felder [wie gewohnt aus](iot-hub-create-through-portal.md), ausgenommen **Region**. Wählen Sie eine dieser Regionen aus:
    
    - USA (Mitte)
    - Europa, Westen
    - Südostasien

1. Wählen Sie die Registerkarte **Verwaltung** aus, und erweitern Sie dann den Abschnitt **Erweiterte Einstellungen**.

1. Wählen Sie neben **Vorschaumodus** die Option **Ein** aus. Lesen Sie den Warnungstext sorgfältig durch.

    :::image type="content" source="media/iot-hub-preview-mode/turn-on-preview-mode-at-create.png" alt-text="Das Bild zeigt, wo die Vorschaumodusoption beim Erstellen einer neuen IoT Hub-Instanz ausgewählt wird":::

1. Klicken Sie auf **Weiter: Überprüfen + erstellen** und dann auf **Erstellen**.

Nach dem Erstellen zeigt eine IoT Hub-Instanz im Vorschaumodus immer dieses Banner an, sodass Sie wissen, dass diese IoT Hub-Instanz nur für Vorschauzwecke verwendet wird: 

:::image type="content" source="media/iot-hub-preview-mode/banner.png" alt-text="Das Bild zeigt das Banner für eine IoT Hub-Instanz im Vorschaumodus":::

## <a name="using-an-iot-hub-in-preview-mode"></a>Verwenden einer IoT Hub-Instanz im Vorschaumodus

Verwenden Sie eine IoT Hub-Instanz im Vorschaumodus *nicht* für die Produktion. Der Vorschaumodus ist *nur* zur Vorschau der oben auf dieser Seite aufgeführten Features vorgesehen. Andere Einschränkungen für eine IoT Hub-Instanz im Vorschaumodus sind:

- Einige vorhandene IoT Hub-Features wie IP-Filter, Private Link, verwaltete Identität, Gerätestreams und Failover funktionieren möglicherweise auf unerwartete Weise oder gar nicht.
- Eine IoT Hub-Instanz im Vorschaumodus kann nicht in eine normale IoT Hub-Instanz geändert oder aktualisiert werden.
- Wir können die normale [IoT Hub-SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/v1_2/) nicht garantieren – darum verwenden Sie die IoT Hub-Instanz im Vorschaumodus nicht für die Produktion.

> [!TIP]
> Der Vorschaumodus ist für [Gerätestreams](iot-hub-device-streams-overview.md) und [verteilte Ablaufverfolgung](iot-hub-distributed-tracing.md) nicht erforderlich. Um diese älteren Vorschaufeatures zu verwenden, beachten Sie wie gewohnt die zugehörige Dokumentation. 

## <a name="next-steps"></a>Nächste Schritte

- Eine Vorschau der MQTT 5-Unterstützung finden Sie unter [Übersicht zur MQTT 5-Unterstützung durch IoT Hub (Vorschau)](iot-hub-mqtt-5.md).
- Informationen zur Vorschau des ECC-Serverzertifikats finden Sie unter [TLS-Zertifikat von ECC-Server (Elliptic Curve Cryptography) (Vorschau)](iot-hub-tls-support.md#elliptic-curve-cryptography-ecc-server-tls-certificate-preview).
- Informationen zur Vorschau der Aushandlung der TLS-Fragmentlänge finden Sie unter [Aushandlung der maximalen TLS-Fragmentlänge (Vorschau)](iot-hub-tls-support.md#tls-maximum-fragment-length-negotiation-preview).