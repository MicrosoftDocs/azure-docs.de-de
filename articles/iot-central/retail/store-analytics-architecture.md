---
title: Store Analytics-Architektur
description: Erfahren Sie, wie Sie eine In-Store-Analyseanwendung mithilfe der Auschecken-Anwendungsvorlage in IoT Central erstellen
author: avneets
ms.author: avneets
ms.date: 10/13/2019
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: 972ab8eaaf7c0b9fe8beb446d74bc8d2e89bbc55
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832197"
---
# <a name="in-store-analytics-architecture"></a>Architektur der In-Store-Analyse


Lösungen zur In-Store-Analyse ermöglichen Ihnen das Überwachen verschiedener Bedingungen innerhalb der Einzelhandelsumgebung. Diese Lösungen können erstellt werden, indem eine der Anwendungsvorlagen in Azure IoT Central und die unten dargestellte Architektur als Richtschnur genutzt werden.


![Azure IoT Central Store Analytics](./media/architecture/store-analytics-architecture-frame.png)

- Ein Satz von IoT-Sensoren zum Senden von Telemetriedaten an ein Gatewaygerät
- Gatewaygeräte senden Telemetrie und aggregierte Erkenntnisse an IoT Central
- Kontinuierlicher Datenexport an den gewünschten Azure-Dienst zur Bearbeitung
- Daten können im gewünschten Format strukturiert und an einen Speicherdienst gesendet werden
- Geschäftsanwendungen können Daten abfragen und Erkenntnisse generieren, die Einzelhandelsvorgänge stützen
 
Werfen wir einen Blick auf die Schlüsselkomponenten, die bei Lösungen zur In-Store-Analyse in der Regel eine Rolle spielen.

## <a name="condition-monitoring-sensors"></a>Sensoren zur Zustandsüberwachung

Eine IoT-Lösung beginnt mit einer Reihe von Sensoren, die aussagekräftige Signale in einer Einzelhandelsumgebung erfassen. Dies wird durch verschiedene Arten von Sensoren ganz am linken Rand des obigen Architekturdiagramms wiedergespiegelt.

## <a name="gateway-devices"></a>Gatewaygeräte

Viele IoT-Sensoren können unformatierte Signale direkt in die Cloud oder in ein Gatewaygerät in ihrer Nähe einspeisen. Das Gatewaygerät führt vor dem Senden von zusammengefassten Einblicken an eine IoT Central-Anwendung eine Datenaggregation auf dem Edge aus. Die Gatewaygeräte sind ggf. außerdem für die Weiterleitung von Befehls- und Steuerungsvorgängen an die Sensorgeräte zuständig. 

## <a name="iot-central-application"></a>IoT Central-Anwendung

Die Azure IoT Central-Anwendung erfasst Daten von unterschiedlichen Arten von IoT-Sensoren sowie von Gatewaygeräten innerhalb der Einzelhandelsumgebung und generiert eine Reihe aussagekräftiger Erkenntnisse.

Azure IoT Central bietet darüber hinaus eine maßgeschneiderte Benutzeroberfläche für den Betreiber des Geschäfts, die ihm die Überwachung und Verwaltung der Infrastrukturgeräte aus der Ferne erlaubt.

## <a name="data-transform"></a>Datentransformation
Die Azure IoT Central-Anwendung kann in einer Lösung so konfiguriert werden, dass sie unformatierte oder aggregierte Erkenntnisse an eine Reihe von Azure-PaaS-Diensten (Platform-as-a-Service) exportiert, die Datenbearbeitung durchführen und diese Erkenntnisse anreichern, bevor sie in einer Geschäftsanwendung landen. 

## <a name="business-application"></a>Geschäftsanwendung
Die IoT-Daten können die Basis verschiedener Geschäftsanwendungen bilden, die in einer Einzelhandelsumgebung bereitgestellt sind. Ein Manager oder Mitarbeiter eines Einzelhandelsunternehmens kann diese Anwendungen nutzen, um geschäftliche Erkenntnisse zu visualisieren und in Echtzeit zielgerichtete Maßnahmen zu ergreifen. Informationen zum Erstellen eines Power BI-Echtzeit-Dashboards für Ihr Einzelhandelsteam finden Sie in [diesem Tutorial](./tutorial-in-store-analytics-create-app.md).

## <a name="next-steps"></a>Nächste Schritte
* Machen Sie den Einstieg mit den Anwendungsvorlagen [In-Store-Analyse – Auschecken](https://aka.ms/checkouttemplate) und [In-Store-Analyse – Überwachen von Bedingungen](https://aka.ms/conditiontemplate). 
* Arbeiten Sie das [End-to-End-Tutorial](https://aka.ms/storeanalytics-tutorial) durch. Darin wird Schritt für Schritt beschrieben, wie Sie eine Lösung erstellen, indem Sie eine der Anwendungsvorlagen für die In-Store-Analyse nutzen.
