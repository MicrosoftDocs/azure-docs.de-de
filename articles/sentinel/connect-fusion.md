---
title: Aktivieren von Fusion in der Vorschauversion von Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Fusion in Azure Sentinel aktivieren.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 82becf50-6628-47e4-b3d7-18d7d72d505f
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: bf9a78006d13614a73a3fccfc57f28ce850456d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65204470"
---
# <a name="enable-fusion"></a>Aktivieren von Fusion

> [!IMPORTANT]
> Azure Sentinel ist aktuell als Public Preview verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Machine Learning (ML) in Azure Sentinel ist von Anfang an integriert. Wir haben das System mit Machine Learning-Innovationen so konzipiert, dass Sicherheitsanalysten, Sicherheitsdatenwissenschaftler und Ingenieure produktiv arbeiten können. Eine solche Innovation ist Azure Sentinel Fusion, die speziell entwickelt wurde, um Warnermüdung zu reduzieren.

Fusion verwendet grafikbasierte ML-Algorithmen, um Millionen von anomalen Aktivitäten mit geringerer Bedeutung aus verschiedenen Produkten wie Azure Active Directory Identity Protection und Microsoft Cloud App Security zueinander in Beziehung zu setzen und zu einer überschaubaren Anzahl von interessanten Sicherheitsfällen zu kombinieren.

## <a name="enable-fusion"></a>Aktivieren von Fusion

1. Wählen Sie im Azure-Portal das Symbol aus, um Cloud Shell zu öffnen.
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  Wählen Sie in den sich öffnenden Fenstern **Willkommen bei Cloud Shell** PowerShell aus.

3.  Wählen Sie das Abonnement aus, in dem Sie Azure Sentinel bereitgestellt haben, und dann **Speicher erstellen**.

4. Nach der Authentifizierung und der Erstellung Ihres Azure-Laufwerks führen Sie an der Eingabeaufforderung die folgenden Befehle aus:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>Deaktivieren von Fusion

Führen Sie die gleichen Schritte wie oben und dann den folgenden Befehl aus:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>Anzeigen des Status von Fusion

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument wurde beschrieben, wie Sie Fusion in Azure Sentinel aktivieren. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats.md).

