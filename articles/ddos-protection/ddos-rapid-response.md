---
title: Azure DDoS Rapid Response
description: Erfahren Sie, wie Sie bei DDoS-Experten während eines aktiven Angriffs spezielle Unterstützung anfordern können.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: yitoh
ms.openlocfilehash: 3655b117b641a6179bd6539aaf3f75af07f21a1a
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992368"
---
# <a name="azure-ddos-rapid-response"></a>Azure DDoS Rapid Response

Während eines aktiven Zugriffs können sich Kunden mit Azure DDoS Protection Standard an das DRR-Team (DDoS Rapid Response) wenden, das sie bei der Untersuchung während eines Angriffs sowie bei der Analyse nach Angriffen unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

- Um die Schritte in diesem Tutorial auszuführen, müssen Sie zunächst einen [Azure DDoS Standard-Schutzplan](manage-ddos-protection.md) erstellen.

## <a name="when-to-engage-drr"></a>Zeitpunkt für den Einsatz von DRR

Sie sollten DRR nur in folgenden Situationen einsetzen: 

- Wenn Sie während eines DDoS-Angriffs feststellen, dass die Leistung der geschützten Ressource erheblich beeinträchtig wird oder die Ressource nicht verfügbar ist. Überprüfen Sie obigen Schritt 2 zur Konfiguration von Monitoren zur Erkennung von Problemen bei der Ressourcenverfügbarkeit und Leistung.
- Sie glauben, dass Ihre Ressource einem DDoS-Angriff ausgesetzt ist, aber der DDoS Protection-Dienst kann den Angriff nicht wirksam abwehren.
- Wenn Sie ein virales Ereignis planen, dass zu einem beträchtlichen Anstieg Ihres Netzwerkdatenverkehrs führen wird.
- Für Angriffe, die unternehmenskritische Auswirkungen haben.

## <a name="engage-drr-during-an-active-attack"></a>Einsetzen von DRR während eines aktiven Angriffs

1. Wählen Sie im Azure-Portal beim Erstellen einer neuen Supportanfrage für **Problemtyp** die Option „Technisch“ aus.
2. Wählen Sie **Dienst** für **DDOS Protection** aus.
3. Wählen Sie eine Ressource im Dropdownmenü für Ressourcen aus. _Sie müssen einen DDoS-Plan auswählen, der mit dem virtuellen Netzwerk verbunden ist, das durch DDoS Protection Standard geschützt wird, um DRR einzusetzen._

    ![Auswählen einer Ressource](./media/ddos-rapid-response/choose-resource.png)

4. Auf der nächsten **Problem**-Seite wählen Sie für den **Schweregrad** die Option „A – Kritische Auswirkungen“ und für den **Problemtyp** die Option „Angriff“ aus.

    ![PSeverity und Problemtyp](./media/ddos-rapid-response/severity-and-problem-type.png)

5. Vervollständigen Sie weitere Details, und übermitteln Sie die Supportanfrage.

DRR folgt dem Azure Rapid Response-Supportmodell. Weitere Informationen zu Rapid Response finden Sie unter [Supportumfang und Reaktionszeiten](https://azure.microsoft.com/en-us/support/plans/response/).

Weitere Informationen finden Sie in der [Dokumentation zu DDoS Protection Standard](./ddos-protection-overview.md).

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Tests über Simulationen ausführen können](test-through-simulations.md).
- Erfahren Sie, wie Sie [Telemetriedaten zum DDoS-Schutz anzeigen und konfigurieren](telemetry-monitoring-alerting.md).
- Erfahren Sie, wie Sie [Berichte und Datenflussprotokolle zur Entschärfung von DDoS-Angriffen konfigurieren](reports-and-flow-logs.md).