---
title: Konfigurieren von Azure Sentinel für Defender für IoT (Vorschau)
description: Erläutert die Konfiguration von Azure Sentinel zum Empfangen von Daten aus Ihrer Defender für IoT-Lösung.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: c5c0f74ed8a5688b20eea4e74f747d3ff6dd0e63
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96339998"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel-preview"></a>Verbinden Ihrer Daten aus Defender für IoT mit Azure Sentinel (Vorschau)

Der Azure Defender für IoT-Datenconnector in Azure Sentinel ist derzeit als Public Preview verfügbar. Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/).

In dieser Anleitung erfahren Sie, wie Sie Ihre Defender für IoT-Daten mit Azure Sentinel verbinden.

> [!div class="checklist"]
> * Voraussetzungen
> * Verbindungseinstellungen
> * Log Analytics-Warnungsansicht

Verbinden Sie Warnungen aus Defender für IoT, und streamen Sie sie direkt in Azure Sentinel.

Durch die engere Integration von Azure Defender für IoT mit Azure Sentinel, der ersten cloudnativen SIEM-Lösung und ersten SIEM-Lösung mit nativer IoT- und OT-Sicherheit, bietet Microsoft einen einfacheren Ansatz für die einheitliche Sicherheit in IT- und Industrienetzwerken. In Kombination mit dem maschinellen Lernen von Azure Sentinel ermöglicht diese Integration Organisationen das schnelle Erkennen mehrstufiger Angriffe, die IT- und OT-Grenzen oft überschreiten. Darüber hinaus ermöglicht die Integration von Azure Defender für IoT mit den SOAR-Funktionen (Security Orchestration, Automation and Response; Sicherheitsorchestrierung, Automatisierung und Reaktion) von Azure Sentinel die automatisierte Reaktion und Prävention mithilfe von integrierten, OT-optimierten Playbooks. 

## <a name="prerequisites"></a>Voraussetzungen

- Sie müssen über die Arbeitsbereichsberechtigungen **Lesen** und **Schreiben** verfügen.
- **Defender für IoT** muss für Ihre relevanten IoT Hub-Instanzen **aktiviert** sein.
- Sie müssen auf dem **Azure IoT Hub**, mit dem Sie eine Verbindung herstellen möchten, über die Berechtigungen **Lesen** und **Schreiben** verfügen.
- Außerdem müssen Sie über die Berechtigungen **Lesen** und **Schreiben** für die **Azure IoT Hub-Ressourcengruppe** verfügen.


## <a name="connect-to-defender-for-iot"></a>Verbinden mit Defender für IoT

1. Wählen Sie in Azure Sentinel **Datenconnectors** aus, und klicken Sie dann auf die Kachel **Defender für IoT**.
1. Klicken Sie unten im rechten Bereich auf **Connectorseite öffnen**.
1. Klicken Sie neben jedem IoT Hub-Abonnement, dessen Warnungen und Gerätewarnungen Sie in Azure Sentinel streamen möchten, auf **Verbinden**.
    - Wenn Defender für IoT auf diesem Hub nicht aktiviert wurde, wird eine Warnung vom Typ „Aktivieren“ angezeigt. Klicken Sie auf den Link **Aktivieren**, um den Dienst zu starten und zu aktivieren.
1. Sie können entscheiden, ob die Warnungen von Defender für IoT automatisch Incidents in Azure Sentinel generieren sollen. Wählen Sie unter **Create incidents** (Incidents erstellen) die Option **Aktivieren** aus, um die Regel zur automatischen Erstellung von Incidents aus den generierten Warnungen zu aktivieren.  Diese Regel kann unter **Analyse** > **Aktive Regeln** geändert oder bearbeitet werden.

> [!NOTE]
>Es kann 10 Sekunden oder länger dauern, bis die Hubliste aktualisiert wird, nachdem Verbindungsänderungen vorgenommen wurden.

## <a name="using-log-analytics-for-alert-display"></a>Verwenden von Log Analytics zum Anzeigen von Warnungen

So verwenden Sie das relevante Schema in Log Analytics, um die Azure Defender für IoT-Benachrichtigungen anzuzeigen:

1. Öffnen Sie **Logs** > **SecurityInsights** > **SecurityAlert**, oder suchen Sie nach **SecurityAlert**.
1. Filtern Sie mithilfe des folgenden KQL-Filters, um nur die von Defender für IoT generierten Benachrichtigungen anzuzeigen:

```kusto
SecurityAlert | where ProductName == "Defender for IoT"
```

### <a name="service-notes"></a>Diensthinweise

Nachdem Sie eine Verbindung mit einem IoT Hub hergestellt haben, stehen die Hubdaten in Azure Sentinel ungefähr 15 Minuten später zur Verfügung.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie Defender für IoT mit Azure Sentinel verbinden. Weitere Informationen zu Bedrohungserkennung und Zugriff auf Sicherheitsdaten finden Sie in folgenden Artikeln:

- Erfahren Sie, wie Sie mithilfe von Azure Sentinel [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](../sentinel/quickstart-get-visibility.md).

- Erfahren Sie, wie Sie auf Ihre [IoT-Sicherheitsdaten zugreifen](how-to-security-data-access.md).