---
title: Erstellen eines IoT-Micro-Agent-Modulzwillings für Defender (Vorschau)
titleSuffix: Azure Defender for IoT
description: Hier erfahren Sie, wie Sie einzelne Modulzwillinge vom Typ „DefenderIotMicroAgent“ für neue Geräte erstellen.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: f14f253960b628f2bf8052e92dbec3c897a205db
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820839"
---
# <a name="create-a-defender-iot-micro-agent-module-twin-preview"></a>Erstellen eines IoT-Micro-Agent-Modulzwillings für Defender (Vorschau)

Sie können einzelne Modulzwillinge vom Typ **DefenderIotMicroAgent** für neue Geräte erstellen. Alternativ können Sie eine Batcherstellung von Modulzwillingen für alle Geräte in einem IoT Hub durchführen. 

## <a name="device-twins"></a>Gerätezwillinge 

Für in Azure erstellte IoT-Lösungen spielen Gerätezwillinge eine wichtige Rolle – sowohl bei der Geräteverwaltung als auch bei der Prozessautomatisierung. 

Defender für IoT lässt sich vollständig in Ihre vorhandene die IoT-Geräteverwaltungsplattform integrieren. Die vollständige Integration ermöglicht die Verwaltung des Sicherheitsstatus Ihres Geräts sowie die Nutzung aller vorhandenen Gerätesteuerungsfunktionen. Die Integration wird erreicht, indem der Mechanismus für IoT Hub-Zwillinge verwendet wird. 

Weitere Informationen zum Konzept von Gerätezwillingen in Azure IoT Hub finden Sie [hier](../iot-hub/iot-hub-devguide-device-twins.md). 

## <a name="security-module-twins"></a>Sicherheitsmodulzwillinge 

Von Defender für IoT wird für jedes Gerät ein Sicherheitsmodulzwilling verwendet. Der Sicherheitsmodulzwilling enthält alle für die Gerätesicherheit relevanten Informationen für jedes einzelne Gerät in Ihrer Lösung. Die Gerätesicherheitseigenschaften werden über einen dedizierten Sicherheitsmodulzwilling konfiguriert, um eine sicherere Kommunikation zu gewährleisten sowie Updates und Wartungsvorgänge zu ermöglichen, die weniger Ressourcen erfordern. 

## <a name="understanding-defenderiotmicroagent-module-twins"></a>Grundlegendes zu Modulzwillingen vom Typ „DefenderIotMicroAgent“ 

Gerätezwillinge spielen eine wichtige Rolle für in Azure erstellte IoT-Lösungen – sowohl bei der Geräteverwaltung als auch bei der Prozessautomatisierung.

Defender für IoT ermöglicht die vollständige Integration Ihrer vorhandenen IoT-Geräteverwaltungsplattform. Auf diese Weise können Sie Ihren Gerätesicherheitsstatus verwalten und die vorhandenen Funktionen für die Gerätesteuerung nutzen. Defender für IoT kann mithilfe des Mechanismus für IoT Hub-Zwillinge integriert werden.  

Weitere Informationen zum allgemeinen Konzept von Modulzwillingen in Azure IoT Hub finden Sie unter  [Verstehen und Verwenden von Modulzwillingen in IoT Hub](../iot-hub/iot-hub-devguide-module-twins.md).

Von Defender für IoT wird der Mechanismus für Modulzwillinge genutzt und für jedes Ihrer Geräte ein Sicherheitsmodulzwilling namens `DefenderIotMicroAgent` verwendet. 

Die Sicherheitsmodulzwillinge müssen für jedes Gerät im Dienst erstellt, konfiguriert und verwendet werden, um die Features von Defender für IoT in vollem Umfang nutzen zu können. 

## <a name="create-defenderiotmicroagent-module-twin"></a>Erstellen eines Modulzwillings vom Typ „DefenderIotMicroAgent“ 

Modulzwillinge vom Typ **DefenderIotMicroAgent** können erstellt werden, indem Sie jeden Modulzwilling manuell bearbeitet, um spezifische Konfigurationen für jedes Gerät einzuschließen. 

So erstellen Sie manuell einen neuen Modellzwilling vom Typ  **DefenderIotMicroAgent** für ein Gerät: 

1. Suchen Sie in Ihrer IoT Hub-Instanz nach dem Gerät, auf dem Sie einen Sicherheitsmodulzwilling erstellen möchten, und wählen Sie es aus. 

1. Wählen Sie  **Modulidentität hinzufügen** aus. 

1. Geben Sie im Feld **Name der Modulidentität** den Namen `DefenderIotMicroAgent` ein. 

1. Wählen Sie **Speichern** aus. 

## <a name="verify-the-creation-of-a-module-twin"></a>Überprüfen der Erstellung eines Modulzwillings 

Überprüfen Sie wie folgt, ob für ein bestimmtes Gerät ein Sicherheitsmodulzwilling vorhanden ist: 

1. Wählen Sie in Ihrer Azure IoT Hub-Instanz im Menü **Explorer** die Option **IoT-Geräte** aus. 

1. Geben Sie die Geräte-ID ein, oder wählen Sie im Feld **Gerät abfragen** eine Option aus, und wählen Sie **Geräte abfragen** aus.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/iot-devices.png" alt-text="Auswählen von „Geräte abfragen“, um eine Liste Ihrer Geräte zu erhalten":::

1. Wählen Sie das Gerät aus, und öffnen Sie die Seite **Gerätedetails**. 

1. Wählen Sie das Menü **Modulidentitäten** aus, und vergewissern Sie sich, dass das Modul **DefenderIotMicroAgent** in der Liste mit den dem Gerät zugeordneten Modulidentitäten enthalten ist.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/device-details-module.png" alt-text="Auswählen von „Modulidentitäten“ auf der Registerkarte":::

## <a name="next-steps"></a>Nächste Schritte 

Im [nächsten Artikel](quickstart-investigate-security-recommendations.md) erfahren Sie, wie Sie Sicherheitsempfehlungen untersuchen.
