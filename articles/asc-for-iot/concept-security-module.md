---
title: Grundlegendes zu Sicherheitsmodulzwillingen für Azure Security Center für IoT (Vorschauversion) | Microsoft-Dokumentation
description: Lernen Sie das Konzept der Sicherheitsmodulzwillinge kennen, und erfahren Sie, wie sie in Azure Security Center für IoT verwendet werden.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: de0f21fbd847ff801308523f48e9d0c8a91880fa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65198379"
---
# <a name="security-module"></a>Sicherheitsmodul

> [!IMPORTANT]
> Azure Security Center für IoT befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Artikel wird erläutert, wie Azure Security Center (ASC) für IoT Gerätezwillinge und -module verwendet. 

## <a name="device-twins"></a>Gerätezwillinge

Für in Azure erstellte IoT-Lösungen spielen Gerätezwillinge eine wichtige Rolle – sowohl bei der Geräteverwaltung als auch bei der Prozessautomatisierung.  

ASC für IoT ermöglicht die vollständige Integration in Ihre vorhandene Plattform für die IoT-Geräteverwaltung. Auf diese Weise können Sie Ihren Gerätesicherheitsstatus verwalten und die vorhandenen Funktionen für die Gerätesteuerung nutzen. Die Integration wird erreicht, indem der Mechanismus für IoT Hub-Zwillinge verwendet wird.  

Erfahren Sie mehr über das Konzept der [Gerätezwillinge](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) in Azure IoT Hub. 

## <a name="security-module-twins"></a>Sicherheitsmodulzwillinge

ASC für IoT verwaltet für jedes Gerät im Dienst einen Sicherheitsmodulzwilling.
Der Sicherheitsmodulzwilling enthält alle für die Gerätesicherheit relevanten Informationen für jedes einzelne Gerät in Ihrer Lösung.
Die Gerätesicherheitseigenschaften werden in einem dedizierten Sicherheitsmodulzwilling gepflegt, um eine sicherere Kommunikation zu gewährleisten sowie Updates und Wartungen zu ermöglichen, die weniger Ressourcen erfordern.  

Unter [Erstellen eines Sicherheitsmodulzwillings](quickstart-create-security-twin.md) und [Konfigurieren von Sicherheits-Agents](how-to-agent-configuration.md) erfahren Sie, wie Sie den Zwilling erstellen, anpassen und konfigurieren. Weitere Informationen zum Konzept der Modulzwillinge in IoT Hub finden Sie unter [Grundlegendes zu Modulzwillingen](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins). 
 

## <a name="see-also"></a>Weitere Informationen
- [ASC für IoT (Vorschauversion)](overview.md)
- [Bereitstellen von Sicherheits-Agents](how-to-deploy-agent.md)
- [Sicherheits-Agent-Authentifizierungsmethoden](concept-security-agent-authentication-methods.md)