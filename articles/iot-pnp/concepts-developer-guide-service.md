---
title: 'Leitfaden für Dienstentwickler: IoT Plug & Play | Microsoft-Dokumentation'
description: Beschreibung von IoT Plug & Play für Dienstentwickler
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: df913716ff34a61e5bde4c0771ea8b7599db3d30
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521379"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>Leitfaden für IoT Plug & Play-Dienstentwickler

Mit IoT Plug & Play können Sie intelligente Geräte erstellen, deren Funktionen für Azure IoT-Anwendungen verfügbar gemacht werden. Bei IoT Plug & Play-Geräten ist keine manuelle Konfiguration erforderlich, wenn ein Kunde sie mit IoT Plug & Play-fähigen Anwendungen verbindet.

Mithilfe von IoT Plug & Play können Sie Geräte verwenden, deren Model-ID bei Ihrem IoT-Hub angekündigt wurde. Beispielsweise können Sie direkt auf die Eigenschaften und Befehle eines Geräts zugreifen.

Zur Verwendung eines mit Ihrem IoT-Hub verbundenen IoT Plug & Play-Geräts können Sie eines der IoT-Dienst-SDKs nutzen:

## <a name="service-sdks"></a>Dienst-SDKs

Verwenden Sie in Ihrer Lösung die Azure IoT-Dienst-SDKs für die Interaktion mit Geräten und Modulen. Beispielsweise können Sie die Dienst-SDKs verwenden, um Zwillingseigenschaften zu lesen und zu aktualisieren und Befehle aufzurufen. Zu den unterstützten Sprachen gehören C#, Java, Node.js und Python.

Mithilfe der Dienst-SDKs können Sie auf Geräteinformationen aus einer Lösung zugreifen, wie etwa einen Desktop oder eine Webanwendung. Die Dienst-SDKs beinhalten zwei Namespaces und Objektmodelle, die Sie zum Abrufen der Modell-ID verwenden können:

- Iot Hub-Dienstclient. Dieser Dienst macht die Modell-ID als Gerätezwillings-Eigenschaft verfügbar.

- Digital Twins-Client. Die neue Digital Twins-API funktioniert mithilfe von Modellkonstrukten der [Digital Twins Definition Language (DTDL)](concepts-digital-twin.md), wie etwa Komponenten, Eigenschaften und Befehlen. Die APIs für digitale Zwillinge erleichtern Lösungsentwicklern das Erstellen von IoT Plug & Play-Lösungen.

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-service-devguide-csharp](../../includes/iot-pnp-service-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-service-devguide-java](../../includes/iot-pnp-service-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-service-devguide-node](../../includes/iot-pnp-service-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-service-devguide-python](../../includes/iot-pnp-service-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit der Gerätemodellierung vertraut gemacht haben, finden Sie weitere Informationen in folgenden Artikeln:

- [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C-Geräte-SDK](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT-REST-API](/rest/api/iothub/device)
- [Modellkomponenten](./concepts-components.md)
- [Installieren und Verwenden der DTDL-Erstellungstools](howto-use-dtdl-authoring-tools.md)