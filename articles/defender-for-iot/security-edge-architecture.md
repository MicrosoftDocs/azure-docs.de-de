---
title: Defender für IoT-Sicherheitsmoduls für IoT Edge
description: Grundlegendes zur Architektur und den Funktionen des Azure Defender für IoT-Sicherheitsmoduls für IoT Edge.
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
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 3e653983b39e8eb4ac13ad840ca53e4ab6d1cfc7
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820618"
---
# <a name="azure-defender-for-iot-edge-security-module"></a>Azure Defender für IoT Edge-Sicherheitsmodul

[Azure IoT Edge](../iot-edge/index.yml) umfasst leistungsstarke Funktionen zum Verwalten und Ausführen von Geschäftsworkflows am Edge.
Seine Schlüsselrolle in IoT-Umgebungen macht IoT Edge zu einem besonders attraktiven Ziel für böswillige Akteure.

Das Sicherheitsmodul von Defender für IoT stellt eine umfassende Sicherheitslösung für Ihr IoT Edge-Gerät bereit.
Das Defender für IoT-Modul sammelt, aggregiert und analysiert Sicherheitsrohdaten von Ihrem Betriebs- und Containersystem und gibt verwertbare Sicherheitsempfehlungen und -warnungen aus.

Ähnlich wie die Defender für IoT-Sicherheits-Agents für IoT-Geräte ist das Defender für IoT Edge-Modul durch seinen Modulzwilling hochgradig anpassbar.
Weitere Informationen finden Sie unter [Konfigurieren Ihres Agents](how-to-agent-configuration.md).

Das Defender für IoT-Sicherheitsmodul für IoT Edge umfasst die folgenden Funktionen:

- Sammeln von Sicherheitsrohereignissen vom zugrunde liegenden Betriebssystem (Linux) und den IoT Edge-Containersystemen

  Weitere Informationen zu verfügbaren Sicherheitsdatensammlern finden Sie unter [Defender für IoT-Agent-Konfiguration](how-to-agent-configuration.md).

- Analyse von IoT Edge-Bereitstellungsmanifesten

- Aggregieren von Sicherheitsrohereignissen in Meldungen, die über [IoT Edge-Hub](../iot-edge/iot-edge-runtime.md#iot-edge-hub) gesendet werden.

- Entfernen der Konfiguration über den Sicherheitsmodulzwilling

  Weitere Informationen finden Sie unter [Konfigurieren eines Defender für IoT-Agents](how-to-agent-configuration.md).

Das Defender für IoT-Sicherheitsmodul für IoT Edge wird im privilegierten Modus unter IoT Edge ausgeführt.
Der privilegierte Modus ist erforderlich, damit das Modul das Betriebssystem und andere IoT Edge-Module überwachen kann.

## <a name="module-supported-platforms"></a>Vom Modul unterstützte Plattformen

Das Defender für IoT-Sicherheitsmodul für IoT Edge ist derzeit nur für Linux verfügbar.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Informationen zur Architektur und den Funktionen des Defender für IoT-Sicherheitsmoduls für IoT Edge erhalten.

Um mit der Defender für IoT-Bereitstellung fortzufahren, lesen Sie die folgenden Artikel:

- Bereitstellen des [Sicherheitsmoduls für IoT Edge](how-to-deploy-edge.md)
- Erfahren Sie, wie Sie [Ihr Sicherheitsmodul konfigurieren](how-to-agent-configuration.md).
- Sehen Sie sich die Informationen unter [Defender for IoT Horizon](resources-manage-proprietary-protocols.md) an.
- Erfahren Sie, wie Sie [den Defender für IoT-Dienst in Ihrer IoT Hub-Instanz aktivieren](quickstart-onboard-iot-hub.md).
- Unter [für IoT – Häufig gestellte Fragen](resources-frequently-asked-questions.md) erfahren Sie mehr über den Dienst.