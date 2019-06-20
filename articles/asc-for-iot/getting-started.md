---
title: Erste Schritte mit der Vorschauversion von Azure Security Center (ASC) für IoT | Microsoft-Dokumentation
description: Erfahren Sie mehr über die ersten Schritte mit dem grundlegenden Workflow der Features und des Diensts von Azure Security Center für IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: bdc5e858286d7db03281408cf3ed00e0760e0c3a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65200639"
---
# <a name="get-started-with-azure-security-center-for-iot"></a>Erste Schritte mit Azure Security Center für IoT 

> [!IMPORTANT]
> Azure Security Center für IoT befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Artikel erfahren Sie mehr zu den verschiedenen Bausteinen des Diensts von Azure Security Center (ASC) für IoT und den ersten Schritten bei der [Aktivierung des Diensts](quickstart-onboard-iot-hub.md). 

ASC für IoT lässt sich nahtlos in IoT Hub integrieren, um Sicherheitsanalysen für die IoT Hub-Konfiguration, die Geräteidentität und Kommunikationsmuster für Hubgeräte bereitzustellen.
Für noch mehr Sicherheit bietet ASC für IoT eine Agent-basierte Sammlung von Sicherheitsdaten aus Ihren IoT-Geräten.

## <a name="asc-for-iot-seamless-iot-hub-integration"></a>Nahtlose IoT Hub-Integration von ASC für IoT

Wenn Sie Ihre persönlichen IoT-Geräte schützen möchten, ist es erforderlich, dass Daten direkt von den Geräten oder aus dem zugehörigen Netzwerk gesammelt werden können. Hierfür bietet ASC für IoT eine ganze Reihe von Sicherheits-Agents mit geringem Speicherbedarf, die eine Geräteüberwachung und -härtung ermöglichen.

Mit der Vorschauversion von ASC für IoT erhalten Sie eine Referenzarchitektur für Linux- und Windows-Sicherheits-Agents, die sowohl in C# als auch in C verfügbar ist.
Die Agents verarbeiten unformatierte Ereignissammlungen aus dem Betriebssystem des Geräts, ermöglichen eine Ereignisaggregation zur Kostensenkung und erlauben die Konfiguration über einen Modulzwilling des Geräts.
Sicherheitsmeldungen werden über Ihre IoT Hub-Instanz an die Analysedienste von ASC für IoT gesendet.

## <a name="asc-for-iot-basics"></a>Grundlagen von ASC für IoT

Wählen Sie das Workflowszenario aus, das die Anforderungen Ihrer IoT-Geräte- und -umgebungen am besten erfüllt:

### <a name="get-started-with-asc-for-iot-seamless-iot-hub-integration"></a>Erste Schritte mit der nahtlosen IoT Hub-Integration von ASC für IoT 

>[!Note]
>Dieser Workflow bietet Ihnen die Möglichkeit, den Dienst ohne Sicherheits-Agents von ASC für IoT zu verwenden. 

Zur Überwachung des Identitätsverwaltung Ihres Geräts und der Kommunikationsmuster zwischen Gerät und Cloud sowie Cloud und Gerät verwenden Sie den folgenden grundlegenden Workflow zum Testen und Starten des Diensts: 

1. [Aktivieren des Diensts von ASC für IoT in Ihrer IoT Hub-Instanz](quickstart-onboard-iot-hub.md)
1. Wenn für Ihre IoT Hub-Instanz keine Geräte registriert sind, [registrieren Sie ein neues Gerät](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Erstellen Sie ein „azureiotsecurity“-Sicherheitsmodul](quickstart-create-security-twin.md) für Ihre Geräte. 
1. Definieren Sie das normale Geräte- und Systemverhalten über [benutzerdefinierte Benachrichtigungen](quickstart-create-custom-alerts.md). 
1. Führen Sie Systemtests durch, um den Dienst- und Gerätestatus zu überprüfen. 
1. Gehen Sie [Benachrichtigungen](concept-security-alerts.md), [Empfehlungen](concept-recommendations.md) und die [ausführlichen Informationen zu Log Analytics](how-to-security-data-access.md) mit IoT Hub durch. 


### <a name="get-started-with-asc-for-iot-security-agents"></a>Erste Schritte mit den Sicherheits-Agents von ASC für IoT

Nutzen Sie die erweiterten Sicherheitsfunktionen von ASC für IoT, wie z.B. die Überwachung von Remoteverbindungen, aktiven Anwendungen und Anmeldeereignissen sowie bewährten Methoden bei der Betriebssystemkonfiguration, indem Sie den Dienst mit dem folgenden grundlegenden Workflow testen und aktivieren: 

1. [Aktivieren des Diensts von ASC für IoT für Ihre IoT Hub-Instanz](quickstart-onboard-iot-hub.md)
1. Wenn für Ihre IoT Hub-Instanz keine Geräte registriert sind, [registrieren Sie ein neues Gerät](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Erstellen Sie ein azureiotsecurity-Sicherheitsmodul](quickstart-create-security-twin.md) für Ihre Geräte.
1. Um den Agent auf einem simulierten Azure-Gerät anstatt auf einem echten Gerät zu installieren, [starten Sie eine neue Azure-VM](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) in einer verfügbaren Zone. 
1. [Stellen Sie einen Sicherheits-Agent von ASC für IoT auf Ihrem IoT-Gerät bereit](how-to-deploy-linux-cs.md), oder stellen Sie eine neue VM bereit.
1. Folgen Sie den Anweisungen für [trigger_events](https://aka.ms/iot-security-github-trigger-events), um einen harmlosen Angriff zu simulieren.
1. Überprüfen Sie die Benachrichtigungen von ASC für IoT als Reaktion auf den im vorherigen Schritt simulierten Angriff. Beginnen Sie mit der Überprüfung erst fünf Minuten, nachdem Sie das Skript ausgeführt haben.
1. Gehen Sie [Benachrichtigungen](concept-security-alerts.md), [Empfehlungen](concept-recommendations.md) und die [ausführlichen Informationen zu Log Analytics](how-to-security-data-access.md) mit IoT Hub durch. 

## <a name="next-steps"></a>Nächste Schritte

- Aktivieren von [ASC für IoT](quickstart-onboard-iot-hub.md)
- [Quickstart: Configure your IoT solution](quickstart-configure-your-solution.md) (Schnellstart: Konfigurieren Ihrer IoT-Lösung)
- [Quickstart: Create an azureiotsecurity module twin](quickstart-create-security-twin.md) (Schnellstart: Erstellen eines azureiotsecurity-Modulzwillings)
- [Quickstart: Create custom alerts](quickstart-create-custom-alerts.md) (Schnellstart: Erstellen benutzerdefinierter Benachrichtigungen)
- [Select and deploy a security agent on your IoT device](how-to-deploy-agent.md) (Auswählen und Bereitstellen eines Sicherheits-Agents auf Ihrem IoT-Gerät)
