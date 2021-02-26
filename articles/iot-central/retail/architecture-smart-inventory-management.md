---
title: Architektur für intelligente IoT-Bestandsverwaltung | Microsoft-Dokumentation
description: Eine Architektur der intelligenten IoT-Bestandsverwaltungsvorlage für IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: conceptual
ms.date: 10/20/2019
ms.custom: mqtt
ms.openlocfilehash: d7939193b0dad1ed74dccd0ace0b0f0323b17124
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832639"
---
# <a name="architecture-of-iot-central-smart-inventory-management-application-template"></a>Architektur der IoT Central-Anwendungsvorlage für intelligente Bestandsverwaltung

Partner und Kunden können die App-Vorlage nutzen und die nachstehenden Anweisungen befolgen, um End-to-End-Lösungen für **intelligente Bestandsverwaltung**  zu entwickeln.

> [!div class="mx-imgBorder"]
> ![Intelligente Bestandsverwaltung](./media/concept-smart-inventory-mgmt-architecture/smart-inventory-management-architecture.png)

1. Ein Satz von IoT-Sensoren zum Senden von Telemetriedaten an ein Gatewaygerät
2. Gatewaygeräte senden Telemetrie und aggregierte Erkenntnisse an IoT Central
3. Daten werden zur Bearbeitung an den gewünschten Azure-Dienst geroutet
4. Mithilfe von Azure-Diensten wie ASA oder Azure Functions können Datenströme neu formatiert und an die gewünschten Speicherkonten gesendet werden 
5. Verarbeitete Daten werden in der heißen Speicherebene für Aktionen in Quasi-Echtzeit oder in der kalten Zugriffsebene zur weiteren Erkenntnisverbesserung auf Grundlage von ML oder Batchanalyse gespeichert. 
6. Logik-Apps können verwendet werden, um verschiedene Geschäftsworkflows in Endbenutzer-Geschäftsanwendungen zu unterstützen

## <a name="details"></a>Details
Im folgenden Abschnitt wird jeder Teil der Telemetrieerfassung in der konzeptionellen Architektur aus RFID- (Radio-frequency Identification) und BLE-Transpondern (Bluetooth Low Energy) umrissen.

## <a name="rfid-tags"></a>RFID-Tags
RFID-Transponder übertragen Daten zu einem Element durch Funkwellen. RFID-Transponder weisen, sofern nicht anders angegeben, in der Regel keinen Akku auf. Transponder empfangen Energie aus den Funkwellen, die vom Leser generiert werden und übertragen ein Signal zurück zum RFID-Leser.

## <a name="ble-tags"></a>BLE-Transponder
Energiefunkfeuer senden Datenpakete in regelmäßigen Abständen. Die Funkfeuerdaten werden von BLE-Lesern oder auf Smartphone installierten Diensten erkannt, die sie dann an die Cloud übertragen.

## <a name="rfid--ble-readers"></a>RFID- und BLE-Leser
Der RFID-Leser konvertiert die Funkwellen in eine besser verwendbare Datenform. Die von den Transpondern gesammelten Informationen werden dann auf einem lokalen Edgeserver gespeichert oder mithilfe von JSON-RPC 2.0 über MQTT an die Cloud gesendet.
BLE-Leser, die auch als Zugriffspunkte (Access Points, AP) bezeichnet werden, sind RFID-Lesern ähnlich. Diese werden verwendet, um Bluetooth-Signale in der Nähe zu erkennen und ihre Nachricht mithilfe von JSON-RPC 2.0 über MQTT an eine lokale Azure IoT Edge-Instanz oder die Cloud weiterzugeben.
Viele Leser sind imstande, RFID- und Beacon-Signale zu lesen sowie weitere Sensorfunktionen bereitzustellen, etwa für Temperatur, Feuchtigkeit, Beschleunigungsmesser und Drehrate.

## <a name="azure-iot-edge-gateway"></a>Azure IoT Edge-Gateway
Azure IoT Edge bietet einen Ort für die lokale Vorverarbeitung dieser Daten, bevor sie in die Cloud gesendet werden. Ferner können künstliche Intelligenz aus Cloudworkloads, Azure und Diensten von Drittanbietern sowie Geschäftslogik mithilfe von Standardcontainern bereitgestellt werden.

## <a name="device-management-with-iot-central"></a>Geräteverwaltung mit IoT Central 
Azure IoT Central ist eine Plattform für die Lösungsentwicklung, die die Konnektivität, Konfiguration und Verwaltung von IoT-Geräten vereinfacht. Die Plattform verringert Aufwand und Kosten von Entwicklungen für IoT-Geräteverwaltung, -betrieb und verwandte Entwicklungen erheblich. Kunden und Partner können eine End-to-End-Enterprise-Lösung aufbauen, um eine digitale Feedbackschleife in der Bestandsverwaltung zu verwirklichen.

## <a name="business-insights--actions-using-data-egress"></a>Geschäftliche Erkenntnisse und Aktionen über ausgehende Daten 
Die IoT Central-Plattform bietet umfassende Optionen zur Erweiterbarkeit über CDE (Continuous Data Export) und APIs. Geschäftserkenntnisse, die auf der Verarbeitung von Telemetriedaten oder auf unverarbeiteter Telemetrie basieren, werden normalerweise in eine bevorzugte Branchenanwendung exportiert. Dies kann per Webhook, Service Bus, Event Hub oder Blobspeicher erreicht werden, um Machine Learning-Modelle zu erstellen, zu trainieren, bereitzustellen und weitere Erkenntnisse zu gewinnen.

## <a name="next-steps"></a>Nächste Schritte
* Informieren Sie sich über die Bereitstellung der [Vorlage für die intelligente Bestandsverwaltung](./tutorial-iot-central-smart-inventory-management.md).
* Weitere Informationen zu [IoT Central-Einzelhandelsvorlagen](./overview-iot-central-retail.md)
* Lesen Sie die [Übersicht über IoT Central](../core/overview-iot-central.md).
