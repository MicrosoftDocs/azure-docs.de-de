---
title: Verbinden von Alcide kAudit-Daten mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Alcide kAudit-Daten mit Azure Sentinel verbinden.
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
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: 6d1314b79f09f1bed2de5d6964b0a6224b5962ca
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100096870"
---
# <a name="connect-your-alcide-kaudit-to-azure-sentinel"></a>Herstellen einer Verbindung zwischen Alcide kAudit und Azure Sentinel

> [!IMPORTANT]
> Der Alcide kAudit-Datenconnector in Azure Sentinel befindet sich derzeit in der öffentlichen Vorschau.
> Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Alcide kAudit](https://www.alcide.io/kaudit-K8s-forensics/) unterstützt Sie bei der Identifizierung von anormalem Kubernetes-Verhalten. Der Schwerpunkt liegt dabei auf Kubernetes-Verletzungen und -Incidents bei verringerter Erkennungszeit. In diesem Artikel wird erläutert, wie Sie Ihre Alcide kAudit-Lösung mit Azure Sentinel verbinden. Mit dem Alcide kAudit-Datenconnector lassen sich Alcide kAudit-Daten problemlos in Azure Sentinel einbinden. Diese Daten können Sie dann in Arbeitsmappen anzeigen, als Vorlage für benutzerdefinierte Warnungen und zur Untersuchung von Problemen verwenden. Die Integration von Alcide kAudit und Azure Sentinel stützt sich auf die REST-API.

> [!NOTE]
> Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Lese- und Schreibberechtigungen für den Azure Sentinel-Arbeitsbereich.

- Sie müssen über Leseberechtigungen für freigegebene Schlüssel für den Arbeitsbereich verfügen.

## <a name="configure-and-connect-alcide-kaudit"></a>Konfigurieren und Verbinden von Alcide kAudit

Alcide kAudit kann Protokolle direkt in Azure Sentinel exportieren.

1. Klicken Sie im Azure Sentinel-Portal im Navigationsmenü auf **Datenconnectors**.

1. Wählen Sie aus dem Katalog **Alcide kAudit** aus, und klicken Sie dann auf die Schaltfläche **Seite „Connector öffnen“** .

1. Befolgen Sie die schrittweisen Anleitungen im [Alcide kAudit-Installationsleitfaden](https://get.alcide.io/hubfs/Azure%20Sentinel%20Integration%20with%20kAudit.pdf).

1. Wenn Sie nach der Arbeitsbereichs-ID und dem Primärschlüssel gefragt werden, können Sie diese Angaben von der Seite „Alcide kAudit-Datenconnector“ kopieren.

    :::image type="content" source="media/connect-alcide-kaudit/alcide-workspace-id-primary-key.png" alt-text="Arbeitsbereichs-ID und Primärschlüssel":::

## <a name="find-your-data"></a>Suchen von Daten

Nachdem eine erfolgreiche Verbindung hergestellt wurde, werden die Daten in **Logs** (Protokolle) unter den folgenden Datentypen in **CustomLogs** angezeigt:

- **alcide_kaudit_detections_1_CL**: Alcide kAudit-Erkennungen 
- **alcide_kaudit_activity_1_CL**: Alcide kAudit-Aktivitätsprotokolle
- **alcide_kaudit_selections_count_1_CL**: Alcide kAudit-Aktivitätsanzahl
- **alcide_kaudit_selections_details_1_CL**: Alcide kAudit-Aktivitätsdetails

Um das relevante Schema in den Protokollen für Alcide kAudit zu verwenden, suchen Sie nach den oben genannten Datentypen.

Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument wurde beschrieben, wie Sie Alcide kAudit mit Azure Sentinel verbinden. Um die in diesem Datenconnector integrierten Funktionen in vollem Umfang nutzen zu können, klicken Sie auf der Datenconnectorseite auf die Registerkarte **Nächste Schritte**. Dort finden Sie einige vorgefertigte Beispielabfragen, die Ihnen die Suche nach nützlichen Informationen erleichtern.

Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](tutorial-monitor-your-data.md), um Ihre Daten zu überwachen.
