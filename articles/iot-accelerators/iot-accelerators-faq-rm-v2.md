---
title: Häufig gestellte Fragen zu Solution Accelerators für die Remoteüberwachung – Azure | Microsoft-Dokumentation
description: Häufig gestellte Fragen zu Solution Accelerators für die Remoteüberwachung
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 746d21c7ff4d5b939eea7690193ac07425b4001c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61447909"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Häufig gestellte Fragen zu Solution Accelerators für die Remoteüberwachung

Informationen finden Sie auch in den allgemeinen [häufig gestellten Fragen](iot-accelerators-faq.md).

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Wie hoch sind die Kosten für die Bereitstellung der neuen Remoteüberwachungslösung?

Der neue Solution Accelerator bietet zwei Bereitstellungsoptionen:

* Die Option *Basic* für Entwickler, die niedrigere Entwicklungskosten wünschen, oder für Kunden, die eine Demonstrationsversion oder ein Proof of Concept erstellen möchten.
* Die Option *Standard* für Unternehmen, die eine produktionsbereite Infrastruktur bereitstellen möchten.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Wie kann ich sicherstellen, dass ich die Kosten beim Entwickeln meiner Lösung gering halte?

Neben zwei unterschiedlichen Bereitstellungen verfügt die neue Remoteüberwachungslösung über eine Einstellung zum Aktivieren oder Deaktivieren aller simulierten Geräte nach Bedarf. Durch die Deaktivierung der Simulation werden die in der Lösung erfassten Daten und somit die Gesamtkosten reduziert.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Worin besteht der Unterschied zwischen der Basic- und der Standard-Bereitstellung? Wie treffe ich die Wahl zwischen den beiden Bereitstellungsoptionen?

Jede Bereitstellungsoption entspricht unterschiedlichen Anforderungen. Die Basic-Bereitstellung dient dem Einstieg und der Entwicklung eines Proof of Concept und kleiner Pilotprojekte. Sie bietet eine optimierte Architektur mit den erforderlichen Mindestressourcen und geringen Kosten. Die Standard-Bereitstellung dient dem Erstellen und Anpassen einer produktionsbereiten Lösung und bietet eine Bereitstellung mit allen dafür erforderlichen Elementen. Für Zuverlässigkeit und Skalierbarkeit werden Anwendungsmicroservices als Docker-Container erstellt und über einen Orchestrator bereitgestellt (standardmäßig ist Kubernetes ausgewählt). Der Orchestrator ist für die Bereitstellung, Skalierung und Verwaltung der Anwendung zuständig. Sie sollten eine Option basierend auf Ihren aktuellen Anforderungen auswählen. Je nach Projektphase können Sie jeweils eine der beiden Optionen oder eine Kombination aus beiden verwenden.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Wie konfiguriere ich eine dynamische Zuordnung auf dem Dashboard?

Weitere Informationen finden Sie unter [Aktualisieren des Zuordnungsschlüssels zum Anzeigen von Geräten in einer dynamischen Zuordnung](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Wo finde ich Informationen über die Vorgängerversion der Remoteüberwachungslösung?

Die Vorversion des Solution Accelerators für die Remoteüberwachung war als vorkonfigurierte IoT Suite-Lösung für die Remoteüberwachung bekannt. Sie finden die archivierte Dokumentation unter [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/).

### <a name="next-steps"></a>Nächste Schritte

Sie können auch einige andere Features und Funktionen der IoT-Solution Accelerators ausprobieren:

* [Erkunden der Funktionen des Solution Accelerators für die Remoteüberwachung](quickstart-remote-monitoring-deploy.md)
* [Übersicht über den Solution Accelerator für Predictive Maintenance](iot-accelerators-predictive-overview.md)
* [Bereitstellen des Solution Accelerators für Connected Factory](quickstart-connected-factory-deploy.md)
* [Sicherheit im Internet der Dinge von Anfang an](/azure/iot-fundamentals/iot-security-ground-up)
