---
title: Verbinden von Zimperium Mobile Threat Defense mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Zimperium Mobile Threat Defense mit Azure Sentinel verbinden.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 7cbf1c52af1d2902ae0726fc0dd98dbf12cecc44
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100097448"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>Verbinden von Zimperium Mobile Threat Defense mit Azure Sentinel


> [!IMPORTANT]
> Der Zimperium Mobile Threat Defense-Datenconnector in Azure Sentinel ist derzeit als öffentliche Vorschau verfügbar.
> Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Der Zimperium Mobile Threat Defense-Connector bietet Ihnen die Möglichkeit, das Zimperium Threat-Protokoll mit Azure Sentinel zu verbinden, um Dashboards anzuzeigen, benutzerdefinierte Warnungen zu erstellen und Untersuchungen zu verbessern. So erhalten Sie einen besseren Einblick in die Bedrohungslage mobiler Geräte in Ihrer Organisation und bessere Möglichkeiten für Sicherheitsvorgänge.

> [!NOTE]
> Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>Konfigurieren und Verbinden von Zimperium Mobile Threat Defense

Zimperium Mobile Threat Defense kann in **Azure Sentinel** integriert werden. Protokolle können dann direkt in Azure Sentinel exportiert werden.

1. Klicken Sie im Azure Sentinel-Portal auf „Datenconnectors“, und wählen Sie **Zimperium Mobile Threat Defense** aus.
2. Wählen Sie **Connectorseite öffnen** aus.
3. Befolgen Sie die Anweisungen auf der Connectorseite **Zimperium Mobile Threat Defense**, die wie folgt zusammengefasst sind.
 1. Klicken Sie in „zConsole“ auf der Navigationsleiste auf **Verwalten**.
 2. Klicken Sie auf die Registerkarte **Integrations** .
 3. Klicken Sie auf die Schaltfläche **Bedrohungsberichte** und dann auf die Schaltfläche **Integrationen hinzufügen**.
 4. Erstellen Sie die Integration, indem Sie aus den verfügbaren Integrationen **Microsoft Azure Sentinel** auswählen und die Arbeitsbereichs-ID und den Primärschlüssel eingeben, um eine Verbindung mit Azure Sentinel zu erstellen.
 5. Außerdem ist eine Option zum Auswählen einer Filterebene für die Bedrohungsdaten verfügbar, die per Push an Azure Sentinel übertragen werden sollen. 

4. Weitere Informationen finden Sie im [Zimperium-Portal für den Kundensupport](https://support.zimperium.com).


## <a name="find-your-data"></a>Suchen nach den Daten

Nachdem Sie eine erfolgreiche Verbindung hergestellt haben, werden die Daten in Log Analytics unter „CustomLogs ZimperiumThreatLog_CL“ und „ZimperiumMitigationLog_CL“ angezeigt.

Um das entsprechende Schema in Log Analytics für Zimperium Mobile Threat Defense zu verwenden, suchen Sie nach „ZimperiumThreatLog_CL“ und „ZimperiumMitigationLog_CL“.


## <a name="validate-connectivity"></a>Überprüfen der Konnektivität

Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie Zimperium Mobile Threat Defense mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).

- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).

- [Verwenden Sie Arbeitsmappen](tutorial-monitor-your-data.md), um Ihre Daten zu überwachen.

Weitere Informationen zu Zimperium erhalten Sie über die folgenden Links:

- [Zimperium](https://zimperium.com)

- [Zimperium Mobile Security Blog](https://blog.zimperium.com)

- [Zimperium-Portal für den Kundensupport](https://support.zimperium.com)

