---
title: 'Sicherheitsmodul für Azure RTOS: Übersicht'
description: Erfahren Sie mehr über die Unterstützung und Implementierung des Sicherheitsmoduls für Azure RTOS als Teil von Azure Defender für IoT.
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
ms.date: 01/14/2021
ms.author: shhazam
ms.openlocfilehash: 47b2b49a21a82be13c987b95c7c775f5014a532e
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809081"
---
# <a name="overview-defender-for-iot-security-module-for-azure-rtos-preview"></a>Übersicht: Defender für IoT-Sicherheitsmodul für Azure RTOS (Vorschau)

Das Azure Defender für IoT-Micro-Modul bietet eine umfassende Sicherheitslösung für Geräte, die Azure RTOS verwenden. Es deckt allgemeine Bedrohungen und potenziell schädliche Aktivitäten auf Echtzeitbetriebssystem-Geräten (Real-Time Operating System, RTOS) ab. Azure RTOS wird nun mit integriertem Azure IoT-Sicherheitsmodul ausgeliefert.

:::image type="content" source="./media/architecture/azure-rtos-security-monitoring.png" alt-text="Visualisierung von Defender für IoT Azure RTOS":::


Micro-Modul für Azure RTOS bietet die folgenden Funktionen:

- Erkennung schädlicher Netzwerkaktivitäten
- Auf benutzerdefinierten Warnungen basierende Basislinienüberwachung für Geräteverhalten
- Verbesserte Gerätesicherheit

## <a name="detect-malicious-network-activities"></a>Erkennung bösartiger Netzwerkaktivitäten

Die eingehenden und ausgehenden Netzwerkaktivitäten jedes Geräts werden überwacht. Die Protokolle TCP, UDP und ICMP auf IPv4 und IPv6 werden unterstützt. Defender für IoT überprüft jede dieser Netzwerkaktivitäten anhand des Microsoft Threat Intelligence-Feeds. Der Feed wird in Echtzeit mit Millionen eindeutiger Bedrohungsindikatoren aktualisiert, die weltweit gesammelt werden.

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>Auf benutzerdefinierten Warnungen basierende Basislinienüberwachung für Geräteverhalten

Die Basislinienüberwachung ermöglicht das Clustering von Geräten in Sicherheitsgruppen und das Festlegen des erwarteten Verhaltens jeder Gruppe. Da IoT-Geräte in der Regel in gut definierten und begrenzten Szenarios betrieben werden, ist es einfach, eine Basislinie zu erstellen, die das erwartete Verhalten mithilfe eines Satzes von Parametern definiert. Jede Abweichung von der Basislinie löst eine Warnung aus.

## <a name="improve-your-device-security-hygiene"></a>Verbesserung der Gerätesicherheit

Wenn Sie die empfohlene Infrastruktur nutzen, die von Defender für IoT bereitgestellt wird, können Sie Informationen über Ihre Umgebung und Einblicke in die Probleme erhalten, die sich auf die Sicherheit Ihrer Geräte auswirken und diese schädigen. Wenn ein schwacher IoT-Geräte-Sicherheitsstatus unverändert bleibt, kann dies den Erfolg möglicher Angriffe bedeuten. Sicherheit wird immer am schwächsten Kettenglied innerhalb einer Organisation gemessen.

## <a name="get-started-protecting-azure-rtos-devices"></a>Erste Schritte zum Schutz von Azure RTOS-Geräten

Das Sicherheitsmodul für Azure RTOS steht als kostenloser Download für Ihre Geräte zur Verfügung. Der Defender für IoT-Clouddienst ist mit einer 30-tägigen Testversion pro Azure-Abonnement verfügbar. Laden Sie für den Einstieg das [Sicherheitsmodul für Azure RTOS](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/iot-security-azure-rtos.md) herunter. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Informationen über das Sicherheitsmodul für Azure RTOS erhalten. Weitere Informationen zu dem Sicherheitsmodul und den ersten Schritten damit finden Sie in den folgenden Artikeln:

- [Konzepte des IoT-Sicherheitsmoduls für Azure RTOS](concept-rtos-security-module.md)
- [Schnellstart: IoT-Sicherheitsmodul für Azure RTOS](quickstart-azure-rtos-security-module.md)
