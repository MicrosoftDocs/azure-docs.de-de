---
title: Erneutes Bereitstellen von Geräten mit dem Azure IoT Hub Device Provisioning Service
description: Hier erfahren Sie, wie Sie Geräte mit Ihrer Instanz von Hub Device Provisioning Service (DPS) nochmal bereitstellen und warum dies notwendig sein kann.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: e5cc5b557aa4dff793f7e87093eeb65028da4f8c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010944"
---
# <a name="how-to-reprovision-devices"></a>Erneutes Bereitstellen von Geräten

Im Lauf des Lebenszyklus einer IoT-Lösung kommt es häufig vor, dass Geräte von einem IoT-Hub zu einem anderen verlagert werden. Folgende Szenarien können der Grund für eine solche Verlagerung sein:

* **Geolocation**: Wenn ein Gerät von einem Standort zu einem anderen verlagert wird, lässt sich die Netzwerklatenz verbessern, indem das Gerät zu einem jeweils näher gelegenen IoT-Hub migriert wird.

* **Mehrinstanzenfähigkeit**: Ein Gerät wird innerhalb der gleichen IoT-Lösung verwendet werden, jedoch auch einem neuen Kunden oder Kundenstandort zugewiesen oder geleast werden. Die Dienstbereitstellung für diesen neuen Kunden erfolgt möglicherweise über einen anderen IoT-Hub.

* **Änderung einer Lösung**: Ein Gerät wird in eine neue oder aktualisierte IoT-Lösung verlagert. Aufgrund der Neuzuweisung muss das Gerät möglicherweise mit einem neuen IoT-Hub kommunizieren, der mit anderen Back-End-Komponenten verbunden ist. 

* **Quarantäne**: Dieses Szenario ähnelt der Änderung einer Lösung. Ein Gerät, das Fehlfunktionen aufweist, eine Sicherheitslücke darstellt oder veraltet ist, kann einem IoT-Hub zugewiesen werden, an dem es ausschließlich Updates ausführen kann, um die Compliance wiederherzustellen. Sobald das Gerät wieder ordnungsgemäß funktioniert, wird es wieder zu seinem primären Hub migriert.

Eine ausführlichere Übersicht über die erneute Bereitstellung finden Sie unter [IoT Hub Device-Konzepte für die erneute Bereitstellung](concepts-device-reprovision.md).


## <a name="configure-the-enrollment-allocation-policy"></a>Konfigurieren der Registrierungszuordnungsrichtlinie

Die Zuordnungsrichtlinie bestimmt, wie die Geräte, die mit der Registrierung verknüpft sind, nach der erneuten Bereitstellung einem IoT-Hub zugeordnet oder zugewiesen werden.

Mit den folgenden Schritten wird die Zuordnungsrichtlinie für eine Geräteregistrierung konfiguriert:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zur Dienstinstanz für die Gerätebereitstellung.

2. Klicken Sie auf **Registrierungen verwalten**, und klicken Sie auf die Registrierungsgruppe oder eine einzelne Registrierung, die Sie für eine erneute Bereitstellung konfigurieren möchten. 

3. Wählen Sie unter **Auswahl, wie Geräte den Hubs zugewiesen werden sollen** eine der folgenden Zuordnungsrichtlinien:

    * **Niedrigste Latenz**: Diese Richtlinie ordnet Geräte dem verknüpften IoT Hub zu, sodass Gerät und IoT Hub mit der niedrigsten Latenz kommunizieren können. Durch diese Option kann das Gerät je nach Standort mit dem nächstgelegenen IoT-Hub kommunizieren. 
    
    * **Gleichmäßig gewichtete Verteilung**: Diese Richtlinie verteilt Geräte auf die verknüpften IoT-Hubs, basierend auf der Zuordnungsgewichtung, das jedem verknüpften IoT-Hub zugeordnet ist. Diese Richtlinie ermöglicht Ihnen den Lastenausgleich der Geräte für eine Gruppe von verknüpften Hubs basierend auf der für diesen Hub festgelegten Zuordnungsgewichtung. Wenn Sie nur für einen IoT Hub Geräte bereitstellen, wird diese Einstellung empfohlen. Dies ist die Standardeinstellung. 
    
    * **Statische Konfiguration**: Diese Richtlinie erfordert, dass der gewünschte IoT Hub im Registrierungseintrag für ein bereitzustellendes Gerät aufgeführt ist. Diese Richtlinie ermöglicht es Ihnen, einen einzelnen spezifischen IoT-Hub zu bestimmen, dem Sie Geräte zuordnen möchten.

4. Wählen Sie unter **Auswahl der IoT Hubs, denen diese Gruppe zugeordnet werden kann** die verknüpften IoT Hubs, die Sie in Ihre Zuordnungsrichtlinie aufnehmen möchten. Optional können Sie einen neuen verknüpften Iot Hub über die Schaltfläche **Einen neuen IoT Hub verknüpfen** hinzufügen.

    Mit der Zuordnungsrichtlinie **Niedrigste Latenz** werden die von Ihnen ausgewählten Hubs in die Latenzauswertung einbezogen, um den nächsten Hub für die Gerätezuordnung zu bestimmen.

    Mit der Zuordnungsrichtlinie **Gleichmäßig gewichtete Verteilung** werden die Geräte auf der Grundlage ihrer konfigurierten Zuordnungsgewichtung und ihrer aktuellen Gerätelast über die von Ihnen ausgewählten Hubs verteilt.

    Wählen Sie mit der Zuordnungsrichtlinie **Statische Konfiguration** den IoT Hub aus, dem Sie Geräte zuordnen möchten.

4. Klicken Sie auf **Speichern**, oder fahren Sie mit dem nächsten Abschnitt fort, um die Richtlinie für die erneute Bereitstellung festzulegen.

    ![Registrierungszuordnungsrichtlinie auswählen](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>Festlegen der Richtlinie für die erneute Bereitstellung

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zur Dienstinstanz für die Gerätebereitstellung.

2. Klicken Sie auf **Registrierungen verwalten**, und klicken Sie auf die Registrierungsgruppe oder eine einzelne Registrierung, die Sie für eine erneute Bereitstellung konfigurieren möchten.

3. Wählen Sie unter **Auswahl, wie Gerätedaten bei der erneuten Bereitstellung an einen anderen IoT-Hub behandelt werden sollen** eine der folgenden Richtlinien für die erneute Bereitstellung:

    * **Erneut bereitstellen und Daten migrieren**: Diese Richtlinie wird angewendet, wenn Geräte, die dem Registrierungseintrag zugeordnet sind, eine neue Bereitstellungsanforderung senden. Je nach Konfiguration des Registrierungseintrags wird das Gerät möglicherweise einem anderen IoT-Hub zugewiesen. Wenn das Gerät den IoT-Hub wechselt, wird die Geräteregistrierung für den ursprünglichen IoT-Hub entfernt. Alle Gerätezustandsinformationen von diesem ursprünglichen IoT-Hub werden zum neuen IoT-Hub migriert. Während der Migration wird der Gerätestatus als **Wird zugewiesen** gemeldet.

    * **Erneut zuweisen und auf anfängliche Konfiguration zurücksetzen**: Diese Richtlinie wird angewendet, wenn Geräte, die dem Registrierungseintrag zugeordnet sind, eine neue Bereitstellungsanforderung senden. Je nach Konfiguration des Registrierungseintrags wird das Gerät möglicherweise einem anderen IoT-Hub zugewiesen. Wenn das Gerät den IoT-Hub wechselt, wird die Geräteregistrierung für den ursprünglichen IoT-Hub entfernt. Die anfänglichen Konfigurationsdaten, die von der Bereitstellungsdienstinstanz bei der Bereitstellung des Geräts empfangen wurden, werden an den neuen IoT-Hub gesendet. Während der Migration wird der Gerätestatus als **Wird zugewiesen** gemeldet.

4. Klicken Sie auf **Speichern**, um das Gerät basierend auf Ihren Änderungen erneut bereitzustellen.

    ![Screenshot, der die von Ihnen vorgenommenen Änderungen und die Schaltfläche „Speichern“ hervorhebt](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>Senden einer Bereitstellungsanforderung von einem Gerät

Damit Geräte auf der Grundlage der in den vorangegangenen Abschnitten vorgenommenen Konfigurationsänderungen neu bereitgestellt werden können, müssen diese Geräte eine erneute Bereitstellung anfordern. 

Wie oft ein Gerät eine Bereitstellungsanforderung sendet, hängt vom Szenario ab. Es wird jedoch empfohlen, Ihr Gerät so zu programmieren, dass es beim Neustart eine Bereitstellungsanforderung an eine Bereitstellungsdienstinstanz sendet, und eine [-Methode](../iot-hub/iot-hub-devguide-direct-methods.md) unterstützt, mit der die Bereitstellung bei Bedarf manuell ausgelöst werden kann. Die Bereitstellung kann auch durch Festlegen einer [gewünschte Eigenschaft](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example) ausgelöst werden. 

Die in einem Registrierungseintrag befindliche Richtlinie für die erneute Bereitstellung bestimmt, wie die Dienstinstanz für die Gerätebereitstellung diese Bereitstellungsanforderungen verarbeitet, und legt fest, ob Gerätezustandsdaten während der erneuten Bereitstellung migriert werden sollen. Für einzelne Registrierungen und Registrierungsgruppen sind die gleichen Richtlinien verfügbar:

Beispielsweise Code zum Senden von Bereitstellungsanforderungen von einem Gerät während einer Startsequenz. Weitere Informationen finden Sie unter [Automatische Bereitstellung eines simulierten Geräts](quick-create-simulated-device.md).


## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur erneuten Bereitstellung finden Sie unter [IoT Hub Device-Konzepte für die erneute Bereitstellung](concepts-device-reprovision.md). 
- Weitere Informationen zum Aufheben der Bereitstellung finden Sie unter [Aufheben der Bereitstellung von Geräten, die zuvor automatisch bereitgestellt wurden](how-to-unprovision-devices.md). 











