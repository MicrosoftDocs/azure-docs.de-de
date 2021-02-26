---
title: Architektur für die kontinuierliche Patientenüberwachung in Azure IoT Central | Microsoft-Dokumentation
description: In diesem Tutorial finden Sie Informationen zu einer Lösungsarchitektur für die kontinuierliche Patientenüberwachung.
author: philmea
ms.author: philmea
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 9a38a14033fe295c36cf8ac17239b0b8e53f75dc
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831177"
---
# <a name="continuous-patient-monitoring-architecture"></a>Architektur für die ständige Überwachung von Patienten

In diesem Artikel wird die Architektur einer Lösung beschrieben, die auf der Grundlage der Anwendungsvorlage für die **kontinuierliche Patientenüberwachung** erstellt wurde:

Lösungen für die kontinuierliche Patientenüberwachung können mithilfe der bereitgestellten App-Vorlage sowie anhand der weiter unten skizzierten Architektur erstellt werden.

:::image type="content" source="media/cpm-architecture.png" alt-text="Architektur für die ständige Überwachung von Patienten":::

## <a name="details"></a>Details

In diesem Abschnitt werden die einzelnen Teile des Architekturdiagramms ausführlicher erläutert:

### <a name="bluetooth-low-energy-ble-medical-devices"></a>Medizinische Geräte mit Bluetooth Low Energy (BLE)

Bei vielen medizinischen Wearables, die in IoT-Lösungen im Gesundheitswesen zum Einsatz kommen, handelt es sich um BLE-Geräte. Diese Geräte können nicht direkt mit der Cloud kommunizieren und benötigen ein Gateway für den Datenaustausch mit Ihrer Cloudlösung. In dieser Architektur wird als Gateway eine Mobiltelefonanwendung verwendet.

### <a name="mobile-phone-gateway"></a>Mobiltelefongateway

Die Hauptfunktion der Mobiltelefonanwendung besteht darin, BLE-Daten von medizinischen Geräten zu erfassen und an IoT Central weiterzugeben. Die App führt Patienten außerdem durch die Geräteeinrichtung und ermöglicht es ihnen, ihre persönlichen Gesundheitsdaten anzuzeigen. Bei anderen Lösungen kann ein Tabletgateway oder ein statisches Gateway in einem Krankenzimmer verwendet werden. Für Android und iOS steht eine Open-Source-Beispielanwendung für Mobilgeräte zur Verfügung, die als Ausgangspunkt für die Anwendungsentwicklung verwendet werden kann. Weitere Informationen zur mobilen IoT Central-App für die kontinuierliche Patientenüberwachung finden Sie [hier](/samples/iot-for-all/iotc-cpm-sample/iotc-cpm-sample/).

### <a name="export-to-azure-api-for-fhirreg"></a>Exportieren nach Azure API for FHIR&reg;

Azure IoT Central ist HIPAA-konform und für HITRUST&reg; zertifiziert. Per [Azure API for FHIR](../../healthcare-apis/overview.md) können Patientengesundheitsdaten auch an andere Dienste gesendet werden. Azure API for FHIR ist eine standardbasierte API für klinische Gesundheitsdaten. Mit dem [Azure IoT-Konnektor für FHIR](../../healthcare-apis/iot-fhir-portal-quickstart.md) können Sie Azure API for FHIR als Ziel für den kontinuierlichen Datenexport über IoT Central verwenden.

### <a name="machine-learning"></a>Machine Learning

Verwenden Sie Machine Learning-Modelle mit Ihren FHIR-Daten, um Erkenntnisse zu gewinnen und Ihr Pflegeteam bei der Entscheidungsfindung zu unterstützen. Weitere Informationen finden Sie in der [Dokumentation zu Azure Machine Learning](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Anbieterdashboard

Erstellen Sie mithilfe der Daten von Azure API for FHIR ein Dashboard für Patientenerkenntnisse, oder integrieren Sie sie direkt in eine elektronische Patientenakte für Pflegeteams. Pflegeteams können das Dashboard verwenden, um Patienten zu unterstützen und frühzeitig Anzeichen für eine Verschlechterung zu erkennen. Weitere Informationen finden Sie unter [Tutorial: Erstellen eines Power BI-Anbieterdashboards](howto-health-data-triage.md).

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes empfiehlt sich das Tutorial [Bereitstellen einer App für die ständige Überwachung von Patienten und exemplarische Vorgehensweise für die zugehörige Vorlage](tutorial-continuous-patient-monitoring.md).