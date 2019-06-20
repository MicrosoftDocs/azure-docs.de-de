---
title: Übersicht über den Solution Accelerator für Predictive Maintenance – Azure | Microsoft-Dokumentation
description: Eine Übersicht für den Azure IoT-Solution Accelerator für Predictive Maintenance.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3387996dc0e1953eaafee9c4c61eb8faa865b654
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61447543"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>Übersicht über den Solution Accelerator für Predictive Maintenance

Der Solution Accelerator für Predictive Maintenance ist eine End-to-End-Lösung für ein Geschäftsszenario, mit der der Zeitpunkt prognostiziert wird, zu dem voraussichtlich ein Fehler auftritt. Sie können diesen Solution Accelerator beispielsweise zur Optimierung von Wartungsroutinen nutzen. In dieser Lösung werden zentrale Azure IoT-Solution Accelerator-Dienste wie IoT Hub und ein [Azure Machine Learning][lnk-machine-learning]-Arbeitsbereich kombiniert. Der Arbeitsbereich enthält ein Modell zum Vorhersagen der Restlebensdauer (Remaining Useful Life, RUL) eines Flugzeugtriebwerks auf der Grundlage eines öffentlichen Datasets mit Beispielwerten. Bei der Lösung wird das IoT-Geschäftsszenario vollständig als Ausgangspunkt implementiert, damit Sie eine Lösung planen und implementieren können, die Ihre besonderen Geschäftsanforderungen erfüllt.

Der zum Solution Accelerator Predictive Maintenance gehörige [Code ist auf GitHub verfügbar](https://github.com/Azure/azure-iot-predictive-maintenance).

## <a name="logical-architecture"></a>Logische Architektur

Im folgenden Diagramm werden die logischen Komponenten des Solution Accelerators beschrieben:

![Logische Architektur][img-architecture]

Die blauen Elemente sind Azure-Dienste, die in der Region bereitgestellt werden, in der Sie den Solution Accelerator bereitgestellt haben. Die Liste mit den Regionen, in denen Sie den Solution Accelerator bereitstellen können, wird auf der [Bereitstellungsseite][lnk-azureiotsolutions] angezeigt.

Das grüne Element ist ein simuliertes Flugzeugtriebwerk. Der Abschnitt [Simulierte Geräte](#simulated-devices) enthält weitere Informationen zu diesen simulierten Geräten.

Die grauen Elemente sind Komponenten, mit denen Funktionen für die *Geräteverwaltung* implementiert werden. Im aktuellen Release des Solution Accelerators für Predictive Maintenance werden diese Ressourcen nicht bereitgestellt. Weitere Informationen zur Geräteverwaltung finden Sie unter [Erkunden der Funktionen des Solution Accelerators für die Remoteüberwachung][lnk-remote-monitoring].

## <a name="azure-resources"></a>Azure-Ressourcen

Navigieren Sie im Azure-Portal zur Ressourcengruppe mit dem von Ihnen gewählten Lösungsnamen, um Ihre bereitgestellten Ressourcen anzuzeigen.

![Accelerator-Ressourcen][img-resource-group]

Beim Bereitstellen des Solution Accelerators erhalten Sie eine E-Mail mit einem Link zum Machine Learning-Arbeitsbereich. Sie können auch über die Seite [Microsoft Azure IoT Solution Accelerators][lnk-azureiotsolutions] zum Machine Learning-Arbeitsbereich navigieren. Wenn sich die Lösung im Zustand **Bereit** befindet, steht auf der Seite eine Kachel zur Verfügung.

![Machine Learning-Modell][img-machine-learning]

## <a name="simulated-devices"></a>Simulierte Geräte

Im Solution Accelerator steht ein simuliertes Gerät für ein Flugzeugtriebwerk. Die Lösung wird mit zwei Triebwerken bereitgestellt, die einem Flugzeug zugeordnet sind. Jedes Triebwerk liefert vier Arten von Telemetriedaten: Sensor 9, Sensor 11, Sensor 14 und Sensor 15. Auf Grundlage dieser Daten berechnet das Machine Learning-Modell die Restlebensdauer des Triebwerks. Jedes simulierte Gerät sendet die folgenden Telemetriemeldungen an IoT Hub:

*Zyklusanzahl*. Ein Zyklus ist ein absolvierter Flug mit einer Dauer von zwei bis zehn Stunden. Während des Flugs werden jede halbe Stunde Telemetriedaten erfasst.

*Telemetrie*. Es sind vier Sensoren vorhanden, die Triebwerkattribute aufzeichnen. Die Sensoren tragen die generischen Bezeichnungen Sensor 9, Sensor 11, Sensor 14 und Sensor 15. Diese vier Sensoren senden genügend Telemetriedaten, um auf der Grundlage des RUL-Modells aussagekräftige Ergebnisse zu erhalten. Das im Solution Accelerator verwendete Modell basiert auf einem öffentlichen Dataset mit echten Daten von Triebwerksensoren. Weitere Informationen dazu, wie das Modell aus dem ursprünglichen Dataset erstellt wurde, finden Sie unter [Cortana Intelligence-Katalog: Vorlage für die vorbeugende Wartung][lnk-cortana-analytics].

Außerdem können die simulierten Geräte auch die folgenden Befehle verarbeiten, die vom IoT-Hub in der Lösung gesendet werden:

| Get-Help | BESCHREIBUNG |
| --- | --- |
| StartTelemetry |Steuert den Status der Simulation.<br/>Startet das Senden der Telemetriedaten vom Gerät. |
| StopTelemetry |Steuert den Status der Simulation.<br/>Beendet das Senden der Telemetriedaten vom Gerät. |

IoT Hub führt die Bestätigung der Gerätebefehle durch.

## <a name="azure-stream-analytics-job"></a>Azure Stream Analytics-Auftrag

**Auftrag: Telemetrie** verarbeitet den eingehenden Datenstrom mit Gerätetelemetriedaten mit zwei Anweisungen:

* Mit der ersten Anweisung werden alle Telemetriedaten von den Geräten ausgewählt und an Blobspeicher gesendet. Von dort aus werden sie in der Web-App visualisiert.
* Mit der zweiten Anweisung werden die durchschnittlichen Sensorwerte für ein gleitendes Fenster von zwei Minuten berechnet und über den Event Hub an einen **Ereignisprozessor** gesendet.

## <a name="event-processor"></a>Ereignisprozessor
Der **Ereignisprozessorhost** wird im Rahmen eines Azure-Webauftrags ausgeführt. Der **Ereignisprozessor** verwendet die durchschnittlichen Sensorwerte eines abgeschlossenen Zyklus. Diese Werte übergibt er dann an ein trainiertes Modell, das die Restlebensdauer eines Triebwerks berechnet. Eine API bietet Zugriff auf das Modell in einem Machine Learning-Arbeitsbereich, der Teil der Lösung ist.

## <a name="machine-learning"></a>Machine Learning
Die Machine Learning-Komponente verwendet ein Modell, das sich von Daten ableitet, die von echten Flugzeugtriebwerken gesammelt wurden. Über die Kachel auf der Seite [azureiotsolutions.com][lnk-azureiotsolutions] Ihrer Lösung können Sie zum Machine Learning-Arbeitsbereich navigieren. Die Kachel ist verfügbar, wenn sich die Lösung im Zustand **Bereit** befindet.

Das Machine Learning-Modell steht als Vorlage zur Verfügung, die das Arbeiten mit Telemetriedaten veranschaulicht, die über IoT-Solution Accelerator-Dienste erfasst wurden. Microsoft hat anhand öffentlich zugänglicher Daten <sup>\[1\]</sup> ein [Regressionsmodell][lnk_regression_model] eines Flugzeugtriebwerks erstellt und eine schrittweise Anleitung zur Verwendung des Modells veröffentlicht.

Der Azure IoT-Solution Accelerator für Predictive Maintenance verwendet das mit dieser Vorlage erstellte Regressionsmodell. Das Modell wird in Ihrem Azure-Abonnement bereitgestellt und über eine automatisch generierte API verfügbar gemacht. Die Lösung umfasst eine Teilmenge der Testdaten von vier (von insgesamt 100) Triebwerken und vier (von insgesamt 21) Sensordatenströmen. Diese Daten sind ausreichend, um ein genaues Ergebnis aus dem trainierten Modell bereitzustellen.

*\[1\] A. Saxena und K. Goebel (2008). „Turbofan Engine Degradation Simulation Data Set“, NASA Ames Prognostics Data Repository (https://c3.nasa.gov/dashlink/resources/139/), NASA Ames Research Center, Moffett Field, CA*

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun die zentralen Komponenten des Solution Accelerators für Predictive Maintenance kennen, können Sie ihn anpassen.

Sie können auch einige der anderen Features der IoT-Solution Accelerators ausprobieren:

* [Häufig gestellte Fragen zu IoT-Solution Accelerators][lnk-faq]
* [Sicherheit im Internet der Dinge von Anfang an][lnk-security-groundup]

[img-architecture]: media/iot-accelerators-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-accelerators-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-accelerators-predictive-walkthrough/machine-learning.png

[lnk-remote-monitoring]: quickstart-predictive-maintenance-deploy.md
[lnk-cortana-analytics]: https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsolutions]: https://www.azureiotsolutions.com/
[lnk-faq]: iot-accelerators-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_regression_model]: https://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
