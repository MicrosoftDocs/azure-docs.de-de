---
title: Auswählen und Bereitstellen eines Azure Security Center für IoT-Agents (Vorschauversion) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Security Center für IoT-Sicherheits-Agents auf IoT-Geräten auswählen und bereitstellen.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 5c76cce5a3d9add8a1dcd8acc439a70c4a8655a9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65200566"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Auswählen und Bereitstellen eines Sicherheits-Agents auf Ihrem IoT-Gerät

> [!IMPORTANT]
> Azure Security Center für IoT befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Security Center (ASC) für IoT bietet Referenzarchitekturen für Sicherheits-Agents, die Daten von IoT-Geräten überwachen und sammeln.
Weitere Informationen finden Sie unter [Sicherheits-Agent-Referenzarchitektur](security-agent-architecture.md).

Agents werden als Open-Source-Projekte entwickelt und sind in zwei Varianten verfügbar: <br> [C](https://aka.ms/iot-security-github-c) und [C#](https://aka.ms/iot-security-github-cs).

In diesem Artikel werden folgende Vorgehensweisen behandelt: 
> [!div class="checklist"]
> * Vergleichen der Varianten von Sicherheits-Agents
> * Identifizieren der unterstützten Agent-Plattformen
> * Auswählen der passenden Agent-Variante für Ihre Lösung

## <a name="understand-security-agent-options"></a>Grundlegendes zu den Varianten des Sicherheits-Agents

Jede Variante des ASC für IoT-Sicherheits-Agents hat den gleichen Funktionsumfang und unterstützt ähnliche Konfigurationsoptionen. 

Der C-basierte Sicherheits-Agent hat einen geringeren Speicherbedarf und ist die optimale Wahl für Geräte mit weniger verfügbaren Ressourcen. 

|     | C-basierter Sicherheits-Agent | C#-basierter Sicherheits-Agent |
| --- | ----------- | --------- |
| Open Source | Verfügbar unter der [MIT-Lizenz](https://en.wikipedia.org/wiki/MIT_License) in [GitHub](https://aka.ms/iot-security-github-cs) | Verfügbar unter der [MIT-Lizenz](https://en.wikipedia.org/wiki/MIT_License) in [GitHub](https://aka.ms/iot-security-github-c) |
| Programmier-/Entwicklungssprache    | C | C# |
| Unterstützte Windows-Plattformen? | Nein | Ja |
| Windows-Voraussetzungen | --- | [WMI](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| Unterstützte Linux-Plattformen? | Ja, x64 und x86 | Ja, nur x64 |
| Linux-Voraussetzungen | libunwind8, libcurl3, uuid-runtime, auditd, audispd-plugins | libunwind8, libcurl3, uuid-runtime, auditd, audispd-plugins, sudo, netstat, iptables |
| Speicherbedarf des Datenträgers | 10,5 MB | 90 MB |
| Speicherbedarf (im Durschnitt) | 5,5 MB | 33 MB |
| [Authentifizierung](concept-security-agent-authentication-methods.md) bei IoT Hub | Ja | Ja |
| [Sammlung](how-to-agent-configuration.md#supported-security-events) von Sicherheitsdaten | Ja | Ja |
| Ereignisaggregation | Ja | Ja |
| Remotekonfiguration über [Sicherheitsmodulzwilling](concept-security-module.md) | Ja | Ja |


## <a name="choose-an-agent-flavor"></a>Auswählen einer Agent-Variante 

Beantworten Sie die folgenden Fragen zu Ihren IoT-Geräten, um den passenden Agent auszuwählen:

- Sie verwenden _Windows Server_ oder _Windows IoT Core_? 

    [Bereitstellen eines C#-basierten Sicherheits-Agents unter Windows](how-to-deploy-windows-cs.md)

- Sie verwenden eine Linux-Distribution mit x86-Architektur? 

    [Bereitstellen eines C-basierten Sicherheits-Agents unter Linux](how-to-deploy-linux-c.md)

- Sie verwenden eine Linux-Distribution mit x64-Architektur?

    Sie können beide Agent-Varianten verwenden. <br>
    [Bereitstellen eines C-basierten Sicherheits-Agents unter Linux](how-to-deploy-linux-c.md) und/oder [Bereitstellen eines C#-basierten Sicherheits-Agents unter Linux](how-to-deploy-linux-cs.md)

Beide Agent-Varianten haben den gleichen Funktionsumfang und unterstützen ähnliche Konfigurationsoptionen.
Weitere Informationen finden Sie unter [Vergleich der Sicherheits-Agents](how-to-deploy-agent.md#understand-security-agent-options).

## <a name="supported-platforms"></a>Unterstützte Plattformen

Die folgende Liste enthält alle derzeit unterstützten Plattformen.

|ASC für IoT-Agent |Betriebssystem |Architecture |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64|
|C|Debian 9 |   x64, x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core Build 17763 |x64|

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Konfigurationsoptionen finden Sie in der Schrittanleitung für die Agent-Konfiguration. 
> [!div class="nextstepaction"]
> [Schrittanleitung für die Agent-Konfiguration](./how-to-agent-configuration.md)
