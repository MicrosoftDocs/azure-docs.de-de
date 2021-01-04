---
title: Ressourcenerkenntnisse über Ihre Daten in Azure Purview (Vorschau)
description: In dieser Schrittanleitung wird beschrieben, wie Sie die Ressourcenberichterstellung mit Purview-Erkenntnissen für Ihre Daten anzeigen und verwenden können.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: ac93d835b8dbdd5a12d031825dcb879160df5e95
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575345"
---
# <a name="asset-insights-on-your-data-in-azure-purview"></a>Ressourcenerkenntnisse über Ihre Daten in Azure Purview

In dieser Schrittanleitung wird beschrieben, wie Sie auf Purview-Ressourcen-Erkenntnisberichte für Ihre Daten zugreifen, sie anzeigen und filtern können.

In dieser Schrittanleitung lernen Sie Folgendes:

> [!div class="checklist"]
> * Anzeigen von Erkenntnissen aus Ihrem Purview-Konto.
> * Erhalten eines Überblicks über Ihre Daten.
> * Drilldown für weitere Details der Ressourcenanzahl.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie vor dem Beginn mit Purview-Erkenntnissen sicher, dass Sie die folgenden Schritte ausgeführt haben:

* Einrichten Ihrer Azure-Ressourcen und Auffüllen des Kontos mit Daten.

* Einrichten und Ausführen eines Scans für den Quelltyp.

Weitere Informationen finden Sie unter [Verwalten von Datenquellen in Azure Purview (Vorschau)](manage-data-sources.md).

## <a name="use-purview-asset-insights"></a>Verwenden von Purview-Ressourcenerkenntnissen

Sie können in Azure Purview Quelltypen registrieren und überprüfen. Nachdem die Überprüfung durchgeführt wurde, können Sie die Ressourcenverteilung in Ressourcenerkenntnisse anzeigen, um den Status der Datenumgebung nach Klassifizierung und Ressourcensätzen anzuzeigen. Außerdem werden Sie darüber informiert, ob eine Änderung der Datengröße vorliegt.

> [!NOTE]
> Nachdem Sie Ihre Quelltypen überprüft haben, lassen Sie „Ressourcenerkenntnisse“ bis zu einer Stunde Zeit, um die neuen Ressourcen zu berücksichtigen.

1. Navigieren Sie im Azure-Portal zu Ihrer Azure Purview-Ressource.

1. Wählen Sie auf der Seite **Übersicht** im Abschnitt **Erste Schritte** die Kachel zum **Öffnen von Purview Studio** aus.

   :::image type="content" source="./media/asset-insights/portal-access.png" alt-text="Starten von Purview im Azure-Portal":::

1. Wählen Sie auf der **Startseite** von Purview die Kachel **Erkenntnisse anzeigen** aus, um auf Ihren Bereich **Erkenntnisse** :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: zuzugreifen.

   :::image type="content" source="./media/asset-insights/view-insights.png" alt-text="Anzeigen Ihrer Erkenntnisse im Azure-Portal":::

1. Wählen Sie im Bereich **Erkenntnisse** :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: die Option **Ressourcen** aus, um den Purview-Bericht mit **Ressourcenerkenntnissen** anzuzeigen.

### <a name="view-asset-insights"></a>Anzeigen von Ressourcenerkenntnissen

1. Auf der Hauptseite **Ressourcenerkenntnisse** werden die folgenden Bereiche angezeigt:

2. Allgemeine KPIs zum Anzeigen von Quelltypen, klassifizierte Ressourcen und ermittelte Ressourcen
 
3. Das erste Diagramm zeigt **Ressourcen pro Quelltyp** an.

4. Zeigen Sie Ihre Ressourcenverteilung nach Quelltyp an. Wählen Sie eine Klassifizierung oder eine gesamte Klassifizierungskategorie aus, um die Ressourcenverteilung nach Quelltyp anzuzeigen. 
 
5. Wenn Sie weitere Informationen anzeigen möchten, wählen Sie **Mehr anzeigen** aus, sodass ein tabellarisches Formular der Quelltypen und Ressourcenzahlen angezeigt wird. Die Klassifizierungsfilter werden auf diese Seite übertragen.

   :::image type="content" source="./media/asset-insights/highlight-kpis.png" alt-text="Anzeigen von KPIs und Diagramm in „Ressourcenerkenntnisse“":::
 
6. Wählen Sie eine bestimmte Quelle aus, für die Sie die obersten Ordner mit der Ressourcenzahl anzeigen möchten. 

   :::image type="content" source="./media/asset-insights/select-data-source.png" alt-text="Auswählen des Quelltyps":::
 
7. Wählen Sie die Gesamtanzahl der Ressourcen für den obersten Ordner innerhalb des Quelltyps aus, den Sie oben ausgewählt haben.

   :::image type="content" source="./media/asset-insights/file-path.png" alt-text="Anzeigen von Dateipfaden":::

8. Zeigen Sie die Liste der Dateien im Ordner an. Navigieren Sie mithilfe der Breadcrumbs zurück zu „Erkenntnisse“.

   :::image type="content" source="./media/asset-insights/list-page.png" alt-text="Anzeigen der Liste mit Ressourcen":::  

### <a name="file-based-source-types"></a>Dateibasierte Quelltypen
Die nächsten Diagramme in „Ressourcenerkenntnisse“ zeigen eine Verteilung dateibasierter Quelltypen an. Das erste Diagramm mit dem Namen **Größentrend (GB) des Dateityps in Quelltypen** zeigt den Dateityp-Größentrend in den letzten 30 Tagen an. 
 
1. Wählen Sie den Quelltyp aus, um den Dateityp innerhalb der Quelle anzuzeigen. 
 
1. Wählen Sie **Mehr anzeigen** aus, um die aktuelle Datengröße, die Größenänderung, die aktuelle Ressourcenanzahl und die Änderungen der Ressourcenanzahl anzuzeigen.
 
   > [!NOTE]
   > Wenn die Überprüfung in den letzten 30 Tagen nur einmal ausgeführt wurde oder eine Katalogänderung wie das Hinzufügen/Entfernen der Klassifizierung nur einmal innerhalb von 30 Tagen stattgefunden hat, werden die oben aufgeführten Änderungsinformationen nicht angezeigt.

1. Wenn Sie auf den Quelltyp klicken, werden die Ordner mit den gravierendsten Änderungen der Ressourcenanzahl angezeigt.

1. Wählen Sie den Pfad aus, um die Liste der Ressourcen anzuzeigen.

Das zweite Diagramm in dateibasierten Quelltypen ist **_Dateien, die nicht mit einem Ressourcensatz verknüpft sind_**. Wenn Sie davon ausgehen, dass für alle Dateien ein Rollup in einem Ressourcensatz ausgeführt werden sollte, können Sie anhand dieses Diagramms nachvollziehen, für welche Ressourcen kein Rollup ausgeführt wurde. Fehlende Ressourcen können ein Hinweis auf ein falsches Dateimuster im Ordner sein. Führen Sie die gleichen Schritte wie in anderen Diagrammen aus, um weitere Details zu den Dateien anzuzeigen.

   :::image type="content" source="./media/asset-insights/file-based-assets.png" alt-text="Dateibasierte Ressourcen anzeigen":::  

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Purview-Erkenntnisberichten finden Sie unter [Ressourcenerkenntnisse über Ihre Daten in Azure Purview](./scan-insights.md).
