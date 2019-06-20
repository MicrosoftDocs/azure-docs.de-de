---
title: Integrieren von SIM-Daten in die Remoteüberwachungslösung – Azure | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie SIM-Daten von Telefónica in die Remoteüberwachungslösung integriert werden.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: b07e21131d9560a49d99644525835ac5ee3bac9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61442218"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integrieren von SIM-Daten in die Remoteüberwachungslösung

IoT-Geräte verwenden für die Cloudverbindung häufig eine SIM-Karte, um Datenströme von einem beliebigen Ort aus senden zu können. Die Azure IoT-Remoteüberwachungslösung ermöglicht die Integration von Verbindungsdaten, die über IoT verwaltet werden. Dadurch können Betreiber die Integrität des Geräts anhand der von der IoT SIM-Karte bereitgestellten Daten nachverfolgen.

Die Remoteüberwachung bietet eine vorkonfigurierte Integration mit der IoT-Konnektivität von Telefónica, wodurch Kunden die IoT-Konnektivitätsplattform zum Synchronisieren der Konnektivitätsdaten Ihrer Geräte-SIMs für Ihre Lösungen verwenden können. Diese Lösung kann erweitert werden, um andere IoT-Konnektivitätsanbieter über das GitHub-[Repository](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) zu unterstützen.

In diesem Tutorial lernen Sie Folgendes:

* Integrieren von IoT-SIM-Daten von Telefónica in die Remoteüberwachungslösung
* Anzeigen von Echtzeit-Telemetriedaten
* Anzeigen der SIM-Daten

## <a name="telefnica-iot-integration-setup"></a>Einrichten der IoT-Integration von Telefónica

### <a name="prerequisites"></a>Voraussetzungen

Diese zusätzliche Remoteüberwachungsfunktion ist derzeit als Vorschau verfügbar. Gehen Sie wie folgt vor, um Ihre Verbindungsdaten mit der Azure-Remoteüberwachungslösung zu synchronisieren:

1. Füllen Sie auf der [Telefónica-Website](https://iot.telefonica.com/contact) eine Anforderung aus, wählen Sie die Option **Azure Remote Monitoring** (Remoteüberwachung für Azure) aus, und geben Sie zudem Ihre Kontaktdaten ein.
2. Telefónica aktiviert Ihr Konto.
3. Wenn Sie noch kein Telefónica-Kunde sind und die Vorteile von diesem oder einem anderen cloudfähigen Dienst für die IoT-Konnektivität nutzen möchten, besuchen Sie die [Telefónica-Website](https://iot.telefonica.com/), und wählen Sie die Option **Connectivity** (Konnektivität) aus.

### <a name="telefnica-sim-setup"></a>Einrichten der Telefónica-SIM
Die Zuordnung der Geräte-ID von Telefónica-SIM und Azure Twin basiert auf der IoT-Eigenschaft „Alias“ der Telefónica-SIM. 

Wechseln Sie zum [Portal der IoT-Konnektivitätsplattform von Telefónica](https://m2m-movistar-es.telefonica.com/), klicken Sie auf „SIM Inventory“ (SIM-Bestand), wählen Sie Ihre SIM aus, und aktualisieren Sie jeden „Alias“ der SIM mit Ihrer gewünschten Twin-Geräte-ID. Diese Aufgabe kann auch durch Masseneinfügung durchgeführt werden (weiter Informationen hierzu erhalten Sie in den Benutzerhandbüchern für die IoT-Konnektivitätsplattform von Telefónica).

Diese Aufgabe kann auch durch Masseneinfügung durchgeführt werden (weiter Informationen hierzu erhalten Sie in den Benutzerhandbüchern für die IoT-Konnektivitätsplattform von Telefónica)

![Telefónica-Update](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Zum Herstellen der Verbindung zwischen Ihrem Gerät und der Remoteüberwachung können Sie das [Tutorial für C](iot-accelerators-connecting-devices-linux.md) oder das [Tutorial für Node](iot-accelerators-connecting-devices-node.md) verwenden. 

## <a name="view-device-telemetry-and-sim-properties"></a>Anzeigen von Gerätetelemetriedaten und SIM-Eigenschaften

Sobald Ihr Telefónica-Konto ordnungsgemäß konfiguriert und Ihr Gerät verbunden ist, können Sie Gerätedetails und SIM-Daten anzeigen.

Folgende Konnektivitätsparameter werden veröffentlicht:

* ICCID
* IP
* Netzverfügbarkeit
* SIM-Status
* Netzbasierter Standort
* Datenverbrauch

![Dashboard](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich hier einen Überblick über die Integration von SIM-Daten in die Azure IoT-Remoteüberwachungslösung verschafft haben, können Sie sich als Nächstes mit Folgendem beschäftigen:

* [Erkunden der Funktionen der vorkonfigurierten Remoteüberwachungslösung](quickstart-remote-monitoring-deploy.md)
* [Ausführen der erweiterten Überwachung](iot-accelerators-remote-monitoring-monitor.md)
* [Verwalten von Geräten](iot-accelerators-remote-monitoring-manage.md)
* [Beheben von Geräteproblemen](iot-accelerators-remote-monitoring-maintain.md)

