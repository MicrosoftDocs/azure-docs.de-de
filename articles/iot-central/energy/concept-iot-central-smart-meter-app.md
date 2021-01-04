---
title: Architektonische Konzepte in Azure IoT Central – Intelligenter Stromzähler | Microsoft-Dokumentation
description: In diesem Artikel werden die wichtigsten Konzepte im Zusammenhang mit der Architektur der Energie-Anwendungsvorlage von Azure IoT Central vorgestellt.
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: f9167df3ce56116e02addec411d5f720ea366ea0
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516674"
---
# <a name="azure-iot-central---smart-meter-app-architecture"></a>Azure IoT Central – Architektur der Intelligenter Stromzähler-Anwendung

Dieser Artikel bietet eine Übersicht der Architektur der Vorlage der Überwachungs-App für intelligente Stromzähler. Das nachstehende Diagramm zeigt eine häufig verwendete Architektur für die Intelligenter Stromzähler-App in Azure unter Verwendung der IoT Central-Plattform.

> [!div class="mx-imgBorder"]
> ![Architektur für intelligente Messgeräte](media/concept-iot-central-smart-meter/smart-meter-app-architecture.png)

Diese Architektur umfasst die folgenden Komponenten. Für einige Lösungen sind möglicherweise nicht alle der hier aufgeführten Komponenten erforderlich.

## <a name="smart-meters-and-connectivity"></a>Intelligente Stromzähler und Konnektivität 

Ein intelligenter Stromzähler zählt unter den Energieressourcen zu den wichtigsten Geräten. Er zeichnet Daten zum Energieverbrauch auf und kommuniziert sie an Hilfsprogramme zur Überwachung und für andere Anwendungen, wie etwa Abrechnung und Reaktion auf die Nachfrage. Der Stromzähler kann je nach Typ entweder über Gateways oder über andere zwischengeschaltete Geräte oder Systeme (z. B. Edgegeräte und Head-End-Systeme) eine Verbindung mit IoT Central herstellen. Erstellen Sie eine IoT Central-Geräte-Bridge, um Geräte zu verbinden, die nicht direkt verbunden werden können. Bei der IoT Central-Gerätebridge handelt es sich um eine Open-Source-Lösung. Die Details finden Sie [hier](../core/howto-build-iotc-device-bridge.md). 

## <a name="iot-central-platform"></a>IoT Central-Plattform

Azure IoT Central ist eine Plattform, die das Erstellen Ihrer IoT-Lösungen vereinfacht und den Aufwand und die Kosten für die Verwaltung, den Betrieb und die Entwicklung von IoT-Lösungen senkt. Mit IoT Central können Sie Ihre IoT-Ressourcen (Internet of Things) einfach und in jeder Größenordnung verbinden, überwachen und verwalten. Nachdem Sie Ihre intelligenten Stromzähler mit IoT Central verbunden haben, verwendet die App-Vorlage integrierte Features wie Gerätemodelle, Befehle und Dashboards. Die App-Vorlage verwendet außerdem den IoT Central-Speicher für Szenarien mit warmen Pfaden, z. B. Datenüberwachung nahezu in Echtzeit, Analysen, Regeln und Visualisierung. 

## <a name="extensibility-options-to-build-with-iot-central"></a>Erweiterbarkeitsoptionen für die Erstellung mit IoT Central
Die IoT Central-Plattform bietet zwei Erweiterbarkeitsoptionen: Kontinuierlichen Datenexport (Continuous Data Export, CDE) und APIs. Die Kunden und Partner können zwischen diesen Optionen auswählen, um ihre Lösungen an bestimmte Anforderungen anzupassen. Beispielsweise konfigurierte einer unserer Partner CDE mit Azure Data Lake Storage (ADLS). Er verwendet ADLS für die langfristige Datenaufbewahrung und andere Speicherszenarien mit kalten Pfaden, z.B. Batchverarbeitung, Überwachung und Berichterstellung. 

## <a name="next-steps"></a>Nächste Schritte

* Nachdem Sie sich nun mit der Architektur vertraut gemacht haben, [erstellen Sie die Intelligenter Stromzähler-App kostenlos](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring).
* Weitere Informationen zu IoT Central finden Sie in der [Übersicht über IoT Central](../index.yml).
