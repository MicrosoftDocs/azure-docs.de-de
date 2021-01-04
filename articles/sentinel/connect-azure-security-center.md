---
title: Verbinden von Azure Defender-Daten mit Azure Sentinel
description: In diesem Artikel erfahren Sie, wie Sie Warnungen aus Azure Defender verbinden und in Azure Sentinel streamen.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: b37675d46d501fc43446eafc83d6c0a02f238177
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655951"
---
# <a name="connect-azure-defender-alert-data-from-azure-security-center"></a>Verbinden von Azure Defender-Warnungsdaten aus dem Azure Security Center

Mit dem Azure Defender-Warnungsconnector können Sie Azure Defender-Warnungen aus dem [Azure Security Center](../security-center/security-center-introduction.md) erfassen und in Azure Sentinel streamen. 

## <a name="prerequisites"></a>Voraussetzungen

- Ihr Benutzer muss in dem Abonnement, dessen Protokolle Sie streamen, über die Rolle „Sicherheitsleseberechtigter“ verfügen.

- Sie müssen Azure Defender im Azure Security Center aktivieren. (Die Standard-Ebene gibt es nicht mehr und stellt daher auch keine Lizenzanforderung mehr dar.)

## <a name="connect-to-azure-defender"></a>Verbinden mit Azure Defender

1. Wählen Sie im Navigationsmenü von Azure Sentinel **Datenconnectors** aus.

1. Klicken Sie im Katalog für Datenconnectors auf **Azure Defender alerts from ASC** (Azure Defender-Warnungen aus dem ASC) (heißt möglicherweise immer noch „Azure Security Center“), und klicken Sie auf die Schaltfläche **Open connector page** (Connectorseite öffnen).

1. Klicken Sie unter **Konfiguration** neben jedem Abonnement, dessen Warnungen Sie in Azure Sentinel streamen möchten, auf **Verbinden**. Die Schaltfläche „Verbinden“ ist nur verfügbar, wenn Ihnen die erforderlichen Berechtigungen erteilt wurden.

1. Sie können auswählen, ob die Azure Defender-Warnungen automatisch Incidents in Azure Sentinel generieren sollen. Wählen Sie unter **Incidents erstellen** die Option **Aktiviert** aus, um die Standardanalyseregel zu aktivieren, durch die aus Warnungen automatisch Incidents erstellt werden. Anschließend können Sie diese Regel unter **Analytics** auf der Registerkarte **Aktive Regeln** bearbeiten.

1. Damit das relevante Schema für die Azure Defender-Warnungen in Log Analytics verwendet wird, suchen Sie nach **SecurityAlert**.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie Azure Defender mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).