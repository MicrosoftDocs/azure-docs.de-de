---
title: Verknüpfen von Microsoft Defender for Endpoint-Daten mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Microsoft Defender for Endpoint-Daten (vormals Microsoft Defender ATP) mit Azure Sentinel verbinden.
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
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: 72b2ba0ea444fb14ef9fc1bc3ea6aea3654677df
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655526"
---
# <a name="connect-alerts-from-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>Verknüpfen von Benachrichtigungen aus Microsoft Defender for Endpoint (vormals Microsoft Defender ATP)

> [!IMPORTANT]
>
> - **Microsoft Defender for Endpoint** wurde bisher als **Microsoft Defender Advanced Threat Protection** oder **MDATP** bezeichnet.
>
>     Möglicherweise wird der alte Name für einen bestimmten Zeitraum weiterhin im Produkt verwendet (einschließlich des Datenconnectors in Azure Sentinel).
>
> - Die Erfassung von Microsoft Defender for Endpoint-Benachrichtigungen befindet sich derzeit in der Public Preview-Phase.
> Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mit dem [Microsoft Defender for Endpoint](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)-Connector können Sie Benachrichtigungen von Microsoft Defender for Endpoint in Azure Sentinel streamen. Dies ermöglicht es Ihnen, Sicherheitsereignisse in Ihrer gesamten Organisation umfassender zu analysieren und Playbooks für effektive und sofortige Reaktionen zu entwickeln.

> [!NOTE]
>
> Um die neuen unformatierten Datenprotokolle aus dem [erweiterten Hunting](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview) von Microsoft Defender for Endpoint zu erfassen, verwenden Sie den neuen Connector für Microsoft 365 Defender (vormals Microsoft Threat Protection, [mehr dazu in der Dokumentation](./connect-microsoft-365-defender.md)).

## <a name="prerequisites"></a>Voraussetzungen

- Sie müssen über eine gültige Lizenz für Microsoft Defender for Endpoint verfügen, wie unter [Set up Microsoft Defender for Endpoint deployment](/windows/security/threat-protection/microsoft-defender-atp/licensing) (Einrichten der Bereitstellung von Microsoft Defender for Endpoint) beschrieben. 

- Sie müssen ein globaler Administrator oder Sicherheitsadministrator für den Azure Sentinel-Mandanten sein.

## <a name="connect-to-microsoft-defender-for-endpoint"></a>Herstellen einer Verbindung mit Microsoft Defender for Endpoint

Wenn Microsoft Defender for Endpoint bereitgestellt ist und Ihre Daten erfasst, können die Benachrichtigungen auf einfache Weise in Azure Sentinel gestreamt werden.

1. Wählen Sie in Azure Sentinel **Datenconnectors**, wählen Sie **Microsoft Defender for Endpoint** (möglicherweise noch als *Microsoft Defender Advanced Threat Protection* bezeichnet) im Katalog und dann die **Seite „Connector öffnen“** aus.

1. Klicken Sie auf **Verbinden**. 

1. Zum Abfragen von Microsoft Defender for Endpoint-Benachrichtigungen geben Sie in **Protokolle** im Abfragefenster **SecurityAlert** ein, und fügen Sie einen Filter hinzu, dessen **Provider name** (Anbietername) **MDATP** lautet.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie gelernt, wie Sie Microsoft Defender for Endpoint mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](./tutorial-detect-threats-built-in.md).