---
title: Verbinden von Orca-Sicherheitswarnungen mit Azure Sentinel | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Daten von Orca-Sicherheitswarnungen mit Azure Sentinel verbinden, um Dashboards anzuzeigen, benutzerdefinierte Warnungen zu erstellen und die Untersuchung von Daten zu verbessern.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 18a50e3d06135f2c17b7e7c5b2969be33f408c62
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100093079"
---
# <a name="connect-your-orca-security-alerts-to-azure-sentinel"></a>Verbinden Ihrer Orca-Sicherheitswarnungen mit Azure Sentinel 

> [!IMPORTANT]
> Der Connector für Orca-Sicherheitswarnungen in Azure Sentinel ist derzeit als öffentliche Vorschau verfügbar.
> Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mit dem Connector für Orca-Sicherheitswarnungen lassen sich Warnungen Ihrer [Orca](https://orca.security/)-Sicherheitslösung problemlos in Azure Sentinel einbinden. Diese Warnungen können Sie dann in Arbeitsmappen anzeigen sowie als Vorlage für benutzerdefinierte Warnungen und zur Untersuchung von Problemen verwenden. Die Integration von Orca-Sicherheitswarnungen und Azure Sentinel stützt sich auf die REST-API.

> [!NOTE]
> Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.

## <a name="configure-and-connect-orca-security-alerts"></a>Konfigurieren und Verknüpfen von Orca-Sicherheitswarnungen

Orca-Sicherheitswarnungen können in Azure Sentinel integriert und Protokolle dann direkt in Azure Sentinel exportiert werden.

1. Klicken Sie im Azure Sentinel-Portal auf **Datenconnectors**, und wählen Sie **Orca Security alerts** (Orca-Sicherheitswarnungen) > **Connectorseite öffnen** aus.

2. Unter https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-integration erfahren Sie, wie Sie die Integration über die Orca-Plattform abschließen.

## <a name="find-your-data"></a>Suchen von Daten

Nach erfolgreicher Verbindungsherstellung werden die Daten in Log Analytics in der Tabelle **OrcaAlerts_CL** unter **CustomLogs** angezeigt.
Suchen Sie nach `OrcaAlerts_CL`, um in Log Analytics das relevante Schema für die Orca-Warnungen zu verwenden.

## <a name="validate-connectivity"></a>Überprüfen der Konnektivität
Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden. 


## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie gelernt, wie Sie Orca-Sicherheitswarnungen mit Azure Sentinel verknüpfen. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](tutorial-monitor-your-data.md), um Ihre Daten zu überwachen.

