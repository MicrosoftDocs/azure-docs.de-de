---
title: Grundlegendes zur Azure Security Center for IoT-Sicherheits-Agent-Architektur (Vorschauversion) | Microsoft-Dokumentation
description: Grundlegendes zur Sicherheits-Agent-Architektur für die im Azure Security Center für IoT-Dienst verwendeten Agents.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 3c05b7e9b1c6d1b9214da168f7abfcbb322f8f6d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65192515"
---
# <a name="security-agent-reference-architecture"></a>Sicherheits-Agent-Referenzarchitektur

> [!IMPORTANT]
> Azure Security Center für IoT befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Azure Security Center (ASC) für IoT bietet eine Referenzarchitektur für Sicherheits-Agents, die Sicherheitsdaten über IoT Hub protokollieren, verarbeiten, aggregieren und senden.

Sicherheits-Agents sind für den Einsatz in einer eingeschränkten IoT-Umgebung konzipiert und können in Bezug auf die Werte, die sie im Vergleich zu den von ihnen verbrauchten Ressourcen bereitstellen, sehr flexibel angepasst werden.

Sicherheits-Agents unterstützen die folgenden Features:

- Sammeln von Rohdaten zu Sicherheitsereignissen aus dem zugrunde liegenden Betriebssystem (Linux, Windows). Weitere Informationen zu verfügbaren Sicherheitsdatensammlern finden Sie unter [ASC für die IoT-Agent-Konfiguration](how-to-agent-configuration.md).

- Aggregieren von Rohdaten zu Sicherheitsereignissen in Nachrichten, die über IoT Hub gesendet werden.

- Authentifizieren mit vorhandener Geräteidentität oder einer dedizierten Modulidentität. Weitere Informationen finden Sie unter [Sicherheits-Agent-Authentifizierungsmethoden](concept-security-agent-authentication-methods.md).

- Remotekonfigurieren durch Verwendung des Modulzwillings **azureiotsecurity**. Weitere Informationen finden Sie unter [Konfigurieren eines ASC für IoT-Agents](how-to-agent-configuration.md).

ASC für IoT-Sicherheits-Agents werden als Open Source-Projekte entwickelt und sind über GitHub verfügbar: 

- [ASC für IoT – C-basierter Agent](https://github.com/Azure/Azure-IoT-Security-Agent-C) 
- [ASC für IoT – C#-basierter Agent](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Vom Agent unterstützte Plattformen

ASC für IoT bietet verschiedene Installer-Agents für 32-Bit- und 64-Bit-Windows sowie für 32-Bit- und 64-Bit-Linux. Stellen Sie anhand der folgenden Tabelle sicher, dass Sie den richtigen Agent-Installer für jedes Ihrer Geräte verwenden:

| 32 oder 64 Bit | Linux | Windows |    Details|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32 Bit  | C  | C#  ||
| 64 Bit  | C# oder C           | C#      | Verwenden Sie den C-Agent für Geräte mit minimalen Ressourcen|

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie die ASC für IoT-Sicherheits-Agent-Architektur und die verfügbaren Installer kennen gelernt.

Um mit der ASC für IoT-Bereitstellung fortzufahren, lesen Sie die folgenden Artikel:

- Machen Sie sich mit den [Methoden der Sicherheits-Agent-Authentifizierung](concept-security-agent-authentication-methods.md) vertraut.
- Wählen Sie einen [Sicherheits-Agent](how-to-deploy-agent.md) aus, und stellen Sie ihn bereit.
- Lesen Sie die ASC für IoT-[Dienstvoraussetzungen](service-prerequisites.md).
- Erfahren Sie, wie Sie [den ASC für IoT-Dienst in Ihrer IoT Hub-Instanz aktivieren](quickstart-onboard-iot-hub.md).
- Unter [ASC für IoT – Häufig gestellte Fragen](resources-frequently-asked-questions.md) erfahren Sie mehr über den Dienst.
