---
title: Architektonische Konzepte in Azure IoT Central | Microsoft-Dokumentation
description: In diesem Artikel werden die wichtigsten Konzepte in Bezug auf die Architektur von Azure IoT Central vorgestellt.
author: dominicbetts
ms.author: dobett
ms.date: 11/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 7b048581b29fa4244c42261810f382b229a627dd
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94985965"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central-Architektur



Dieser Artikel enthält eine Übersicht über die Architektur von Microsoft Azure IoT Central.

![Architektur im Überblick](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Geräte

Geräte tauschen Daten mit Ihrer Azure IoT Central-Anwendung aus. Ein Gerät kann Folgendes durchführen:

- Senden von Messungen wie Telemetrie
- Synchronisieren von Einstellungen mit Ihrer Anwendung

In Azure IoT Central werden die Daten, die ein Gerät mit Ihrer Anwendung austauschen kann, in einer Gerätevorlage angegeben. Weitere Informationen zu Gerätevorlagen finden Sie unter [Metadatenverwaltung](#metadata-management).

Um mehr darüber zu erfahren, wie Geräte eine Verbindung mit Ihrer Azure IoT Central-Anwendung herstellen können, lesen Sie [Gerätekonnektivität](concepts-get-connected.md).

## <a name="azure-iot-edge-devices"></a>Azure IoT Edge-Geräte

Ebenso wie Geräte, die mit den [Azure IoT-SDKs](https://github.com/Azure/azure-iot-sdks) erstellt wurden, können Sie auch [Azure IoT Edge Geräte](../../iot-edge/about-iot-edge.md) mit einer IoT Central-Anwendung verbinden. IoT Edge ermöglicht die Ausführung von Cloud Intelligence und benutzerdefinierter Logik direkt auf IoT-Geräten, die von IoT Central verwaltet werden. Die IoT Edge-Laufzeit ermöglicht Ihnen Folgendes:

- Installieren und Aktualisieren von Workloads auf dem Gerät
- Aufrechterhalten von IoT Edge-Sicherheitsstandards auf dem Gerät
- Sicherstellen, dass die IoT Edge-Module immer ausgeführt werden
- Melden der Modulintegrität an die Cloud für die Remoteüberwachung
- Verwalten der Kommunikation zwischen nachgeschalteten Blattknotengeräten und einem IoT Edge-Gerät, zwischen Modulen auf einem IoT Edge-Gerät sowie zwischen einem IoT Edge-Gerät und der Cloud.

![Azure IoT Central mit Azure IoT Edge](./media/concepts-architecture/iotedge.png)

IoT Central bietet die folgenden Funktionen für IoT Edge-Geräte:

- Gerätevorlagen zum Beschreiben der Funktionen eines IoT Edge-Geräts, wie z.B.:
  - Funktion zum Upload eines Bereitstellungsmanifests, wodurch ein Manifest für eine Geräteflotte verwaltet werden kann
  - Module, die auf dem IoT Edge-Gerät ausgeführt werden
  - Die von jedem Modul gesendeten Telemetriedaten
  - Die von jedem Modul gemeldeten Eigenschaften
  - Die Befehle, auf die jedes Modul reagiert
  - Die Beziehungen zwischen einem IoT Edge-Gatewaygerät und dem nachgeschalteten Gerät
  - Cloudeigenschaften, die auf dem IoT Edge-Gerät nicht gespeichert werden
  - Anpassungen, Dashboards und Formulare, die Teil der IoT Central-Anwendung sind

  Weitere Informationen finden Sie im Artikel [Verbinden eines Azure IoT Edge-Geräts mit einer Azure IoT Central-Anwendung](./concepts-iot-edge.md).

- Die Funktion zum Bereitstellen von IoT Edge-Geräten nach Maß mit dem Azure IoT-Gerätebereitstellungsdienst
- Regeln und Aktionen
- Benutzerdefinierte Dashboards und Analysen
- Fortlaufender Datenexport von Telemetriedaten von IoT Edge-Geräten

### <a name="iot-edge-device-types"></a>IoT Edge-Gerätetypen

IoT Central klassifiziert IoT Edge-Gerätetypen folgendermaßen:

- Blattgeräte. Ein IoT Edge-Gerät kann über nachgeschaltete Blattgeräte verfügen, doch diese Geräte werden in IoT Central nicht bereitgestellt.
- Gatewaygeräte mit nachgeschalteten Geräten. Sowohl das Gatewaygerät als auch nachgeschaltete Geräte werden in IoT Central bereitgestellt.

![Übersicht über IoT Central mit IoT Edge](./media/concepts-architecture/gatewayedge.png)

### <a name="iot-edge-patterns"></a>IoT Edge-Muster

IoT Central unterstützt die folgenden IoT Edge-Gerätemuster:

#### <a name="iot-edge-as-leaf-device"></a>IoT Edge als Blattgerät

![IoT Edge als Blattgerät](./media/concepts-architecture/edgeasleafdevice.png)

Das IoT Edge-Gerät wird in IoT Central bereitgestellt; alle nachgeschalteten Geräte und deren Telemetriedaten werden so dargestellt, als stammten sie vom IoT Edge-Gerät. Nachgeschaltete Geräte, die mit dem IoT Edge-Gerät verbunden sind, werden in IoT Central nicht bereitgestellt.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>IoT Edge-Gatewaygerät, das mit nachgeschalteten Geräten mit Identität verbunden ist

![IoT Edge mit nachgeschalteter Geräteidentität](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

Das IoT Edge-Gerät wird in IoT Central zusammen mit den nachgeschalteten Geräten bereitgestellt, die mit ihm verbunden sind. Runtime-Unterstützung für die Bereitstellung von nachgeschalteten Geräten über das Gateway gibt es derzeit nicht.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>IoT Edge-Gatewaygerät, das mit nachgeschalteten Geräten mit Identität verbunden ist, die vom IoT Edge-Gateway bereitgestellt wird

![IoT Edge mit nachgeschaltetem Gerät ohne Identität](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

Das IoT Edge-Gerät wird in IoT Central zusammen mit den nachgeschalteten Geräten bereitgestellt, die mit ihm verbunden sind. Runtime-Unterstützung für die Bereitstellung von Identitätsdaten für nachgeschaltete Geräte durch ein Gateway und die Bereitstellung nachgeschalteter Geräte gibt es derzeit nicht. Wenn Sie ein eigenes Modul für die Identitätsübersetzung einsetzen, kann IoT Central dieses Muster unterstützen.

## <a name="cloud-gateway"></a>Cloudgateway

Azure IoT Central verwendet Azure IoT Hub als Cloudgateway, das Gerätekonnektivität ermöglicht. IoT Hub ermöglicht Folgendes:

- Datenerfassung im großen Maßstab in der Cloud
- Geräteverwaltung
- Sichere Gerätekonnektivität

Weitere Informationen zu IoT Hub finden Sie unter [Dokumentation zu IoT Hub](../../iot-hub/index.yml).

Weitere Informationen zur Gerätekonnektivität in Azure IoT Central finden Sie unter [Gerätekonnektivität](concepts-get-connected.md).

## <a name="data-stores"></a>Datenspeicher

Azure IoT Central speichert Anwendungsdaten in der Cloud. Zu den gespeicherten Anwendungsdaten zählen Folgende:

- Gerätevorlagen
- Geräteidentitäten
- Gerätemetadaten
- Benutzer- und Rollendaten

Azure IoT Central verwendet einen Zeitreihenspeicher für die Messdaten, die von Ihren Geräten gesendet werden. Die Zeitreihendaten von Geräten werden vom Analysedienst verwendet.

## <a name="analytics"></a>Analytics

Der Analysedienst ist für das Generieren von benutzerdefinierten Berichterstellungsdaten zuständig, die die Anwendung anzeigt. Ein Operator kann die [Analysen anpassen](howto-create-analytics.md), die in der Anwendung angezeigt werden. Der Analysedienst basiert auf [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) und verarbeitet die von Ihren Geräten gesendeten Messdaten.

## <a name="rules-and-actions"></a>Regeln und Aktionen

[Regeln und Aktionen](tutorial-create-telemetry-rules.md) werden zur Automatisierung von Aufgaben in der Anwendung ausgiebig zusammen verwendet. Ein Ersteller kann Regeln basierend auf Gerätetelemetrie definieren, wie etwa Temperaturen, die einen definierten Schwellenwert definieren. Azure IoT Central verwendet einen Datenstromprozessor, um zu bestimmen, wann die Regelbedingungen erfüllt sind. Wenn eine Regelbedingung erfüllt ist, löst diese eine vom Ersteller definierte Aktion aus. Eine Aktion kann z.B. eine E-Mail senden, um einen Techniker darüber zu benachrichtigen, dass die Temperatur in einem Gerät zu hoch ist.

## <a name="metadata-management"></a>Metadatenverwaltung

In einer Azure IoT Central-Anwendung definieren Gerätevorlagen das Verhalten und die Funktion der Gerätetypen. So gibt z.B. die Gerätevorlage eines Kühlschranks die Telemetrie an, die ein Kühlschrank an Ihre Anwendung sendet.

![Vorlagenarchitektur](media/concepts-architecture/template-architecture.png)

In einer IoT Central-[Gerätevorlage](concepts-device-templates.md) gilt Folgendes:

- Ein **Gerätemodell** gibt die Funktionen eines Geräts an, z. B. die vom Gerät gesendeten Telemetriedaten, die Eigenschaften, die den Gerätestatus definieren, und die Befehle, auf die das Gerät reagiert. Gerätefunktionen sind in einer oder mehreren Schnittstellen organisiert.
- **Cloudeigenschaften** geben die Eigenschaften an, die IoT Central für ein Gerät speichert. Diese Eigenschaften werden nur in IoT Central gespeichert und nie an ein Gerät gesendet.
- **Ansichten** geben die Dashboards und Formulare an, die der Generator erstellt, damit der Operator die Geräte überwachen und verwalten kann.
- Durch **Anpassungen** kann der Generator einige der Definitionen im Gerätemodell überschreiben, damit sie für die IoT Central-Anwendung relevanter werden.

Eine Anwendung kann ein oder mehrere simulierte und echte Geräte basierend auf den einzelnen Gerätevorlagen verwenden.

## <a name="data-export"></a>Datenexport

In einer Azure IoT Central-Anwendung können Sie einen [fortlaufenden Export Ihrer Daten](howto-export-data.md) auf Ihre eigenen Azure Event Hubs und Azure Service Bus-Instanzen durchführen. Außerdem können Sie Ihre Daten in regelmäßigen Abständen in Ihr Azure-Blobspeicherkonto exportieren. Mit IoT Central können Messungen, Geräte und Gerätevorlagen exportiert werden.

## <a name="batch-device-updates"></a>Geräteupdates als Batchvorgang

In einer Azure IoT Central-Anwendung können Sie [Aufträge erstellen und ausführen](howto-run-a-job.md), um verbundene Geräte zu verwalten. Mit diesen Aufträgen können Sie Massenaktualisierungen an Geräteeigenschaften oder -einstellungen vornehmen oder Befehle ausführen. Sie können beispielsweise einen Auftrag erstellen, um die Lüfterdrehzahl für mehrere Verkaufsautomaten mit Kühlung zu erhöhen.

## <a name="role-based-access-control-rbac"></a>Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC)

Jede IoT Central-Anwendung verfügt über ein eigenes integriertes RBAC-System. Ein Administrator kann mithilfe einer der vordefinierten Rollen oder durch Erstellen einer benutzerdefinierten Rolle [Zugriffsregeln für eine Azure IoT Central-Anwendung definieren](howto-manage-users-roles.md). Über Rollen wird festgelegt, auf welche Bereiche einer Anwendung ein Benutzer zugreifen und welche Aktionen er ausführen darf.

## <a name="security"></a>Sicherheit

Zu den Sicherheitsfeatures in Azure IoT Central gehören Folgende:

- Während der Übertragung und im Ruhezustand verschlüsselte Daten
- Die von Azure Active Directory oder vom Microsoft-Konto bereitgestellte Authentifizierung. Unterstützung für zweistufige Authentifizierung
- Vollständige Mandantenisolation
- Sicherheit auf Geräteebene

## <a name="ui-shell"></a>UI Shell

Die UI Shell ist eine moderne, reaktionsfähige und browserbasierte HTML5-Anwendung.
Ein Administrator kann die Benutzeroberfläche der Anwendung anpassen, indem er benutzerdefinierte Designs anwendet und die Hilfelinks so ändert, dass sie auf die eigenen benutzerdefinierten Hilferessourcen verweisen. Weitere Informationen zur Anpassung der Benutzeroberfläche finden Sie im Artikel [Anpassen der Azure IoT Central-Benutzeroberfläche](howto-customize-ui.md).

Ein Bediener kann personalisierte Anwendungsdashboards erstellen. Sie können über mehrere Dashboards mit verschiedene Daten verfügen und zwischen diesen wechseln.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mehr über die Architektur von Azure IoT Central erfahren haben, besteht der nächste empfohlene Schritt darin, sich mit der [Gerätekonnektivität](concepts-get-connected.md) in Azure IoT Central vertraut zu machen.