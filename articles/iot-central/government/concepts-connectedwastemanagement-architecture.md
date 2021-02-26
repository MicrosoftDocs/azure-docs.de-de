---
title: Referenzarchitektur für die mit Azure IoT Central erstellte Lösung für vernetzte Abfallwirtschaft | Microsoft-Dokumentation
description: Lernen Sie Konzepte der mit Azure IoT Central erstellten Lösung für vernetzte Abfallwirtschaft kennen.
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 2d6c8c693db42789a965ee896a6d913d6ee20ae0
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831670"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>Überwachung für vernetzte Abfallwirtschaft: Referenzarchitektur 

Eine Lösung für vernetzte Abfallwirtschaft kann mit der **App-Vorlage „Azure IoT Central“** als Ausgangspunkt für IoT-Anwendungen entwickelt werden. Dieser Artikel enthält einen Leitfaden mit einer allgemeinen Referenzarchitektur für die Entwicklung einer Komplettlösung. 

![Architektur für vernetzte Abfallwirtschaft](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)


Konzepte:

1. Geräte und Konnektivität  
1. IoT Central 
1. Erweiterbarkeit und Integrationen
1. Geschäftsanwendungen

Wir werfen nun einen Blick auf Schlüsselkomponenten, die bei einer Lösung zur Überwachung des Wasserverbrauchs in der Regel eine Rolle spielen.

## <a name="devices-and-connectivity"></a>Geräte und Konnektivität 
Geräte im öffentlichen Raum (z. B. Mülltonnen) können per LPWAN (Low-Power Wide Area Network) über einen externen Netzbetreiber vernetzt werden. Für diese Gerätetypen können Sie die [Azure IoT Central-Geräte-Bridge](../core/howto-build-iotc-device-bridge.md) nutzen, um Daten vom Gerät an Ihre IoT-Anwendung in Azure IoT Central zu senden. Alternativ bieten sich Gerätegateways an, die IP-fähig sind und direkt mit IoT Central verbunden werden können.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central ist eine IoT-App-Plattform, mit der Sie Ihre IoT-Lösung schnell in Betrieb nehmen können. Sie können Ihre Lösung mit Branding versehen, anpassen und in Dienste von Drittanbietern integrieren.
Nachdem Sie Ihre intelligenten Wasserwirtschaftsgeräte mit IoT Central verbunden haben, stehen Ihnen hierfür Befehls-, Steuerungs-, Überwachungs- und Alarmfunktionen, eine Benutzeroberfläche mit integrierter rollenbasierter Zugriffssteuerung (RBAC), konfigurierbare Dashboards zum Gewinnen von Erkenntnissen und Erweiterungsoptionen zur Verfügung. 

## <a name="extensibility-and-integrations"></a>Erweiterbarkeit und Integrationen
Sie können Ihre IoT-Anwendung in IoT Central erweitern und haben folgende Optionen:
* Ihre IoT-Daten für erweiterte Analysen transformieren und integrieren, z. B. für das Training von Machine Learning-Modellen durch kontinuierlichen Datenexport aus der IoT Central-Anwendung.
* Automatisieren von Workflows in anderen Systemen, indem in der IoT Central-Anwendung Aktionen über Power Automate oder Webhooks ausgelöst werden
* über IoT Central-APIs programmgesteuert auf Ihre IoT-Anwendung in IoT Central zugreifen

## <a name="business-applications"></a>Geschäftsanwendungen 
Die IoT-Daten können verwendet werden, um eine Vielzahl von Geschäftsanwendungen innerhalb eines Abfallwirtschaftsbetriebs zu betreiben. Um zu erfahren, wie Sie Ihre mit IoT Central vernetzte Abfallwirtschaftsanwendung mit dem Außendienst verbinden können, sehen Sie sich das Tutorial über die [Integration in Dynamics 365 for Field Services](./how-to-configure-connected-field-services.md) an. 

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie, wie Sie eine Azure IoT Central-Anwendung für [vernetzte Abfallwirtschaft](./tutorial-connected-waste-management.md) erstellen.
* Informieren Sie sich über [IoT Central-Vorlagen für Behörden](./overview-iot-central-government.md).
* Weitere Informationen zu IoT Central finden Sie in der [Übersicht über IoT Central](../core/overview-iot-central.md).
