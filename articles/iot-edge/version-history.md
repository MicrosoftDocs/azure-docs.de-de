---
title: IoT Edge-Versionsnavigation und -verlauf – Azure IoT Edge
description: Hier finden Sie Neuerungen bei IoT Edge und Informationen zu neuen Funktionen in den neuesten Versionen.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cdb84f817f63e6401d17f18319e161f4c3477293
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024652"
---
# <a name="azure-iot-edge-versions-and-release-notes"></a>Azure IoT Edge-Versionen und Versionshinweise

Azure IoT Edge ist ein Produkt, das auf dem Open Source-IoT Edge-Projekt auf GitHub aufbaut. Alle neuen Versionen werden im [Azure IoT Edge-Projekt](https://github.com/Azure/azure-iotedge) verfügbar gemacht. Beiträge und Fehlerberichte können im [Open Source-IoT Edge-Projekt](https://github.com/Azure/iotedge) eingereicht werden.

## <a name="documented-versions"></a>Dokumentierte Versionen

Die IoT Edge-Dokumentation auf dieser Website ist für zwei unterschiedliche Versionen des Produkts verfügbar, sodass Sie den Inhalt auswählen können, der für Ihre IoT Edge-Umgebung gilt. Derzeit werden folgende Versionen unterstützt:

* **IoT Edge 1.0.10** deckt alle Funktionen bis zur neuesten allgemein verfügbaren Version ab: [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10).
* **IoT Edge 1.2 (Vorschauversion)** enthält zusätzliche Inhalte für Funktionen, die in der neuesten Vorschauversion enthalten sind: [1.2-rc1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1)
  * Während sich IoT Edge 1.2 in der Vorschauphase befindet, müssen Sie die Release Candidate Versionen installieren. Weitere Informationen finden Sie unter [Offlineinstallation oder Installation einer bestimmten Version](how-to-install-iot-edge.md?tabs=linux#offline-or-specific-version-installation).

## <a name="version-history"></a>Versionsverlauf

Diese Tabelle enthält den aktuellen Versionsverlauf für IoT Edge-Paketreleases und nennt die für jede Version vorgenommenen Aktualisierungen an der Dokumentation.

| Versionshinweise und Ressourcen | type | Date | Highlights |
| ------------------------ | ---- | ---- | ---------- |
| [1.2-rc1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) | Vorschau | November 2020 | [IoT Edge-Geräte hinter Gateways](how-to-connect-downstream-iot-edge-device.md?view=iotedge-2020-11&preserve-view=true)<br>[IoT Edge-MQTT-Broker](how-to-publish-subscribe.md?view=iotedge-2020-11&preserve-view=true) |
| [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10) | Stable | Oktober 2020 | [UploadSupportBundle (direkte Methode)](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)<br>[Hochladen von Laufzeitmetriken](how-to-access-built-in-metrics.md)<br>[Routenpriorität und Gültigkeitsdauer](module-composition.md#priority-and-time-to-live)<br>[Startreihenfolge für Module](module-composition.md#configure-modules)<br>[Manuelle X.509-Bereitstellung](how-to-manual-provision-x509.md) |
| [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) | Stable | März 2020 | [Automatische X.509-Bereitstellung mit DPS](how-to-auto-provision-x509-certs.md)<br>[RestartModule (direkte Methode)](how-to-edgeagent-direct-method.md#restart-module)<br>[Befehl „support-bundle“](troubleshoot.md#gather-debug-information-with-support-bundle-command) |

## <a name="next-steps"></a>Nächste Schritte

* [Alle Azure IoT Edge-Releases anzeigen](https://github.com/Azure/azure-iotedge/releases)
* [Funktionsanfragen im Feedbackforum stellen oder lesen](https://feedback.azure.com/forums/907045-azure-iot-edge)