---
title: Grundlegendes zum Azure Security Center für das IoT-Sicherheitsmodul für IoT Edge | Microsoft-Dokumentation
description: Grundlegendes zur Architektur und den Funktionen des Azure Security Center für IoT-Sicherheitsmoduls für IoT Edge.
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
ms.openlocfilehash: 847d6238bd42dc7da723dcc9acd47ed09c16dbf9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65200580"
---
# <a name="azure-iot-edge-security-module"></a>Azure IoT Edge-Sicherheitsmodul

> [!IMPORTANT]
> Azure Security Center für IoT befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) umfasst leistungsstarke Funktionen zum Verwalten und Ausführen von Geschäftsworkflows am Edge.
Seine Schlüsselrolle in IoT-Umgebungen macht IoT Edge zu einem besonders attraktiven Ziel für böswillige Akteure.

Das Azure Security Center (ASC) für IoT-Sicherheitsmodul bietet eine umfassende Sicherheitslösung für Ihre IoT Edge-Geräte.
Das ASC für IoT-Modul sammelt, aggregiert und analysiert Sicherheitsrohdaten von Ihrem Betriebs- und Containersystem und gibt verwertbare Sicherheitsempfehlungen und -warnungen aus.

Ähnlich wie die ASC für IoT-Sicherheits-Agents für IoT-Geräte ist das ASC für IoT Edge-Modul durch seinen Modulzwilling hochgradig anpassbar.
Weitere Informationen finden Sie unter [Konfigurieren Ihres Agents](how-to-agent-configuration.md).

Das ASC für IoT-Sicherheitsmodul für IoT Edge umfasst die folgenden Funktionen:

- Sammeln von Sicherheitsrohereignissen vom zugrunde liegenden Betriebssystem (Linux) und den IoT Edge-Containersystemen
  
  Weitere Informationen zu verfügbaren Sicherheitsdatensammlern finden Sie unter [Konfigurieren eines ASC für IoT-Agents](how-to-agent-configuration.md).

- Analyse von IoT Edge-Bereitstellungsmanifesten

- Aggregieren von Sicherheitsrohereignissen in Meldungen, die über [IoT Edge-Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub) gesendet werden.

- Entfernen der Konfiguration über den Sicherheitsmodulzwilling

  Weitere Informationen finden Sie unter [Konfigurieren eines ASC für IoT-Agents](how-to-agent-configuration.md).

Das ASC für IoT-Sicherheitsmodul für IoT Edge wird im privilegierten Modus unter IoT Edge ausgeführt.
Der privilegierte Modus ist erforderlich, damit das Modul das Betriebssystem und andere IoT Edge-Module überwachen kann.

## <a name="agent-supported-platforms"></a>Vom Agent unterstützte Plattformen

Das ASC für IoT-Sicherheitsmodul für IoT Edge ist derzeit nur für Linux verfügbar.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Informationen zur Architektur und den Funktionen des ASC für IoT-Sicherheitsmoduls für IoT Edge erhalten.

Um mit der ASC für IoT-Bereitstellung fortzufahren, lesen Sie die folgenden Artikel:

- Bereitstellen des [Sicherheitsmoduls für IoT Edge](how-to-deploy-edge.md)
- Erfahren Sie, wie Sie [Ihr Sicherheitsmodul konfigurieren](how-to-agent-configuration.md).
- Lesen Sie die ASC für IoT-[Dienstvoraussetzungen](service-prerequisites.md).
- Erfahren Sie, wie Sie [den ASC für IoT-Dienst in Ihrer IoT Hub-Instanz aktivieren](quickstart-onboard-iot-hub.md).
- Unter [ASC für IoT – Häufig gestellte Fragen](resources-frequently-asked-questions.md) erfahren Sie mehr über den Dienst.