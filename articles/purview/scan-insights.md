---
title: Überprüfungserkenntnisse über Ihre Daten in Azure Purview
description: In dieser Schrittanleitung wird beschrieben, wie Sie die Überprüfungsberichterstellung mit Purview-Erkenntnissen für Ihre Daten anzeigen und verwenden können.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 00f72e1de230cdc68f86010b7b25d86debaa5eb5
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575787"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>Überprüfungserkenntnisse über Ihre Daten in Azure Purview

In dieser Schrittanleitung wird beschrieben, wie Sie auf Azure Purview-Erkenntnisberichte zu Überprüfungen Ihrer Daten zugreifen und diese anzeigen und filtern können.

In dieser Schrittanleitung lernen Sie Folgendes:

> [!div class="checklist"]
> * Anzeigen von Erkenntnissen aus Ihrem Purview-Konto
> * Erhalten einer Übersicht über Ihre Überprüfungen

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie vor dem Beginn mit Purview-Erkenntnissen sicher, dass Sie die folgenden Schritte ausgeführt haben:

* Einrichten Ihrer Azure-Ressourcen und Auffüllen des Kontos mit Daten
* Einrichten und Ausführen einer Überprüfung der Datenquelle

Weitere Informationen finden Sie unter [Verwalten von Datenquellen in Azure Purview (Vorschau)](manage-data-sources.md).

## <a name="use-purview-scan-insights"></a>Verwenden der Überprüfungserkenntnisse von Purview

Sie können in Azure Purview Quelltypen registrieren und überprüfen. Der Überprüfungsstatus kann in den Überprüfungserkenntnissen über einen Zeitraum angezeigt werden. Die Erkenntnisse geben Aufschluss darüber, wie viele Überprüfungen innerhalb eines bestimmten Zeitraums fehlerhaft oder erfolgreich waren und wie viele abgebrochen wurden.

### <a name="view-scan-insights"></a>Scanerkenntnisse anzeigen

1. Wechseln Sie zum **Azure Purview**-Instanzbildschirm im Azure-Portal, und wählen Sie Ihr Purview-Konto aus.

1. Wählen Sie auf der Seite **Übersicht** im Abschnitt **Erste Schritte** die Kachel zum **Öffnen von Purview Studio** aus.

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="Starten von Purview im Azure-Portal":::

1. Wählen Sie auf der **Startseite** von Purview die Kachel **Erkenntnisse anzeigen** aus, um auf Ihren Bereich **Erkenntnisse** (:::image type="icon" source="media/scan-insights/ico-insights.png" border="false":::) zuzugreifen.

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Anzeigen Ihrer Erkenntnisse im Azure-Portal":::

1. Wählen Sie im Bereich **Erkenntnisse** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: die Option **Überprüfungen** aus, um den Purview-Bericht mit **Überprüfungserkenntnissen** anzuzeigen.

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status"></a>Überprüfen allgemeiner KPIs zum Anzeigen der Anzahl der Überprüfungen nach dem Status
 
Die allgemeinen KPIs zeigen an, wie viele Überprüfungen insgesamt innerhalb eines Zeitraums ausgeführt wurden. Standardmäßig umfasst dieser Zeitraum die letzten 30 Tage. Sie können jedoch auch die letzten sieben Tage auswählen. Basierend auf dem Zeitfilter geben die KPI-Werte die entsprechende Anzahl der Überprüfungen wieder.


Sie können je nach ausgewähltem Zeitfilterwert die Verteilung erfolgreicher, fehlerhafter und abgebrochener Überprüfungen pro Woche oder pro Tag im Diagramm anzeigen.

   :::image type="content" source="./media/scan-insights/scan-insights.png" alt-text="Anzeigen von Scanerkenntnissen":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Purview-Erkenntnisberichten finden Sie unter [Ressourcenerkenntnisse zu Ihren Daten in Azure Purview](./asset-insights.md).
