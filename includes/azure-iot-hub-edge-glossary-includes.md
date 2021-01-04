---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 11/09/2018
ms.author: dobett
ms.openlocfilehash: e5b85b31f0aed3a5e8df6bace5bf06c450bf91ff
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026646"
---
## <a name="automatic-device-management-in-azure-iot-hub"></a>Automatische Geräteverwaltung in Azure IoT Hub
Die automatische Geräteverwaltung in Azure IoT Hub automatisiert viele der repetitiven und komplexen Aufgaben im Zusammenhang mit der Verwaltung eines umfangreichen Gerätebestands über den gesamten Lebenszyklus. Mit der automatischen Geräteverwaltung können Sie eine Gruppe von Geräten auf der Grundlage ihrer Eigenschaften als Ziel festlegen, eine gewünschte Konfiguration definieren und IoT Hub die Geräte aktualisieren lassen, sobald sie in den entsprechenden Bereich fallen.  Umfasst [automatische Gerätekonfigurationen](../articles/iot-hub/iot-hub-automatic-device-management.md) und [automatische IoT Edge-Bereitstellungen](../articles/iot-edge/how-to-deploy-at-scale.md).

## <a name="iot-edge"></a>IoT Edge
Azure IoT Edge ermöglicht die cloudgesteuerte Bereitstellung von Azure-Diensten und lösungsspezifischem Code auf lokalen Geräten. IoT Edge-Geräte können Daten anderer Geräte aggregieren und Berechnungen und Analysen durchführen, bevor die Daten an die Cloud gesendet werden. Weitere Informationen finden Sie unter [Azure IoT Edge](../articles/iot-edge/index.yml).

## <a name="iot-edge-agent"></a>IoT Edge-Agent
Die für die Bereitstellung und Überwachung von Modulen zuständige Komponente der IoT Edge-Laufzeit.

## <a name="iot-edge-device"></a>IoT Edge-Gerät
Auf IoT Edge-Geräten ist die IoT Edge-Runtime installiert, und diese Geräte sind in den Gerätedetails als **IoT Edge-Gerät** gekennzeichnet. Weitere Informationen erhalten Sie unter [Schnellstart: Bereitstellen Ihres ersten IoT Edge-Moduls auf einem virtuellen Linux-Gerät](../articles/iot-edge/quickstart-linux.md).

## <a name="iot-edge-automatic-deployment"></a>Automatische IoT Edge-Bereitstellung
Eine automatische IoT Edge-Bereitstellung konfiguriert eine Zielgruppe von IoT Edge-Geräten für die Ausführung einer Gruppe von IoT Edge-Modulen. Jede Bereitstellung stellt kontinuierlich sicher, dass alle Geräte, die der Zielbedingung entsprechen, die angegebene Gruppe von Modulen ausführen. Das gilt auch, wenn neue Geräte erstellt oder geändert werden, um der Zielbedingung zu entsprechen. Jedes IoT Edge-Gerät erhält nur die Bereitstellung mit der höchsten Priorität, deren Zielbedingung es erfüllt. Erfahren Sie mehr zur [automatischen IoT Edge-Bereitstellung](../articles/iot-edge/module-deployment-monitoring.md).

## <a name="iot-edge-deployment-manifest"></a>IoT Edge-Bereitstellungsmanifest
Ein JSON-Dokument mit Informationen, die in mindestens einen Modulzwilling von IoT Edge-Geräten kopiert werden, um eine Gruppe von Modulen, Routen und zugeordneten gewünschten Moduleigenschaften bereitzustellen.

## <a name="iot-edge-gateway-device"></a>IoT Edge-Gatewaygerät
Ein IoT Edge-Gerät mit nachgeschaltetem Gerät. Bei dem nachgeschalteten Gerät kann es sich um ein IoT Edge-Gerät oder um ein IoT Edge-fremdes Gerät handeln.

## <a name="iot-edge-hub"></a>IoT Edge-Hub
Die Komponente der IoT Edge-Laufzeit, die für die Kommunikation zwischen Modulen sowie für die (bei IoT Hub eingehende) Upstreamkommunikation und die (von IoT Hub ausgehende) Downstreamkommunikation zuständig ist. 

## <a name="iot-edge-leaf-device"></a>IoT Edge-Blattknotengerät
Ein IoT Edge-Gerät ohne nachgeschaltetes Gerät. 

## <a name="iot-edge-module"></a>IoT Edge-Modul
Bei einem IoT Edge-Modul handelt es sich um einen Docker-Container, den Sie auf IoT Edge-Geräten bereitstellen können. Es führt eine spezifische Aufgabe aus und erfasst so beispielsweise eine Nachricht von einem Gerät, transformiert eine Nachricht oder sendet eine Nachricht an eine IoT Hub-Instanz. Es kommuniziert mit anderen Modulen und sendet Daten an die IoT Edge-Laufzeit. [Grundlegendes zu den Anforderungen und Tools für die Entwicklung von IoT Edge-Modulen – Vorschau](../articles/iot-edge/module-development.md)

## <a name="iot-edge-module-identity"></a>IoT Edge-Modulidentität
Ein Datensatz in der IoT Hub-Modulidentitätsregistrierung mit Details zur Existenz sowie mit Sicherheitsanmeldeinformationen, die von einem Modul zur Authentifizierung bei einer Edge Hub- oder IoT Hub-Instanz verwendet werden.

## <a name="iot-edge-module-image"></a>IoT Edge-Modulimage
Das Docker-Image, das von der IoT Edge-Laufzeit zum Instanziieren der Modulinstanzen verwendet wird.

## <a name="iot-edge-module-twin"></a>IoT Edge-Modulzwilling
Ein in der IoT Hub-Instanz gespeichertes JSON-Dokument mit den Zustandsinformationen für eine Modulinstanz.

## <a name="iot-edge-priority"></a>IoT Edge-Priorität
Wenn zwei IoT Edge-Bereitstellungen das gleiche Gerät als Ziel haben, wird die Bereitstellung mit der höheren Priorität angewendet. Wenn zwei Bereitstellungen die gleiche Priorität haben, wird die Bereitstellung mit dem späteren Erstellungsdatum angewendet. Weitere Informationen zur Priorität finden Sie [hier](../articles/iot-edge/module-deployment-monitoring.md#priority).

## <a name="iot-edge-runtime"></a>IoT Edge-Laufzeit
Die IoT Edge-Laufzeit enthält alles, was Microsoft für die Installation auf einem IoT Edge-Gerät bereitstellt. Sie enthält den Edge-Agent, den Edge-Hub und den IoT Edge-Sicherheitsdaemon.

## <a name="iot-edge-set-modules-to-a-single-device"></a>Festlegen von IoT Edge-Modulen auf ein einzelnes Gerät
Ein Vorgang, der den Inhalt eines IoT Edge-Manifests auf dem Gerätezwilling eines einzelnen Geräts kopiert. Die zugrunde liegende API ist ein generischer Vorgang vom Typ „Konfiguration anwenden“, der einfach ein IoT Edge-Manifest als Eingabe akzeptiert.

## <a name="iot-edge-target-condition"></a>IoT Edge-Zielbedingung
In einer IoT Edge-Bereitstellung ist die Zielbedingung eine beliebige boolesche Bedingung für Gerätezwillingstags zur Auswahl der Zielgeräte für die Bereitstellung, z. B. **tag.environment = prod**. Die Zielbedingung wird kontinuierlich ausgewertet, um jedes neue Gerät einzubeziehen, das die Anforderungen erfüllt, bzw. Geräte zu entfernen, die die Bedingung nicht mehr erfüllen. Weitere Informationen erhalten Sie unter[Zielbedingung](../articles/iot-edge/module-deployment-monitoring.md#target-condition).