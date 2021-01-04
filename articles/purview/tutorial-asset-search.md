---
title: 'Tutorial: Navigieren auf der Startseite von Azure Purview und Suchen nach einer Ressource'
description: In diesem Tutorial wird beschrieben, wie Sie Features auf der Azure Purview-Startseite verwenden und im Katalog suchen.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 46c2ee284c33cc8ffb7b0bd3bc536905cf4e2052
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97399875"
---
# <a name="tutorial-navigate-the-azure-purview-preview-home-page-and-search-for-an-asset"></a>Tutorial: Navigieren auf der Startseite von Azure Purview (Vorschau) und Suchen nach einer Ressource

> [!IMPORTANT]
> Azure Purview ist derzeit als VORSCHAUVERSION verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Tutorial machen Sie sich mit Azure Purview vertraut, indem Sie die Features der Startseite verwenden und im Katalog nach einer Ressource suchen.

Dieser Artikel ist *Teil 2 einer fünfteiligen Tutorialreihe*. Er enthält Informationen zur Verwaltung der Datengovernance in einem Datenbestand mithilfe von Azure Purview. Dieses Tutorial basiert auf den Schritten aus [Teil 1: Überprüfen von Daten mit Azure Purview](tutorial-scan-data.md)

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Navigieren auf der Azure Purview-Startseite
> * Suchen nach einer Ressource
> * Hinzufügen eines Ressourcenbesitzers

## <a name="prerequisites"></a>Voraussetzungen

* Abschließen von [Tutorial: Überprüfen von Daten mit Azure Purview](tutorial-scan-data.md)

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="navigate-the-azure-purview-home-page"></a>Navigieren auf der Azure Purview-Startseite

In den folgenden Schritten machen Sie sich mit der Azure Purview-Startseite vertraut:

1. Navigieren Sie im Azure-Portal zu Ihrer Azure Purview-Ressource, und wählen Sie **Open Purview Studio** (Purview Studio öffnen) aus. Sie werden automatisch zur Startseite von Purview Studio weitergeleitet.

   Oben auf der Startseite werden der Name Ihres Katalogs und verschiedene Kataloganalysen angezeigt. Sie enthalten die Anzahl von Benutzern, Datenressourcen und Glossarbegriffen. In der Zusammenfassung sehen Sie, dass insgesamt mehr als 200 Ressourcen und 113 Glossarbegriffe vorhanden sind. Diese Zahlen werden aktualisiert, wenn Ihre Organisation Überprüfungen durchführt und Azure Purview weitere Begriffe hinzufügt.

   :::image type="content" source="./media/tutorial-asset-search/purview-home-page.png" alt-text="Screenshot der Azure Purview-Startseite":::

1. Wählen Sie **Browse assets** (Ressourcen durchsuchen) aus, um alle Ihre Ressourcen anzuzeigen.

## <a name="search-for-an-asset"></a>Suchen nach einer Ressource

Nachfolgend finden Sie noch einmal eine kurze Übersicht über die grundlegende Terminologie:

* **Ressource:** Ein einzelnes präzises Objekt im Katalog, das die Definition einer beliebigen Entität im Datenbestand enthält. Beispiele für Entitäten sind SQL-Tabellen, Power BI-Berichte und Data Factory-Aktivitäten.
  
* **Schema**: Ein Schema, das auch als Spalte oder Attribut bezeichnet wird, stellt die Struktur einer Ressource oder eines Ressourcensatzes dar.

* **Ressourcensatz:** Ein einzelnes Objekt im Katalog, das eine große Anzahl von physischen Objekten im Speicher darstellt. Diese Objekte verfügen in der Regel über ein gemeinsames Schema und in den meisten Fällen über eine Benennungskonvention oder eine Ordnerstruktur. Beispielsweise lautet das Datumsformat *jjjj/mm/tt*. Weitere Informationen finden Sie unter [Grundlegendes zu Ressourcensätzen](concept-resource-sets.md).

* **Ressourcentyp:** Eine Gruppierung von Ressourcen und Ressourcensätzen unter einem logischen Namen, der normalerweise dem Namen der Datenplattform zugeordnet ist.

Führen Sie die folgenden Schritte aus, um nach einer Ressource zu suchen und sich selbst als Besitzer zu kennzeichnen:

1. Geben Sie auf der Startseite in das Feld **Katalog durchsuchen** den Namen der Ressourcengruppe ein, in der Ihr Datenbestand (die im vorherigen Tutorial erstellte Ressourcengruppe) enthalten ist. Eine Liste mit Vorschlägen wird angezeigt.

1. Wählen Sie **View search results** (Suchergebnisse anzeigen) aus. Da alle Ihre Ressourcen mit dem Namen Ihrer Ressourcengruppe beginnen, werden sie alle in den Suchergebnissen angezeigt. Außerhalb dieses Tutorials suchen Sie nach bestimmten Ressourcennamen und nicht nach Ressourcengruppen.

    :::image type="content" source="./media/tutorial-asset-search/search-suggestions.png" alt-text="Screenshot: Liste der Katalogsuchvorschläge":::

1. Mit den Filtern im linken Navigationsbereich können Sie nach Ressourcentyp, Klassifizierung, Kontakt, Bezeichnung und Glossarbegriff filtern.

1. Beginnen Sie für die Suche nach einem Ressourcensatz mit der Eingabe des Namens des Satzes. Eine Liste der Suchvorschläge mit den richtigen Schlüsselwörtern wird angezeigt. Sie können auch nach absoluten Namen suchen, indem Sie sie in Anführungszeichen setzen.

   :::image type="content" source="media/tutorial-asset-search/keyword-search.png" alt-text="Azure Purview: Ressourcensuche mit Schlüsselwörtern":::

## <a name="edit-an-asset"></a>Bearbeiten einer Ressource

1. Wählen Sie in den Suchergebnissen eine Ressource aus. Wählen Sie anschließend **Bearbeiten** aus.

1. Auf der Registerkarte **Übersicht** können Sie eine Beschreibung hinzufügen.

    :::image type="content" source="./media/tutorial-asset-search/overview-tab.png" alt-text="Screenshot: Feld „Beschreibung“ auf der Registerkarte „Übersicht“":::

1. Wählen Sie die Registerkarte **Kontakte** aus. Beginnen Sie neben **Besitzer** im Feld **Benutzer oder Gruppe auswählen** mit der Eingabe Ihrer Unternehmens-E-Mail-Adresse.

    :::image type="content" source="./media/tutorial-asset-search/contacts-tab.png" alt-text="Screenshot: Ausgefüllte Felder":::

    Ein Namensvorschlag wird automatisch angezeigt.

1. Geben Sie neben **Experten** im Feld **Benutzer oder Gruppe auswählen** einen Namen (etwa den Namen Ihres Vorgesetzten) ein, und wählen Sie dann **Speichern** aus.

    Die Felder für die Beschreibung, den Besitzernamen und den Expertennamen sind nun aufgefüllt.

## <a name="next-steps"></a>Nächste Schritte

Bevor Sie mit dem nächsten Tutorial in dieser Reihe fortfahren, sollten Sie sich etwas Zeit nehmen, um die Azure Purview-Startseite selbst zu erkunden. In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
>
> * Navigieren auf der Azure Purview-Startseite
> * Suchen nach einer Ressource
> * Hinzufügen eines Ressourcenbesitzers

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie in Azure Purview nach Ressourcen suchen und die Ressourcenherkunft ermitteln.

> [!div class="nextstepaction"]
> [Durchsuchen von Ressourcen und Anzeigen ihrer Herkunft](tutorial-browse-and-view-lineage.md)
