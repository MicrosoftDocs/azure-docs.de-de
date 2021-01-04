---
title: 'Azure IoT Central: Micro-Fulfillment-Center | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie in IoT Central unter Verwendung der entsprechenden Vorlage eine Micro-Fulfillment-Center-Anwendung erstellen.
author: avneet723
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: 9a0d91677770e89f3e249fc699057332cccaa323
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347471"
---
# <a name="micro-fulfillment-center-architecture"></a>Micro-Fulfillment-Center-Architektur

Micro-Fulfillment-Center-Lösungen ermöglichen die digitale Verbindung, Überwachung und Verwaltung sämtlicher Aspekte eines vollständig automatisierten Fulfillment-Centers mit dem Ziel, die Kosten zu senken, indem Sie Downtime beseitigen und gleichzeitig die Sicherheit und die Gesamteffizienz erhöhen. Diese Lösungen können unter Nutzung einer der Anwendungsvorlagen in Azure IoT Central und der unten dargestellten Architektur als Richtschnur erstellt werden.

![Azure IoT Central Store Analytics](./media/architecture/micro-fulfillment-center-architecture-frame.png)

1. Ein Satz von IoT-Sensoren zum Senden von Telemetriedaten an ein Gatewaygerät
2. Gatewaygeräte senden Telemetrie und aggregierte Erkenntnisse an IoT Central
3. Kontinuierlicher Datenexport an den gewünschten Azure-Dienst zur Bearbeitung
4. Daten können im gewünschten Format strukturiert und an einen Speicherdienst gesendet werden
5. Geschäftsanwendungen können Daten abfragen und Erkenntnisse generieren, die Einzelhandelsvorgänge stützen
 
Als Nächstes beschäftigen wir uns mit allgemeinen Schlüsselkomponenten einer Micro-Fulfillment-Center-Lösung.

## <a name="robotic-carriers"></a>Transportroboter

Eine Micro-Fulfillment-Center-Lösung verfügt wahrscheinlich über zahlreiche Transportroboter, die verschiedene Telemetriesignale generieren. Diese Signale können von einem Gatewaygerät erfasst, aggregiert und anschließend an IoT Central gesendet werden, wie auf der linken Seite des Architekturdiagramms zu sehen.  

## <a name="condition-monitoring-sensors"></a>Sensoren zur Zustandsüberwachung

Eine IoT-Lösung beginnt mit einer Reihe von Sensoren, die aussagekräftige Signale aus Ihrem Fulfillment-Center erfassen. Dies wird durch verschiedene Arten von Sensoren ganz am linken Rand des Architekturdiagramms oben wiedergegeben.

## <a name="gateway-devices"></a>Gatewaygeräte

Viele IoT-Sensoren können unformatierte Signale direkt in die Cloud oder in ein Gatewaygerät in ihrer Nähe einspeisen. Das Gatewaygerät führt vor dem Senden von zusammengefassten Einblicken an eine IoT Central-Anwendung eine Datenaggregation auf dem Edge aus. Die Gatewaygeräte sind ggf. außerdem für die Weiterleitung von Befehls- und Steuerungsvorgängen an die Sensorgeräte zuständig. 

## <a name="iot-central-application"></a>IoT Central-Anwendung

Die Azure IoT Central-Anwendung erfasst Daten von verschiedenen IoT-Sensoren, Robotern und Gatewaygeräten innerhalb der Fulfillment-Center-Umgebung und generiert eine Reihe aussagekräftiger Erkenntnisse.

Azure IoT Central bietet darüber hinaus eine maßgeschneiderte Benutzeroberfläche für den Betreiber des Geschäfts, die ihm die Überwachung und Verwaltung der Infrastrukturgeräte aus der Ferne erlaubt.

## <a name="data-transform"></a>Datentransformation
Die Azure IoT Central-Anwendung kann in einer Lösung so konfiguriert werden, dass sie unformatierte oder aggregierte Erkenntnisse an eine Reihe von Azure-PaaS-Diensten (Platform-as-a-Service) exportiert, die die Daten bearbeiten und die Erkenntnisse anreichern können, bevor sie in einer Geschäftsanwendung landen. 

## <a name="business-application"></a>Geschäftsanwendung
Die IoT-Daten können die Basis verschiedener Geschäftsanwendungen bilden, die in einer Einzelhandelsumgebung bereitgestellt sind. Ein Manager oder Mitarbeiter eines Fulfillment-Centers kann diese Anwendungen nutzen, um geschäftliche Erkenntnisse zu visualisieren und in Echtzeit zielgerichtete Maßnahmen zu ergreifen. Informationen zum Erstellen eines Power BI-Echtzeit-Dashboards für Ihr Einzelhandelsteam finden Sie in [diesem Tutorial](./tutorial-in-store-analytics-create-app.md).

## <a name="next-steps"></a>Nächste Schritte
* Führen Sie die ersten Schritte mit der Anwendungsvorlage für [Micro-Fulfillment-Center](https://aka.ms/checkouttemplate) aus. 
* Sehen Sie sich [dieses Tutorial](https://aka.ms/mfc-tutorial) an. Darin erfahren Sie Schritt für Schritt, wie Sie eine Lösung mit der Vorlage für Micro-Fulfillment-Center-Apps erstellen.
