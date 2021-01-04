---
title: 'Tutorial: Durchsuchen von Ressourcen in Azure Purview und Anzeigen ihrer Herkunft'
description: In diesem Tutorial wird beschrieben, wie Sie im Katalog nach Ressourcen suchen und ihre Herkunft anzeigen.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: ab51e604412e79fb706190fef769ad76c694fd6c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97399433"
---
# <a name="tutorial-browse-assets-in-azure-purview-preview-and-view-their-lineage"></a>Tutorial: Durchsuchen von Ressourcen in Azure Purview (Vorschau) und Anzeigen ihrer Herkunft

> [!IMPORTANT]
> Azure Purview ist derzeit als VORSCHAUVERSION verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Tutorial wird gezeigt, wie Sie Ressourcen in Azure Purview durchsuchen und wichtige Details dazu anzeigen, beispielsweise zur Herkunft.

Dieses Tutorial ist *Teil 3 einer fünfteiligen Tutorialreihe*. Es enthält Informationen zur Verwaltung der Datengovernance in einem Datenbestand mithilfe von Azure Purview. Dieses Tutorial basiert auf den Schritten aus [Teil 2: Navigieren auf der Startseite von Azure Purview (Vorschau) und Suchen nach einer Ressource](tutorial-asset-search.md).

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Durchsuchen des Katalogs nach Ressourcen
> * Anzeigen der Herkunft von Ressourcen

## <a name="prerequisites"></a>Voraussetzungen

* Abschließen von [Tutorial: Navigieren auf der Startseite von Azure Purview (Vorschau) und Suchen nach einer Ressource](tutorial-asset-search.md).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="browse-for-assets-in-the-catalog"></a>Suchen nach Ressourcen im Katalog

Im vorherigen Tutorial haben Sie gelernt, wie die Suche Ihnen beim Ermitteln von Ressourcen im Azure Purview-Katalog helfen kann. Eine weitere Möglichkeit, Ressourcen im Katalog zu ermitteln, ist die Verwendung der Oberfläche zum Durchsuchen des Katalogs.

In diesem Abschnitt wird gezeigt, wie Sie den Azure Purview-Katalog durchsuchen.

1. Navigieren Sie im Azure-Portal zu Ihrer Azure Purview-Ressource, und wählen Sie **Open Purview Studio** (Purview Studio öffnen) aus. Sie werden automatisch zur Startseite von Purview Studio weitergeleitet.

1. Wählen Sie auf der **Startseite** die Option **Browse assets** (Ressourcen durchsuchen) aus.

   Die Seite **Browse assets** (Ressourcen durchsuchen) wird angezeigt. Sie enthält eine Zusammenfassung aller Ressourcentypen im Katalog.

1. Wählen Sie zum Erkunden der verschiedenen Ressourcen vom Typ „Azure Data Lake Gen2“, die in Ihrem Katalog verfügbar sind, die Kachel **Azure Data Lake Gen2** aus.

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/browse-by-asset-type.png" alt-text="Screenshot: Seite zum Durchsuchen nach Ressourcentyp, auf der „Azure Data Lake Gen2“ ausgewählt ist":::

1. Sind mehrere Ressourcen vorhanden, können Sie die Spaltenüberschrift **Name** auswählen, um die Ressourcen alphabetisch zu sortieren. In diesem Tutorial gibt es nur eine Ressource vom Typ „Azure Data Lake Storage Gen2“.

1. Wählen Sie den Namen der Ressource aus.

1. Wählen Sie den Ressourcensatz **Contoso_GrossProfit_{N}.ssv** aus. Ist diese Ressource nicht in Ihrem Katalog vorhanden, wählen Sie eine andere Ressource aus.

   :::image type="content" source="media/tutorial-browse-and-view-lineage/view-adls-assets.png" alt-text="Liste der Azure Data Lake Storage Gen2-Ressourcen":::

## <a name="view-the-lineage-of-assets"></a>Anzeigen der Herkunft von Ressourcen

Untersuchen Sie auf der Seite mit den Ressourcendetails die Quelle der Daten.

1. Wählen Sie die Registerkarte **Herkunft** des Ressourcensatzes **Contoso_GrossProfit_{N}.ssv** aus.

   Die von Ihnen ausgewählte Ressource wird angezeigt. An den angezeigten Herkunftsinformationen können Sie erkennen, dass dieser Ressourcensatz aus Ihrem Azure Blob Storage-Konto in Azure Data Lake Storage Gen2 kopiert wurde.

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/view-lineage.png" alt-text="Screenshot: Herkunftsansicht für die Ressource":::

1. Wählen Sie die Blobressource auf dieser Seite und dann den Link **Switch to asset** (Zu Ressource wechseln) aus.

   Beachten Sie, dass das Fenster zum Azure Blob-Ressourcensatz gewechselt hat, der die Quelle der ursprünglichen Azure Data Lake Storage Gen2-Ressource war.

1. Wählen Sie die Registerkarte **Übersicht** aus, um die Ressource zu untersuchen und ihre Details zu überprüfen.

Informationen zum Erstellen einer Azure Data Factory-Datenflussaktivität zwischen einem Azure Blob und einem Azure Data Lake Storage Gen2-Ressourcensatz sowie zum Ermitteln der Herkunft finden Sie unter [Zuordnungsdatenflüsse in Azure Data Factory](../data-factory/concepts-data-flow-overview.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
>
> * Durchsuchen des Katalogs nach Ressourcen
> * Anzeigen der Herkunft von Ressourcen

Fahren Sie mit dem nächsten Tutorial fort, um mehr über Ressourcensätze, Ressourcendetails, Schemas und Klassifizierungen zu erfahren.

> [!div class="nextstepaction"]
> [Tutorial: Erkunden von Ressourcensätzen, Details, Schemas und Klassifizierungen in Azure Purview (Vorschau)](tutorial-schemas-and-classifications.md)
