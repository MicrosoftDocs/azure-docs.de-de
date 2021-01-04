---
title: Einführung in IoT Solution Accelerators – Azure | Microsoft-Dokumentation
description: Enthält eine Beschreibung der Azure IoT Solution Accelerators. Bei IoT Solution Accelerators handelt es sich um vollständige End-to-End-IoT-Lösungen, die fertig für die Bereitstellung sind.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: 193ceab0b2df1ab833a86eb748c18271a8e33b71
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96852913"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>Was sind Azure IoT Solution Accelerators?

Für eine cloudbasierte IoT-Lösung werden normalerweise benutzerdefinierter Code und Clouddienste verwendet, um die Gerätekonnektivität, die Datenverarbeitung und -analyse und die Darstellung zu verwalten.

Die IoT Solution Accelerators sind vollständige, sofort bereitstellbare IoT-Lösungen, die allgemeine IoT-Szenarien implementieren. Zu den Szenarien zählen verbundene Factory und Gerätesimulation. Wenn Sie einen Solution Accelerator bereitstellen, enthält die Bereitstellung alle erforderlichen cloudbasierten Dienste sowie den gesamten erforderlichen Anwendungscode.

Die Solution Accelerators sind Ausgangspunkte für Ihre eigenen IoT-Lösungen. Der Quellcode für alle Solution Accelerators ist Open-Source-Code und auf GitHub verfügbar. Wir empfehlen Ihnen, die Solution Accelerators herunterzuladen und anzupassen, damit sie Ihre Anforderungen erfüllen.

Sie können die Solution Accelerators auch als Lerntools verwenden, bevor Sie eine benutzerdefinierte IoT-Lösung selbst neu erstellen. Mit den Solution Accelerators werden bewährte Methoden für cloudbasierte IoT-Lösungen implementiert, an die Sie sich halten können.

Der Anwendungscode in den einzelnen Solution Accelerators enthält eine Web-App, mit der Sie den Solution Accelerator verwalten können.

> [!NOTE]
> Die Lösungen für Remoteüberwachung und Predictive Maintenance wurden von der Website [Azure IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators) entfernt. Weitere Informationen finden Sie unter [Was sind Azure IoT Solution Accelerators?](/previous-versions/azure/iot-accelerators/about-iot-accelerators.md) (vorherige Version).

## <a name="supported-iot-scenarios"></a>Unterstützte IoT-Szenarien

Derzeit sind zwei Solution Accelerators verfügbar, die Sie bereitstellen können:

### <a name="connected-factory"></a>Verbundene Factory

Verwenden Sie den [Solution Accelerator „Verbundene Factory“](iot-accelerators-connected-factory-features.md), um Telemetriedaten von Industrieanlagen mit einer Schnittstelle vom Typ [OPC Unified Architecture](https://opcfoundation.org/about/opc-technologies/opc-ua/) zu erfassen und zu steuern. Beispiele für Industrieanlagen sind Montage- und Teststationen einer Fertigungsstrecke.

Sie können das Dashboard für verbundene Factorys zum Überwachen und Verwalten Ihrer Industriegeräte verwenden:

:::image type="content" source="./media/about-iot-accelerators/cf-dashboard-inline.png" alt-text="Screenshot, der das Dashboard für die Lösung „Verbundene Factory“ zeigt." lightbox="./media/about-iot-accelerators/cf-dashboard-expanded.png":::

### <a name="device-simulation"></a>Gerätesimulation

Verwenden Sie den [Solution Accelerator „Gerätesimulation“](iot-accelerators-device-simulation-overview.md), um simulierte Geräte auszuführen, die realistische Telemetriedaten generieren. Sie können diesen Solution Accelerator nutzen, um das Verhalten der anderen Solution Accelerators oder Ihre eigenen benutzerdefinierten IoT-Lösungen zu testen.

Sie können die Gerätesimulations-Web-App zum Konfigurieren und Ausführen von Simulationen verwenden:

:::image type="content" source="./media/about-iot-accelerators/ds-dashboard-inline.png" alt-text="Screenshot: Dashboard für die Lösung „Gerätesimulation“" lightbox="./media/about-iot-accelerators/ds-dashboard-expanded.png":::

## <a name="design-principles"></a>Entwurfsprinzipien

Alle Solution Accelerators basieren auf den gleichen Entwurfsprinzipien und -zielen. Sie sind wie folgt konzipiert:

* **Skalierbar**: Sie können Millionen von verbundenen Geräten verknüpfen und verwalten.
* **Erweiterbar**: Sie können Anpassungen vornehmen, um Ihre Anforderungen zu erfüllen.
* **Verständlich**: Sie können leicht verstehen, wie sie funktionieren und implementiert werden.
* **Modular**: Sie können Dienste gegen Alternativen austauschen.
* **Sicher**: Sie können die Azure-Sicherheit mit integrierten Features für Konnektivität und Gerätesicherheit kombinieren.

## <a name="architectures-and-languages"></a>Architekturen und Sprachen

Die ursprünglichen Solution Accelerators wurden mit .NET geschrieben, indem eine MVC-Architektur (Model-View-Controller) verwendet wurde. Microsoft führt für die Solution Accelerators die Aktualisierung auf eine neue Microservice-Architektur durch. Die folgende Tabelle zeigt den aktuellen Status der Solution Accelerators mit Links zu den GitHub-Repositorys:

| Solution Accelerator   | Aufbau  | Languages     |
| ---------------------- | ------------- | ------------- |
| Verbundene Factory      | MVC           | [.NET](https://github.com/Azure/azure-iot-connected-factory)          |
| Gerätesimulation      | Microservices | [.NET](https://github.com/Azure/device-simulation-dotnet)          |

Weitere Informationen zur Microservicearchitektur finden Sie unter [Introduction to the Azure IoT reference architecture](/azure/architecture/reference-architectures/iot/) (Einführung in die Azure IoT-Referenzarchitektur).

## <a name="deployment-options"></a>Bereitstellungsoptionen

Sie können die Solution Accelerators über die Website [Microsoft Azure IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators#) oder mithilfe der Befehlszeile bereitstellen.

Die Kosten für die Ausführung eines Solution Accelerators ergeben sich aus den zusammengefassten [Kosten der zugrunde liegenden Azure-Dienste](https://azure.microsoft.com/pricing). Die Details der verwendeten Azure-Dienste werden angezeigt, wenn Sie Ihre Bereitstellungsoptionen auswählen.

## <a name="next-steps"></a>Nächste Schritte

In der Schnellstartanleitung [Testen einer cloudbasierten Lösung zum Verwalten von industriellen IoT-Geräten](quickstart-connected-factory-deploy.md) können Sie einen der IoT Solution Accelerators testen.
