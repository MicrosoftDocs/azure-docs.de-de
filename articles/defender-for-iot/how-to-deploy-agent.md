---
title: Auswählen und Bereitstellen von Sicherheits-Agents
description: Erfahren Sie, wie Sie Defender für IoT-Sicherheits-Agents auf IoT-Geräten auswählen und bereitstellen.
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
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 2634ed819b8818632c58b9e471b0f26190e5f16b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96339964"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Auswählen und Bereitstellen eines Sicherheits-Agents auf Ihrem IoT-Gerät

Defender für IoT bietet Referenzarchitekturen für Sicherheits-Agents, die Daten von IoT-Geräten überwachen und sammeln.
Weitere Informationen finden Sie unter [Sicherheits-Agent-Referenzarchitektur](security-agent-architecture.md).

Agents werden als Open-Source-Projekte entwickelt und sind in zwei Varianten verfügbar: <br> [C](https://aka.ms/iot-security-github-c) und [C#](https://aka.ms/iot-security-github-cs).

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Vergleichen der Varianten von Sicherheits-Agents
> * Identifizieren der unterstützten Agent-Plattformen
> * Auswählen der passenden Agent-Variante für Ihre Lösung

## <a name="understand-security-agent-options"></a>Grundlegendes zu den Varianten des Sicherheits-Agents

Jede Variante des Defender für IoT-Sicherheits-Agents hat den gleichen Funktionsumfang und unterstützt ähnliche Konfigurationsoptionen.

Der C-basierte Sicherheits-Agent hat einen geringeren Speicherbedarf und ist die optimale Wahl für Geräte mit weniger verfügbaren Ressourcen.

|     | C-basierter Sicherheits-Agent | C#-basierter Sicherheits-Agent |
| --- | ----------- | --------- |
| **Open-Source** | Verfügbar unter der [MIT-Lizenz](https://en.wikipedia.org/wiki/MIT_License) auf [GitHub](https://aka.ms/iot-security-github-c) | Verfügbar unter der [MIT-Lizenz](https://en.wikipedia.org/wiki/MIT_License) auf [GitHub](https://aka.ms/iot-security-github-cs) |
| **Programmier-/Entwicklungssprache**    | C | C# |
| **Unterstützte Windows-Plattformen?** | Nein | Ja |
| **Windows-Voraussetzungen** | --- | [WMI](/windows/desktop/wmisdk/) |
| **Unterstützte Linux-Plattformen?** | Ja, x64 und x86 | Ja, nur x64 |
| **Linux-Voraussetzungen** | libunwind8, libcurl3, uuid-runtime, auditd, audispd-plugins | libunwind8, libcurl3, uuid-runtime, auditd, audispd-plugins, sudo, netstat, iptables |
| **Speicherbedarf des Datenträgers** | 10,5 MB | 90 MB |
| **Speicherbedarf (im Durschnitt)** | 5,5 MB | 33 MB |
| **[Authentifizierung](concept-security-agent-authentication-methods.md) bei IoT Hub** | Ja | Ja |
| **[Sammlung](how-to-agent-configuration.md#supported-security-events) von Sicherheitsdaten** | Ja | Ja |
| **Ereignisaggregation** | Ja | Ja |
| **Remotekonfiguration über [Sicherheitsmodulzwilling](concept-security-module.md)** | Ja | Ja |

## <a name="security-agent-installation-guidelines"></a>Installationsrichtlinien für den Sicherheits-Agent

Für **Windows**: Das Skript „InstallSecurityAgent.ps1“ muss über ein Administratorfenster von PowerShell ausgeführt werden.

Für **Linux**: „InstallSecurityAgent.sh“ muss als Superuser ausgeführt werden. Es wird empfohlen, dem Installationsbefehl „sudo“ voranzustellen.

## <a name="choose-an-agent-flavor"></a>Auswählen einer Agent-Variante

Beantworten Sie die folgenden Fragen zu Ihren IoT-Geräten, um den passenden Agent auszuwählen:

- Sie verwenden _Windows Server_ oder _Windows IoT Core_?

    [Bereitstellen eines C#-basierten Sicherheits-Agents unter Windows](how-to-deploy-windows-cs.md)

- Sie verwenden eine Linux-Distribution mit x86-Architektur?

    [Bereitstellen eines C-basierten Sicherheits-Agents unter Linux](how-to-deploy-linux-c.md)

- Sie verwenden eine Linux-Distribution mit x64-Architektur?

    Beide Agent-Varianten können verwendet werden. <br>
    [Bereitstellen eines C-basierten Sicherheits-Agents unter Linux](how-to-deploy-linux-c.md) und/oder [Bereitstellen eines C#-basierten Sicherheits-Agents unter Linux](how-to-deploy-linux-cs.md)

Beide Agent-Varianten haben den gleichen Funktionsumfang und unterstützen ähnliche Konfigurationsoptionen.
Weitere Informationen finden Sie unter [Vergleich der Sicherheits-Agents](how-to-deploy-agent.md#understand-security-agent-options).

## <a name="supported-platforms"></a>Unterstützte Plattformen

Die folgende Liste enthält alle derzeit unterstützten Plattformen.

|Defender für IoT-Agent |Betriebssystem |Aufbau |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |    x64|
|C|Ubuntu 18.04 |    x64, ARMv7|
|C|Debian 9 |    x64, x86|
|C#|Ubuntu 16.04     |x64|
|C#|Ubuntu 18.04    |x64, ARMv7|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core, Build 17763    |x64|
|

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Konfigurationsoptionen finden Sie in der Schrittanleitung für die Agent-Konfiguration.
> [!div class="nextstepaction"]
> [Schrittanleitung für die Agent-Konfiguration](./how-to-agent-configuration.md)