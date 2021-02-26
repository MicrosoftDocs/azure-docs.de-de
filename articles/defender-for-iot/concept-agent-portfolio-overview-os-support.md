---
title: Agent-Portfolioübersicht und Betriebssystemunterstützung (Vorschau)
description: Azure Defender für IoT bietet ein umfangreiches Portfolio an gerätetypbasierten Agents.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: f731b034b5d4f795bae51107e9ff4e2e90788d7d
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99810069"
---
# <a name="agent-portfolio-overview-and-os-support-preview"></a>Agent-Portfolioübersicht und Betriebssystemunterstützung (Vorschau)

Azure Defender für IoT bietet ein umfangreiches Portfolio an gerätetypbasierten Agents. 

## <a name="standalone-agent"></a>Eigenständiger Agent

Der eigenständige Agent deckt die meisten Linux-Betriebssysteme ab. Diese können als binäres Paket oder als Quellcode bereitgestellt werden, der in die Firmware integriert werden kann, und ermöglichen Änderungen und Anpassungen gemäß den Kundenanforderungen. Beispiel für die Betriebssystemunterstützung: 

| Betriebssystem | AMD64 | ARM32v7 |
|--|--|--|
| Debian 9 | ✓ | ✓ |
| Ubuntu 18.04 | ✓ |  |
| Ubuntu 20.04 | ✓ |  |

Wenn Sie weitere Informationen oder Angaben zur Betriebssystemunterstützung benötigen oder Zugriff auf den Quellcode anfordern möchten, um ihn in die Firmware des Geräts integrieren zu können, wenden Sie sich entweder an Ihren Konto-Manager, oder senden Sie eine E-Mail an <defender_micro_agent@microsoft.com>. 

## <a name="azure-rtos-micro-agent"></a>Micro-Agent für Azure RTOS

Der Micro-Agent für Azure Defender für IoT bietet eine umfassende Lightweight-Sicherheitslösung für Geräte, die Azure RTOS verwenden. Der Micro-Agent für Azure Defender für IoT deckt allgemeine Bedrohungen und potenziell schädliche Aktivitäten auf Geräten mit Echtzeitbetriebssystem (Real-Time Operating System, RTOS) ab. Der Micro-Agent ist in die Komponente „Azure RTOS NetX Duo“ integriert und überwacht die Netzwerkaktivität des Geräts. 

Der Micro-Agent für Azure Defender für IoT ist in die Komponente „Azure RTOS NetX Duo“ integriert und überwacht die Netzwerkaktivität des Geräts. Der Micro-Agent besteht aus einer umfassenden Lightweight-Sicherheitslösung zum Schutz vor allgemeinen Bedrohungen und potenziell schädlichen Aktivitäten auf Geräten mit Echtzeitbetriebssystem (Real-Time Operating System, RTOS).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in der [Übersicht über den eigenständigen Micro-Agent (Vorschau)](concept-standalone-micro-agent-overview.md).