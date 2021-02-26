---
title: 'Tutorial: Verbinden einer generischen Client-App mit Azure IoT Central | Microsoft-Dokumentation'
description: In diesem Tutorial für Geräteentwickler erfahren Sie, wie Sie ein Gerät, auf dem eine C-, C#-, Java-, JavaScript- oder Python-Client-App ausgeführt wird, mit Ihrer Azure IoT Central-Anwendung verbinden. Sie ändern die automatisch generierte Gerätevorlage durch Hinzufügen von Ansichten, über die ein Operator mit einem verbundenen Gerät interagieren kann.
author: dominicbetts
ms.author: dobett
ms.date: 11/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mqtt
- device-developer
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 8f1b5eabe235d107b48dc7b2db5b6d4b1188a3fa
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833965"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application"></a>Tutorial: Erstellen einer Clientanwendung und Verbinden dieser Anwendung mit Ihrer Azure IoT Central-Anwendung

*Dieser Artikel gilt für Lösungsersteller und Geräteentwickler.*

In diesem Tutorial für Geräteentwickler erfahren Sie, wie Sie eine Clientanwendung mit Ihrer Azure IoT Central-Anwendung verbinden. Die Anwendung simuliert das Verhalten eines Thermostats. Wenn die Anwendung eine Verbindung mit IoT Central herstellt, sendet sie die Modell-ID des Thermostatgerätemodells. IoT Central verwendet die Modell-ID, um das Gerätemodell abzurufen und eine Gerätevorlage für Sie zu erstellen. Sie fügen der Gerätevorlage Anpassungen und Ansichten hinzu, um einem Operator die Interaktion mit einem Gerät zu ermöglichen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen und Ausführen des Gerätecodes und Verfolgen der Verbindungsherstellung mit Ihrer IoT Central-Anwendung
> * Zeigen Sie die simulierten Telemetriedaten an, die vom Gerät gesendet werden.
> * Hinzufügen von benutzerdefinierten Ansichten zu einer Gerätevorlage
> * Veröffentlichen der Gerätevorlage
> * Verwalten von Geräteeigenschaften mithilfe einer Ansicht
> * Aufrufen eines Befehls zum Steuern des Geräts

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-central-connect-device-c](../../../includes/iot-central-connect-device-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-central-connect-device-csharp](../../../includes/iot-central-connect-device-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-central-connect-device-java](../../../includes/iot-central-connect-device-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-central-connect-device-nodejs](../../../includes/iot-central-connect-device-nodejs.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-central-connect-device-python](../../../includes/iot-central-connect-device-python.md)]

:::zone-end

## <a name="view-raw-data"></a>Anzeigen von Rohdaten

Als Geräteentwickler können Sie die Ansicht **Rohdaten** verwenden, um die Rohdaten zu untersuchen, die von Ihrem Gerät an IoT Central gesendet werden:

:::image type="content" source="media/tutorial-connect-device/raw-data.png" alt-text="Ansicht „Rohdaten“":::

In dieser Ansicht können Sie die anzuzeigenden Spalten auswählen und einen Zeitbereich zum Anzeigen festlegen. In der Spalte für **Nicht modellierte Daten** werden die Daten des Geräts angezeigt, die keiner der Definitionen für Eigenschaften oder Telemetriedaten in der Gerätevorlage entsprechen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie mit den Tutorials zu IoT Central fortfahren und mehr über das Erstellen einer IoT Central-Lösung erfahren möchten, lesen Sie den folgenden Artikel:

> [!div class="nextstepaction"]
> [Erstellen einer Gatewaygerätevorlage](./tutorial-define-gateway-device-type.md)

Als Geräteentwickler haben Sie hier die Grundlagen zur Erstellung eines Geräts mithilfe von Java kennengelernt. Vorschläge für die nächsten Schritte:

* Unter [Was sind Gerätevorlagen?](./concepts-device-templates.md) erfahren Sie mehr über die Rolle von Gerätevorlagen beim Implementieren Ihres Gerätecodes.
* Weitere Informationen dazu, wie Sie Geräte bei IoT Central registrieren und wie IoT Central Geräteverbindungen schützt, finden Sie unter [Herstellen einer Verbindung mit Azure IoT Central](./concepts-get-connected.md).
* Weitere Informationen zu den Daten, die das Gerät mit IoT Central austauscht, finden Sie unter [Telemetrie-, Eigenschaften- und Befehlsnutzlasten](concepts-telemetry-properties-commands.md).
* Lesen Sie den [Leitfaden für IoT Plug & Play-Geräteentwickler](../../iot-pnp/concepts-developer-guide-device.md).
