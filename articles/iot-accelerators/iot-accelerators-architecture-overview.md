---
title: Referenzarchitektur mit IoT-Solution Accelerators – Azure | Microsoft-Dokumentation
description: Hier erfahren Sie mehr zur Referenzarchitektur mit Azure IoT-Solution Accelerators. Die vorhandenen Solution Accelerators nutzen diese Referenzarchitektur. Sie können die Referenzarchitektur auch verwenden, wenn Sie Ihre eigenen IoT-Lösungen erstellen.
author: dominicbetts
ms.author: dobett
ms.date: 12/04/2018
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: philmea
ms.openlocfilehash: 01349dbc5fe98fd69ce7219ef6f2029d7bb815d0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067909"
---
# <a name="introduction-to-the-azure-iot-reference-architecture"></a>Einführung in die Azure IoT-Referenzarchitektur

In diesem Artikel wird die [Azure IoT-Referenzarchitektur](https://aka.ms/iotrefarchitecture) vorgestellt. Zudem wird erläutert, wie die [Azure IoT-Solution Accelerators](about-iot-accelerators.md) deren Empfehlungen folgen.

Die Open-Source-Solution Accelerators [Remote Monitoring](iot-accelerators-remote-monitoring-sample-walkthrough.md) und [Connected Factory](iot-accelerators-connected-factory-features.md) befolgen viele der Empfehlungen aus der Referenzarchitektur. Sie können diese Solution Accelerators als Ausgangspunkt für Ihre eigene IoT-Lösung oder als Lernhilfe verwenden.

## <a name="overview"></a>Übersicht

In der Referenzarchitektur werden die Bestandteile einer IoT-Lösung beschrieben, z. B. die Technologieprinzipien, der Aufbau von Azure IoT-Diensten und die Geräte. In der Architektur werden zudem Empfehlungen zu Implementierungstechnologien gegeben.

Grob besteht eine IoT-Lösung aus folgenden Elementen:

* Elemente, die Telemetrie generieren und übermitteln, z. B. Messungen und Ereignisse. Beim Solution Accelerator für die Remoteüberwachung senden z. B. LKW und Aufzüge Telemetriedaten.
* Erkenntnisse, die aus den Telemetriedaten der Geräte generiert werden. Im Solution Accelerator für die Remoteüberwachung generiert eine Regel eine Erkenntnis. Eine Regel kann z. B. erkennen, wenn die Temperatur in einem Motor einen Grenzwert überschreitet.
* Aktionen basierend auf Erkenntnissen, die ein Unternehmen oder einen Prozess optimieren können. Beim Solution Accelerator für die Remoteüberwachung kann ein Bediener durch eine E-Mail über mögliche Motorprobleme informiert werden.

Die [Azure IoT-Referenzarchitektur](https://aka.ms/iotrefarchitecture) wird laufend mit sich verändernder Technologie aktualisiert.

## <a name="core-subsystems"></a>Wichtigste Subsysteme

In der Referenzarchitektur werden die folgenden wichtigsten Subsysteme angegeben:

![Wichtigste Subsysteme](media/iot-accelerators-architecture-overview/coresubsystems1.png)

In den folgenden Abschnitten wird beschrieben, in welchem Verhältnis die Komponenten des Solution Accelerators für die Remoteüberwachung zu diesen Subsystemen stehen.

### <a name="iot-devices"></a>IoT-Geräte

Eine IoT-Lösung sollte die sichere, effiziente und widerstandsfähige Kommunikation zwischen fast jedem Gerät und einem Cloudgateway ermöglichen. Geräte sind Unternehmensressourcen. Dazu zählen sowohl einfache Temperatursensoren als auch komplexe Produktionslinien mit Hunderten Komponenten und Sensoren.

Ein Bereichsgateway bzw. Edgegerät ist ein spezialisiertes Gerät oder eine Allzwecksoftware, die folgende Aufgaben erfüllen kann:

* Kommunikation zur Protokollkonvertierung.
* Lokale Gerätesteuerung und Verarbeitung von Gerätetelemetriedaten. Ein Edgegerät kann Telemetriedaten lokal verarbeiten, um Geräte zu steuern, ohne mit der Cloud kommunizieren zu müssen. Zudem kann es Gerätetelemetriedaten filtern und aggregieren, um die an die Cloud übertragene Menge an Telemetriedaten zu senken.

Im Solution Accelerator für die Remoteüberwachung stellen Geräte eine Verbindung mit einem IoT-Hub her und übermitteln Telemetriedaten zur Verarbeitung. Mit dem Solution Accelerator für die Remoteüberwachung können Bediener zudem Geräte anhand von Aufträgen oder anhand der automatischen Geräteverwaltung verwalten. Sie können die Azure IoT-Geräte-SDKs verwenden, um Ihre IoT-Geräte zu implementieren.

Der Solution Accelerator für die Remoteüberwachung kann IoT Edge-Geräte bereitstellen und verwalten. Die Verarbeitung am Edge trägt dazu bei, die Menge der Telemetriedaten zu senken, die an die Cloud gesendet werden, und Reaktionen auf Geräteereignisse zu beschleunigen.

### <a name="cloud-gateway"></a>Cloudgateway

Durch ein Cloudgateway wird die Kommunikation mit Geräten und Edgegeräten ermöglicht. Diese Geräte kann es an vielen Remotestandorten geben.

Das Gateway verwaltet die Gerätekommunikation, einschließlich Verbindungsverwaltung, den Schutz des Kommunikationspfads, die Authentifizierung und die Autorisierung. Zudem erzwingt es Verbindungs- und Durchsatzquoten und erfasst Telemetriedaten, anhand derer Rechnungen gestellt, Diagnosen getätigt und andere Überwachungsaufgaben durchgeführt werden.

Der Solution Accelerator für die Remoteüberwachung stellt einen IoT-Hub bereit, damit Geräte Telemetriedaten an einen sicheren Endpunkt senden können. Für den IoT-Hub gilt Folgendes:

* Er umfasst einen Speicher für Geräteidentitäten, um die Geräte zu verwalten, die eine Verbindung mit der Lösung herstellen können.
* Er ermöglicht, dass die Lösung Befehle an Geräte senden kann, z. B. um ein Ventil zu öffnen, um Druck abzulassen.
* Er ermöglicht die Massenverwaltung von Geräten, z. B. um die Firmware auf mehreren Geräten gleichzeitig zu aktualisieren.

### <a name="stream-processing"></a>Datenstromverarbeitung

Da die Lösung Telemetriedaten erfasst, ist es wichtig zu verstehen, wie sich der Fluss der Telemetrieverarbeitung unterscheiden kann. Je nach Szenario können Daten unterschiedliche Stufen durchlaufen:

* Speicher, z. B. speicherinterne Caches, temporäre Warteschlangen und permanente Archive.

* Analyse. Eingabetelemetriedaten durchlaufen unterschiedliche Bedingungen und können unterschiedliche Ausgabedatensätze erzeugen. Es kann z. B. sein, dass Eingabetelemetriedaten in Avro Telemetriedaten in JSON ausgeben.

* Die ursprünglich Eingabetelemetriedaten und die Ausgabedaten der Analyse werden normalerweise gespeichert und zur Anzeige verfügbar gemacht. Die Eingabetelemetriedaten und Ausgabedatensätze können zudem Aktionen wie E-Mails, Incidenttickets und Gerätebefehle auslösen.

Durch Routing können Telemetriedaten an Speicherendpunkte, Analyseprozesse und Aktionen verteilt werden. Eine Lösung enthält diese Stufen vielleicht in einer anderen Reihenfolge oder verarbeitet sie in gleichzeitigen, parallelen Tasks.

Der Solution Accelerator für die Remoteüberwachung verwendet [Azure Stream Analytics](/azure/stream-analytics/) für die Datenstromverarbeitung. Die Regel-Engine der Lösung verwendet Stream Analytics-Abfragen zum Generieren von Warnungen und Aktionen. Die Lösung kann z. B anhand einer Abfrage bestimmen, wenn die Durchschnittstemperatur im Laderaum eines LKW länger als fünf Minuten durchschnittlich weniger als 2° C beträgt.

### <a name="storage"></a>Storage

IoT-Lösungen können sehr hohe Datenmengen generieren. Dabei handelt es sich oft um Zeitreihendaten. Diese Daten müssen dort gespeichert werden, wo sie für die Visualisierung und Berichterstellung verwendet werden können. Es kann außerdem sein, dass eine Lösung zu einem späteren Zeitpunkt zu Analysezwecken oder zur weiteren Verarbeitung auf Daten zugreifen muss. Es ist üblich, Daten in Speichern für kalte und für warme Daten zu speichern. Der Speicher für warme Daten enthält aktuelle Daten für einen Zugriff mit geringer Wartezeit. Der Speicher für kalte Daten enthält normalerweise Verlaufsdaten.

Der Solution Accelerator für die Remoteüberwachung verwendet [Azure Time Series Insights](/azure/time-series-insights/) als Speicher für warme Daten und Cosmos DB als Speicher für kalte Daten.

### <a name="ui-and-reporting-tools"></a>Benutzeroberfläche und Berichterstellungstools

Über die Benutzeroberfläche des Solution Accelerators können Sie folgende Aktionen durchführen und Funktionen nutzen:

* Zugriff auf und Visualisierung von Gerätedaten und Analyseergebnissen
* Ermittlung von Geräten in der Registrierung
* Steuerung von Geräten mit Befehlen
* Workflows zur Gerätebereitstellung
* Benachrichtigungen und Berichte
* Integration mit interaktiven oder Livedashboards zum Anzeigen von Daten von vielen Geräten  
* Geolocation und Geräte die den geografischen Standort beachten

Der Solution Accelerator für die Remoteüberwachung verfügt über eine Webbenutzeroberfläche für diese Funktionen. Die Webbenutzeroberfläche umfasst Folgendes:

* Eine interaktive Karte mit den Positionen der Geräte
* Zugriff auf den Time Series Insights-Explorer zur Abfrage und Analyse der Telemetriedaten

### <a name="business-integration"></a>Geschäftliche Integration

Die geschäftliche Integration befasst sich mit der Integration der IoT-Lösung in Unternehmenssysteme wie CRM, ERP und Branchenanwendungen. Dies können z. B. die Dienstrechnungsstellung, der Kundensupport und die Versorgung mit Ersatzteilen sein.

Der Solution Accelerator für die Remoteüberwachung verwendet Regeln, um E-Mails zu versenden, wenn eine Bedingung erfüllt wird. Die Lösung kann z. B. einen Bediener informieren, wenn die Temperatur in einem LKW unter 2° C fällt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie die Azure IoT-Referenzarchitektur kennengelernt. Außerdem wurde veranschaulicht, wie der Solution Accelerator für die Remoteüberwachung deren Empfehlungen folgt. Lesen Sie als Nächstes den Artikel zur [Microsoft Azure IoT-Referenzarchitektur](https://aka.ms/iotrefarchitecture).