---
title: Architektur einer Agent-basierten Lösung
description: Erfahren Sie mehr über die Architektur mit Agents und den Informationsfluss von Azure Defender für IoT.
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
ms.date: 1/25/2021
ms.author: shhazam
ms.openlocfilehash: 3a7dabfed86dab463b38ae45278ead7f5acb5ef9
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521950"
---
# <a name="agent-based-solution-for-device-builders"></a>Agent-basierte Lösung für Gerätehersteller

In diesem Artikel wird die funktionale Systemarchitektur der Defender für IoT-Lösung mit Agents beschrieben. Azure Defender für IoT stellt für die Anforderungen in Ihrer Umgebung zwei Gruppen von Funktionen bereit: Unternehmenslösung ohne Agent und Agent-basierte Lösung für Gerätehersteller.

## <a name="iot-hub-built-in-security"></a>Integrierte IoT Hub-Sicherheit

Defender für IoT ist in jedem neu erstellten IoT-Hub standardmäßig aktiviert. Defender für IoT bietet eine Überwachung, Empfehlungen und Warnungen in Echtzeit, ohne dass eine Agent-Installation auf Geräten erforderlich ist. Defender für IoT wendet erweiterte Analysen auf die Metadaten angemeldeter IoT-Hubs an, um Ihre Feldgeräte und IoT-Hubs zu analysieren und zu schützen. 

## <a name="defender-for-iot-micro-agent"></a>Micro-Agent für Defender für IoT 

Der Micro-Agent für Defender für IoT bietet umfassenden Sicherheitsschutz und Einblick in das Geräteverhalten. Er sammelt, aggregiert und analysiert Rohdaten zu Sicherheitsereignissen auf Ihren Geräten. Sicherheitsereignis-Rohdaten können IP-Verbindungen, die Prozesserstellung, Benutzeranmeldungen und andere sicherheitsrelevante Informationen enthalten. Defender für IoT-Geräte-Agents aggregieren die Ereignisdaten darüber hinaus, um hohe Netzwerkdurchsätze zu vermeiden. Die Agents sind in hohem Maße anpassbar, sodass Sie sie für bestimmte Aufgaben wie das Senden ausschließlich wichtiger Informationen mit dem dringlichsten SLA oder für das Aggregieren umfangreicher Sicherheitsinformationen und Kontextdaten zu größeren Segmenten verwenden und auf diese Weise höhere Servicekosten vermeiden können.

Geräte-Agents und andere Anwendungen verwenden das **Azure Security Center SDK zum Senden von Sicherheitsmeldungen**, um Sicherheitsinformationen an Azure IoT Hub zu senden. IoT Hub ruft diese Informationen ab und leitet sie an den Defender für IoT-Dienst weiter.

Sobald der Defender für IoT-Dienst aktiviert ist, sendet IoT Hub zusätzlich zu den weitergeleiteten Daten alle seine internen Daten für die Analyse durch Defender für IoT. Diese Daten umfassen Cloudvorgangsprotokolle von Geräten, Geräteidentitäten und die Hubkonfiguration. Alle diese Informationen tragen dazu bei, die Defender für IoT-Analysepipeline zu erstellen.

Die Defender für IoT-Analysepipeline empfängt ferner weitere Threat Intelligence-Datenströme aus verschiedenen Quellen innerhalb von Microsoft sowie von Microsoft-Partnern. Die gesamte Defender für IoT-Analysepipeline arbeitet mit sämtlichen für den Dienst erstellten Kundenkonfigurationen (wie benutzerdefinierte Warnungen und die Verwendung des SDK zum Senden von Sicherheitsmeldungen).

Bei der Verwendung der Analysepipeline kombiniert Defender für IoT alle Informationsdatenströme, um direkt umsetzbare Empfehlungen und Warnungen zu generieren. Die Pipeline enthält sowohl benutzerdefinierte Regeln, die von Sicherheitsanalytikern und Experten erstellt wurden, als auch nach Abweichungen vom standardmäßigen Geräteverhalten suchende Machine Learning-Modelle und Risikoanalysen.

Defender für IoT-Empfehlungen und -Warnungen (Ausgabe der Analysepipeline) werden in den Log Analytics-Arbeitsbereich des jeweiligen Kunden geschrieben. Durch die Einbeziehung der Rohereignisse in den Arbeitsbereich sowie der Warnungen und Empfehlungen werden tiefgreifende Untersuchungen und Abfragen anhand der genauen Details der erkannten verdächtigen Aktivitäten ermöglicht.

:::image type="content" source="media/architecture/micro-agent-architecture.png" alt-text="Micro-Agent-Architektur":::

## <a name="next-steps"></a>Nächste Schritte

[Häufig gestellte Fragen zu Defender für IoT](resources-frequently-asked-questions.md)

[Systemanforderungen](quickstart-system-prerequisites.md)
