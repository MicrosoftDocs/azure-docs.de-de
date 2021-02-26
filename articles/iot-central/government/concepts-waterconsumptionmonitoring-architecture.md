---
title: Referenzarchitektur für eine mit Azure IoT Central erstellte Lösung zur Überwachung des Wasserverbrauchs | Microsoft-Dokumentation
description: Enthält eine Beschreibung der Konzepte einer Lösung zur Überwachung des Wasserverbrauchs, die mit Azure IoT Central erstellt wurde.
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: da1eafee719919d7f3086a059d8d2c70ef8d8cb1
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831653"
---
# <a name="water-consumption-monitoring-reference-architecture"></a>Überwachung des Wasserverbrauchs: Referenzarchitektur 

Die Lösung für die Überwachung des Wasserverbrauchs kann mit der **Azure IoT Central-App-Vorlage** als Einstieg in IoT-Anwendungen erstellt werden. Dieser Artikel enthält einen Leitfaden mit einer allgemeinen Referenzarchitektur für die Entwicklung einer Komplettlösung. 

![Architektur zur Überwachung des Wasserverbrauchs](./media/concepts-waterconsumptionmonitoring-architecture/concepts-waterconsumptionmonitoring-architecture1.png)

Konzepte:

1. Geräte und Konnektivität  
1. IoT Central 
1. Erweiterbarkeit und Integrationen
1. Geschäftsanwendungen

Wir werfen nun einen Blick auf Schlüsselkomponenten, die bei einer Lösung zur Überwachung des Wasserverbrauchs in der Regel eine Rolle spielen.

## <a name="devices-and-connectivity"></a>Geräte und Konnektivität 
In diesem Abschnitt werden Geräte, die für intelligente Lösungen im Bereich der Wasserwirtschaft verwendet werden, z. B. zur Überwachung der Wasserqualität oder des Wasserverbrauchs, einheitlich als „intelligente Wasserwirtschaftsgeräte“ bezeichnet. Bei intelligenten Wasserwirtschaftsgeräten kann es sich um Strömungsmessgeräte, Geräte zur Überwachung der Wasserqualität, intelligente Ventile, Leckdetektoren usw. handeln.

Geräte, die für intelligente Lösungen im Wasserwirtschaftsbereich zum Einsatz kommen, sind im Allgemeinen mithilfe von Low-Power Wide Area Networks (LPWAN) über einen externen Netzbetreiber vernetzt. Für diese Gerätetypen können Sie die [Azure IoT Central-Geräte-Bridge](../core/howto-build-iotc-device-bridge.md) nutzen, um Daten vom Gerät an Ihre IoT-Anwendung in Azure IoT Central zu senden. Alternativ bieten sich Gerätegateways an, die IP-fähig sind und direkt mit IoT Central verbunden werden können.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central ist eine IoT-App-Plattform, mit der Sie Ihre IoT-Lösung schnell in Betrieb nehmen können. Sie können Ihre Lösung mit Branding versehen, anpassen und in Dienste von Drittanbietern integrieren.
Nachdem Sie Ihre intelligenten Wasserwirtschaftsgeräte mit IoT Central verbunden haben, stehen Ihnen hierfür Befehls-, Steuerungs-, Überwachungs- und Alarmfunktionen, eine Benutzeroberfläche mit integrierter rollenbasierter Zugriffssteuerung (RBAC), konfigurierbare Dashboards zum Gewinnen von Erkenntnissen und Erweiterungsoptionen zur Verfügung. 


## <a name="extensibility-and-integrations"></a>Erweiterbarkeit und Integrationen
Sie können Ihre IoT-Anwendung in IoT Central erweitern und haben folgende Optionen:
* Transformieren und Integrieren Ihrer IoT-Daten für erweiterte Analysen, z. B. für das Training von Machine Learning-Modellen durch kontinuierlichen Datenexport aus der IoT Central-Anwendung
* Automatisieren von Workflows in anderen Systemen, indem in der IoT Central-Anwendung Aktionen über Power Automate oder Webhooks ausgelöst werden
* Programmgesteuertes Zugreifen auf Ihre IoT-Anwendung in IoT Central über IoT Central-APIs

## <a name="business-applications"></a>Geschäftsanwendungen 
Die IoT-Daten können verwendet werden, um in einem Wasserwirtschaftsbetrieb eine Vielzahl von Geschäftsanwendungen zu betreiben. Informationen dazu, wie Sie Ihre IoT Central-Anwendung für die Überwachung des Wasserverbrauchs mit dem Außendienst verbinden können, finden Sie im Tutorial über die [Integration in Dynamics 365 for Field Service](./how-to-configure-connected-field-services.md). 


## <a name="next-steps"></a>Nächste Schritte
* Informieren Sie sich über die Erstellung einer IoT Central-Anwendung zur [Überwachung des Wasserverbrauchs](./tutorial-water-consumption-monitoring.md).
* Informieren Sie sich über [IoT Central-Vorlagen für Behörden](./overview-iot-central-government.md).
* Weitere Informationen zu IoT Central finden Sie in der [Übersicht über IoT Central](../core/overview-iot-central.md).
