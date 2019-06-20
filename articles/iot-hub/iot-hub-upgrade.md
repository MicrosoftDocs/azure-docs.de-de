---
title: Upgrade für Azure IoT Hub | Microsoft-Dokumentation
description: Ändern Sie den Tarif und die Skalierung für IoT Hub, um weitere Messaging- und Geräteverwaltungsfunktionen zu erhalten.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: 96c3a7b2cfda23f173f4caeff4fb7a92b1ddc438
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61440212"
---
# <a name="how-to-upgrade-your-iot-hub"></a>Gewusst wie: Durchführen eines Upgrades für IoT Hub

Wächst Ihre IoT-Lösung und muss zentral hochskaliert werden, ist Azure IoT Hub zur Stelle. Azure IoT Hub bietet zwei Tarife, nämlich Basic (B) und Standard (S), für Kunden, die verschiedene Anforderungen an Features stellen. In jedem Tarif werden drei Größen (1, 2 und 3) angeboten, die die Anzahl der Nachrichten angeben, die jeden Tag gesendet werden können.

Wenn Sie mehr Geräte haben und weitere Funktionen benötigen, haben Sie drei Möglichkeiten, um IoT Hub hinsichtlich Ihrer Anforderungen anzupassen:

* Fügen Sie Einheiten innerhalb von IoT Hub hinzu. Jede zusätzliche Einheit ermöglicht z.B. in einem IoT Hub im Tarif B1 zusätzlich 400.000 Nachrichten pro Tag.

* Ändern Sie die Größe von IoT Hub. Migrieren Sie beispielsweise vom Tarif B1 zum Tarif B2, um die Anzahl Nachrichten zu erhöhen, die jede Einheit pro Tag unterstützen kann.

* Führen Sie ein Upgrade auf einen höheren Tarif durch. Führen Sie beispielsweise ein Upgrade vom Tarif B1 auf den Tarif S1 durch, um bei gleicher Messagingkapazität Zugriff auf erweiterte Funktionen zu erhalten.

All diese Änderungen können ohne Unterbrechung bestehender Vorgänge vorgenommen werden.

Wenn Sie Ihren IoT Hub herabstufen möchten, können Sie Einheiten entfernen und die Größe von IoT Hub verringern, jedoch können Sie keine Herabstufung in einen niedrigeren Tarif durchführen. Beispielsweise können Sie vom Tarif S2 zum Tarif S1 wechseln, jedoch nicht vom Tarif S2 zum Tarif B1. Pro IoT Hub-Instanz kann nur ein [IoT Hub-Editionstyp](https://azure.microsoft.com/pricing/details/iot-hub/) in einem Tarif ausgewählt werden. Beispielsweise können Sie eine IoT Hub-Instanz mit mehreren S1-Einheiten erstellen- Allerdings ist keine Kombination aus Einheiten verschiedener Editionen möglich, z.B. S1 und B3 oder S1 und S2.

Diese Beispiele sollen veranschaulichen, wie IoT Hub im Zuge von Änderungen in Ihrer Lösung angepasst werden kann. Spezifische Informationen zu den Funktionen in jedem Tarif finden Sie wie immer unter [Azure IoT Hub-Preise](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="upgrade-your-existing-iot-hub"></a>Durchführen eines Upgrades für einen vorhandenen IoT Hub

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu Ihrem IoT Hub.

2. Wählen Sie **Preise und Skalierung** aus.

   ![Preise und Skalierung](./media/iot-hub-upgrade/pricing-scale.png)

3. Wählen Sie zum Ändern des Tarifs für Ihren Hub **Preise und Skalierung** aus. Wählen Sie den neuen Tarif aus, und klicken Sie auf **Auswählen**.

   ![Tarif und Skalierung](./media/iot-hub-upgrade/select-tier.png)

4. Um die Anzahl der Einheiten in Ihrem Hub zu ändern, geben Sie einen neuen Wert unter **IoT Hub-Einheiten** ein.

5. Wählen Sie **Speichern**, um Ihre Änderungen zu speichern.

Ihr IoT Hub ist jetzt angepasst, und Ihre Konfigurationen wurden geändert.

Die Partitionsobergrenze für IoT Hub im Basic-Tarif und im Standardtarif ist 32. Die meisten IoT Hub-Instanzen benötigen nur vier Partitionen. Die Partitionsgrenze wird beim Erstellen von IoT Hub ausgewählt und setzt die Gerät-zu-Cloud-Nachrichten in Relation zur Anzahl von gleichzeitigen Lesern der Nachrichten. Dieser Wert ändert sich nicht, wenn Sie vom Basictarif zum Standardtarif migrieren.

## <a name="next-steps"></a>Nächste Schritte

Einzelheiten finden Sie unter [Wählen des richtigen IoT Hub-Tarifs für Ihre Lösung](iot-hub-scaling.md).