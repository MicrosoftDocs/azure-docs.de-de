---
title: 'Übersicht: Was ist Azure Time Series Insights? | Microsoft-Dokumentation'
description: Einführung in Azure Time Series Insights, einem neuen Dienst zum Analysieren von Zeitreihendaten und für IoT-Lösungen.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: overview
ms.date: 04/26/2019
ms.custom: seodec18
ms.openlocfilehash: 6ef965b9c22524df5893d5826548a0b07f077062
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237636"
---
# <a name="what-is-azure-time-series-insights"></a>Was ist Azure Time Series Insights?

Azure Time Series Insights ist zum Speichern, Visualisieren und Abfragen großer Mengen an Zeitreihendaten ausgelegt, z. B. der von IoT-Geräten generierten Daten. Wenn Sie Zeitreihendaten in der Cloud speichern, verwalten, abfragen oder visualisieren möchten, ist Time Series Insights wahrscheinlich der richtige Dienst für Sie. 

![Flussdiagramm für Time Series Insights](media/overview/time-series-insights-flowchart.png)

Time Series Insights hat vier Hauptaufgaben bzw. -merkmale:

- Die Lösung ist vollständig in Cloudgateways wie Azure IoT Hub und Azure Event Hubs integriert. Sie stellt einfach eine Verbindung mit diesen Ereignisquellen her und analysiert JSON in Nachrichten und Strukturen, die Daten in fehlerfreien Zeilen und Spalten enthalten. Die Lösung verbindet Metadaten mit Telemetrie und indiziert Ihre Daten in einem einspaltigem Speicher.
- Time Series Insights verwaltet die Speicherung Ihrer Daten. Um sicherzustellen, dass Sie immer mühelos auf die Daten zugreifen können, werden sie bis zu 400 Tage im Arbeitsspeicher und auf SSD-Datenträgern gespeichert. Sie können bei Bedarf Milliarden von Ereignissen in Sekundenschnelle interaktiv abfragen.
- Time Series Insights bietet über den Time Series Insights-Explorer eine standardmäßige Visualisierung. 
- Time Series Insights bietet einen Abfragedienst, sowohl im Time Series Insights-Explorer als auch durch den Einsatz von APIs, die zum Einbetten Ihrer Zeitreihendaten in benutzerdefinierte Anwendungen einfach integriert werden können.

Wenn Sie eine Anwendung zur internen Nutzung oder für externe Kunden erstellen, können Sie Time Series Insights als Back-End verwenden. Sie können damit Zeitreihendaten indizieren, speichern und aggregieren. Wenn Sie darauf aufbauend eine benutzerdefinierte Visualisierung und Benutzeroberfläche erstellen möchten, können Sie das [Client SDK](tutorial-explore-js-client-lib.md) verwenden. Time Series Insights verfügt außerdem über mehrere [Abfrage-APIs](how-to-shape-query-json.md) für diese kundenspezifischen Szenarien.

Zeitreihendaten stellen dar, wie eine Ressource oder ein Prozess sich im Zeitablauf ändert. Zeitreihendaten werden anhand von Zeitstempeln indiziert. Zeit ist die aussagekräftigste Achse für die Organisation solcher Daten. Zeitreihendaten gehen normalerweise in sequenzieller Reihenfolge ein und werden daher in der Regel als Einfügung und nicht als Aktualisierung der Datenbank behandelt.

Das Speichern, Indizieren, Abfragen, Analysieren und Visualisieren großer Mengen an Zeitreihendaten kann eine Herausforderung darstellen.
Azure Time Series Insights erfasst und speichert jedes neue Ereignis als eine Zeile, und die Änderung wird effizient im Zeitverlauf gemessen. Sie können daher zurückblicken und Erkenntnisse aus der Vergangenheit gewinnen, um zukünftige Änderungen vorhersagen zu können.

## <a name="video"></a>Video

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>Dieses Video enthält Informationen zu Azure Time Series Insights, der cloudbasierten IoT-Analyseplattform.</br>

[![VIDEO](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Primäre Szenarien

- Skalierbare Speicherung von Zeitreihendaten 

   Im Kern enthält Time Series Insights eine Datenbank, die im Hinblick auf Zeitreihendaten entworfen wurde. Da diese Datenbank skalierbar und vollständig verwaltet ist, übernimmt Time Series Insights die Speicherung und Verwaltung von Ereignissen.

- Durchsuchen von Daten nahezu in Echtzeit 

   Time Series Insights enthält einen Explorer, der alle in eine Umgebung gestreamten Daten visualisiert. Kurz nachdem Sie eine Verbindung mit einer Ereignisquelle hergestellt haben, können Sie Ereignisdaten in Time Series Insights anzeigen, durchsuchen und abfragen. Mithilfe der Daten können Sie überprüfen, ob ein Gerät Daten wie erwartet ausgibt, sowie die Integrität, Produktivität und allgemeine Effektivität einer IoT-Ressource überwachen. 

- Durchführen von Grundursachenanalysen und Erkennen von Anomalien

   Time Series Insights umfasst Tools wie Muster und Perspektivenansichten zum Durchführen und Speichern von mehrstufigen Grundursachenanalysen. Darüber hinaus kann Time Series Insights mit Warnungsdiensten wie Azure Stream Analytics verwendet werden, sodass Sie Warnungen und erkannte Anomalien nahezu in Echtzeit im Time Series Insights-Explorer anzeigen können. 

- Globale Ansicht der von verschiedenen Standorten gestreamten Zeitreihendaten zum Vergleich mehrerer Ressourcen oder Standorte

   Sie können mehrere Ereignisquellen mit einer Time Series Insights-Umgebung verbinden. Auf diese Weise können Sie Daten, die von mehreren verschiedenen Standorten gestreamt werden, nahezu in Echtzeit zusammen anzeigen. Benutzer können diese Sichtbarkeit nutzen, um Daten mit Führungskräften zu teilen. Sie können besser mit Experten zusammenarbeiten, die mit ihrem Fachwissen dabei helfen können, Probleme zu lösen, bewährte Methoden anzuwenden und Erkenntnisse zu teilen.

- Erstellen einer auf Time Series Insights aufbauenden Kundenanwendung 

   Time Series Insights macht REST-Abfrage-APIs verfügbar, mit denen Sie Anwendungen erstellen können, die Zeitreihendaten nutzen.

## <a name="capabilities"></a>Funktionen

- **Schneller Einstieg**: Azure Time Series Insights erfordert keine Vorbereitung der Daten. Sie können im Handumdrehen eine Verbindung mit unzähligen Ereignissen in Ihrem IoT-Hub oder Event Hub herstellen. Nachdem Sie eine Verbindung hergestellt haben, können Sie zur schnellen Überprüfung Ihrer IoT-Lösungen Sensordaten visualisieren und mit diesen interagieren. Sie können mit Ihren Daten interagieren, ohne Code zu schreiben, und müssen keine neue Programmiersprache lernen. Time Series Insights enthält für fortgeschrittene Benutzer eine Oberfläche für präzise Freitextabfragen und bietet zudem die Möglichkeit, Daten per Zeigen und Klicken zu untersuchen.

- **Einblicke nahezu in Echtzeit**: Mit Time Series Insights können pro Tag Millionen von Sensorereignissen erfasst werden – mit einer Wartezeit von einer Minute. Time Series Insights bietet Einblicke in Ihre Sensordaten. So können Sie Trends und Anomalien erkennen, Grundursachenanalysen durchführen und teure Ausfallzeiten vermeiden. Dank der Korrelation von Echtzeit- und Verlaufsdaten können Sie versteckte Trends in den Daten aufdecken.

- **Erstellen von benutzerdefinierten Lösungen**: Betten Sie Azure Time Series Insights-Daten in Ihre bestehenden Anwendungen ein. Mithilfe der Time Series Insights-REST-APIs können Sie auch neue benutzerdefinierte Lösungen erstellen. Erstellen Sie personalisierte Ansichten, die Sie mit anderen teilen können, um ihnen die Untersuchung Ihrer Daten zu ermöglichen.

- **Skalierbarkeit:** Time Series Insights ist für die skalierte IoT-Nutzung ausgelegt. Es können zwischen 1 Million und 100 Millionen Ereignisse pro Tag erfasst werden, und die Standardaufbewahrungsdauer beträgt 31 Tage. Sie können sowohl Verlaufsdaten als auch Livedatenströme nahezu in Echtzeit visualisieren und analysieren.

## <a name="get-started"></a>Erste Schritte

Führen Sie zum Einstieg die folgenden Schritte aus.

1. Stellen Sie im Azure-Portal eine Time Series Insights-Umgebung bereit.
1. Verbinden Sie eine Ereignisquelle, beispielsweise einen IoT-Hub oder Event Hub. 
1. Laden Sie Referenzdaten hoch. Dies ist kein zusätzlicher Dienst.
1. Sie können Ihre Daten innerhalb von Minuten mit dem Time Series Insights-Explorer anzeigen.

## <a name="time-series-insights-explorer"></a>Time Series Insights-Explorer

Dieses Diagramm zeigt ein Beispiel für die Anzeige von Time Series Insights-Daten im Time Series Insights-Explorer.

![Time Series Insights-Explorer](media/time-series-insights-explorer/explorer4.png)

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die allgemein verfügbare [kostenlose Demoumgebung](./time-series-quickstart.md) von Azure Time Series Insights an.
- Informieren Sie sich ausführlicher über die [Planung Ihrer Time Series Insights-Umgebung](time-series-insights-environment-planning.md).
