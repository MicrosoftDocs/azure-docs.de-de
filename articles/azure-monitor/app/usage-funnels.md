---
title: Azure Application Insights-Trichter
description: In diesem Artikel wird erläutert, wie Sie Trichter dazu verwenden können, sich über die Interaktionen der Kunden mit Ihrer Anwendung zu informieren.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/17/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: 2cb7e15b701b53e74618c21bf219a355d495f985
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60372915"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Informieren Sie sich mit Application Insights-Trichtern darüber, wie die Kunden Ihre Anwendung verwenden

Es ist für Ihr Unternehmen von essenzieller Bedeutung, ein Gefühl für die Kundenerfahrung zu entwickeln. Wenn in Ihrer Anwendung mehrere Schritte durchlaufen werden, müssen Sie wissen, ob die meisten Kunden den gesamten Prozess durchlaufen oder diesen an einem bestimmten Punkt beenden. Das Durchlaufen einer Reihe von Schritten in einer Webanwendung wird als *Trichter* (auch: Verkaufstrichter) bezeichnet. Sie können Azure Application Insights-Trichter verwenden, um Erkenntnisse über Ihre Benutzer zu erhalten und die Abschlussraten Schritt für Schritt zu überwachen. 

## <a name="create-your-funnel"></a>Erstellen Ihres Trichters
Bevor Sie Ihren Trichter erstellen, sollten Sie eine Entscheidung darüber treffen, welche Frage damit beantwortet werden soll. Beispiel: Sie möchten wissen, wie viele Benutzer die Homepage oder ein Kundenprofil anzeigen und ein Ticket erstellen. In diesem Beispiel möchten die Besitzer des Unternehmens Fabrikam Fiber den Prozentsatz der Kunden erfahren, die ein Kundenticket erstellt haben.

Im Folgenden werden die Schritte zum Erstellen ihres Trichters beschrieben.

1. Wählen Sie im Application Insights-Trichter-Tool die Option **Neu**.
1. Wählen Sie im Dropdownmenü **Zeitbereich** die Option **Letzte 90 Tage**. Wählen Sie entweder **Meine Trichter** oder **Freigegebene Trichter** aus.
1. Wählen Sie in der Dropdownliste **Schritt 1** die Option **Index**. 
1. Wählen Sie in der Liste **Schritt 2** die Option **Kunde**.
1. Wählen Sie in der Liste **Schritt 3** die Option **Erstellen**.
1. Fügen Sie dem Trichter einen Namen hinzu, und wählen Sie **Speichern**.

Der folgende Screenshot enthält ein Beispiel für die Art der Daten, die mit dem Trichter-Tool generiert werden. Die Besitzer von Fabrikam können erkennen, dass während der letzten 90 Tage 54,3 Prozent der Kunden, die die Homepage aufgerufen haben, ein Kundenticket erstellt haben. Außerdem ist erkennbar, dass 2.700 Kunden über die Homepage zum Index gelangt sind. Dies kann auf ein Aktualisierungsproblem hindeuten.


![Screenshot: Trichter-Tool mit Daten](media/usage-funnels/funnel1.png)

### <a name="funnels-features"></a>Trichter-Features
Der obige Screenshot enthält fünf hervorgehobene Bereiche. Dies sind Features des Trichters. In der folgenden Liste werden die einzelnen Bereiche des Screenshots näher beschrieben:
1. Wenn für Ihre App Stichproben erstellt werden, wird ein entsprechendes Banner angezeigt. Wenn Sie das Banner auswählen, wird ein Kontextbereich mit Informationen zum Deaktivieren der Stichprobenfunktion geöffnet. 
2. Sie können den Trichter nach [Power BI](../../azure-monitor/app/export-power-bi.md ) exportieren.
3. Wählen Sie einen Schritt aus, um rechts weitere Details anzuzeigen. 
4. Im Graphen zum Verlauf der Abschlüsse sind die Abschlussraten für die letzten 90 Tage angegeben. 
5. Entwickeln Sie ein besseres Verständnis Ihrer Benutzer, indem Sie auf das Benutzertool zugreifen. Sie können in jedem Schritt Filter verwenden. 

## <a name="next-steps"></a>Nächste Schritte
  * [Nutzungsübersicht](usage-overview.md)
  * [Benutzer-, Sitzungs- und Ereignisanalyse in Azure Application Insights](usage-segmentation.md)
  * [Vermerkdauer](usage-retention.md)
  * [Arbeitsmappen](../../azure-monitor/app/usage-workbooks.md)
  * [Hinzufügen von Benutzerkontext](usage-send-user-context.md)
  * [Exportieren nach Power BI](../../azure-monitor/app/export-power-bi.md )

