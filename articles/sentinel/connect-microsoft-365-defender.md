---
title: Verknüpfen von Microsoft 365 Defender-Rohdaten mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie unformatierte Ereignisdaten aus Microsoft 365 Defender in Azure Sentinel erfassen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 756c245fe06ae81545a125dd98f30fb27fdff2dd
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655578"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>Verknüpfen von Daten aus Microsoft 365 Defender mit Azure Sentinel

> [!IMPORTANT]
>
> **Microsoft 365 Defender** wurde bisher als **Microsoft Threat Protection** oder **MTP** bezeichnet.
>
> **Microsoft Defender for Endpoint** wurde bisher als **Microsoft Defender Advanced Threat Protection** oder **MDATP** bezeichnet.
>
> Die alten Namen werden möglicherweise eine Zeit lang weiterhin verwendet.

## <a name="background"></a>Hintergrund

Mithilfe des neuen [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection)-Connectors können Sie **erweiterte Hunting**-Protokolle – einen Typ von Ereignisrohdaten – von Microsoft 365 Defender in Azure Sentinel streamen. 

Dank der Integration von [Microsoft Defender for Endpoint (MDATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) in den Schutzschirm von Microsoft 365 Defender können Sie jetzt Ihre [erweiterten Hunting](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview)-Ereignisse aus Microsoft Defender for Endpoint mithilfe des Microsoft 365 Defender-Connectors erfassen und sie direkt in neue, zu diesem Zweck erstellte Tabellen in Ihrem Azure Sentinel-Arbeitsbereich streamen. Diese Tabellen bauen auf dem gleichen Schema auf, das im Microsoft 365 Defender-Portal verwendet wird, wodurch Sie vollen Zugriff auf die gesamte Reihe erweiterter Huntingprotokolle erhalten und Folgendes erreichen können:

- Einfaches Kopieren Ihrer vorhandenen erweiterten Huntingabfragen für Microsoft Defender ATP in Azure Sentinel.

- Verwenden der Ereignisrohprotokolle zum Gewinnen zusätzlicher Erkenntnisse für Ihre Benachrichtigungen, Huntings und Untersuchungen und Korrelieren von Ereignissen mit Daten aus zusätzlichen Datenquellen in Azure Sentinel.

- Speichern der Protokolle mit verlängerter Aufbewahrungsdauer, jenseits der Standardaufbewahrungsdauer von 30 Tagen in Microsoft Defender for Endpoint oder Microsoft 365 Defender. Dies erreichen Sie durch Konfigurieren der Aufbewahrungsdauer Ihres Arbeitsbereichs oder der tabellenweisen Aufbewahrung in Log Analytics.

> [!IMPORTANT]
>
> Der Microsoft 365 Defender-Connector befindet sich derzeit in der öffentlichen Vorschauphase. Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

- Sie müssen über eine gültige Lizenz für Microsoft Defender for Endpoint verfügen, wie unter [Set up Microsoft Defender for Endpoint deployment](/windows/security/threat-protection/microsoft-defender-atp/licensing) (Einrichten der Bereitstellung von Microsoft Defender for Endpoint). 

- Ihrem Benutzer muss die globale Administratorrolle im Mandanten (in Azure Active Directory) zugewiesen sein.

## <a name="connect-to-microsoft-365-defender"></a>Herstellen einer Verbindung mit Microsoft 365 Defender

Wenn Microsoft Defender for Endpoint bereitgestellt ist und Ihre Daten erfasst, können die Ereignisprotokolle auf einfache Weise in Azure Sentinel gestreamt werden.

1. Wählen Sie in Azure Sentinel **Datenconnectors**, dann im Katalog **Microsoft 365 Defender (Vorschau)** und anschließend **Connectorseite öffnen** aus.

1. Die folgenden Typen von Ereignissen können aus ihren entsprechenden erweiterten Huntingtabellen erfasst werden. Aktivieren Sie die Kontrollkästchen der Ereignistypen, die Sie erfassen möchten:

    | Ereignistyp | Tabellenname |
    |-|-|
    | Computerinformationen (einschließlich Betriebssysteminformationen) | DeviceInfo |
    | Netzwerkeigenschaften von Computern | DeviceNetworkInfo |
    | Prozesserstellung und zugehörige Ereignisse | DeviceProcessEvents |
    | Netzwerkverbindung und zugehörige Ereignisse | DeviceNetworkEvents |
    | Dateierstellung, Änderung und andere Dateisystemereignisse | DeviceFileEvents |
    | Erstellen und Ändern von Registrierungseinträgen | DeviceRegistryEvents |
    | Anmeldungen und andere Authentifizierungsereignisse | DeviceLogonEvents |
    | DLL-Ladeereignisse | DeviceImageLoadEvents |
    | Weitere Ereignistypen | DeviceEvents |
    |

1. Klicken Sie auf **Apply Changes**. 

1. Zum Abfragen der erweiterten Huntingtabellen in Log Analytics geben Sie den Tabellennamen aus der Liste oben im Abfragefenster ein.

## <a name="verify-data-ingestion"></a>Überprüfen der Datenerfassung

Das Datendiagramm auf der Connectorseite weist darauf hin, dass Sie Daten erfassen. Sie werden feststellen, dass es eine einzelne Linie anzeigt, die das Ereignisvolumen über alle aktivierten Tabellen zusammenfasst. Nachdem Sie den Connector aktiviert haben, können Sie die folgende KQL-Abfrage verwenden, um ein ähnliches Diagramm des Ereignisvolumens für eine einzelne Tabelle zu generieren (ändern Sie die *DeviceEvents*-Tabelle in die erforderliche Tabelle Ihrer Wahl):

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

Auf der Registerkarte **Nächste Schritte**  finden Sie einige Beispielabfragen, die wir für Sie aufgenommen haben. Sie können sie sofort ausführen oder sie ändern und speichern.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Ereignisrohdaten aus Microsoft Defender for Endpoint mithilfe des Microsoft 365 Defender-Connectors in Azure Sentinel erfasst werden können. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](./tutorial-detect-threats-built-in.md).