---
title: Verknüpfen von Azure Security Center-Daten mit der Vorschauversion von Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Security Center-Daten mit Azure Sentinel verknüpfen.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 7f2f85f8b68efadf1dc0a35d1a8e6bda2655f53b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65207296"
---
# <a name="connect-data-from-azure-security-center"></a>Verknüpfen von Daten aus Azure Security Center

> [!IMPORTANT]
> Azure Sentinel ist derzeit als öffentliche Vorschauversion (Public Preview) verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Mit Azure Sentinel können Sie Warnungen aus [Azure Security Center](../security-center/security-center-intro.md) verknüpfen und in Azure Sentinel streamen. 

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie Warnungen aus dem Azure Security Center exportieren möchten, müssen Sie ein Mitwirkender im Abonnement sein, dessen Protokolle Sie streamen.

- Sie müssen das Abonnement im [Azure Security Center-Standard-Tarif](../security-center/security-center-pricing.md) ausführen. Wenn dies nicht der Fall ist, [aktualisieren Sie Ihr Abonnement auf den Standard-Tarif](https://azure.microsoft.com/pricing/details/security-center/).

- Sie müssen sich als Benutzer anmelden, der über globale Administrator- oder Sicherheitsadministratorrechte für jedes Abonnement verfügt, das Sie verbinden möchten.


## <a name="connect-to-azure-security-center"></a>Herstellen einer Verbindung mit Azure Security Center

1. Klicken Sie in Azure Sentinel auf **Data connectors** (Datenconnectors) und anschließend auf die Kachel **Azure Security Center**.
1. Klicken Sie in der rechten Spalte neben jedem Abonnement, dessen Warnungen Sie in Azure Sentinel streamen möchten, auf **Verbinden**. Stellen Sie sicher, dass Sie jedes Abonnement auf den Azure Security Center-Standard-Tarif aktualisieren, um Warnungen an Azure Sentinel zu streamen.

3. Klicken Sie auf **Verbinden**.

4. Um das relevante Schema für die Azure Security Center-Warnungen in Log Analytics zu verwenden, suchen Sie nach **SecurityEvent**.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument wurde beschrieben, wie Sie Azure Security Center mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats.md).
