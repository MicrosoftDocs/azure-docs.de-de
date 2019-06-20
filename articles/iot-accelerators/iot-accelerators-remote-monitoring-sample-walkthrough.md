---
title: 'Solution Accelerator für die Remoteüberwachung: Übersicht – Azure | Microsoft-Dokumentation'
description: Übersicht über den Solution Accelerator für die Remoteüberwachung.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: af09ea39f373d518d5600e3fa46adc378fd9236d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61442534"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Solution Accelerator für die Remoteüberwachung: Übersicht

Der [Solution Accelerator](../iot-accelerators/about-iot-accelerators.md) für Remoteüberwachung implementiert eine End-to-End-Überwachungslösung für mehrere Computer an Remotestandorten. In der Lösung sind wichtige Azure-Dienste kombiniert, um eine generische Implementierung des Geschäftsszenarios zu erzielen. Sie können sie als Ausgangspunkt für Ihre Implementierung verwenden und [anpassen](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md), um Ihre eigenen speziellen Geschäftsanforderungen zu erfüllen.

In diesem Artikel werden einige wichtige Elemente der Remoteüberwachungslösung beschrieben, um die Funktionsweise zu verdeutlichen. Dieses Wissen ist für folgende Zwecke hilfreich:

* Behandeln von Problemen in der Lösung
* Planen der Lösungsanpassung zur Erfüllung besonderer Anforderungen
* Entwerfen einer eigenen IoT-Lösung mit Verwendung von Azure-Diensten

Der Code für den Solution Accelerator für die Remoteüberwachung ist auf GitHub verfügbar:

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>Logische Architektur

Im folgenden Diagramm sind die logischen Komponenten des Solution Accelerators für die Remoteüberwachung zusammen mit der [IoT-Architektur](../iot-fundamentals/iot-introduction.md) dargestellt:

![Logische Architektur](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Gründe für die Verwendung von Microservices

Die Cloudarchitektur wurde ständig weiterentwickelt, seit Microsoft die ersten Solution Accelerators veröffentlicht hat. [Microservices](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) haben sich als bewährte Methode herausgestellt, um Skalierbarkeit und Flexibilität zu erzielen, ohne die Entwicklungsgeschwindigkeit zu opfern. Mehrere Microsoft-Dienste nutzen dieses Architekturmuster intern mit hervorragenden Ergebnissen bei der Zuverlässigkeit und Skalierbarkeit. Mit den aktualisierten Solution Accelerators werden diese Erkenntnisse in die Praxis umgesetzt, damit auch Sie davon profitieren können.

> [!TIP]
> Weitere Informationen zu Microservicearchitekturen finden Sie unter [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture) (.NET-Anwendungsarchitektur) und [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microservices: Eine cloudbasierte Anwendungsrevolution).

## <a name="device-connectivity"></a>Gerätekonnektivität

Die Lösung enthält die folgenden Komponenten im Teil zur Gerätekonnektivität der logischen Architektur:

### <a name="real-devices"></a>Verwenden realer Geräte

Sie können für reale Geräte eine Verbindung mit der Lösung herstellen. Sie können das Verhalten Ihrer simulierten Geräte implementieren, indem Sie die Azure IoT-Geräte-SDKs verwenden.

Sie können reale Geräte über das Dashboard im Lösungsportal bereitstellen.

### <a name="device-simulation-microservice"></a>Microservice „Gerätesimulation“

Die Lösung enthält den [Microservice „Gerätesimulation“](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation), mit dem Sie einen Pool mit simulierten Geräten über das Lösungsportal verwalten können, um den End-to-End-Datenfluss der Lösung zu testen. Die simulierten Geräte ermöglichen Folgendes:

* Generieren der Gerät-zu-Cloud-Telemetrie (D2C)
* Reagieren auf Cloud-zu-Gerät-Methodenaufrufe (C2D) aus IoT Hub

Der Microservice stellt einen RESTful-Endpunkt für Sie bereit, über den Sie Simulationen erstellen, starten und beenden können. Jede Simulation umfasst einen Satz mit virtuellen Geräten unterschiedlichen Typs, die Telemetriedaten senden und auf Methodenaufrufe reagieren.

Sie können simulierte Geräte über das Dashboard im Lösungsportal bereitstellen.

### <a name="iot-hub"></a>IoT Hub

Der [IoT Hub](../iot-hub/index.yml) erfasst Telemetriedaten, die von den realen und simulierten Geräten in die Cloud gesendet wurden. Der IoT Hub stellt die Telemetriedaten den Diensten im IoT-Lösungs-Back-End für die Verarbeitung zur Verfügung.

Zu den Aufgaben des IoT Hub in der Lösung zählen auch:

* Verwalten einer Identitätsregistrierung zum Speichern der IDs und Authentifizierungsschlüssel aller Geräte, die Verbindungen zum Portal herstellen dürfen.
* Aufrufen von Methoden auf Ihren Geräten im Namen des Solution Accelerators.
* Verwalte von Gerätezwillingen für alle registrierten Geräte. Ein Gerätezwilling speichert die von einem Gerät gemeldeten Eigenschaftswerte. Ein Gerätezwilling speichert außerdem die im Lösungsportal gespeicherten gewünschten Eigenschaften, damit das Gerät diese beim nächsten Verbinden abrufen kann.
* Planen von Aufträgen, um Eigenschaften für mehrere Geräte festzulegen oder Methoden auf mehreren Geräte aufzurufen.

## <a name="data-processing-and-analytics"></a>Datenverarbeitung und Analysen

Die Lösung enthält die folgenden Komponenten im Teil zur Datenverarbeitung und zu Analysen der logischen Architektur:

### <a name="iot-hub-manager-microservice"></a>Microservice „IoT Hub-Manager“

Die Lösung enthält den [Microservice „IoT Hub-Manager“](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) zum Verarbeiten von Interaktionen mit Ihrem IoT Hub, z.B.:

* Erstellen und Verwalten von IoT-Geräten
* Verwalten von Gerätezwillingen
* Aufrufen von Methoden auf Geräten
* Verwalten von IoT-Anmeldeinformationen

Dieser Dienst führt auch IoT Hub-Abfragen durch, um Geräte abzurufen, die zu benutzerdefinierten Gruppen gehören.

Der Microservice stellt einen RESTful-Endpunkt zum Verwalten von Geräten und Gerätezwillingen, Aufrufen von Methoden und Ausführen von IoT Hub-Abfragen bereit.

### <a name="device-telemetry-microservice"></a>Microservice „Gerätetelemetrie“

Der [Microservice „Gerätetelemetrie“](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry) stellt einen RESTful-Endpunkt für den Lesezugriff auf Gerätetelemetrie bereit, die in Time Series Insights gespeichert ist. Der RESTful-Endpunkt ermöglicht außerdem CRUD-Vorgänge an Regeln und Lese-/Schreibzugriff für Alarmdefinitionen aus dem Speicher.

### <a name="storage-adapter-microservice"></a>Microservice „Speicheradapter“

Der [Microservice „Speicheradapter“](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter) verwaltet Schlüssel-Wert-Paare, die die Speicherdienstsemantik abstrahiert und eine einfache Schnittstelle zum Speichern von Daten von beliebigem Format mithilfe von Azure Cosmos DB enthält.

Werte werden in Auflistungen organisiert. Sie können an einzelnen Werten arbeiten oder ganze Sammlungen abrufen. Komplexe Datenstrukturen werden von den Clients serialisiert und als einfache Textnutzlast verwaltet.

Der Dienst stellt einen RESTful-Endpunkt für CRUD-Vorgänge bei Schlüssel-Wert-Paaren bereit. Werte

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Solution Accelerator-Bereitstellungen verwenden [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) zum Speichern von Regeln, Warnungen, Konfigurationseinstellungen und allem anderen Cold Storage.

### <a name="azure-stream-analytics-manager-microservice"></a>Microservice „Azure Stream Analytics-Manager“

Der [Microservice „Azure Stream Analytics-Manager“](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager) verwaltet ASA-Aufträge (Azure Stream Analytics) und legt unter anderem deren Konfiguration fest, startet und stoppt sie und überwacht deren Status.

Der ASA-Auftrag wird von zwei Verweisdatasets unterstützt. Ein Dataset definiert Regeln, und das andere definiert Gerätegruppen. Die Verweisdaten für Regeln werden aus den vom Microservice „Gerätetelemetrie“ verwalteten Informationen generiert. Der Microservice „Azure Stream Analytics-Manager“ transformiert Telemetrieregeln in Datenstrom-Verarbeitungslogik.

Anhand der Verweisdaten für Gerätegruppen wird identifiziert, welche Gruppe von Regeln für eine eingehende Telemetriemeldung gilt. Die Gerätegruppen werden vom Microservice „Konfiguration“ verwaltet, und sie verwenden Azure IoT Hub-Gerätezwillingsabfragen.

Die ASA-Aufträge übermitteln die Telemetriedaten von den verbundenen Geräten an Time Series Insights zur Speicherung und Analyse.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics

[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) ist ein Ereignisverarbeitungsmodul, mit dem Sie große Datenmengen untersuchen können, die von Geräten gestreamt werden.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/) speichert die Telemetriedaten aus den mit dem Solution Accelerator verbundenen Geräten. Es ermöglicht auch das Visualisieren und Abfragen von Gerätetelemetrie in der Projektmappe-Webbenutzeroberfläche.

> [!NOTE]
> Time Series Insights ist für die Azure-Cloud in China derzeit nicht verfügbar. Für neue Solution Accelerator-Bereitstellungen für die Remoteüberwachung in der Azure-Cloud in China kann Cosmos DB für alle Speicherzwecke genutzt werden.

### <a name="configuration-microservice"></a>Microservice „Konfiguration“

Der [Microservice „Konfiguration“](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) stellt einen RESTful-Endpunkt für CRUD-Vorgänge an Gerätegruppen, Lösungseinstellungen und Benutzereinstellungen im Solution Accelerator bereit. Er arbeitet mit dem Microservice „Speicheradapter“ zusammen, um die Konfigurationsdaten beizubehalten.

### <a name="authentication-and-authorization-microservice"></a>Microservice „Authentifizierung und Autorisierung“

Der [Microservice „Authentifizierung und Autorisierung“](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) verwaltet die Benutzer, die berechtigt sind, auf den Solution Accelerator zuzugreifen. Für die Benutzerverwaltung kann jeder beliebige Identitätsanbieter verwendet werden, der [OpenID Connect](https://openid.net/connect/) unterstützt.

### <a name="azure-active-directory"></a>Azure Active Directory

Solution Accelerator-Bereitstellungen verwenden [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) als OpenID Connect-Anbieter. Azure Active Directory speichert Benutzerinformationen und stellt Zertifikate zum Überprüfen von JWT-Tokensignaturen bereit.

## <a name="presentation"></a>Präsentation

Die Lösung enthält die folgenden Komponenten im Teil zur Darstellung der logischen Architektur:

Die [Webbenutzeroberfläche ist eine React-JavaScript-Anwendung](https://github.com/Azure/pcs-remote-monitoring-webui). Für die Anwendung gilt Folgendes:

* Ausschließliche Verwendung von JavaScript React und vollständige Ausführung im Browser
* Formatierung per CSS
* Interaktion mit öffentlichen Microservices über AJAX-Aufrufe

Die Benutzeroberfläche enthält alle Funktionen des Solution Accelerators und interagiert mit anderen Microservices, z.B.:

* Microservice „Authentifizierung und Autorisierung“ zum Schützen von Benutzerdaten.
* Microservice „IoT Hub-Manager“ zum Auflisten und Verwalten der IoT-Geräte.

Die Benutzeroberfläche integriert den Azure Time Series Insights-Explorer, um das Abfragen und Analysieren von Gerätetelemetrie zu ermöglichen.

Mit dem Microservice „Konfiguration“ kann die Benutzeroberfläche Konfigurationseinstellungen speichern und abrufen.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie den Quellcode und die Entwicklerdokumentation erkunden möchten, können Sie mit einem der beiden GitHub-Repositorys beginnen:

* [Solution Accelerator für Remoteüberwachung mit Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Solution Accelerator für Remoteüberwachung mit Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

Ausführliche Diagramme für die Lösungsarchitektur:
* [Architektur des Solution Accelerators für Remoteüberwachung](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture)

Weitere konzeptionelle Informationen zum Solution Accelerator für die Remoteüberwachung finden Sie unter [Anpassen des Solution Accelerators für die Remoteüberwachung](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).
