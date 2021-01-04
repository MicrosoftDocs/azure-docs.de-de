---
title: Sicherheitsmodul und Gerätezwillinge
description: Lernen Sie das Konzept der Sicherheitsmodulzwillinge kennen, und erfahren Sie, wie sie in Defender für IoT verwendet werden.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: b33c456d47426a3721e8582f24ffd603db0429c9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340032"
---
# <a name="security-module"></a>Sicherheitsmodul

In diesem Artikel wird erläutert, wie Defender für IoT Gerätezwillinge und -module verwendet.

## <a name="device-twins"></a>Gerätezwillinge

Für in Azure erstellte IoT-Lösungen spielen Gerätezwillinge eine wichtige Rolle – sowohl bei der Geräteverwaltung als auch bei der Prozessautomatisierung.

Defender für IoT ermöglicht die vollständige Integration in Ihre vorhandene Plattform für die IoT-Geräteverwaltung. Auf diese Weise können Sie Ihren Gerätesicherheitsstatus verwalten und die vorhandenen Funktionen für die Gerätesteuerung nutzen. Die Integration wird erreicht, indem der Mechanismus für IoT Hub-Zwillinge verwendet wird.

Erfahren Sie mehr über das Konzept der [Gerätezwillinge](../iot-hub/iot-hub-devguide-device-twins.md) in Azure IoT Hub.

## <a name="security-module-twins"></a>Sicherheitsmodulzwillinge

Defender für IoT verwaltet für jedes Gerät im Dienst einen Sicherheitsmodulzwilling.
Der Sicherheitsmodulzwilling enthält alle für die Gerätesicherheit relevanten Informationen für jedes einzelne Gerät in Ihrer Lösung.
Die Gerätesicherheitseigenschaften werden in einem dedizierten Sicherheitsmodulzwilling verwaltet, um eine sicherere Kommunikation zu gewährleisten sowie Updates und Wartungen zu ermöglichen, die weniger Ressourcen erfordern.

Unter [Erstellen eines Sicherheitsmodulzwillings](quickstart-create-security-twin.md) und [Konfigurieren von Sicherheits-Agents](how-to-agent-configuration.md) erfahren Sie, wie Sie den Zwilling erstellen, anpassen und konfigurieren. Weitere Informationen zum Konzept der Modulzwillinge in IoT Hub finden Sie unter [Grundlegendes zu Modulzwillingen](../iot-hub/iot-hub-devguide-module-twins.md).

## <a name="see-also"></a>Weitere Informationen

- [Übersicht über Defender für IoT](overview.md)
- [Bereitstellen von Sicherheits-Agents](how-to-deploy-agent.md)
- [Sicherheits-Agent-Authentifizierungsmethoden](concept-security-agent-authentication-methods.md)