---
title: Optionen für die Architektur der Remoteüberwachungslösung – Azure | Microsoft-Dokumentation
description: Dieser Artikel beschreibt die architekturbezogenen und technischen Auswahlmöglichkeiten für die Remoteüberwachung.
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: conceptual
ms.openlocfilehash: 1bd08596a30db7322a72b4269fddfe0b9df19119
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61447171"
---
# <a name="remote-monitoring-architectural-choices"></a>Optionen für die Remoteüberwachungsarchitektur

Der Azure IoT-Solution Accelerator für die Remoteüberwachung ist ein Open Source-Solution Accelerator mit MIT-Lizenz. Zur Beschleunigung der IoT-Entwicklung veranschaulicht er etwa folgende allgemeine IoT-Szenarien:

- Gerätekonnektivität
- Geräteverwaltung
- Datenstromverarbeitung

Die Remoteüberwachungslösung folgt der empfohlenen [Azure IoT-Referenzarchitektur](https://aka.ms/iotrefarchitecture).

Dieser Artikel beschreibt die wichtigen technischen Auswahlmöglichkeiten für jedes Subsystem der Remoteüberwachungslösung. Die technischen Optionen, die Microsoft für die Remoteüberwachungslösung auswählt, sind nicht die einzige Möglichkeit, eine IoT-Remoteüberwachungslösung zu implementieren. Betrachten Sie die technische Implementierung als Grundlage für die Erstellung einer erfolgreichen Anwendung, und passen Sie sie an Folgendes an:

- Vorhandene Qualifikationen und Kenntnisse in Ihrer Organisation
- Vertikale Anwendungsanforderungen

## <a name="architectural-choices"></a>Architekturoptionen

Die von Microsoft empfohlene Architektur für IoT-Anwendungen ist cloudbasiert und serverlos und besteht aus Microservices. Die verschiedenen Subsysteme einer IoT-Anwendung sollten als separate Dienste erstellt werden, die unabhängig voneinander bereitgestellt und skaliert werden können. Diese Attribute ermöglichen eine höhere Skalierbarkeit und Flexibilität bei der Aktualisierung einzelner Subsysteme und ermöglichen zudem die flexible Auswahl der geeigneten Technologie für jedes Subsystem.

Microservices können mit verschiedenen Technologien implementiert werden. Sie können Microservices beispielsweise mit einer der folgenden Optionen implementieren:

- Verwenden einer Containertechnologie wie Docker mit einer serverlosen Technologie wie Azure Functions
- Hosten Ihrer Microservices in PaaS-Diensten wie Azure App Services

## <a name="technology-choices"></a>Auswahl der Technologie

In diesem Abschnitt werden die Technologieoptionen erläutert, die in der Remoteüberwachungslösung für jedes der grundlegenden Subsysteme ausgewählt wurden.

![Diagramm der grundlegenden Subsysteme](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png)

### <a name="cloud-gateway"></a>Cloudgateway

Azure IoT Hub wird als Cloudgateway der Remoteüberwachungslösung verwendet. [IoT Hub](https://azure.microsoft.com/services/iot-hub/) bietet eine sichere bidirektionale Kommunikation mit Geräten.

Für IoT-Gerätekonnektivität können Sie Folgendes verwenden:

- Die [IoT Hub-Geräte-SDKs](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) zum Implementieren einer nativen Clientanwendung für Ihr Gerät. Die SDKs bieten Wrapper für die IoT Hub-REST-API und verarbeiten Szenarios wie beispielsweise Wiederholungen.
- Die Integration mit Azure IoT Edge zum Bereitstellen und Verwalten von benutzerdefinierten Modulen, die in Containern auf Ihren Geräten ausgeführt werden.
- Die Integration mit der automatischen Geräteverwaltung im IoT Hub zur Massenverwaltung verbundener Geräte.

### <a name="stream-processing"></a>Datenstromverarbeitung

Für die Datenstromverarbeitung verwendet die Remoteüberwachungslösung Azure Stream Analytics, um die Verarbeitung komplexer Regeln durchzuführen. Wenn Sie einfachere Regeln verwenden möchten, steht ein benutzerdefinierter Microservice mit Unterstützung für die Verarbeitung einfacherer Regeln zur Verfügung. Dieses Setup gehört allerdings nicht zur schlüsselfertigen Bereitstellung. Die Referenzarchitektur empfiehlt Azure Functions für die Verarbeitung einfacher Regeln und Azure Stream Analytics für die Verarbeitung komplexer Regeln.

### <a name="storage"></a>Storage

Für die Speicherung verwendet der Solution Accelerator für die Remoteüberwachung sowohl Azure Time Series Insights als auch Azure Cosmos DB. Azure Time Series Insights speichert die Nachrichten, die über IoT Hub von Ihren verbundenen Geräten eingehen. Der Solution Accelerator verwendet Azure Cosmos DB für alle anderen Speicher, z.B. Cold Storage, Regeldefinitionen, Warnungen und Konfigurationseinstellungen.

Azure Cosmos DB ist die empfohlene allgemeine Warm Storage-Lösung für IoT-Anwendungen. Lösungen wie Azure Time Series Insights oder Azure Data Lake sind jedoch in vielen Anwendungsfällen auch geeignet. Mit Azure Time Series Insights erhalten Sie ausführliche Einblicke in Ihre Sensordaten und erkennen Trends und Abweichungen. Mit diesem Feature können Sie Ursachenanalysen ausführen und kostenintensive Ausfallzeiten vermeiden.

> [!NOTE]
> Time Series Insights ist für die Azure-Cloud in China derzeit nicht verfügbar. Für neue Solution Accelerator-Bereitstellungen für die Remoteüberwachung in der Azure-Cloud in China kann Cosmos DB für alle Speicherzwecke genutzt werden.

### <a name="business-integration"></a>Geschäftliche Integration

Die geschäftliche Integration der Remoteüberwachungslösung ist auf die Erzeugung von Warnungen beschränkt, die in Warm Storage abgelegt werden. Verbinden Sie die Lösung mit Azure Logic Apps, um komplexere Szenarien für die geschäftliche Integration zu implementieren.

### <a name="user-interface"></a>Benutzeroberfläche

Die Webbenutzeroberfläche basiert auf JavaScript React. React bietet ein häufig verwendetes, branchenspezifisches Framework für Benutzeroberflächen und ähnelt anderen beliebten Frameworks wie Angular.

### <a name="runtime-and-orchestration"></a>Runtime und Orchestrierung

Die Remoteüberwachungslösung nutzt Docker-Container, um die Subsysteme mit Kubernetes als Orchestrator für die horizontale Skalierung auszuführen. Diese Architektur ermöglicht individuelle Skalierungsdefinitionen für jedes Subsystem. Bei dieser Architektur fallen jedoch DevOps-Kosten an, da gewährleistet wird, dass die virtuellen Computer und Container stets geschützt und auf dem neuesten Stand sind.

Alternativ zu Docker können Microservices in PaaS-Diensten wie Azure App Service gehostet werden. Alternativ zu Kubernetes können Orchestratoren wie Service Fabric, DC/OS oder Swarm verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

* Stellen Sie Ihre Remoteüberwachungslösung [hier](https://www.azureiotsolutions.com/) bereit.
* Erkunden Sie [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/)- und [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/)-Code in GitHub.  
* Erfahren Sie [hier](https://aka.ms/iotrefarchitecture) mehr über die IoT-Referenzarchitektur.
