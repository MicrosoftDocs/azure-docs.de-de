---
title: Erstellen eines Modulzwillings für Sicherheit
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie einen Modulzwilling für Azure Defender für IoT zur Verwendung mit Azure Defender für IoT erstellen.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/21/2021
ms.author: shhazam
ms.openlocfilehash: 68d137caeed91e9dea2dbf3883929d85c25382aa
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523259"
---
# <a name="create-an-azureiotsecurity-module-twin"></a>Erstellen eines Azure IoT-Sicherheitsmodulzwillings

In dieser Schnellstartanleitung wird beschrieben, wie Sie individuelle _azureiotsecurity_-Modulzwillinge für neue Geräte oder im Batchmodus Modulzwillinge für alle Geräte einer IoT Hub-Instanz erstellen.

## <a name="understanding-azureiotsecurity-module-twins"></a>Grundlegendes zu azureiotsecurity-Modulzwillingen

Für in Azure erstellte IoT-Lösungen spielen Gerätezwillinge eine wichtige Rolle – sowohl bei der Geräteverwaltung als auch bei der Prozessautomatisierung.

Defender für IoT ermöglicht die vollständige Integration in Ihre vorhandene Plattform für die IoT-Geräteverwaltung. Auf diese Weise können Sie Ihren Gerätesicherheitsstatus verwalten und die vorhandenen Funktionen für die Gerätesteuerung nutzen.
Die Integration von Defender für IoT wird mithilfe des Mechanismus für IoT Hub-Zwillinge erreicht.

Weitere Informationen zum allgemeinen Konzept in Bezug auf Modulzwillinge in Azure IoT Hub finden Sie unter [Verstehen und Verwenden von Modulzwillingen in IoT Hub](../iot-hub/iot-hub-devguide-module-twins.md).

Defender für IoT nutzt den Mechanismus für Modulzwillinge und verwendet für jedes Gerät einen Sicherheitsmodulzwilling mit dem Namen _azureiotsecurity_.

Der Sicherheitsmodulzwilling enthält alle Informationen, die für die Gerätesicherheit Ihrer einzelnen Geräte jeweils relevant sind.

Zur umfassenden Nutzung der Features von Defender für IoT müssen diese Sicherheitsmodulzwillinge für jedes Gerät des Diensts erstellt, konfiguriert und verwendet werden.

## <a name="create-azureiotsecurity-module-twin"></a>Erstellen eines azureiotsecurity-Modulzwillings

_azureiotsecurity_-Modulzwillinge können auf zwei Arten erstellt werden:

1. [Modulbatchskript](https://aka.ms/iot-security-github-create-module): Erstellt automatisch einen Modulzwilling für neue Geräte bzw. Geräte ohne Modulzwilling, indem die Standardkonfiguration verwendet wird.
1. Jeder Modulzwilling wird manuell individuell bearbeitet, und auf jedes Gerät werden spezifische Konfigurationen angewendet.

>[!NOTE]
> Beim Verwenden der Batchmethode werden vorhandene azureiotsecurity-Modulzwillinge nicht überschrieben. Wenn NUR die Batchmethode verwendet wird, werden neue Modulzwillinge für Geräte erstellt, die nicht bereits über einen Sicherheitsmodulzwilling verfügen.

Informationen zum Modifizieren oder Ändern der Konfiguration eines vorhandenen Modulzwillings finden Sie unter [Agent-Konfiguration](how-to-agent-configuration.md).

So erstellen Sie manuell einen neuen _azureiotsecurity_-Modellzwilling für ein Gerät:

1. Suchen Sie in Ihrer IoT Hub-Instanz nach dem Gerät, für das Sie einen Sicherheitsmodulzwilling erstellen möchten, und wählen Sie es aus.

1. Wählen Sie Ihr Gerät und dann **Modulidentität hinzufügen** aus.

1. Geben Sie im Feld **Name der Modulidentität** den Namen **azureiotsecurity** ein.

1. Klicken Sie auf **Speichern**.

## <a name="verify-creation-of-a-module-twin"></a>Überprüfen der Erstellung eines Modulzwillings

Überprüfen Sie wie folgt, ob für ein bestimmtes Gerät ein Sicherheitsmodulzwilling vorhanden ist:

1. Wählen Sie auf Ihrer Azure IoT Hub-Instanz im Menü **Explorer** die Option **IoT-Geräte**.

1. Geben Sie die Geräte-ID ein, oder wählen Sie im Feld **Gerät abfragen** eine Option aus, und wählen Sie **Geräte abfragen** aus.

    :::image type="content" source="./media/quickstart/verify-security-module-twin.png" alt-text="Geräte abfragen":::

1. Wählen Sie das Gerät aus, oder doppelklicken Sie darauf, um die Seite „Gerätedetails“ zu öffnen.

1. Wählen Sie das Menü **Modulidentitäten** aus, und bestätigen Sie das Vorhandensein des Moduls **azureiotsecurity** in der Liste mit den Modulidentitäten, die dem Gerät zugeordnet sind.

    :::image type="content" source="./media/quickstart/verify-security-module-twin-3.png" alt-text="Einem Gerät zugeordnete Module":::

Weitere Informationen zum Anpassen der Eigenschaften von Modulzwillingen für Defender für IoT finden Sie unter [Tutorial: Konfigurieren von Sicherheits-Agents](how-to-agent-configuration.md).

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Artikel erfahren Sie, wie Sie Sicherheitsempfehlungen untersuchen:

> [!div class="nextstepaction"]
> [Untersuchen von Sicherheitsempfehlungen](quickstart-investigate-security-recommendations.md)
