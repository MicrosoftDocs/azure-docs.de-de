---
title: Übersicht über die Gerätesimulation – Azure | Microsoft-Dokumentation
description: Enthält eine Beschreibung des Solution Accelerators für die Gerätesimulation und der zugehörigen Funktionen.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: 7be0278fd8870395b8495168eb9723780eba4b26
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96852369"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Übersicht über den Solution Accelerator für die Gerätesimulation

In einer cloudbasierten IoT-Lösung stellen Ihre Geräte eine Verbindung mit einem Cloudendpunkt her, um Telemetriedaten wie Temperatur, Standort und Status zu senden. Ihre Lösung nutzt diese Telemetriedaten, sodass Sie Maßnahmen ergreifen oder Erkenntnisse daraus ableiten können.

Beim Entwickeln einer IoT-Lösung sind Experimente und Tests wesentliche Teile dieses Prozesses. Die Simulation ist während dieses gesamten Prozesses ein wichtiges Tool. Mit der Gerätesimulation haben Sie folgende Möglichkeiten:

* Entwickeln Sie in kurzer Zeit einen Prototyp, und führen Sie dann mehrere Durchläufe aus, indem Sie das simulierte Geräteverhalten während der Ausführung anpassen. Mit diesem Prozess können Sie eine Idee testen, bevor Sie in teure Hardware investieren. Sie können benutzerdefinierte Geräte über die Webbenutzeroberfläche erstellen, um innerhalb von wenigen Sekunden ein Prototypgerät zu generieren.
* Überprüfen Sie, ob die Lösung erwartungsgemäß funktioniert, indem Sie reales Geräteverhalten simulieren. Sie können für komplexes Geräteverhalten per JavaScript ein Skript erstellen, um realistische simulierte Telemetriedaten zu generieren.
* Führen Sie für Ihre Lösung Skalierungstests durch, indem Sie Zustände mit normaler Last, Spitzenlast und Überlastung simulieren. Mit Skalierungstests können Sie auch besser die richtige Größe der Azure-Ressourcen ermitteln, die zum Ausführen Ihrer Lösung benötigt werden.

![Beispiel: Drohnensimulation](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Mit der Gerätesimulation können Sie Gerätemodelle definieren, um Ihre echten Geräte zu definieren. Dieses Modell umfasst Nachrichtenformate, Zwillingseigenschaften und Methoden. Außerdem können Sie auch mit JavaScript komplexes Geräteverhalten simulieren.

Sie können Simulationen für einzelne oder mehrere Tausend Geräte ausführen, bei denen eine Verbindung mit beliebigen IoT-Hubs hergestellt wird. Zur Unterstützung des Testens können Sie optional einen IoT-Hub zusammen mit der Gerätesimulation für eine eigenständige Umgebung bereitstellen.

Die Gerätesimulation ist kostenlos. Allerdings werden bei der Gerätesimulation Daten unter Ihrem Azure-Abonnement in der Cloud bereitgestellt, und es werden Azure-Ressourcen verbraucht. Falls die Gerätesimulation Ihre Anforderungen nicht erfüllt, haben Sie die Möglichkeit, den [Quellcode auf GitHub](https://github.com/Azure/device-simulation-dotnet) zu kopieren und zu ändern.

## <a name="sample-simulations"></a>Beispielsimulationen

Wenn Sie die Gerätesimulation bereitstellen, erhalten Sie einige Beispielsimulationen und Beispielgeräte. Sie können diese Beispiele verwenden, um die Nutzung der Gerätesimulation zu erlernen. Führen Sie als Einstieg eine [Beispielsimulation](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) aus. Sie können auch [Ihre eigene Simulation erstellen, indem Sie eines der vielen bereitgestellten Beispielgeräte verwenden](iot-accelerators-device-simulation-create-simulation.md).

![Simulationskonfiguration](media/iot-accelerators-device-simulation-overview/samplesimulation1.png)

## <a name="custom-simulated-devices"></a>Benutzerdefinierte simulierte Geräte

Sie können die Gerätesimulation verwenden, um [benutzerdefinierte Gerätemodelle zu erstellen](iot-accelerators-device-simulation-create-custom-device.md), die in Ihren Simulationen verwendet werden können. Beispielsweise können Sie ein neues Kühlschrank-Gerätemodell definieren, das Telemetriedaten zu Temperatur und Luftfeuchtigkeit sendet. Benutzerdefinierte simulierte Geräte sind für einfaches Geräteverhalten mit zufälligen, inkrementierenden oder dekrementierenden Telemetriewerten ideal geeignet.

![Erstellen des Gerätemodells](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Erweiterte simulierte Geräte

Wenn Sie eine bessere Kontrolle über die von einem Gerät gesendeten Telemetriewerte benötigen, können Sie ein erweitertes Gerätemodell verwenden. Für erweiterte Gerätemodelle ist JavaScript-Unterstützung vorhanden, damit die gesendeten Telemetriedaten geändert werden können. Sie können beispielsweise die Innentemperatur eines geparkten PKW an einem heißen, sonnigen Tag simulieren. Wenn die Außentemperatur steigt, erhöht sich die Innentemperatur exponentiell.

Mit erweiterten Gerätemodellen können Sie [Ihre eigenen Gerätemodelle erstellen und hochladen](iot-accelerators-device-simulation-advanced-device.md), die eine JSON-Gerätedefinitionsdatei und entsprechende JavaScript-Dateien umfassen.

Mit erweiterten Gerätemodellen haben Sie folgende Möglichkeiten:

* Geben Sie das vom Gerät gesendete Nachrichtenformat und die Telemetrietypen an.
* Verwenden Sie benutzerdefinierte Skripts, um Telemetriewerte zu generieren, die den Status des Geräts im Laufe der Zeit widerspiegeln.
* Verwenden Sie benutzerdefinierte Skripts, um anzugeben, wie das simulierte Gerät auf Methoden reagiert.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden der Solution Accelerator für die Gerätesimulation und die zugehörigen Funktionen beschrieben. Informationen zum Bereitstellen des Solution Accelerators finden Sie im GitHub-Repository unter:

> [!div class="nextstepaction"]
> [Bereitstellen und Ausführen einer IoT-Gerätesimulation in Azure](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md)
