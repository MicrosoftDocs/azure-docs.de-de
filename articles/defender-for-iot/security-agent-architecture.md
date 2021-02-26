---
title: Security agents overview (Sicherheits-Agents (Übersicht))
description: Grundlegendes zur Sicherheits-Agent-Architektur für die im Azure Defender für IoT-Dienst verwendeten Agents.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: ff837fe88f878c522366b2b6bc19a1ef3954b667
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820652"
---
# <a name="security-agent-reference-architecture"></a>Sicherheits-Agent-Referenzarchitektur

Azure Defender für IoT bietet eine Referenzarchitektur für Sicherheits-Agents, die Sicherheitsdaten über IoT Hub protokollieren, verarbeiten, aggregieren und senden.

Sicherheits-Agents sind für den Einsatz in einer eingeschränkten IoT-Umgebung konzipiert und können in Bezug auf die Werte, die sie im Vergleich zu den von ihnen verbrauchten Ressourcen bereitstellen, sehr flexibel angepasst werden.

Sicherheits-Agents unterstützen die folgenden Features:

- Authentifizieren mit vorhandener Geräteidentität oder einer dedizierten Modulidentität. Weitere Informationen finden Sie unter  [Authentifizierungsmethoden des Sicherheits-Agents](concept-security-agent-authentication-methods.md).

- Sammeln von Rohdaten zu Sicherheitsereignissen aus dem zugrunde liegenden Betriebssystem (Linux, Windows). Weitere Informationen zu verfügbaren Sicherheitsdatensammlern finden Sie unter [für die IoT-Agent-Konfiguration](how-to-agent-configuration.md).

- Aggregieren von Rohdaten zu Sicherheitsereignissen in Nachrichten, die über IoT Hub gesendet werden.

- Remotekonfigurieren durch Verwendung des Modulzwillings **azureiotsecurity**. Weitere Informationen finden Sie unter [Konfigurieren eines Defender für IoT-Agents](how-to-agent-configuration.md).

Sicherheits-Agents von Defender für IoT werden als Open-Source-Projekte entwickelt und sind über GitHub verfügbar:

- [Defender für IoT – C-basierter Agent](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Defender für IoT – C#-basierter Agent](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Vom Agent unterstützte Plattformen

Defender für IoT bietet verschiedene Installer-Agents für 32-Bit- und 64-Bit-Windows sowie für 32-Bit- und 64-Bit-Linux. Stellen Sie anhand der folgenden Tabelle sicher, dass Sie den richtigen Agent-Installer für jedes Ihrer Geräte verwenden:

| Aufbau | Linux | Windows | Details |
|--|--|--|--|
| 32-Bit | C | C# |  |
| 64-Bit | C# oder C | C# | Wir empfehlen, den C-Agent für Geräte mit eingeschränkteren oder minimalen Geräteressourcen zu verwenden. |


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel erhalten Sie eine allgemeine Übersicht über die Architektur des Defender für IoT-Sicherheitsmoduls und die verfügbaren Installationsprogramme.

Um mit der Defender für IoT-Bereitstellung fortzufahren, lesen Sie die folgenden Artikel:

- Grundlegendes zu den [Authentifizierungsmethoden des Sicherheits-Agents](concept-security-agent-authentication-methods.md)
- Wählen Sie einen [Sicherheits-Agent](how-to-deploy-agent.md) aus, und stellen Sie ihn bereit.
- Lesen Sie die [Systemvoraussetzungen](quickstart-system-prerequisites.md) für Defender für IoT.
- Erfahren Sie, wie Sie [den Defender für IoT-Dienst in Ihrer IoT Hub-Instanz aktivieren](quickstart-onboard-iot-hub.md).
- Unter [für IoT – Häufig gestellte Fragen](resources-frequently-asked-questions.md) erfahren Sie mehr über den Dienst.
