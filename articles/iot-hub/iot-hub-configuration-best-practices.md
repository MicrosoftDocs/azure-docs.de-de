---
title: Bewährte Methoden der Gerätekonfiguration für Azure IoT Hub | Microsoft-Dokumentation
description: Hier erfahren Sie etwas über bewährte Methoden zur Verwendung der automatischen Geräteverwaltung, um wiederkehrende und komplexe Aufgaben im Zusammenhang mit der Verwaltung von IoT-Geräten bedarfsgerecht zu minimieren.
author: robinsh
ms.author: robinsh
ms.date: 06/28/2019
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 8a39c2b06ca8a0f852891acb60ba199fc2c6db5c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024128"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Bewährte Methoden für die Gerätekonfiguration innerhalb einer IoT-Lösung

Mit der automatischen Geräteverwaltung in Azure IoT Hub werden viele häufig wiederkehrende und komplexe Aufgaben im Zusammenhang mit der Verwaltung eines umfangreichen Gerätebestands über den gesamten Lebenszyklus automatisiert. In diesem Artikel werden viele bewährte Methoden für die unterschiedlichen Rollen definiert, die an der Entwicklung und dem Betrieb einer IoT-Lösung beteiligt sind.

* **IoT-Hardwarehersteller/-integrator:** Hersteller von IoT-Hardware, Integratoren, die Hardware von verschiedenen Herstellern zusammenstellen, oder Lieferanten, die Hardware für eine von anderen Lieferanten hergestellte oder integrierte IoT-Bereitstellung bereitstellen. Beteiligt an der Entwicklung und Integration von Firmware, eingebetteten Betriebssystemen und eingebetteter Software.

* **IoT-Lösungsentwickler:** IoT-Lösungen werden normalerweise von einem Lösungsentwickler entwickelt. Dieser Entwickler kann zu einem internen Team oder zu einem auf diese Aktivität spezialisierten Systemintegrator gehören. Der IoT-Lösungsentwickler kann verschiedene Komponenten der IoT-Lösung von Grund auf neu entwickeln, verschiedene Standard- oder Open Source-Komponenten integrieren oder Anpassungen an einem [IoT Solution Accelerator](../iot-accelerators/index.yml) vornehmen.

* **IoT-Lösungsoperator:** Nachdem die IoT-Lösung bereitgestellt wurde, muss sie längerfristig betrieben, überwacht, aktualisiert und gewartet werden. Diese Aufgaben können durch ein internes Team aus IT-Experten, Hardwarebetriebs- und Wartungsteams sowie Fachspezialisten erfolgen, die das richtige Verhalten der gesamten IoT-Infrastruktur überwachen.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Verstehen der automatischen Geräteverwaltung für die bedarfsabhängige Konfiguration von IoT-Geräten

Die automatische Geräteverwaltung enthält die vielen Vorteile von [Gerätezwillingen](iot-hub-devguide-device-twins.md) und [Modulzwillingen](iot-hub-devguide-module-twins.md), damit der gewünschte und gemeldete Status zwischen der Cloud und den Geräten synchronisiert werden kann. Bei [automatischen Gerätekonfigurationen](./iot-hub-automatic-device-management.md) werden große Gruppen von Zwillingen automatisch aktualisiert, und der Fortschritt und die Konformität werden zusammengefasst. Mit den folgenden allgemeinen Schritten wird beschrieben, wie die automatische Geräteverwaltung entwickelt und verwendet wird:

* Der **IoT-Hardwarehersteller/-integrator** implementiert die Features der Geräteverwaltung in einer eingebetteten Anwendung mithilfe von [Gerätezwillingen](iot-hub-devguide-device-twins.md). Diese Features können Firmwareupdates, Softwareinstallation und -update und die Verwaltung von Einstellungen umfassen.

* Der **IoT-Lösungsentwickler** implementiert die Verwaltungsebene für die Vorgänge der Geräteverwaltung mithilfe von [Gerätezwillingen](iot-hub-devguide-device-twins.md) und [automatischen Gerätekonfigurationen](./iot-hub-automatic-device-management.md). Die Lösung sollte auch die Definition einer Operator-Benutzeroberfläche umfassen, auf der Aufgaben der Geräteverwaltung durchgeführt werden können.

* Der **IoT-Lösungsoperator** nutzt die IoT-Lösung zum Durchführen von Aufgaben der Geräteverwaltung, vor allem zum Gruppieren von Geräten, Initiieren von Konfigurationsänderungen, z.B. Firmwareupdates, Überwachen des Status und Beheben von auftretenden Problemen.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT-Hardwarehersteller/-integrator

Im Folgenden sind die bewährten Methoden für Hardwarehersteller und -integratoren aufgeführt, die sich mit der Entwicklung von eingebetteter Software beschäftigen:

* **Implementieren von [Gerätezwillingen](iot-hub-devguide-device-twins.md):** Gerätezwillinge ermöglichen das Synchronisieren der gewünschten Konfiguration über die Cloud und das Melden der aktuellen Konfiguration und der Geräteeigenschaften. Die beste Möglichkeit zum Implementieren von Gerätezwillingen in eingebetteten Anwendungen sind die [Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks). Gerätezwillinge eignen sich aus den folgenden Gründen am besten für die Konfiguration:

    * Sie unterstützen die bidirektionale Kommunikation.
    * Sie ermöglichen sowohl den verbundenen als auch den nicht verbundenen Gerätezustand.
    * Sie basieren auf dem Prinzip der letztlichen Konsistenz.
    * Sie können in der Cloud vollständig abgefragt werden.

* **Strukturieren des Gerätezwillings für die Geräteverwaltung:** Der Gerätezwilling sollte so strukturiert sein, dass die Eigenschaften der Geräteverwaltung auf logische Weise in Abschnitten gruppiert sind. Auf diese Weise können Konfigurationsänderungen isoliert werden, ohne dass andere Abschnitte des Zwillings beeinträchtigt werden. Erstellen Sie beispielsweise einen Abschnitt in den gewünschten Eigenschaften für Firmware, einen weiteren Abschnitt für Software und einen dritten Abschnitt für Netzwerkeinstellungen. 

* **Melden von Geräteattributen, die für die Geräteverwaltung nützlich sind:** Attribute wie Marke und Modell des physischen Geräts, Firmware, Betriebssystem, Seriennummer und andere Bezeichner sind nützlich für die Berichterstellung und als Parameter für Konfigurationsänderungen.

* **Definieren der Hauptzustände für das Melden des Status und des Fortschritts:** Die Status auf oberster Ebene sollten enumeriert werden, damit sie an den Operator gemeldet werden können. Für ein Firmwareupdate kann beispielsweise der Status „Aktuell“, „Wird heruntergeladen“, „Wird angewendet“, „In Bearbeitung“ und „Fehler“ gemeldet werden. Definieren Sie zusätzliche Felder für weitere Informationen zu den einzelnen Status.

## <a name="iot-solution-developer"></a>IoT-Lösungsentwickler

Hier sind die bewährten Methoden für IoT-Lösungsentwickler angegeben, die in Azure angeordnete Systeme erstellen:

* **Implementieren von [Gerätezwillingen](iot-hub-devguide-device-twins.md):** Gerätezwillinge ermöglichen das Synchronisieren der gewünschten Konfiguration über die Cloud und das Melden der aktuellen Konfiguration und der Geräteeigenschaften. Die beste Möglichkeit zum Implementieren von Gerätezwillingen in Cloudanwendungen sind die [Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks). Gerätezwillinge eignen sich aus den folgenden Gründen am besten für die Konfiguration:

    * Sie unterstützen die bidirektionale Kommunikation.
    * Sie ermöglichen sowohl den verbundenen als auch den nicht verbundenen Gerätezustand.
    * Sie basieren auf dem Prinzip der letztlichen Konsistenz.
    * Sie können in der Cloud vollständig abgefragt werden.

* **Organisieren von Geräten mithilfe von Gerätezwillingstags:** Die Lösung sollte es dem Operator ermöglichen, Qualitätsringe oder andere Gerätegruppen basierend auf verschiedenen Bereitstellungsstrategien, z. B. Canary, zu definieren. Die Geräteorganisation kann in Ihrer Lösung mit Gerätezwillingstags und [Abfragen](iot-hub-devguide-query-language.md) implementiert werden. Die Geräteorganisation ist erforderlich, damit Rollouts für Konfigurationen sicher und präzise durchgeführt werden können.

* **Implementieren von [automatischen Gerätekonfigurationen](./iot-hub-automatic-device-management.md):** Bei automatischen Gerätekonfigurationen werden Konfigurationsänderungen für große Gruppen von IoT-Geräten über Gerätezwillinge bereitgestellt und überwacht.

   Automatische Gerätekonfigurationen werden über die **Zielbedingung** auf Gruppen von Gerätezwillingen ausgerichtet. Die Zielbedingung ist eine Abfrage für Gerätezwillingstags oder gemeldete Eigenschaften. Der **Zielinhalt** umfasst eine Gruppe von gewünschten Eigenschaften, die auf den jeweiligen Gerätezwillingen festgelegt werden. Der Zielinhalt sollte an der Gerätezwillingsstruktur ausgerichtet sein, die vom IoT-Hardwarehersteller/-integrator definiert wird. Die **Metriken** sind Abfragen der gemeldeten Eigenschaften von Gerätezwillingen. Sie sollten ebenfalls an der vom IoT-Hardwarehersteller/-integrator definierten Gerätezwillingsstruktur ausgerichtet sein.

   Die automatischen Gerätekonfigurationen werden zum ersten Mal kurz nach Erstellung der Konfiguration ausgeführt und danach in Intervallen von fünf Minuten. Sie bieten außerdem den Vorteil, dass IoT Hub Gerätezwillingsvorgänge immer mit einer Rate durchführt, die die [Drosselungsgrenzwerte](iot-hub-devguide-quotas-throttling.md) für Lesevorgänge und Updates von Gerätezwillingen niemals überschreitet.

* **Verwenden des [Device Provisioning-Diensts](../iot-dps/how-to-manage-enrollments.md):** Lösungsentwickler sollten den Device Provisioning-Dienst verwenden, um neuen Geräten Gerätezwillingstags zuzuweisen. Diese werden dann über **automatische Gerätekonfigurationen**, die auf Zwillinge mit diesem Tag ausgerichtet sind, automatisch konfiguriert. 

## <a name="iot-solution-operator"></a>IoT-Lösungsoperator

Hier sind bewährte Methoden für IoT-Lösungsoperatoren aufgeführt, die eine unter Azure erstellte IoT-Lösung verwenden:

* **Organisieren von Geräten für die Verwaltung:** Die IoT-Lösung sollte die Erstellung von Qualitätsringen oder anderen Gruppen von Geräten basierend auf unterschiedlichen Bereitstellungsstrategien, z. B. Canary, definieren bzw. zulassen. Die Gruppen mit den Geräten werden verwendet, um den Rollout für Konfigurationsänderungen und andere bedarfsabhängige Vorgänge für die Geräteverwaltung durchzuführen.

* **Durchführen von Konfigurationsänderungen mithilfe eines Rollouts in mehreren Phasen:**  Ein Rollout in mehreren Phasen ist ein allgemeiner Prozess, bei dem ein Operator Änderungen für eine größere Gruppe von IoT-Geräten bereitstellt. Hierbei werden Änderungen nach und nach vorgenommen, um das Risiko durch umfangreiche wichtige Änderungen zu reduzieren.    Der Operator sollte die Oberfläche der Lösung verwenden, um eine [automatische Gerätekonfiguration](./iot-hub-automatic-device-management.md) zu erstellen, und die Zielbedingung sollte auf eine anfängliche Gruppe von Geräten ausgerichtet sein (z.B. eine Canary-Gruppe). Der Operator sollte die Konfigurationsänderung dann in der anfänglichen Gruppe von Geräten überprüfen.

   Nachdem die Überprüfung abgeschlossen ist, aktualisiert der Operator die automatische Gerätekonfiguration auf eine größere Gruppe von Geräten. Darüber hinaus sollte der Operator die Priorität für die Konfiguration höher als für andere Konfigurationen festlegen, die derzeit auf diese Geräte ausgerichtet sind. Der Rollout kann über die Metriken überwacht werden, die von der automatischen Gerätekonfiguration gemeldet werden.

* **Durchführen von Rollbacks bei Fehlern oder Fehlkonfigurationen:**  Für eine automatische Gerätekonfiguration, die Fehler oder Fehlkonfigurationen verursacht, kann ein Rollback ausgeführt werden. Hierzu wird die **Zielbedingung** geändert, damit die Geräte die Zielbedingung nicht mehr erfüllen. Stellen Sie sicher, dass eine andere automatische Gerätekonfiguration mit einer geringeren Priorität weiterhin auf diese Geräte ausgerichtet ist. Überprüfen der erfolgreichen Ausführung des Rollbacks, indem die Metriken angezeigt werden: Die Konfiguration, für die der Rollback ausgeführt wurde, sollte den Status für herausgenommene Geräte nicht mehr anzeigen, und die Metriken der zweiten Konfiguration sollten nun die Anzahl für die Geräte enthalten, die der Ausrichtung weiterhin unterliegen.

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich unter [Verstehen und Verwenden von Gerätezwillingen in IoT Hub](iot-hub-devguide-device-twins.md) über das Implementieren von Gerätezwillingen.

* Gehen Sie unter [Bedarfsgerechtes Konfigurieren und Überwachen von IoT-Geräten mit dem Azure-Portal](./iot-hub-automatic-device-management.md) die Schritte zum Erstellen, Aktualisieren oder Löschen einer automatischen Gerätekonfiguration durch.

* Implementieren Sie ein Muster für Firmwareupdates, indem Sie das [Tutorial: Implementieren eines Updateprozesses für die Gerätefirmware](tutorial-firmware-update.md) durcharbeiten und Gerätezwillinge und automatische Gerätekonfigurationen verwenden.